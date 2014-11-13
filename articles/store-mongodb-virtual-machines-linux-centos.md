<properties urlDisplayName="Install MongoDB" pageTitle="Installare MongoDB in una macchina virtuale che esegue CentOS Linux in Azure" metaKeywords="Azure, MongoDB" description="Informazioni su come installare Mongo DB in una macchina virtuale in Azure." metaCanonical="" services="" documentationCenter="" title="Installare MongoDB in una macchina virtuale che esegue CentOS Linux in Azure" authors="bbenz, MSOpenTech" solutions="" manager="timlt" editor="" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-linux" ms.devlang="" ms.topic="article" ms.date="01/01/1900" ms.author="bbenz, MSOpenTech" />

# Installare MongoDB in una macchina virtuale che esegue CentOS Linux in Azure

[MongoDB][MongoDB] è un diffuso database NoSQL open source a prestazioni elevate. Dalla raccolta immagini del [portale di gestione di Azure][portale di gestione di Azure], è possibile creare una macchina virtuale che esegue CentOS Linux. Su tale macchina virtuale sarà quindi possibile installare e configurare un database MongoDB.

Si apprenderà come:

-   Usare il portale di gestione per creare una macchina virtuale che esegue CentOS Linux dalla raccolta.
-   Connettersi alla macchina virtuale tramite SSH o PuTTY.
-   Installare MongoDB nella macchina virtuale.

## Creare una macchina virtuale che esegue CentOS Linux

[WACOM.INCLUDE [create-and-configure-centos-vm-in-portal](../includes/create-and-configure-centos-vm-in-portal.md)]

## Installare ed eseguire MongoDB nella macchina virtuale

[WACOM.INCLUDE [install-and-run-mongo-on-centos-vm](../includes/install-and-run-mongo-on-centos-vm.md)]

## Riepilogo

In questa esercitazione è stato illustrato come creare una macchina virtuale e come stabilire una connessione remota a quest'ultima usando SSH o PuTTY. È stato inoltre illustrato come installare e configurare MongoDB sulla macchina virtuale Linux. Per altre informazioni su MongoDB, vedere la ￼[Documentazione di MongoDB][Documentazione di MongoDB].

  [MongoDB]: http://www.mongodb.org/
  [portale di gestione di Azure]: http://manage.windowsazure.com
  [Documentazione di MongoDB]: http://www.mongodb.org/display/DOCS/Home
