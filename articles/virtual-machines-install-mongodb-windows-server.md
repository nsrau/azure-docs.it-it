<properties 
	pageTitle="Installare MongoDB in una macchina virtuale con Windows Server" 
	description="Informazioni su come installare MongoDB in una macchina virtuale di Azure che esegue Windows Server in Azure" 
	services="virtual-machines" 
	documentationCenter="" 
	authors="KBDAzure" 
	manager="timlt" 
	editor="tysonn"/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-windows" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/24/2014" 
	ms.author="kathydav"/>

# Installare MongoDB in una macchina virtuale che esegue Windows Server

[MongoDB][MongoDB] è un diffuso database NoSQL open source a prestazioni elevate.  Dalla raccolta immagini del [portale di gestione di Azure][AzureManagementPortal] è possibile creare una macchina virtuale che esegue Windows Server.  Su tale macchina virtuale sarà quindi possibile installare e configurare un database MongoDB.

Questo articolo descrive come:

- Usare il portale di gestione per creare una macchina virtuale Windows Server dalla raccolta
- Connettersi alla macchina virtuale tramite Desktop remoto
- Collegare un disco dati alla macchina virtuale
- Installare MongoDB nella macchina virtuale

## Creare una macchina virtuale che esegue Windows Server

Le istruzioni che seguono sono generali e possono essere modificate creando un endpoint per consentire l'accesso a MongoDB da remoto. È anche possibile crearlo in un momento successivo, come descritto dopo le istruzioni per l'installazione di MongoDB. Nell'ultima pagina della procedura guidata aggiungere un endpoint e configurarlo come segue:

- Denominarlo **Mongo**
- Usare **TCP** come protocollo
- Impostare sia la porta pubblica che quella privata su **27017**.
 
[AZURE.INCLUDE [virtual-machines-create-WindowsVM](../includes/virtual-machines-create-WindowsVM.md)]

## Collegamento di un disco dati
Per fornire l'archiviazione per la macchina virtuale, collegare un disco dati e inizializzarlo affinché possa essere usato da Windows. È possibile collegare un disco dati esistente, se si dispone già di dati da usare, oppure un disco vuoto.

[AZURE.INCLUDE [howto-attach-disk-windows-linux](../includes/howto-attach-disk-windows-linux.md)]

Per istruzioni sull'inizializzazione del disco, vedere la sezione "Procedura: Inizializzare un nuovo disco dati in Windows Server" in [Come collegare un disco dati a una macchina virtuale](http://azure.microsoft.com/documentation/articles/storage-windows-attach-disk/).

## Installare ed eseguire MongoDB nella macchina virtuale 

[AZURE.INCLUDE [install-and-run-mongo-on-win2k8-vm](../includes/install-and-run-mongo-on-win2k8-vm.md)]

## Riepilogo
In questa esercitazione è stato illustrato come creare una macchina virtuale Windows Server, come stabilire una connessione remota a quest'ultima e come collegare un disco dati.  È stato inoltre illustrato come installare e configurare MongoDB sulla macchina virtuale Windows. Per altre informazioni su MongoDB, vedere la [Documentazione di MongoDB][MongoDocs].

[MongoDocs]: http://www.mongodb.org/display/DOCS/Home
[MongoDB]: http://www.mongodb.org/
[AzureManagementPortal]: http://manage.windowsazure.com

<!--HONumber=45--> 
