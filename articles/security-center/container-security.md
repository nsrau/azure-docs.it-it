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
ms.date: 06/28/2020
ms.author: memildin
ms.openlocfilehash: c01ed6dbbd6e1f7febfb99df11d2ee67cb1e5465
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85800605"
---
# <a name="container-security-in-security-center"></a>Sicurezza del contenitore nel centro sicurezza

Il Centro sicurezza di Azure è la soluzione nativa di Azure per la protezione dei contenitori. Il Centro sicurezza può proteggere i tipi di risorse contenitore seguenti:



|Risorsa |Nome  |Dettagli  |
|:---------:|---------|---------|
|![Host contenitore](./media/security-center-virtual-machine-recommendations/icon-container-host-rec.png)|Host del contenitore (macchine virtuali che eseguono Docker)|Il Centro sicurezza analizza le configurazioni di Docker e mostra le configurazioni errate, fornendo un elenco di tutte le regole non riuscite che sono state valutate. Visualizza inoltre linee guida per consentire di risolvere rapidamente questi problemi e risparmiare tempo. Il Centro sicurezza valuta costantemente le configurazioni di Docker e ne visualizza lo stato più recente.|
|![Servizio Kubernetes](./media/security-center-virtual-machine-recommendations/icon-kubernetes-service-rec.png)|Cluster Azure Kubernetes Service (AKS)|Ottieni una maggiore visibilità sui nodi AKS, il traffico cloud e i controlli di sicurezza con il [bundle AKS facoltativo del Centro sicurezza](azure-kubernetes-service-integration.md) per gli utenti di livello standard.|
|![Registro contenitori](./media/security-center-virtual-machine-recommendations/icon-container-registry-rec.png)|Registri di Azure Container Registry (ACR)|Ottieni una maggiore visibilità sulle vulnerabilità delle immagini nei registri ACR basati su ARM con il [bundle ACR facoltativo del Centro sicurezza](azure-kubernetes-service-integration.md) per gli utenti di livello standard.|
||||


Questo articolo descrive come usare questi bundle per migliorare, monitorare e mantenere la sicurezza dei contenitori e delle relative app. Si apprenderà come il Centro sicurezza contribuisca a questi aspetti principali della sicurezza dei contenitori:

- [Gestione delle vulnerabilità-analisi delle immagini del contenitore](#vulnerability-management---scanning-container-images)
- [Protezione avanzata dell'ambiente-monitoraggio continuo della configurazione Docker e dei cluster Kubernetes](#environment-hardening)
- [Protezione in fase di esecuzione-rilevamento delle minacce in tempo reale](#run-time-protection---real-time-threat-detection)

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

[!INCLUDE [AKS in ASC threat protection](../../includes/security-center-azure-kubernetes-threat-protection.md)]




## <a name="next-steps"></a>Passaggi successivi

In questa panoramica sono stati appresi gli elementi principali della sicurezza dei contenitori nel centro sicurezza di Azure. Continuare a [monitorare la sicurezza dei contenitori](monitor-container-security.md).
> [!div class="nextstepaction"]
> [Monitoraggio della sicurezza dei contenitori](monitor-container-security.md)