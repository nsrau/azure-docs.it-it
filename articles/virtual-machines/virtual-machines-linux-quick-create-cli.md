<properties
   pageTitle="Creare rapidamente una VM Linux in Azure tramite l'interfaccia della riga di comando | Microsoft Azure"
   description="Creare una VM Linux in Azure tramite l'interfaccia della riga di comando."
   services="virtual-machines-linux"
   documentationCenter=""
   authors="vlivech"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="NA"
   ms.topic="hero-article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure"
   ms.date="05/03/2016"
   ms.author="v-livech"/>


# Creare una VM Linux in Azure tramite l'interfaccia della riga di comando

Questo articolo illustra come distribuire rapidamente una macchina virtuale Linux in Azure usando il comando `azure vm quick-create` dell'interfaccia della riga di comando di Azure. Il comando `quick-create` distribuisce una macchina virtuale con un'infrastruttura di base che è possibile usare per creare un prototipo o testare un concetto molto rapidamente. Può essere considerato il modo più rapido per raggiungere una shell bash. L'articolo richiede un account Azure ([fare clic qui per una versione di valutazione gratuita](https://azure.microsoft.com/pricing/free-trial/)) e l'[interfaccia della riga di comando di Azure](../xplat-cli-install.md) connessa (`azure login`) e in modalità Resource Manager (`azure config mode arm`). È anche possibile distribuire rapidamente una VM Linux usando il [portale di Azure](virtual-machines-linux-quick-create-portal.md).

## Breve riepilogo del comando

Un solo comando per distribuire una VM CoreOS e collegare la chiave SSH

```bash
azure vm quick-create -M ~/.ssh/azure_id_rsa.pub -Q CoreOS
```

## Distribuire la VM Linux

Usando lo stesso comando riportato sopra, di seguito viene mostrato ogni prompt insieme all'output previsto, ma viene usato l'alias RHEL per creare una VM RedHat Enteprise Linux 7.2.

## Usare un alias ImageURN

Il comando `quick-create` dell'interfaccia della riga di comando di Azure ha alias con mapping alle distribuzioni di sistema operativo più comuni. La tabella seguente elenca gli alias di distribuzione, a partire dall'interfaccia della riga di comando di Azure versione 0.10. Tutte le distribuzioni che usano `quick-create` includono per impostazione predefinita VM basate su risorse di archiviazione SSD, in modo da offrire prestazioni elevate.

| Alias | Autore | Offerta | SKU | Versione |
|:----------|:----------|:-------------|:------------|:--------|
| CentOS | OpenLogic | Centos | 7,2 | più recenti |
| CoreOS | CoreOS | CoreOS | Stabile | più recenti |
| Debian | credativ | Debian | 8 | più recenti |
| openSUSE | SUSE | openSUSE | 13\.2 | più recenti |
| RHEL | Redhat | RHEL | 7,2 | più recenti |
| SLES | SLES | SLES | 12-SP1 | più recenti |
| UbuntuLTS | Canonical | UbuntuServer | 14\.04.4-LTS | più recenti |



