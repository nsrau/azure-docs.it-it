<properties linkid="manage-windows-common-task-mongodb-vm" urlDisplayName="Install MongoDB" pageTitle="Install MongoDB on a Windows Server virtual machine" metaKeywords="Azure vm, Azure MongoDB, Azure remote desktop" description="Learn how to create an Azure virtual machine with Windows Server 2008 R2, and then use Remote Desktop to install MongoDB." metaCanonical="" services="virtual-machines" documentationCenter="" title="Install MongoDB on a virtual machine running Windows Server in Azure" authors="kathydav" solutions="" manager="jeffreyg" editor="tysonn" />

Installazione di MongoDB su una macchina virtuale che esegue Windows Server in Azure
====================================================================================

[MongoDB](http://www.mongodb.org/) è un diffuso database NoSQL open source a prestazioni elevate. Dalla raccolta immagini del [portale di gestione di Azure](http://manage.windowsazure.com), è possibile creare una macchina virtuale che esegue Windows Server. Su tale macchina virtuale sarà quindi possibile installare e configurare un database MongoDB.

In questa esercitazione si apprenderà come:

-   Utilizzare il portale di gestione per creare una macchina virtuale Windows Server dalla raccolta.
-   Connettersi alla macchina virtuale utilizzando il Desktop remoto.
-   Installare MongoDB nella macchina virtuale.

Creazione di una macchina virtuale che esegue Windows Server 2008 R2
--------------------------------------------------------------------

[WACOM.INCLUDE [create-and-configure-windows-server-2008-vm-in-portal](../includes/create-and-configure-windows-server-2008-vm-in-portal.md)]

Collegamento di un disco dati
-----------------------------

[WACOM.INCLUDE [attach-data-disk-windows-server-2008-vm-in-portal](../includes/attach-data-disk-windows-server-2008-vm-in-portal.md)]

Installazione ed esecuzione di MongoDB sulla macchina virtuale
--------------------------------------------------------------

[WACOM.INCLUDE [install-and-run-mongo-on-win2k8-vm](../includes/install-and-run-mongo-on-win2k8-vm.md)]

Riepilogo
---------

In questa esercitazione è stato illustrato come creare una macchina virtuale Windows Server e come stabilire una connessione remota a quest'ultima. È stato inoltre illustrato come installare e configurare MongoDB sulla macchina virtuale Windows. Per ulteriori informazioni su MongoDB, vedere la [Documentazione di MongoDB](http://www.mongodb.org/display/DOCS/Home).

