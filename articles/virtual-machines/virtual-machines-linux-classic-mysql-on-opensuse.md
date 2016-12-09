---
title: Installare MySQL in una macchina virtuale OpenSUSE | Microsoft Docs
description: Informazioni su come installare MySQL in una macchina virtuale OpenSUSE Linux in Azure.
services: virtual-machines-linux
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 1594e10e-c314-455a-9efb-a89441de364b
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 07/19/2016
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: bf51a9835448339b4fb4d35bd0c97097bf767ec1


---
# <a name="install-mysql-on-a-virtual-machine-running-opensuse-linux-in-azure"></a>Installazione di MySQL in una macchina virtuale che esegue OpenSUSE Linux in Azure
[MySQL][MySQL] è un database SQL open source molto diffuso. Questa esercitazione illustra come creare una macchina virtuale che esegue OpenSUSE Linux, poi installa MySQL.

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

<br>

## <a name="create-a-virtual-machine-running-opensuse-linux"></a>Creare una macchina virtuale che esegue OpenSUSE Linux
[!INCLUDE [create-and-configure-opensuse-vm-in-portal](../../includes/create-and-configure-opensuse-vm-in-portal.md)]

## <a name="install-and-run-mysql-on-the-virtual-machine"></a>Installare ed eseguire MySQL nella macchina virtuale
[!INCLUDE [install-and-run-mysql-on-opensuse-vm](../../includes/install-and-run-mysql-on-opensuse-vm.md)]

## <a name="next-steps"></a>Passaggi successivi
Per informazioni su MySQL, vedere la [Documentazione di MySQL][MySQLDocs].

[MySQLDocs]: http://dev.mysql.com/doc/index-topic.html
[MySQL]: http://www.mysql.com




<!--HONumber=Nov16_HO3-->


