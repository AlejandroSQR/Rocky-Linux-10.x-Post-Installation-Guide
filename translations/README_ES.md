![alt text](https://github.com/AlejandroSQR/Rocky-Linux-10.x-Post-Installation-Guide/blob/main/images/rockypostguide10xlogo.svg "RockyL10XPI")

# Introducción
Rocky Linux 10 es una distribución basada en RHEL que tiene como objetivo la estabilidad a largo plazo, que se mantiene constante a lo largo del tiempo y evita cambios frecuentes o rápidos. Esta guia sirve para aquellos usuarios que deseen tener su escritorio actualizado y lo escencial para poder trabajar.

# 0.1 Antes de Instalar

## [IMPORTANTE] Secure Boot Desactivado 
En esta guía se hizo en base a una versión con Rocky Linux sin Secure Boot, aun así en algunos puntos se resalta que hacer en caso de que esté activado.

## [IMPORTANTE] NO está hecho para Distribuciones con Actualizaciones Frecuentes 

**NO** intentes con distribuciones Linux que se actualizan constantemente, como Fedora Workstation/Spin, Nobara, Bazzite, Asahi, Ultramarine, etc.

> [!WARNING]
> Si quieres aplicar esta guía con las distribuciones mencionadas anteriormente, es posible que funcione, pero es muy probable que dañes tu sistema operativo debido a la frecuencia de las actualizaciones que ofrece cada distribución. Esto incluye herramientas, kernel, controladores y kits de desarrollo, que se actualizan en períodos cortos de tiempo.

> [!TIP]
> Si buscas un tutorial para distribuciones basadas en Fedora, consulta la Guía de Post-Instalación para Fedora 43) (Puede funcionar en versiones anteriores de Fedora).

