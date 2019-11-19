---
title: Sicurezza del contenitore nel centro sicurezza di Azure | Microsoft Docs
description: Informazioni sulle funzionalità di sicurezza del contenitore del Centro sicurezza di Azure.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: fb8f5f2adf497a12b4ed54b381a2dbf85e160b8e
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/15/2019
ms.locfileid: "74123960"
---
# <a name="container-security-in-security-center"></a>Sicurezza del contenitore nel centro sicurezza

Il Centro sicurezza di Azure è la soluzione nativa di Azure per la sicurezza dei contenitori. Il Centro sicurezza è anche il riquadro ottimale per la sicurezza dei carichi di lavoro cloud, delle macchine virtuali, dei server e dei contenitori.

Questo articolo descrive come migliorare, monitorare e gestire la sicurezza dei contenitori e delle relative app. Si apprenderà come il Centro sicurezza contribuisca a questi aspetti principali della sicurezza dei contenitori:

* Gestione vulnerabilità
* Protezione avanzata dell'ambiente del contenitore
* Protezione runtime

[![scheda sicurezza contenitore del Centro sicurezza di Azure](media/container-security/container-security-tab.png)](media/container-security/container-security-tab.png#lightbox)

## <a name="vulnerability-management---scanning-container-images-preview"></a>Gestione delle vulnerabilità-analisi delle immagini del contenitore (anteprima)
Per monitorare la Container Registry di Azure, connetterla al centro sicurezza. Quando viene effettuato il push di una nuova immagine, il Centro sicurezza esegue l'analisi dell'immagine usando uno scanner del fornitore leader del settore per l'analisi delle vulnerabilità, Qualys.

Quando vengono rilevati problemi, da Qualys o dal centro sicurezza, si riceverà una notifica nel dashboard del Centro sicurezza. Per ogni vulnerabilità, il Centro sicurezza offre raccomandazioni di utilità pratica, oltre a una classificazione di gravità e indicazioni su come correggere il problema. Per informazioni dettagliate sulle raccomandazioni del Centro sicurezza, vedere l'elenco di raccomandazioni nella [protezione delle macchine virtuali](security-center-virtual-machine-protection.md##compute-and-app-recs).

## <a name="environment-hardening"></a>Protezione avanzata dell'ambiente

### <a name="continuous-monitoring-of-your-docker-configuration"></a>Monitoraggio continuo della configurazione di Docker
Il Centro sicurezza di Azure identifica i contenitori non gestiti ospitati in macchine virtuali IaaS Linux o altri computer Linux che eseguono contenitori docker. Il Centro sicurezza valuta continuamente le configurazioni di questi contenitori. Viene quindi confrontato con il [benchmark Docker di Center for Internet Security (CIS)](https://www.cisecurity.org/benchmark/docker/). 

Il Centro sicurezza include l'intero set di regole del benchmark Docker di CIS e avvisa l'utente se i contenitori non soddisfano i controlli. Quando rileva configurazioni non configurate, il Centro sicurezza genera raccomandazioni sulla sicurezza. Utilizzare la **pagina raccomandazioni** per visualizzare le raccomandazioni e correggere i problemi. Verranno inoltre visualizzati i consigli nella scheda **contenitori** che Visualizza tutte le macchine virtuali distribuite con Docker. Quando si esplorano i problemi di sicurezza in una macchina virtuale, il Centro sicurezza fornisce informazioni aggiuntive sui contenitori nel computer. Tali informazioni includono la versione Docker e il numero di immagini in esecuzione nell'host. Per informazioni dettagliate sulle raccomandazioni, vedere [qui](https://docs.microsoft.com/azure/security-center/security-center-virtual-machine-protection). 

>[!NOTE]
> Questi controlli di benchmark di CIS non vengono eseguiti nelle macchine virtuali gestite da AKS o dalle macchine virtuali gestite da databricks.

### <a name="continuous-monitoring-of-your-kubernetes-clusters-preview"></a>Monitoraggio continuo dei cluster Kubernetes (anteprima)
Il Centro sicurezza interagisce con il servizio Azure Kubernetes (AKS), il servizio di orchestrazione dei contenitori gestiti di Microsoft per lo sviluppo, la distribuzione e la gestione di applicazioni in contenitori.

AKS fornisce controlli di sicurezza e visibilità sul comportamento di sicurezza dei cluster. Il Centro sicurezza usa queste funzionalità per:
* Monitorare costantemente la configurazione dei cluster AKS
* Genera raccomandazioni sulla sicurezza allineate con gli standard del settore

Per informazioni dettagliate sulle raccomandazioni del Centro sicurezza, vedere [protezione delle macchine virtuali](security-center-virtual-machine-protection.md).

## <a name="run-time-protection---real-time-threat-detection"></a>Protezione in fase di esecuzione-rilevamento delle minacce in tempo reale 

Il Centro sicurezza fornisce il rilevamento delle minacce in tempo reale per gli ambienti in contenitori e genera avvisi per le attività sospette. È possibile usare queste informazioni per risolvere rapidamente i problemi di sicurezza e migliorare la sicurezza dei contenitori.

Le minacce vengono rilevate a livello di host e del cluster AKS. Per informazioni complete, vedere [servizio contenitore di Azure](https://docs.microsoft.com/azure/security-center/security-center-alerts-compute#azure-containers-).


## <a name="to-view-the-security-posture-of-your-container-related-resources"></a>Per visualizzare il comportamento di sicurezza delle risorse correlate al contenitore
1.  Aprire la pagina di **calcolo delle app &** del Centro sicurezza.
2.  Fare clic sulla scheda **contenitori** . Viene visualizzata la posizione dei cluster AKS, dei registri ACR e delle macchine virtuali che eseguono docker.

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sulla sicurezza del contenitore nel centro sicurezza di Azure, vedere:
* Dettagli dell' [integrazione con il servizio Azure Kubernetes](azure-kubernetes-service-integration.md)

* Dettagli dell' [integrazione con container Registry di Azure](azure-container-registry-integration.md)