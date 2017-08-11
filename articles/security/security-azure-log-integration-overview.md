---
title: Integrare i log delle risorse di Azure nei sistemi SIEM | Microsoft Docs
description: "Informazioni sull'integrazione dei log di Azure, le funzionalità principali e il funzionamento."
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
ms.date: 08/08/2017
ms.author: TomSh
ms.custom: azlog
ms.translationtype: HT
ms.sourcegitcommit: 0aae2acfbf30a77f57ddfbaabdb17f51b6938fd6
ms.openlocfilehash: 5e672bd6b9356ce16663e843e4a4e7365cb159c3
ms.contentlocale: it-it
ms.lasthandoff: 08/09/2017

---
# <a name="introduction-to-microsoft-azure-log-integration"></a>Introduzione all'integrazione dei log di Microsoft Azure
Informazioni sull'integrazione dei log di Azure, le funzionalità principali e il funzionamento.

## <a name="overview"></a>Panoramica

L'integrazione dei log di Azure è una soluzione gratuita che consente di integrare log non elaborati dalle risorse di Azure nei sistemi SIEM (Security Information and Event Management) locali.

L'integrazione dei log di Azure raccoglie gli eventi di Windows dai canali del Visualizzatore eventi di Windows, i [log attività di Azure](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md), gli [avvisi del Centro sicurezza di Azure](../security-center/security-center-intro.md) e i [log di Diagnostica di Azure](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md) dalle risorse di Azure. Questa integrazione aiuta il sistema SIEM a fornire un dashboard unificato per tutti gli asset, locali o su cloud, consentendo di aggregare, correlare, analizzare e inviare avvisi per gli eventi di sicurezza.

>[!NOTE]
Al momento i soli cloud supportati sono quello commerciale e quello di Azure per enti pubblici. Gli altri cloud per ora non sono supportati.

![Integrazione dei log di Azure][1]

## <a name="what-logs-can-i-integrate"></a>Quali log è possibile integrare?
Azure produce registrazioni complete per ogni servizio di Azure. Questi log rappresentano tre tipi di log:

* I **log di gestione/controllo** che offrono visibilità sulle operazioni CREATE, UPDATE e DELETE di [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md). I log attività di Azure sono un esempio di questo tipo di log.
* I **log del piano dati** che offrono visibilità sugli eventi generati come parte dell'uso di una risorsa di Azure. Un esempio di questo tipo di log sono i canali **Sistema**, **Sicurezza** e **Applicazione** del Visualizzatore eventi di Windows in una macchina virtuale Windows. Un altro esempio è la registrazione di diagnostica configurata tramite Monitor di Azure
* Gli **eventi elaborati** forniscono eventi analizzati e informazioni sugli avvisi elaborate per conto del cliente. Un esempio di questo tipo di evento è costituito dagli avvisi del Centro sicurezza di Azure, in cui il Centro sicurezza di Azure ha elaborato e analizzato la sottoscrizione per fornire avvisi pertinenti alle condizioni di sicurezza attuali.

L'integrazione dei log di Azure supporta attualmente l'integrazione dei log di attività di Azure, del log eventi delle macchine virtuali Windows incluse nella sottoscrizione di Azure, degli avvisi del Centro sicurezza di Azure, dei log di Diagnostica di Azure e dei log di controllo di Azure Active Directory.

>[!NOTE]
L'integrazione dei log di Azure è una soluzione gratuita, ma ci saranno costi di archiviazione di Azure derivanti dall'archiviazione di informazioni nel file di log.

La tabella seguente illustra la categoria Log e i dettagli dell'integrazione SIEM

