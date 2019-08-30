---
title: Pianificare un Azure Active Directory la distribuzione di report e monitoraggio
description: Viene descritto come pianificare ed eseguire implmentation di Reporting e monitoraggio.
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
ms.openlocfilehash: 6d24a4ab74f7957c068fc664fc4e8898c525afcb
ms.sourcegitcommit: 19a821fc95da830437873d9d8e6626ffc5e0e9d6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/29/2019
ms.locfileid: "70162532"
---
# <a name="plan-an-azure-active-directory-reporting-and-monitoring-deployment"></a>Pianificare un Azure Active Directory la distribuzione di report e monitoraggio

La soluzione per la creazione di report e il monitoraggio di Azure Active Directory (Azure AD) dipende dai requisiti legali, di sicurezza e operativi e dall'ambiente e dai processi esistenti. Questo articolo presenta le varie opzioni di progettazione e guida la strategia di distribuzione corretta.

### <a name="benefits-of-azure-ad-reporting-and-monitoring"></a>Vantaggi della creazione di report e del monitoraggio Azure AD

Azure AD Reporting fornisce una visualizzazione completa e i log di Azure AD attività nell'ambiente, inclusi gli eventi di accesso, gli eventi di controllo e le modifiche apportate alla directory.

I dati forniti consentono di:

* determinare il modo in cui vengono usate le app e i servizi.

* rilevare potenziali rischi che interessano l'integrità dell'ambiente.

* risolvere i problemi che impediscono agli utenti di svolgere il proprio lavoro.

* ottenere informazioni dettagliate visualizzando gli eventi di controllo delle modifiche apportate alla directory Azure AD.

> [!IMPORTANT]
> Azure AD monitoraggio consente di instradare i log generati da Azure AD la creazione di report a diversi sistemi di destinazione. dove sarà poi possibile conservarli per l'uso a lungo termine o integrarli con strumenti per informazioni di sicurezza e gestione degli eventi di terze parti per ottenere informazioni dettagliate sull'ambiente.

Con Azure AD monitoraggio, è possibile indirizzare i log a:

* un account di archiviazione di Azure per scopi di archiviazione.
* Log di monitoraggio di Azure, noto in precedenza come area di lavoro di Azure Log Analytics, in cui è possibile analizzare i dati, creare dashboard e inviare avvisi per eventi specifici.
* Hub eventi di Azure in cui è possibile eseguire l'integrazione con gli strumenti SIEM esistenti, ad esempio Splunk, Sumologic o QRadar.

> [!NOTE]
Di recente abbiamo iniziato a usare il termine log di monitoraggio di Azure anziché Log Analytics. I dati di log vengono comunque archiviati in un'area di lavoro Log Analytics e vengano ancora raccolti e analizzati dallo stesso servizio Log Analytics. Si sta procedendo a un aggiornamento della terminologia per riflettere meglio il ruolo dei [log in Monitoraggio di Azure](https://docs.microsoft.com/azure/azure-monitor/platform/data-collection). Per informazioni dettagliate, vedere [Modifiche della terminologia di Monitoraggio di Azure](https://docs.microsoft.com/azure/azure-monitor/azure-monitor-rebrand).

