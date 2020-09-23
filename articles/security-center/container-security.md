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
ms.date: 09/22/2020
ms.author: memildin
ms.openlocfilehash: cbea99b08ed0cbcc1125d15c78119a509b6dcc48
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90977264"
---
# <a name="container-security-in-security-center"></a>Sicurezza del contenitore nel centro sicurezza

Il Centro sicurezza di Azure è la soluzione nativa di Azure per la protezione dei contenitori.

Il Centro sicurezza può proteggere i tipi di risorse contenitore seguenti:

| Tipo di risorsa | Protezioni offerte dal centro sicurezza |
|:--------------------:|-----------|
| ![Servizio Kubernetes](./media/security-center-virtual-machine-recommendations/icon-kubernetes-service-rec.png)<br>**Cluster Azure Kubernetes Service (AKS)** | -Valutazione continua delle configurazioni dei cluster AKS per fornire visibilità a errori di configurazione e linee guida per la risoluzione di eventuali problemi individuati.<br>[Altre informazioni sulla protezione avanzata dell'ambiente tramite le raccomandazioni sulla sicurezza](#environment-hardening).<br><br>-Protezione dalle minacce per i cluster AKS e i nodi Linux. Gli avvisi per le attività sospette vengono forniti da  [Azure Defender facoltativo per Kubernetes](defender-for-kubernetes-introduction.md).<br>[Altre informazioni sulla protezione in fase di esecuzione per i nodi e i cluster AKS](#run-time-protection-for-aks-nodes-and-clusters).|
| ![Host contenitore](./media/security-center-virtual-machine-recommendations/icon-container-host-rec.png)<br>**Host contenitore**<br>(VM che eseguono Docker) | -Valutazione continua delle configurazioni di Docker per fornire visibilità a errori di configurazione e linee guida per la risoluzione di eventuali problemi individuati con  [Azure Defender facoltativo per i server](defender-for-servers-introduction.md).<br>[Altre informazioni sulla protezione avanzata dell'ambiente tramite le raccomandazioni sulla sicurezza](#environment-hardening).|
| ![Registro contenitori](./media/security-center-virtual-machine-recommendations/icon-container-registry-rec.png)<br>**Registri di Azure Container Registry (ACR)** | -Strumenti di valutazione e gestione della vulnerabilità per le immagini nei registri ACR basati su Azure Resource Manager con il [Azure Defender facoltativo per i registri dei contenitori](defender-for-container-registries-introduction.md).<br>[Altre informazioni sull'analisi delle immagini del contenitore per le vulnerabilità](#vulnerability-management---scanning-container-images). |
|||

Questo articolo descrive come usare il Centro sicurezza, insieme ai piani facoltativi di Azure Defender per i registri dei contenitori, le versioni di Kubernetes e le procedure per migliorare, monitorare e mantenere la sicurezza dei contenitori e delle relative app.

Si apprenderà come il Centro sicurezza contribuisca a questi aspetti principali della sicurezza dei contenitori:

- [Gestione delle vulnerabilità-analisi delle immagini del contenitore](#vulnerability-management---scanning-container-images)
- [Protezione avanzata dell'ambiente](#environment-hardening)
- [Protezione della fase di esecuzione per i nodi e i cluster AKS](#run-time-protection-for-aks-nodes-and-clusters)

Lo screenshot seguente mostra la pagina inventario asset e i vari tipi di risorse contenitore protetti dal centro sicurezza.

:::image type="content" source="./media/container-security/container-security-tab.png" alt-text="Risorse correlate ai contenitori nella pagina di inventario degli asset del Centro sicurezza" lightbox="./media/container-security/container-security-tab.png":::

## <a name="vulnerability-management---scanning-container-images"></a>Gestione delle vulnerabilità-analisi delle immagini del contenitore

Per monitorare le immagini nei registri contenitori di Azure basati su Azure Resource Manager, abilitare [Azure Defender per i registri di contenitori](defender-for-container-registries-introduction.md). Il Centro sicurezza esegue l'analisi di tutte le immagini estratte negli ultimi 30 giorni, effettuando il push al registro di sistema o importato. Lo scanner integrato viene fornito dal fornitore leader del settore per l'analisi delle vulnerabilità, Qualys.

Quando vengono rilevati problemi, da Qualys o dal centro sicurezza, si riceverà una notifica nel [dashboard di Azure Defender](azure-defender-dashboard.md). Per ogni vulnerabilità, il Centro sicurezza offre raccomandazioni di utilità pratica, oltre a una classificazione di gravità e indicazioni su come correggere il problema. Per informazioni dettagliate sulle raccomandazioni del Centro sicurezza per i contenitori, vedere l' [elenco di riferimento di raccomandazioni](recommendations-reference.md#recs-containers).

Il Centro sicurezza filtra e classifica i risultati dallo scanner. Quando un'immagine è integra, il Centro sicurezza le contrassegna come tali. Il Centro sicurezza genera raccomandazioni sulla sicurezza solo per le immagini che presentano problemi da risolvere. Inviando notifiche solo quando si verificano problemi, il Centro sicurezza riduce il rischio di avvisi indesiderati informativi.

## <a name="environment-hardening"></a>Protezione avanzata dell'ambiente

### <a name="continuous-monitoring-of-your-docker-configuration"></a>Monitoraggio continuo della configurazione di Docker

Il Centro sicurezza di Azure identifica i contenitori non gestiti ospitati in macchine virtuali IaaS Linux o altri computer Linux che eseguono contenitori docker. Il Centro sicurezza valuta continuamente le configurazioni di questi contenitori. Viene quindi confrontato con il [benchmark Docker di Center for Internet Security (CIS)](https://www.cisecurity.org/benchmark/docker/).

Il Centro sicurezza include l'intero set di regole del benchmark Docker di CIS e avvisa l'utente se i contenitori non soddisfano i controlli. Quando rileva configurazioni non configurate, il Centro sicurezza genera raccomandazioni sulla sicurezza. Utilizzare la **pagina raccomandazioni** per visualizzare le raccomandazioni e correggere i problemi. Verranno inoltre visualizzati i consigli nella scheda **contenitori** che Visualizza tutte le macchine virtuali distribuite con Docker. I controlli del benchmark CIS non vengono eseguiti nelle macchine virtuali gestite da AKS o dalle macchine virtuali gestite da databricks.

Per informazioni dettagliate sulle raccomandazioni pertinenti del Centro sicurezza che potrebbero essere visualizzate per questa funzionalità, vedere la sezione relativa ai [contenitori](recommendations-reference.md#recs-containers) della tabella di riferimento per le raccomandazioni.

Quando si esplorano i problemi di sicurezza di una VM, il Centro sicurezza fornisce informazioni aggiuntive sui contenitori nel computer. Tali informazioni includono la versione Docker e il numero di immagini in esecuzione nell'host. 

Per monitorare i contenitori non gestiti ospitati in macchine virtuali IaaS Linux, abilitare [Azure Defender facoltativo per i server](defender-for-servers-introduction.md).


### <a name="continuous-monitoring-of-your-kubernetes-clusters"></a>Monitoraggio continuo dei cluster Kubernetes
Il Centro sicurezza interagisce con il servizio Azure Kubernetes (AKS), il servizio di orchestrazione dei contenitori gestiti di Microsoft per lo sviluppo, la distribuzione e la gestione di applicazioni in contenitori.

AKS fornisce controlli di sicurezza e visibilità sul comportamento di sicurezza dei cluster. Il Centro sicurezza usa queste funzionalità per:
* Monitorare costantemente la configurazione dei cluster AKS
* Genera raccomandazioni sulla sicurezza allineate con gli standard del settore

Per informazioni dettagliate sulle raccomandazioni pertinenti del Centro sicurezza che potrebbero essere visualizzate per questa funzionalità, vedere la sezione relativa ai [contenitori](recommendations-reference.md#recs-containers) della tabella di riferimento per le raccomandazioni.

###  <a name="workload-protection-best-practices-using-kubernetes-admission-control"></a>Procedure consigliate per la protezione del carico di lavoro usando il controllo di ammissione Kubernetes

Installare il  **componente aggiuntivo criteri di Azure per Kubernetes** per ottenere un bundle di consigli per la protezione dei carichi di lavoro dei contenitori di Kubernetes.

Come illustrato in [Questa pagina di criteri di Azure per Kubernetes](../governance/policy/concepts/policy-for-kubernetes.md), il componente aggiuntivo estende il webhook del controller di ingresso [Gatekeeper V3](https://github.com/open-policy-agent/gatekeeper)Open Source   per l' [agente criteri di apertura](https://www.openpolicyagent.org/). I controller di ammissione Kubernetes sono plug-in che applicano come vengono usati i cluster. Il componente aggiuntivo viene registrato come un webhook per il controllo di ammissione Kubernetes e consente di applicare le misure e le misure di sicurezza sui cluster in modo centralizzato e coerente. 

Dopo aver installato il componente aggiuntivo nel cluster AKS, ogni richiesta al server API Kubernetes verrà monitorata rispetto al set predefinito di procedure consigliate prima di essere resa permanente nel cluster. È quindi possibile configurare per **applicare** le procedure consigliate e incaricarle per carichi di lavoro futuri. 

Ad esempio, è possibile imporre che i contenitori con privilegi non debbano essere creati e tutte le richieste future verranno bloccate.

Per altre informazioni, vedere [proteggere i carichi di lavoro Kubernetes](kubernetes-workload-protections.md).


## <a name="run-time-protection-for-aks-nodes-and-clusters"></a>Protezione della fase di esecuzione per i nodi e i cluster AKS

[!INCLUDE [AKS in ASC threat protection](../../includes/security-center-azure-kubernetes-threat-protection.md)]



## <a name="next-steps"></a>Passaggi successivi

In questa panoramica sono stati appresi gli elementi principali della sicurezza dei contenitori nel centro sicurezza di Azure. Per materiale correlato, vedere:

- [Introduzione ad Azure Defender per Kubernetes](defender-for-kubernetes-introduction.md)
- [Introduzione ad Azure Defender per registri contenitori](defender-for-container-registries-introduction.md)