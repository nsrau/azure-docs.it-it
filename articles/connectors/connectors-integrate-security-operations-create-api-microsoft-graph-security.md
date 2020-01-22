---
title: Integrare e gestire le operazioni di sicurezza & Microsoft Graph sicurezza
description: Migliorare la protezione, il rilevamento e la risposta alle minacce dell'app con Microsoft Graph sicurezza & app per la logica di Azure
services: logic-apps
ms.suite: integration
author: preetikr
ms.author: preetikr
ms.reviewer: klam, estfan, logicappspm
ms.topic: article
ms.date: 12/12/2019
tags: connectors
ms.openlocfilehash: f9aa88934d67d98fce43763c6c8fac7c384d765d
ms.sourcegitcommit: a9b1f7d5111cb07e3462973eb607ff1e512bc407
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/22/2020
ms.locfileid: "76313791"
---
# <a name="improve-threat-protection-by-integrating-security-operations-with-microsoft-graph-security--azure-logic-apps"></a>Migliorare la protezione dalle minacce integrando le operazioni di sicurezza con Microsoft Graph Security e le app per la logica di Azure

Con le [app per la logica di Azure](../logic-apps/logic-apps-overview.md) e il connettore di [Microsoft Graph Security](https://docs.microsoft.com/graph/security-concept-overview) è possibile migliorare le modalità di rilevamento, protezione e risposta alle minacce mediante la creazione di flussi di lavoro automatizzati per l'integrazione di prodotti, servizi e partner Microsoft per la sicurezza. È ad esempio possibile creare [playbook per Centro sicurezza di Azure](../security-center/security-center-playbooks.md) che monitorano e gestiscono entità di Microsoft Graph Security come gli avvisi. Di seguito sono riportati alcuni scenari supportati dal connettore di sicurezza Microsoft Graph:

* Ottenere avvisi in base a query o all'ID avviso. È ad esempio possibile ottenere un elenco degli avvisi con gravità alta.

* Aggiornare gli avvisi. È ad esempio possibile aggiornare le assegnazioni degli avvisi, aggiungere commenti agli avvisi o contrassegnarli.

* Monitorare la creazione o la modifica degli avvisi creando [sottoscrizioni per gli avvisi (webhook)](https://docs.microsoft.com/graph/api/resources/webhooks).

* Gestire le sottoscrizioni per gli avvisi. È ad esempio possibile ottenere le sottoscrizioni attive, estendere la scadenza per una sottoscrizione o eliminare le sottoscrizioni.

Il flusso di lavoro dell'app per la logica può usare azioni per ottenere risposte dal connettore di Microsoft Graph Security e rendere disponibile l'output per altre azioni nel flusso di lavoro. L'output delle azioni del connettore di Microsoft Graph Security può anche essere usato da altre azioni nel flusso di lavoro. Se ad esempio si ricevono avvisi con gravità alta tramite il connettore di Microsoft Graph Security, è possibile inviare tali avvisi in un messaggio di posta elettronica mediante il connettore di Outlook. 

Per altre informazioni su Microsoft Graph Security, vedere [Microsoft Graph Security API overview](https://aka.ms/graphsecuritydocs) (Panoramica dell'API di Microsoft Graph Security). Se non si ha familiarità con le app per la logica, consultare [Informazioni su App per la logica di Azure](../logic-apps/logic-apps-overview.md). Se si sta cercando Microsoft Flow o PowerApps, vedere [che cos'è Flow?](https://flow.microsoft.com/) o [che cos'è PowerApps?](https://powerapps.microsoft.com/)

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, [iscriversi per creare un account Azure gratuito](https://azure.microsoft.com/free/). 

* Per usare il connettore di Microsoft Graph Security, è necessario *fornire in modo esplicito* il consenso dell'amministratore del tenant di Azure Active Directory (AD), come previsto dai [requisiti di autenticazione di Microsoft Graph Security](https://aka.ms/graphsecurityauth). Questo consenso richiede l'ID applicazione e il nome del connettore di Microsoft Graph Security, che è anche possibile trovare nel [portale di Azure](https://portal.azure.com):

  | Proprietà | Valore |
  |----------|-------|
  | **Nome dell'applicazione** | `MicrosoftGraphSecurityConnector` |
  | **ID applicazione** | `c4829704-0edc-4c3d-a347-7c4a67586f3c` |
  |||

  Per concedere il consenso per il connettore, l'amministratore tenant di Azure AD può seguire questa procedura:

  * [Fornire il consenso dell'amministratore del tenant per le applicazioni di AD Azure](../active-directory/develop/v2-permissions-and-consent.md).

  * Durante la prima esecuzione dell'app per la logica, l'app può richiedere il consenso dell'amministratore del tenant di Azure AD tramite l'[esperienza di consenso per le applicazioni](../active-directory/develop/application-consent-experience.md).
   
* Conoscenza di base di [come creare le app per la logica](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* L'app per la logica in cui si vuole accedere alle entità di Microsoft Graph Security, ad esempio gli avvisi. Per usare un trigger di sicurezza Microsoft Graph, è necessaria un'app per la logica vuota. Per usare un'azione di sicurezza Microsoft Graph, è necessaria un'app per la logica che inizia con il trigger appropriato per lo scenario.

## <a name="connect-to-microsoft-graph-security"></a>Connettersi a Microsoft Graph Security 

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Accedere al [portale di Azure](https://portal.azure.com/) e aprire l'app per la logica in Progettazione app per la logica, se non è già aperta.

1. Per le app per la logica vuote, aggiungere il trigger e tutte le altre azioni desiderate prima di aggiungere un'azione di sicurezza Microsoft Graph.

   -oppure-

   Per le app per la logica esistenti, nell'ultimo passaggio in cui si vuole aggiungere un'azione di sicurezza Microsoft Graph selezionare **nuovo passaggio**.

   -oppure-

   Per aggiungere un'azione tra i passaggi, spostare il puntatore del mouse sulla freccia tra i passaggi. Selezionare il segno più (+) visualizzato e selezionare **Aggiungi un'azione**.

1. Nella casella di ricerca immettere "microsoft graph security" come filtro. Nell'elenco delle azioni scegliere l'azione desiderata.

1. Accedere con le credenziali di Microsoft Graph Security.

1. Specificare i dettagli necessari per l'azione selezionata e continuare a creare il flusso di lavoro dell'app per la logica.

## <a name="add-triggers"></a>Aggiungi trigger

In App per la logica di Azure, ogni app per la logica deve essere avviata con un [trigger](../logic-apps/logic-apps-overview.md#logic-app-concepts), che viene attivato quando si verifica un determinato evento o quando viene soddisfatta una condizione specifica. Ogni volta che il trigger viene attivato, il motore app per la logica crea un'istanza dell'app per la logica e avvia l'esecuzione del flusso di lavoro dell'app.

> [!NOTE] 
> Quando viene attivato un trigger, il trigger elabora tutti i nuovi avvisi. Se non vengono ricevuti avvisi, l'esecuzione del trigger viene ignorata. Il polling successivo si baserà sull'intervallo di ricorrenza specificato nelle proprietà del trigger.

Questo esempio illustra come è possibile avviare un flusso di lavoro dell'app per la logica quando vengono inviati nuovi avvisi all'app.

1.  Nel portale di Azure o in Visual Studio creare un'app per la logica vuota, che apre la finestra di progettazione dell'app per la logica. Questo esempio usa il portale di Azure.

1.  Nella casella di ricerca della finestra di progettazione immettere "sicurezza Microsoft Graph" come filtro. Dall'elenco trigger selezionare questo trigger: **in tutti i nuovi avvisi**

1.  Nel trigger fornire le informazioni sugli avvisi che si desidera monitorare. Per ulteriori proprietà, aprire l'elenco **Aggiungi nuovo parametro** e selezionare un parametro per aggiungere tale proprietà al trigger.

   | Proprietà | Property (JSON) | Obbligatorio | Tipo | Description |
   |----------|-----------------|----------|------|-------------|
   | **Interval** | `interval` | Sì | Integer | Numero intero positivo che indica l'intervallo con cui viene eseguito il flusso di lavoro in base alla frequenza. Ecco gli intervalli minimo e massimo: <p><p>- Mese: 1-16 mesi <br>- Giorno: 1-500 giorni <br>- Ora: 1-12.000 ore <br>- Minuto: 1-72.000 minuti <br>- Secondo: 1-9.999.999 secondi <p>Ad esempio, se l'intervallo è 6 e la frequenza è "Mese", la ricorrenza è ogni 6 mesi. |
   | **Frequenza** | `frequency` | Sì | string | L'unità di tempo per la ricorrenza: **Secondo**, **Minuto**, **Ora**, **Giorno**, **Settimana** o **Mese** |
   | **Fuso orario** | `timeZone` | No | string | Valido solo quando si specifica un'ora di inizio, perché il trigger non accetta la [differenza dall'ora UTC](https://en.wikipedia.org/wiki/UTC_offset). Selezionare il fuso orario che si desidera applicare. |
   | **Ora di inizio** | `startTime` | No | string | Specificare una data e un'ora di inizio nel formato seguente: <p><p>AAAA-MM-GGThh:mm:ss se si seleziona un fuso orario <p>-oppure- <p>AAAA-MM-GGThh:mm:ssZ se non si seleziona un fuso orario <p>Ad esempio, se si desidera il 18 settembre 2017 alle 2:00 PM, specificare "2017-09-18T14:00:00" e selezionare un fuso orario, ad esempio ora solare Pacifico. In alternativa, specificare "2017-09-18T14:00:00Z" senza un fuso orario. <p>**Nota:** Questa ora di inizio ha un numero massimo di 49 anni nel futuro e deve seguire la [specifica di data e ora ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) nel [formato di data e ora UTC](https://en.wikipedia.org/wiki/Coordinated_Universal_Time), ma senza [offset UTC](https://en.wikipedia.org/wiki/UTC_offset). Se non si seleziona un fuso orario, è necessario aggiungere la lettera "Z" alla fine, senza spazi. La lettera "Z" fa riferimento all'[ora nautica](https://en.wikipedia.org/wiki/Nautical_time) equivalente. <p>Per le pianificazioni semplici, l'ora di inizio è la prima occorrenza, mentre per le pianificazioni complesse il trigger non viene attivato prima dell'ora di inizio. [*In quali modi posso usare la data e l'ora di inizio?* ](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#start-time) |
   ||||||

1.  Al termine, fare clic su **Salva**nella barra degli strumenti della finestra di progettazione.

1.  Continuare ad aggiungere una o più azioni all'app per la logica per le attività da eseguire con i risultati del trigger.

## <a name="add-actions"></a>Aggiunta di azioni

Di seguito sono riportate informazioni più dettagliate sull'uso delle varie azioni disponibili con il connettore di Microsoft Graph Security.

### <a name="manage-alerts"></a>Gestisci avvisi

Per filtrare, ordinare oppure ottenere i risultati più recenti, fornire *solo* i [parametri di query ODATA supportati da Microsoft Graph](https://docs.microsoft.com/graph/query-parameters). *Non specificare* l'URL di base completo o l'azione HTTP, ad esempio `https://graph.microsoft.com/v1.0/security/alerts` o l'operazione `GET` o `PATCH`. Ecco un esempio specifico che illustra i parametri per un'azione **Ottieni avvisi** quando si vuole un elenco degli avvisi con gravità alta:

`Filter alerts value as Severity eq 'high'`

Per altre informazioni sulle query che è possibile usare con questo connettore, vedere la [documentazione di riferimento sugli avvisi di Microsoft Graph Security](https://docs.microsoft.com/graph/api/alert-list). Per creare esperienze ottimizzate con questo connettore, consultare altre informazioni sugli [avvisi delle proprietà dello schema](https://docs.microsoft.com/graph/api/resources/alert) supportati dal connettore.

| Azione | Description |
|--------|-------------|
| **Ottieni avvisi** | Ottenere gli avvisi filtrati in base a una o più [proprietà degli avvisi](https://docs.microsoft.com/graph/api/resources/alert), ad esempio: <p>`Provider eq 'Azure Security Center' or 'Palo Alto Networks'` | 
| **Get alert by ID** (Ottieni avviso in base all'ID) | Ottenere un avviso specifico in base all'ID dell'avviso. | 
| **Aggiorna avviso** | Aggiornare un avviso specifico in base all'ID dell'avviso. <p>Per assicurarsi di passare le proprietà obbligatorie e modificabili nella richiesta, vedere le [proprietà modificabili per gli avvisi](https://docs.microsoft.com/graph/api/alert-update). Ad esempio, per assegnare un avviso a un analista della sicurezza in modo che possa svolgere indagini, è possibile aggiornare la proprietà **Assegnato a** dell'avviso. |
|||

### <a name="manage-alert-subscriptions"></a>Gestire le sottoscrizioni per gli avvisi

Microsoft Graph supporta le [*sottoscrizioni*](https://docs.microsoft.com/graph/api/resources/subscription), o [*webhook*](https://docs.microsoft.com/graph/api/resources/webhooks). Per ottenere, aggiornare o eliminare le sottoscrizioni, fornire i [parametri di query ODATA supportati da Microsoft Graph](https://docs.microsoft.com/graph/query-parameters) al costrutto dell'entità di Microsoft Graph e includere `security/alerts` seguito dalla query ODATA. *Non includere* l'URL di base, ad esempio `https://graph.microsoft.com/v1.0`. Usare il formato illustrato in questo esempio:

`security/alerts?$filter=status eq 'New'`

| Azione | Description |
|--------|-------------|
| **Create subscriptions** (Crea sottoscrizioni) | [Creare una sottoscrizione](https://docs.microsoft.com/graph/api/subscription-post-subscriptions) per ricevere notifiche in caso di modifiche. È possibile filtrare la sottoscrizione in base agli specifici tipi di avviso a cui si è interessati. È ad esempio creare una sottoscrizione che invia una notifica in caso di avvisi con gravità alta. |
| **Get active subscriptions** (Ottieni sottoscrizioni attive) | [Ottenere le sottoscrizioni non scadute](https://docs.microsoft.com/graph/api/subscription-list). | 
| **Update subscription** (Aggiorna sottoscrizione) | [Aggiornare una sottoscrizione](https://docs.microsoft.com/graph/api/subscription-update) specificando l'ID sottoscrizione. Ad esempio, per estendere la sottoscrizione, è possibile aggiornare la proprietà `expirationDateTime` della sottoscrizione. | 
| **Delete subscription** (Elimina sottoscrizione) | [Eliminare una sottoscrizione](https://docs.microsoft.com/graph/api/subscription-delete) specificando l'ID sottoscrizione. | 
||| 

## <a name="connector-reference"></a>Informazioni di riferimento sui connettori

Per informazioni tecniche su trigger, azioni e limiti, illustrati dalla descrizione OpenAPI (in precedenza Swagger) del connettore, esaminare la [pagina di riferimento](https://aka.ms/graphsecurityconnectorreference) del connettore.

## <a name="next-steps"></a>Passaggi successivi

Informazioni su altri [connettori di App per la logica](../connectors/apis-list.md)
