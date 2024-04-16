# Домашнее задание к занятию 1 «Введение в Ansible» - Илларионов Дмитрий

ДЗ выполнял на ВМ в облаке, которую ставил через терраформ - см. код в папке trr .
Почему ansible c контейнером на ubuntu не заработал?


## Подготовка к выполнению

1. Установите Ansible версии 2.10 или выше.
2. Создайте свой публичный репозиторий на GitHub с произвольным именем.
3. Скачайте [Playbook](./playbook/) из репозитория с домашним заданием и перенесите его в свой репозиторий.

## Основная часть

1. Попробуйте запустить playbook на окружении из `test.yml`, зафиксируйте значение, которое имеет факт `some_fact` для указанного хоста при выполнении playbook.

![alt text](image.png)

Есть код:

```
      - name: Print fact
        debug:
          msg: "{{ some_fact }}"
```

код выводит:

![alt text](image-1.png)

Потому что в инвентори для тест - нет групп deb и и др. а есть только группа inside .
Но в group_vars нет значения для переменной some_fact для группы inside, поэтому применяется значение для группы all:

```
  some_fact: 12
```  

2. Найдите файл с переменными (group_vars), в котором задаётся найденное в первом пункте значение, и поменяйте его на `all default fact`.

Выполнил, и попробовал применить:

![alt text](image-2.png)

3. Воспользуйтесь подготовленным (используется `docker`) или создайте собственное окружение для проведения дальнейших испытаний.
4. Проведите запуск playbook на окружении из `prod.yml`. Зафиксируйте полученные значения `some_fact` для каждого из `managed host`.

ПО докер поставлено, но, сами контейнеры еще не запущены не развернуты, поэтому при запуске - ошибка:

![alt text](image-3.png)

Запустил:
![alt text](image-4.png)

![alt text](image-5.png)

![alt text](image-6.png)

Переименовал контейнеры:
![alt text](image-7.png)

Еще раз запускаю:

![alt text](image-8.png)

some_fact для centos7 = el потому что в инвенторе группа = el для centos7 и поэтому из group_vars взято значение для этой группы:

```
  some_fact: "el"
```  
А для ubuntu что-то пошло не так, и не смог с ней работать.

![alt text](image-9.png)

Причина что в контейнере для ubuntu нет питона.

установил питон для ubuntu:

Подключился к контейнеру:
```
docker exec -it bash ubuntu
```
Установил питон:
```
apt update
apt install python3
```
После этого все ок:

![alt text](image-20.png)



5. Добавьте факты в `group_vars` каждой из групп хостов так, чтобы для `some_fact` получились значения: для `deb` — `deb default fact`, для `el` — `el default fact`.
6.  Повторите запуск playbook на окружении `prod.yml`. Убедитесь, что выдаются корректные значения для всех хостов.

![alt text](image-10.png)

7. При помощи `ansible-vault` зашифруйте факты в `group_vars/deb` и `group_vars/el` с паролем `netology`.

![alt text](image-11.png)

![alt text](image-12.png)


8. Запустите playbook на окружении `prod.yml`. При запуске `ansible` должен запросить у вас пароль. Убедитесь в работоспособности.

```
 ansible-playbook -i inventory/prod.yml site.yml --ask-vault-pass
```

![alt text](image-13.png)


9. Посмотрите при помощи `ansible-doc` список плагинов для подключения. Выберите подходящий для работы на `control node`.


https://runebook.dev/ru/docs/ansible/plugins/connection
```
ansible-doc -t connection -l
```

![alt text](image-14.png)

Не понял что именно нужно сделать по: "Выберите подходящий для работы на `control node`".
control node - это как я понимаю сервер с которого работает ансил, и может вызывать плагины.
Например можно выбрать ansible.builtin.ssh  

10. В `prod.yml` добавьте новую группу хостов с именем  `local`, в ней разместите localhost с необходимым типом подключения.


11. Запустите playbook на окружении `prod.yml`. При запуске `ansible` должен запросить у вас пароль. Убедитесь, что факты `some_fact` для каждого из хостов определены из верных `group_vars`.

![alt text](image-15.png)

все верно.

12. Заполните `README.md` ответами на вопросы. Сделайте `git push` в ветку `master`. В ответе отправьте ссылку на ваш открытый репозиторий с изменённым `playbook` и заполненным `README.md`.

13. Предоставьте скриншоты результатов запуска команд.

## Необязательная часть

1. При помощи `ansible-vault` расшифруйте все зашифрованные файлы с переменными.

![alt text](image-16.png)

```
ansible-vault decrypt group_vars/el/examp.yml
```

2. Зашифруйте отдельное значение `PaSSw0rd` для переменной `some_fact` паролем `netology`. Добавьте полученное значение в `group_vars/all/exmp.yml`.

![alt text](image-17.png)

![alt text](image-18.png)

3. Запустите `playbook`, убедитесь, что для нужных хостов применился новый `fact`.

![alt text](image-19.png)

Про установку питона в ubuntu уже в конце решил, не на всех скринах там зеленое поэтому.
После исправления итоговый запуск где все ок:

![alt text](image-21.png)



4. Добавьте новую группу хостов `fedora`, самостоятельно придумайте для неё переменную. В качестве образа можно использовать [этот вариант](https://hub.docker.com/r/pycontribs/fedora).



5. Напишите скрипт на bash: автоматизируйте поднятие необходимых контейнеров, запуск ansible-playbook и остановку контейнеров.


6. Все изменения должны быть зафиксированы и отправлены в ваш личный репозиторий.

---

### Как оформить решение задания

Выполненное домашнее задание пришлите в виде ссылки на .md-файл в вашем репозитории.

---
