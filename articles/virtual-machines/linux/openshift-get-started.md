---
title: Panoramica di OpenShift in Azure | Microsoft Docs
description: Una panoramica di OpenShift in Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldwongms
manager: joraio
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: ''
ms.author: haroldw
ms.openlocfilehash: d68215359d50ac153d6df3bbcce5a9b6171698bb
ms.sourcegitcommit: 5de9de61a6ba33236caabb7d61bee69d57799142
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50085440"
---
# <a name="openshift-in-azure"></a>OpenShift in Azure

OpenShift è una piattaforma applicativa di contenitori aperta ed estendibile che consente di usare Docker e Kubernetes in ambito aziendale.  

OpenShift include Kubernetes per la gestione e l'orchestrazione di contenitori. Aggiunge strumenti incentrati sugli sviluppatori e sulle operazioni che consentono di:

- Sviluppare applicazioni rapidamente.
- Eseguire facilmente operazioni di distribuzione e scalabilità.
- Gestire il ciclo di vita a lungo termine per i team e le applicazioni.

Sono disponibili più versioni di OpenShift:

- OpenShift Container Platform
- OpenShift in Azure (OpenShift completamente gestito disponibile dall'inizio del 2019)
- OKD (noto in precedenza come OpenShift Origin)
- OpenShift Dedicated
- OpenShift Online

Delle cinque versioni descritte in questo articolo, solo due possono essere distribuite in Azure dai clienti: OpenShift Container Platform e OKD.

## <a name="openshift-container-platform"></a>OpenShift Container Platform

Container Platform è una [versione commerciale](https://www.openshift.com) per uso aziendale di Red Hat supportata da Red Hat. Con questa versione, il cliente acquista i diritti necessari per OpenShift Container Platform ed è responsabile dell'installazione e della gestione dell'intera infrastruttura.

Poiché il cliente è "proprietario" dell'intera piattaforma, può eseguire l'installazione nel data center locale o nel cloud pubblico (come Azure, AWS, Google e così via).

## <a name="openshift-on-azure"></a>OpenShift in Azure

OpenShift in Azure è un'offerta completamente gestita di OpenShift in esecuzione in Azure. Questo servizio è congiuntamente gestito e supportato da Microsoft e Red Hat. Il cluster verrà distribuito nella sottoscrizione di Azure del cliente. Il servizio è attualmente disponibile in anteprima privata e sarà disponibile a livello generale all'inizio del 2019. Altre informazioni saranno disponibili con l'approssimarsi della fase di disponibilità generale.

## <a name="okd-formerly-openshift-origin"></a>OKD (noto in precedenza come OpenShift Origin)

OKD è un progetto upstream [open source](https://www.okd.io/) di OpenShift supportato dalla community. È possibile installare OKD in CentOS o Red Hat Enterprise Linux (RHEL).

## <a name="openshift-dedicated"></a>OpenShift Dedicated

Dedicated è una versione di OpenShift a *singolo tenant* gestita da Red Hat che usa OpenShift Container Platform. Red Hat gestisce tutta l'infrastruttura sottostante (macchine virtuali, cluster OpenShift, rete, archiviazione e così via). Il cluster è specifico di un cliente e viene eseguito in un cloud pubblico (come AWS o Google). Un cluster iniziale include quattro nodi applicazione e tutti i costi sono annuali e con pagamento anticipato.

## <a name="openshift-online"></a>OpenShift Online

Online è una versione di OpenShift *multi-tenant* gestita da Red Hat (tramite Container Platform). Red Hat gestisce tutta l'infrastruttura sottostante (macchine virtuali, cluster OpenShift, rete e archiviazione). 

Con questa versione, il cliente distribuisce i contenitori, ma non ha alcun controllo sugli host in cui i contenitori vengono eseguiti. Poiché si tratta di una versione multi-tenant, i contenitori possono trovarsi nelle stesse macchine virtuali host dei contenitori di altri clienti. Il costo è calcolato per contenitore.

## <a name="next-steps"></a>Passaggi successivi

- [Configurare i prerequisiti comuni per OpenShift in Azure](./openshift-prerequisites.md)
- [Distribuire OpenShift Container Platform in Azure](./openshift-container-platform.md)
- [Distribuire OKD in Azure](./openshift-okd.md)
- [Deploy OpenShift in Azure Stack](./openshift-azure-stack.md) (Distribuire OpenShift in Azure Stack)
- [Attività di post-distribuzione](./openshift-post-deployment.md)
- [Risoluzione dei problemi relativi alla distribuzione di OpenShift](./openshift-troubleshooting.md)
