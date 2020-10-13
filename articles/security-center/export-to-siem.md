---
title: Trasmettere gli avvisi dal centro sicurezza di Azure ai sistemi di gestione delle informazioni e degli eventi di sicurezza e ad altre soluzioni di monitoraggio
description: Informazioni su come trasmettere gli avvisi di sicurezza a Sentinel di Azure, SIEM di terze parti, SOAR o soluzioni ITSM
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 10/06/2020
ms.author: memildin
ms.openlocfilehash: 0800c0d6fb2cf57b919d29ac354d2d89c06c7aeb
ms.sourcegitcommit: ba7fafe5b3f84b053ecbeeddfb0d3ff07e509e40
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/12/2020
ms.locfileid: "91946731"
---
# <a name="stream-alerts-to-a-siem-soar-or-it-service-management-solution"></a>Trasmettere avvisi a una soluzione SIEM, SOAR o IT Service Management

Il Centro sicurezza di Azure è in grado di trasmettere gli avvisi di sicurezza nelle soluzioni di sicurezza e gestione degli eventi (SIEM, Security Information and Event Management) più diffuse, della risposta automatica per l'orchestrazione della sicurezza (ITSM).

Sono disponibili strumenti nativi di Azure per garantire la possibilità di visualizzare i dati degli avvisi in tutte le soluzioni più diffuse attualmente in uso, tra cui:

- **Azure Sentinel**
- **Splunk Enterprise and Splunk Cloud**
- **QRadar IBM**
- **ServiceNow**
- **ArcSight**
- **Power BI**
- **Palo Alto Networks**

## <a name="stream-alerts-to-azure-sentinel"></a>Trasmettere avvisi ad Azure Sentinel 

Il Centro sicurezza si integra in modo nativo con Azure Sentinel, la soluzione SIEM e SOAR nativa del cloud di Azure. 

[Altre informazioni su Sentinel di Azure](../sentinel/overview.md).

### <a name="azure-sentinels-connectors-for-security-center"></a>Connettori di Azure Sentinel per il Centro sicurezza

Azure Sentinel include connettori predefiniti per il Centro sicurezza di Azure a livello di sottoscrizione e tenant:

