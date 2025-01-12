# Utilizamos una imagen oficial de Ubuntu
FROM ubuntu:latest

# Damos información sobre la imagen que estamos creando
LABEL \
    version="2.0" \
    description="Ubuntu + Apache2 + virtual host + ftp + ssh" \
    maintainer="Josegarcia <Josegarcia@birt.eus>"

# Actualizamos la lista de paquetes e instalamos nano y apache2
RUN apt-get update upgrade && \
    DEBIAN_FRONTEND=noninteractive apt-get install -y nano apache2 proftpd openssl proftpd-mod-crypto git ssh openssh-server && \
    rm -rf /var/lib/apt/lists/*

# Creamos directorios para los sitios web y configuraciones
RUN mkdir -p /var/www/html/sitioprimero /var/www/html/sitiosegundo
RUN mkdir /var/www/html/sitiosegundo/.ssh

# Usuarios
RUN useradd -r -d /var/www/html/sitioprimero -s /usr/sbin/nologin -m josegarcia1 -p "$(openssl passwd -1 "josegarcia1")" -g nogroup
RUN useradd -r -d /var/www/html/sitioprimero -s /usr/sbin/nologin -m josegarcia2 -p "$(openssl passwd -1 "josegarcia2")" -g nogroup

# Copiamos archivos al contenedor
COPY    indexPrimero.html indexSegundo.html sitioPrimero.conf sitioSegundo.conf sitioPrimero.key sitioPrimero.cer /
COPY    ftp/proftpd.conf /etc/proftpd/
COPY    ftp/tls.conf /etc/proftpd/
COPY    ftp/modules.conf /etc/proftpd/
COPY    ftp/proftpd.key /etc/ssl/private/
COPY    ftp/proftpd.crt /etc/ssl/certs/
COPY    ssh/id_rsa.pub /var/www/html/sitiosegundo/.ssh

# Permisos key
RUN chmod 0600 /etc/ssl/private/proftpd.key

RUN cat /var/www/html/sitioprimero/.ssh/id_rsa.pub >> /var/www/html/sitioprimero/.ssh/authorized_keys

# propietarios
RUN chown -R josegarcia1 /var/www/html/sitioprimero
RUN chown -R josegarcia2 /var/www/html/sitiosegundo

# Movemos los archivos a sus ubicaciones adecuadas
RUN mv /indexPrimero.html /var/www/html/sitioprimero/index.html && \
    mv /indexSegundo.html /var/www/html/sitiosegundo/index.html && \
    mv /sitioPrimero.conf /etc/apache2/sites-available/sitioprimero.conf && \
    mv /sitioSegundo.conf /etc/apache2/sites-available/sitiosegundo.conf && \
    mv /sitioPrimero.key /etc/ssl/private/sitioprimero.key && \
    mv /sitioPrimero.cer /etc/ssl/certs/sitioprimero.cer

# Habilitamos los sitios y el módulo SSL
RUN a2ensite sitioprimero.conf && \
    a2ensite sitioprimero.conf && \
    a2enmod ssl

# Exponemos los puertos
EXPOSE 80
EXPOSE 443
EXPOSE 50000-50030
EXPOSE 21
EXPOSE 50

# Comando por defecto al iniciar el contenedor
#CMD ["apache2-foreground"]
