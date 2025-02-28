# Manual de Instalación de MySIT Call Center Solution - OML

Este es un manual de instalación paso a paso para configurar OmniLeads en un servidor Linux. Asegúrate de seguir cuidadosamente cada uno de los pasos a continuación.

## Requisitos Previos

- Sistema operativo Linux (CentOS 7 se utiliza en este ejemplo).
- Acceso de superusuario (root) o permisos sudo.
- Conexión a Internet.

## Paso 1: Actualizar el Sistema

```bash
yum update
yum upgrade
```

## Paso 2: Instalar Dependencias

Instala las dependencias necesarias con el siguiente comando:

```bash
yum install nano net-tools wget epel-release
```

## Paso 3: Configurar SSH

Abre el archivo de configuración de SSH para realizar modificaciones:

```bash
nano /etc/ssh/sshd_config
```

Dentro del archivo, busca las siguientes líneas y modifícalas según se indica a continuación:

```bash
PermitRootLogin yes
PasswordAuthentication yes
```

Guarda los cambios y luego continúa con el Paso 3. Esto permitirá la autenticación de contraseña para el usuario root y habilitará el inicio de sesión como root a través de SSH. Asegúrate de reiniciar el servicio SSH después de realizar estos cambios:

```bash
systemctl restart sshd
```

## Paso 4: Cambiar la contraseña de Root

Para cambiar la contraseña del usuario root, usa el siguiente comando y sigue las instrucciones:

```bash
passwd root
```

## Paso 5: Desactivar SELinux y el Firewall

Desactiva SELinux temporalmente y el firewall con los siguientes comandos:

```bash
setenforce 0
sed -i 's/^SELINUX=.*/SELINUX=disabled/' /etc/sysconfig/selinux
sed -i 's/^SELINUX=.*/SELINUX=disabled/' /etc/selinux/config
systemctl disable firewalld
```

## Paso 6: Actualizar el Kernel y Git

Asegúrate de que tu sistema tenga el kernel y Git actualizados para continuar con la instalación:

```bash
yum update -y && yum install kernel-devel git -y
```

## Paso 7: Instalar Python y pip

Instala Python 3 y pip3 en tu sistema:

```bash
yum install epel-release -y && yum install python3-pip python3 -y
pip3 install --upgrade pip
```

## Paso 8: Instalar Ansible

Instala Ansible en tu sistema con el siguiente comando:

```bash
pip3 install setuptools_rust
pip3 install 'ansible==2.9.2' --user
```

## Paso 9: Verificar el Kernel y las Dependencias

Antes de continuar, verifica la versión del kernel y la presencia de las dependencias necesarias con los siguientes comandos:

```bash
uname -r
rpm -qa | grep kernel-devel
```

## Paso 10: Instalar Completado Automático

Instala el completado automático de Bash con los siguientes comandos:

```bash
yum install bash-completion bash-completion-extras -y
```

## Paso 11: Clonar el Repositorio de OmniLeads

Clona el repositorio de OmniLeads desde GitLab utilizando el siguiente comando:

```bash
git clone https://gitlab.com/omnileads/ominicontacto.git
```

## Paso 12: Navegar al Directorio de OmniLeads

Accede al directorio donde se encuentra el código fuente de OmniLeads. Utiliza el siguiente comando para navegar al directorio `ominicontacto`:

```bash
cd ominicontacto/
```

## Paso 13: Actualizar el Código Fuente

Actualiza el código fuente de OmniLeads a la versión específica con los siguientes comandos:

```bash
git fetch
git checkout release-1.30.0
git submodule init
git submodule update --remote
```

## Paso 14: Acceder a las Configuraciones de Implementación

Accede al directorio de configuraciones de implementación de Ansible con el siguiente comando:

```bash
cd install/onpremise/deploy/ansible
```

## Paso 15: Creamos el archivo first_boot_installer.sh y copiamos el script determinado

Para crear el archivo first_boot_installer.sh y copiar el script proporcionado en su interior, sigue estos pasos:

```bash
nano first_boot_installer.sh
```