- [Trasmettere avvisi ad Azure Sentinel a livello di sottoscrizione](../sentinel/connect-azure-security-center.md)
- [Connettere tutte le sottoscrizioni nel tenant ad Azure Sentinel](https://techcommunity.microsoft.com/t5/azure-sentinel/azure-security-center-auto-connect-to-sentinel/ba-p/1387539) 

### <a name="configure-ingestion-of-all-audit-logs-into-azure-sentinel"></a>Configurare l'inserimento di tutti i log di controllo in Sentinel di Azure 

Un'altra alternativa per esaminare gli avvisi del Centro sicurezza in Sentinel di Azure consiste nello trasmettere i log di controllo in Sentinel di Azure:
    - [Connettere gli eventi di sicurezza di Windows](../sentinel/connect-windows-security-events.md)
    - [Raccogliere dati da origini basate su Linux usando syslog](../sentinel/connect-syslog.md)
    - [Connettere i dati dal log attività di Azure](../sentinel/connect-azure-activity.md)

> [!TIP]
> Azure Sentinel viene fatturato in base al volume di dati inseriti per l'analisi in Sentinel di Azure e archiviato nell'area di lavoro Log Analytics di monitoraggio di Azure. Azure Sentinel offre un modello di determinazione dei prezzi flessibile e prevedibile. [Per altre informazioni, vedere la pagina dei prezzi di Sentinel per Azure](https://azure.microsoft.com/pricing/details/azure-sentinel/).


## <a name="stream-alerts-with-microsoft-graph-security-api"></a>Trasmettere avvisi con Microsoft Graph API di sicurezza

Il Centro sicurezza offre integrazione predefinita con Microsoft Graph API di sicurezza. Non è richiesta alcuna configurazione e non sono previsti costi aggiuntivi. 

È possibile usare questa API per trasmettere avvisi dall' **intero tenant** (e dai dati di molti altri prodotti Microsoft per la sicurezza) a Siem di terze parti e ad altre piattaforme comuni:

- **Splunk Enterprise e Splunk cloud**  -  [Usare l'API di sicurezza di Microsoft Graph Add-On per Splunk](https://splunkbase.splunk.com/app/4564/) 
- **Power bi**  -  [Connettersi all'API di sicurezza Microsoft Graph in Power bi desktop](https://docs.microsoft.com/power-bi/connect-data/desktop-connect-graph-security)
- **ServiceNow**  -  [Seguire le istruzioni per installare e configurare l'applicazione API di sicurezza Microsoft Graph dall'archivio ServiceNow](https://docs.servicenow.com/bundle/orlando-security-management/page/product/secops-integration-sir/secops-integration-ms-graph/task/ms-graph-install.html)
- **QRadar**  -  [Modulo di supporto dei dispositivi IBM per il Centro sicurezza di Azure tramite Microsoft Graph API](https://www.ibm.com/support/knowledgecenter/SS42VS_DSM/com.ibm.dsm.doc/c_dsm_guide_ms_azure_security_center_overview.html) 
- **Palo Alto Networks**, **anomali**, **Lookout**, **inspark**e altre [Microsoft Graph API](https://www.microsoft.com/security/business/graph-security-api#office-MultiFeatureCarousel-09jr2ji) per la sicurezza

[Altre informazioni su Microsoft Graph API di sicurezza](https://www.microsoft.com/security/business/graph-security-api).


## <a name="stream-alerts-with-azure-monitor"></a>Trasmettere avvisi con monitoraggio di Azure 

Per eseguire lo streaming di avvisi in **ArcSight**, **Splunk**, **SumoLogic**, server syslog, **LogRhythm**, **LOGZ.io piattaforma di osservazione cloud**e altre soluzioni di monitoraggio. connettere il Centro sicurezza con monitoraggio di Azure tramite hub eventi di Azure:

1. Abilitare l' [esportazione continua](continuous-export.md) per trasmettere gli avvisi del Centro sicurezza in un hub eventi di Azure dedicato a livello di sottoscrizione. 
    > [!TIP]
    > Per eseguire questa operazione a livello di gruppo di gestione usando criteri di Azure, vedere [creare configurazioni di automazione di esportazione continua su larga scala](continuous-export.md?tabs=azure-policy#configure-continuous-export-at-scale-using-the-supplied-policies)

1. [Connettere l'hub eventi di Azure alla soluzione preferita usando i connettori predefiniti di monitoraggio di Azure](../azure-monitor/platform/stream-monitoring-data-event-hubs.md#partner-tools-with-azure-monitor-integration).

1. Facoltativamente, trasmettere i log non elaborati all'hub eventi di Azure e connettersi alla soluzione preferita. Ulteriori informazioni sono [disponibili in monitoraggio dei dati disponibili](../azure-monitor/platform/stream-monitoring-data-event-hubs.md#monitoring-data-available).

> [!TIP]
> Per visualizzare gli schemi di eventi dei tipi di dati esportati, vedere gli [schemi degli eventi dell'hub eventi](https://aka.ms/ASCAutomationSchemas).


## <a name="next-steps"></a>Passaggi successivi

Questa pagina ha illustrato come verificare che i dati di avviso del Centro sicurezza di Azure siano disponibili nello strumento SIEM, SOAR o ITSM preferito. Per materiale correlato, vedere:

- [Che cos'è Azure Sentinel?](../sentinel/overview.md)
- [Convalida degli avvisi nel centro sicurezza di Azure](security-center-alert-validation.md) : verificare che gli avvisi siano configurati correttamente
- [Esporta continuamente avvisi e raccomandazioni sulla sicurezza](continuous-export.md)