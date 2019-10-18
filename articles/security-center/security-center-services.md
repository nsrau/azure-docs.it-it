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
ms.date: 09/24/2019
ms.author: memildin
ms.openlocfilehash: 275598aa50c252512348f4a04543e1beaf538626
ms.sourcegitcommit: f29fec8ec945921cc3a89a6e7086127cc1bc1759
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/17/2019
ms.locfileid: "72529476"
---
# <a name="supported-features-available-in-azure-security-center"></a>Funzionalità supportate disponibili nel centro sicurezza di Azure

> [!NOTE]
>Alcune funzionalità sono disponibili solo con il livello standard. Se non si è ancora iscritti al livello standard del Centro sicurezza, è disponibile un periodo di valutazione gratuito. Per ulteriori informazioni, vedere la [pagina dei prezzi del Centro sicurezza](https://azure.microsoft.com/pricing/details/security-center/).

Le sezioni seguenti illustrano le funzionalità del Centro sicurezza disponibili per le [piattaforme supportate](security-center-os-coverage.md).

* [Macchine virtuali/server](#vm-server-features)
* [Servizi PaaS](#paas-services)


## Funzionalità supportate da macchine virtuali/server<a name="vm-server-features"></a>

> [!div class="mx-tableFixed"]

|Server|Windows|||Linux|||Prezzi|
|----|----|----|----|----|----|----|----|
|**Ambiente**|**Azure**||**Non Azure**|**Azure**||**Non Azure**||
||**Macchina virtuale**|**Set di scalabilità della macchina virtuale**||**Macchina virtuale**|**Set di scalabilità della macchina virtuale**|
|[Integrazione di Microsoft Defender ATP](https://docs.microsoft.com/azure/security-center/security-center-wdatp)|✔ (nelle versioni supportate)|✔ (nelle versioni supportate)|✔|X|X|X|Standard|
|[Avvisi di rilevamento delle minacce per l'analisi comportamentale delle macchine virtuali](https://docs.microsoft.com/azure/security-center/security-center-alerts-iaas)|✔|✔|✔|✔ (nelle versioni supportate)|✔ (nelle versioni supportate)|✔|Indicazioni (gratuite) rilevamento minacce (standard)|
|[Avvisi di rilevamento minacce non file](https://docs.microsoft.com/azure/security-center/security-center-alerts-iaas#fileless-attack-detection-)|✔|✔|✔|X|X|X|Standard|
|[Avvisi di rilevamento minacce basati sulla rete](https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer#azure-network-layer)|✔|✔|X|✔|✔|X|Standard|
|[Accesso just-in-time alle macchine virtuali](https://docs.microsoft.com/azure/security-center/security-center-just-in-time)|✔|X|X|✔|X|X|Standard|
|[Monitoraggio dell'integrità dei file](https://docs.microsoft.com/azure/security-center/security-center-file-integrity-monitoring)|✔|✔|✔|✔|✔|✔|Standard|
|[Controlli adattivi delle applicazioni](https://docs.microsoft.com/azure/security-center/security-center-adaptive-application)|✔|X|✔|✔|X|✔|Standard|
|[Mappa di rete](https://docs.microsoft.com/azure/security-center/security-center-network-recommendations#network-map)|✔|✔|X|✔|✔|X|Standard|
|[Protezione avanzata della rete adattiva](https://docs.microsoft.com/azure/security-center/security-center-adaptive-network-hardening)|✔|X|X|✔|X|X|Standard|
|Controlli adattivi della rete|✔|✔|X|✔|✔|X|Standard|
|[Dashboard di conformità normativa & report](https://docs.microsoft.com/azure/security-center/security-center-compliance-dashboard)|✔|✔|✔|✔|✔|✔|Standard|
|Raccomandazioni e rilevamento delle minacce nei contenitori IaaS ospitati da Docker|X|X|X|✔|✔|✔|Standard|
|Valutazione patch del sistema operativo mancanti|✔|✔|✔|✔|✔|✔|Gratis|
|Valutazione delle configurazioni errate della sicurezza|✔|✔|✔|✔|✔|✔|Gratis|
|[Valutazione di Endpoint Protection](https://docs.microsoft.com/azure/security-center/security-center-services#supported-endpoint-protection-solutions-)|✔|✔|✔|X|X|X|Gratis|
|Valutazione della crittografia del disco|✔|✔|X|✔|✔|X|Gratis|
|Valutazione della vulnerabilità di terze parti|✔|X|X|✔|X|X|Gratis|
|[Valutazione della sicurezza di rete](https://docs.microsoft.com/azure/security-center/security-center-network-recommendations)|✔|✔|X|✔|✔|X|Gratis|

### Soluzioni di Endpoint Protection supportate<a name="endpoint-supported"></a>

La tabella seguente contiene una matrice che indica:

 - Se è possibile usare Centro sicurezza di Azure per l'installazione ogni soluzione.
 - Quali soluzioni di protezione endpoint può fornire Centro sicurezza. Se viene individuata una soluzione di Endpoint Protection da questo elenco, il Centro sicurezza non consiglia di installarne uno.

Per informazioni sul momento in cui vengono generate le indicazioni per ognuna di queste protezioni, vedere [Endpoint Protection Assessment and raccomandazioni](security-center-endpoint-protection.md).

| Endpoint Protection| Piattaforme | Installazione del Centro sicurezza | Individuazione del Centro sicurezza |
|------|------|-----|-----|
| Windows Defender (Microsoft Antimalware)| Windows Server 2016| No, predefinito nel sistema operativo| SÌ |
| System Center Endpoint Protection (Microsoft Antimalware) | Windows Server 2012 R2, 2012, 2008 R2 (vedere la nota seguente) | Tramite estensione | SÌ |
| Tendenza Micro-tutte le versioni * | Famiglia Windows Server  | No | SÌ |
| Symantec v12.1.1100+| Famiglia Windows Server  | No | SÌ |
| McAfee v10+ | Famiglia Windows Server  | No | SÌ |
| McAfee v10+ | Famiglia di server Linux  | No | Sì **\*** |
| Sophos V9 +| Famiglia di server Linux  | No | Sì **\***  |

 **\*** Lo stato di code coverage e i dati di supporto sono attualmente disponibili solo nell'area di lavoro Log Analytics associata alle sottoscrizioni protette. Non viene riflessa nel portale del Centro sicurezza di Azure.

> [!NOTE]
>
> - Per poter rilevare System Center Endpoint Protection (SCEP) in una macchina virtuale Windows Server 2008 R2, SCEP deve essere installato dopo PowerShell 3.0 (o versione successiva).
> - Il rilevamento di tendenza Micro Protection è supportato per gli agenti di sicurezza approfonditi.  Gli agenti OfficeScan non sono supportati.


## Funzionalità <a name="paas-services"></a> supportate per i servizi PaaS

Le risorse PaaS seguenti sono supportate dal centro sicurezza di Azure:

|Servizio|Raccomandazioni (gratuito)|Rilevamento delle minacce (standard)|
|----|----|----|
|SQL|✔| ✔|
|PostGreSQL*|✔| ✔|
|MySQL*|✔| ✔|
|CosmosDB|X| ✔|
|Archiviazione BLOB|✔| ✔|
|Account di archiviazione|✔| X|
|Servizio app|✔| ✔|
|Funzione|✔| X|
|Servizio cloud|✔| X|
|VNet|✔| X|
|Subnet|✔| X|
|NIC|✔| X|
|NSG|✔| X|
|Sottoscrizione|✔ * *| ✔|
|Account Batch|✔| X|
|Account di Service Fabric|✔| X|
|Account di Automazione|✔| X|
|Load Balancer|✔| X|
|Ricerca|✔| X|
|Spazio dei nomi del bus di servizio|✔| X|
|Analisi dei flussi|✔| X|
|Spazio dei nomi dell'hub eventi|✔| X|
|App per la logica|✔| X|
|Redis|✔| X|
|Analisi Data Lake|✔| X|
|Archivio Data Lake|✔| X|
|Key Vault|✔| X|

\* Queste funzionalità sono attualmente supportate in anteprima pubblica.

\* consigli di \* Azure Active Directory (Azure AD) sono disponibili solo per le sottoscrizioni standard.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [il Centro sicurezza raccoglie i dati e l'agente di log Analytics](security-center-enable-data-collection.md).
- Informazioni su come il [Centro sicurezza gestisce e protegge i dati](security-center-data-security.md).
- Leggere le informazioni su come [pianificare e progettare l'adozione del Centro sicurezza di Azure](security-center-planning-and-operations-guide.md).
- Esaminare le [piattaforme che supportano il Centro sicurezza](security-center-os-coverage.md).
- Altre informazioni sul [rilevamento delle minacce per le macchine virtuali & Server nel centro sicurezza di Azure](security-center-alerts-iaas.md).
- Vedere le [domande frequenti sull'uso del Centro sicurezza di Azure](security-center-faq.md).
- Vedere i [post di blog sulla sicurezza e sulla conformità di Azure](https://blogs.msdn.com/b/azuresecurity/).
