<properties
	pageTitle="Installare MongoDB in una macchina virtuale con Windows Server"
	description="Informazioni su come installare MongoDB in una macchina virtuale di Azure che esegue Windows Server in Azure"
	services="virtual-machines"
	documentationCenter=""
	authors="dsk-2015"
	manager="timlt"
	editor="tysonn"
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/13/2015"
	ms.author="dkshir"/>

#Installare MongoDB in una macchina virtuale che esegue Windows Server

[MongoDB][MongoDB] è un diffuso database NoSQL open source a prestazioni elevate. Dalla raccolta immagini del [portale di gestione di Azure][AzureManagementPortal], è possibile creare una macchina virtuale che esegue Windows Server. Su tale macchina virtuale sarà quindi possibile installare e configurare un database MongoDB.


## Creare una macchina virtuale che esegue Windows Server

Per creare una macchina virtuale, seguire queste istruzioni.

[AZURE.INCLUDE [virtual-machines-create-WindowsVM](../../includes/virtual-machines-create-WindowsVM.md)]

> [AZURE.NOTE]Durante la creazione della macchina virtuale, è possibile aggiungere un endpoint per MongoDB, che deve essere configurato come segue: assegnare il nome **Mongo**, utilizzare **TCP** come protocollo e impostare le porte pubbliche e private su **27017**.

## Collegamento di un disco dati
Per fornire l'archiviazione per la macchina virtuale, collegare un disco dati e inizializzarlo affinché possa essere usato da Windows. È possibile collegare un disco dati esistente, se si dispone già di dati da usare, oppure un disco vuoto.

[AZURE.INCLUDE [howto-attach-disk-windows-linux](../../includes/howto-attach-disk-windows-linux.md)]

Per istruzioni sull’inizializzazione del disco, vedere "Procedura: inizializzazione di un nuovo disco dati in Windows Server" in [Come collegare un disco dati a una macchina virtuale di Windows](storage-windows-attach-disk.md).

## Installare ed eseguire MongoDB nella macchina virtuale

[AZURE.INCLUDE [install-and-run-mongo-on-win2k8-vm](../../includes/install-and-run-mongo-on-win2k8-vm.md)]

##Riepilogo
In questa esercitazione è stato illustrato come creare una macchina virtuale Windows Server, come stabilire una connessione remota a quest'ultima e come collegare un disco dati. È stato inoltre illustrato come installare e configurare MongoDB sulla macchina virtuale Windows. È ora possibile accedere a MongoDB sulla macchina virtuale di Windows, seguendo gli argomenti avanzati nella [documentazione di MongoDB][MongoDocs].

[MongoDocs]: http://docs.mongodb.org/manual/
[MongoDB]: http://www.mongodb.org/
[AzureManagementPortal]: http://manage.windowsazure.com

<!---HONumber=July15_HO4-->