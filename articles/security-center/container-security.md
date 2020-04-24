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
ms.date: 02/11/2020
ms.author: memildin
ms.openlocfilehash: d46e2a9820ec0c45d197f135428f1ace712b2fb8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80125132"
---
# <a name="container-security-in-security-center"></a>Sicurezza del contenitore nel centro sicurezza

Il Centro sicurezza di Azure è la soluzione nativa di Azure per la sicurezza dei contenitori. Il Centro sicurezza è anche il riquadro ottimale per la sicurezza dei carichi di lavoro cloud, delle macchine virtuali, dei server e dei contenitori.

Questo articolo descrive il modo in cui il Centro sicurezza consente di migliorare, monitorare e gestire la sicurezza dei contenitori e delle relative app. Si apprenderà come il Centro sicurezza contribuisca a questi aspetti principali della sicurezza dei contenitori:

* Gestione vulnerabilità
* Protezione avanzata dell'ambiente del contenitore
* Protezione runtime

[![Scheda sicurezza del contenitore del Centro sicurezza di Azure](media/container-security/container-security-tab.png)](media/container-security/container-security-tab.png#lightbox)

Per istruzioni su come usare queste funzionalità, vedere [monitoraggio della sicurezza dei contenitori](monitor-container-security.md).

## <a name="vulnerability-management---scanning-container-images"></a>Gestione delle vulnerabilità-analisi delle immagini del contenitore
Per monitorare la Container Registry di Azure basata su ARM, assicurarsi di essere al livello standard del Centro sicurezza (vedere i [prezzi](/azure/security-center/security-center-pricing)). Abilitare quindi il bundle facoltativo dei registri contenitori. Quando viene effettuato il push di una nuova immagine, il Centro sicurezza esegue l'analisi dell'immagine usando uno scanner del fornitore leader del settore per l'analisi delle vulnerabilità, Qualys.

Quando vengono rilevati problemi, da Qualys o dal centro sicurezza, si riceverà una notifica nel dashboard del Centro sicurezza. Per ogni vulnerabilità, il Centro sicurezza offre raccomandazioni di utilità pratica, oltre a una classificazione di gravità e indicazioni su come correggere il problema. Per informazioni dettagliate sulle raccomandazioni del Centro sicurezza per i contenitori, vedere l' [elenco di riferimento di raccomandazioni](recommendations-reference.md#recs-containers).

Il Centro sicurezza filtra e classifica i risultati dallo scanner. Quando un'immagine è integra, il Centro sicurezza le contrassegna come tali. Il Centro sicurezza genera raccomandazioni sulla sicurezza solo per le immagini che presentano problemi da risolvere. Inviando notifiche solo quando si verificano problemi, il Centro sicurezza riduce il rischio di avvisi indesiderati informativi.

## <a name="environment-hardening"></a>Protezione avanzata dell'ambiente

### <a name="continuous-monitoring-of-your-docker-configuration"></a>Monitoraggio continuo della configurazione di Docker
Il Centro sicurezza di Azure identifica i contenitori non gestiti ospitati in macchine virtuali IaaS Linux o altri computer Linux che eseguono contenitori docker. Il Centro sicurezza valuta continuamente le configurazioni di questi contenitori. Viene quindi confrontato con il [benchmark Docker di Center for Internet Security (CIS)](https://www.cisecurity.org/benchmark/docker/).

Il Centro sicurezza include l'intero set di regole del benchmark Docker di CIS e avvisa l'utente se i contenitori non soddisfano i controlli. Quando rileva configurazioni non configurate, il Centro sicurezza genera raccomandazioni sulla sicurezza. Utilizzare la **pagina raccomandazioni** per visualizzare le raccomandazioni e correggere i problemi. Verranno inoltre visualizzati i consigli nella scheda **contenitori** che Visualizza tutte le macchine virtuali distribuite con Docker. 

Per informazioni dettagliate sulle raccomandazioni pertinenti del Centro sicurezza che potrebbero essere visualizzate per questa funzionalità, vedere la sezione relativa ai [contenitori](recommendations-reference.md#recs-containers) della tabella di riferimento per le raccomandazioni.

Quando si esplorano i problemi di sicurezza di una VM, il Centro sicurezza fornisce informazioni aggiuntive sui contenitori nel computer. Tali informazioni includono la versione Docker e il numero di immagini in esecuzione nell'host. 

>[!NOTE]
> Questi controlli di benchmark di CIS non vengono eseguiti nelle macchine virtuali gestite da AKS o dalle macchine virtuali gestite da databricks.

### <a name="continuous-monitoring-of-your-kubernetes-clusters"></a>Monitoraggio continuo dei cluster Kubernetes
Il Centro sicurezza interagisce con il servizio Azure Kubernetes (AKS), il servizio di orchestrazione dei contenitori gestiti di Microsoft per lo sviluppo, la distribuzione e la gestione di applicazioni in contenitori.

AKS fornisce controlli di sicurezza e visibilità sul comportamento di sicurezza dei cluster. Il Centro sicurezza usa queste funzionalità per:
* Monitorare costantemente la configurazione dei cluster AKS
* Genera raccomandazioni sulla sicurezza allineate con gli standard del settore

Per informazioni dettagliate sulle raccomandazioni pertinenti del Centro sicurezza che potrebbero essere visualizzate per questa funzionalità, vedere la sezione relativa ai [contenitori](recommendations-reference.md#recs-containers) della tabella di riferimento per le raccomandazioni.

## <a name="run-time-protection---real-time-threat-detection"></a>Protezione in fase di esecuzione-rilevamento delle minacce in tempo reale

Il Centro sicurezza fornisce il rilevamento delle minacce in tempo reale per gli ambienti in contenitori e genera avvisi per le attività sospette. È possibile usare queste informazioni per risolvere rapidamente i problemi di sicurezza e migliorare la sicurezza dei contenitori.

Le minacce vengono rilevate a livello di host e del cluster AKS. Per informazioni dettagliate, vedere [rilevamento delle minacce per i contenitori di Azure](threat-protection.md#azure-containers).


## <a name="container-security-faq"></a>Domande frequenti sulla sicurezza del contenitore

### <a name="what-types-of-images-can-azure-security-center-scan"></a>Quali tipi di immagini possono essere analizzati dal centro sicurezza di Azure?
Il Centro sicurezza analizza le immagini basate sul sistema operativo Linux che forniscono l'accesso alla Shell. 

Lo scanner Qualys non supporta immagini estremamente minimaliste come immagini [Scratch di Docker](https://hub.docker.com/_/scratch/) o immagini "senza distribuzione" che contengono solo l'applicazione e le relative dipendenze di runtime senza gestione pacchetti, Shell o sistema operativo.

### <a name="how-does-azure-security-center-scan-an-image"></a>In che modo il Centro sicurezza di Azure esegue l'analisi di un'immagine?
Viene eseguito il pull dell'immagine dal registro di sistema. Viene quindi eseguito in una sandbox isolata con lo scanner Qualys che estrae un elenco di vulnerabilità note.

Il Centro sicurezza filtra e classifica i risultati dallo scanner. Quando un'immagine è integra, il Centro sicurezza le contrassegna come tali. Il Centro sicurezza genera raccomandazioni sulla sicurezza solo per le immagini che presentano problemi da risolvere. Inviando notifiche solo quando si verificano problemi, il Centro sicurezza riduce il rischio di avvisi indesiderati informativi.

### <a name="how-often-does-azure-security-center-scan-my-images"></a>Con quale frequenza il Centro sicurezza di Azure analizza le immagini?
Le analisi delle immagini vengono attivate a ogni push.

### <a name="can-i-get-the-scan-results-via-rest-api"></a>È possibile ottenere i risultati dell'analisi tramite l'API REST?
Sì. I risultati si trovano nell' [API REST delle sottovalutazioni](/rest/api/securitycenter/subassessments/list/). È anche possibile usare Azure Resource Graph (ARG), l'API simile a kusto per tutte le risorse: una query può recuperare un'analisi specifica.
 

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulla sicurezza del contenitore nel centro sicurezza di Azure, vedere gli articoli correlati:

* Per visualizzare il comportamento di sicurezza delle risorse correlate al contenitore, vedere la sezione contenitori di [proteggere i computer e le applicazioni](security-center-virtual-machine-protection.md#containers).

* Dettagli dell' [integrazione con il servizio Azure Kubernetes](azure-kubernetes-service-integration.md)

* Dettagli dell' [integrazione con container Registry di Azure](azure-container-registry-integration.md)