Per l'opzione **ImageURN** (`-Q`) verrà usato `RHEL` per distribuire una VM RedHat Enterprise Linux 7.2. Questi 7 alias rappresentano una piccola parte dei sistemi operativi disponibili in Azure. Per trovare altre immagini nel Marketplace, [cercare un'immagine](virtual-machines-linux-cli-ps-findimage.md) o [caricare un'immagine personalizzata](virtual-machines-linux-create-upload-generic.md).

Nell'analisi dettagliata del comando seguente sostituire i prompt con i valori dell'ambiente in uso, perché qui vengono usati valori di esempio.

Seguire le istruzioni e immettere i nomi personalizzati

```bash
azure vm quick-create -M ~/.ssh/id_rsa.pub -Q RHEL
```

L'output dovrebbe essere simile al blocco di output seguente.

```bash
info:    Executing command vm quick-create
Resource group name: rhel-quick
Virtual machine name: rhel
Location name: westus
Operating system Type [Windows, Linux]: linux
User name: ops
+ Listing virtual machine sizes available in the location "westus"
+ Looking up the VM "rhel"
info:    Verifying the public key SSH file: /Users/ops/.ssh/id_rsa.pub
info:    Using the VM Size "Standard_DS1"
info:    The [OS, Data] Disk or image configuration requires storage account
+ Looking up the storage account cli1630678171193501687
info:    Could not find the storage account "cli1630678171193501687", trying to create new one
+ Creating storage account "cli1630678171193501687" in "westus"
+ Looking up the storage account cli1630678171193501687
+ Looking up the NIC "rhel-westu-1630678171-nic"
info:    An nic with given name "rhel-westu-1630678171-nic" not found, creating a new one
+ Looking up the virtual network "rhel-westu-1630678171-vnet"
info:    Preparing to create new virtual network and subnet
+ Creating a new virtual network "rhel-westu-1630678171-vnet" [address prefix: "10.0.0.0/16"] with subnet "rhel-westu-1630678171-snet" [address prefix: "10.0.1.0/24"]
+ Looking up the virtual network "rhel-westu-1630678171-vnet"
+ Looking up the subnet "rhel-westu-1630678171-snet" under the virtual network "rhel-westu-1630678171-vnet"
info:    Found public ip parameters, trying to setup PublicIP profile
+ Looking up the public ip "rhel-westu-1630678171-pip"
info:    PublicIP with given name "rhel-westu-1630678171-pip" not found, creating a new one
+ Creating public ip "rhel-westu-1630678171-pip"
+ Looking up the public ip "rhel-westu-1630678171-pip"
+ Creating NIC "rhel-westu-1630678171-nic"
+ Looking up the NIC "rhel-westu-1630678171-nic"
+ Looking up the storage account clisto909893658rhel
+ Creating VM "rhel"
+ Looking up the VM "rhel"
+ Looking up the NIC "rhel-westu-1630678171-nic"
+ Looking up the public ip "rhel-westu-1630678171-pip"
data:    Id                              :/subscriptions/<guid>/resourceGroups/rhel-quick/providers/Microsoft.Compute/virtualMachines/rhel
data:    ProvisioningState               :Succeeded
data:    Name                            :rhel
data:    Location                        :westus
data:    Type                            :Microsoft.Compute/virtualMachines
data:
data:    Hardware Profile:
data:      Size                          :Standard_DS1
data:
data:    Storage Profile:
data:      Image reference:
data:        Publisher                   :RedHat
data:        Offer                       :RHEL
data:        Sku                         :7.2
data:        Version                     :latest
data:
data:      OS Disk:
data:        OSType                      :Linux
data:        Name                        :clic5abbc145c0242c1-os-1462425492101
data:        Caching                     :ReadWrite
data:        CreateOption                :FromImage
data:        Vhd:
data:          Uri                       :https://cli1630678171193501687.blob.core.windows.net/vhds/clic5abbc145c0242c1-os-1462425492101.vhd
data:
data:    OS Profile:
data:      Computer Name                 :rhel
data:      User Name                     :ops
data:      Linux Configuration:
data:        Disable Password Auth       :true
data:
data:    Network Profile:
data:      Network Interfaces:
data:        Network Interface #1:
data:          Primary                   :true
data:          MAC Address               :00-0D-3A-32-0F-DD
data:          Provisioning State        :Succeeded
data:          Name                      :rhel-westu-1630678171-nic
data:          Location                  :westus
data:            Public IP address       :104.42.236.196
data:            FQDN                    :rhel-westu-1630678171-pip.westus.cloudapp.azure.com
data:
data:    Diagnostics Profile:
data:      BootDiagnostics Enabled       :true
data:      BootDiagnostics StorageUri    :https://clisto909893658rhel.blob.core.windows.net/
data:
data:      Diagnostics Instance View:
info:    vm quick-create command OK
```

Ora è possibile connettersi alla VM con SSH usando la porta SSH predefinita 22 e il nome di dominio completo (FQDN) indicato nell'output precedente. È anche possibile usare l'indirizzo IP elencato.

```bash
ssh ops@rhel-westu-1630678171-pip.westus.cloudapp.azure.com
```
Il processo di accesso dovrebbe essere simile a quanto segue:

```bash
The authenticity of host 'rhel-westu-1630678171-pip.westus.cloudapp.azure.com (104.42.236.196)' can't be established.
RSA key fingerprint is 0e:81:c4:36:2d:eb:3c:5a:dc:7e:65:8a:3f:3e:b0:cb.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added 'rhel-westu-1630678171-pip.westus.cloudapp.azure.com,104.42.236.196' (RSA) to the list of known hosts.
[ops@rhel ~]$ ls -a
.  ..  .bash_logout  .bash_profile  .bashrc  .cache  .config  .ssh
```

## Passaggi successivi

Il comando `azure vm quick-create` consente di distribuire rapidamente una VM per poter accedere a una shell bash e iniziare a lavorare. L'uso di `vm quick-create` non offre i vantaggi aggiuntivi di un ambiente complesso. Per distribuire una VM Linux personalizzata per l'infrastruttura, è possibile vedere gli articoli seguenti.

- [Usare un modello di Azure Resource Manager per creare una distribuzione specifica](virtual-machines-linux-cli-deploy-templates.md)
- [Creare un ambiente personalizzato per una VM Linux usando direttamente i comandi dell'interfaccia della riga di comando di Azure](virtual-machines-linux-create-cli-complete.md).
- [Creare una VM Linux protetta con SSH in Azure usando i modelli](virtual-machines-linux-create-ssh-secured-vm-from-template.md)

Questi articoli illustrano come iniziare a compilare un'infrastruttura di Azure oltre a un numero qualsiasi di strumenti di orchestrazione, configurazione e distribuzione delle infrastrutture proprietarie e open source.

<!---HONumber=AcomDC_0706_2016-->