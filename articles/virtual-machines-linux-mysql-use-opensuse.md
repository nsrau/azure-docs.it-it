<properties urlDisplayName="Install MySQL" pageTitle="Installazione di MySQL in una macchina virtuale che esegue OpenSUSE Linux in Azure" metaKeywords="Azure, MySQL" description="Learn to install MySQL on a virtual machine in Azure." metaCanonical="" services="" documentationCenter="" title="Install MongoDB on a virtual machine running CentOS Linux in Azure" authors="kathydav" solutions="" manager="timlt" editor="" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-linux" ms.devlang="na" ms.topic="article" ms.date="12/11/2014" ms.author="kathydav" />

# Installazione di MySQL in una macchina virtuale che esegue OpenSUSE Linux in Azure

[MySQL][MySQL] è un database SQL open source molto diffuso.  Con il [portale di gestione di Azure][AzurePortal] è possibile creare una macchina virtuale che esegue OpenSUSE Linux.  Su tale macchina virtuale sarà quindi possibile installare e configurare un database MySQL.

In questa esercitazione si apprenderà come:

- Usare il portale di gestione per creare una macchina virtuale OpenSUSE Linux da un'immagine disponibile tramite Azure.
- Connettersi alla macchina virtuale tramite SSH o PuTTY.
- Installare MySQL nella macchina virtuale.

[WACOM.INCLUDE [antares-iaas-signup-iaas](../includes/antares-iaas-signup-iaas.md)]

## Creazione di una macchina virtuale che esegue OpenSUSE Linux

[WACOM.INCLUDE [create-and-configure-opensuse-vm-in-portal](../includes/create-and-configure-opensuse-vm-in-portal.md)]

##Installazione ed esecuzione di MySQL nella macchina virtuale

[WACOM.INCLUDE [install-and-run-mysql-on-opensuse-vm](../includes/install-and-run-mysql-on-opensuse-vm.md)]

##Riepilogo
In questa esercitazione è stato illustrato come creare una macchina virtuale che esegue OpenSUSE Linux e come stabilire una connessione remota a quest'ultima usando SSH o PuTTY.  È stato inoltre illustrato come installare e configurare MySQL nella macchina virtuale Linux.  Per altre informazioni su MySQL, vedere la [Documentazione di MySQL][MySQLDocs].

[MySQLDocs]: http://dev.mysql.com/doc/
[MySQL]: http://www.mysql.com
[AzurePortal]: http://manage.windowsazure.com

<!--HONumber=35.1-->
