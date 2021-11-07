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
Bloquear IP
	/etc/hosts.deny 
	##### To block FTP Access #####
	vsftpd: 192.168.1.100
	vsftpd: 192.168.1.0/255.255.255.0




<html>
	<head>	
	</head>
	<body>
		<style type ="text/css">
			table { border: solid 2px black;}
			td.par {
				background-color: black;
				color:white;
			}
		</style>
		<?php
		$col=16;
		$fila=8;
		$num=1;
		echo "<table>";
		for($i=1;$i<=$fila;$i++)
		{
			echo "<tr >";
			for($j=1;$j<=$col;$j++)
			{
				/*Si 1+1 ES PAR lo pongo en negro*/
				if(($i+$j)%2==0){
					
					/*	echo "<td bgcolor=\"black\" style=\"color:white\">".$num++."</td>";*/
						echo "<td class=\"par\">".$num++."</td>";
					
				}
				/*Si 1+2 ES IMPAR lo pongo en blanco*/
				else{
					
						echo "<td class =\"impar\">".$num++."</td>";
			
				}
			}
			echo "<tr>";
		}
		echo "</table>";
		?>
	</body>
</html> 

