<properties
	pageTitle="Comandi di CLI di Azure equivalenti per le attività di macchina virtuale | Microsoft Azure"
	description="Informazioni sui comandi equivalenti di CLI di Azure per creare e gestire macchine virtuali di Azure in modalità Gestione dei servizi e Gestione risorse di Azure."
	services="virtual-machines"
	documentationCenter=""
	authors="dlepow"
	manager="timlt"
	editor=""
	tags="azure-resource-manager,azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="command-line-interface"
	ms.workload="infrastructure-services"
	ms.date="08/28/2015"
	ms.author="danlep"/>


# Comandi equivalenti di Gestione risorse e Gestione dei servizi per le attività di macchina virtuale con CLI di Azure per Mac, Linux e Windows
Questo articolo illustra i comandi equivalenti dell'interfaccia della riga di comando di Microsoft Azure (CLI di Azure) per creare e gestire le macchine virtuali in Gestione dei servizi e Gestione risorse di Azure. Usare questo documento come guida utile per eseguire la migrazione degli script da una modalità di comando all'altra.

* Se l'interfaccia della riga di comando di Azure non è ancora stata installata e connessa alla sottoscrizione, vedere [Installare l'interfaccia della riga di comando di Azure](../xplat-cli-install.md) e [Connettersi a una sottoscrizione Azure dall'interfaccia della riga di comando di Azure](../xplat-cli-connect.md). Quando si desidera usare i comandi della modalità Gestione risorse, assicurarsi di connettersi con il metodo di accesso.

* Per iniziare a usare la modalità Gestione risorse in CLI di Azure e passare da una modalità all'altra, vedere l'argomento relativo all'[uso dell'interfaccia della riga di comando di Azure con Gestione risorse](xplat-cli-azure-resource-manager.md). Per impostazione predefinita, l'interfaccia della riga di comando viene avviata in modalità Gestione dei servizi. Per passare alla modalità Gestione risorse, eseguire `azure config mode arm`. Per tornare alla modalità Gestione dei servizi, eseguire `azure config mode asm`.

* Per la Guida in linea di comando e le opzioni, digitare `azure <command> <subcommand> --help` o `azure help <command> <subcommand>`.

## Attività della macchina virtuale
La tabella seguente confronta attività di macchina virtuale comuni che è possibile eseguire con i comandi CLI di Azure in Gestione risorse e Gestione dei servizi. Con molti comandi di Gestione risorse è necessario passare il nome di un gruppo di risorse esistente.

