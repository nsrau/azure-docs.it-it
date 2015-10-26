<properties
	pageTitle="Installazione di MySQL in una macchina virtuale OpenSUSE di Linux in Microsoft Azure"
	description="Informazioni sull'installazione di MySQL in una macchina virtuale di Azure."
	services="virtual-machines"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/31/2015"
	ms.author="cynthn"/>

# Installazione di MySQL in una macchina virtuale che esegue OpenSUSE Linux in Azure

[MySQL][MySQL] è un database SQL open source molto diffuso. Questa esercitazione illustra come creare una macchina virtuale che esegue OpenSUSE Linux, poi installa MySQL.

[AZURE.INCLUDE [free-trial-note](../../includes/free-trial-note.md)]

## Creare una macchina virtuale che esegue OpenSUSE Linux

[AZURE.INCLUDE [create-and-configure-opensuse-vm-in-portal](../../includes/create-and-configure-opensuse-vm-in-portal.md)]

## Installare ed eseguire MySQL nella macchina virtuale

[AZURE.INCLUDE [install-and-run-mysql-on-opensuse-vm](../../includes/install-and-run-mysql-on-opensuse-vm.md)]

## Passaggi successivi
Per informazioni su MySQL, vedere la [Documentazione di MySQL][MySQLDocs].

[MySQLDocs]: http://dev.mysql.com/doc/index-topic.html
[MySQL]: http://www.mysql.com
[AzurePortal]: http://manage.windowsazure.com

<!---HONumber=Oct15_HO3-->