[https://github.com/devangshekhawat/Fedora-43-Post-Install-Guide](https://github.com/devangshekhawat/Fedora-43-Post-Install-Guide)

## [EXPERIMENTAL] Distros Basados en RHEL 
Aunque la guía está escrita para funcionar con Rocky Linux 10 y versiones posteriores, también puede funcionar en otros sistemas basados en RHEL, como lo son:
- Alma Linux
- CentOS
  
> [!WARNING]  
> No se garantiza la compatibilidad total o parcial, algunas secciones o herramientas pueden comportarse de manera diferente dependiendo de la distro basada en RHEL incluidos sus repositorios.

> [!IMPORTANT]
> También aplica a versiones anteriores de Rocky (Rocky Linux 8 y 9)

## Diferencias entre ISOs de Rocky Linux

Link para descargar Rocky Linux 10.x
[https://rockylinux.org/download](https://rockylinux.org/download)

> [!IMPORTANT]
> En esta guía se utilizará la instalación completa **(DVD ISO)**, ya que proporciona GNOME de forma predeterminada y limpia, además no es necesario seleccionar configuraciones adicionales.
  
- La versión **Online (Boot ISO)** requiere conexión a Internet durante la instalación, lo que le ofrece más opciones, repositorios y programas en función de lo que desees, pero eso no cambiará los pasos de esta guía.

 > [!TIP]
> Si prefieres una configuración como la versión completa (DVD ISO), solo tiene que marcar Workstation/Desktop durante la instalación.

- La versión **(Minimal ISO)** no instalará ninguna interfaz gráfica después de la instalación, todo lo que se haga después de reiniciar se realizará desde la línea de comandos.
 > [!TIP]
> Si deseas instalar un entorno gráfico, pruebe a utilizar este comando (Por defecto, esto instala el entorno Gnome)
```
sudo dnf groupinstall "Server with GUI"
sudo systemctl set-default graphical
sudo reboot
```
> [!IMPORTANT]
> **Cloud, Docker, Workstation Live Images and WSL** no han sido probados

## Primer Vistazo
Si instalaste la versión completa y/o una versión con componentes básicos tendrás 3 repositorios esenciales.
- BaseOS
- AppStream
- Extras.

> [!NOTE]
> Si deseas obtener más información para saber que hace cada repositorio, visita: [https://wiki.rockylinux.org/rocky/repo/](https://wiki.rockylinux.org/rocky/repo/)

# 0.5 Actualización a Rocky Linux 10.x
A partir del 11 de Noviembre del 2025 Rocky empezo a actualizar a versiones 10.X *(Similar a sus versiones anteriores)*

Si tienes la versión 10.0 y existe una versión superior (10.0 → 10.x) actualizalo a la ultima versión, así evitarás errores en esta guía.
## Método 1 - Terminal
```
sudo dnf update
sudo dnf upgrade
```
## Método 2 - Software
**Software > Updates > Check Updates**

# 1. Repositorios Esenciales
Agrega los repositorios "EPEL" (Extra Packages for Enterprise Linux)
```
sudo dnf install epel-release
```
Agrega los repositorios "ElRepo"
```
sudo dnf install https://www.elrepo.org/elrepo-release-10.el10.elrepo.noarch.rpm
```
Agrega los repositorios "RPM Fusion Free & Non Free"
```
sudo dnf install https://download1.rpmfusion.org/free/el/rpmfusion-free-release-$(rpm -E %rhel).noarch.rpm https://download1.rpmfusion.org/nonfree/el/rpmfusion-nonfree-release-$(rpm -E %rhel).noarch.rpm
```
Después de agregar los repositorios que necesites revisa si están habilitados con el comando
```
sudo dnf repolist
```
> [!NOTE]
> En caso de ser necesario, reinicia el sistema para poder ver las aplicaciones en la tienda de Software
```
reboot
```
# 1.5 [OPCIONAL] Instalar Kernel 6.12+
Un kernel nuevo en Rocky Linux no es necesario si no presentas algún problema con tu Hardware o dispositivos externos. En caso de que tu hardware sea muy reciente y no detecte algo en específico, el kernel estable que viene con Rocky puede no funcionar y requerir este paso.
Importar la llave GPG
```
rpm —import https://www.elrepo.org/RPM-GPG-KEY-v2-elrepo.org
```
> [!NOTE]
> ¿Por qué? RPM verifica que no haya sido modificado y que provenga realmente del repositorio oficial.

Instala el nuevo kernel **(Q4 2025)**
```
dnf —enablerepo elrepo-kernel install kernel-ml
```
El repositorio te ofrecerá la versión **6.17.9 (Q4 2025)**

## [IMPORTANTE] Secure Boot después de instalar el nuevo Kernel
Este paso es muy importante si instalaste un kernel más moderno y tienes Secure Boot activado, si ignoras este paso, no te dejará iniciar el S.O correctamente.

Actualizar el repositorio de ElRepo
```
sudo dnf update elrepo-release
```
- Esto nos servirá para tener la key mas reciente y asi evitar problemas
Obtener tu Key de SecureBoot **(ElRepo)**
```
wget https://elrepo.org/SECURE-BOOT-KEY-elrepo.org.der
```
Instalar **mokutil**
```
sudo dnf install mokutil
```
Instalar tu Key con mokutil
```
mokutil --import /etc/pki/elrepo/SECURE-BOOT-KEY-elrepo.org.der
```
Al momento de que ingreses el comando, te pedirá crear una contraseña y que la vuelvas a confirmar
```
input password:
input password again:
```
Una vez creada tu contraseña, reinicia y veras la pantalla de **Shim UEFI Key Management** *(Generalmente es Azul)*

Presione cualquier tecla en un plazo de 10 segundos para continuar. Ingresa utilizando la contraseña establecida anteriormente

> [!NOTE]
> Si ves que al ingresar la contraseña dice "GPG check FAILED" "Error when fetching, installing or upgrading packages" es porque lanzaron una nueva key, lo más recomendable es desactivar Secure Boot para volver a entrar y generar una nueva key.

> [!CAUTION]
> En caso de algún error con el Kernel, regresa a la versión anterior mediante GRUB o vuelve a reinstalar tu S.O

Si tienes mas dudas, visita estas páginas como alternativa a esta instalación del Kernel
[https://vinfrastructure.it/2025/08/installing-latest-kernel-on-a-rocky-linux/](https://vinfrastructure.it/2025/08/installing-latest-kernel-on-a-rocky-linux/)
[https://docs.rockylinux.org/10/guides/custom-linux-kernel/](https://docs.rockylinux.org/10/guides/custom-linux-kernel/)

# 2. Utilidades y Dependencias Esenciales


> [!IMPORTANT]
> Muchas herramientas son para programadores, sin embargo este paso es obligatorio para aquellos que deseen instalar driver en especial los de NVIDIA e INTEL

Habilita el repositorio CodeReady Builder. Tienes dos metodos para hacerlo. (CRB)

#### Metodo 1 - Terminal
```
sudo dnf config-manager --enable crb
```
#### Metodo 2 - Software
- Software > Main Menu > Software Repositories > "Rocky Linux 10 - CRB"


Instala el grupo de "Development Tools" que viene por defecto en Rocky
```
sudo dnf groupinstall "Development Tools"
```
> El comando sirve para construir ciertos paquetes que no están pre compilados y para compilar desde su código fuente

Paquetes esenciales para la gestión de paquetes y el funcionamiento de la red.
```
sudo dnf install wget curl dnf-utils
```

Instalar los paquetes kernel-devel
```
sudo dnf install kernel-devel-matched kernel-headers
```
> [!WARNING]
> Usualmente no pasara nada y no presentara problemas pero si se hizo el paso de actualizar el Kernel puede que el comando no funcione o te instale un paquete con el kernel que venia por defecto al inicio de la instalación. 


# 3. Drivers NVIDIA
> [!WARNING]
> Es **OBLIGATORIO** Instalar todos los paquetes de la sección **"Utilidades y Dependencias Esenciales" (Paso 2)**

> [!NOTE]
> En la mayoría de los casos, lo mejor es instalar los controladores NVIDIA desde la pagina oficial. Sin embargo, Rocky tiene un repositorio para RHEL 10, lo cual facilita la instalación

## Método Recomendado por Rocky Linux
Agrega el repositorio oficial de NVIDIA (Rocky Linux 10 / RHEL 10)
```
sudo dnf config-manager —add-repo http://developer.download.nvidia.com/compute/cuda/repos/rhel10/$(uname -m)/cuda-rhel10.repo
```
Limpia el cache de los Repositorios
```
sudo dnf clean expire-cache
```
Selecciona el Driver que te interesa instalar, tienes dos opciones para hacerlo:
### Drivers Libres / Código Abierto
```
sudo dnf install nvidia-open
```
### Drivers Propietarios
```
sudo dnf install cuda-drivers
```

#### ¿Qué drivers de NVIDIA debería escoger?
- **Drivers de Código Abierto (nvidia-open)**
	- Pueden funcionar en aplicaciones y entornos casuales que necesiten tarjeta gráfica como lo pueden ser juegos o aplicaciones con vistas 2D y 3D.
   
- **Drivers Propietarios (cuda-drivers):**
	- Se requiere más en áreas de trabajo 3D, Edición de video, Simulación o Inteligencia Artificial que necesiten CUDA y aprovechar al máximo el rendimiento de tu Tarjeta Gráfica.
   
## Desactivar "Nouveau" como configuración
Nouveau es un controlador NVIDIA de código abierto que ofrece una funcionalidad limitada en comparación con los controladores propietarios de NVIDIA. Es mejor desactivarlo para evitar conflictos entre controladores

> [!IMPORTANT]
> Aun si instalaste la versión propietaria con CUDA, es recomendable usar este comando, existen situaciones en donde si no se deshabilita, el sistema cambiará automáticamente a drivers de código abierto
```
sudo grubby —args="nouveau.modeset=0 rd.driver.blacklist=nouveau" —update-kernel=ALL
```

## NVIDIA & Secure Boot [IMPORTANTE]
Si tienes Secure Boot activado, necesitas activar este comando:
```
sudo mokutil --import /var/lib/dkms/mok.pub
```
> [!NOTE]
> ¿Por qué? Con Secure Boot activado, Linux solo cargará módulos firmados, los drivers NVIDIA propietarios necesitan estar firmados para cargarse correctamente.

> [!IMPORTANT]
> Después de reiniciar, el sistema le preguntará si desea registrar una clave, seleccione "sí" y le pedirá crear una nueva contraseña que introdujiste en el comando mokutil *(Similar al proceso de Secure Boot del Kernel).*

## Verifica si NVIDIA se instaló correctamente
Al momento de que instalaste los drivers NVIDIA, lo más recomendable es:
- Esperar 5-10 minutos para que los drivers carguen.
- Reiniciar el equipo.
  
En cualquier caso, al momento de instalarse es necesario que verifiques si están funcionando correctamente.

Verifica que el módulo del kernel de NVIDIA esté cargado
```
lsmod | grep nvidia
```
Comprueba que el Sistema detecta el Driver NVIDIA
```
lspci | grep -i nvidia
```
Verifica la versión de NVIDIA
```
nvidia-smi
```

## Alternativas [PRÓXIMAMENTE]
Repositorio "RPMFusion y ElRepo" **(Alternativo)**

- RPMFusion y ELRepo están disponibles para aquellos que prefieren un repositorio NVIDIA comunitario basados en la Distro, para hardware más antiguo o deprecado, RPMFusion funciona mejor.

# 4. Flatpak / Flathub

Flatpak viene instalado por defecto a partir de Rocky 8, solo se necesita habilitar sus repositorios de Flathub

## Metodo 1 (Gnome Software)
Instalar desde la aplicación de Software:
- Descargue, abra e instala el archivo de repositorio Flathub:

[https://dl.flathub.org/repo/flathub.flatpakrepo](https://dl.flathub.org/repo/flathub.flatpakrepo)
  
## Método 2 (Terminal)
```
flatpak remote-add —if-not-exists flathub https://dl.flathub.org/repo/flathub.flatpakrepo
```
Después de agregar los repositorios que necesites revisa si están habilitados con el comando
```
sudo dnf repolist
```
# 5. AppImage + Fuse (Opcional)
Rocky 10 no necesitas instalar dependencias para abrir archivos .AppImage, sin embargo, tener "fuse" hace que evites problemas de compatibilidad
```
sudo dnf in fuse
```
- El paquete viene incluido en el repositorio de "BaseOS"
  
# 6. Codecs de Audio y Video
Codecs y Librerías adicionales para reproducir audio y videos con mayor fluidez en las aplicaciones de Linux

## Codecs Generales
```
sudo dnf4 group install multimedia
sudo dnf swap ffmpeg-free ffmpeg --allowerasing
sudo dnf update @multimedia --setopt="install_weak_deps=False" --exclude=PackageKit-gstreamer-plugin
```
## FFmpeg + Librerias Multimedia
FFmpeg permite procesar, convertir formatos y optimizar streaming, siendo compatible con casi todos los formatos de audio y video.
```
sudo dnf install ffmpeg ffmpeg-devel x264 x265
```
- Estas dependencias se encuentran el los repositorios de "RPMFusion"

# 7. Hardware Video Acceleration (Base)

## Libva
Libva permite que los reproductores y herramientas multimedia usen la GPU para decodificar, codificar o procesar video, reduciendo la carga sobre la CPU y mejorando el rendimiento.
```
sudo dnf install libva
```
## Mesa
Mesa es un conjunto de controladores y librerías gráficas de código abierto que permiten que Linux use aceleración 3D y 2D mediante APIs como OpenGL y Vulkan, aprovechando la GPU.
```
sudo dnf install mesa-dri-drivers
sudo dnf install mesa-libgbm
```

# 8. Hardware Acceleration + GPU (NVIDIA/AMD/INTEL)

A partir de aquí dependiendo de tu hardware, necesitas instalar estos paquetes para sacarle mas provecho a tu GPU (o CPU con gráficos integrados)

## Intel
### Libva + Intel (Gráficos Integrados) (Moderno) (CPU Haswell o superiores)
```
sudo dnf install libva-intel-driver
```
### Libva + Intel (Gráficos Integrados) (Antiguo) (G45, Sandy Bridge, Ivy Bridge o anteriores)
```
sudo dnf install libva-intel-hybrid-driver
```
### Libva + Intel (Discreto) (Arc / Intel Iris Xe MAX)
```
sudo dnf install intel-media-driver
```
> [!NOTE]
> Igualmente puedes instalar todos los paquetes.

## AMD
AMD no usa un paquete separado como Intel o NVIDIA, toda la aceleración de video AMD está integrada dentro de Mesa, igualmente usamos el comando anterior de la sección Mesa.
```
sudo dnf install mesa-dri-drivers
sudo dnf install mesa-libgbm
```

## NVIDIA

### Libva + NVIDIA
```
sudo dnf install libva-nvidia-driver
```

# 9. Optimizaciones
## Desactivar servicio "NetworkManager-wait-online.service"

> [!NOTE]
> Por defecto, Linux comprueba que tengas internet, en caso de que no tengas internet o no se detecta bien tu placa Wifi/Bluetooth puede terminar en un ciclo infinito.

```
sudo systemctl disable NetworkManager-wait-online.service
```
## Desactivar Gnome Software al iniciar el equipo
```
sudo rm /etc/xdg/autostart/org.gnome.Software.desktop
```
> [!NOTE]
> Reduce entre 50 y 200MB de RAM, lo cual es una buena adición si tienes ram limitada

# 10. Complementos Adiciones
## Gnome Tweaks
```
sudo dnf install gnome-tweaks
```

# Observaciones
- Terra no funciona en distros basadas en RHEL

# Cosas por Agregar
- Impresoras y Escanner
- Rendimiento en Laptops
- Optimización en Laptops (AMD/NVIDIA/INTEL)
- Tutorial para controladores AMD / Intel (Arc/Iris)
- Mejor documentación al tener Secure Boot Activado (Kernel y NVIDIA Drivers)
- Agregar alternativas a controladores NVIDIA (ElRepo y RPM Fusion)
- Comportamiento del sistema con el Kernel Actualizado
- Revisar si el sistema se puede romper con actualizaciones generales
- Comportamiento entre Versiones de Rocky 10 (Ej: 10.0 a 10.1 y futuras versiones)
