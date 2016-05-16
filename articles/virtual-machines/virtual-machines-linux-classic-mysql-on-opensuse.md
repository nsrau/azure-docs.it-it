<properties
	pageTitle="Installazione di MySQL in una macchina virtuale OpenSUSE di Linux in Microsoft Azure"
	description="Informazioni sull'installazione di MySQL in una macchina virtuale di Azure."
	services="virtual-machines-linux"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines-linux"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/29/2016"
	ms.author="cynthn"/>

# Installazione di MySQL in una macchina virtuale che esegue OpenSUSE Linux in Azure

[MySQL][MySQL] è un database SQL open source molto diffuso. Questa esercitazione illustra come creare una macchina virtuale che esegue OpenSUSE Linux, poi installa MySQL.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Modello Gestione risorse.


<br>


## Creare una macchina virtuale che esegue OpenSUSE Linux

[AZURE.INCLUDE [create-and-configure-opensuse-vm-in-portal](../../includes/create-and-configure-opensuse-vm-in-portal.md)]

## Installare ed eseguire MySQL nella macchina virtuale

[AZURE.INCLUDE [install-and-run-mysql-on-opensuse-vm](../../includes/install-and-run-mysql-on-opensuse-vm.md)]

## Passaggi successivi
Per informazioni su MySQL, vedere la [Documentazione di MySQL][MySQLDocs].

[MySQLDocs]: http://dev.mysql.com/doc/index-topic.html
[MySQL]: http://www.mysql.com

<!---HONumber=AcomDC_0504_2016-->