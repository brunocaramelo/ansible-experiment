# Instação dos ambientes

## Do que se tratam estas pastas:

`/webapp` - arquivos de configuração da aplicação frontend (laravel) e backend (wordpress)
`/redis` - arquivos de configuração do Redis
`/mysql` - arquivos de configuração do Mysql
`/comandos` - arquivos de execucação 

## Instalando


Caso utilize o Windows, cetifique-se que tenha instalado o WSL e **Ansible**   

### Servidores de aplicação e bancos de dados
Deve ter o pacote **sshpass**

### Como instalar o Redis
1. Executar:
	```
	ansible-playbook -i 192.168.xxx.xx,  redis/main.yml -u MATRICULA -kK -vvv
	```
2. Na pasta `redis/vars.yml`, defina a senha desejada

### Como instalar o Mysql
1. Executar:
	```
	ansible-playbook -i 192.168.xxx.xx,  mysql/ansible/playbook.yml -u MATRICULA -kK -vvv
	```
2. Executar: 
	```
	mysql_secure_installation
	```
    - Definir parâmetros de segurança e senha do root
3. Acessar como root
	```
	mysql -u root -p
	```
	- Executar comandos de criação de usuário:
	```
    CREATE USER 'usuarioapp'@'%' IDENTIFIED BY 'senha';
	GRANT ALL PRIVILEGES ON * . * TO 'usuarioapp'@'%' IDENTIFIED BY 'senha';
	FLUSH PRIVILEGES;
	```

### Como instalar o Wordpress
1. Executar:
	```
	ansible-playbook -i 192.168.xxx.xx webapp/backend-server.yml -u MATRICULA -kK -vvv
	```
2. Baixar aplicação do Wordpress
    - cd `/var/www/html/`
3. Alterar o arquivo do wp-config.php
    - Com os dados da nova conexão com o banco de dados, exemplo:
    ```
    $connectstr_dbhost = '...';
    $connectstr_dbname = '...';
    $connectstr_dbusername = '...';
    $connectstr_dbpassword = '...';
	```

4. Copiar arquivos de configuração
    - Arquivo contido em `webapp/config/backend/000-default.conf` e `webapp/config/backend/001-storage.conf`
    - No servidor, copiar para `/etc/httpd/conf.d/000-default.conf` e
    `/etc/httpd/conf.d/001-storage.conf`

5. Reiniciar
    - Executar:
	```
	sudo systemctl restart httpd
	```

### Como instalar o Laravel
1. Executar:
	```
	ansible-playbook -i 192.168.xxx.xx, webapp/frontend-server.yml -u MATRICULA -kK -vvv
	```
2. Baixar aplicação do wordpress
3. Enviar arquivo de configuração da aplicação `.env` na raiz do projeto
    - Arquivo contido em `webapp/config/frontend/.env`
    - Alterar modelo com os novos dados 
    - No servidor, copiar para `/var/www/html/yyy-novoportal/.env`

4. Copiar arquivo de configuração
    - Arquivo contido em `webapp/config/frontend/000-default.conf`
    - No servidor, copiar para `/etc/httpd/conf.d/000-default.conf`

5. Reiniciar
    - Executar:
	```
	sudo systemctl restart httpd
	```
