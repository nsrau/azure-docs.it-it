<properties
pageTitle="ProjectOnline | Microsoft Azure"
description="Creare app per la logica con il servizio app di Azure. Project Online è una soluzione flessibile online per la gestione del portfolio dei progetti (PPM) e per il lavoro di tutti i giorni offerta da Microsoft. Distribuito tramite Office 365, Project Online consente alle organizzazioni di iniziare a usare da subito potenti funzionalità di gestione per pianificare, classificare in ordine di priorità e gestire progetti e investimenti del portfolio di progetti praticamente ovunque e da qualsiasi dispositivo."
services="app-servicelogic"	
documentationCenter=".net,nodejs,java" 	
authors="msftman"	
manager="erikre"	
editor=""
tags="connectors" />

<tags
ms.service="app-service-logic"
ms.devlang="multiple"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="integration"
ms.date="04/29/2016"
ms.author="deonhe"/>

# Introduzione al connettore ProjectOnline

Project Online è una soluzione flessibile online per la gestione del portfolio dei progetti (PPM) e per il lavoro di tutti i giorni offerta da Microsoft. Distribuito tramite Office 365, Project Online consente alle organizzazioni di iniziare a usare da subito potenti funzionalità di gestione per pianificare, classificare in ordine di priorità e gestire progetti e investimenti del portfolio di progetti praticamente ovunque e da qualsiasi dispositivo.

Il connettore ProjectOnline può essere usato da:

