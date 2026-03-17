==============================================================================
             BLOCKADE - SISTEMA DE CONTROL PARENTAL Y FILTRADO DNS
              
              
              
              
Autor: Álvaro González Román
Proyecto Final de Grado - Sistemas Microinformáticos y Redes (SMR)
Año: 2026
=============================================================================

1. DESCRIPCIÓN DEL PROYECTO
-----------------------------------------------------------------------------
Blockade es una solución de ciberseguridad y control parental a nivel de red, 
empaquetada en una máquina virtual (Debian 13) lista para usar (Plug & Play).
Actúa como router, servidor DHCP, DNS y cortafuegos, filtrando todo el tráfico 
de la red interna antes de que salga a Internet.

Bloquea publicidad, malware y sitios web específicos (ej. ChatGPT, Discord) 
de forma forzada, impidiendo que los usuarios (hijos) puedan saltarse el 
bloqueo cambiando los DNS en sus dispositivos.

2. INSTRUCCIONES DE IMPORTACIÓN EN VIRTUALBOX (¡MUY IMPORTANTE!)
-----------------------------------------------------------------------------
Para que la máquina funcione correctamente y dé internet a los clientes, 
debes configurar los adaptadores de red EXACTAMENTE de esta forma antes de 
arrancarla:

  * Adaptador 1: NAT 
    (Se usa para que el servidor Blockade tenga salida a Internet).

  * Adaptador 2: Red Interna (Nombre: intnet)
    (Se usa para conectar a los clientes/hijos. El servidor repartirá IPs 
    automáticamente por esta interfaz).

3. DIRECCIONES Y ACCESOS
-----------------------------------------------------------------------------
Una vez arrancada la máquina, todos los servicios se inician automáticamente. 
Conecta una máquina cliente (ej. Windows 10/11) a la misma "Red Interna".
El cliente recibirá una IP del rango 192.168.0.20-40 automáticamente.

Accesos desde el cliente web:
  * Manual de Usuario (Apache2): http://192.168.0.100
  * Panel de Control (AdGuard):  http://192.168.0.100:3000

4. CREDENCIALES POR DEFECTO
-----------------------------------------------------------------------------
Panel de Control AdGuard Home:
  - Usuario: Admin
  - Clave:   Admin@123

Sistema Operativo Debian (Consola / SSH):
  - Usuario: root
  - Clave:   toor
  - Usuario: usuario
  - Clave:   usuario
    
5. ARQUITECTURA TÉCNICA Y PUERTOS
-----------------------------------------------------------------------------
El sistema está compuesto por los siguientes servicios enlazados:
  * isc-dhcp-server: Reparte IPs (192.168.0.x) y asigna el DNS (192.168.0.100).
  * BIND9 (Puerto 53): Recibe las peticiones DNS de los clientes y las 
    reenvía al puerto 5454.
  * AdGuard Home (Puerto 5454): Motor que filtra, bloquea dominios no 
    deseados y resuelve los permitidos.
  * AdGuard Web (Puerto 3000): Interfaz gráfica de administración.
  * Apache2 (Puerto 80): Servidor web que aloja el manual de usuario.
  * IPTables: Configurado para hacer IP Masquerade (NAT) y forzar la 
    redirección de todo el tráfico TCP/UDP del puerto 53 hacia el servidor local.

6. CÓMO PROBAR EL SISTEMA
-----------------------------------------------------------------------------
1. Arranca la OVA de Blockade y espera a que pida login (no hace falta entrar).
2. Arranca una máquina Windows configurada en "Red Interna".
3. Abre la consola CMD en Windows y escribe: ipconfig /renew
4. Comprueba que navegas (ej. entra a wikipedia.org).
5. Intenta entrar en discord.com o chatgpt.com.
   acceso mostrando un error (ERR_ADDRESS_INVALID).
6. Entra en http://192.168.0.100:3000 para ver las estadísticas de bloqueo en vivo.