| Tipo di log  |Log Analytics con supporto JSON (Splunk, ELK)| ArcSight  | QRadar  |   
|---|---|---|---|
|  Log di controllo AAD |  Sì | Richiede la creazione di un file parser JSON FlexConnector. Per altre informazioni, vedere la documentazione ArcSight.  |  È necessario creare un'estensione Origine log. Per altre informazioni, vedere la documentazione QRadar. |  
| Log attività  | Sì  |  File parser JSON FlexConnector disponibile nell'area download con il download dell'integrazione log di Azure. |  [DSM QRadar](https://www.ibm.com/support/knowledgecenter/SSKMKU/com.ibm.dsm.doc/c_dsm_guide_microsoft_azure_overview.html) (invio tramite Syslog) |  
| Avvisi del Centro sicurezza di Azure  | Sì  |  Richiede la creazione di un file parser JSON FlexConnector. Per altre informazioni, vedere la documentazione ArcSight. | [DSM QRadar](https://www.ibm.com/support/knowledgecenter/SSKMKU/com.ibm.dsm.doc/c_dsm_guide_microsoft_azure_overview.html) (invio tramite Syslog)   |   
| Log di diagnostica (log di risorse) | Sì | L'utente finale deve creare un file parser JSON FlexConnector. Per informazioni su questa operazione, vedere la documentazione ArcSight. | È necessario creare un'estensione Origine log. Per altre informazioni, vedere la documentazione QRadar. |
| Log VM | Sì, tramite Eventi inoltrati e non attraverso JSON | Sì, tramite Eventi inoltrati | Sì, tramite Eventi inoltrati |

Per altre informazioni sui tipi di log supportati, consultare le [domande frequenti](security-azure-log-integration-faq.md)


L'assistenza della community è disponibile tramite il [forum MSDN di Integrazione log di Azure](https://social.msdn.microsoft.com/Forums/office/home?forum=AzureLogIntegration). Il forum offre ai membri della community di AzLog la possibilità di supporto reciproco con domande, risposte, suggerimenti e trucchi su come ottenere il massimo dall'integrazione dei log di Azure. Inoltre, il team di Integrazione log di Azure monitora questo forum e offrirà il suo contributo quando possibile.

Si può anche aprire un [richiesta di supporto](../azure-supportability/how-to-create-azure-support-request.md). A tale scopo selezionare **Integrazione log** come servizio per cui richiedere supporto.

## <a name="next-steps"></a>Passaggi successivi
In questo documento è stata presentata l'integrazione dei log di Azure. Per altre informazioni sull'integrazione dei log di Azure e sui tipi di log supportati, vedere gli argomenti seguenti:

* [Integrazione log di Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=53324)- Area download per informazioni dettagliate, requisiti di sistema e istruzioni di installazione per l'integrazione dei log di Azure.
* [Introduzione all'integrazione dei log di Azure](security-azure-log-integration-get-started.md) - Questa esercitazione illustra come installare l'integrazione dei log di Azure e integrare i log dall'archiviazione di Azure WAD, i log attività di Azure, gli avvisi del Centro sicurezza di Azure e i log di controllo di Azure Active Directory.
* [Passaggi per la configurazione dei partner](https://blogs.msdn.microsoft.com/azuresecurity/2016/08/23/azure-log-siem-configuration-steps/) – Questo post di blog mostra come configurare l'integrazione dei log di Azure per lavorare con le soluzioni partner Splunk, HP ArcSight e IBM QRadar. Questo blog rappresenta la posizione corrente sulla configurazione di soluzioni dei partner. In ogni caso, fare riferimento prima di tutto alla documentazione della soluzione partner.
* [Integrate Azure Activity logs and Azure Security Center Alerts over Syslog to QRadar](https://blogs.msdn.microsoft.com/azuresecurity/2016/09/24/integrate-azure-logs-to-qradar/) (Integrare log attività e avvisi del Centro di sicurezza di Azure in QRadar tramite Syslog) - Questo post di blog illustra la procedura per l'invio di log attività e avvisi del Centro sicurezza di Azure a QRadar tramite Syslog.
* [Domande frequenti sull'integrazione dei log di Azure](security-azure-log-integration-faq.md) - Queste domande frequenti riguardano l'integrazione dei log di Azure.
* [Integrazione degli avvisi del Centro sicurezza di Azure con l'integrazione dei log di Azure](../security-center/security-center-integrating-alerts-with-log-integration.md) - Questo documento illustra come sincronizzare gli avvisi del Centro sicurezza di Azure con Integrazione log di Azure.

<!--Image references-->
[1]: ./media/security-azure-log-integration-overview/azure-log-integration.png

