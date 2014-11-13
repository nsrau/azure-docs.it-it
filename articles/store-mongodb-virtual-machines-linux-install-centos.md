<properties urlDisplayName="Install MongoDB" pageTitle="Installare MongoDB in una macchina virtuale Linux in Azure" metaKeywords="Azure vm CentOS, Azure vm Linux, Linux vm, Linux MongoDB" description="Informazioni su come creare una macchina virtuale di Azure con CentOS Linux e quindi usare SSH o PuTTY per installare MongoDB." metaCanonical="" services="virtual-machines" documentationCenter="" title="Installare MongoDB in una macchina virtuale che esegue CentOS Linux in Azure" authors="kathydav" solutions="" manager="timlt" editor="tysonn" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-linux" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="kathydav" />

# Installare MongoDB in una macchina virtuale che esegue CentOS Linux in Azure

[MongoDB][MongoDB] è un diffuso database NoSQL open source a prestazioni elevate. Dalla raccolta immagini del portale di gestione di Azure, è possibile creare una macchina virtuale che esegue CentOS Linux. Su tale macchina virtuale sarà quindi possibile installare e configurare un database MongoDB.

Si apprenderà come:

-   Usare il portale di gestione per selezionare e installare una macchina virtuale Linux che esegue CentOS Linux dalla raccolta.

-   Connettersi alla macchina virtuale tramite SSH o PuTTY.
-   Installare MongoDB nella macchina virtuale.

## Creare una macchina virtuale che esegue CentOS Linux

[WACOM.INCLUDE [create-and-configure-centos-vm-in-portal](../includes/create-and-configure-centos-vm-in-portal.md)]

## Collegamento di un disco dati

[WACOM.INCLUDE [attach-data-disk-centos-vm-in-portal](../includes/attach-data-disk-centos-vm-in-portal.md)]

## Installare ed eseguire MongoDB nella macchina virtuale

[WACOM.INCLUDE [install-and-run-mongo-on-centos-vm](../includes/install-and-run-mongo-on-centos-vm.md)]

## Riepilogo

In questa esercitazione è stato illustrato come creare una macchina virtuale e come stabilire una connessione remota a quest'ultima usando SSH o PuTTY. È stato inoltre illustrato come installare e configurare MongoDB sulla macchina virtuale Linux. Per altre informazioni su MongoDB, vedere la ￼[Documentazione di MongoDB][Documentazione di MongoDB].

  [MongoDB]: http://www.mongodb.org/
  [Documentazione di MongoDB]: http://www.mongodb.org/display/DOCS/Home
