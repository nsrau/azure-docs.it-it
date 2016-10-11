<properties
	pageTitle="Diversi modi per creare una macchina virtuale Linux | Microsoft Azure"
	description="Informazioni sui diversi modi per creare una macchina virtuale Linux in Azure, con collegamenti a strumenti ed esercitazioni per ogni metodo."
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
	ms.date="09/27/2016"
	ms.author="iainfou"/>

# Diversi modi per creare una macchina virtuale Linux in Azure

Azure offre la possibilità di creare una macchina virtuale (VM) Linux usando gli strumenti e i flussi di lavoro con cui si ha familiarità. Questo articolo offre un riepilogo delle differenze e degli esempi per la creazione di VM Linux.


## Interfaccia della riga di comando di Azure 

L'interfaccia della riga di comando di Azure è disponibile per più piattaforme tramite un pacchetto npm, pacchetti forniti tramite distribuzione o un contenitore Docker. Altre informazioni su come [Installare l'interfaccia della riga di comando di Azure](../xplat-cli-install.md). Le esercitazioni seguenti offrono esempi relativi all'uso dell'interfaccia della riga di comando di Azure. Vedere ogni articolo per altre informazioni sui comandi di avvio rapido dell'interfaccia della riga di comando illustrati:

- [Creare una VM Linux dall'interfaccia della riga di comando di Azure per sviluppo e test](virtual-machines-linux-quick-create-cli.md)
	- L'esempio seguente crea una VM CoreOS usando una chiave pubblica denominata `azure_id_rsa.pub`:

	```bash
	azure vm quick-create -ssh-publickey-file ~/.ssh/azure_id_rsa.pub \
		--image-urn CoreOS
	```

- [Creare una VM Linux protetta usando un modello di Azure](virtual-machines-linux-create-ssh-secured-vm-from-template.md)
	- L'esempio seguente crea una VM usando un modello archiviato in GitHub:

	```bash
	azure group create --name TestRG --location WestUS 
		--template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json
	```

- [Creare un ambiente Linux completo tramite l'interfaccia della riga di comando di Azure](virtual-machines-linux-create-cli-complete.md)
	- Include la creazione di un servizio di bilanciamento del carico e di più VM in un set di disponibilità.

- [Aggiungere un disco a una VM Linux](virtual-machines-linux-add-disk.md)
	- L'esempio seguente aggiunge un disco da 5 GB a una VM esistente denominata `TestVM`:

	```bash
	azure vm disk attach-new --resource-group TestRG --vm-name TestVM \
		--size-in-GB 5
	```

## Portale di Azure

Il [portale di Azure](https://portal.azure.com) consente di creare rapidamente una VM perché non richiede installazioni nel sistema. Usare il portale di Azure per creare la VM:

- [Creare una macchina virtuale Linux tramite il portale di Azure](virtual-machines-linux-quick-create-portal.md)
- [Come collegare un disco dati a una macchina virtuale Linux nel portale di Azure](virtual-machines-linux-attach-disk-portal.md)


## Sistema operativo e opzioni dell'immagine
Quando si crea una macchina virtuale, scegliere un'immagine in base al sistema operativo da eseguire. Azure e i relativi partner mettono a disposizione diverse immagini, alcune delle quali includono applicazioni e strumenti preinstallati. In alternativa, caricare una delle immagini personalizzate (vedere la [sezione seguente](#use-your-own-image)).

### Immagini di Azure
Usare i comandi dell'interfaccia della riga di comando `azure vm image` per visualizzare ciò che è disponibile per editore, versione di distribuzione e build.

Elencare gli editori disponibili come segue:

```bash
azure vm image list-publishers --location WestUS
```

Elencare i prodotti disponibili (offerte) per un determinato editore come segue:

```bash
azure vm image list-offers --location WestUS --publisher Canonical
```

Elencare gli SKU disponibili (versioni di distribuzione) di una determinata offerta come segue:

```bash
azure vm image list-skus --location WestUS --publisher Canonical --offer UbuntuServer
```

Elencare tutte le immagini disponibili per una determinata versione come segue:

```bash
azure vm image list --location WestUS --publisher Canonical --offer UbuntuServer --sku 16.04.0-LTS
```

Per altri esempi sull'esplorazione e l'uso delle immagini disponibili, vedere [Individuare e selezionare immagini di macchine virtuali di Azure con l'interfaccia della riga di comando di Azure](virtual-machines-linux-cli-ps-findimage.md).

I comandi `azure vm quick-create` e `azure vm create` hanno alias che è possibile usare per accedere rapidamente alle distribuzioni più comuni e alle versioni più recenti. Usare alias è in genere più veloce che specificare editore, offerta, SKU e versione ogni volta che si crea una VM:

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

Per personalizzazioni specifiche è possibile usare un'immagine basata su una VM di Azure esistente *acquisendo* tale VM. È anche possibile caricare un'immagine creata in locale. Per altre informazioni sulle distribuzioni supportate e su come usare immagini personalizzate, vedere gli articoli seguenti:

- [Distribuzioni approvate per Azure](virtual-machines-linux-endorsed-distros.md)

- [Informazioni per distribuzioni non approvate](virtual-machines-linux-create-upload-generic.md)

- [Come acquisire una macchina virtuale Linux come modello di Resource Manager](virtual-machines-linux-capture-image.md).
	- Comandi di avvio rapido di esempio per l'acquisizione di una VM esistente:

	```bash
	azure vm deallocate --resource-group TestRG --vm-name TestVM
	azure vm generalize --resource-group TestRG --vm-name TestVM
	azure vm capture --resource-group TestRG --vm-name TestVM --vhd-name-prefix CapturedVM
	```

## Passaggi successivi

- Creare una VM Linux dal [portale](virtual-machines-linux-quick-create-portal.md), con l'[interfaccia della riga di comando](virtual-machines-linux-quick-create-cli.md) oppure usando un [modello di Azure Resource Manager](virtual-machines-linux-cli-deploy-templates.md).

- Dopo aver creato una VM Linux, [aggiungere un disco dati](virtual-machines-linux-add-disk.md).

- Azioni rapide per [reimpostare una password o chiavi SSH e gestire gli utenti](virtual-machines-linux-using-vmaccess-extension.md)

<!---HONumber=AcomDC_1005_2016-->