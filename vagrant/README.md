
Empezando a trabajar con Vagrant
================================

#https://drupalize.me/videos/installing-vagrant-and-virtualbox?p=1526

Primero de todo hemos de instalar la ultima version de Virtualbox.
```
https://www.virtualbox.org/wiki/Downloads
```
Tambien nos bajaremos la ultima versión de vagrant para windows.
```
https://www.vagrantup.com/downloads.html
```


Abrir cmd como admin..
```cmd
C:\HashiCorp\Vagrant\bin>dir
 El volumen de la unidad C es Acer
 El número de serie del volumen es: B6C1-DC73

 Directorio de C:\HashiCorp\Vagrant\bin

10/09/2017  21:32    <DIR>          .
10/09/2017  21:32    <DIR>          ..
07/09/2017  06:08         2.283.520 vagrant.exe
               1 archivos      2.283.520 bytes
               2 dirs  409.189.715.968 bytes libres
```

Ejecutamos `vagrant init` con la imagen que queramos.
Esto lo que hará es generar un `Vagrantfile` que podemos generar nosotros mismos con la informacion pública de
`https://www.vagrantup.com/downloads.html` por ejemplo.

```cmd
C:\HashiCorp\Vagrant\bin>vagrant init precise32 http://files.vagrantup.com/precise32.box

A `Vagrantfile` has been placed in this directory. You are now
ready to `vagrant up` your first virtual environment! Please read
the comments in the Vagrantfile as well as documentation on
`vagrantup.com` for more information on using Vagrant.
```

```cmd
C:\HashiCorp\Vagrant\bin>dir
 El volumen de la unidad C es Acer
 El número de serie del volumen es: B6C1-DC73

 Directorio de C:\HashiCorp\Vagrant\bin

11/09/2017  21:01    <DIR>          .
11/09/2017  21:01    <DIR>          ..
07/09/2017  06:08         2.283.520 vagrant.exe
11/09/2017  21:01             3.044 Vagrantfile
               2 archivos      2.286.564 bytes
               2 dirs  409.188.401.152 bytes libres
```

A continuación levantaremos ese entorno virtual con `vagrant up`
```cmd
C:\HashiCorp\Vagrant\bin>vagrant up
Bringing machine 'default' up with 'virtualbox' provider...
==> default: Box 'precise32' could not be found. Attempting to find and install...
    default: Box Provider: virtualbox
    default: Box Version: >= 0
==> default: Box file was not detected as metadata. Adding it directly...
==> default: Adding box 'precise32' (v0) for provider: virtualbox
    default: Downloading: http://files.vagrantup.com/precise32.box
    default: Progress: 100% (Rate: 2913k/s, Estimated time remaining: --:--:--)
==> default: Successfully added box 'precise32' (v0) for 'virtualbox'!
==> default: Importing base box 'precise32'...
==> default: Matching MAC address for NAT networking...
==> default: Setting the name of the VM: bin_default_1505156798276_5581
==> default: Clearing any previously set network interfaces...
==> default: Preparing network interfaces based on configuration...
    default: Adapter 1: nat
==> default: Forwarding ports...
    default: 22 (guest) => 2222 (host) (adapter 1)
==> default: Booting VM...
==> default: Waiting for machine to boot. This may take a few minutes...
    default: SSH address: 127.0.0.1:2222
    default: SSH username: vagrant
    default: SSH auth method: private key
    default: Warning: Connection reset. Retrying...
    default: Warning: Connection aborted. Retrying...
    default: Warning: Connection reset. Retrying...
    default: Warning: Connection aborted. Retrying...
    default: Warning: Connection reset. Retrying...
    default: Warning: Connection aborted. Retrying...
    default: Warning: Connection reset. Retrying...
    default: Warning: Connection aborted. Retrying...
    default:
    default: Vagrant insecure key detected. Vagrant will automatically replace
    default: this with a newly generated keypair for better security.
    default:
    default: Inserting generated public key within guest...
    default: Removing insecure key from the guest if it's present...
    default: Key inserted! Disconnecting and reconnecting using new SSH key...
==> default: Machine booted and ready!
==> default: Checking for guest additions in VM...
    default: The guest additions on this VM do not match the installed version of
    default: VirtualBox! In most cases this is fine, but in rare cases it can
    default: prevent things such as shared folders from working properly. If you see
    default: shared folder errors, please make sure the guest additions within the
    default: virtual machine match the version of VirtualBox you have installed on
    default: your host and reload your VM.
    default:
    default: Guest Additions Version: 4.2.0
    default: VirtualBox Version: 5.1
==> default: Mounting shared folders...
    default: /vagrant => C:/HashiCorp/Vagrant/bin
```

Accedemos via ssh con putty contra  `127.0.0.1:2222` y con el usuario `vagrant` y contraseña `vagrant`.

Contenido del Vagrantfile
```bash
C:\HashiCorp\Vagrant\bin>type Vagrantfile
# -*- mode: ruby -*-
# vi: set ft=ruby :

# All Vagrant configuration is done below. The "2" in Vagrant.configure
# configures the configuration version (we support older styles for
# backwards compatibility). Please don't change it unless you know what
# you're doing.
Vagrant.configure("2") do |config|
  # The most common configuration options are documented and commented below.
  # For a complete reference, please see the online documentation at
  # https://docs.vagrantup.com.

  # Every Vagrant development environment requires a box. You can search for
  # boxes at https://vagrantcloud.com/search.
  config.vm.box = "precise32"

  # The url from where the 'config.vm.box' box will be fetched if it
  # doesn't already exist on the user's system.
  config.vm.box_url = "http://files.vagrantup.com/precise32.box"

  # Create a forwarded port mapping which allows access to a specific port
  # within the machine from a port on the host machine. In the example below,
  # accessing "localhost:8080" will access port 80 on the guest machine.
  # NOTE: This will enable public access to the opened port
  # config.vm.network "forwarded_port", guest: 80, host: 8080

  # Create a forwarded port mapping which allows access to a specific port
  # within the machine from a port on the host machine and only allow access
  # via 127.0.0.1 to disable public access
  # config.vm.network "forwarded_port", guest: 80, host: 8080, host_ip: "127.0.0.1"

  # Create a private network, which allows host-only access to the machine
  # using a specific IP.
  # config.vm.network "private_network", ip: "192.168.33.10"

  # Create a public network, which generally matched to bridged network.
  # Bridged networks make the machine appear as another physical device on
  # your network.
  # config.vm.network "public_network"

  # Share an additional folder to the guest VM. The first argument is
  # the path on the host to the actual folder. The second argument is
  # the path on the guest to mount the folder. And the optional third
  # argument is a set of non-required options.
  # config.vm.synced_folder "../data", "/vagrant_data"

  # Provider-specific configuration so you can fine-tune various
  # backing providers for Vagrant. These expose provider-specific options.
  # Example for VirtualBox:
  #
  # config.vm.provider "virtualbox" do |vb|
  #   # Display the VirtualBox GUI when booting the machine
  #   vb.gui = true
  #
  #   # Customize the amount of memory on the VM:
  #   vb.memory = "1024"
  # end
  #
  # View the documentation for the provider you are using for more
  # information on available options.

  # Enable provisioning with a shell script. Additional provisioners such as
  # Puppet, Chef, Ansible, Salt, and Docker are also available. Please see the
  # documentation for more information about their specific syntax and use.
  # config.vm.provision "shell", inline: <<-SHELL
  #   apt-get update
  #   apt-get install -y apache2
  # SHELL
end
```




