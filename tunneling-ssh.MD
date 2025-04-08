# Cómo configurar un túnel inverso SSH

Un túnel inverso permite conectarse de una red a otra, sorteando firewalls u otras restricciones intermedias.

## Requisitos
Para configurar un túnel inverso, se necesitan tres máquinas:

1. **Cliente (Emisor):** La máquina desde la que queremos conectarnos.
2. **Enlazador (Puerta):** Actúa como intermediario y permite el reenvío de la conexión.
3. **Servidor (Receptor):** La máquina a la que nos conectaremos.

## Configuración previa
Tanto en **Puerta** como en **Servidor**, es necesario habilitar el reenvío de conexiones SSH. Para ello, modificamos el archivo de configuración:

```sh
sudo nano /etc/ssh/sshd_config
```

Buscamos y activamos la siguiente línea:

```plaintext
GatewayPorts yes
```

Guardamos los cambios y reiniciamos el servicio SSH:

```sh
sudo systemctl restart ssh
```

## Creación del túnel inverso
El **Servidor** no tiene acceso directo al **Cliente**, pero la **Puerta** sí tiene acceso a ambos.

Desde **Servidor**, ejecutamos el siguiente comando para establecer el túnel:

```sh
ssh -NR PE:IP:PS usuario@IP_PUERTA
```

### Parámetros:
- `-N` → No abre una sesión interactiva, solo establece el túnel.
- `-R` → Indica que se está creando un túnel inverso.
- `PE` → Puerto de entrada en la Puerta.
- `PS` → Puerto de salida hacia el Servidor.
- `&` → Opcional, coloca el proceso en segundo plano.

Ejemplo práctico:

```sh
ssh -NR 2275:localhost:22 usuario_puerta@IP_PUERTA
```

Si el proceso se ejecutó en segundo plano, podemos administrarlo con:

```sh
fg  # Traer el proceso al primer plano
bg  # Enviar el proceso al segundo plano
```

## Conexión desde el Cliente
Una vez el túnel está activo, el Cliente puede acceder al Servidor a través de la Puerta usando el siguiente comando:

```sh
ssh -p 2275 usuario_servidor@IP_PUERTA
```

---
