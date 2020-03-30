---
title: Pianificare i report & monitorare la distribuzione - Azure ADPlan reports and monitoring deployment - Azure AD
description: Viene descritto come pianificare ed eseguire l'implazione dei report e del monitoraggio.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: baselden
ms.reviewer: plenzke
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5ad84b8910e8d4f8af9845c33c22d128e317dedc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74232115"
---
# <a name="plan-an-azure-active-directory-reporting-and-monitoring-deployment"></a>Pianificare una distribuzione di report e monitoraggio di Azure Active DirectoryPlan an Azure Active Directory reporting and monitoring deployment

La soluzione di report e monitoraggio di Azure Active Directory (Azure AD) dipende dai requisiti legali, di sicurezza e operativi e dall'ambiente e dai processi esistenti. In questo articolo vengono presentate le varie opzioni di progettazione e viene presentata la strategia di distribuzione corretta.

### <a name="benefits-of-azure-ad-reporting-and-monitoring"></a>Vantaggi della creazione di report e monitoraggio di Azure ADBenefits of Azure AD reporting and monitoring

I report di Azure AD offrono una visualizzazione completa e i log dell'attività di Azure AD nell'ambiente, inclusi gli eventi di accesso, gli eventi di controllo e le modifiche apportate alla directory.

I dati forniti consentono di:

* determinare come vengono utilizzati le app e i servizi.

* rilevare potenziali rischi che influiscono sulla salute dell'ambiente.

* risolvere i problemi che impediscono agli utenti di portare a termine il proprio lavoro.

* ottenere informazioni dettagliate visualizzando gli eventi di controllo delle modifiche apportate alla directory di Azure AD.

> [!IMPORTANT]
> Il monitoraggio di Azure AD consente di instradare i log generati dai report di Azure AD a sistemi di destinazione diversi. dove sarà poi possibile conservarli per l'uso a lungo termine o integrarli con strumenti per informazioni di sicurezza e gestione degli eventi di terze parti per ottenere informazioni dettagliate sull'ambiente.

Con il monitoraggio di Azure AD è possibile instradare i log a:With Azure AD monitoring, you can route logs to:

* un account di archiviazione di Azure per scopi di archiviazione.
* Log di Monitoraggio di Azure, precedentemente noti come area di lavoro di Azure Log Analytics, in cui è possibile analizzare i dati, creare dashboard e avvisare su eventi specifici.
* un hub eventi di Azure in cui è possibile integrare con gli strumenti SIEM esistenti, ad esempio Splunk, Sumologic o QRadar.

> [!NOTE]
Di recente abbiamo iniziato a usare il termine log di Monitoraggio di Azure anziché Log Analytics.We recently started using the term Azure Monitor logs instead of Log Analytics. I dati di log vengono comunque archiviati in un'area di lavoro Log Analytics e vengano ancora raccolti e analizzati dallo stesso servizio Log Analytics. Si sta procedendo a un aggiornamento della terminologia per riflettere meglio il ruolo dei [log in Monitoraggio di Azure](https://docs.microsoft.com/azure/azure-monitor/platform/data-collection). Per informazioni dettagliate, vedere [Modifiche della terminologia di Monitoraggio di Azure](https://docs.microsoft.com/azure/azure-monitor/azure-monitor-rebrand).

