---
title: Creare una VM Linux classica usando l&quot;interfaccia della riga di comando di Azure 1.0 | Documentazione Microsoft
description: Informazioni su come creare una macchina virtuale con l&quot;interfaccia della riga di comando di Azure 1.0 usando il modello di distribuzione classica.
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: f8071a2e-ed91-4f77-87d9-519a37e5364f
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/09/2017
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: 356de369ec5409e8e6e51a286a20af70a9420193
ms.openlocfilehash: c4d29c453d22a2765f0884c11d98d84676b3bf32
ms.lasthandoff: 03/27/2017


---
# <a name="how-to-create-a-classic-linux-vm-with-the-azure-cli-10"></a>Come creare una VM Linux classica con l'interfaccia della riga di comando di Azure 1.0
> [!IMPORTANT] 
> Azure offre due diversi modelli di distribuzione per creare e usare le risorse: [Gestione risorse e la distribuzione classica](../../../resource-manager-deployment-model.md). Questo articolo illustra l'uso del modello di distribuzione classica. Microsoft consiglia di usare il modello di Gestione risorse per le distribuzioni più recenti. Per la versione di Resource Manager, vedere [qui](../../virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Questo argomento descrive come creare una macchina virtuale con l'interfaccia della riga di comando di Azure 1.0 usando il modello di distribuzione classica. Viene utilizzata un'immagine Linux dalle **IMMAGINI** disponibili in Azure. I comandi dell'interfaccia della riga di comando di Azure 1.0 offrono le opzioni di configurazione seguenti:

* Connessione della macchina virtuale a una rete virtuale
* Aggiunta della macchina virtuale a un servizio cloud esistente
* Aggiunta della macchina virtuale a un account di archiviazione esistente
* Aggiunta della macchina virtuale a un set di disponibilità o a un percorso

> [!IMPORTANT]
> Se si desidera che la macchina virtuale utilizzi una rete virtuale in modo da poter effettuare la connessione direttamente tramite il nome host o configurare connessioni cross-premise, assicurarsi di specificare la rete virtuale quando viene creata la macchina virtuale. Una macchina virtuale può essere configurata per accedere a una rete virtuale solo quando viene creata la macchina virtuale. Per informazioni dettagliate sulle reti virtuali, vedere [Panoramica di Rete virtuale](http://go.microsoft.com/fwlink/p/?LinkID=294063).
> 
> 

## <a name="how-to-create-a-linux-vm-using-the-classic-deployment-model"></a>Come creare una macchina virtuale Linux usando il modello di distribuzione classica
[!INCLUDE [virtual-machines-create-LinuxVM](../../../../includes/virtual-machines-create-linuxvm.md)]


