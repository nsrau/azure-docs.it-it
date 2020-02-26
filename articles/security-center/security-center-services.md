---
title: Funzionalità supportate disponibili nel centro sicurezza di Azure | Microsoft Docs
description: Questo documento fornisce un elenco dei servizi supportati dal centro sicurezza di Azure.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 870ebc8d-1fad-435b-9bf9-c477f472ab17
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/05/2020
ms.author: memildin
ms.openlocfilehash: 8700421551af227f158abaa38d5f96c8e2987ba3
ms.sourcegitcommit: 0cc25b792ad6ec7a056ac3470f377edad804997a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/25/2020
ms.locfileid: "77603398"
---
# <a name="supported-features-available-in-azure-security-center"></a>Funzionalità supportate disponibili nel centro sicurezza di Azure

> [!NOTE]
>Alcune funzionalità sono disponibili solo con il livello standard. Se non si è ancora iscritti al livello standard del Centro sicurezza, è disponibile un periodo di valutazione gratuito. Per ulteriori informazioni, vedere la [pagina dei prezzi del Centro sicurezza](https://azure.microsoft.com/pricing/details/security-center/).

Le sezioni seguenti illustrano le funzionalità del Centro sicurezza disponibili per le [piattaforme supportate](security-center-os-coverage.md).

* [Macchine virtuali/server](#vm-server-features)
* [Servizi PaaS](#paas-services)


## Funzionalità supportate da macchine virtuali/server<a name="vm-server-features"></a>

### <a name="windows"></a>[Windows](#tab/features-windows)

|||||||||
|----|:----:|:----:|:----:|:----:|:----:|:----:|:----:|
||**Macchine virtuali di Azure**|**Set di scalabilità di macchine virtuali di Azure**|**Computer non Azure**|**Prezzi**
|[Integrazione di Microsoft Defender ATP](security-center-wdatp.md)|✔ (nelle versioni supportate)|✔ (nelle versioni supportate)|✔|Standard|
|[Avvisi di rilevamento delle minacce per l'analisi comportamentale delle macchine virtuali](security-center-alerts-iaas.md)|✔|✔|✔|Indicazioni (gratuite) rilevamento minacce (standard)|
|[Avvisi di rilevamento minacce non file](alerts-reference.md#alerts-windows)|✔|✔|✔|Standard|
|[Avvisi di rilevamento minacce basati sulla rete](security-center-alerts-service-layer.md#azure-network-layer)|✔|✔|-|Standard|
|[Accesso just-in-time alle macchine virtuali](security-center-just-in-time.md)|✔|-|-|Standard|
|[Valutazione della vulnerabilità nativa](built-in-vulnerability-assessment.md)|✔|-|-|Standard|
|[Monitoraggio dell'integrità dei file](security-center-file-integrity-monitoring.md)|✔|✔|✔|Standard|
|[Controlli adattivi delle applicazioni](security-center-adaptive-application.md)|✔|-|✔|Standard|
|[Mappa di rete](security-center-network-recommendations.md#network-map)|✔|✔|-|Standard|
|[Protezione avanzata della rete adattiva](security-center-adaptive-network-hardening.md)|✔|-|-|Standard|
|Controlli adattivi della rete|✔|✔|-|Standard|
|[Dashboard di conformità normativa & report](security-center-compliance-dashboard.md)|✔|✔|✔|Standard|
|Raccomandazioni e rilevamento delle minacce nei contenitori IaaS ospitati da Docker|-|-|-|Standard|
|Valutazione patch del sistema operativo mancanti|✔|✔|✔|Gratuito|
|Valutazione delle configurazioni errate della sicurezza|✔|✔|✔|Gratuito|
|[Valutazione di Endpoint Protection](security-center-services.md#supported-endpoint-protection-solutions-)|✔|✔|✔|Gratuito|
|Valutazione della crittografia del disco|✔|✔|-|Gratuito|
|Valutazione della vulnerabilità di terze parti|✔|-|-|Gratuito|
|[Valutazione della sicurezza di rete](security-center-network-recommendations.md)|✔|✔|-|Gratuito|


### <a name="linux"></a>[Linux](#tab/features-linux)

|||||||||
|----|:----:|:----:|:----:|:----:|:----:|:----:|:----:|
||**Macchine virtuali di Azure**|**Set di scalabilità di macchine virtuali di Azure**|**Computer non Azure**|**Prezzi**
|[Integrazione di Microsoft Defender ATP](security-center-wdatp.md)|-|-|-|Standard|
|[Avvisi di rilevamento delle minacce per l'analisi comportamentale delle macchine virtuali](security-center-alerts-iaas.md)|✔ (nelle versioni supportate)|✔ (nelle versioni supportate)|✔|Indicazioni (gratuite) rilevamento minacce (standard)|
|[Avvisi di rilevamento minacce non file](alerts-reference.md#alerts-windows)|-|-|-|Standard|
|[Avvisi di rilevamento minacce basati sulla rete](security-center-alerts-service-layer.md#azure-network-layer)|✔|✔|-|Standard|
|[Accesso just-in-time alle macchine virtuali](security-center-just-in-time.md)|✔|-|-|Standard|
|[Valutazione della vulnerabilità nativa](built-in-vulnerability-assessment.md)|✔|-|-|Standard|
|[Monitoraggio dell'integrità dei file](security-center-file-integrity-monitoring.md)|✔|✔|✔|Standard|
|[Controlli adattivi delle applicazioni](security-center-adaptive-application.md)|✔|-|✔|Standard|
|[Mappa di rete](security-center-network-recommendations.md#network-map)|✔|✔|-|Standard|
|[Protezione avanzata della rete adattiva](security-center-adaptive-network-hardening.md)|✔|-|-|Standard|
|Controlli adattivi della rete|✔|✔|-|Standard|
|[Dashboard di conformità normativa & report](security-center-compliance-dashboard.md)|✔|✔|✔|Standard|
|Raccomandazioni e rilevamento delle minacce nei contenitori IaaS ospitati da Docker|✔|✔|✔|Standard|
|Valutazione patch del sistema operativo mancanti|✔|✔|✔|Gratuito|
|Valutazione delle configurazioni errate della sicurezza|✔|✔|✔|Gratuito|
|[Valutazione di Endpoint Protection](security-center-services.md#supported-endpoint-protection-solutions-)|-|-|-|Gratuito|
|Valutazione della crittografia del disco|✔|✔|-|Gratuito|
|Valutazione della vulnerabilità di terze parti|✔|-|-|Gratuito|
|[Valutazione della sicurezza di rete](security-center-network-recommendations.md)|✔|✔|-|Gratuito|

--- 

## Soluzioni di Endpoint Protection supportate<a name="endpoint-supported"></a>

La tabella seguente contiene una matrice che indica:

 - Se è possibile usare Centro sicurezza di Azure per l'installazione ogni soluzione.
 - Quali soluzioni di protezione endpoint può fornire Centro sicurezza. Se viene individuata una soluzione di Endpoint Protection da questo elenco, il Centro sicurezza non consiglia di installarne uno.

Per informazioni sul momento in cui vengono generate le indicazioni per ognuna di queste protezioni, vedere [Endpoint Protection Assessment and raccomandazioni](security-center-endpoint-protection.md).

| Endpoint Protection| Piattaforme | Installazione del Centro sicurezza | Individuazione del Centro sicurezza |
|------|------|-----|-----|
| Windows Defender (Microsoft Antimalware)| Windows Server 2016| No, predefinito nel sistema operativo| Sì |
| System Center Endpoint Protection (Microsoft Antimalware) | Windows Server 2012 R2, 2012, 2008 R2 (vedere la nota seguente) | Tramite estensione | Sì |
| Tendenza Micro-tutte le versioni * | Famiglia Windows Server  | No | Sì |
| Symantec v12.1.1100+| Famiglia Windows Server  | No | Sì |
| McAfee v10+ | Famiglia Windows Server  | No | Sì |
| McAfee v10+ | Famiglia di server Linux  | No | Sì **\*** |
| Sophos V9 +| Famiglia di server Linux  | No | Sì **\***  |

 **\*** Lo stato di code coverage e i dati di supporto sono attualmente disponibili solo nell'area di lavoro Log Analytics associata alle sottoscrizioni protette. Non viene riflessa nel portale del Centro sicurezza di Azure.

> [!NOTE]
> - Per poter rilevare System Center Endpoint Protection (SCEP) in una macchina virtuale Windows Server 2008 R2, SCEP deve essere installato dopo PowerShell 3.0 (o versione successiva).
> - Il rilevamento di tendenza Micro Protection è supportato per gli agenti di sicurezza approfonditi.  Gli agenti OfficeScan non sono supportati.


## Funzionalità <a name="paas-services"></a> supportate per i servizi PaaS

Le risorse PaaS seguenti sono supportate dal centro sicurezza di Azure:

|Service|Raccomandazioni (gratuito)|Avvisi di rilevamento minacce (standard)|Valutazione della vulnerabilità (standard)|
|----|:----:|:----:|:----:|
|DATABASE SQL|✔|✔|✔|
|Registro Azure Container|-|-|✔|
|Servizio Azure Kubernetes|✔|✔|-|
|Database di Azure per PostgreSQL *|✔|✔|-|
|Database di Azure per MySQL *|✔|✔|-|
|Azure CosmosDB *|-|✔|-|
|Account di archiviazione|✔|-|-|
|Archiviazione BLOB|✔|✔|-|
|Servizio app|✔|✔|-|
|App per le funzioni|✔|-|-|
|Servizi cloud|✔|-|-|
|Rete virtuale|✔|-|-|
|Subnet|✔|-|-|
|NIC|✔|-|-|
|Gruppi di sicurezza di rete|✔|-|-|
|Subscription|✔ **|✔|-|
|Account Batch|✔|-|-|
|Account Service Fabric|✔|-|-|
|Account di Automazione|✔|-|-|
|Load Balancer|✔|-|-|
|Ricerca cognitiva|✔|-|-|
|Spazio dei nomi del bus di servizio|✔|-|-|
|Analisi dei flussi|✔|-|-|
|Spazio dei nomi dell'hub eventi|✔|-|-|
|App per la logica|✔|-|-|
|Cache per Redis|✔|-|-|
|Data Lake Analytics|✔|-|-|
|Archiviazione di Azure Data Lake|✔|-|-|
|Key Vault|✔|✔ *|-|

\* queste funzionalità sono attualmente supportate in anteprima.

\*consigli di \* Azure Active Directory (Azure AD) sono disponibili solo per le sottoscrizioni standard.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [il Centro sicurezza raccoglie i dati e l'agente di log Analytics](security-center-enable-data-collection.md).
- Informazioni su come il [Centro sicurezza gestisce e protegge i dati](security-center-data-security.md).
- Leggere le informazioni su come [pianificare e progettare l'adozione del Centro sicurezza di Azure](security-center-planning-and-operations-guide.md).
- Esaminare le [piattaforme che supportano il Centro sicurezza](security-center-os-coverage.md).
- Altre informazioni sul [rilevamento delle minacce per le macchine virtuali & Server nel centro sicurezza di Azure](security-center-alerts-iaas.md).
- Trova le [domande frequenti sul centro sicurezza di Azure](faq-general.md).
- Vedere i [post di blog sulla sicurezza e sulla conformità di Azure](https://blogs.msdn.com/b/azuresecurity/).