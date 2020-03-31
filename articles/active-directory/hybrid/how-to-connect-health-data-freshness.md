---
title: Azure AD Connect Health - Avviso "I dati del Servizio integrità non sono aggiornati" | Microsoft Docs
description: In questo documento vengono descritte la causa dell'avviso "I dati del Servizio integrità non sono aggiornati" e la modalità di risoluzione di questo problema.
services: active-directory
documentationcenter: ''
author: zhiweiwangmsft
manager: SamuelD
editor: ''
ms.service: active-directory
ms.subservice: hybrid
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/26/2018
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: a94bd07cf5020981cdf028ec0eccfa8fa531d240
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76897174"
---
# <a name="health-service-data-is-not-up-to-date-alert"></a>I dati del Servizio integrità non sono aggiornati

## <a name="overview"></a>Panoramica

Gli agenti nei computer locali monitorati da Azure AD Connect Health caricano periodicamente i dati nel servizio Azure AD Connect Health. Se il servizio non riceve dati da un agente, le informazioni presentate dal portale non saranno aggiornate. Per evidenziare il problema, il servizio genererà i dati del **servizio integrità non è aggiornato.** Questo avviso viene generato quando il servizio non ha ricevuto dati completi nelle ultime due ore.  

- L'avviso di stato di **avviso** viene generato se il servizio integrità ha ricevuto solo tipi di dati **parziali** inviati dal server nelle ultime due ore. L'avviso di stato di avviso non attiva le notifiche e-mail ai destinatari configurati. 
- L'avviso **Stato errore** viene generato se il servizio integrità non ha ricevuto alcun tipo di dati dal server nelle ultime due ore. L'avviso di stato di errore attiva le notifiche di posta elettronica ai destinatari configurati.

Il servizio ottiene i dati dagli agenti in esecuzione nei computer locali, a seconda del tipo di servizio. Nella tabella seguente sono elencati gli agenti eseguiti nel computer, le operazioni eseguite e i tipi di dati generati dal servizio. In alcuni casi, ci sono più servizi coinvolti nel processo, quindi ognuno di loro potrebbe essere il colpevole. 

## <a name="understanding-the-alert"></a>Comprendere l'avviso

Il pannello **Dettagli avviso** mostra quando si è verificato l'avviso ed è stato rilevato per l'ultima volta. Un processo in background che viene eseguito ogni due ore genera e rivaluta l'avviso. Nell'esempio seguente, l'avviso iniziale si è verificato il 03/10 alle 9:59 AM. L'avviso esisteva ancora il 03/12 alle 10:00 quando l'avviso è stato valutato nuovamente. Il pannello descrive inoltre in dettaglio l'ora dell'ultima ricezione di un tipo di dati da parte del servizio integrità. 
 
 ![Dettagli degli avvisi di Azure AD Connect HealthAzure AD Connect Health alert details](./media/how-to-connect-health-data-freshness/data-freshness-details.png)
 
Nella tabella seguente viene eseguito il mapping dei tipi di servizio ai tipi di dati obbligatori corrispondenti:The following table maps service types to corresponding required data types:

| Tipo di servizio | Agente (nome servizio Windows) | Scopo | Tipo di dati generato  |
| --- | --- | --- | --- |  
| Azure AD Connect (sincronizzazione)Azure AD Connect (Sync) | Azure AD Connect Health Sync Insights Service | Raccogliere informazioni specifiche di AAD Connect (connettori, regole di sincronizzazione e così via) | - AadSyncService-SynchronizationRules <br />  - AadSyncService-Connettori <br /> - AadSyncService-GlobalConfigurations  <br />  - AadSyncService-RunProfileResults <br /> - AadSyncService-ServiceConfigurations <br /> - AadSyncService-ServiceStatus   |
|  | Azure AD Connect Health Sync Monitoring Service | Raccogliere contatori di perf specifici di AAD Connect, tracce ETW, fileCollect AAD Connect-specific perf counters, ETW traces, files | Contatore delle prestazioni |
| Servizi di dominio Active Directory | Azure AD Connect Health AD DS Insights Service | Eseguire test sintetici, raccogliere informazioni sulla topologia, metadati di replica |  - Adds-TopologyInfo-Json <br /> - Common-TestData-Json (crea i risultati del test)- Common-TestData-Json (creates the test results)   | 
|  | Azure AD Connect Health AD DS Monitoring Service | Raccogliere contatori di perf specifici di ADDS, tracce ETW, file | - Contatore delle prestazioni  <br /> - Common-TestData-Json (carica i risultati del test)- Common-TestData-Json (uploads the test results)  |
| AD FS | Azure AD Connect Health AD FS Diagnostics Service | Eseguire test sintetici | TestResult (crea i risultati del test) | 
| | Azure AD Connect Health AD FS Insights Service  | Raccogliere le metriche di utilizzo di ADFSCollect ADFS usage metrics | Adfs-UsageMetrics |
| | Azure AD Connect Health AD FS Monitoring Service | Raccogliere contatori di perf specifici di ADFS, tracce ETW, fileCollect ADFS-specific perf counters, ETW traces, files | TestResult (carica i risultati del test) |

## <a name="troubleshooting-steps"></a>Passaggi per la risoluzione dei problemi 

Di seguito sono riportati i passaggi necessari per diagnosticare il problema. Il primo è un set di controlli di base comuni a tutti i tipi di servizio. 

> [!IMPORTANT] 
> Questo avviso è conforme ai [criteri di conservazione dei dati](reference-connect-health-user-privacy.md#data-retention-policy) di Connect Health.

* Assicurarsi che siano installate le versioni più recenti degli agenti. Visualizzare la [cronologia delle versioni](reference-connect-health-version-history.md). 
* Assicurarsi che i servizi di Azure AD Connect Health Agents siano **in esecuzione** nel computer. Ad esempio, Connect Health per AD FS deve avere tre servizi.
  ![Verificare Azure AD Connect Health](./media/how-to-connect-health-agent-install/install5.png)

* Assicurarsi di esaminare le informazioni nella [sezione dei requisiti](how-to-connect-health-agent-install.md#requirements) e di essere conformi a tali requisiti.
* Usare lo [strumento per il test della connettività](how-to-connect-health-agent-install.md#test-connectivity-to-azure-ad-connect-health-service) per individuare i problemi di connettività.
* Se è presente un proxy HTTP, seguire questa [procedura di configurazione](how-to-connect-health-agent-install.md#configure-azure-ad-connect-health-agents-to-use-http-proxy). 


## <a name="next-steps"></a>Passaggi successivi
Se uno dei passaggi precedenti ha identificato un problema, risolverlo e attendere che l'avviso venga risolto. Il processo in background dell'avviso viene eseguito ogni 2 ore, pertanto la risoluzione dell'avviso richiederà fino a 2 ore. 

* [Criteri di conservazione dei dati di Azure AD Connect HealthAzure AD Connect Health data retention policy](reference-connect-health-user-privacy.md#data-retention-policy)
* [Domande frequenti su Azure AD Connect Health](reference-connect-health-faq.md)
