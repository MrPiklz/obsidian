Inventory

По умолчанию inventory хранится в /etc/ansible/hosts, но вы можете указать другое расположение с помощью флага -i или конфигурационного файла ansible.cfg.


1.  [  ] - В данные скобки мы указываем название группы серверов.  
    Пример: [dev]
    
2. ansible_host - ip адрес.  
    Пример: ansible_host = 0.0.0.0
    
3. ansible_user - имя пользователя для подключения на удаленный сервер.  
    Пример: ansible_user - test
    
4. ansible_pass - Пароль пользователя.  
    Пример: ansible_pass = 12345
    
5. ansible_ssh_private_key_file - путь до закрытого ключа. Для входа без участия пароля.  
    Пример: ansible_ssh_private_key_file = /home/test/.ssh/id_rsa
    
6. ansible_port - порт подключения ssh.  
    Пример: ansible_port = 2222


host01 ansible_host=host01.example.ru ansible_port=5555

# ...
  hosts:
    jumper1:
      ansible_port: 5555
      ansible_host: 192.0.2.50
    jumper2:
      ansible_port: 5555
      ansible_host: 192.0.2.50
Переменные

[webservers] 
host01.example.ru app_version=1.1 
host02.example.ru app_version=1.2