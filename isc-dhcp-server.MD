# Cómo montar un servidor DHCP

## Instalación del servidor DHCP
### Paso 1: Actualizar el sistema
Antes de instalar el servidor DHCP, actualizamos el sistema:

```sh
apt-get update
apt-get upgrade -y
```

### Paso 2: Instalar el servidor DHCP

```sh
apt-get install isc-dhcp-server -y
```

Es posible que aparezca un error indicando que el servicio no está habilitado. No te preocupes, sigue con los siguientes pasos.

## Configuración del servidor DHCP

### Paso 3: Definir la interfaz de red
Editamos el archivo de configuración:

```sh
nano /etc/default/isc-dhcp-server
```

Buscamos la línea:

```plaintext
INTERFACESv4=" "
```

Y dentro de las comillas ponemos la interfaz de red que usaremos para escuchar las solicitudes DHCP (por ejemplo, `eth0` o `ens33`).

### Paso 4: Configurar el archivo `dhcpd.conf`
Abrimos el archivo de configuración:

```sh
nano /etc/dhcp/dhcpd.conf
```

Dentro, modificamos las siguientes opciones:

```plaintext
option domain-name "example.com";  # Especificamos el dominio de la empresa
option domain-name-servers X.X.X.X;  # Dirección IP del servidor DNS que asignaremos por DHCP
```

### Paso 5: Configurar la subred

Buscamos la línea 35 (aproximadamente) o agregamos una nueva configuración de subred en cualquier parte del archivo:

```plaintext
subnet 192.168.1.0 netmask 255.255.255.0 {
    range 192.168.1.100 192.168.1.200;  # Rango de IPs a repartir
    option routers 192.168.1.1;  # IP del gateway
}
```

Guardamos y cerramos el archivo.

### Paso 6: Reiniciar el servicio DHCP

```sh
systemctl restart isc-dhcp-server
```

---

## Cómo hacer una reserva de IP
Para asignar una IP fija a un dispositivo específico, agregamos la siguiente configuración debajo de la sección de asignación de rangos:

```plaintext
host NOMBRE_DEL_DISPOSITIVO {
    hardware ethernet XX:XX:XX:XX:XX:XX;  # Dirección MAC del cliente
    fixed-address 192.168.1.50;  # IP fija reservada
}
```

Reiniciamos el servicio para aplicar los cambios:

```sh
systemctl restart isc-dhcp-server
```
---
