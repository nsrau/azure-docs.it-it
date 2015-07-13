<properties
	pageTitle="Installazione di MySQL in una macchina virtuale che esegue OpenSUSE Linux in Azure"
	description="Informazioni sull'installazione di MySQL in una macchina virtuale di Azure."
	services="virtual-machines"
	documentationCenter=""
	authors="KBDAzure"
	manager="timlt"
	editor=""
	tags="mysql"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/22/2015"
	ms.author="kathydav"/>

# Installazione di MySQL in una macchina virtuale che esegue OpenSUSE Linux in Azure

[MySQL][MySQL] è un database SQL open source molto diffuso. In questa esercitazione si apprenderà come:

- Come usare il [portale di gestione di Azure][AzurePortal] per creare una macchina virtuale OpenSUSE Linux da un'immagine disponibile tramite Azure.
- Come connettersi alla macchina virtuale tramite SSH o PuTTY.
- Come installare MySQL nella macchina virtuale.

[AZURE.INCLUDE [antares-iaas-signup-iaas](../../includes/antares-iaas-signup-iaas.md)]

## Creare una macchina virtuale che esegue OpenSUSE Linux

[AZURE.INCLUDE [create-and-configure-opensuse-vm-in-portal](../../includes/create-and-configure-opensuse-vm-in-portal.md)]

##Installare ed eseguire MySQL nella macchina virtuale

[AZURE.INCLUDE [install-and-run-mysql-on-opensuse-vm](../../includes/install-and-run-mysql-on-opensuse-vm.md)]

##Riepilogo
In questa esercitazione è stato illustrato come creare una macchina virtuale che esegue OpenSUSE Linux e come stabilire una connessione remota a quest'ultima usando SSH o PuTTY. È stato inoltre illustrato come installare e configurare MySQL nella macchina virtuale Linux. Per ulteriori informazioni su MySQL, vedere la [Documentazione di MySQL][MySQLDocs].

[MySQLDocs]: http://dev.mysql.com/doc/
[MySQL]: http://www.mysql.com
[AzurePortal]: http://manage.windowsazure.com
 

<!---HONumber=July15_HO1-->