---
title: Azure AD Connect Health - Avviso "I dati del Servizio integrità non sono aggiornati" | Microsoft Docs
description: In questo documento vengono descritte la causa dell'avviso "I dati del Servizio integrità non sono aggiornati" e la modalità di risoluzione di questo problema.
services: active-directory
documentationcenter: ''
author: zhiweiwangmsft
manager: SamuelD
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/26/2018
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 41c1c102e88e1712d561874aef87a6f22ed250a9
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/16/2019
ms.locfileid: "72430224"
---
# <a name="health-service-data-is-not-up-to-date-alert"></a>I dati del Servizio integrità non sono aggiornati

## <a name="overview"></a>Overview

Gli agenti nei computer locali che Azure AD Connect Health monitora periodicamente i dati vengono caricati nel servizio Azure AD Connect Health. Se il servizio non riceve dati da un agente, le informazioni presenti nel portale saranno obsolete. Per evidenziare il problema, il servizio genererà i **dati del servizio integrità non** aggiornati. Questo avviso viene generato quando il servizio non ha ricevuto dati completi nelle due ore precedenti.  

- L'avviso di stato di **avviso** viene attivato se il servizio integrità ha ricevuto solo i tipi di dati **parziali** inviati dal server nelle ultime due ore. L'avviso di stato di avviso non attiva le notifiche tramite posta elettronica ai destinatari configurati. 
- L'avviso di stato di **errore** viene attivato se il servizio integrità non ha ricevuto tipi di dati dal server nelle ultime due ore. L'avviso di stato di errore attiva le notifiche tramite posta elettronica ai destinatari configurati.

Il servizio ottiene i dati dagli agenti in esecuzione nei computer locali, a seconda del tipo di servizio. La tabella seguente elenca gli agenti che vengono eseguiti nel computer, le operazioni eseguite e i tipi di dati generati dal servizio. In alcuni casi, nel processo sono presenti più servizi, quindi uno di essi potrebbe essere il colpevole. 

## <a name="understanding-the-alert"></a>Informazioni sull'avviso

Il pannello **Dettagli avviso** indica quando si è verificato l'avviso e l'ultimo è stato rilevato. Un processo in background che viene eseguito ogni due ore genera e valuta di nuovo l'avviso. Nell'esempio seguente, l'avviso iniziale si è verificato il 03/10 alle 9:59. L'avviso esisteva ancora il 03/12 alle 10:00 AM quando l'avviso è stato valutato nuovamente. Nel pannello viene inoltre illustrata la data e l'ora dell'ultima ricezione di un particolare tipo di dati Servizio integrità. 
 
 ![Dettagli avviso Azure AD Connect Health](./media/how-to-connect-health-data-freshness/data-freshness-details.png)
 
Nella tabella seguente viene eseguito il mapping dei tipi di servizio ai tipi di dati necessari corrispondenti:

| Tipo di servizio | Agent (nome del servizio Windows) | Scopo | Tipo di dati generato  |
| --- | --- | --- | --- |  
| Azure AD Connect (sincronizzazione) | Azure AD Connect Health Sync Insights Service | Raccogliere informazioni specifiche di AAD Connect (connettori, regole di sincronizzazione e così via) | - AadSyncService-SynchronizationRules <br />  -AadSyncService-connettori <br /> - AadSyncService-GlobalConfigurations  <br />  - AadSyncService-RunProfileResults <br /> - AadSyncService-ServiceConfigurations <br /> -AadSyncService-ServiceStatus   |
|  | Azure AD Connect Health Sync Monitoring Service | Raccolta di contatori delle prestazioni specifici di AAD Connect, tracce ETW, file | Contatore delle prestazioni |
| Servizi di dominio Active Directory | Azure AD Connect Health AD DS Insights Service | Eseguire test sintetici, raccogliere informazioni sulla topologia, metadati di replica |  -Aggiunge-TopologyInfo-JSON <br /> -Common-TestData-JSON (crea i risultati del test)   | 
|  | Azure AD Connect Health AD DS Monitoring Service | Raccolta di contatori delle prestazioni specifici di aggiunta, tracce ETW, file | -Contatore delle prestazioni  <br /> -Common-TestData-JSON (carica i risultati del test)  |
| AD FS | Azure AD Connect Health AD FS Diagnostics Service | Esegui test sintetici | TestResult (crea i risultati del test) | 
| | Azure AD Connect Health AD FS Insights Service  | Raccolta delle metriche di utilizzo di ADFS | Adfs-UsageMetrics |
| | Azure AD Connect Health AD FS Monitoring Service | Raccolta di contatori delle prestazioni specifici di ADFS, tracce ETW, file | TestResult (carica i risultati del test) |

## <a name="troubleshooting-steps"></a>Passaggi per la risoluzione dei problemi 

Di seguito sono riportati i passaggi necessari per diagnosticare il problema. Il primo è un set di controlli di base comuni a tutti i tipi di servizio. 

> [!IMPORTANT] 
> Questo avviso è conforme ai [criteri di conservazione dei dati](reference-connect-health-user-privacy.md#data-retention-policy) di Connect Health.

* Verificare che siano installate le versioni più recenti degli agenti. Visualizza la [cronologia delle versioni](reference-connect-health-version-history.md). 
* Assicurarsi che i servizi di Azure AD Connect Health Agents siano **in esecuzione** nel computer. Ad esempio, Connect Health per AD FS deve avere tre servizi.
  ![verificare Azure AD Connect Health](./media/how-to-connect-health-agent-install/install5.png)

* Assicurarsi di esaminare le informazioni nella [sezione dei requisiti](how-to-connect-health-agent-install.md#requirements) e di essere conformi a tali requisiti.
* Usare lo [strumento per il test della connettività](how-to-connect-health-agent-install.md#test-connectivity-to-azure-ad-connect-health-service) per individuare i problemi di connettività.
* Se è presente un proxy HTTP, seguire questa [procedura di configurazione](how-to-connect-health-agent-install.md#configure-azure-ad-connect-health-agents-to-use-http-proxy). 


## <a name="next-steps"></a>Passaggi successivi
Se uno dei passaggi precedenti ha identificato un problema, correggerlo e attendere la risoluzione dell'avviso. Il processo in background dell'avviso viene eseguito ogni 2 ore, per cui la risoluzione dell'avviso può richiedere fino a 2 ore. 

* [Criteri di conservazione dei dati Azure AD Connect Health](reference-connect-health-user-privacy.md#data-retention-policy)
* [Domande frequenti su Azure AD Connect Health](reference-connect-health-faq.md)