- [App per la logica](../app-service-logic/app-service-logic-what-are-logic-apps.md)  
- [PowerApps](http://powerapps.microsoft.com)  
- [Flusso](http://flow.microsoft.com)  

>[AZURE.NOTE] Questa versione dell'articolo si applica alla versione dello schema 2015-08-01-preview delle app per la logica.

Per iniziare con la creazione di un'app per la logica, vedere l'articolo sulla [creazione di un'app per la logica](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Trigger e azioni

Il connettore ProjectOnline può essere usato come un'azione e dispone di trigger. Tutti i connettori supportano dati nei formati JSON e XML.

 Nel connettore ProjectOnline sono disponibili le azioni e/o i trigger seguenti:

### Azioni ProjectOnline
È possibile eseguire queste azioni:

|Azione|Descrizione|
|--- | ---|
|[ListProjects](connectors-create-api-projectonline.md#listprojects)|Consente di elencare i progetti nel sito del progetto online|
|[CreateProject](connectors-create-api-projectonline.md#createproject)|Consente di creare un nuovo progetto nel sito del progetto online|
|[CreateTask](connectors-create-api-projectonline.md#createtask)|Consente di creare una nuova attività nel progetto|
|[CreateResource](connectors-create-api-projectonline.md#createresource)|Consente di creare risorse per l'organizzazione nel sito del progetto online|
|[ListTasks](connectors-create-api-projectonline.md#listtasks)|Consente di elencare le attività pubblicate in un progetto|
|[CheckoutProject](connectors-create-api-projectonline.md#checkoutproject)|Consente di estrarre un progetto nel sito|
|[PublishProject](connectors-create-api-projectonline.md#publishproject)|Consente di registrare e pubblicare un progetto esistente nel sito|
### Trigger di ProjectOnline
È possibile ascoltare questi eventi:

|Trigger | Descrizione|
|--- | ---|
|Quando viene creato un nuovo progetto|Attiva un flusso ogni volta che viene creato un nuovo progetto|
|Quando viene creata una nuova risorsa|Attiva un nuovo flusso quando viene creata una nuova risorsa|
|Quando viene creata una nuova attività|Attiva un flusso quando viene creata una nuova attività|


## Creare una connessione a ProjectOnline
Per creare app per la logica con ProjectOnline, è necessario creare innanzitutto una **connessione**, quindi fornire i dettagli per le proprietà seguenti:

|Proprietà| Obbligatorio|Descrizione|
| ---|---|---|
|Token|Sì|Fornire le credenziali ProjectOnline|
Dopo aver creato la connessione, è possibile usarla per eseguire le azioni e restare in attesa dei trigger descritti in questo articolo.

>[AZURE.TIP] È possibile usare questa connessione in altre app per la logica.

## Riferimento per ProjectOnline
Si applica alla versione: 1.0

## OnNewProject
Quando viene creato un nuovo progetto: attiva un flusso ogni volta che viene creato un nuovo progetto

```GET: /trigger/_api/ProjectData/Projects```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|siteUrl|string|yes|query|nessuno|Url del sito radice del sito del progetto (esempio: https://sampletenant.sharepoint.com/teams/sampleteam )|

#### Risposta

|Nome|Descrizione|
|---|---|
|200|OK|
|400|Bad Request|
|401|Non autorizzata|
|403|Accesso negato|
|404|Non trovato|
|500|Errore interno del server. Si è verificato un errore sconosciuto|
|default|Operazione non riuscita.|


## OnNewResource
Quando viene creata una nuova risorsa: attiva un nuovo flusso quando viene creata una nuova risorsa

```GET: /trigger/_api/ProjectData/Resources```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|siteUrl|string|yes|query|nessuno|Url del sito radice del sito del progetto (esempio: https://sampletenant.sharepoint.com/teams/sampleteam )|

#### Risposta

|Nome|Descrizione|
|---|---|
|200|OK|
|400|Bad Request|
|401|Non autorizzata|
|403|Accesso negato|
|404|Non trovato|
|500|Errore interno del server. Si è verificato un errore sconosciuto|
|default|Operazione non riuscita.|


## OnNewTask
Quando viene creata una nuova attività: attiva un flusso quando viene creata una nuova attività

```GET: /trigger/_api/ProjectData/Tasks```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|siteUrl|string|yes|query|nessuno|Url del sito radice del sito del progetto (esempio: https://sampletenant.sharepoint.com/teams/sampleteam )|

#### Risposta

|Nome|Descrizione|
|---|---|
|200|OK|
|400|Bad Request|
|401|Non autorizzata|
|403|Accesso negato|
|404|Non trovato|
|500|Errore interno del server. Si è verificato un errore sconosciuto|
|default|Operazione non riuscita.|


## ListProjects
Elenco dei progetti: elenca i progetti nel sito del progetto online

```GET: /_api/ProjectServer/Projects```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|siteUrl|string|yes|query|nessuno|Url del sito radice del sito del progetto (esempio: https://sampletenant.sharepoint.com/teams/sampleteam )|

#### Risposta

|Nome|Descrizione|
|---|---|
|200|OK|
|400|Bad Request|
|401|Non autorizzata|
|403|Accesso negato|
|404|Non trovato|
|500|Errore interno del server. Si è verificato un errore sconosciuto|
|default|Operazione non riuscita.|


## CreateProject
Crea nuovo progetto: crea un nuovo progetto nel sito del progetto online

```POST: /_api/ProjectServer/Projects```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|siteUrl|string|yes|query|nessuno|Url del sito radice del sito del progetto (esempio: https://sampletenant.sharepoint.com/teams/sampleteam )|
|proj| |sì|body|nessuno|Nuovo progetto da creare|

#### Risposta

|Nome|Descrizione|
|---|---|
|200|OK|
|400|Bad Request|
|401|Non autorizzata|
|403|Accesso negato|
|404|Non trovato|
|500|Errore interno del server. Si è verificato un errore sconosciuto|
|default|Operazione non riuscita.|


## CreateTask
Crea una nuova attività: crea una nuova attività nel progetto

```POST: /_api/ProjectServer/Projects('{project_id}')/Draft/Tasks/Add```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|siteUrl|string|yes|query|nessuno|Url del sito radice del sito del progetto (esempio: https://sampletenant.sharepoint.com/teams/sampleteam )|
|project\_id|string|yes|path|nessuno|ID univoco del progetto a cui aggiungere l'attività|
|attività| |sì|body|nessuno|Nuova attività da aggiungere al progetto|

#### Risposta

|Nome|Descrizione|
|---|---|
|200|OK|
|400|Bad Request|
|401|Non autorizzata|
|403|Accesso negato|
|404|Non trovato|
|500|Errore interno del server. Si è verificato un errore sconosciuto|
|default|Operazione non riuscita.|


## CreateResource
Crea una nuova risorsa: crea risorse per l'organizzazione nel sito del progetto online

```POST: /_api/ProjectServer/EnterpriseResources```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|siteUrl|string|yes|query|nessuno|Url del sito radice del sito del progetto (esempio: https://sampletenant.sharepoint.com/teams/sampleteam )|
|resource| |sì|body|nessuno|Nuova risorsa per l'organizzazione da aggiungere al progetto|

#### Risposta

|Nome|Descrizione|
|---|---|
|200|OK|
|400|Bad Request|
|401|Non autorizzata|
|403|Accesso negato|
|404|Non trovato|
|500|Errore interno del server. Si è verificato un errore sconosciuto|
|default|Operazione non riuscita.|


## ListTasks
Elenca le attività: elenca le attività pubblicate in un progetto

```GET: /_api/ProjectServer/Projects('{project_id}')/Tasks```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|siteUrl|string|yes|query|nessuno|Url del sito radice del sito del progetto (esempio: https://sampletenant.sharepoint.com/teams/sampleteam )|
|project\_id|string|yes|path|nessuno|ID univoco del progetto per recuperare le attività|

#### Risposta

|Nome|Descrizione|
|---|---|
|200|OK|
|400|Bad Request|
|401|Non autorizzata|
|403|Accesso negato|
|404|Non trovato|
|500|Errore interno del server. Si è verificato un errore sconosciuto|
|default|Operazione non riuscita.|


## CheckoutProject
Estrae un progetto: consente di estrarre un progetto nel sito

```POST: /_api/ProjectServer/Projects('{project_id}')/checkOut```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|siteUrl|string|yes|query|nessuno|Url del sito radice del sito del progetto (esempio: https://sampletenant.sharepoint.com/teams/sampleteam )|
|project\_id|string|yes|path|nessuno|ID univoco del progetto a cui aggiungere l'attività|

#### Risposta

|Nome|Descrizione|
|---|---|
|200|OK|
|400|Bad Request|
|401|Non autorizzata|
|403|Accesso negato|
|404|Non trovato|
|500|Errore interno del server. Si è verificato un errore sconosciuto|
|default|Operazione non riuscita.|


## PublishProject
Registra e pubblica un progetto: registra e pubblica un progetto esistente nel sito

```POST: /_api/ProjectServer/Projects('{project_id}')/Draft/Publish(true)```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|siteUrl|string|yes|query|nessuno|Url del sito radice del sito del progetto (esempio: https://sampletenant.sharepoint.com/teams/sampleteam )|
|project\_id|string|yes|path|nessuno|ID univoco del progetto da registrare|

#### Risposta

|Nome|Descrizione|
|---|---|
|200|OK|
|400|Bad Request|
|401|Non autorizzata|
|403|Accesso negato|
|404|Non trovato|
|500|Errore interno del server. Si è verificato un errore sconosciuto|
|default|Operazione non riuscita.|


## Definizioni oggetti 

### TriggerProjectsWrapper


| Nome proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|value|array|No |



### TriggerProject


| Nome proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|ProjectStartDate|string|No |
|ProjectFinishDate|string|No |
|ProjectCreatedDate|string|No |
|ProjectId|string|No |
|ProjectModifiedDate|string|No |
|ProjectType|numero intero|No |
|ProjectName|string|No |



### TriggerResourcesWrapper


| Nome proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|value|array|No |



### TriggerResource


| Nome proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|ResourceId|string|No |
|ResourceBaseCalendar|string|No |
|ResourceBookingType|numero intero|No |
|ResourceCanLevel|boolean|No |
|ResourceCostPerUse|number|No |
|ResourceCreatedDate|string|No |
|ResourceEarliestAvailableFrom|string|No |
|ResourceEmail|string|No |
|ResourceInitials|string|No |
|ResourceIsActive|boolean|No |
|ResourceIsGeneric|boolean|No |
|ResourceLatestAvailableTo|string|No |
|ResourceModifiedDate|string|No |
|ResourceName|string|No |
|ResourceStatsuName|string|No |
|ResourceType|numero intero|No |
|TypeDescription|string|No |
|TypeName|string|No |



### TriggerTasksWrapper


| Nome proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|value|array|No |



### TriggerTask


| Nome proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|ProjectId|string|No |
|TaskId|string|No |
|ProjectName|string|No |
|TaskName|string|No |
|TaskCreatedDate|string|No |
|TaskModifieddate|string|No |
|TaskStartDate|string|No |
|TaskFinishDate|string|No |
|TaskPriority|numero intero|No |
|TaskIsActive|boolean|No |



### NewProject


| Nome proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|Nome|string|Sì |
|Descrizione|string|No |
|Inizia|string|No |



### NewReource


| Nome proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|Nome|string|Sì |
|IsBudget|boolean|No |
|IsGeneric|boolean|No |
|IsInactive|boolean|No |



### Project


| Nome proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|ApprovedStart|string|No |
|ApprovedEnd|string|No |
|CheckedOutDate|string|No |
|CheckOutDescription|string|No |
|CheckOutId|string|No |
|CreatedDate|string|No |
|ID|string|No |
|IsCheckedOut|boolean|No |
|LastPublishedDate|string|No |
|LastSavedDate|string|No |
|OptimizerDecision|numero intero|No |
|PlannerDecision|numero intero|No |
|ProjectType|numero intero|No |
|Nome|string|No |
|WinprojVersion|string|No |



### ProjectsWrapper


| Nome proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|value|array|No |



### NewTask


| Nome proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|parameters|non definito|Sì |



### TaskParameters


| Nome proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|Nome|string|Sì |
|Note|string|No |
|Inizia|string|No |
|Durata|string|No |



### EnterpriseResource


| Nome proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|CanLevel|boolean|No |
|Codice|string|No |
|CostAccrual|numero intero|No |
|CostCenter|string|No |
|Data di creazione|string|No |
|DefaultBookingType|numero intero|No |
|Email|string|No |
|ExternalId|string|No |
|Group|string|No |
|HireDate|string|No |
|ID|string|No |
|Initials|string|No |
|IsActive|boolean|No |
|IsBudget|boolean|No |
|IsCheckedOut|boolean|No |
|IsGeneric|boolean|No |
|IsTeam|boolean|No |
|MaterialLabel|string|No |
|Data modifica|string|No |
|Nome|string|No |
|Phonetics|string|No |
|ResourceType|numero intero|No |
|TerminationDate|string|No |



### TasksWrapper


| Nome proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|value|array|No |



### Attività


| Nome proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|Data di creazione|string|No |
|Data modifica|string|No |
|Inizia|string|No |
|Fine|string|No |
|Nome|string|No |
|ID|string|No |
|Priorità|numero intero|No |
|PercentComplete|numero intero|No |
|Note|string|No |
|Contatto|string|No |


## Passaggi successivi
[Creare un'app per la logica](../app-service-logic/app-service-logic-create-a-logic-app.md)

<!---HONumber=AcomDC_0504_2016-->