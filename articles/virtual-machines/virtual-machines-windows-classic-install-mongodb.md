<properties
	pageTitle="Installazione di MongoDB in una macchina virtuale di Windows | Microsoft Azure"
	description="Informazioni su come installare MongoDB in una macchina virtuale di Azure creata con il modello di distribuzione classica che esegue Windows Server."
	services="virtual-machines-windows"
	documentationCenter=""
	authors="iainfoulds"
	manager="timlt"
	editor="tysonn"
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/07/2016"
	ms.author="iainfou"/>

#Installazione di MongoDB in una macchina virtuale di Windows

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

[MongoDB][MongoDB] è un diffuso database NoSQL open source a prestazioni elevate. Questo articolo fornisce informazioni dettagliate sulla creazione di una nuova macchina virtuale Windows Server usando il [portale di Azure classico][AzurePortal], creando e collegando un disco dati alla VM e quindi installando e configurando MongoDB. Se in Azure è già disponibile una macchina virtuale da usare, è possibile passare direttamente all'[installazione e configurazione di MongoDB](#install-and-run-mongo-on-win2k8-vm).


## Creare una macchina virtuale che esegue Windows Server

Per creare una macchina virtuale, seguire queste istruzioni.

[AZURE.INCLUDE [virtual-machines-create-WindowsVM](../../includes/virtual-machines-create-windowsvm.md)]

> [AZURE.NOTE] Durante la creazione della macchina virtuale, è possibile aggiungere un endpoint per MongoDB e configurarlo come segue: assegnare il nome **Mongo**, utilizzare **TCP** come protocollo e impostare le porte pubbliche e private su **27017**.

## Collegamento di un disco dati
Per fornire la risorsa di archiviazione per la macchina virtuale, collegare un disco dati e inizializzarlo affinché possa essere utilizzato da Windows. È possibile collegare un disco dati esistente, se si dispone già di dati da utilizzare oppure un disco vuoto.

[AZURE.INCLUDE [howto-attach-disk-windows-linux](../../includes/howto-attach-disk-windows-linux.md)]

Per istruzioni sull’inizializzazione del disco, vedere "Procedura: inizializzazione di un nuovo disco dati in Windows Server" in [Come collegare un disco dati a una macchina virtuale di Windows](virtual-machines-windows-classic-attach-disk.md).

## Installare ed eseguire MongoDB nella macchina virtuale

[AZURE.INCLUDE [install-and-run-mongo-on-win2k8-vm](../../includes/install-and-run-mongo-on-win2k8-vm.md)]

## Riepilogo
In questa esercitazione è stato illustrato come creare una macchina virtuale che esegue Windows Server, come stabilire una connessione remota a quest'ultima e come collegare un disco dati. È stato inoltre illustrato come installare e configurare MongoDB sulla macchina virtuale basata su Windows. È ora possibile accedere a MongoDB sulla macchina virtuale basata su Windows, seguendo gli argomenti avanzati nella [documentazione di MongoDB][MongoDocs].

[MongoDocs]: http://docs.mongodb.org/manual/
[MongoDB]: http://www.mongodb.org/
[AzurePortal]: http://manage.windowsazure.com

<!---HONumber=AcomDC_0629_2016-->