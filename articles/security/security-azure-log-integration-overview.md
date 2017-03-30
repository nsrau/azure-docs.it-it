---
title: Integrare i log delle risorse di Azure nei sistemi SIEM | Microsoft Docs
description: "Informazioni sull&quot;integrazione dei log di Azure, le funzionalità principali e il funzionamento."
services: security
documentationcenter: na
author: TomShinder
manager: MBaldwin
editor: TerryLanfear
ms.assetid: 9c1346e1-baf8-4975-b2f2-42ae05b2dc0a
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/03/2017
ms.author: TomSh
translationtype: Human Translation
ms.sourcegitcommit: 2c9877f84873c825f96b62b492f49d1733e6c64e
ms.openlocfilehash: 434961f7d25195eee3310461a3a97e03ffdd8917
ms.lasthandoff: 03/15/2017


---
# <a name="introduction-to-microsoft-azure-log-integration"></a>Introduzione all'integrazione dei log di Microsoft Azure
Informazioni sull'integrazione dei log di Azure, le funzionalità principali e il funzionamento.

## <a name="overview"></a>Overview
I servizi PaaS (Platform as a Service, piattaforma distribuita come servizio) e IaaS (Infrastructure as a Service, infrastruttura distribuita come servizio) ospitati in Azure generano una grande quantità di dati nei log di sicurezza. Questi log contengono dati importanti che possono fornire informazioni utili e approfondite sulle violazioni dei criteri, le minacce interne ed esterne, i problemi di conformità alle normative e le anomalie della rete, dell'host e dell'attività dell'utente.

L'integrazione dei log di Azure è una soluzione gratuita che consente di integrare log non elaborati dalle risorse di Azure nei sistemi di gestione di informazioni ed eventi di sicurezza locali. L'integrazione dei log di Azure raccoglie i dati di Diagnostica di Azure dalle macchine virtuali Windows *(WAD)*, i log attività di Azure, gli avvisi del Centro sicurezza di Azure e i log dei provider di risorse di Azure. Questa integrazione fornisce un dashboard unificato per tutti gli asset, locali o su cloud, consentendo di aggregare, correlare, analizzare e inviare avvisi per gli eventi di sicurezza.

![Integrazione dei log di Azure][1]

## <a name="what-logs-can-i-integrate"></a>Quali log è possibile integrare?
Azure produce registrazioni complete per ogni servizio di Azure. I log sono classificati nei seguenti tipi principali:

* **Log di gestione/controllo**, che offrono visibilità sulle operazioni CREATE, UPDATE e DELETE di Azure Resource Manager. I log attività di Azure sono un esempio di questo tipo di log.
* **Log del piano dati**, che offrono visibilità sugli eventi generati durante l'uso di una risorsa di Azure. Sono esempi di questo tipo il log eventi di sistema di Windows, il log di sicurezza e il log applicazioni di una macchina virtuale, nonché i log di diagnostica configurati tramite Monitoraggio di Azure.
* **Eventi elaborati**, che restituisce gli eventi/avvisi analizzati ed elaborati per conto dell'utente. Esempi di questo tipo sono i brevi avvisi emessi dal Centro sicurezza di Azure dopo l'elaborazione e l'analisi della sottoscrizione.

L'integrazione dei log di Azure supporta attualmente l'integrazione di log di attività di Azure, del log eventi della macchina virtuale Windows inclusa nella sottoscrizione di Azure, degli avvisi del Centro sicurezza di Azure, dei log di diagnostica di Azure e dei log di controllo di Azure Active Directory.

La tabella seguente illustra la categoria Log e i dettagli dell'integrazione SIEM

