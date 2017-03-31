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
ms.sourcegitcommit: 356de369ec5409e8e6e51a286a20af70a9420193
ms.openlocfilehash: 01b798a25575b66f89057315ce80d6cc0cde53b5
ms.lasthandoff: 03/27/2017


---
# <a name="install-mysql-on-a-virtual-machine-running-opensuse-linux-in-azure"></a>Installazione di MySQL in una macchina virtuale che esegue OpenSUSE Linux in Azure
[MySQL][MySQL] è un database SQL open source molto diffuso. Questa esercitazione illustra come creare una macchina virtuale che esegue OpenSUSE Linux, poi installa MySQL.

> [!IMPORTANT] 
> Azure offre due diversi modelli di distribuzione per creare e usare le risorse: [Gestione risorse e la distribuzione classica](../../../resource-manager-deployment-model.md). Questo articolo illustra l'uso del modello di distribuzione classica. Microsoft consiglia di usare il modello di Gestione risorse per le distribuzioni più recenti.

<br>

## <a name="create-a-virtual-machine-running-opensuse-linux"></a>Creare una macchina virtuale che esegue OpenSUSE Linux
[!INCLUDE [create-and-configure-opensuse-vm-in-portal](../../../../includes/create-and-configure-opensuse-vm-in-portal.md)]

## <a name="install-and-run-mysql-on-the-virtual-machine"></a>Installare ed eseguire MySQL nella macchina virtuale
[!INCLUDE [install-and-run-mysql-on-opensuse-vm](../../../../includes/install-and-run-mysql-on-opensuse-vm.md)]

## <a name="next-steps"></a>Passaggi successivi
Per informazioni su MySQL, vedere la [documentazione di MySQL][MySQLDocs].

[MySQLDocs]:http://dev.mysql.com/doc/index-topic.html
[MySQL]:http://www.mysql.com


