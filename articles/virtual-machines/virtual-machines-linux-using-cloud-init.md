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
    ms.date="04/29/2016"
    ms.author="v-livech"
/>

# Uso di cloud-init per personalizzare una VM Linux durante la creazione

Questo articolo illustra come creare script cloud-init per impostare il nome host, aggiornare i pacchetti installati e gestire gli account utente. Gli script cloud-init verranno quindi usati durante la creazione della VM dall'[interfaccia della riga di comando di Azure](../xplat-cli-install.md).

## Prerequisiti

Prerequisiti: [un account Azure](https://azure.microsoft.com/pricing/free-trial/), [chiavi pubbliche e private SSH](virtual-machines-linux-mac-create-ssh-keys.md), un gruppo di risorse di Azure in cui avviare la VM Linux e l'interfaccia della riga di comando di Azure installata e impostata in modalità ARM tramite `azure config mode arm`.

## Introduzione

Quando si avvia una nuova VM Linux si ottiene un VM Linux standard senza alcuna personalizzazione né alcun adattamento. [Cloud-init](https://cloudinit.readthedocs.org) è un metodo standard che consente di inserire uno script o alcune impostazioni di configurazione in una VM Linux quando viene avviata per la prima volta.

In Azure è possibile apportare modifiche a una VM Linux durante l'avvio in tre modi diversi.

- Inserendo script con cloud-init.
- Inserendo script tramite l'estensione [Script personalizzato](virtual-machines-linux-extensions-customscript.md) di Azure.
- È possibile specificare impostazioni personalizzate in un modello di Azure e usare quest'ultimo per avviare e personalizzare la VM Linux, che include il supporto per cloud-init, nonché l'estensione VM Script personalizzato e molte altre.

Per inserire script in qualsiasi momento è possibile:

- usare SSH per eseguire comandi direttamente, usare l'estensione [Script personalizzato](virtual-machines-linux-extensions-customscript.md) di Azure come comando o in un modello di Azure, oppure usare strumenti comuni di gestione della configurazione quali Ansible, Salt, Chef e Puppet, che a loro volta funzionano con SSH dopo che la VM ha completato l'avvio.

NOTA: se un'estensione [Script personalizzato](virtual-machines-linux-extensions-customscript.md) esegue semplicemente uno script come radice nello stesso modo in cui lo esegue SSH, con l'estensione VM è possibile usare diverse funzionalità offerte da Azure che possono essere utili a seconda dello scenario.

## Comandi rapidi

Creare uno script cloud-init per nome host

```bash
#cloud-config
hostname: exampleServerName
```

Creare uno script cloud-init di aggiornamento di Linux al primo avvio per la famiglia Debian

```bash
#cloud-config
apt_upgrade: true
```

Creare uno script cloud-init di aggiunta di un utente

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

## Procedura dettagliata

### Aggiunta di uno script cloud-init per la creazione della VM con l'interfaccia della riga di comando di Azure

Per avviare uno script cloud-init durante la creazione di una VM in Azure, specificare il file cloud-init tramite l'opzione `--custom-data` dell'interfaccia della riga di comando di Azure.

NOTA: anche se questo articolo illustra l'uso dell'opzione `--custom-data` per i file cloud-init, questa opzione consente anche di passare codice o file arbitrari. Se la VM Linux sa già quali operazioni eseguire con questi file, tali operazioni vengono eseguite automaticamente.

```bash
azure vm create \
--resource-group exampleRG \
--name exampleVM \
--location westus \
--admin-username exampleAdminUserName \
--os-type Linux \
--nic-name exampleNIC \
--image-urn canonical:ubuntuserver:14.04.2-LTS:latest \
--custom-data cloud_init_script.txt
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
--resource-group exampleRG \
--name exampleVM \
--location westus \
--admin-username exampleAdminUserName \
--os-type Linux \
--nic-name exampleNIC \
--image-urn canonical:ubuntuserver:14.04.2-LTS:latest \
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

Dopo l'avvio della nuova VM Linux, vengono aggiornati immediatamente tutti i pacchetti installati tramite `apt-get`.

```bash
azure vm create \
--resource-group exampleRG \
--name exampleVM \
--location westus \
--admin-username exampleAdminUserName \
--os-type Linux \
--nic-name exampleNIC \
--image-urn canonical:ubuntuserver:14.04.2-LTS:latest \
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

Una delle prime attività eseguite in qualsiasi nuova VM Linux è l'aggiunta di un utente distinto da `root`, per evitare di usare quest'ultimo. Ciò è molto utile per motivi di sicurezza e favorisce l'usabilità, se si aggiunge la propria chiave pubblica SSH al file `~/.ssh/authorized_keys` di tale utente per consentire l'accesso SSH sicuro e senza password.

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

Dopo l'avvio della VM Linux il nuovo utente viene creato e aggiunto al gruppo sudo.

```bash
azure vm create \
--resource-group exampleRG \
--name exampleVM \
--location westus \
--admin-username exampleAdminUserName \
--os-type Linux \
--nic-name exampleNIC \
--image-urn canonical:ubuntuserver:14.04.2-LTS:latest \
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

<!---HONumber=AcomDC_0504_2016-->