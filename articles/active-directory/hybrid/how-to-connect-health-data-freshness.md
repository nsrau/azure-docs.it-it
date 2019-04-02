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
ms.author: zhiweiw
ms.collection: M365-identity-device-management
ms.openlocfilehash: 16794dfdcdc6ed9c2effe412237d2681fca4f394
ms.sourcegitcommit: 3341598aebf02bf45a2393c06b136f8627c2a7b8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/01/2019
ms.locfileid: "58803294"
---
# <a name="health-service-data-is-not-up-to-date-alert"></a>I dati del Servizio integrità non sono aggiornati

## <a name="overview"></a>Panoramica
Gli agenti nei computer locali monitorati da Azure AD Connect Health caricano periodicamente dati nel servizio Azure AD Connect Health. Se il servizio non riceve dati da un agente, le informazioni visualizzate nel portale non saranno aggiornate. Per evidenziare il problema, il servizio genererà un avviso **I dati del Servizio integrità non sono aggiornati**. Viene generato quando il servizio non ha ricevuto dati completati nelle ultime due ore.  

* Il **avviso** avviso di stato viene attivato se il servizio integrità ha ricevuto solo **parziale** i tipi di dati inviati dal server nelle ultime due ore. Avviso di stato di avviso non attiva notifiche tramite posta elettronica ai destinatari configurati. 
* Il **errore** viene generato un avviso di stato se il servizio integrità non ha ricevuto tutti i tipi di dati dal server nelle ultime due ore. Attivazione dell'avviso sullo stato errore notifiche tramite posta elettronica ai destinatari configurati.

Il servizio ottiene i dati dagli agenti in esecuzione nel computer locale. A seconda del tipo di servizio, la tabella seguente elenca gli agenti che eseguono nel computer, le operazioni eseguite, nonché i tipi di dati generati dal servizio. In alcuni casi, sono presenti più servizi coinvolti nel processo, quindi, una di esse può essere la causa del problema. 

## <a name="understanding-the-alert"></a>La comprensione dell'avviso
Nel pannello dei dettagli degli avvisi indica il tempo quando l'avviso viene generato e ultimo rilevato. L'avviso è generato/Ripeti-evaluated da un processo in background che viene eseguito ogni due ore. Nell'esempio seguente, è stato generato l'avviso iniziale 03/10 alle 9:00: 59. Essa ha continuato a esistere anche a 12 03 10:00 quando l'avviso è stata valutata nuovamente.
Il pannello dettaglio anche l'ora di ricezione ultimo un particolare tipo di dati dal servizio integrità. 
 
 ![Dettagli degli avvisi di Azure AD Connect Health](./media/how-to-connect-health-data-freshness/data-freshness-details.png)
 
Di seguito è la mappa dei tipi di servizio e tipo di dati obbligatorio corrispondente.

| Tipo di servizio | Agente (nome del servizio Windows) | Scopo | Tipo di dati generato  |
| --- | --- | --- | --- |  
| Azure AD Connect (sincronizzazione) | Azure AD Connect Health Sync Insights Service | Raccogliere informazioni specifiche di AAD Connect (sincronizzazione, i connettori delle regole e così via.) | AadSyncService-SynchronizationRules <br />  AadSyncService-connettori <br /> AadSyncService-GlobalConfigurations  <br />  - AadSyncService-RunProfileResults <br /> AadSyncService-ServiceConfigurations <br /> - AadSyncService-ServiceStatus   |
|  | Azure AD Connect Health Sync Monitoring Service | Raccogliere i contatori delle prestazioni (AAD Connect specifici), le tracce ETW, file | Contatore delle prestazioni |
| Servizi di dominio Active Directory | Azure AD Connect Health AD DS Insights Service | Eseguire test sintetici, raccogliere informazioni sulla topologia, i metadati della replica |  -Aggiunge-TopologyInfo-Json <br /> -Common-TestData-Json (Crea i risultati del test)   | 
|  | Azure AD Connect Health AD DS Monitoring Service | Raccogliere i file di contatori, le tracce ETW, delle prestazioni (specifico di ADDS) | : Contatore delle prestazioni  <br /> -Common-TestData-Json (consente di caricare i risultati del test)  |
| AD FS | Azure AD Connect Health AD FS Diagnostics Service | Eseguire test sintetici | TestResult (Crea i risultati del test) | 
| | Azure AD Connect Health AD FS Insights Service  | Raccogliere le metriche di utilizzo di ad FS | Adfs-UsageMetrics |
| | Azure AD Connect Health AD FS Monitoring Service | Raccogliere i file di contatori, le tracce ETW, delle prestazioni (specifico di ad FS) | TestResult (consente di caricare i risultati del test) |

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