> [AZURE.NOTE]Questi esempi non includono operazioni basate su modello in Gestione risorse. Per informazioni, vedere l'argomento relativo all'[uso dell'interfaccia della riga di comando di Azure con Gestione risorse](xplat-cli-azure-resource-manager.md).

Attività | Gestione dei servizi | Gestione risorse
-------------- | ----------- | -------------------------
Creare la macchina virtuale di base | `azure vm create [options] <dns-name> <image> [userName] [password]` | `azure vm quick-create [options] <resource-group> <name> <location> <os-type> <image-urn> <admin-username> <admin-password>`<br/><br/>(Ottenere `image-urn` dal comando `azure vm image list`.)
Creare una macchina virtuale Linux | `azure vm create [options] <dns-name> <image> [userName] [password]` | `azure  vm create [options] <resource-group> <name> <location> -y "Linux"`
Creare un'app Windows | `azure vm create [options] <dns-name> <image> [userName] [password]` | `azure  vm create [options] <resource-group> <name> <location> -y "Windows"`
Elenco delle macchine virtuali | `azure  vm list [options]` | `azure  vm list [options] <resource_group>`
Visualizzare informazioni su una macchina virtuale | `azure  vm show [options] <vm_name>` | `azure  vm show [options] <resource_group> <name>`
Avviare una macchina virtuale | `azure vm start [options] <name>` | `azure vm start [options] <resource_group> <name>`
Arrestare una macchina virtuale | `azure vm shutdown [options] <name>` | `azure vm stop [options] <resource_group> <name>`
Deallocare una macchina virtuale | Non disponibile | `azure vm deallocate [options] <resource-group> <name>`
Riavviare una macchina virtuale | `azure vm restart [options] <vname>` | `azure vm restart [options] <resource_group> <name>`
Eliminare una macchina virtuale | `azure vm delete [options] <name>` | `azure vm delete [options] <resource_group> <name>`
Acquisire una macchina virtuale | `azure vm capture [options] <name>` | `azure vm capture [options] <resource_group> <name>`
Creare una macchina virtuale da un'immagine dell'utente | `azure  vm create [options] <dns-name> <image> [userName] [password]` | `azure  vm create [options] –q <image-name> <resource-group> <name> <location> <os-type>`
Creare una macchina virtuale da un disco specializzato | `azure  vm create [options]-d <custom-data-file> <dns-name> [userName] [password]` | `azue  vm create [options] –d <os-disk-vhd> <resource-group> <name> <location> <os-type>`
Aggiungere un disco dati a una macchina virtuale | `azure  vm disk attach [options] <vm-name> <disk-image-name>` -O- <br/> `vm disk attach-new [options] <vm-name> <size-in-gb> [blob-url]` | `azure  vm disk attach-new [options] <resource-group> <vm-name> <size-in-gb> [vhd-name]`
Rimuovere un disco dati da una macchina virtuale | `azure  vm disk detach [options] <vm-name> <lun>` | `azure  vm disk detach [options] <resource-group> <vm-name> <lun>`
Aggiungere un'estensione generica a una macchina virtuale | `azure  vm extension set [options] <vm-name> <extension-name> <publisher-name> <version>` | `azure  vm extension set [options] <resource-group> <vm-name> <name> <publisher-name> <version>`
Aggiungere l'estensione accesso alle macchine Virtuali a una macchina virtuale | Non disponibile | `azure vm reset-access [options] <resource-group> <name>`
Aggiungere Docker estensione a una macchina virtuale | `azure  vm docker create [options] <dns-name> <image> <user-name> [password]` | `azure  vm docker create [options] <resource-group> <name> <location> <os-type>`
Aggiungere l'estensione Chef a una macchina virtuale | `azure  vm extension get-chef [options] <vm-name>` | Non disponibile
Disabilitare un'estensione di macchina virtuale | `azure  vm extension set [options] –b <vm-name> <extension-name> <publisher-name> <version>` | Non disponibile
Rimuovere un'estensione di macchina virtuale | `azure  vm extension set [options] –u <vm-name> <extension-name> <publisher-name> <version>` | `azure  vm extension set [options] –u <resource-group> <vm-name> <name> <publisher-name> <version>`
Elencare le estensioni di macchina virtuale | `azure vm extension list [options]` | `azure  vm extension get [options] <resource-group> <vm-name>`
Elenco immagini di macchina virtuale | `azure vm image list [options]` | `azure vm image list [options] <location> <publisher> [offer] [sku]` -OR- <br/> `azure vm image list-publishers [options] <location>` -OR- <br/> `azure vm image list-offers [options] <location>` -OR- <br/> `azure vm image list-skus [options] <location>`
Visualizzare un'immagine di macchina virtuale | `azure vm image show [options]` | Non disponibile
Ottenere l'utilizzo delle risorse della macchina virtuale | Non disponibile | `azure vm list-usage [options] <location>`
Ottenere tutte le dimensioni delle macchine virtuali disponibili | Non disponibile | `azure vm sizes [options]`


## Passaggi successivi

* Per altre informazioni sull'uso di CLI di Azure per lavorare con le risorse di Gestione risorse, vedere l'argomento relativo all'[uso dell'interfaccia della riga di comando di Azure con Gestione risorse](xplat-cli-azure-resource-manager.md) e [Gestione del controllo di accesso basato sui ruoli con l'interfaccia della riga di comando di Azure (Azure CLI)](../role-based-access-control-xplat-cli.md).
* Per altri esempi di comandi CLI, vedere l'argomento relativo all'[uso dell'interfaccia della riga di comando di Azure con Gestione dei servizi di Azure](../virtual-machines-command-line-tools.md) e l'argomento relativo all'[uso della riga di comando di Azure con Gestione risorse di Azure](azure-cli-arm-commands.md).

<!---HONumber=September15_HO1-->