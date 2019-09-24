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
ms.date: 08/29/2019
ms.author: memildin
ms.openlocfilehash: 97708fa3211aa203aab56253b1c7720d04329014
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/23/2019
ms.locfileid: "71200822"
---
# <a name="supported-features-available-in-azure-security-center"></a>Funzionalità supportate disponibili nel centro sicurezza di Azure

> [!NOTE]
>Alcune funzionalità sono disponibili solo con il livello standard. Se non si è ancora iscritti al livello standard del Centro sicurezza, è disponibile un periodo di valutazione gratuito. Per altre informazioni, vedere [Prezzi di Centro sicurezza](https://azure.microsoft.com/pricing/details/security-center/).

Le sezioni seguenti illustrano le funzionalità del Centro sicurezza disponibili per le [piattaforme supportate](security-center-os-coverage.md).

* [Macchine virtuali/server](#vm-server-features)
* [Servizi PaaS](#paas-services)


## Funzionalità supportate da macchine virtuali/server<a name="vm-server-features"></a>

> [!div class="mx-tableFixed"]

|Server|Windows|||Linux|||Prezzi|
|----|----|----|----|----|----|----|----|
|**Environment**|**Azure**||**Non Azure**|**Azure**||**Non Azure**||
||**Macchina virtuale**|**Set di scalabilità della macchina virtuale**||**Macchina virtuale**|**Set di scalabilità della macchina virtuale**|
|Avvisi di rilevamento minacce VMBA|✔|✔|✔|✔ (nelle versioni supportate)|✔ (nelle versioni supportate)|✔|Indicazioni (gratuite) rilevamento minacce (standard)|
|Avvisi di rilevamento delle minacce di rete|✔|✔|x|✔|✔|x|Standard|
|Integrazione con Windows Defender ATP|✔ (nelle versioni supportate)|✔ (nelle versioni supportate)|✔|x|X|x|Standard|
|Patch mancanti|✔|✔|✔|✔|✔|✔|Gratuito|
|Configurazioni della sicurezza|✔|✔|✔|✔|✔|✔|Gratuito|
|Valutazione di Endpoint Protection|✔|✔|✔|x|X|x|Gratuito|
|Accesso JIT alle VM|✔|x|x|✔|x|x|Standard|
|Controlli applicazione adattivi|✔|x|✔|✔|x|✔|Standard|
|FIM|✔|✔|✔|✔|✔|✔|Standard|
|Valutazione della crittografia del disco|✔|✔|x|✔|✔|x|Gratuito|
|Distribuzione di terze parti|✔|x|x|✔|x|x|Gratuito|
|Valutazione NSG|✔|✔|x|✔|✔|x|Gratuito|
|Rilevamento delle minacce senza file|✔|✔|✔|x|X|x|Standard|
|Mappa di rete|✔|✔|x|✔|✔|x|Standard|
|Controlli adattivi della rete|✔|✔|x|✔|✔|x|Standard|
|Dashboard di conformità normativa & report|✔|✔|✔|✔|✔|✔|Standard|
|Raccomandazioni e rilevamento delle minacce nei contenitori IaaS ospitati da Docker|x|X|x|✔|✔|✔|Standard|

### Soluzioni di Endpoint Protection supportate<a name="endpoint-supported"></a>

La tabella seguente contiene una matrice che indica:

 - Se è possibile usare Centro sicurezza di Azure per l'installazione ogni soluzione.
 - Quali soluzioni di protezione endpoint può fornire Centro sicurezza. In caso venga individuata una di queste soluzioni di protezione endpoint, il Centro sicurezza consiglierà di non installarne nessuna.

Per informazioni sul momento in cui vengono generate le indicazioni per ognuna di queste protezioni, vedere [Endpoint Protection Assessment and raccomandazioni](security-center-endpoint-protection.md).

| Endpoint Protection| Piattaforme | Installazione del Centro sicurezza | Individuazione del Centro sicurezza |
|------|------|-----|-----|
| Windows Defender (Microsoft Antimalware)| Windows Server 2016| No, predefinito nel sistema operativo| Yes |
| System Center Endpoint Protection (Microsoft Antimalware) | Windows Server 2012 R2, 2012, 2008 R2 (vedere la nota seguente) | Tramite estensione | Yes |
| Tendenza Micro-tutte le versioni * | Famiglia Windows Server  | No | Yes |
| Symantec v12.1.1100+| Famiglia Windows Server  | No | Yes |
| McAfee v10+ | Famiglia Windows Server  | No | Yes |
| McAfee v10+ | Famiglia di server Linux  | No | Sì **\*** |
| Sophos V9 +| Famiglia di server Linux  | No | Sì **\***  |

 **\*** Lo stato di code coverage e i dati di supporto sono attualmente disponibili solo nell'area di lavoro Log Analytics associata alle sottoscrizioni protette e non vengono riflesse nel portale del Centro sicurezza di Azure.

> [!NOTE]
>
> - Per poter rilevare System Center Endpoint Protection (SCEP) in una macchina virtuale Windows Server 2008 R2, SCEP deve essere installato dopo PowerShell 3.0 (o versione successiva).
> - Il rilevamento di tendenza Micro Protection è supportato per gli agenti di sicurezza approfonditi.  Gli agenti OfficeScan non sono supportati.


## Funzionalità <a name="paas-services"></a> supportate per i servizi PaaS

Le risorse PaaS seguenti sono supportate dal centro sicurezza di Azure:

|Service|Raccomandazioni (gratuito)|Rilevamento delle minacce (standard)|
|----|----|----|
|SQL|✔| ✔|
|PostGreSQL*|✔| ✔|
|MySQL*|✔| ✔|
|CosmosDB|x| ✔|
|Archiviazione BLOB|✔| ✔|
|Account di archiviazione|✔| NA|
|Servizio app|✔| ✔|
|Funzione|✔| x|
|Servizio cloud|✔| x|
|Rete virtuale|✔| NA|
|Subnet|✔| NA|
|Scheda di interfaccia di rete|✔| NA|
|Gruppo di sicurezza di rete|✔| NA|
|Sottoscrizione|✔ **| ✔|
|Account Batch|✔| x|
|Account di Service Fabric|✔| x|
|Account di Automazione|✔| x|
|Servizio di bilanciamento del carico|✔| x|
|Cerca|✔| x|
|Spazio dei nomi del bus di servizio|✔| x|
|Analisi di flusso|✔| x|
|Spazio dei nomi dell'hub eventi|✔| x|
|App per la logica|✔| x|
|Redis|✔| NA|
|Data Lake Analytics|✔| x|
|Data Lake Store|✔| x|
|Insieme di credenziali delle chiavi|✔| x|

\* Queste funzionalità sono attualmente supportate in anteprima pubblica.

\*\*Le raccomandazioni Azure Active Directory (Azure AD) sono disponibili solo per le sottoscrizioni standard.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [il Centro sicurezza raccoglie i dati e l'agente di log Analytics](security-center-enable-data-collection.md).
- Informazioni su come il [Centro sicurezza gestisce e protegge i dati](security-center-data-security.md).
- Leggere le informazioni su come [pianificare e progettare l'adozione del Centro sicurezza di Azure](security-center-planning-and-operations-guide.md).
- Esaminare le [piattaforme che supportano il Centro sicurezza](security-center-os-coverage.md).
- Altre informazioni sul [rilevamento delle minacce per le macchine virtuali & Server nel centro sicurezza di Azure](security-center-alerts-iaas.md).
- Vedere le [domande frequenti sull'uso del Centro sicurezza di Azure](security-center-faq.md).
- Vedere i [post di blog sulla sicurezza e sulla conformità di Azure](https://blogs.msdn.com/b/azuresecurity/).
