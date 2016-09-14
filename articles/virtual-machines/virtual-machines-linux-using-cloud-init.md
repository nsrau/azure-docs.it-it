<properties
    pageTitle="Uso di cloud-init per personalizzare una VM Linux durante la creazione | Microsoft Azure"
    description="Uso di cloud-init per personalizzare una VM Linux durante la creazione."
    services="virtual-machines-linux"
    documentationCenter=""
    authors="vlivech"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"
/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/30/2016"
    ms.author="v-livech"
/>

# Uso di cloud-init per personalizzare una VM Linux durante la creazione

Questo articolo illustra come creare script cloud-init per impostare il nome host, aggiornare i pacchetti installati e gestire gli account utente. Gli script cloud-init vengono richiamati durante la creazione della VM dall'interfaccia della riga di comando di Azure.

## Prerequisiti

Prerequisiti: [un account Azure](https://azure.microsoft.com/pricing/free-trial/), [chiavi SSH pubbliche e private](virtual-machines-linux-mac-create-ssh-keys.md) e l'[interfaccia della riga di comando di Azure ](../xplat-cli-install.md) con la modalità Azure Resource Manager attivata tramite `azure config mode arm`.

## Comandi rapidi

Creare uno script cloud-init.txt che consente di impostare il nome host, aggiornare tutti i pacchetti e aggiungere un utente sudo per Linux.

```bash
#cloud-config
hostname: exampleServerName
apt_upgrade: true
users:
  - name: exampleUser
    groups: sudo
    shell: /bin/bash
    sudo: ['ALL=(ALL) NOPASSWD:ALL']
    ssh-authorized-keys:
      - ssh-rsa AAAAB3<snip>==exampleuser@slackwarelaptop
```

Creare una VM Linux che utilizza cloud-init per configurarla durante l'avvio.

```bash
azure group create cloudinitexample westus
```

```bash
azure vm create \
--resource-group cloudinitexample \
--name cloudinitexample \
--location westus \
--os-type Linux \
--nic-name cloudinitnicexample \
--vnet-name cloudinitvnetexample \
--vnet-address-prefix 10.0.0.0/22 \
--vnet-subnet-name cloudinitvsubnet \
--vnet-subnet-address-prefix 10.0.0.0/24 \
--image-urn canonical:ubuntuserver:14.04.2-LTS:latest \
--ssh-publickey-file ~/.ssh/azure_id_rsa.pub \
--admin-username ahmet \
--custom-data cloud-init.txt

```

## Introduzione

Quando si avvia una nuova VM Linux si ottiene un VM Linux standard senza alcuna personalizzazione né alcun adattamento. [Cloud-init](https://cloudinit.readthedocs.org) è un metodo standard che consente di inserire uno script o alcune impostazioni di configurazione in una VM Linux quando viene avviata per la prima volta.

In Azure è possibile apportare modifiche a una VM Linux in fase di distribuzione o avvio in tre modi diversi.

- Inserire gli script che usano cloud-init.
- Inserire gli script che usano l'[estensione VMAccess](virtual-machines-linux-using-vmaccess-extension.md) di Azure.
- Un modello di Azure che usa cloud-init.
- Un modello di Azure che usa [CustomScriptExtention](virtual-machines-linux-extensions-customscript.md).

Per inserire script in qualsiasi momento dopo l'avvio:

- Esecuzione diretta di comandi tramite SSH
- Inserire gli script usando l'[estensione VMAccess](virtual-machines-linux-using-vmaccess-extension.md) di Azure, in modo imperativo o in un modello di Azure
- Strumenti per la gestione della configurazione come Ansible, Salt, Chef e Puppet.

>[AZURE.NOTE]\: l'estensione VMAccess esegue uno script come radice nello stesso modo di SSH. Tuttavia, l'utilizzo dell'estensione della VM consente di abilitare diverse funzionalità di da Azure potenzialmente utili a seconda dello scenario.

### Disponibilità di cloud-init negli alias delle immagini a creazione rapida della VM di Azure:

| Alias | Autore | Offerta | SKU | Versione | cloud-init |
|:----------|:----------|:-------------|:------------|:--------|:-----------|
| CentOS | OpenLogic | Centos | 7,2 | più recenti | no |
| CoreOS | CoreOS | CoreOS | Stabile | più recenti | sì |
| Debian | credativ | Debian | 8 | più recenti | no |
| openSUSE | SUSE | openSUSE | 13\.2 | più recenti | no |
| RHEL | Redhat | RHEL | 7,2 | più recenti | no |
| UbuntuLTS | Canonical | UbuntuServer | 14\.04.4-LTS | più recenti | sì |

Microsoft collabora con i partner per promuovere l'inclusione di cloud-init e utilizza le immagini da essi fornite per Azure.


## Procedura dettagliata

### Aggiunta di uno script cloud-init per la creazione della VM con l'interfaccia della riga di comando di Azure

Per avviare uno script cloud-init durante la creazione di una VM in Azure, specificare il file cloud-init tramite l'opzione `--custom-data` dell'interfaccia della riga di comando di Azure.

```bash
azure group create cloudinitexample westus
```

```bash
azure vm create \
--resource-group cloudinitexample \
--name cloudinitexample \
--location westus \
--os-type Linux \
--nic-name cloudinitnicexample \
--vnet-name cloudinitvnetexample \
--vnet-address-prefix 10.0.0.0/22 \
--vnet-subnet-name cloudinitvsubnet \
--vnet-subnet-address-prefix 10.0.0.0/24 \
--image-urn canonical:ubuntuserver:14.04.2-LTS:latest \
--ssh-publickey-file ~/.ssh/azure_id_rsa.pub \
--admin-username ahmet \
--custom-data cloud-init.txt

```

### Creazione di uno script cloud-init per impostare il nome host di una VM Linux

Una delle impostazioni più semplici e più importanti per qualsiasi VM Linux è il nome host. È possibile definire facilmente tale impostazione tramite cloud-init con questo script.

#### Script cloud-init di esempio denominato `cloud_config_hostname.txt`.

``` bash
#cloud-config
hostname: exampleServerName
```

Durante l'avvio iniziale della VM questo script cloud-init imposta il nome host su `exampleServerName`.

```bash
azure vm create \
--resource-group cloudinitexample \
--name cloudinitexample \
--location westus \
--os-type Linux \
--nic-name cloudinitnicexample \
--vnet-name cloudinitvnetexample \
--vnet-address-prefix 10.0.0.0/22 \
--vnet-subnet-name cloudinitvsubnet \
--vnet-subnet-address-prefix 10.0.0.0/24 \
--image-urn canonical:ubuntuserver:14.04.2-LTS:latest \
--ssh-publickey-file ~/.ssh/azure_id_rsa.pub \
--admin-username ahmet \
--custom-data cloud_config_hostname.txt

```

Accedere e verificare il nome host della nuova VM.

```bash
ssh exampleVM
hostname
exampleServerName
```

### Creazione di uno script cloud-init per aggiornare Linux

Per motivi di sicurezza, è consigliabile aggiornare la VM Ubuntu al primo avvio. Con cloud-init è possibile eseguire questa operazione tramite lo script seguente, a seconda della distribuzione Linux in uso.

#### Script cloud-init `cloud_config_apt_upgrade.txt` di esempio per la famiglia Debian

```bash
#cloud-config
apt_upgrade: true
```

Una volta avviato Linux, tutti i pacchetti installati vengono aggiornati tramite `apt-get`.

```bash
azure vm create \
--resource-group cloudinitexample \
--name cloudinitexample \
--location westus \
--os-type Linux \
--nic-name cloudinitnicexample \
--vnet-name cloudinitvnetexample \
--vnet-address-prefix 10.0.0.0/22 \
--vnet-subnet-name cloudinitvsubnet \
--vnet-subnet-address-prefix 10.0.0.0/24 \
--image-urn canonical:ubuntuserver:14.04.2-LTS:latest \
--ssh-publickey-file ~/.ssh/azure_id_rsa.pub \
--admin-username ahmet \
--custom-data cloud_config_apt_upgrade.txt
```

Accedere e verificare che tutti i pacchetti siano aggiornati.

```bash
ssh exampleVM
sudo apt-get upgrade
Reading package lists... Done
Building dependency tree
Reading state information... Done
Calculating upgrade... Done
The following packages have been kept back:
  linux-generic linux-headers-generic linux-image-generic
0 upgraded, 0 newly installed, 0 to remove and 0 not upgraded.
```

### Creazione di uno script cloud-init per aggiungere un utente a Linux

Una delle prime attività eseguite in qualsiasi nuova VM Linux è l'aggiunta di un utente distinto da `root`, per evitare di usare quest'ultimo. Le chiavi SSH rappresentano la procedura consigliata per la protezione e l'usabilità e vengono aggiunte al file `~/.ssh/authorized_keys` con questo script cloud-init.

#### Script cloud-init `cloud_config_add_users.txt` di esempio per la famiglia Debian

```bash
#cloud-config
users:
  - name: exampleUser
    groups: sudo
    shell: /bin/bash
    sudo: ['ALL=(ALL) NOPASSWD:ALL']
    ssh-authorized-keys:
      - ssh-rsa AAAAB3<snip>==exampleuser@slackwarelaptop
```

Una volta avviato Linux, tutti gli utenti elencati vengono creati e aggiunti al gruppo sudo.

```bash
azure vm create \
--resource-group cloudinitexample \
--name cloudinitexample \
--location westus \
--os-type Linux \
--nic-name cloudinitnicexample \
--vnet-name cloudinitvnetexample \
--vnet-address-prefix 10.0.0.0/22 \
--vnet-subnet-name cloudinitvsubnet \
--vnet-subnet-address-prefix 10.0.0.0/24 \
--image-urn canonical:ubuntuserver:14.04.2-LTS:latest \
--ssh-publickey-file ~/.ssh/azure_id_rsa.pub \
--admin-username ahmet \
--custom-data cloud_config_add_users.txt
```

Accedere e verificare l'utente appena creato.

```bash
cat /etc/group
```

Output

```bash
root:x:0:
<snip />
sudo:x:27:exampleUser
<snip />
exampleUser:x:1000:
```

## Passaggi successivi

Cloud-init si sta affermando come metodo standard per modificare la VM Linux in fase di avvio. Azure offre inoltre estensioni VM che consentono di modificare la VM Linux in fase di avvio o durante l'esecuzione. Ad esempio, è possibile usare VMAccessExtension di Azure per reimpostare le informazioni SSH o dell'utente mentre la VM è in esecuzione. Con cloud-init, per reimpostare la password è necessario riavviare il computer.

[Informazioni sulle estensioni e sulle funzionalità delle macchine virtuali](virtual-machines-linux-extensions-features.md)

[Gestire utenti, SSH e dischi di controllo o di ripristino in VM Linux di Azure tramite l'estensione VMAccess](virtual-machines-linux-using-vmaccess-extension.md)

<!---HONumber=AcomDC_0831_2016-->