[Altre informazioni sui criteri di conservazione dei report](https://docs.microsoft.com/azure/active-directory/reports-monitoring/reference-reports-data-retention).

### <a name="licensing-and-prerequisites-for-azure-ad-reporting-and-monitoring"></a>Licenze e prerequisiti per la creazione di report e il monitoraggio di Azure AD

Per accedere ai log di accesso Azure AD è necessaria una licenza di Azure AD Premium.

Per informazioni dettagliate sulle funzionalità e sulle licenze, fare [riferimento alla guida ai prezzi Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/).

Per distribuire Azure AD monitoraggio e la creazione di report, è necessario un utente che sia amministratore globale o amministratore della sicurezza per il tenant di Azure AD.

A seconda della destinazione finale dei dati di log, è necessario disporre di uno dei seguenti elementi:

* Un account di archiviazione di Azure per cui si dispone di autorizzazioni di ListKeys. È consigliabile usare un account di archiviazione generale invece di un account di archiviazione BLOB. Per informazioni sui prezzi per l'archiviazione, vedere il [calcolatore prezzi di Archiviazione di Azure](https://azure.microsoft.com/pricing/calculator/?service=storage).

* Uno spazio dei nomi di hub eventi di Azure per l'integrazione con soluzioni SIEM di terze parti.

* Un'area di lavoro Azure Log Analytics per inviare log a log di Monitoraggio di Azure.

## <a name="plan-an-azure-reporting-and-monitoring-deployment-project"></a>Pianificare un progetto di distribuzione di report e monitoraggio di Azure

In questo progetto si definiranno i destinatari che utilizzeranno e monitoreranno i report e definiranno l'architettura di monitoraggio Azure AD.

### <a name="engage-the-right-stakeholders"></a>Coinvolgere gli stakeholder appropriati

Quando i progetti tecnologici hanno esito negativo, in genere lo fanno a causa di una mancata corrispondenza delle aspettative in merito a conseguenze, risultati e responsabilità. Per evitare questi problemi, [assicurarsi di coinvolgere gli stakeholder appropriati](https://aka.ms/deploymentplans). Assicurarsi inoltre che i ruoli stakeholder nel progetto siano ben comprensibili documentando gli stakeholder e i rispettivi input e responsabilità del progetto.

### <a name="plan-communications"></a>Pianificare le comunicazioni

La comunicazione è fondamentale per il successo di un nuovo servizio. Comunica in modo proattivo con gli utenti in che modo cambiano l'esperienza, quando verrà modificata e come ottenere supporto in caso di problemi.

### <a name="document-your-current-infrastructure-and-policies"></a>Documentare l'infrastruttura e i criteri correnti

L'infrastruttura e i criteri correnti guideranno la progettazione per la creazione di report e il monitoraggio. Assicurarsi di essere a conoscenza di

* Quali sono gli strumenti SIEM in uso.

* L'infrastruttura di Azure, inclusi gli account di archiviazione e il monitoraggio esistenti usati.

* Criteri di conservazione dell'organizzazione per i log, inclusi eventuali Framework di conformità applicabili. 

## <a name="plan-an-azure-ad-reporting-and-monitoring-deployment"></a>Pianificare un Azure AD la distribuzione di report e monitoraggio

La creazione di report e il monitoraggio vengono usati per soddisfare i requisiti aziendali, ottenere informazioni approfondite sui modelli di utilizzo e t aumentare il comportamento di sicurezza dell'organizzazione.

### <a name="business-use-cases"></a>Casi d'uso aziendali

* Obbligatorio per la soluzione per soddisfare le esigenze aziendali
* Ideale per soddisfare le esigenze aziendali
* Non applicabile

|Area |DESCRIZIONE |
|-|-|
|Fidelizzazione| **Conservazione dei log di più di 30 giorni**. A causa dei requisiti legali o aziendali, è necessario archiviare i log di controllo e i log di accesso di Azure AD più di 30 giorni. |
|Analisi| **I log devono essere disponibili per la ricerca**. È necessario eseguire ricerche nei log archiviati con gli strumenti di analisi. |
| Operational Insights| **Informazioni dettagliate per diversi team**. La necessità di concedere l'accesso a utenti diversi per ottenere informazioni operative, ad esempio l'utilizzo delle applicazioni, errori di accesso, utilizzo self-service, tendenze e così via. |
| Informazioni dettagliate sulla sicurezza| **Informazioni dettagliate per diversi team**. La necessità di concedere l'accesso a utenti diversi per ottenere informazioni operative, ad esempio l'utilizzo delle applicazioni, errori di accesso, utilizzo self-service, tendenze e così via. |
| Integrazione nei sistemi SIEM      | **Integrazione Siem**. La necessità di integrare e trasmettere in flusso Azure AD log di accesso e i log di controllo ai sistemi SIEM esistenti. |

### <a name="choose-a-monitoring-solution-architecture"></a>Scegliere un'architettura della soluzione di monitoraggio

Con Azure AD monitoraggio, è possibile indirizzare i log attività Azure AD a un sistema che soddisfi al meglio le esigenze aziendali. È quindi possibile mantenerli per l'analisi e la creazione di report a lungo termine per ottenere informazioni approfondite sull'ambiente e integrarlo con gli strumenti SIEM.

#### <a name="decision-flow-chartan-image-showing-what-is-described-in-subsequent-sectionsmediareporting-deployment-plandeploy-reporting-flow-diagrampng"></a>Diagramma del flusso decisionale![Immagine che Mostra gli elementi descritti nelle sezioni successive](media/reporting-deployment-plan/deploy-reporting-flow-diagram.png)

#### <a name="archive-logs-in-a-storage-account"></a>Archiviare i log in un account di archiviazione

Con il routing dei log a un account di archiviazione di Azure, è possibile mantenerli per più tempo del periodo di conservazione predefinito indicato nei [criteri di conservazione](https://docs.microsoft.com/azure/active-directory/reports-monitoring/reference-reports-data-retention). Usare questo metodo se è necessario archiviare i log, ma non è necessario integrarli con un sistema SIEM e non è necessaria l'analisi e le query in corso. È comunque possibile eseguire ricerche su richiesta.

Informazioni su come [indirizzare i dati all'account di archiviazione](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-azure-monitor-route-logs-to-storage-account).

#### <a name="send-logs-to-azure-monitor-logs"></a>Inviare log ai log di Monitoraggio di Azure

I log di monitoraggio di [Azure](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview) consolidano i dati di monitoraggio da origini diverse. Fornisce anche un linguaggio di query e un motore di analisi che offre informazioni dettagliate sul funzionamento delle applicazioni e sull'uso delle risorse. Inviando Azure AD log attività ai log di monitoraggio di Azure, è possibile recuperare, monitorare e avvisare rapidamente i dati raccolti. Utilizzare questo metodo quando non si dispone di una soluzione SIEM esistente a cui si desidera inviare direttamente i dati, ma si desidera eseguire query e analisi. Una volta che i dati sono presenti nei log di monitoraggio di Azure, è possibile inviarli a hub eventi e da qui a un SIEM se lo si desidera.

Vedere come [inviare dati ai log di Monitoraggio di Azure](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics).

È anche possibile installare le visualizzazioni predefinite per Azure AD log attività per monitorare gli scenari comuni che coinvolgono gli eventi di accesso e di controllo.

Vedere come [installare e usare le viste di analisi dei log per i log attività di Azure AD](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-install-use-log-analytics-views).

#### <a name="stream-logs-to-your-azure-event-hub"></a>Eseguire lo streaming dei log nell'hub eventi di Azure

Il routing dei log a un hub eventi di Azure consente l'integrazione con gli strumenti SIEM di terze parti. Questa integrazione consente di combinare i dati di log attività di Azure AD con altri dati gestiti dallo strumento per informazioni di sicurezza e gestione degli eventi, per fornire informazioni dettagliate più estese per il proprio ambiente. 

Informazioni su come [trasmettere i log a un hub eventi](https://docs.microsoft.com//azure/active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub).

## <a name="plan-operations-and-security-for-azure-ad-reporting-and-monitoring"></a>Pianificare operazioni e sicurezza per la creazione di report e il monitoraggio di Azure AD

Gli stakeholder devono accedere ai log Azure AD per ottenere informazioni operative. È probabile che gli utenti includano membri del team di sicurezza, revisori interni o esterni e il team operativo di gestione delle identità e degli accessi.

Azure AD ruoli consentono di delegare la possibilità di configurare e visualizzare Azure AD report in base al ruolo. Identificare gli utenti dell'organizzazione che necessitano dell'autorizzazione per leggere Azure AD report e il ruolo appropriato. 

I ruoli seguenti possono leggere Azure AD report:

* Amministratore globale

* Amministrazione della protezione

* Ruolo con autorizzazioni di lettura per la sicurezza

* Lettore di report

Altre informazioni su [Azure ad ruoli amministrativi](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal).

*Applicare sempre il concetto di privilegi minimi per ridurre il rischio di compromissione di un account*. Prendere in considerazione l'implementazione di [Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure) per proteggere ulteriormente l'organizzazione.

##  

## <a name="deploy-azure-ad-reporting-and-monitoring"></a>Distribuire Azure AD Reporting e il monitoraggio

A seconda delle decisioni prese in precedenza utilizzando le linee guida di progettazione riportate sopra, in questa sezione verrà illustrata la documentazione relativa alle diverse opzioni di distribuzione.

### <a name="consume-and-archive-azure-ad-logs"></a>Utilizzare e archiviare i log di Azure AD

[Trovare i report attività nel portale di Azure](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-find-activity-reports)

[Archiviare i log di Azure AD in un account di archiviazione di Azure](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-azure-monitor-route-logs-to-storage-account)

### <a name="implement-monitoring-and-analytics"></a>Implementare il monitoraggio e l'analisi

[Inviare i log a monitoraggio di Azure](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

[Installare e usare le viste di analisi dei log per Azure Active Directory](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-install-use-log-analytics-views)

[Analizzare i log attività di Azure AD con i log di Monitoraggio di Azure](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-analyze-activity-logs-log-analytics)

* [Interpretare lo schema dei log di controllo in Monitoraggio di Azure](https://docs.microsoft.com/azure/active-directory/reports-monitoring/reference-azure-monitor-audit-log-schema)

* [Interpretare lo schema dei log di accesso in monitoraggio di Azure](https://docs.microsoft.com/azure/active-directory/reports-monitoring/reference-azure-monitor-sign-ins-log-schema)

 * [Trasmettere i log Azure AD a un hub eventi di Azure](https://docs.microsoft.com/azure/active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub)

* [Integrare i log di Azure AD con Splunk usando Monitoraggio di Azure](https://docs.microsoft.com/azure/active-directory/reports-monitoring/tutorial-integrate-activity-logs-with-splunk)

* [Integrate Azure AD logs with SumoLogic by using Azure Monitor](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-sumologic) (Integrare i log di Azure AD con SumoLogic usando Monitoraggio di Azure)

 

 

## <a name="next-steps"></a>Passaggi successivi

Prendere in considerazione l'implementazione di [Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure) 

Considerare l'implementazione del controllo degli accessi in [base al ruolo (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/overview)

 
