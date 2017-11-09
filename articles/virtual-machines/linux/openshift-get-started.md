---
title: Panoramica di OpenShift in Azure | Microsoft Docs
description: Panoramica di OpenShift in Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldw
manager: najoshi
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 
ms.author: haroldw
ms.openlocfilehash: f9641b52db91a4356f6d5789a8cd78a6bb3da02b
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2017
---
# <a name="openshift-overview"></a>Panoramica di OpenShift

OpenShift è una piattaforma applicativa di contenitori aperta ed estendibile che consente di usare Docker e Kubernetes in ambito aziendale.  

OpenShift include Kubernetes per la gestione e l'orchestrazione di contenitori. Aggiunge strumenti per sviluppatori e incentrati sulle operazioni che consentono:

- Sviluppo rapido di applicazioni
- Semplicità di distribuzione e scalabilità
- Gestione del ciclo di vita a lungo termine per i team e le applicazioni

Sono disponibili più offerte di OpenShift, due delle quali possono essere eseguite in Azure.

- OpenShift Origin
- OpenShift Container Platform
- OpenShift Online
- OpenShift Dedicated

Di queste quattro offerte, due possono essere distribuite in Azure dai clienti in modo autonomo:OpenShift Origin e OpenShift Container Platform.

## <a name="openshift-origin"></a>OpenShift Origin

Progetto upstream [open source](https://www.openshift.org/) di OpenShift supportato dalla community. È possibile installare Origin in CentOS o RHEL.

## <a name="openshift-container-platform"></a>OpenShift Container Platform

Versione per uso aziendale ([offerta commerciale](https://www.openshift.com)) di Red Hat supportata da Red Hat. Il cliente acquista i diritti necessari per OpenShift Container Platform ed è responsabile dell'installazione e della gestione dell'intera infrastruttura.

Poiché il cliente è "proprietario" dell'intera piattaforma, può eseguire l'installazione nel data center locale, nel cloud pubblico (Azure, AWS, Google e così via) e in altre posizioni.

## <a name="openshift-online"></a>OpenShift Online

Versione di OpenShift **multi-tenant** gestita da Red Hat (tramite Container Platform). Red Hat gestisce tutta l'infrastruttura sottostante (macchine virtuali, cluster OpenShift, rete, archiviazione e così via). 

Il cliente distribuisce i contenitori, ma non ha alcun controllo sugli host in cui i contenitori vengono eseguiti. Poiché si tratta di una versione multi-tenant, i contenitori possono trovarsi nelle stesse macchine virtuali host dei contenitori di altri clienti. Il costo è calcolato per contenitore.

## <a name="openshift-dedicated"></a>OpenShift Dedicated

Versione di OpenShift a **singolo tenant** gestita da Red Hat (tramite Container Platform). Red Hat gestisce tutta l'infrastruttura sottostante (macchine virtuali, cluster OpenShift, rete, archiviazione e così via). Il cluster è specifico di un cliente e viene eseguito in un cloud pubblico (AWS, Google, Azure, disponibile dall'inizio del 2018). Il cluster iniziale include quattro nodi applicazione a 48.000 $/anno (pagamento anticipato per un intero anno).

## <a name="next-steps"></a>Passaggi successivi

- [Configurare i prerequisiti comuni per OpenShift in Azure](./openshift-prerequisites.md)
- [Distribuire OpenShift Origin](./openshift-origin.md)
- [Distribuire OpenShift Container Platform](./openshift-container-platform.md)
- [Attività successive alla distribuzione](./openshift-post-deployment.md)
- [Risoluzione dei problemi relativi alla distribuzione di OpenShift](./openshift-troubleshooting.md)
