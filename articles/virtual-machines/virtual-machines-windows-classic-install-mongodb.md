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
    ms.date="10/10/2016"
    ms.author="iainfou"/>


#<a name="install-mongodb-on-a-windows-vm"></a>Installazione di MongoDB in una macchina virtuale di Windows

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)] Per installare e configurare MongoDB tramite il modello di distribuzione Resource Manager, vedere [questo articolo](virtual-machines-windows-classic-install-mongodb.md).

[MongoDB][MongoDB] è un diffuso database NoSQL open source a prestazioni elevate. Questo articolo illustra come creare una macchina virtuale (VM) di Windows Server usando il [portale di Azure classico][AzurePortal]. Quindi, creare e collegare un disco dati alla macchina virtuale prima di installare e configurare MongoDB. Se in Azure è già disponibile una macchina virtuale da usare, è possibile passare direttamente all'[installazione e configurazione di MongoDB](#install-and-run-mongodb-on-the-virtual-machine).


## <a name="create-a-virtual-machine-running-windows-server"></a>Creare una macchina virtuale che esegue Windows Server

Per creare una macchina virtuale, seguire queste istruzioni.

[AZURE.INCLUDE [virtual-machines-create-WindowsVM](../../includes/virtual-machines-create-windowsvm.md)]

> [AZURE.NOTE] Durante la creazione della macchina virtuale, è possibile aggiungere un endpoint per MongoDB e configurarlo come segue: denominarlo **Mongo**, usare il protocollo **TCP** e impostare le porte pubbliche e private su **27017**.

## <a name="attach-a-data-disk"></a>Collegamento di un disco dati
Per fornire la risorsa di archiviazione per la macchina virtuale, collegare un disco dati e inizializzarlo affinché possa essere utilizzato da Windows. È possibile collegare un eventuale disco dati esistente o collegare un disco vuoto.

[AZURE.INCLUDE [howto-attach-disk-windows-linux](../../includes/howto-attach-disk-windows-linux.md)]

Per istruzioni sull’inizializzazione del disco, vedere "Procedura: inizializzazione di un nuovo disco dati in Windows Server" in [Come collegare un disco dati a una macchina virtuale di Windows](virtual-machines-windows-classic-attach-disk.md).

## <a name="install-and-run-mongodb-on-the-virtual-machine"></a>Installare ed eseguire MongoDB nella macchina virtuale

[AZURE.INCLUDE [install-and-run-mongo-on-win2k8-vm](../../includes/install-and-run-mongo-on-win2k8-vm.md)]

## <a name="summary"></a>Riepilogo
In questa esercitazione è stato illustrato come creare una macchina virtuale che esegue Windows Server, come stabilire una connessione remota a quest'ultima e come collegare un disco dati.  È stato inoltre illustrato come installare e configurare MongoDB sulla macchina virtuale basata su Windows. È ora possibile accedere a MongoDB nella macchina virtuale basata su Windows, seguendo gli argomenti avanzati illustrati nella [documentazione di MongoDB][MongoDocs].

[MongoDocs]: http://docs.mongodb.org/manual/
[MongoDB]: http://www.mongodb.org/
[AzurePortal]: http://manage.windowsazure.com



<!--HONumber=Oct16_HO2-->


