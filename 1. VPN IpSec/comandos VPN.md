# Configuración VPN Site to site Juniper
## Paso 1: Configuración de los parámetros generales de la VPN
Accede al firewall Juniper utilizando SSH, Telnet o la interfaz de línea de comandos (CLI).
# Ingresa al modo de configuración.
```go
configure 
```
# Crea una interfaz para la VPN IPsec.
```go
set interfaces st0 unit 0 family inet
```
Explicación: Esto crea una interfaz de túnel (st0) para la VPN IPsec.
## Paso 2: Configuración de la fase 1 (IKE)
# Configura la fase 1 del túnel IPsec

```go
set security ike proposal <nombre_propuesta> authentication-method <método_autenticación>
set security ike proposal <nombre_propuesta> dh-group <grupo_DH>
set security ike proposal <nombre_propuesta> encryption-algorithm <algoritmo_cifrado>
set security ike proposal <nombre_propuesta> lifetime-seconds <tiempo_vida_segundos>
```
Explicación:
<nombre_propuesta>: Un nombre descriptivo para la propuesta IKE.
<método_autenticación>: Método de autenticación, como pre-shared-keys (psk) o certificados digitales.
<grupo_DH>: Grupo de Diffie-Hellman para el intercambio de claves.
<algoritmo_cifrado>: Algoritmo de cifrado, como aes256.
<tiempo_vida_segundos>: Tiempo de vida de la negociación IKE en segundos.

** Ejemplo **
set security ike proposal proposal1 authentication-method pre-shared-keys
set security ike proposal proposal1 dh-group group2
set security ike proposal proposal1 authentication-algorithm sha1
set security ike proposal proposal1 encryption-algorithm aes-256-cbc
set security ike proposal proposal1 lifetime-seconds 3600
# Configura el peer remoto
```go
set security ike policy <nombre_politica> mode <modo>
set security ike policy <nombre_politica> proposals <nombre_propuesta>
set security ike policy <nombre_politica> pre-shared-key ascii-text <clave_precompartida>
```
Explicación:
<nombre_politica>: Un nombre descriptivo para la política IKE.
<modo>: Modo de operación de la VPN IPsec, como main o aggressive.
<nombre_propuesta>: El nombre de la propuesta IKE que definiste anteriormente.
<clave_precompartida>: La clave precompartida para la autenticación del peer remoto.
## Paso 3: Configuración de la fase 2 (IPsec)
# Configura la fase 2 del túnel IPsec.

```go
set security ipsec proposal <nombre_propuesta> protocol <protocolo>
set security ipsec proposal <nombre_propuesta> authentication-algorithm <algoritmo_autenticación>
set security ipsec proposal <nombre_propuesta> encryption-algorithm <algoritmo_cifrado>
set security ipsec proposal <nombre_propuesta> lifetime-seconds <tiempo_vida_segundos>
```
Explicación:
<nombre_propuesta>: Un nombre descriptivo para la propuesta IPsec.
<protocolo>: Protocolo de seguridad, como esp.
<algoritmo_autenticación>: Algoritmo de autenticación, como hmac-sha1-96.
<algoritmo_cifrado>: Algoritmo de cifrado, como aes256-cbc.
<tiempo_vida_segundos>: Tiempo de vida de la negociación IPsec en segundos.

** Ejemplo **
set security ipsec proposal proposal2 protocol esp
set security ipsec proposal proposal2 authentication-algorithm hmac-sha1-96
set security ipsec proposal proposal2 encryption-algorithm aes-256-cbc
set security ipsec proposal proposal2 lifetime-seconds 3600
# Configura la política IPsec.

```go
set security ipsec policy <nombre_politica> perfect-forward-secrecy keys <tamaño_clave_DH>
set security ipsec policy <nombre_politica> proposals <nombre_propuesta>
```
Explicación:
<nombre_politica>: Un nombre descriptivo para la política IPsec.
<tamaño_clave_DH>: El tamaño de la clave Diffie-Hellman para Perfect Forward Secrecy (PFS).
<nombre_propuesta>: El nombre de la propuesta IPsec que definiste anteriormente.
## Paso 4: Configuración de las interfaces y la política de seguridad
# Asigna la interfaz de salida para el túnel IPsec.

```go
set security zones security-zone <zona_segura> interfaces st0.0
```
Explicación:
<zona_segura>: La zona de seguridad a la que pertenece la interfaz.
** Ejemplo **
set security zones security-zone trust interfaces st0.0
# Crea una política de seguridad para permitir el tráfico a través del túnel IPsec.

```go
set security policies from-zone <zona_origen> to-zone <zona_destino> policy <nombre_politica> match source-address <direccion_origen> destination-address <direccion_destino> application <aplicacion> then permit
set security policies from-zone <zona_origen> to-zone <zona_destino> policy <nombre_politica> then permit tunnel ipsec-vpn <nombre_tunel>
```
Explicación:
<zona_origen>: Zona de seguridad del tráfico de origen.
<zona_destino>: Zona de seguridad del tráfico de destino.
<nombre_politica>: Un nombre descriptivo para la política de seguridad.
<direccion_origen>: Dirección IP de origen.
<direccion_destino>: Dirección IP de destino.
<aplicacion>: La aplicación o el tipo de tráfico que deseas permitir.
<nombre_tunel>: El nombre del túnel IPsec.

## Paso 5: Confirmar y aplicar la configuración
# Verifica la configuración.

```go
show security ike security-associations
show security ipsec security-associations
```

# Guarda los cambios y activa la configuración.

```go
commit
```
