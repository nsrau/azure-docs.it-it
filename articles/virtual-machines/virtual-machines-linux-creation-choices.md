<properties
	pageTitle="Diversi modi per creare una macchina virtuale Linux | Microsoft Azure"
	description="Elenca i diversi modi per creare una macchina virtuale Linux in Azure e indica collegamenti a strumenti ed esercitazioni per ogni metodo."
	services="virtual-machines-linux"
	documentationCenter=""
	authors="iainfoulds"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-linux"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="vm-linux"
	ms.workload="infrastructure-services"
	ms.date="07/06/2016"
	ms.author="iainfou"/>

# Diversi modi per creare una macchina virtuale Linux con Resource Manager

In Azure sono disponibili diversi modi per creare una macchina virtuale con il modello di distribuzione Resource Manager, adatti a obiettivi e utenti diversi. Questo articolo riepiloga le differenze e le opzioni disponibili per la creazione di macchine virtuali (VM) Linux.

## Interfaccia della riga di comando di Azure 

L'interfaccia della riga di comando di Azure è disponibile per più piattaforme tramite un pacchetto npm, pacchetti forniti tramite distribuzione o un contenitore Docker. Altre informazioni su come [Installare l'interfaccia della riga di comando di Azure](../xplat-cli-install.md). Le esercitazioni seguenti offrono esempi relativi all'uso dell'interfaccia della riga di comando di Azure. Vedere ogni articolo per altre informazioni sui comandi di avvio rapido dell'interfaccia della riga di comando illustrati:

* [Creare una VM Linux dall'interfaccia della riga di comando di Azure per sviluppo e test](virtual-machines-linux-quick-create-cli.md)

	```bash
	azure vm quick-create -M ~/.ssh/azure_id_rsa.pub -Q CoreOS
	```

* [Creare una VM Linux protetta usando un modello di Azure](virtual-machines-linux-create-ssh-secured-vm-from-template.md)

	```bash
	azure group create --name TestRG --location WestUS 
		--template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json
	```

* [Creare una VM Linux da zero con l'interfaccia della riga di comando di Azure](virtual-machines-linux-create-cli-complete.md)

* [Aggiungere un disco a una VM Linux](virtual-machines-linux-add-disk.md)

	```bash
	azure vm disk attach-new --resource-group TestRG --vm-name TestVM <size-in-GB>
	```

## Portale di Azure

L'interfaccia utente grafica del [portale di Azure](https://portal.azure.com) è un modo semplice per provare a usare una macchina virtuale, soprattutto se si ha poca esperienza con Azure, perché non richiede installazioni nel sistema. Usare il portale di Azure per creare la VM:

* [Creare una macchina virtuale Linux tramite il portale di Azure](virtual-machines-linux-quick-create-portal.md)
* [Come collegare un disco dati a una macchina virtuale Linux nel portale di Azure](virtual-machines-linux-attach-disk-portal.md)

## Sistema operativo e opzioni dell'immagine
Quando si crea una macchina virtuale, scegliere un'immagine in base al sistema operativo da eseguire. Azure e i relativi partner mettono a disposizione diverse immagini, alcune delle quali includono applicazioni e strumenti preinstallati. In alternativa, è possibile caricare una delle immagini personalizzate (vedere di seguito).

### Immagini di Azure
È possibile usare i comandi dell'interfaccia della riga di comando `azure vm image` per vedere ciò che è disponibile per editore, versione di distribuzione e compilazioni.

Elenca gli editori disponibili:

```bash
azure vm image list-publishers --location WestUS
```

Elenca i prodotti disponibili (offerte) per un determinato editore:

```bash
azure vm image list-offers --location WestUS --publisher Canonical
```

Elenca gli SKU disponibili (versioni di distribuzione) di una determinata offerta:

```bash
azure vm image list-skus --location WestUS --publisher Canonical --offer UbuntuServer
```

Elenca tutte le immagini disponibili per una determinata versione:

```bash
azure vm image list --location WestUS --publisher Canonical --offer UbuntuServer --sku 16.04.0-LTS
```

Per esempi sull'esplorazione e l'uso delle immagini disponibili, vedere [Individuare e selezionare immagini di macchine virtuali Linux in Azure con l'interfaccia della riga di comando o PowerShell](virtual-machines-linux-cli-ps-findimage.md).

I comandi `azure vm quick-create` e `azure vm create` hanno anche alcuni alias è possibile usare per accedere rapidamente alle distribuzioni più comuni e alle versioni più recenti. Questo approccio è più facile che dover specificare autore, offerta, SKU e versione ogni volta che si crea una macchina virtuale:

| Alias | Autore | Offerta | SKU | Versione |
|:----------|:----------|:-------------|:------------|:--------|
| CentOS | OpenLogic | Centos | 7,2 | più recenti |
| CoreOS | CoreOS | CoreOS | Stabile | più recenti |
| Debian | credativ | Debian | 8 | più recenti |
| openSUSE | SUSE | openSUSE | 13\.2 | più recenti |
| RHEL | Redhat | RHEL | 7,2 | più recenti |
| SLES | SLES | SLES | 12-SP1 | più recenti |
| UbuntuLTS | Canonical | UbuntuServer | 14\.04.4-LTS | più recenti |

### Usare la propria immagine

Per personalizzazioni specifiche è possibile usare un'immagine basata su una macchina virtuale di Azure esistente *acquisendo* tale macchina virtuale oppure caricare un'immagine personalizzata e archiviata in un disco rigido virtuale. Per altre informazioni sulle distribuzioni supportate e su come usare immagini personalizzate, vedere gli articoli seguenti:

* [Distribuzioni approvate per Azure](virtual-machines-linux-endorsed-distros.md)

* [Informazioni per distribuzioni non approvate](virtual-machines-linux-create-upload-generic.md)

* [Come acquisire una macchina virtuale Linux da usare come modello di Resource Manager](virtual-machines-linux-capture-image.md). Comandi di avvio rapido:

	```bash
	azure vm deallocate --resource-group TestRG --vm-name TestVM
	azure vm generalize --resource-group TestRG --vm-name TestVM
	azure vm capture --resource-group TestRG --vm-name TestVM --vhd-name-prefix CapturedVM
	```

## Passaggi successivi

* Provare a eseguire una delle esercitazioni per creare una VM Linux dal [portale](virtual-machines-linux-quick-create-portal.md), con l'[interfaccia della riga di comando](virtual-machines-linux-quick-create-cli.md) o usando un [modello](virtual-machines-linux-cli-deploy-templates.md) di Azure Resource Manager.

* Dopo aver creato una VM Linux è possibile [aggiungere un disco dati](virtual-machines-linux-add-disk.md) con facilità.

* Azioni rapide per [reimpostare una password o chiavi SSH e gestire gli utenti](virtual-machines-linux-using-vmaccess-extension.md)

<!---HONumber=AcomDC_0706_2016-->