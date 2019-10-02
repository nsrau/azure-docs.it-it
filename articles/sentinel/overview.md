---
title: Che cos'è Azure Sentinel? | Microsoft Docs
description: Informazioni su Azure Sentinel, le funzionalità principali e il funzionamento.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: 10cce91a-421b-4959-acdf-7177d261f6f2
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: overview
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/24/2019
ms.author: rkarlin
ms.openlocfilehash: 964ba16fb7421c58f43d8b7d0d0d96f5cd377579
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2019
ms.locfileid: "71261873"
---
# <a name="what-is-azure-sentinel"></a>Che cos'è Azure Sentinel?

Microsoft Azure Sentinel è una soluzione di tipo **SIEM (Security Information and Event Management)** e **SOAR (Security Orchestration, Automation and Response)** scalabile e nativa del cloud. Azure Sentinel offre analisi della sicurezza intelligenti e intelligence sulle minacce per l'intera azienda, fornendo un'unica soluzione per il rilevamento degli avvisi, la visibilità delle minacce, la ricerca proattiva e la risposta alle minacce. 

Azure Sentinel offre una vista a colpo d'occhio sull'azienda, alleviando il carico associato ad attacchi sempre più sofisticati, volume crescente degli avvisi e lunghi tempi di risoluzione.

- **Raccoglie i dati su scala cloud** per tutti gli utenti, i dispositivi, le applicazioni e l'infrastruttura, in locale e in più cloud. 

- **Rileva minacce precedentemente non individuate** e riduce al minimo i falsi positivi usando le sofisticate funzionalità di analisi e intelligence sulle minacce di Microsoft. 

- **Analizza le minacce con l'intelligenza artificiale** e individua le attività sospette su larga scala, sfruttando i vantaggi di anni di esperienza di Microsoft a livello di cybersecurity. 

- **Risponde rapidamente agli eventi imprevisti** con orchestrazione e automazione integrate delle attività comuni.


![Funzionalità principali di Azure Sentinel](./media/overview/core-capabilities.png)

Basato sull'intera gamma di servizi di Azure esistenti, Azure Sentinel incorpora in modo nativo servizi collaudati come Log Analytics e App per la logica. Azure Sentinel arricchisce l'indagine e il rilevamento con l'intelligenza artificiale e fornisce il flusso di Threat Intelligence di Microsoft, consentendo di aggiungere anche la propria intelligence sulle minacce. 

 
## <a name="connect-to-all-your-data"></a>Connessione a tutte le origini dati

Per eseguire l'onboarding Azure Sentinel, è necessario innanzitutto [connettersi alle origini di sicurezza](connect-data-sources.md). Azure Sentinel include numerosi connettori predefiniti per l'integrazione in tempo reale con le soluzioni Microsoft, comprese le soluzioni Microsoft Threat Protection e origini Microsoft 365, tra cui Office 365, Azure AD, Azure ATP, Microsoft Cloud App Security e altre ancora. Inoltre, sono presenti connettori predefiniti a soluzioni non Microsoft, per l'ecosistema di sicurezza allargato. Per connettere le origini dati con Azure Sentinel è anche possibile usare Common Event Format, Syslog o API REST.  

![Agenti di raccolta dati](./media/collect-data/collect-data-page.png)

## <a name="workbooks"></a>Workbooks

Dopo aver [connesso le origini dati](quickstart-onboard.md) ad Azure Sentinel, è possibile monitorare i dati usando l'integrazione di Azure Sentinel con Cartelle di lavoro di Monitoraggio di Azure, che consente di creare cartelle di lavoro personalizzate in modo versatile. Anche se le cartelle di lavoro sono visualizzate in modo diverso in Azure Sentinel, può essere utile vedere come [creare report interattivi con Cartelle di lavoro di Monitoraggio di Azure](../azure-monitor/app/usage-workbooks.md). Azure Sentinel consente di creare cartelle di lavoro personalizzate tra i dati e include anche modelli di cartella di lavoro predefiniti per ottenere rapidamente informazioni dettagliate sui dati non appena si connette un'origine dati.

![Dashboard](./media/tutorial-monitor-data/access-workbooks.png)

## <a name="analytics"></a>Analytics

