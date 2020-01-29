<h1> TrilhaSRE </h1>

<h2>Esta trilha tem como objetivo demonstrar os conhecimentos necessários para estar apto para o time de SRE.</h2>

<h2>Pré requisitos:</h3>

- Vagrant (CentOS7)
- Nginx
- PHP-FPM 7.2
- MariaDB
- WordPress

<h4>Multi-Machine with Vagrant separating web server from database VERSION 1.0</h4>

<h5> Instalando o Vagrant </h5>

<ol>
  <li>Crie um diretório em sua máquina local e uma pasta para armazenar o Vagrant.</li>
  <li>Abra seu terminal e acesse o diretório que acabou de criar.</li>
  <li>No terminal dentro do diretório utilize o comando <kbd> apt-get install vagrant </kbd> </li>
  <li>Após o término da instalação, utilize o comando <kbd>vagrant init</kbd> (Este comando irá gerir o Arquivo de conf do Vagrant).</li>
  <li>Gerando o arquivo padrão do Vagrant, basta fazer um clone do arquivo VagrantFile disponibilizado aqui para ter as mesmas configurações.</li>
  <li>Pronto! Neste momento você subiu duas VMS separadas uma para o seu banco de dados e outra para seu Web Server!</li>
</ol>

<h5> Instalando o NGINX </h5>
<ol>
  <li>Agora, iremos iniciar a instalação do nosso server.</li>
  <li>No seu terminal instale primeiramente o repositório epel-release com o comando <kbd>yum install -y epel-release </kbd></li>
  <li>Adicione o repositório oficial no caminho vim /etc/yum.repos.d/nginx.repo
    <kbd>
        [nginx]
        name=nginx repo
        baseurl=http://nginx.org/packages/centos/$releasever/$basearch/
        gpgcheck=0
        enabled=1</kbd>
  </li>
  <li>Agora, rode o comando <kbd>yum -y install nginx</kbd></li>
  <li>Após concluir a instalação você irá iniciar e habilitar para que ele inicie o serviço sempre que ligar a VM, utilizando o comando <kbd>systemctl enable nginx && systemctl start nginx</kbd>.</li>
  <li>Agora, você poderá acessar o seu browser com o IP que foi setado em seu VagrantFile e ele lhe mostrará a página padrão do NGINX.
  
