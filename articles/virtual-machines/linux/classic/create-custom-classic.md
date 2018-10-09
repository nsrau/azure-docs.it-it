---
title: Creare una VM Linux classica usando l'interfaccia della riga di comando classica di Azure | Microsoft Docs
description: Informazioni su come creare una macchina virtuale Linux con l'interfaccia della riga di comando classica di Azure usando il modello di distribuzione classico
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-service-management
ROBOTS: NOINDEX
ms.assetid: f8071a2e-ed91-4f77-87d9-519a37e5364f
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/09/2017
ms.author: cynthn
ms.openlocfilehash: 507d9e12a37d7bf187a3e56b04cb47ac0104773d
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2018
ms.locfileid: "46982042"
---
# <a name="how-to-create-a-classic-linux-vm-with-the-azure-classic-cli"></a>Come creare una macchina virtuale classica Linux con l'interfaccia della riga di comando classica di Azure
> [!IMPORTANT] 
> Azure offre due diversi modelli di distribuzione per creare e usare le risorse: [Gestione risorse e la distribuzione classica](../../../resource-manager-deployment-model.md). Questo articolo illustra l'uso del modello di distribuzione classica. Microsoft consiglia di usare il modello di Gestione risorse per le distribuzioni più recenti. Per la versione di Resource Manager, vedere [qui](../create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Questo argomento descrive come creare una macchina virtuale (VM) Linux con l'interfaccia della riga di comando classica di Azure usando il modello di distribuzione classica. Viene utilizzata un'immagine Linux dalle **IMMAGINI** disponibili in Azure. I comandi dell'interfaccia della riga di comando classica di Azure offrono, tra le altre, le opzioni di configurazione seguenti:

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

