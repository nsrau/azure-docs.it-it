---
title: Sicurezza dei contenitori nel Centro sicurezza di Azure | Microsoft Docs
description: Informazioni sulle funzionalità di sicurezza dei contenitori nel Centro sicurezza di Azure.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/22/2020
ms.author: memildin
ms.openlocfilehash: 6b57428aeba702dc8cf06ec4ae7984854a94ac7a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91449172"
---
# <a name="container-security-in-security-center"></a>Sicurezza dei contenitori nel Centro sicurezza

Il Centro sicurezza di Azure è la soluzione nativa di Azure per la protezione dei contenitori.

Il Centro sicurezza consente di proteggere i tipi di risorse contenitore seguenti:

| Tipo di risorsa | Protezioni offerte dal Centro sicurezza |
|:--------------------:|-----------|
| ![Servizio Kubernetes](./media/security-center-virtual-machine-recommendations/icon-kubernetes-service-rec.png)<br>**Cluster del servizio Azure Kubernetes** | - Valutazione continua delle configurazioni dei cluster del servizio Azure Kubernetes per fornire visibilità sugli errori di configurazione e linee guida per la risoluzione di eventuali problemi individuati.<br>[Altre informazioni sulla protezione avanzata dell'ambiente tramite le raccomandazioni sulla sicurezza](#environment-hardening).<br><br>- Protezione dalle minacce per i cluster del servizio Azure Kubernetes e i nodi Linux. Lo strumento facoltativo [Azure Defender per Kubernetes](defender-for-kubernetes-introduction.md) fornisce avvisi relativi alle attività sospette.<br>[Altre informazioni sulla protezione in fase di esecuzione per i nodi e i cluster del servizio Azure Kubernetes](#run-time-protection-for-aks-nodes-and-clusters).|
| ![Host contenitore](./media/security-center-virtual-machine-recommendations/icon-container-host-rec.png)<br>**Host contenitore**<br>(VM che eseguono Docker) | - Valutazione continua delle configurazioni Docker per fornire visibilità sugli errori di configurazione e linee guida per la risoluzione di eventuali problemi individuati con il servizio facoltativo [Azure Defender per server](defender-for-servers-introduction.md).<br>[Altre informazioni sulla protezione avanzata dell'ambiente tramite le raccomandazioni sulla sicurezza](#environment-hardening).|
| ![Registro contenitori](./media/security-center-virtual-machine-recommendations/icon-container-registry-rec.png)<br>**Registri del servizio Registro Azure Container** | - Strumenti di valutazione e gestione delle vulnerabilità per le immagini nei registri del servizio Registro Azure Container basati su Azure Resource Manager con il servizio facoltativo [Azure Defender per registri contenitori](defender-for-container-registries-introduction.md).<br>[Altre informazioni sull'analisi delle immagini del contenitore per rilevare le vulnerabilità](#vulnerability-management---scanning-container-images). |
|||

Questo articolo illustra come usare il Centro sicurezza, insieme ai piani facoltativi di Azure Defender per registri contenitori, server e Kubernetes, per migliorare, monitorare e mantenere la sicurezza dei contenitori e delle relative app.

Si apprenderà il contributo offerto dal Centro sicurezza a questi aspetti principali della sicurezza dei contenitori:

- [Gestione delle vulnerabilità - analisi delle immagini dei contenitori](#vulnerability-management---scanning-container-images)
- [Protezione avanzata dell'ambiente](#environment-hardening)
- [Protezione in fase di esecuzione per i nodi e i cluster del servizio Azure Kubernetes](#run-time-protection-for-aks-nodes-and-clusters)

Lo screenshot seguente mostra la pagina dell'inventario delle risorse e i vari tipi di risorse contenitore protetti dal Centro sicurezza.

:::image type="content" source="./media/container-security/container-security-tab.png" alt-text="Risorse correlate ai contenitori nella pagina dell'inventario delle risorse del Centro sicurezza" lightbox="./media/container-security/container-security-tab.png":::

## <a name="vulnerability-management---scanning-container-images"></a>Gestione delle vulnerabilità - analisi delle immagini dei contenitori

Per monitorare le immagini nei registri contenitori di Azure basati su Azure Resource Manager, abilitare [Azure Defender per registri contenitori](defender-for-container-registries-introduction.md). Il Centro sicurezza analizza le immagini che sono state estratte negli ultimi 30 giorni o che sono state inserite nel registro o importate. La funzionalità di analisi integrata è fornita da Qualys, leader nel settore dell'analisi delle vulnerabilità.

Quando viene rilevato un problema, da Qualys o dal Centro sicurezza, viene visualizzata una notifica nel [dashboard di Azure Defender](azure-defender-dashboard.md). Per ogni vulnerabilità il Centro sicurezza offre raccomandazioni pratiche, una classificazione della gravità e istruzioni per la risoluzione del problema. Per informazioni dettagliate sulle raccomandazioni del Centro sicurezza per i contenitori, vedere l'[elenco di riferimento delle raccomandazioni](recommendations-reference.md#recs-containers).

Il Centro sicurezza filtra e classifica i risultati restituiti dalla funzionalità di analisi. Quando un'immagine è integra, il Centro sicurezza la contrassegna come tale. Il Centro sicurezza genera raccomandazioni sulla sicurezza solo per le immagini che presentano problemi da risolvere. Inviando notifiche solo in caso di problemi individuati, il Centro sicurezza riduce il rischio di avvisi informativi indesiderati.

## <a name="environment-hardening"></a>Protezione avanzata dell'ambiente

### <a name="continuous-monitoring-of-your-docker-configuration"></a>Monitoraggio continuo della configurazione di Docker

Il Centro sicurezza di Azure identifica i contenitori non gestiti ospitati in VM Linux IaaS o in altre VM Linux che eseguono contenitori Docker. Il Centro sicurezza valuta continuamente le configurazioni di questi contenitori. Le confronta quindi con [CIS Docker Benchmark](https://www.cisecurity.org/benchmark/docker/).

Il Centro sicurezza include l'intero set di regole di CIS Docker Benchmark e invia avvisi se i contenitori non soddisfano i controlli. Quando rileva errori di configurazione, il Centro sicurezza genera raccomandazioni sulla sicurezza. Usare la **pagina delle raccomandazioni** del Centro sicurezza per visualizzare le raccomandazioni e correggere i problemi. I controlli di CIS Benchmark non vengono eseguiti nelle istanze gestite dal servizio Azure Kubernetes o nelle macchine virtuali gestite da Databricks.

Per informazioni dettagliate sulle raccomandazioni pertinenti del Centro sicurezza che potrebbero essere visualizzate per questa funzionalità, vedere la sezione [Consigli per i contenitori](recommendations-reference.md#recs-containers) della tabella di riferimento per le raccomandazioni.

Quando si esplorano i problemi di sicurezza di una VM, il Centro sicurezza fornisce informazioni aggiuntive sui contenitori nella macchina virtuale. Queste informazioni includono la versione di Docker e il numero di immagini in esecuzione nell'host. 

Per monitorare i contenitori non gestiti ospitati in macchine virtuali IaaS Linux, abilitare il servizio facoltativo [Azure Defender per server](defender-for-servers-introduction.md).


### <a name="continuous-monitoring-of-your-kubernetes-clusters"></a>Monitoraggio continuo dei cluster Kubernetes
Il Centro sicurezza interagisce con Azure Kubernetes, il servizio Microsoft di orchestrazione dei contenitori gestiti per lo sviluppo, la distribuzione e la gestione di applicazioni in contenitori.

Questo servizio fornisce controlli di sicurezza e visibilità sul comportamento di sicurezza dei cluster. Il Centro sicurezza usa queste funzionalità per:
* Monitorare in modo costante la configurazione dei cluster del servizio Azure Kubernetes
* Generare raccomandazioni sulla sicurezza in linea con gli standard del settore

Per informazioni dettagliate sulle raccomandazioni pertinenti del Centro sicurezza che potrebbero essere visualizzate per questa funzionalità, vedere la sezione [Consigli per i contenitori](recommendations-reference.md#recs-containers) della tabella di riferimento per le raccomandazioni.

###  <a name="workload-protection-best-practices-using-kubernetes-admission-control"></a>Procedure consigliate per la protezione dei carichi di lavoro con il controllo ammissione di Kubernetes

Installare il **componente aggiuntivo Criteri di Azure per Kubernetes** per ottenere una serie di raccomandazioni per proteggere i carichi di lavoro dei contenitori di Kubernetes.

Come spiegato in questa [pagina su Criteri di Azure per Kubernetes](../governance/policy/concepts/policy-for-kubernetes.md), il componente aggiuntivo estende il webhook del controller di ammissione open source [Gatekeeper v3](https://github.com/open-policy-agent/gatekeeper) per [Open Policy Agent](https://www.openpolicyagent.org/). I controller di ammissione Kubernetes sono plug-in che stabiliscono la modalità di utilizzo dei cluster. Il componente aggiuntivo viene registrato come webhook nel controllo di ammissione Kubernetes e consente di applicare tutele e misure di sicurezza su larga scala per i cluster in modo centralizzato e coerente. 

Dopo l'installazione del componente aggiuntivo nel cluster del servizio Azure Kubernetes, ogni richiesta al server API Kubernetes sarà monitorata rispetto al set predefinito di procedure consigliate prima del salvataggio permanente nel cluster. È quindi possibile configurare l'**applicazione** delle procedure consigliate e renderle obbligatorie per i carichi di lavoro futuri. 

È ad esempio possibile imporre il divieto di creazione di contenitori con privilegi, pertanto eventuali richieste future di creazione di tali contenitori verranno bloccate.

Per altre informazioni, vedere [Proteggere i carichi di lavoro Kubernetes](kubernetes-workload-protections.md).


## <a name="run-time-protection-for-aks-nodes-and-clusters"></a>Protezione in fase di esecuzione per i nodi e i cluster del servizio Azure Kubernetes

[!INCLUDE [AKS in ASC threat protection](../../includes/security-center-azure-kubernetes-threat-protection.md)]



## <a name="next-steps"></a>Passaggi successivi

In questa panoramica sono stati illustrati gli elementi principali della sicurezza dei contenitori nel Centro sicurezza di Azure. Per accedere a materiale correlato, vedere:

- [Introduzione ad Azure Defender per Kubernetes](defender-for-kubernetes-introduction.md)
- [Introduzione ad Azure Defender per registri contenitori](defender-for-container-registries-introduction.md)