<properties linkid="manage-linux-common-task-mongodb-virtual-machine" urlDisplayName="Install MongoDB" pageTitle="Install MongoDB on a Linux virtual machine in Azure" metaKeywords="Azure vm CentOS, Azure vm Linux, Linux vm, Linux MongoDB" description="Learn how to create an Azure virtual machine with CentOS Linux, and then use SSH or PuTTY to install MongoDB." metaCanonical="" services="virtual-machines" documentationCenter="" title="Install MongoDB on a virtual machine running CentOS Linux in Azure" authors="kathydav" solutions="" manager="jeffreyg" editor="tysonn" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-linux" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="kathydav"></tags>

# Installazione di MongoDB in una macchina virtuale che esegue CentOS Linux in Azure

[MongoDB][MongoDB] è un diffuso database NoSQL open source a prestazioni elevate. Dalla raccolta immagini del portale di gestione di Azure, è possibile creare una macchina virtuale che esegue CentOS Linux. Su tale macchina virtuale sarà quindi possibile installare e configurare un database MongoDB.

Si apprenderà come:

-   Utilizzare il portale di gestione per selezionare e installare una macchina virtuale Linux che esegue CentOS Linux dalla raccolta.

-   Connettersi alla macchina virtuale tramite SSH o PuTTY.
-   Installare MongoDB nella macchina virtuale.

## Creazione di una macchina virtuale che esegue CentOS Linux

[WACOM.INCLUDE [create-and-configure-centos-vm-in-portal][create-and-configure-centos-vm-in-portal]]

## Collegamento di un disco dati

[WACOM.INCLUDE [attach-data-disk-centos-vm-in-portal][attach-data-disk-centos-vm-in-portal]]

## Installazione ed esecuzione di MongoDB sulla macchina virtuale

[WACOM.INCLUDE [install-and-run-mongo-on-centos-vm][install-and-run-mongo-on-centos-vm]]

## Riepilogo

In questa esercitazione è stato illustrato come creare una macchina virtuale e come stabilire una connessione remota a quest'ultima utilizzando SSH o PuTTY. È stato inoltre illustrato come installare e configurare MongoDB sulla macchina virtuale Linux. Per ulteriori informazioni su MongoDB, vedere la [￼Documentazione di MongoDB][￼Documentazione di MongoDB].

  [MongoDB]: http://www.mongodb.org/
  [create-and-configure-centos-vm-in-portal]: ../includes/create-and-configure-centos-vm-in-portal.md
  [attach-data-disk-centos-vm-in-portal]: ../includes/attach-data-disk-centos-vm-in-portal.md
  [install-and-run-mongo-on-centos-vm]: ../includes/install-and-run-mongo-on-centos-vm.md
  [￼Documentazione di MongoDB]: http://www.mongodb.org/display/DOCS/Home
