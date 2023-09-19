# Manual de Instalación de OmniLeads

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
