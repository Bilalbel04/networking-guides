# Montar un servidor DNS con Bind9

## Instalación de Bind9
Para instalar Bind9 en un sistema basado en Debian/Ubuntu, ejecuta:

```sh
apt-get install bind9
```

## Configurar reenviadores (Forwarders)
Para mejorar la resolución de nombres, configuramos reenviadores en el archivo de opciones:

```sh
nano /etc/bind/named.conf.options
```

Dentro del archivo, añadimos:

```plaintext
options {
    forwarders {
        8.8.8.8;
        8.8.4.4;
    };
};
```

## Creación de una zona DNS
### Paso 1: Declarar la zona
Editamos el archivo de configuración local de Bind9:

```sh
sudo nano /etc/bind/named.conf.local
```

Añadimos la siguiente configuración:

```plaintext
zone "DOMINIO.LOCAL" IN {
    type master;
    file "/etc/bind/zones/DOMINIO.zone";
};
```

### Paso 2: Crear el archivo de configuración de la zona
Creamos el directorio para las zonas si no existe:

```sh
sudo mkdir -p /etc/bind/zones
```

Copiamos un archivo base de configuración:

```sh
sudo cp /etc/bind/db.local /etc/bind/zones/DOMINIO.zone
```

### Paso 3: Configurar la zona
Editamos el archivo de la zona para definir los registros adecuados:

```sh
sudo nano /etc/bind/zones/DOMINIO.zone
```

Ejemplo de configuración:

```plaintext
@   IN  SOA  DOMINIO. root.DOMINIO.COM. (
          2 ; Serial
     604800 ; Refresh
      86400 ; Retry
    2419200 ; Expire
     604800 ); Negative Cache TTL

@   IN  NS      DOMINIO.
@   IN  A       IP_DEL_SERVIDOR_DNS
```

Guardamos los cambios y reiniciamos Bind9:

```sh
sudo systemctl restart bind9
```

## Bloquear acceso a ciertas páginas web
Para bloquear dominios específicos, por ejemplo, `pccomponentes.com`, seguimos estos pasos:

### Paso 1: Editar `named.conf.local`

Abrimos el archivo de configuración local:

```sh
sudo nano /etc/bind/named.conf.local
```

Añadimos la siguiente configuración:

```plaintext
zone "pccomponentes.com" {
    type master;
    file "block";
};
```

### Paso 2: Reiniciar Bind9

```sh
sudo systemctl restart bind9
```

Con esto, el dominio `pccomponentes.com` quedará bloqueado en nuestro servidor DNS.