```bash
#!/bin/bash

########################## README ############ README ############# README #########################
# El script first_boot_installer, tiene como finalidad desplegar el componente sobre una instancia
# de linux exclusiva. Las variables que utiliza son "variables de entorno" de la instancia que estÃƒÆ’Ã‚Â¡
# por lanzar el script como acto seguido al primer boot del sistema operativo.
# Dichas variables podrÃƒÆ’Ã‚Â¡n ser provisionadas por un archivo .env (ej: Vagrant) o bien utilizando este
# script como plantilla de terraform.
#
# En el caso de necesitar ejecutar este script manualmente sobre el user_data de una instancia cloud
# o bien sobre una instancia onpremise a travÃƒÆ’Ã‚Â©s de una conexiÃƒÆ’Ã‚Â³n ssh, entonces se deberÃƒÆ’Ã‚Â¡ copiar
# esta plantilla hacia un archivo ignorado por git (first_boot_installer.sh) para luego sobre
# dicha copia, descomentar las lÃƒÆ’Ã‚Â­neas que comienzan con la cadena "export", para posteriormente
# introducir el valor deseado a cada variable.
########################## README ############ README ############# README #########################

# ******************** SET ENV VARS ******************** #
# ********** # The infrastructure environment:
# ********** # Values: onpremise | digitalocean | linode | vultr | aws
export oml_infras_stage=onpremise

# ********** # The GitLab branch
export oml_app_release=master

# ********** # OMniLeads tenant NAME
export oml_tenant_name=oml

# ********** # Device for recordings
# ********** # Values: local | s3 | s3-minio | s3-aws | nfs
# local: if you want OMniLeads to consider storage data & recordings on local disk.
# nfs: if you want OMniLeads to consider storage data & recordings on /opt/callrec (NFS mount point)
# s3: if you want OMniLeads to consider storage data & recordings on generic S3 object storage.
# s3-aws: if you want OMniLeads to consider storage data & recordings on AWS S3 object storage.
# s3: if you want OMniLeads to consider storage data & recordings on minio S3 object storage.
# ***********
export oml_callrec_device=local

# ********** # Parameters for S3 when s3 is selected as store
# ********** # s3_access_key or NULL
export s3_access_key=NULL
# ********** # s3_access_secret_key | NULL
export s3_secret_key=NULL
# ********** # se bucket name | NULL
export s3_bucket_name=NULL
# ********** # s3 endpoint url Only when use non AWS S3 object | NULL
export s3_endpoint_url=NULL
# ********** # s3 bucket region | NULL
export s3_region=NULL

# ********** # Parameters for NFS when nfs is selected as store for oml_callrec_device
# ********** # NFS netaddr | NULL
export nfs_host=NULL

# ********** # Set your network interface
export oml_nic=ens192

# ********** # ******* Variables for ACD Asterisk *******
# ********** # AMI connection from OMLApp
export oml_ami_user=omnileadsami
export oml_ami_password=d3c0ntr0l
# ********** # Values: NULL or IP address/FQDN in case of ACD isolate host
export oml_acd_host=NULL

# ********** # ******* Variables for PGSQL *******
# ********** # POSTGRESQL network address and port
# ********** # Values: NULL | IP address or FQDN  in case of ACD isolate host
export oml_pgsql_host=NULL
export oml_pgsql_port=5432
# ********** # POSTGRESQL user, password and DB parameters
export oml_pgsql_db=omnileads
export oml_pgsql_user=omnileads
export oml_pgsql_password=d3c0ntr0l
# ********** # If PGSQL runs on cloud cluster, set this parameter to true
export oml_pgsql_cloud=NULL

# ********** # If PGSQL runs on HA cluster, uncomment and set this parameter to "true"
export oml_pgsql_cluster=false
export oml_pgsql_read_only_host=

# ********* # IF PGSQL engine require SSL
export oml_pgsql_ssl=NULL


# ********** ## ******* Variables for Dialer *******
export api_dialer_user=omnileads
export api_dialer_password=d3c0ntr0l
# ********** # Values: NULL | IP address or FQDN
export oml_dialer_host=NULL

# ********** # ******* Variables for WebRTC bridge *******
# ********** # Values: NULL | IP address or FQDN  in case of RTPengine isolate host
export oml_rtpengine_host=NULL
# ********** # Values: NULL | IP address or FQDN  in case of Kamailio isolate host
export oml_kamailio_host=NULL

# ********** # ******* Variables for Redis *******
# ********** # Values: NULL | IP address or FQDN  in case of Websockets isolate host
export oml_redis_host=NULL

# ********** # ******* Variables for Websockets *******
# ********** # Values: NULL | IP address or FQDN
export oml_websocket_host=NULL
export oml_websocket_port=NULL

# ********** # In case of Redis HA Cluster deploy
# ********** # Values: true or NULL
export oml_redis_ha=NULL
# ********** # Values: NULL or IP addr / FQDN
export oml_sentinel_host_01=NULL
export oml_sentinel_host_02=NULL
export oml_sentinel_host_03=NULL

# *********************** NAT voip webrtc setting ***************************************************************************************
# External IP. This parameter will set the public IP for SIP and RTP traffic, on environments where calls go through a firewall.        #
# Values: auto | IP address | none                                                                                                      #
# auto = The public IP will be obtained from http://ipinfo.io/ip. It depends on the WAN connection that OML is using to go to Internet. #
# X.X.X.X = The public IP is set manually.                                                                                              #
# none = If the agents are working on a LAN environment, and don't need a public IP.                                                    #
# ***************************************************************************************************************************************
export oml_extern_ip=auto

# ********** # ******* Vaiables for OMLApp web *******
export oml_tz=America/Bogota
# ********** # Session Cookie Age (SCA): It's the time in seconds that will last the https session when inactivity
# ********** # is detected in the session (by default is 1 hour)
export oml_app_sca=3600
# ********** # Ephemeral Credentials TTL (ECTTL): It's the time in seconds that will last the SIP credentials
# ********** # used to authenticate a SIP user in the telephony system (by default 8 hours)
export oml_app_ecctl=28800
# ********** # Login failure limit (LFM): It's the number of attempts a user has to enter an incorrect password in login
# ********** # Decrease it if paranoic reasons
export oml_app_login_fail_limit=10

# ********** # Values: true | false
export oml_app_reset_admin_pass=true

# ********** # Above 200 users enable this
# ********** # Values: true | NULL
export oml_high_load=NULL

# ********** # Google maps API
export oml_google_maps_api_key=NULL
export oml_google_maps_center='{ "lat": -31.416668, "lng": -64.183334 }'

###########################################################################
# ------------------------ SMTP relay settings -------------------------- #
# You can modify these parameters according to your own smtp relay server #
# On the contrary, we leave the google smtp relay configured.             #
###########################################################################

# **** Values: true or false
export oml_smtp_relay=false

# **** If smtp_relay is true, then you must to define
# **** some of these parameters:

#export oml_email_default_from=
#export oml_email_host=
#export oml_email_password=
#export oml_email_user=
#export oml_email_port=
#export oml_email_use_tls=
#export oml_email_use_ssl=
export oml_email_ssl_certfile=NULL
export oml_email_ssl_keyfile=NULL

# ******************** SET ENV VARS ******************** #

oml_app_repo_url=https://gitlab.com/omnileads/ominicontacto.git
SRC=/usr/src
PATH_DEPLOY=install/onpremise/deploy/ansible
CALLREC_DIR_DST=/opt/omnileads/asterisk/var/spool/asterisk/monitor
SSM_AGENT_URL="https://s3.amazonaws.com/ec2-downloads-windows/SSMAgent/latest/linux_amd64/amazon-ssm-agent.rpm"
S3FS="/bin/s3fs"
PATH_CERTS="$(cd "$(dirname "$BASH_SOURCE")" &> /dev/null && pwd)/certs"

echo "******************** OML RELEASE = ${oml_app_release} ********************"
echo ""
sleep 5

echo "******************** check vars ********************"
if [ -z "$oml_nic" ]; then
    echo "NIC variable is empty, you must to set oml_nic Network Interface parameter !!! "
		exit
fi

echo "******************** clean up ********************"
rm -rf $SRC/ominicontacto

echo "******************** IPV4 address config ********************"

case ${oml_infras_stage} in
	aws)
		echo -n "AWS"
		PRIVATE_IPV4=$(ip addr show ${oml_nic} | grep "inet\b" | awk '{print $2}' | cut -d/ -f1)
		PUBLIC_IPV4=$(curl ifconfig.co)
		;;
	digitalocean)
		echo -n "DigitalOcean"
		PUBLIC_IPV4=$(curl -s http://169.254.169.254/metadata/v1/interfaces/public/0/ipv4/address)
		PRIVATE_IPV4=$(curl -s http://169.254.169.254/metadata/v1/interfaces/private/0/ipv4/address)
		;;
	linode)
		echo -n "Linode"
		PRIVATE_IPV4=$(ip addr show ${oml_nic} |grep "inet 192.168" |awk '{print $2}' | cut -d/ -f1)
		PUBLIC_IPV4=$(curl checkip.amazonaws.com)
		;;
	onpremise)
		echo -n "Onpremise CentOS7 Minimal"
		PRIVATE_IPV4=$(ip addr show ${oml_nic} | grep "inet\b" | awk '{print $2}' | cut -d/ -f1)
		PUBLIC_IPV4=$(curl ifconfig.co)
		;;
	vagrant)
		echo -n "Vagrant CentOS7 Minimal CI/CD"
		PRIVATE_IPV4=$STAGING_IP_CENTOS
		PUBLIC_IPV4=$(curl ifconfig.co)
		;;
	*)
		echo -n "You must to set STAGE variable\n"
		;;
esac

echo "******************** STAGE fix /etc/hosts ********************"

case ${oml_infras_stage} in
	digitalocean)
		echo -n "DigitalOcean"
		sed -i 's/127.0.0.1 '$(hostname)'/#127.0.0.1 '$(hostname)'/' /etc/hosts
		sed -i 's/::1 '$(hostname)'/#::1 '$(hostname)'/' /etc/hosts
		;;
	vultr)
		echo -n "Vultr"
		TEMP_HOSTNAME=$(hostname)
		sed -i 's/127.0.0.1 '$TEMP_HOSTNAME'/#127.0.0.1 '$TEMP_HOSTNAME'/' /etc/hosts
		sed -i 's/::1       '$TEMP_HOSTNAME'/#::1 '$TEMP_HOSTNAME'/' /etc/hosts
		;;
	*)
		echo -n "Your stage is clean.\n"
		;;
esac

echo "******************** SELinux and firewalld disable ********************"

setenforce 0
sed -i 's/^SELINUX=.*/SELINUX=disabled/' /etc/sysconfig/selinux
sed -i 's/^SELINUX=.*/SELINUX=disabled/' /etc/selinux/config
systemctl disable firewalld > /dev/null 2>&1
systemctl stop firewalld > /dev/null 2>&1

echo "******************** yum update and install packages ********************"

case ${oml_infras_stage} in
	aws)
		yum remove -y python3 python3-pip
		yum install -y $SSM_AGENT_URL
		yum install -y patch libedit-devel libuuid-devel git podman
		amazon-linux-extras install -y epel
		amazon-linux-extras install python3 -y
		systemctl start amazon-ssm-agent
		;;
	*)
		yum -y install git python3 python3-pip kernel-devel epel-release libselinux-python3 awscli podman
		;;
esac

echo "******************** Ansible installation ********************"

pip3 install --upgrade pip
pip3 install boto3  'ansible==2.9.2'
export PATH="$HOME/.local/bin/:$PATH"

echo "******************** git clone omnileads repo ********************"

cd $SRC
git clone --recurse-submodules --branch ${oml_app_release} ${oml_app_repo_url}
cd ominicontacto
git submodule update --remote

echo "******************** inventory setting ********************"

sed -i "s/#localhost ansible/localhost ansible/g" $PATH_DEPLOY/inventory

# PGSQL edit inventory params **************************************************

if [[ "${oml_pgsql_cloud}"  == "true" ]];then
	sed -i "s/postgres_cloud=false/postgres_cloud=true/g" $PATH_DEPLOY/inventory
fi
if [[ "${oml_pgsql_db}"  != "NULL" ]];then
	sed -i "s/postgres_database=omnileads/postgres_database=${oml_pgsql_db}/g" $PATH_DEPLOY/inventory
fi
if [[ "${oml_pgsql_user}"  != "NULL" ]];then
	sed -i "s/#postgres_user=omnileads/postgres_user=${oml_pgsql_user}/g" $PATH_DEPLOY/inventory
fi
if [[ "${oml_pgsql_password}"  != "NULL" ]];then
	sed -i "s/#postgres_password=my_very_strong_pass/postgres_password=${oml_pgsql_password}/g" $PATH_DEPLOY/inventory
fi
if [[ "${oml_pgsql_host}"  != "NULL" ]];then
	sed -i "s/#postgres_host=/postgres_host=${oml_pgsql_host}/g" $PATH_DEPLOY/inventory
fi
if [[ "${oml_pgsql_port}"  != "NULL" ]];then
	sed -i "s/#postgres_port=/postgres_port=${oml_pgsql_port}/g" $PATH_DEPLOY/inventory
fi
if [[ "${oml_pgsql_cluster}"  == "true" ]];then
	sed -i "s/postgres_ha=false/postgres_ha=True/g" $PATH_DEPLOY/inventory
	sed -i "s/postgres_ro_host=/postgres_ro_host=${oml_pgsql_read_only_host}/g" $PATH_DEPLOY/inventory
fi
if [[ "${oml_pgsql_ssl}" != "NULL" ]];then
sed -i "s/postgres_ssl=false/postgres_ssl=true/g" $PATH_DEPLOY/inventory
fi


# Asterisk ACD parameters *******

if [[ "${oml_ami_user}"  != "NULL" ]];then
	sed -i "s/#ami_user=omnileadsami/ami_user=${oml_ami_user}/g" $PATH_DEPLOY/inventory
fi
if [[ "${oml_ami_password}"  != "NULL" ]];then
	sed -i "s/#ami_password=5_MeO_DMT/ami_password=${oml_ami_password}/g" $PATH_DEPLOY/inventory
fi
if [[ "${oml_acd_host}"  != "NULL" ]];then
	sed -i "s/#asterisk_host=/asterisk_host=${oml_acd_host}/g" $PATH_DEPLOY/inventory
fi

# Wombat Dialer parameters *******

if [[ "${api_dialer_user}"  != "NULL" ]];then
	sed -i "s/#dialer_user=demoadmin/dialer_user=${api_dialer_user}/g" $PATH_DEPLOY/inventory
fi
if [[ "${api_dialer_password}"  != "NULL" ]];then
	sed -i "s/#dialer_password=demo/dialer_password=${api_dialer_password}/g" $PATH_DEPLOY/inventory
fi
if [[ "${oml_dialer_host}" != "NULL" ]];then
	sed -i "s/#dialer_host=/dialer_host=${oml_dialer_host}/g" $PATH_DEPLOY/inventory
fi

# WebRTC kamailio & rtpengine params *******

if [[ "${oml_kamailio_host}"  != "NULL" ]];then
	sed -i "s/#kamailio_host=/kamailio_host=${oml_kamailio_host}/g" $PATH_DEPLOY/inventory
fi
if [[ "${oml_rtpengine_host}" != "NULL" ]];then
	sed -i "s/#rtpengine_host=/rtpengine_host=${oml_rtpengine_host}/g" $PATH_DEPLOY/inventory
fi
if [[ "${oml_extern_ip}" != "NULL" ]];then
	sed -i "s/#extern_ip=auto/extern_ip=${oml_extern_ip}/g" $PATH_DEPLOY/inventory
fi

# Redis, Nginx and Websockets params *******
if [[ "${oml_redis_host}" != "NULL" ]];then
	sed -i "s/#redis_host=/redis_host=${oml_redis_host}/g" $PATH_DEPLOY/inventory
fi

if [[ "${oml_redis_ha}" == "true" ]];then
sed -i "s/redis_ha=false/redis_ha=true/g" $PATH_DEPLOY/inventory
sed -i "s/#sentinel_host_01=/sentinel_host_01=${oml_sentinel_host_01}/g" $PATH_DEPLOY/inventory
sed -i "s/#sentinel_host_02=/sentinel_host_02=${oml_sentinel_host_02}/g" $PATH_DEPLOY/inventory
sed -i "s/#sentinel_host_03=/sentinel_host_03=${oml_sentinel_host_03}/g" $PATH_DEPLOY/inventory
fi


if [[ "$NGINX_HOST" != "NULL" ]];then
	sed -i "s/#nginx_host=/nginx_host=$NGINX_HOST/g" $PATH_DEPLOY/inventory
fi
if [[ "${oml_websocket_host}" != "NULL" ]];then
	sed -i "s/#websocket_host=/websocket_host=${oml_websocket_host}/g" $PATH_DEPLOY/inventory
fi
if [[ "${oml_websocket_port}" != "NULL" ]];then
	sed -i "s/websocket_port=8000/websocket_port=${oml_websocket_port}/g" $PATH_DEPLOY/inventory
fi

# Others App params *******

sed -i "s%\#TZ=%TZ=${oml_tz}%g" $PATH_DEPLOY/inventory

if [[ "$${oml_app_sca}" != "NULL" ]];then
	sed -i "s/sca=3600/sca=${oml_app_sca}/g" $PATH_DEPLOY/inventory
fi
if [[ "${oml_app_ecctl}" != "NULL" ]];then
	sed -i "s/sca=28800/sca=${oml_app_ecctl}/g" $PATH_DEPLOY/inventory
fi
if [[ "${oml_app_login_fail_limit}" != "NULL" ]];then
	sed -i "s/LOGIN_FAILURE_LIMIT=10/LOGIN_FAILURE_LIMIT=${oml_app_login_fail_limit}/g" $PATH_DEPLOY/inventory
fi
if [[ "${oml_app_reset_admin_pass}" == "true" ]];then
	sed -i "s/reset_admin_password=false/reset_admin_password=true/g" $PATH_DEPLOY/inventory
fi

if [[ "${oml_callrec_device}" != "NULL" ]];then
sed -i "s/callrec_device=local/callrec_device=${oml_callrec_device}/g" $PATH_DEPLOY/inventory
fi
if [[ "${s3_access_key}" != "NULL" ]];then
sed -i "s%\#s3_access_key=%s3_access_key=${s3_access_key}%g" $PATH_DEPLOY/inventory
fi
if [[ "${s3_secret_key}" != "NULL" ]];then
sed -i "s%\#s3_secret_key=%s3_secret_key=${s3_secret_key}%g" $PATH_DEPLOY/inventory
fi
if [[ "${s3_bucket_name}" != "NULL" ]];then
sed -i "s%\#s3_bucket_name=%s3_bucket_name=${s3_bucket_name}%g" $PATH_DEPLOY/inventory
fi
if [[ "${s3_endpoint_url}" != "NULL" ]];then
sed -i "s%\#s3url=%s3url=${s3_endpoint_url}%g" $PATH_DEPLOY/inventory
fi
if [[ "${s3_region}" != "NULL" ]];then
sed -i "s%\#s3_region=%s3_region=${s3_region}%g" $PATH_DEPLOY/inventory
fi
if [[ "${oml_high_load}" != "NULL" ]];then
sed -i "s/high_load=false/high_load=${oml_high_load}/g" $PATH_DEPLOY/inventory
fi

if [[ "${oml_google_maps_api_key}" != "NULL" ]];then
sed -i "s%\#google_maps_api_key=%google_maps_api_key=${oml_google_maps_api_key}%g" $PATH_DEPLOY/inventory
sed -i "s%\#google_maps_center=%google_maps_center='${oml_google_maps_center}'%g" $PATH_DEPLOY/inventory
fi

if [[ "${oml_smtp_relay}" == "true" ]];then
sed -i "s%\#email_backend=%email_backend=django.core.mail.backends.smtp.EmailBackend%g" $PATH_DEPLOY/inventory
sed -i "s%\#email_default_from=%email_default_from=${oml_email_default_from}%g" $PATH_DEPLOY/inventory
sed -i "s%\#email_host=%email_host=${oml_email_host}%g" $PATH_DEPLOY/inventory
sed -i "s%\#email_port=%email_port=${oml_email_port}%g" $PATH_DEPLOY/inventory
sed -i "s%\#email_password=%email_password=${oml_email_password}%g" $PATH_DEPLOY/inventory
sed -i "s%\#email_user=%email_user=${oml_email_user}%g" $PATH_DEPLOY/inventory
	if [[ "${oml_email_use_tls}" == "True" ]];then
	sed -i "s%\#email_use_tls=%email_use_tls=${oml_email_use_tls}%g" $PATH_DEPLOY/inventory
	fi
	if [[ "${oml_email_use_ssl}" == "True" ]];then
	sed -i "s%\#email_use_ssl=%email_use_ssl=${oml_email_use_ssl}%g" $PATH_DEPLOY/inventory
	fi
	if [[ "${oml_email_ssl_certfile}" != "NULL" ]];then
	sed -i "s%\#email_ssl_certfile=%email_ssl_certfile=${oml_email_ssl_certfile}%g" $PATH_DEPLOY/inventory
	fi
	if [[ "${oml_email_ssl_keyfile}" != "NULL" ]];then
	sed -i "s%\#email_ssl_keyfile=%email_ssl_keyfile=${oml_email_ssl_keyfile}%g" $PATH_DEPLOY/inventory
	fi
else
sed -i "s%\#email_backend=%email_backend=django.core.mail.backends.dummy.EmailBackend%g" $PATH_DEPLOY/inventory
fi

# User certs verification *******

if [ -f $PATH_CERTS/key.pem ] && [ -f $PATH_CERTS/cert.pem ];then
				cp $PATH_CERTS/key.pem $SRC/ominicontacto/install/onpremise/deploy/ansible/certs
				cp $PATH_CERTS/cert.pem $SRC/ominicontacto/install/onpremise/deploy/ansible/certs
fi

sleep 2
echo "******************** deploy.sh execution ********************"

cd $PATH_DEPLOY
./deploy.sh -i --iface=${oml_nic}

echo "******************** NET File Systen callrec ********************"

case ${oml_callrec_device} in
	nfs)
		echo "Callrec device: NFS \n"
		yum install -y nfs-utils nfs-utils-lib
		if [ ! -d $CALLREC_DIR_DST ];then
			mkdir -p $CALLREC_DIR_DST
			chown omnileads.omnileads -R $CALLREC_DIR_DST
		fi
		echo "${nfs_host}:$CALLREC_DIR_DST $CALLREC_DIR_DST nfs auto,nofail,noatime,nolock,intr,tcp,actimeo=1800 0 0" >> /etc/fstab
		mount -a
		;;
	disk)
		echo "Callrec device: Disk \n"
		mkdir -p $CALLREC_DIR_DST
		echo "$CALLREC_BLOCK_DEVICE  $CALLREC_DIR_DST ext4 defaults,nofail,discard 0 0" >> /etc/fstab
		mount -a
		;;
	*)
		echo "other method ... \n"
		;;
 esac

sleep 5

echo "******************** Exec task if RTP run AIO ********************"

if [[ "${oml_rtpengine_host}" == "NULL" && "${oml_infras_stage}" != "onpremise" ]];then
	echo -n "STAGE rtpengine \n"
	echo "OPTIONS="-i $PUBLIC_IPV4 -o 60 -a 3600 -d 30 -s 120 -n 127.0.0.1:22222 -m 20000 -M 30000 -L 7 --log-facility=local1""  > /etc/rtpengine-config.conf
	systemctl start rtpengine
fi

echo "********************* Deactivate cron callrec convert to mp3 *****************"
if [ "${oml_acd_host}"  != "NULL" ] &&  [ "${oml_callrec_device}"  == "nfs" ];then
sed -i "s/0 1 \* \* \* source/#0 1 \* \* \* source/g" /var/spool/cron/omnileads
fi

if [ "${oml_acd_host}"  != "NULL" ] &&  [ "${oml_callrec_device}"  != "nfs" ];then
sed -i "s/conversor.sh 1 0/conversor.sh 2 0/g" /var/spool/cron/omnileads
fi

echo "******************** sngrep SIP sniffer install ********************"

if [[ "${oml_app_install_sngrep}" == "true" ]];then
	yum install ncurses-devel make libpcap-devel pcre-devel \
			openssl-devel git gcc autoconf automake -y
	cd /root && git clone https://github.com/irontec/sngrep
	cd sngrep && ./bootstrap.sh && ./configure && make && make install
	ln -s /usr/local/bin/sngrep /usr/bin/sngrep
fi

reboot
```

## Paso 16: Otorga permisos de ejecución al archivo

Esto permitirá que el archivo sea ejecutable y puedas proceder con su ejecución:

```bash
chmod +x first_boot_installer.sh
```

## Paso 17: Ejecuta el instalador del primer arranque

Ejecuta el script del primer arranque para comenzar la instalación:

```bash
./first_boot_installer.sh
```
