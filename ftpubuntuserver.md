# Instalacion
vsftpd

# config
/etc/vsftpd

$$$$ Archivo de configuración $$$$
Permitir subir archivos
	write_enable=YES
Permitir usuarios locales loggear
	local_enable=YES
Restringir solo acceso a la carpeta local (Crear archivo empty chroot_list)
	chroot_local_user=YES
	chroot_list_enable=YES
	chroot_list_file=/etc/vsftpd/chroot_list
Usuarios Anonimos
	anonymous_enable=YES
	no_anon_password=YES
	anon_root=/storage/ftp/
Mensaje de inicio del servidor
	ftpd_banner=Bienvenido
Escuchar (Desavilitar ipv6)
	Listen=YES
Blacklist
	userlist_enable=YES
	userlist_file=/etc/vsftp.user_list
	userlist_deny=YES
Whitelist
	userlist_deny=NO
Iptables
	modprobe ip_conntrack_ftp 
	sudo echo "ip_conntrack_ftp" >> /etc/modules

