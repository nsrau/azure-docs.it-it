<properties linkid="virtual-machines-linux-mysql-use-opensuse" urlDisplayName="Install MongoDB" pageTitle="nstall MongoDB on a virtual machine running CentOS Linux in Azure" metaKeywords="Azure, MongoDB" description="Learn how to install Mongo DB on a virtual machine in Azure." metaCanonical="" services="" documentationCenter="" title="Install MongoDB on a virtual machine running CentOS Linux in Azure" authors="" solutions="" manager="" editor="" />

Installazione di MySQL in una macchina virtuale che esegue OpenSUSE Linux in Azure
==================================================================================

[MySQL](http://www.mysql.com) è un database SQL open source molto diffuso. Dalla raccolta immagini del [portale di gestione di Azure](http://manage.windowsazure.com), è possibile creare una macchina virtuale che esegue OpenSUSE Linux. Su tale macchina virtuale sarà quindi possibile installare e configurare un database MySQL.

In questa esercitazione si apprenderà come:

-   Utilizzare il portale di gestione per creare una macchina virtuale OpenSUSE Linux dalla raccolta.
-   Connettersi alla macchina virtuale tramite SSH o PuTTY.
-   Installare MySQL nella macchina virtuale.

Iscrizione alla funzionalità di anteprima di Macchine virtuali
--------------------------------------------------------------

Per creare una macchina virtuale, è necessario effettuare l'iscrizione alla funzionalità di anteprima di Macchine virtuali di Azure. Se non si dispone di un account Azure, è possibile iscriversi per una versione di valutazione gratuita.

[WACOM.INCLUDE [antares-iaas-signup-iaas](../includes/antares-iaas-signup-iaas.md)]

Creazione di una macchina virtuale che esegue OpenSUSE Linux
------------------------------------------------------------

[WACOM.INCLUDE [create-and-configure-opensuse-vm-in-portal](../includes/create-and-configure-opensuse-vm-in-portal.md)]

Installazione ed esecuzione di MySQL nella macchina virtuale
------------------------------------------------------------

[WACOM.INCLUDE [install-and-run-mysql-on-opensuse-vm](../includes/install-and-run-mysql-on-opensuse-vm.md)]

Riepilogo
---------

In questa esercitazione è stato illustrato come creare una macchina virtuale che esegue OpenSUSE Linux e come stabilire una connessione remota a quest'ultima utilizzando SSH o PuTTY. È stato inoltre illustrato come installare e configurare MySQL nella macchina virtuale Linux. Per ulteriori informazioni su MySQL, vedere la [Documentazione di MySQL](http://dev.mysql.com/doc/).

