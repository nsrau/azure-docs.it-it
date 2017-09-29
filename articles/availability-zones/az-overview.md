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
ms.date: 09/28/2017
ms.author: markgal
ms.custom: mvc I am an ITPro and application developer, and I want to protect (use Availability Zones) my applications and data against data center failure (to build Highly Available applications).
ms.translationtype: HT
ms.sourcegitcommit: 4f77c7a615aaf5f87c0b260321f45a4e7129f339
ms.openlocfilehash: c6225d368044ee2b2ab24136c854f81ae473c9e1
ms.contentlocale: it-it
ms.lasthandoff: 09/22/2017

---

# <a name="overview-of-availability-zones-in-azure-preview"></a>Panoramica di Zone di disponibilità in Azure (anteprima)

Le zone di disponibilità offrono protezione dagli errori a livello di data center. Si trovano all'interno di un'area di Azure e ognuna ha alimentazione, rete e raffreddamento a sé. Per garantire la resilienza sono presenti almeno tre zone separate in tutte le aree abilitate. La separazione logica e fisica delle zone di disponibilità all'interno di un'area consente di proteggere applicazioni e dati da errori a livello di zona. 

![Visualizzazione concettuale di una zona che diventa indisponibile in un'area](./media/az-overview/az-graphic-two.png)

## <a name="regions-that-support-availability-zones"></a>Aree che supportano la funzionalità Zone di disponibilità

- Stati Uniti orientali 2
- Europa occidentale

## <a name="services-that-support-availability-zones"></a>Servizi che supportano la funzionalità Zone di disponibilità

I servizi che supportano la funzionalità Zone di disponibilità sono i seguenti:

- Macchine virtuali Linux
- Macchine virtuali Windows
- Set di scalabilità di macchine virtuali di zona
- Managed Disks
- Bilanciamento del carico

## <a name="supported-virtual-machine-size-families"></a>Famiglie di dimensioni di macchine virtuali supportate

- Av2
- Dv2
- DSv2


## <a name="get-started-with-the-availability-zones-preview"></a>Introduzione all'anteprima di Zone di disponibilità

L'anteprima di Zone di disponibilità è disponibile nelle aree Stati Uniti orientali 2 ed Europa occidentale per servizi specifici di Azure. 

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