Per ridurre i risultati non significativi e minimizzare il numero di avvisi da esaminare e di cui ricercare le cause, Azure Sentinel usa [l'analisi per correlare gli avvisi in eventi imprevisti](tutorial-detect-threats-built-in.md). Gli **eventi imprevisti** sono gruppi di avvisi correlati che insieme creano una possibile minaccia di utilità pratica ai fini delle indagini e della risoluzione. È possibile usare le regole di correlazione predefinite così come sono o come punto di partenza per crearne di personalizzate. Azure Sentinel fornisce anche regole di Machine Learning per mappare il comportamento di rete e quindi cercare le anomalie in tutte le risorse. Queste funzionalità di analisi uniscono i puntini, combinando gli avvisi con un basso livello di affidabilità relativi a entità diverse in potenziali eventi imprevisti della sicurezza di affidabilità elevata.

![Eventi imprevisti](./media/tutorial-investigate-cases/incident-severity.png)


## <a name="security-automation--orchestration"></a>Automazione e orchestrazione della sicurezza

È possibile automatizzare le attività comuni e [semplificare l'orchestrazione della sicurezza con playbook](tutorial-respond-threats-playbook.md) che si integrano con i servizi di Azure e con gli strumenti esistenti. Basata su App per la logica di Azure, la soluzione di automazione e orchestrazione di Azure Sentinel fornisce un'architettura altamente estendibile, che rende possibile ridimensionare l'automazione man mano che emergono nuove tecnologie e minacce. Per compilare playbook con App per la logica di Azure, è possibile scegliere da una raccolta in costante crescita di playbook predefiniti. Questi includono [oltre 200 connettori](https://docs.microsoft.com/azure/connectors/apis-list) per servizi, ad esempio Funzioni di Azure. I connettori consentono di applicare qualsiasi logica personalizzata in codice, ServiceNow, Jira, Zendesk, richieste HTTP, Microsoft Teams, Slack, Windows Defender ATP e Cloud App Security.

Ad esempio, se si usa il sistema di creazione dei ticket ServiceNow, è possibile usare gli strumenti forniti da App per la logica di Azure per automatizzare i flussi di lavoro e aprire un ticket in ServiceNow ogni volta che viene rilevato un evento specifico.

![Playbook](./media/tutorial-respond-threats-playbook/logic-app.png)


## <a name="investigation"></a>Analisi

Attualmente disponibili in anteprima, gli strumenti di [indagine approfondita](tutorial-investigate-cases.md) di Azure Sentinel sono utili per comprendere l'ambito e individuare la causa radice di una potenziale minaccia per la sicurezza. È possibile scegliere un'entità nel grafico interattivo per porre domande interessanti per una specifica entità, oltre che eseguire il drill-down nell'entità e nelle relative connessioni per risalire alla causa radice della minaccia. 

![Analisi](./media/tutorial-investigate-cases/map-timeline.png)


## <a name="hunting"></a>Ricerca

Usando i [potenti strumenti di ricerca e query](hunting.md) di Azure Sentinel, basati sul framework MITRE, è possibile cercare in modo proattivo le minacce alla sicurezza tra le origini dati dell'organizzazione prima che venga attivato un avviso. Dopo aver individuato la query di ricerca che fornisce informazioni cognitive dettagliate di valore elevato sui possibili attacchi, è anche possibile creare regole di rilevamento personalizzate basate sulla query e presentare queste informazioni sotto forma di avvisi al personale addetto a rispondere agli eventi imprevisti di sicurezza. Durante la ricerca si possono creare segnalibri per gli eventi interessanti, per potervi tornare in seguito, condividerli con altri utenti e raggrupparli con altri eventi correlati per creare un evento imprevisto interessante da sottoporre a indagine.

![Ricerca](./media/overview/hunting.png)

## <a name="community"></a>Community

La community di Azure Sentinel è una risorsa potente per il rilevamento delle minacce e l'automazione. Gli analisti della sicurezza di Microsoft creano e aggiungono continuamente nuove cartelle di lavoro, playbook, query di ricerca e altro ancora, pubblicandoli nella community perché ognuno possa usarli nel proprio ambiente. È possibile scaricare contenuto di esempio dal [repository](https://aka.ms/asicommunity) GitHub privato della community, per creare cartelle di lavoro, query di ricerca, notebook e playbook personalizzati per Azure Sentinel. 

![Community](./media/overview/community.png)

## <a name="next-steps"></a>Passaggi successivi

- Per iniziare a usare Azure Sentinel, è necessario avere una sottoscrizione di Microsoft Azure. Se non si ha una sottoscrizione, è possibile iscriversi per una [versione di valutazione gratuita](https://azure.microsoft.com/free/).
- Informazioni su come [caricare i dati in Azure Sentinel](quickstart-onboard.md) e [ottenere visibilità sui dati e sulle potenziali minacce](quickstart-get-visibility.md).
