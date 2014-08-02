<properties linkid="store-mongodb-virtual-machines-linux-centos" urlDisplayName="Install MongoDB" pageTitle="nstall MongoDB on a virtual machine running CentOS Linux in Azure" metaKeywords="Azure, MongoDB" description="Learn how to install Mongo DB on a virtual machine in Azure." metaCanonical="" services="" documentationCenter="" title="Install MongoDB on a virtual machine running CentOS Linux in Azure" authors="" solutions="" manager="" editor="" />

Installazione di MongoDB in una macchina virtuale che esegue CentOS Linux in Azure
==================================================================================

[MongoDB](http://www.mongodb.org/) è un diffuso database NoSQL open source a prestazioni elevate. Dalla raccolta immagini del [portale di gestione di Azure](http://manage.windowsazure.com), è possibile creare una macchina virtuale che esegue CentOS Linux. Su tale macchina virtuale sarà quindi possibile installare e configurare un database MongoDB.

Si apprenderà come:

-   Utilizzare il portale di gestione per creare una macchina virtuale che esegue CentOS Linux dalla raccolta.
-   Connettersi alla macchina virtuale tramite SSH o PuTTY.
-   Installare MongoDB nella macchina virtuale.

Iscrizione alla funzionalità di anteprima di Macchine virtuali
--------------------------------------------------------------

Per creare una macchina virtuale, è necessario effettuare l'iscrizione alla funzionalità di anteprima di Macchine virtuali di Azure. Se non si dispone di un account Azure, è possibile iscriversi per una versione di valutazione gratuita.

[WACOM.INCLUDE [antares-iaas-signup-iaas](../includes/antares-iaas-signup-iaas.md)]

Creazione di una macchina virtuale che esegue CentOS Linux
----------------------------------------------------------

[WACOM.INCLUDE [create-and-configure-centos-vm-in-portal](../includes/create-and-configure-centos-vm-in-portal.md)]

Installazione ed esecuzione di MongoDB sulla macchina virtuale
--------------------------------------------------------------

[WACOM.INCLUDE [install-and-run-mongo-on-centos-vm](../includes/install-and-run-mongo-on-centos-vm.md)]

Riepilogo
---------

In questa esercitazione è stato illustrato come creare una macchina virtuale e come stabilire una connessione remota a quest'ultima utilizzando SSH o PuTTY. È stato inoltre illustrato come installare e configurare MongoDB sulla macchina virtuale Linux. Per ulteriori informazioni su MongoDB, vedere la [Documentazione di MongoDB](http://www.mongodb.org/display/DOCS/Home).

