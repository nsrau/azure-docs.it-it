---
title: Sicurezza dei contenitori nel Centro sicurezza di Azure Documenti Microsoft
description: Informazioni sulle funzionalità di sicurezza dei contenitori del Centro sicurezza di Azure.Learn about Azure Security Center's container security features.
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
# <a name="container-security-in-security-center"></a>Container security in Security Center

Il Centro sicurezza di Azure è la soluzione nativa di Azure per la sicurezza dei contenitori. Il Centro sicurezza è anche il singolo riquadro ottimale dell'esperienza di vetro per la sicurezza di carichi di lavoro cloud, macchine virtuali, server e contenitori.

Questo articolo descrive in che modo il Centro sicurezza consente di migliorare, monitorare e gestire la sicurezza dei contenitori e delle relative app. Si apprenderà in che modo il Centro sicurezza aiuta con questi aspetti fondamentali della sicurezza dei contenitori:You'll learn how Security Center helps with these core aspects of container security:

* Gestione vulnerabilità
* Indurimento dell'ambiente del contenitore
* Protezione runtime

[![Scheda Sicurezza contenitore del Centro sicurezza di AzureAzure Security Center's container security tab](media/container-security/container-security-tab.png)](media/container-security/container-security-tab.png#lightbox)

Per istruzioni su come utilizzare queste funzionalità, vedere [Monitoraggio della sicurezza dei contenitori.](monitor-container-security.md)

## <a name="vulnerability-management---scanning-container-images"></a>Gestione delle vulnerabilità - scansione delle immagini dei contenitori
Per monitorare il Registro di sistema del contenitore di Azure basato su ARM, assicurarsi di essere nel livello standard del Centro sicurezza (vedere [prezzi](/azure/security-center/security-center-pricing)). Quindi, abilitare il bundle facoltativo Registri contenitori. Quando viene inserita una nuova immagine, il Centro sicurezza esegue la scansione dell'immagine utilizzando uno scanner del fornitore leader del settore di scansione delle vulnerabilità, Qualys.

Quando vengono rilevati problemi, da parte di Qualys o centro sicurezza, si riceverà una notifica nel dashboard del Centro sicurezza. Per ogni vulnerabilità, il Centro sicurezza fornisce consigli fruibili, oltre a una classificazione di gravità e indicazioni su come risolvere il problema. Per informazioni dettagliate sui consigli del Centro sicurezza per i contenitori, vedere [l'elenco di riferimento dei suggerimenti.](recommendations-reference.md#recs-containers)

Il Centro sicurezza filtra e classifica i risultati dallo scanner. Quando un'immagine è integra, il Centro sicurezza la contrassegna come tale. Il Centro sicurezza genera consigli di sicurezza solo per le immagini con problemi da risolvere. Notificando solo in caso di problemi, il Centro sicurezza riduce il rischio di avvisi informativi indesiderati.

## <a name="environment-hardening"></a>Indurimento dell'ambiente

### <a name="continuous-monitoring-of-your-docker-configuration"></a>Monitoraggio continuo della configurazione di Docker
Il Centro sicurezza di Azure identifica i contenitori non gestiti ospitati nelle macchine virtuali Linux di IaaS o in altre macchine Linux che eseguono contenitori Docker.Azure Security Center identifies unmanaged containers hosted on IaaS Linux VMs, or other Linux machines running Docker containers. Il Centro sicurezza valuta continuamente le configurazioni di questi contenitori. Quindi li confronta con il [Center for Internet Security (CIS) Docker Benchmark](https://www.cisecurity.org/benchmark/docker/).

Il Centro sicurezza include l'intero set di regole di CIS Docker Benchmark e avvisa l'utente se i contenitori non soddisfano nessuno dei controlli. Quando vengono rilevate configurazioni errate, il Centro sicurezza genera suggerimenti per la sicurezza. Utilizzare la **pagina dei suggerimenti** per visualizzare i suggerimenti e risolvere i problemi. Verranno inoltre visualizzati i suggerimenti nella scheda **Contenitori** in cui vengono visualizzate tutte le macchine virtuali distribuite con Docker.You'll also see the recommendations on the Containers tab that displays all virtual machines deployed with Docker. 

Per informazioni dettagliate sui consigli del Centro sicurezza pertinenti che potrebbero essere visualizzati per questa funzionalità, vedere la sezione relativa al [contenitore](recommendations-reference.md#recs-containers) della tabella di riferimento dei suggerimenti.

Quando si esplorano i problemi di sicurezza di una macchina virtuale, il Centro sicurezza fornisce informazioni aggiuntive sui contenitori nel computer. Tali informazioni includono la versione Docker e il numero di immagini in esecuzione sull'host. 

>[!NOTE]
> Questi controlli di benchmark CIS non verranno eseguiti in istanze gestite da AKS o nelle macchine virtuali gestite da Databricks.

### <a name="continuous-monitoring-of-your-kubernetes-clusters"></a>Monitoraggio continuo dei cluster Kubernetes
Il Centro sicurezza collabora con il servizio Azure Kubernetes Service (AKS), il servizio di orchestrazione dei contenitori gestiti di Microsoft per lo sviluppo, la distribuzione e la gestione di applicazioni containerizzate.

AKS fornisce controlli di sicurezza e visibilità sul livello di sicurezza dei cluster. Il Centro sicurezza usa queste funzionalità per:
* Monitorare costantemente la configurazione dei cluster AKS
* Genera consigli sulla sicurezza in linea con gli standard del settore

Per informazioni dettagliate sui consigli del Centro sicurezza pertinenti che potrebbero essere visualizzati per questa funzionalità, vedere la sezione relativa al [contenitore](recommendations-reference.md#recs-containers) della tabella di riferimento dei suggerimenti.

## <a name="run-time-protection---real-time-threat-detection"></a>Protezione in fase di esecuzione - Rilevamento delle minacce in tempo reale

Il Centro sicurezza fornisce il rilevamento delle minacce in tempo reale per gli ambienti containerizzati e genera avvisi per attività sospette. È possibile usare queste informazioni per risolvere rapidamente i problemi di sicurezza e migliorare la sicurezza dei contenitori.

Rileviamo le minacce a livello di host e cluster AKS. Per i dettagli completi, vedere [Rilevamento delle minacce per i contenitori](threat-protection.md#azure-containers)di Azure.For full details, see threat detection for Azure containers .


## <a name="container-security-faq"></a>Domande frequenti sulla sicurezza dei contenitoriContainer security FAQ

### <a name="what-types-of-images-can-azure-security-center-scan"></a>Quali tipi di immagini possono essere analizzate dal Centro sicurezza di Azure?
Il Centro sicurezza analizza le immagini basate su Sistema operativo Linux che forniscono l'accesso alla shell. 

Lo scanner Qualys non supporta immagini super minimaliste come immagini [scratch Docker](https://hub.docker.com/_/scratch/) o immagini "Distroless" che contengono solo l'applicazione e le relative dipendenze di runtime senza un gestore di pacchetti, shell o sistema operativo.

### <a name="how-does-azure-security-center-scan-an-image"></a>In che modo il Centro sicurezza di Azure analizza un'immagine?
L'immagine viene estratta dal Registro di sistema. Viene quindi eseguito in una sandbox isolata con lo scanner Qualys che estrae un elenco di vulnerabilità note.

Il Centro sicurezza filtra e classifica i risultati dallo scanner. Quando un'immagine è integra, il Centro sicurezza la contrassegna come tale. Il Centro sicurezza genera consigli di sicurezza solo per le immagini con problemi da risolvere. Notificando solo in caso di problemi, il Centro sicurezza riduce il rischio di avvisi informativi indesiderati.

### <a name="how-often-does-azure-security-center-scan-my-images"></a>Con quale frequenza il Centro sicurezza di Azure analizza le immagini?
Le scansioni delle immagini vengono attivate ad ogni pressione.

### <a name="can-i-get-the-scan-results-via-rest-api"></a>È possibile ottenere i risultati della scansione tramite l'API REST?
Sì. I risultati si trovano in [Sub-Assessments Rest API](/rest/api/securitycenter/subassessments/list/). Inoltre, è possibile usare Azure Resource Graph (ARG), l'API Kusto-like per tutte le risorse: una query può recuperare un'analisi specifica.
 

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulla sicurezza dei contenitori nel Centro sicurezza di Azure, vedere questi articoli correlati:To learn more about container security in Azure Security Center, see these related articles:

* Per visualizzare il livello di sicurezza delle risorse correlate ai contenitori, vedere la sezione [relativa ai](security-center-virtual-machine-protection.md#containers)contenitori in Proteggere i computer e le applicazioni .

* Dettagli dell'integrazione con il [servizio Azure Kubernetes](azure-kubernetes-service-integration.md)

* Dettagli [dell'integrazione con](azure-container-registry-integration.md) il Registro di sistema del contenitore di AzureDetails of the integration with Azure Container Registry