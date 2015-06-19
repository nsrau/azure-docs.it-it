<properties
	pageTitle="Equivalente di gestione delle risorse e i comandi di Gestione servizio per le operazioni di macchina virtuale con CLI per Mac, Linux e Windows Azure"
	description="Vengono elencati i comandi di Azure CLI equivalenti per creare e gestire macchine virtuali di Azure in modalità di gestione del servizio e Gestione risorse"
	services="virtual-machines"
	documentationCenter=""
	authors="dlepow"
	manager="timlt"
	editor=""/>

<tags
	ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="command-line-interface"
	ms.workload="infrastructure-services"
	ms.date="04/28/2015"
	ms.author="danlep"/>


# Equivalente di gestione delle risorse e i comandi di Gestione servizio per le operazioni di macchina virtuale con CLI per Mac, Linux e Windows Azure
In questo articolo vengono elencati i comandi equivalenti interfaccia della riga di comando di Azure (Azure CLI) per creare e gestire macchine virtuali di Azure in modalità di gestione dei servizi (asm) in modalità di gestione risorse (arm). Utilizzare come guida utile per la migrazione degli script dalla modalità uno comando a altro.

* Se non già installato Azure CLI e connesso alla sottoscrizione, vedere [installare CLI Azure per Mac, Linux e Windows](../xplat-cli-install.md) e [connettersi a una sottoscrizione Azure da CLI per Mac, Linux e Windows Azure](../xplat-cli-connect.md). Assicurarsi di connettersi con il metodo di accesso quando si utilizzano i comandi di modalità arm.

* Per iniziare a utilizzare modalità arm in modalità di comando CLI di Azure e switch, vedere [utilizzando l'interfaccia della riga di comando di Azure con Gestione risorse](xplat-cli-azure-resource-manager.md).

* Per la Guida in linea di comando e le opzioni, digitare `azure <command> <subcommand> --help` o `azure help <command> <subcommand>`.

## Scenari
Di seguito sono operazioni comuni di macchina virtuale è possibile eseguire con i comandi di Azure CLI in modalità asm e arm. Con molti comandi in modalità arm, è necessario passare il nome di un gruppo di risorse esistente.

> [AZURE.NOTE]Questi esempi non includono operazioni basate su modello in modalità arm. Per informazioni, vedere [Utilizzo dell'interfaccia della riga di comando multipiattaforma di Azure con Gestione risorse](xplat-cli-azure-resource-manager.md).

Scenario | modalità asm | modalità ARM
-------------- | ----------- | -------------------------
Creare la macchina virtuale di base | `azure vm create [options] <dns-name> <image> [userName] [password]` | `azure vm quick-create [options] <resource-group> <name> <location> <os-type> <image-urn> <admin-username> <admin-password>`
Creare una macchina virtuale Linux | `azure vm create [options] <dns-name> <image> [userName] [password]` | `azure  vm create [options] <resource-group> <name> <location> -y "Linux"`
Creare un'app Windows | `azure vm create [options] <dns-name> <image> [userName] [password]` | `azure  vm create [options] <resource-group> <name> <location> -y "Windows"`
Elenco delle macchine virtuali | `azure  vm list [options]` | `azure  vm list [options] <resource_group>`
Visualizzare informazioni su una macchina virtuale | `azure  vm show [options] <vm_name>` | `azure  vm show [options] <resource_group> <name>`
Avviare una macchina virtuale | `azure vm start [options] <name>` | `azure vm start [options] <resource_group> <name>`
Arrestare una macchina virtuale | `azure vm shutdown [options] <name>` | `azure vm stop [options] <resource_group> <name>`
Avviare una macchina virtuale | `azure vm restart [options] <vname>` | `azure vm restart [options] <resource_group> <name>`
Eliminare una macchina virtuale | `azure vm delete [options] <name>` | `azure vm delete [options] <resource_group> <name>`
Acquisire una macchina virtuale | `azure vm capture [options] <name>` | `azure vm capture [options] <resource_group> <name>`
Creare una macchina virtuale da un'immagine dell'utente | `azure  vm create [options] <dns-name> <image> [userName] [password]` | `azure  vm create [options] –q <image-name> <resource-group> <name> <location> <os-type>`
Creare una macchina virtuale da un disco specializzato | `azure  vm create [options]-d <custom-data-file> <dns-name> [userName] [password]` | `azue  vm create [options] –d <os-disk-vhd> <resource-group> <name> <location> <os-type>`
Aggiungere un disco dati a una macchina virtuale | `azure  vm disk attach [options] <vm-name> <disk-image-name>` -O- <br/> `vm disk attach-new [options] <vm-name> <size-in-gb> [blob-url]` | `azure  vm disk attach-new [options] <resource-group> <vm-name> <size-in-gb> [vhd-name]`
Rimuovere un disco dati da una macchina virtuale | `azure  vm disk detach [options] <vm-name> <lun>` | `azure  vm disk detach [options] <resource-group> <vm-name> <lun>`
Aggiungere un'estensione generica a una macchina virtuale | `azure  vm extension set [options] <vm-name> <extension-name> <publisher-name> <version>` | `azure  vm extension set [options] <resource-group> <vm-name> <name> <publisher-name> <version>`
Aggiungere l'estensione accesso alle macchine Virtuali a una macchina virtuale | NON DISPONIBILE | `azure vm reset-access [options] <resource-group> <name>`
Aggiungere Docker estensione a una macchina virtuale | `azure  vm docker create [options] <dns-name> <image> <user-name> [password]` | `azure  vm docker create [options] <resource-group> <name> <location> <os-type>`
Aggiungere l'estensione Chef a una macchina virtuale | `azure  vm extension get-chef [options] <vm-name>` | NON DISPONIBILE
Disabilitare un'estensione di macchina virtuale | `azure  vm extension set [options] –b <vm-name> <extension-name> <publisher-name> <version>` | NON DISPONIBILE
Rimuovere un'estensione di macchina virtuale | `azure  vm extension set [options] –u <vm-name> <extension-name> <publisher-name> <version>` | `azure  vm extension set [options] –u <resource-group> <vm-name> <name> <publisher-name> <version>`
Elencare le estensioni di macchina virtuale | `azure vm extension list [options]` | `azure  vm extension get [options] <resource-group> <vm-name>`
Elenco immagini di macchina virtuale | `azure vm image list [options]` | `azure vm image list [options] <location> <publisher> [offer] [sku]` -OR- <br/> `azure vm image list-publishers [options] <location>` -OR- <br/> `azure vm image list-offers [options] <location>` -OR- <br/> `azure vm image list-skus [options] <location>`
Visualizzare un'immagine di macchina virtuale | `azure vm image show [options]` | NON DISPONIBILE


## Passaggi successivi

* Per ulteriori informazioni sull'utilizzo di Azure CLI per lavorare con le risorse in modalità arm, vedere [utilizzando l'interfaccia della riga di comando di Azure con Gestione risorse](xplat-cli-azure-resource-manager.md) e [Managing Role-Based controllo di accesso con l'interfaccia della riga di comando di Azure](../role-based-access-control-xplat-cli.md).
* Per ulteriori esempi di comandi CLI, vedere [utilizzando l'interfaccia della riga di comando di Azure per Mac, Linux e Windows](../virtual-machines-command-line-tools.md) e [tramite la CLI di Azure per Mac, Linux e Windows con Gestione risorse di Azure](azure-cli-arm-commands.md).

<!--HONumber=52-->
 