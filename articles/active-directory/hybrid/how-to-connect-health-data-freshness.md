---
title: Azure AD Connect Health - Avviso "I dati del Servizio integrità non sono aggiornati" | Microsoft Docs
description: In questo documento vengono descritte la causa dell'avviso "I dati del Servizio integrità non sono aggiornati" e la modalità di risoluzione di questo problema.
services: active-directory
documentationcenter: ''
author: zhiweiw
manager: maheshu
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/26/2018
ms.author: zhiweiw
ms.openlocfilehash: ca9f23703315424fcf08350ae3111a20dd94c160
ms.sourcegitcommit: fbdfcac863385daa0c4377b92995ab547c51dd4f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2018
ms.locfileid: "50233226"
---
# <a name="health-service-data-is-not-up-to-date-alert"></a>I dati del Servizio integrità non sono aggiornati

## <a name="overview"></a>Panoramica
<li>Azure AD Connect Health genera l'avviso sull'aggiornamento dei dati quando non riceve tutti i punti dati dal server per due ore. Il titolo dell'avviso è **I dati del Servizio integrità non sono aggiornati**. </li>
<li>Viene attivato l'avviso dello stato di tipo **Avviso** se Azure AD Connect Health non riceve elementi dati parziali dal server per due ore. L'avviso di stato di tipo Avviso non attiva le notifiche tramite posta elettronica all'amministratore del tenant. </li>
<li>Viene attivato l'avviso di stato di tipo **Errore** se Azure AD Connect Health non riceve elementi dati dal server per due ore. L'avviso di stato di tipo Errore attiva le notifiche tramite posta elettronica all'amministratore del tenant. </li>

>[!IMPORTANT] 
> Questo avviso è conforme ai [criteri di conservazione dei dati](reference-connect-health-user-privacy.md#data-retention-policy) di Connect Health.

## <a name="troubleshooting-steps"></a>Passaggi per la risoluzione dei problemi 
* Assicurarsi di esaminare le informazioni nella [sezione dei requisiti](how-to-connect-health-agent-install.md#requirements) e di essere conformi a tali requisiti.
* Usare lo [strumento per il test della connettività](how-to-connect-health-agent-install.md#test-connectivity-to-azure-ad-connect-health-service) per individuare i problemi di connettività.
* Se è presente un proxy HTTP, seguire la [procedura di configurazione](how-to-connect-health-agent-install.md#configure-azure-ad-connect-health-agents-to-use-http-proxy). 

### <a name="connect-health-for-sync"></a>Connect Health per la sincronizzazione

| Elementi dati | Passaggi per la risoluzione dei problemi |
| --- | --- | 
| PerfCounter | - [Connettività in uscita agli endpoint di servizio di Azure](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections) <br />- [L'analisi SSL per il traffico in uscita è filtrata o disabilitata](https://technet.microsoft.com/library/ee796230.aspx) <br /> - [Porte del firewall nel server che esegue l'agente](https://technet.microsoft.com/library/ms345310(v=sql.100).aspx) <br /> - [Consentire i siti Web seguenti se è abilitata la funzionalità Protezione avanzata di IE](https://technet.microsoft.com/en-us/windows/ms537180(v=vs.60)) |
| AadSyncService-SynchronizationRules, <br /> AadSyncService-Connectors, <br /> AadSyncService-GlobalConfigurations, <br /> AadSyncService-RunProfileResults, <br /> AadSyncService-ServiceConfigurations, <br /> AadSyncService-ServiceStatus | - Per altre informazioni sulla connettività in uscita in base agli indirizzi IP, vedere [Azure IP Ranges](https://www.microsoft.com/download/details.aspx?id=41653) (Intervalli IP di Azure) <br /> - [Connettività in uscita agli endpoint di servizio di Azure](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections) <br /> -  [Porte del firewall nel server che esegue l'agente](https://technet.microsoft.com/library/ms345310(v=sql.100).aspx) | 

### <a name="connect-health-for-adfs"></a>Connect Health per file system distribuito di Azure

Passaggi aggiuntivi per la convalida in AD FS e per seguire il flusso di lavoro sono disponibili nella [guida di AD FS](https://adfshelp.microsoft.com/TroubleshootingGuides/Workflow/3ef51c1f-499e-4e07-b3c4-60271640e282).

| Elementi dati | Passaggi per la risoluzione dei problemi |
| --- | --- | 
| PerfCounter, TestResult | - [Connettività in uscita agli endpoint di servizio di Azure](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections) <br />- [L'analisi SSL per il traffico in uscita è filtrata o disabilitata](https://technet.microsoft.com/library/ee796230.aspx) <br />-  [Porte del firewall nel server che esegue l'agente](https://technet.microsoft.com/library/ms345310(v=sql.100).aspx) <br /> - [Consentire i siti Web seguenti se è abilitata la funzionalità Protezione avanzata di IE](https://technet.microsoft.com/en-us/windows/ms537180(v=vs.60)) |
|  Adfs-UsageMetrics | Per altre informazioni sulla connettività in uscita in base agli indirizzi IP, vedere [Azure IP Ranges](https://www.microsoft.com/download/details.aspx?id=41653) (Intervalli IP di Azure) | 

### <a name="connect-health-for-adds"></a>Connect Health per ADDS

| Elementi dati | Passaggi per la risoluzione dei problemi |
| --- | --- | 
| PerfCounter, Adds-TopologyInfo-Json, Common-TestData-Json | - [Connettività in uscita agli endpoint di servizio di Azure](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections) <br /> - [L'analisi SSL per il traffico in uscita è filtrata o disabilitata](https://technet.microsoft.com/library/ee796230.aspx) <br />-  [Porte del firewall nel server che esegue l'agente](https://technet.microsoft.com/library/ms345310(v=sql.100).aspx) <br /> - [Consentire i siti Web seguenti se è abilitata la funzionalità Protezione avanzata di IE](https://technet.microsoft.com/en-us/windows/ms537180(v=vs.60)) <br />  - Per altre informazioni sulla connettività in uscita in base agli indirizzi IP, vedere [Azure IP Ranges](https://www.microsoft.com/download/details.aspx?id=41653) (Intervalli IP di Azure)  |


## <a name="next-steps"></a>Passaggi successivi
* [Domande frequenti su Azure AD Connect Health](reference-connect-health-faq.md)
