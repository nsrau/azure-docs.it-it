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
ms.openlocfilehash: 0ad829b976d8b712ee8027c89fb618c6c07de1bc
ms.sourcegitcommit: 9aa9552c4ae8635e97bdec78fccbb989b1587548
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/20/2019
ms.locfileid: "56429015"
---
# <a name="health-service-data-is-not-up-to-date-alert"></a>I dati del Servizio integrità non sono aggiornati

## <a name="overview"></a>Panoramica
Gli agenti nei computer locali monitorati da Azure AD Connect Health caricano periodicamente dati nel servizio Azure AD Connect Health. Se il servizio non riceve dati da un agente, le informazioni visualizzate nel portale non saranno aggiornate. Per evidenziare il problema, il servizio genererà un avviso **I dati del Servizio integrità non sono aggiornati**. Questo avviso viene generato quando il servizio non ha ricevuto dati nelle ultime due ore.  

* Viene attivato l'avviso dello stato di tipo **Avviso** se Azure AD Connect Health non riceve elementi dati parziali dal server per due ore. L'avviso di stato di tipo Avviso non attiva le notifiche tramite posta elettronica all'amministratore del tenant.
* Viene attivato l'avviso di stato di tipo **Errore** se Azure AD Connect Health non riceve elementi dati dal server per due ore. L'avviso di stato di tipo Errore attiva le notifiche tramite posta elettronica all'amministratore del tenant.


## <a name="troubleshooting-steps"></a>Passaggi per la risoluzione dei problemi 

> [!IMPORTANT] 
> Questo avviso è conforme ai [criteri di conservazione dei dati](reference-connect-health-user-privacy.md#data-retention-policy) di Connect Health.

* Assicurarsi che i servizi degli agenti di Azure AD Connect Health siano in esecuzione nel computer. Ad esempio, Connect Health per AD FS deve avere tre servizi.  
  ![Verificare Azure AD Connect Health](./media/how-to-connect-health-agent-install/install5.png)

* Assicurarsi di esaminare le informazioni nella [sezione dei requisiti](how-to-connect-health-agent-install.md#requirements) e di essere conformi a tali requisiti.
* Usare lo [strumento per il test della connettività](how-to-connect-health-agent-install.md#test-connectivity-to-azure-ad-connect-health-service) per individuare i problemi di connettività.
* Se è presente un proxy HTTP, seguire questa [procedura di configurazione](how-to-connect-health-agent-install.md#configure-azure-ad-connect-health-agents-to-use-http-proxy). 

Il pannello dei dettagli degli avvisi elenca gli elementi dati mancanti da un server. La tabella seguente aiuta a circoscrivere ulteriormente il problema. 
### <a name="connect-health-for-sync"></a>Connect Health per la sincronizzazione

| Elementi dati | Passaggi per la risoluzione dei problemi |
| --- | --- | 
| PerfCounter | - [Connettività in uscita agli endpoint di servizio di Azure](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections) <br />- [L'analisi SSL per il traffico in uscita è filtrata o disabilitata](https://technet.microsoft.com/library/ee796230.aspx) <br /> - [Porte del firewall nel server che esegue l'agente](https://technet.microsoft.com/library/ms345310(v=sql.100).aspx) |
| AadSyncService-SynchronizationRules, <br /> AadSyncService-Connectors, <br /> AadSyncService-GlobalConfigurations, <br /> AadSyncService-RunProfileResults, <br /> AadSyncService-ServiceConfigurations, <br /> AadSyncService-ServiceStatus | - [Connettività in uscita agli endpoint di servizio di Azure](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections) <br /> -  [Porte del firewall nel server che esegue l'agente](https://technet.microsoft.com/library/ms345310(v=sql.100).aspx) | 

### <a name="connect-health-for-adfs"></a>Connect Health per file system distribuito di Azure

Passaggi aggiuntivi per la convalida in AD FS e per seguire il flusso di lavoro sono disponibili nella [guida di AD FS](https://adfshelp.microsoft.com/TroubleshootingGuides/Workflow/3ef51c1f-499e-4e07-b3c4-60271640e282).

| Elementi dati | Passaggi per la risoluzione dei problemi |
| --- | --- | 
| PerfCounter, TestResult | - [Connettività in uscita agli endpoint di servizio di Azure](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections) <br />- [L'analisi SSL per il traffico in uscita è filtrata o disabilitata](https://technet.microsoft.com/library/ee796230.aspx) <br />-  [Porte del firewall nel server che esegue l'agente](https://technet.microsoft.com/library/ms345310(v=sql.100).aspx) |
|  Adfs-UsageMetrics | Per altre informazioni sulla connettività in uscita in base agli indirizzi IP, vedere [Azure IP Ranges](https://www.microsoft.com/download/details.aspx?id=41653) (Intervalli IP di Azure) | 

### <a name="connect-health-for-adds"></a>Connect Health per ADDS

| Elementi dati | Passaggi per la risoluzione dei problemi |
| --- | --- | 
| PerfCounter, Adds-TopologyInfo-Json, Common-TestData-Json | - [Connettività in uscita agli endpoint di servizio di Azure](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections) <br /> -  [Porte del firewall nel server che esegue l'agente](https://technet.microsoft.com/library/ms345310(v=sql.100).aspx) |


## <a name="next-steps"></a>Passaggi successivi
* [Domande frequenti su Azure AD Connect Health](reference-connect-health-faq.md)