[Ulteriori informazioni sui criteri](https://docs.microsoft.com/azure/active-directory/reports-monitoring/reference-reports-data-retention)di conservazione dei report .

### <a name="licensing-and-prerequisites-for-azure-ad-reporting-and-monitoring"></a>Licenze e prerequisiti per la creazione di report e il monitoraggio di Azure AD

Per accedere ai log di accesso di Azure AD è necessaria una licenza Premium di Azure AD.

Per informazioni dettagliate sulle funzionalità e sulle licenze nella [guida ai prezzi di Azure Active Directory.](https://azure.microsoft.com/pricing/details/active-directory/)

Per distribuire il monitoraggio e la creazione di report di Azure AD, è necessario un utente amministratore globale o amministratore della sicurezza per il tenant di Azure AD.

A seconda della destinazione finale dei dati di log, è necessario uno dei seguenti:

* Un account di archiviazione di Azure, con autorizzazioni ListKeys. È consigliabile usare un account di archiviazione generale invece di un account di archiviazione BLOB. Per informazioni sui prezzi per l'archiviazione, vedere il [calcolatore prezzi di Archiviazione di Azure](https://azure.microsoft.com/pricing/calculator/?service=storage).

* Uno spazio dei nomi Hub eventi di Azure da integrare con soluzioni SIEM di terze parti.

* Un'area di lavoro Azure Log Analytics per inviare log a log di Monitoraggio di Azure.

## <a name="plan-an-azure-reporting-and-monitoring-deployment-project"></a>Pianificare un progetto di distribuzione di report e monitoraggio di AzurePlan an Azure reporting and monitoring deployment project

In questo progetto verranno definiti i gruppi di destinatari che utilizzeranno e monitoreranno i report e si definirà l'architettura di monitoraggio di Azure AD.

### <a name="engage-the-right-stakeholders"></a>Coinvolgi le parti interessate giuste

Quando i progetti tecnologici falliscono, in genere lo fanno a causa di aspettative non corrispondenti su impatto, risultati e responsabilità. Per evitare queste insidie, [assicurati di coinvolgere le parti interessate giuste.](https://aka.ms/deploymentplans) Assicurarsi inoltre che i ruoli delle parti interessate nel progetto siano ben compresi documentando le parti interessate e il loro contributo e responsabilità al progetto.

### <a name="plan-communications"></a>Pianificare le comunicazioni

La comunicazione è fondamentale per il successo di ogni nuovo servizio. Comunicare in modo proattivo con gli utenti come cambierà la loro esperienza, quando cambierà e come ottenere supporto in caso di problemi.

### <a name="document-your-current-infrastructure-and-policies"></a>Documentare l'infrastruttura e i criteri correnti

L'infrastruttura e i criteri correnti guideranno la progettazione dei report e del monitoraggio. Assicurarsi di sapere

* Quali sono gli eventuali strumenti SIEM in uso.

* L'infrastruttura di Azure, inclusi gli account di archiviazione esistenti e il monitoraggio in uso.

* I criteri di conservazione dell'organizzazione per i log, inclusi eventuali framework di conformità applicabili necessari. 

## <a name="plan-an-azure-ad-reporting-and-monitoring-deployment"></a>Pianificare una distribuzione di report e monitoraggio di Azure ADPlan an Azure AD reporting and monitoring deployment

La creazione di report e il monitoraggio vengono utilizzati per soddisfare i requisiti aziendali, ottenere informazioni dettagliate sui modelli di utilizzo e aumentare la sicurezza dell'organizzazione.

### <a name="business-use-cases"></a>Casi d'uso aziendali

* Necessario per la soluzione per soddisfare le esigenze aziendali
* Bello dover soddisfare le esigenze aziendali
* Non applicabile

|Area |Descrizione |
|-|-|
|Conservazione| **Registra la conservazione di più di 30 giorni**. A causa dei requisiti legali o aziendali, è necessario archiviare i log di controllo e accedere ai log di Azure AD più di 30 giorni. |
|Analytics| **I registri devono essere ricercabili**. I registri memorizzati devono essere ricercabili con strumenti analitici. |
| Operational Insights| **Approfondimenti per vari team**. La necessità di fornire l'accesso a utenti diversi per ottenere informazioni operative, ad esempio l'utilizzo dell'applicazione, gli errori di accesso, l'utilizzo self-service, le tendenze e così via. |
| Approfondimenti sulla sicurezza| **Approfondimenti per vari team**. La necessità di fornire l'accesso a utenti diversi per ottenere informazioni operative, ad esempio l'utilizzo dell'applicazione, gli errori di accesso, l'utilizzo self-service, le tendenze e così via. |
| Integrazione nei sistemi SIEM      | **Integrazione SIEM**. La necessità di integrare e trasmettere i log di accesso e i log di controllo di Azure AD ai sistemi SIEM esistenti. |

### <a name="choose-a-monitoring-solution-architecture"></a>Scegliere un'architettura di soluzione di monitoraggioChoose a monitoring solution architecture

Con il monitoraggio di Azure AD è possibile instradare i log attività di Azure AD a un sistema che meglio soddisfa le esigenze aziendali. È quindi possibile conservarli per la creazione di report e l'analisi a lungo termine per ottenere informazioni dettagliate sull'ambiente e integrarli con gli strumenti SIEM.

#### <a name="decision-flow-chartan-image-showing-what-is-described-in-subsequent-sections"></a>Diagramma di flusso delle decisioni![Immagine che mostra ciò che viene descritto nelle sezioni successive](media/reporting-deployment-plan/deploy-reporting-flow-diagram.png)

#### <a name="archive-logs-in-a-storage-account"></a>Archiviare i log in un account di archiviazioneArchive logs in a storage account

Instradando i log a un account di archiviazione di Azure, è possibile mantenerli per un periodo di tempo superiore al periodo di conservazione predefinito descritto nei criteri di [conservazione.](https://docs.microsoft.com/azure/active-directory/reports-monitoring/reference-reports-data-retention) Utilizzare questo metodo se è necessario archiviare i log, ma non è necessario integrarli con un sistema SIEM e non sono necessarie query e analisi in corso. È comunque possibile eseguire ricerche su richiesta.

Informazioni su come [indirizzare i dati all'account di archiviazione](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-azure-monitor-route-logs-to-storage-account).

#### <a name="send-logs-to-azure-monitor-logs"></a>Inviare log ai log di Monitoraggio di Azure

[I log](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview) di Monitoraggio di Azure consolidano i dati di monitoraggio da origini diverse. Fornisce inoltre un motore di analisi e linguaggio di query che fornisce informazioni dettagliate sul funzionamento delle applicazioni e sull'utilizzo delle risorse. Inviando i log attività di Azure AD ai log di Monitoraggio di Azure, è possibile recuperare, monitorare e avvisare rapidamente i dati raccolti. Utilizzare questo metodo quando non si dispone di una soluzione SIEM esistente a cui si desidera inviare i dati direttamente, ma si desidera che le query e l'analisi. Dopo che i dati si trova nei log di Monitoraggio di Azure, è possibile inviarli all'hub eventi e da lì a un SIEM, se lo si desidera.

Vedere come [inviare dati ai log di Monitoraggio di Azure](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics).

È anche possibile installare le visualizzazioni predefinite per i log attività di Azure AD per monitorare scenari comuni relativi agli eventi di accesso e controllo.

Informazioni su come [installare e usare le visualizzazioni](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-install-use-log-analytics-views)di analisi dei log per i log attività di Azure AD.

#### <a name="stream-logs-to-your-azure-event-hub"></a>Eseguire il flusso dei log nell'hub eventi di AzureStream logs to your Azure event hub

Il routing dei log a un hub eventi di Azure consente l'integrazione con strumenti SIEM di terze parti. Questa integrazione consente di combinare i dati di log attività di Azure AD con altri dati gestiti dallo strumento per informazioni di sicurezza e gestione degli eventi, per fornire informazioni dettagliate più estese per il proprio ambiente. 

Informazioni su come [trasmettere i log a un hub eventi](https://docs.microsoft.com//azure/active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub).

## <a name="plan-operations-and-security-for-azure-ad-reporting-and-monitoring"></a>Pianificare la creazione di report e il monitoraggio delle operazioni e della sicurezza per azure ADPlan Operations and Security for Azure AD reporting and monitoring

Gli stakeholder devono accedere ai log di Azure AD per ottenere informazioni operative. Gli utenti probabilmente includono membri del team di sicurezza, revisori interni o esterni e il team operativo di gestione di identità e accessi.

I ruoli di Azure AD consentono di delegare la possibilità di configurare e visualizzare i report di Azure AD in base al ruolo. Identificare chi nell'organizzazione ha bisogno dell'autorizzazione per leggere i report di Azure AD e quale ruolo sarebbe appropriato per loro. 

I ruoli seguenti possono leggere i report di Azure AD:The following roles can read Azure AD reports:

* Amministratore globale

* Amministrazione della protezione

* Ruolo con autorizzazioni di lettura per la sicurezza

* Lettore di report

Ulteriori informazioni sui [ruoli amministrativi](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal)di Azure AD .

*Applicare sempre il concetto di privilegi minimi per ridurre il rischio di compromissione di un account.* Valutare la possibilità di implementare [La gestione delle identità con privilegi](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure) per proteggere ulteriormente l'organizzazione.

##  

## <a name="deploy-azure-ad-reporting-and-monitoring"></a>Distribuire report e monitoraggio di Azure ADDeploy Azure AD reporting and monitoring

A seconda delle decisioni prese in precedenza utilizzando le linee guida di progettazione precedenti, questa sezione ti guiderà alla documentazione sulle diverse opzioni di distribuzione.

### <a name="consume-and-archive-azure-ad-logs"></a>Usare e archiviare i log di Azure ADConsume and archive Azure AD logs

[Trovare i report attività nel portale di Azure](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-find-activity-reports)

[Archiviare i log di Azure AD in un account di archiviazione di AzureArchive Azure AD logs to an Azure Storage account](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-azure-monitor-route-logs-to-storage-account)

### <a name="implement-monitoring-and-analytics"></a>Implementare il monitoraggio e l'analisi

[Inviare log a Monitoraggio di AzureSend logs to Azure Monitor](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

[Installare e usare le visualizzazioni di analisi dei log per Azure Active DirectoryInstall and use the log analytics views for Azure Active Directory](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-install-use-log-analytics-views)

[Analizzare i log attività di Azure AD con i log di Monitoraggio di Azure](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-analyze-activity-logs-log-analytics)

* [Interpretare lo schema dei log di controllo in Monitoraggio di AzureInterpret audit logs schema in Azure Monitor](https://docs.microsoft.com/azure/active-directory/reports-monitoring/reference-azure-monitor-audit-log-schema)

* [Interpretare lo schema dei log di accesso in Monitoraggio di AzureInterpret sign in sign in log schema in Azure Monitor](https://docs.microsoft.com/azure/active-directory/reports-monitoring/reference-azure-monitor-sign-ins-log-schema)

 * [Trasmettere i log di Azure AD a un hub eventi di AzureStream Azure AD logs to an Azure event hub](https://docs.microsoft.com/azure/active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub)

* [Integrare i log di Azure AD con Splunk usando Monitoraggio di Azure](https://docs.microsoft.com/azure/active-directory/reports-monitoring/tutorial-integrate-activity-logs-with-splunk)

* [Integrate Azure AD logs with SumoLogic by using Azure Monitor](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-sumologic) (Integrare i log di Azure AD con SumoLogic usando Monitoraggio di Azure)

 

 

## <a name="next-steps"></a>Passaggi successivi

Valutare la possibilità di implementare la gestione delle [identità con privilegiConsider](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure) implementing Privileged Identity Management 

Prendere in considerazione l'implementazione del [controllo degli accessi in base](https://docs.microsoft.com/azure/role-based-access-control/overview) al ruolo

 
