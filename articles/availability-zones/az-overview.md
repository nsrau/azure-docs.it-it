---
title: "Panoramica di Zone di disponibilità| Microsoft Docs"
description: "Questo articolo offre una panoramica di Zone di disponibilità in Azure."
services: 
documentationcenter: 
author: markgalioto
manager: carmonm
editor: 
tags: 
ms.assetid: 
ms.service: 
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/16/2017
ms.author: markgal
ms.custom: mvc I am an ITPro and application developer, and I want to protect (use Availability Zones) my applications and data against data center failure (to build Highly Available applications).
ms.openlocfilehash: 9d21b112a1021cbefa42722404391220e6c018e5
ms.sourcegitcommit: 9ea2edae5dbb4a104322135bef957ba6e9aeecde
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/03/2018
---
# <a name="overview-of-availability-zones-in-azure-preview"></a>Panoramica di Zone di disponibilità in Azure (anteprima)

Le zone di disponibilità offrono protezione dagli errori a livello di data center. Si trovano all'interno di un'area di Azure e ognuna ha alimentazione, rete e raffreddamento a sé. Per garantire la resilienza sono presenti almeno tre zone separate in tutte le aree abilitate. La separazione logica e fisica delle zone di disponibilità all'interno di un'area consente di proteggere applicazioni e dati da errori a livello di zona. 

![Visualizzazione concettuale di una zona che diventa indisponibile in un'area](./media/az-overview/az-graphic-two.png)

## <a name="regions-that-support-availability-zones"></a>Aree che supportano la funzionalità Zone di disponibilità

- Stati Uniti orientali 2
- Europa occidentale
- Francia centrale

## <a name="services-that-support-availability-zones"></a>Servizi che supportano la funzionalità Zone di disponibilità

I servizi che supportano la funzionalità Zone di disponibilità sono i seguenti:

- Macchine virtuali Linux
- Macchine virtuali Windows
- Set di scalabilità di macchine virtuali di zona
- Managed Disks
- Bilanciamento del carico
- Indirizzo IP pubblico

## <a name="get-started-with-the-availability-zones-preview"></a>Introduzione all'anteprima di Zone di disponibilità

L'anteprima di zone di disponibilità è disponibile negli Stati Uniti orientali 2, Europa occidentale e aree Francia centrale per i servizi di Azure specifici. 

1. [Effettuare l'iscrizione all'anteprima di Zone di disponibilità](http://aka.ms/azenroll). 
2. Accedere alla sottoscrizione di Azure.
3. Scegliere un'area che supporta la funzionalità Zone di disponibilità.
4. Usare uno dei collegamenti seguenti per iniziare a usare Zone di disponibilità con il servizio. 
    - [Creare una macchina virtuale](../virtual-machines/windows/create-portal-availability-zone.md)
    - [Creare un set di scalabilità di macchine virtuali di zona](../virtual-machine-scale-sets/virtual-machine-scale-sets-portal-create.md)
    - [Aggiungere un disco gestito usando PowerShell](../virtual-machines/windows/attach-disk-ps.md#add-an-empty-data-disk-to-a-virtual-machine)
    - [Bilanciamento del carico](../load-balancer/load-balancer-standard-overview.md)

## <a name="next-steps"></a>Passaggi successivi
- [Modelli di avvio rapido](http://aka.ms/azqs)
