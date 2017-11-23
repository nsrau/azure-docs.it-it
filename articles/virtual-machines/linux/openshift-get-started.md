---
title: Panoramica di OpenShift in Azure | Microsoft Docs
description: Una panoramica di OpenShift in Azure.
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
ms.openlocfilehash: 5d154a1c860a5ebd7af2efd55b470bb14efe6c67
ms.sourcegitcommit: 6a22af82b88674cd029387f6cedf0fb9f8830afd
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/11/2017
---
# <a name="openshift-in-azure"></a>OpenShift in Azure

OpenShift è una piattaforma applicativa di contenitori aperta ed estendibile che consente di usare Docker e Kubernetes in ambito aziendale.  

OpenShift include Kubernetes per la gestione e l'orchestrazione di contenitori. Aggiunge strumenti per sviluppatori e incentrati sulle operazioni che consentono:

- Sviluppo rapido di applicazioni.
- Semplicità di distribuzione e scalabilità.
- Gestione del ciclo di vita a lungo termine per i team e le applicazioni.

Sono disponibili più versioni di OpenShift, due delle quali possono essere eseguite in Azure:

- OpenShift Origin
- OpenShift Container Platform
- OpenShift Online
- OpenShift Dedicated

Delle quattro versioni descritte in questo articolo, due possono essere distribuite in Azure dai clienti in modo autonomo: OpenShift Origin e OpenShift Container Platform.

## <a name="openshift-origin"></a>OpenShift Origin

Origin è un progetto upstream [open source](https://www.openshift.org/) di OpenShift supportato dalla community. È possibile installare Origin in CentOS o Red Hat Enterprise Linux (RHEL).

## <a name="openshift-container-platform"></a>OpenShift Container Platform

Container Platform è una [versione commerciale](https://www.openshift.com) per uso aziendale di Red Hat supportata da Red Hat. Con questa versione, il cliente acquista i diritti necessari per OpenShift Container Platform ed è responsabile dell'installazione e della gestione dell'intera infrastruttura.

Poiché il cliente è "proprietario" dell'intera piattaforma, può eseguire l'installazione nel data center locale o nel cloud pubblico (come Azure, AWS, Google e così via).

## <a name="openshift-online"></a>OpenShift Online

Online è una versione di OpenShift *multi-tenant* gestita da Red Hat (tramite Container Platform). Red Hat gestisce tutta l'infrastruttura sottostante (macchine virtuali, cluster OpenShift, rete e archiviazione). 

Con questa versione, il cliente distribuisce i contenitori, ma non ha alcun controllo sugli host in cui i contenitori vengono eseguiti. Poiché si tratta di una versione multi-tenant, i contenitori possono trovarsi nelle stesse macchine virtuali host dei contenitori di altri clienti. Il costo è calcolato per contenitore.

## <a name="openshift-dedicated"></a>OpenShift Dedicated

Dedicated è una versione di OpenShift a *singolo tenant* gestita da Red Hat (tramite Container Platform). Red Hat gestisce tutta l'infrastruttura sottostante (macchine virtuali, cluster OpenShift, rete, archiviazione e così via). Il cluster è specifico di un cliente e viene eseguito in un cloud pubblico (come AWS, Google o Azure, disponibile dall'inizio del 2018). Il cluster iniziale include quattro nodi applicazione a 48.000 $/anno (pagamento anticipato).

## <a name="next-steps"></a>Passaggi successivi

- [Configurare i prerequisiti comuni per OpenShift in Azure](./openshift-prerequisites.md)
- [Distribuire OpenShift Origin in Azure](./openshift-origin.md)
- [Distribuire OpenShift Container Platform in Azure](./openshift-container-platform.md)
- [Attività di post-distribuzione](./openshift-post-deployment.md)
- [Risoluzione dei problemi relativi alla distribuzione di OpenShift](./openshift-troubleshooting.md)