![nginx-welcome](https://user-images.githubusercontent.com/45441463/73197157-99456c00-410f-11ea-8f81-9c470029dd59.png)
  </li>
</ol>
<h5> Instalando o PHP-FPM 7.2 </h5>
<ol>
  <li>Primeiro você deverá adicionar o repositório oficial REMI, desta forma:
  <kbd>yum install -y http://rpms.remirepo.net/enterprise/remi-release-7.rpm</kbd>
  <kbd> yum-config-manager --enable remi-php72</kbd>
</li>
  <li>Agora, você irá instalar os pacotes base do PHP juntamente com outros pacotes que aplicações PHP utilizam. </li>
      <kbd>yum install -y php php-fpm php-mysqlnd</kbd>
        <li>Se quiser verificar a versão que está instalada do PHP, basta rodar o comando:</li>
        <kbd> php --version </kbd>
          <li> Agora verifique se os serviços estão no ar, com o comando <kbd>netstat -anput |grep OU |grep -E '80|9000'</kbd> </li>

<li>Agora acesse o arquivo vim /etc/php-fpm.d/www.conf nele você irá alterar alguns parâmetros, são eles:
     
     - user = nginx
    
    - group = nginx
     
     - listen = 127.0.0.1:9000
  </li>
  <li>Pronto, agora inicie e ative os serviços para iniciarem com a inicialização da VM
   
   <kbd>systemctl start php-fpm && systemctl enable php-fpm</kbd>

![image](https://user-images.githubusercontent.com/45441463/73200054-d4966980-4114-11ea-8293-03460149b3f8.png)

 </li>
 Se a sua saída foi parecida com a imagem acima, o seu serviço está funcionando corretamente.
  
<h5> Configurando o NGINX para utilizar o PHP-FPM </h5>

<li>Para realizar essa configuração iremos editar o arquivo padrão do NGINX</li>
    Copie o arquivo nginx.conf para o diretório conf.d e crie um arquivo default.conf com conteúdo que está em nginx.conf.
<li>Existem "zilhares" de configurações disponíveis, porém segue um arquivo de exemplo para facilitara na sua configuração.
    
    
    server {
    listen       80;
    server_name  localhost;
 
    #charset koi8-r;
    #access_log  /var/log/nginx/host.access.log  main;
 
    location / {
        root   /usr/share/nginx/html;
        index  index.html index.htm;
    }
 
    #error_page  404              /404.html;
 
    # redirect server error pages to the static page /50x.html
    #
    error_page   500 502 503 504  /50x.html;
    location = /50x.html {
        root   /usr/share/nginx/html;
    }
 
    # pass the PHP scripts to FastCGI server listening on 127.0.0.1:9000
    #
    location ~ \.php$ {
        root           /usr/share/nginx/html;
        fastcgi_pass   127.0.0.1:9000;
        fastcgi_index  index.php;
    fastcgi_param  SCRIPT_FILENAME         $document_root$fastcgi_script_name;
        include        fastcgi_params;
    }
 
    # deny access to .htaccess files, if Apache's document root
    # concurs with nginx's one
    #
    #location ~ /\.ht {
    #    deny  all;
    #} 
    }
   
</li>

  <li>Pronto, configurado, agora para validarmos o funcionamento do PHP iremos criar o arquivo padrão do PHP
    Acesse o caminho vim /usr/share/nginx/html/info.php
  </li>

  <li>Dentro deste arquivo adicione a seguinte linha: 

![image](https://user-images.githubusercontent.com/45441463/73201071-e2e58500-4116-11ea-875d-f1b68ebc5cab.png)

 <li>Agora reinicie os serviços do PHP E NGINX 
  <kbd>systemctl restart nginx && systemctl restart php-pfm</kbd>

</li> 
  </li>

<li>Pronto, agora vá no seu browser e digite o IP setado/info.php e você obterá a seguinte imagem: 

![image](https://user-images.githubusercontent.com/45441463/73201208-44a5ef00-4117-11ea-911b-19021de7b089.png)


</li>
<li>
Antes de darmos andamento a instação iremos liberar a porta padrão do MYSQL 3306, para que haja comunicação, utilize este comando:

<kbd>iptables -I INPUT -i eth0 -p tcp --destination-port 3306 -j ACCEPT</kbd>
</li>
</ol>
<ol>
<h4>Agora, que você já tem o seu LAMP, iremos acessar a máquina do banco de dados e realizar a instalação do servidor de banco de dados MariaDB. </h4>

<li>Primeiro acesse o servidor DB utilizando o comando <kbd>vagrant ssh db</kbd></li>
<li>Para realizar a instalação do Client e Server do MariaDB iremos adicionar o repositório neste caminho:
    cd /etc/yum.repos.d/MariaDB.repo e adicionaremos os seguintes parâmetros:

      MariaDB 10.3 CentOS repository list - created 2018-05-25 19:02 UTC
      http://downloads.mariadb.org/mariadb/repositories/
      [mariadb]
      name = MariaDB
      baseurl = http://yum.mariadb.org/10.3/centos7-amd64
      gpgkey=https://yum.mariadb.org/RPM-GPG-KEY-MariaDB
      gpgcheck=1
      

</li>
<li>Agora rode o comando de instalação do MariaDB, <kbd>yum install mariadb-server mariadb-client</kbd></li>
<li>Após o termino da instalação inicie o serviço e adicione para iniciar com a inicialização da VM, com os comandos
  <kbd>systemctl start mariadb && systemctl enable mariadb</kbd>
</li>
<li>Agora, iremos deixaremos nossa instalação mais segura, rode o comando <kbd>mysql_secure_instalattion</kbd></li>
<li>As configurações dos próximos passos até que seja solicitado a alteração da senha de root, pode responder "Y" nos questionamentos que a instalação irá fazer.</li>

<h3>Agora, iremos permitir conexões de outros computadores ao nosso BD.</h3>
<li>Acesse o caminho cd /etc/my.cnf.d</li>
<li>Neste diretório irá encontrar três arquivos, acesse o server.cnf e altere os seguintes parâmetros:
    Localize o [mysqld] e adicione abaixo dele o parâmetro: bind-address = ip_do_servidor_do_bd </li>
<li>Agora reinicie o MariaDB, <kbd>systemctl restart mariadb</kbd></li>
<li>Para verificar se o servidor está escutando uma interface externa utilize o comando:
  <kbd>netstat -plunt |grep mysqld </kbd>
</li>
</ol>
  
<h4>Configurando o BD Wordpress para acesso com credenciais remotas</h4>
<ol>
<li>Comece se conectando como root em seu BD, com o comando:
    <kbd>mysql -u root -p </kbd>
  Será solicitado a senha de root do banco e você será direcionado a página do BD.
  </li>
  <li>Agora iremos criar o banco de dados que será utilizado pelo WordPress, utilize o comando SQL:
  
  mysql> CREATE DATABASE wordpress;
  </li>
  
  <li>Agora, temos um banco de dados criado, iremos criar os usuários locais para conseguir acessa-lo de forma segura com os devido privilégios.

-mysql> CREATE USER wordpressuser@localhost IDENTIFIED BY 'elfos123';
-mysql> GRANT ALL PRIVILEGES ON wordpress.* TO wordpressuser@localhost;

</li>
<li>Pronto, usuário para acesso local criado, agora iremos criar o usuário para realizar o acesso remotamente:

mysql> CREATE USER wordpressuser@ip_do_seu_webserver IDENTIFIED BY 'elfos123';
</li>

<li>Libere os prvilégios concedidos com o comando:
  <kbd>mysql> FLUSH PRIVILEGES;</kbd>
  
  Agora, pode sair do prompt do BD, utilizando <kbd>EXIT</kbd>
</li>

<li>Para validarmos, tente se logar com o usuário que acabou de ser criado, desta maneira:

mysql -u wordpressuser -p

Se você obtiver êxito, quer dizer que o usuário de acesso local foi configurado corretamente, neste caso iremos agora acessar o WebServer.
</li>

<li>Acesse o web server utlizando o comando:
  <kbd>vagrant ssh web(nome do seu webserver)</kbd>
</li>
<li>Após acessar o WebServer será necessário instalar os pacotes de cliente do MariaDB, utilizando os comandos:
  <kbd> yum install mariadb-server mariadb-client </kbd>
  
Ao término da instalação você poderá realizar o teste de conexão. 

mysql -u wordpressuser -h (ip_do_banco_de_dados) -p

Se obtiver êxito, quer dizer que a conexão remota está OK.</li>

<li>Você pode verificar isto, digitando status dentro do painel do MariaDB</li>
</ol>
<H4> Instalando o WordPress </h4>
<ol>
  <li>Dentro do seu servidor WEB acesso o caminho cd /usr/share/nginx/html </li>
  
  <li>Realize o comando <kbd>curl -O https://wordpress.org/latest.tar.gz</kbd>
</li>
  <li> Agora extraia: <kbd>tar xzvf latest.tar.gz </kbd>
     O wordpress vem incluído com um arquivo de configuração nomeado wp-config-sample.php, você irá criar e copiar este arquivo para o wp-config.php, assim:

<kbd>cp ~/wordpress/wp-config-sample.php ~/wordpress/wp-config.php</kbd>
</li>
<li>Assim que abrirmos esse arquivo iremos realizar algumas alterações, para fornecer mais segurança em nossa instalação, para obter esses valores, rode o comando:

<kbd>curl -s https://api.wordpress.org/secret-key/1.1/salt/</kbd>

Isto deve ser realizado a cada nova instalação.

</li>
<li>Agora acesse o arquivo wp-config.php e onde haver estes campos abaixo:

    ATUAL:
    
            define('AUTH_KEY',         'put your unique phrase here');
            define('SECURE_AUTH_KEY',  'put your unique phrase here');
            define('LOGGED_IN_KEY',    'put your unique phrase here');
            define('NONCE_KEY',        'put your unique phrase here');
            define('AUTH_SALT',        'put your unique phrase here');
            define('SECURE_AUTH_SALT', 'put your unique phrase here');
            define('LOGGED_IN_SALT',   'put your unique phrase here');
            define('NONCE_SALT',       'put your unique phrase here');

    SUBSTITUIDO:
    
            define('AUTH_KEY',         '1jl/vqfs<XhdXoAPz9 DO NOT COPY THESE VALUES c_j{iwqD^<+c9.k<J@4H');
            define('SECURE_AUTH_KEY',  'E2N-h2]Dcvp+aS/p7X DO NOT COPY THESE VALUES {Ka(f;rv?Pxf})CgLi-3');
            define('LOGGED_IN_KEY',    'W(50,{W^,OPB%PB<JF DO NOT COPY THESE VALUES 2;y&,2m%3]R6DUth[;88');
            define('NONCE_KEY',        'll,4UC)7ua+8<!4VM+ DO NOT COPY THESE VALUES #`DXF+[$atzM7 o^-C7g');
            define('AUTH_SALT',        'koMrurzOA+|L_lG}kf DO NOT COPY THESE VALUES  07VC*Lj*lD&?3w!BT#-');
            define('SECURE_AUTH_SALT', 'p32*p,]z%LZ+pAu:VY DO NOT COPY THESE VALUES C-?y+K0DK_+F|0h{!_xY');
            define('LOGGED_IN_SALT',   'i^/G2W7!-1H2OQ+t$3 DO NOT COPY THESE VALUES t6**bRVFSD[Hi])-qS`|');
            define('NONCE_SALT',       'Q6]U:K?j4L%Z]}h^q7 DO NOT COPY THESE VALUES 1% ^qUswWgn+6&xqHN&%');


Você irá alterar pelos valores gerados.

</li>
<li>Em seguido iremos inserir as informações de conexão ao nosso banco de dados, substitua as informações abaixo de acordo com as configurações que você criou:

    . . .
    /** Nome do banco de dados WordPress */
    define('DB_NAME', 'wordpress');

    /** Usuário criado */
    define('DB_USER', 'wordpressuser');

    /** Senha criada */
    define('DB_PASSWORD', 'password');

    /** IP do banco de dados com a porta padrão MYSQL 3306 */
    define('DB_HOST', 'db_server_ip');

</li>
<li>Libere o acesso para os arquivos do wordpress no caminho /usr/share/nginx/html/wordpress 

Utilize o comando sudo chown -R nginx:nginx

</li>

<li>FIM!!! Agora acesse seu browser http://ip_do_web_server/wordpress </li>
  </ol>
        
