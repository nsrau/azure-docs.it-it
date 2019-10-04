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
ms.openlocfilehash: 6c5bc2ea76c558e47eaa5f297ebe36a629aa5754
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/09/2019
ms.locfileid: "67702633"
---
# <a name="health-service-data-is-not-up-to-date-alert"></a>I dati del Servizio integrità non sono aggiornati

## <a name="overview"></a>Panoramica

Gli agenti nei computer locali a cui Azure AD Connect Health monitora periodicamente caricare i dati per il servizio Azure AD Connect Health. Se il servizio non riceve dati da un agente, le informazioni con che il portale Visualizza saranno non aggiornate. Per evidenziare il problema, il servizio genererà il **dati del servizio integrità non sono aggiornati** avviso. Questo avviso viene generato quando il servizio non ha ricevuto dati completi nelle ultime due ore.  

- Il **avviso** avviso di stato viene attivato se il servizio integrità ha ricevuto solo **parziale** i tipi di dati inviati dal server nelle ultime due ore. L'avviso di stato di avviso non attiva notifiche tramite posta elettronica ai destinatari configurati. 
- Il **errore** viene generato un avviso di stato se il servizio integrità non ha ricevuto tutti i tipi di dati dal server nelle ultime due ore. I trigger di avviso dello stato di errore notifiche tramite posta elettronica ai destinatari configurati.

Il servizio ottiene i dati dagli agenti in esecuzione nel computer locale, a seconda del tipo di servizio. Nella tabella seguente elenca gli agenti in esecuzione su computer, le operazioni eseguite e i tipi di dati che il servizio genera. In alcuni casi, esistono più servizi coinvolti nel processo, quindi una di esse può essere la causa del problema. 

## <a name="understanding-the-alert"></a>La comprensione dell'avviso

Il **i dettagli degli avvisi** pannello mostra quando l'avviso si è verificato e ultima è stata rilevata. Un processo in background che viene eseguito ogni due ore viene generato l'errore e viene nuovamente valutata l'avviso. Nell'esempio seguente, l'avviso iniziale si è verificato 03/10 alle 9:00: 59. L'avviso trovasse ancora nel 03/12 a 10:00 quando l'avviso è stata valutata nuovamente. Il pannello dettaglio anche il tempo per ultimo, il servizio integrità ha un particolare tipo di dati. 
 
 ![Dettagli degli avvisi di Azure AD Connect Health](./media/how-to-connect-health-data-freshness/data-freshness-details.png)
 
Nella tabella seguente viene eseguito il mapping tipi di servizio ai corrispondenti tipi di dati necessaria:

| Tipo di servizio | Agente (nome del servizio Windows) | Scopo | Tipo di dati generato  |
| --- | --- | --- | --- |  
| Azure AD Connect (sincronizzazione) | Azure AD Connect Health Sync Insights Service | Raccogliere informazioni specifiche AAD Connect (connettori, le regole di sincronizzazione e così via) | AadSyncService-SynchronizationRules <br />  AadSyncService-connettori <br /> AadSyncService-GlobalConfigurations  <br />  - AadSyncService-RunProfileResults <br /> AadSyncService-ServiceConfigurations <br /> - AadSyncService-ServiceStatus   |
|  | Azure AD Connect Health Sync Monitoring Service | Raccogliere i contatori delle prestazioni specifici AAD Connect, le tracce ETW, file | Contatore delle prestazioni |
| Servizi di dominio Active Directory | Azure AD Connect Health AD DS Insights Service | Eseguire test sintetici, raccogliere informazioni sulla topologia, i metadati della replica |  -Aggiunge-TopologyInfo-Json <br /> -Common-TestData-Json (Crea i risultati del test)   | 
|  | Azure AD Connect Health AD DS Monitoring Service | Raccogliere i contatori delle prestazioni consente di aggiungere specifiche, le tracce ETW, file | : Contatore delle prestazioni  <br /> -Common-TestData-Json (consente di caricare i risultati del test)  |
| AD FS | Azure AD Connect Health AD FS Diagnostics Service | Eseguire test sintetici | TestResult (Crea i risultati del test) | 
| | Azure AD Connect Health AD FS Insights Service  | Raccogliere le metriche di utilizzo di ad FS | Adfs-UsageMetrics |
| | Azure AD Connect Health AD FS Monitoring Service | Raccogliere i contatori delle prestazioni di ad FS specifici, le tracce ETW, file | TestResult (consente di caricare i risultati del test) |

## <a name="troubleshooting-steps"></a>Passaggi per la risoluzione dei problemi 

I passaggi necessari per diagnosticare il problema è indicato di seguito. Il primo è un set di controlli di base comuni a tutti i tipi di servizio. La tabella seguente che elenca i passaggi specifici per ogni tipo di servizio e il tipo di dati. 

> [!IMPORTANT] 
> Questo avviso è conforme ai [criteri di conservazione dei dati](reference-connect-health-user-privacy.md#data-retention-policy) di Connect Health.

* Assicurarsi che siano installate le versioni più recenti degli agenti. Vista [cronologia versioni](reference-connect-health-version-history.md). 
* Assicurarsi che i servizi di agenti di Azure AD Connect Health siano **in esecuzione** nel computer. Ad esempio, Connect Health per AD FS deve avere tre servizi.
  ![Verificare Azure AD Connect Health](./media/how-to-connect-health-agent-install/install5.png)

* Assicurarsi di esaminare le informazioni nella [sezione dei requisiti](how-to-connect-health-agent-install.md#requirements) e di essere conformi a tali requisiti.
* Usare lo [strumento per il test della connettività](how-to-connect-health-agent-install.md#test-connectivity-to-azure-ad-connect-health-service) per individuare i problemi di connettività.
* Se è presente un proxy HTTP, seguire questa [procedura di configurazione](how-to-connect-health-agent-install.md#configure-azure-ad-connect-health-agents-to-use-http-proxy). 


## <a name="next-steps"></a>Passaggi successivi
Se uno dei passaggi precedenti identificato un problema, risolvere il problema e attendere che l'avviso da risolvere. Il processo di avviso in background viene eseguito ogni 2 ore, in modo che fino a 2 ore per risolvere l'avviso. 

* [Criteri di conservazione di Azure AD Connect Health dei dati](reference-connect-health-user-privacy.md#data-retention-policy)
* [Domande frequenti su Azure AD Connect Health](reference-connect-health-faq.md)
