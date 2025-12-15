

## Criar uma pasta html no diretório /var/www/

ficando assim:

/var/www/html/index.html
/var/www/html/css
/var/www/html/img

## Colocar a seguinte permissão na pasta html:

chmod -R 755 /var/www/html/

## Editar o arquivo que está na pasta  etc/bind/named.conf.local

e
[db.gestorveicular.com.br](http://db.gestorveicular.com.br/)

```
    $TTL    604800
@       IN      SOA     ns1.gestorveicular.com.br. admin.gestorveicular.com.br. (
                              2         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL

;
@       IN      NS      ns1.gestorveicular.com.br.
ns1     IN      A       192.168.0.100
@       IN      A       192.168.0.100

```

sudo systemctl restart bind9

## Por o meu ip np arquivo /etc/resolv.conf no arquivo:

sudo nano /etc/apache2/sites-available/gestorveicular.com.br.conf
<VirtualHost *:80>
ServerAdmin [webmaster@gestorveicular.com.br](mailto:webmaster@gestorveicular.com.br)
ServerName [gestorveicular.com.br](http://gestorveicular.com.br/)
ServerAlias [www.gestorveicular.com.br](http://www.gestorveicular.com.br/)
DocumentRoot /var/www/html
ErrorLog ${APACHE_LOG_DIR}/error.log
CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>

## colocar ip local no junto com nome do site nano /etc/hosts

sudo a2ensite gestorveicular.com.br.conf
sudo systemctl reload apache2

# Atualizar e instalar Apache2 e Bind9

sudo apt update
sudo apt install apache2 bind9 -y

# Criar estrutura de diretórios para o site

sudo mkdir -p /var/www/html/css
sudo mkdir -p /var/www/html/img
sudo nano /var/www/html/index.html

# Ajustar permissões

sudo chmod -R 755 /var/www/html/

# Configurar Bind9

# Editar named.conf.local

sudo tee /etc/bind/named.conf.local > /dev/null <<EOL
zone "[gestorveicular.com.br](http://gestorveicular.com.br/)" {
type master;
file "/etc/bind/db.gestorveicular.com.br";
};
EOL

# Criar arquivo de zona

sudo tee /etc/bind/db.gestorveicular.com.br > /dev/null <<EOL
\$TTL    604800
@       IN      SOA     [ns1.gestorveicular.com.br](http://ns1.gestorveicular.com.br/). [admin.gestorveicular.com.br](http://admin.gestorveicular.com.br/). (
2         ; Serial
604800         ; Refresh
86400         ; Retry
2419200         ; Expire
604800 )       ; Negative Cache TTL

@       IN      NS      [ns1.gestorveicular.com.br](http://ns1.gestorveicular.com.br/).
ns1     IN      A       192.168.0.100
@       IN      A       192.168.0.100
EOL

# Reiniciar Bind9

sudo systemctl restart bind9

# Configurar resolv.conf temporariamente

sudo tee /etc/resolv.conf > /dev/null <<EOL
nameserver 192.168.0.106
EOL

# Configurar Apache Virtual Host

sudo tee /etc/apache2/sites-available/gestorveicular.com.br.conf > /dev/null <<EOL
<VirtualHost *:80>
ServerAdmin [webmaster@gestorveicular.com.br](mailto:webmaster@gestorveicular.com.br)
ServerName [gestorveicular.com.br](http://gestorveicular.com.br/)
ServerAlias [www.gestorveicular.com.br](http://www.gestorveicular.com.br/)
DocumentRoot /var/www/html
ErrorLog \${APACHE_LOG_DIR}/error.log
CustomLog \${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
EOL

# Atualizar arquivo hosts

sudo tee -a /etc/hosts > /dev/null <<EOL
192.168.0.106   [gestorveicular.com.br](http://gestorveicular.com.br/) [www.gestorveicular.com.br](http://www.gestorveicular.com.br/)
EOL

# Habilitar site e reiniciar Apache

sudo a2ensite gestorveicular.com.br.conf
sudo systemctl reload apache2