| Tipo di log  |Log Analytics con supporto JSON (Splunk, ELK)| ArcSight  | QRadar  |   
|---|---|---|---|
|  Log di controllo AAD |  sì | Richiede la creazione di un file parser JSON FlexConnector. Per altre informazioni, vedere la documentazione ArcSight.  |  È necessario creare un'estensione Origine log. Per altre informazioni, vedere la documentazione QRadar. |  
| Log attività  | Sì  |  File parser JSON FlexConnector disponibile nell'area download con il download dell'integrazione log di Azure. |  [DSM QRadar](https://www.ibm.com/support/knowledgecenter/SSKMKU/com.ibm.dsm.doc/c_dsm_guide_microsoft_azure_overview.html) (invio tramite Syslog) |  
| Avvisi del Centro sicurezza di Azure  | Sì  |  Richiede la creazione di un file parser JSON FlexConnector. Per altre informazioni, vedere la documentazione ArcSight. | [DSM QRadar](https://www.ibm.com/support/knowledgecenter/SSKMKU/com.ibm.dsm.doc/c_dsm_guide_microsoft_azure_overview.html) (invio tramite Syslog)   |   
| Log di diagnostica (log di risorse) | Sì | L'utente finale deve creare un file parser JSON FlexConnector. Per informazioni su questa operazione, vedere la documentazione ArcSight. | È necessario creare un'estensione Origine log. Per altre informazioni, vedere la documentazione QRadar. |
| Log VM | Sì, tramite Eventi inoltrati e non attraverso JSON | Sì, tramite Eventi inoltrati | Sì, tramite Eventi inoltrati |

L'integrazione dei log di Azure è una soluzione gratuita: non è necessario pagare per i file binari di integrazione di Log di Azure. Vi saranno tuttavia dei costi correlati all'archiviazione di Azure necessari per le informazioni sui file di log.

In caso di domande sull'integrazione dei log di Azure, inviare un messaggio di posta elettronica all'indirizzo [AzSIEMteam@microsoft.com](mailto:AzSIEMteam@microsoft.com)

## <a name="next-steps"></a>Passaggi successivi
In questo documento è stata presentata l'integrazione dei log di Azure. Per altre informazioni sull'integrazione dei log di Azure e sui tipi di log supportati, vedere gli argomenti seguenti:

* [Microsoft Azure Log Integration](https://www.microsoft.com/download/details.aspx?id=53324) (Integrazione log di Microsoft Azure) - Area download per informazioni dettagliate, requisiti di sistema e istruzioni di installazione per l'integrazione dei log di Azure.
* [Introduzione all'integrazione dei log di Azure](security-azure-log-integration-get-started.md) - Questa esercitazione illustra come installare l'integrazione dei log di Azure e integrare i log dall'archiviazione di Azure WAD, i log attività di Azure, gli avvisi del Centro sicurezza di Azure e i log di controllo di Azure Active Directory.
* [Integrate Diagnostics Logs](https://blogs.msdn.microsoft.com/azuresecurity/2016/09/25/integrate-azure-logs-streamed-to-event-hubs-to-siem/) (Integrare log di diagnostica) - Questo post di blog fornisce la procedura per integrare log di diagnostica tramite l'integrazione dei log di Azure.
* [Passaggi per la configurazione dei partner](https://blogs.msdn.microsoft.com/azuresecurity/2016/08/23/azure-log-siem-configuration-steps/) – Questo post di blog mostra come configurare l'integrazione dei log di Azure per lavorare con le soluzioni partner Splunk, HP ArcSight e IBM QRadar.
* [Integrate Azure Activity logs and Azure Security Center Alerts over Syslog to QRadar](https://blogs.msdn.microsoft.com/azuresecurity/2016/09/24/integrate-azure-logs-to-qradar/) (Integrare log attività e avvisi del Centro di sicurezza di Azure in QRadar tramite Syslog) - Questo post di blog illustra la procedura per l'invio di log attività e avvisi del Centro sicurezza di Azure a QRadar tramite Syslog.
* [Domande frequenti sull'integrazione dei log di Azure](security-azure-log-integration-faq.md) - Queste domande frequenti riguardano l'integrazione dei log di Azure.
* [Integrazione degli avvisi del Centro sicurezza con l'integrazione dei log di Azure](../security-center/security-center-integrating-alerts-with-log-integration.md) - Questo documento mostra come sincronizzare gli avvisi del Centro sicurezza di Azure, insieme agli eventi di sicurezza delle macchine virtuali raccolti da Diagnostica di Azure e dai log di controllo di Azure, con la propria soluzione SIEM o di analisi dei log.

<!--Image references-->
[1]: ./media/security-azure-log-integration-overview/azure-log-integration.png

