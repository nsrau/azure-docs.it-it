---
title: ProjectOnline | Microsoft Docs
description: Creare app per la logica con il servizio app di Azure. Project Online è una soluzione flessibile online per la gestione del portfolio dei progetti (PPM) e per il lavoro di tutti i giorni offerta da Microsoft. Distribuito tramite Office 365, Project Online consente alle organizzazioni di iniziare a usare da subito potenti funzionalità di gestione per pianificare, classificare in ordine di priorità e gestire progetti e investimenti del portfolio di progetti praticamente ovunque e da qualsiasi dispositivo.
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: erikre
editor: ''
tags: connectors

ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 08/18/2016
ms.author: deonhe

---
# Introduzione al connettore ProjectOnline
Project Online è una soluzione flessibile online per la gestione del portfolio dei progetti (PPM) e per il lavoro di tutti i giorni offerta da Microsoft. Distribuito tramite Office 365, Project Online consente alle organizzazioni di iniziare a usare da subito potenti funzionalità di gestione per pianificare, classificare in ordine di priorità e gestire progetti e investimenti del portfolio di progetti praticamente ovunque e da qualsiasi dispositivo.

> [!NOTE]
> Questa versione dell'articolo si applica alla versione dello schema 2015-08-01-preview delle app per la logica.
> 
> 

Per iniziare subito a creare un'app per la logica, vedere [Creare una nuova app per la logica che connette servizi SaaS](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Trigger e azioni
Il connettore ProjectOnline può essere usato come un'azione e dispone di trigger. Tutti i connettori supportano dati nei formati JSON e XML.

 Nel connettore ProjectOnline sono disponibili le azioni e/o i trigger seguenti:

### Azioni ProjectOnline
È possibile eseguire queste azioni:

| Azione | Descrizione |
| --- | --- |
| [ListProjects](connectors-create-api-projectonline.md#listprojects) |Consente di elencare i progetti nel sito del progetto online |
| [CreateProject](connectors-create-api-projectonline.md#createproject) |Consente di creare un nuovo progetto nel sito del progetto online |
| [CreateTask](connectors-create-api-projectonline.md#createtask) |Consente di creare una nuova attività nel progetto |
| [CreateResource](connectors-create-api-projectonline.md#createresource) |Consente di creare risorse per l'organizzazione nel sito del progetto online |
| [ListTasks](connectors-create-api-projectonline.md#listtasks) |Consente di elencare le attività pubblicate in un progetto |
| [CheckoutProject](connectors-create-api-projectonline.md#checkoutproject) |Consente di estrarre un progetto nel sito |
| [PublishProject](connectors-create-api-projectonline.md#publishproject) |Consente di registrare e pubblicare un progetto esistente nel sito |

### Trigger di ProjectOnline
È possibile ascoltare questi eventi:

| Trigger | Descrizione |
| --- | --- |
| Quando viene creato un nuovo progetto |Attiva un flusso ogni volta che viene creato un nuovo progetto |
| Quando viene creata una nuova risorsa |Attiva un nuovo flusso quando viene creata una nuova risorsa |
| Quando viene creata una nuova attività |Attiva un flusso quando viene creata una nuova attività |

## Creare una connessione a ProjectOnline
Per creare app per la logica con ProjectOnline, è prima necessario creare una **connessione** e quindi fornire i dettagli per le proprietà seguenti:

| Proprietà | Obbligatorio | Descrizione |
| --- | --- | --- |
| Token |Sì |Fornire le credenziali ProjectOnline |

> [!INCLUDE [Passaggi per creare una connessione a ProjectOnline](../../includes/connectors-create-api-projectonline.md)]
> 
> [!TIP]
> È possibile usare questa connessione in altre app per la logica.
> 
> 

## Riferimento per ProjectOnline
Si applica alla versione 1.0

## OnNewProject
Quando viene creato un nuovo progetto: attiva un flusso ogni volta che viene creato un nuovo progetto

```GET: /trigger/_api/ProjectData/Projects```

| Nome | Tipo di dati | Obbligatorio | Posizione | Valore predefinito | Description |
| --- | --- | --- | --- | --- | --- |
| siteUrl |string |yes |query |nessuno |URL del sito radice del sito del progetto, ad esempio: https://sampletenant.sharepoint.com/teams/sampleteam |

#### Response
| Nome | Descrizione |
| --- | --- |
| 200 |OK |
| 400 |Bad Request |
| 401 |Non autorizzata |
| 403 |Accesso negato |
| 404 |Non trovato |
| 500 |Errore interno del server. Si è verificato un errore sconosciuto |
| default |Operazione non riuscita. |

## OnNewResource
Quando viene creata una nuova risorsa: attiva un nuovo flusso quando viene creata una nuova risorsa

```GET: /trigger/_api/ProjectData/Resources```

| Nome | Tipo di dati | Obbligatorio | Posizione | Valore predefinito | Description |
| --- | --- | --- | --- | --- | --- |
| siteUrl |string |yes |query |nessuno |URL del sito radice del sito del progetto, ad esempio: https://sampletenant.sharepoint.com/teams/sampleteam |

#### Response
| Nome | Descrizione |
| --- | --- |
| 200 |OK |
| 400 |Bad Request |
| 401 |Non autorizzata |
| 403 |Accesso negato |
| 404 |Non trovato |
| 500 |Errore interno del server. Si è verificato un errore sconosciuto |
| default |Operazione non riuscita. |

## OnNewTask
Quando viene creata una nuova attività: attiva un flusso quando viene creata una nuova attività

```GET: /trigger/_api/ProjectData/Tasks```

| Nome | Tipo di dati | Obbligatorio | Posizione | Valore predefinito | Descrizione |
| --- | --- | --- | --- | --- | --- |
| siteUrl |stringa |yes |query |nessuno |URL del sito radice del sito del progetto, ad esempio: https://sampletenant.sharepoint.com/teams/sampleteam |

#### Response
| Nome | Descrizione |
| --- | --- |
| 200 |OK |
| 400 |Bad Request |
| 401 |Non autorizzata |
| 403 |Accesso negato |
| 404 |Non trovato |
| 500 |Errore interno del server. Si è verificato un errore sconosciuto |
| default |Operazione non riuscita. |

## ListProjects
Elenco dei progetti: elenca i progetti nel sito del progetto online

```GET: /_api/ProjectServer/Projects```

| Nome | Tipo di dati | Obbligatorio | Posizione | Valore predefinito | Descrizione |
| --- | --- | --- | --- | --- | --- |
| siteUrl |stringa |yes |query |nessuno |URL del sito radice del sito del progetto, ad esempio: https://sampletenant.sharepoint.com/teams/sampleteam |

#### Response
| Nome | Descrizione |
| --- | --- |
| 200 |OK |
| 400 |Bad Request |
| 401 |Non autorizzata |
| 403 |Accesso negato |
| 404 |Non trovato |
| 500 |Errore interno del server. Si è verificato un errore sconosciuto |
| default |Operazione non riuscita. |

## CreateProject
Crea nuovo progetto: crea un nuovo progetto nel sito del progetto online

```POST: /_api/ProjectServer/Projects```

| Nome | Tipo di dati | Obbligatorio | Posizione | Valore predefinito | Description |
| --- | --- | --- | --- | --- | --- |
| siteUrl |stringa |yes |query |nessuno |URL del sito radice del sito del progetto, ad esempio: https://sampletenant.sharepoint.com/teams/sampleteam |
| proj | |sì |body |nessuno |Nuovo progetto da creare |

#### Response
| Name | Descrizione |
| --- | --- |
| 200 |OK |
| 400 |Bad Request |
| 401 |Non autorizzata |
| 403 |Accesso negato |
| 404 |Non trovato |
| 500 |Errore interno del server. Si è verificato un errore sconosciuto |
| default |Operazione non riuscita. |

## CreateTask
Crea una nuova attività: crea una nuova attività nel progetto

```POST: /_api/ProjectServer/Projects('{project_id}')/Draft/Tasks/Add```

| Name | Tipo di dati | Obbligatorio | Posizione | Valore predefinito | Description |
| --- | --- | --- | --- | --- | --- |
| siteUrl |stringa |yes |query |nessuno |URL del sito radice del sito del progetto, ad esempio: https://sampletenant.sharepoint.com/teams/sampleteam |
| project\_id |stringa |yes |path |nessuno |ID univoco del progetto a cui aggiungere l'attività |
| attività | |sì |body |nessuno |Nuova attività da aggiungere al progetto |

#### Response
| Nome | Descrizione |
| --- | --- |
| 200 |OK |
| 400 |Bad Request |
| 401 |Non autorizzata |
| 403 |Accesso negato |
| 404 |Non trovato |
| 500 |Errore interno del server. Si è verificato un errore sconosciuto |
| default |Operazione non riuscita. |

## CreateResource
Crea una nuova risorsa: crea risorse per l'organizzazione nel sito del progetto online

```POST: /_api/ProjectServer/EnterpriseResources```

| Nome | Tipo di dati | Obbligatorio | Posizione | Valore predefinito | Descrizione |
| --- | --- | --- | --- | --- | --- |
| siteUrl |string |yes |query |nessuno |URL del sito radice del sito del progetto, ad esempio: https://sampletenant.sharepoint.com/teams/sampleteam |
| resource | |sì |body |nessuno |Nuova risorsa per l'organizzazione da aggiungere al progetto |

#### Response
| Nome | Descrizione |
| --- | --- |
| 200 |OK |
| 400 |Bad Request |
| 401 |Non autorizzata |
| 403 |Accesso negato |
| 404 |Non trovato |
| 500 |Errore interno del server. Si è verificato un errore sconosciuto |
| default |Operazione non riuscita. |

## ListTasks
Elenca le attività: elenca le attività pubblicate in un progetto

```GET: /_api/ProjectServer/Projects('{project_id}')/Tasks```

| Name | Tipo di dati | Obbligatorio | Posizione | Valore predefinito | Descrizione |
| --- | --- | --- | --- | --- | --- |
| siteUrl |string |yes |query |nessuno |URL del sito radice del sito del progetto, ad esempio: https://sampletenant.sharepoint.com/teams/sampleteam |
| project\_id |string |yes |path |nessuno |ID univoco del progetto per recuperare le attività |

#### Response
| Nome | Descrizione |
| --- | --- |
| 200 |OK |
| 400 |Bad Request |
| 401 |Non autorizzata |
| 403 |Accesso negato |
| 404 |Non trovato |
| 500 |Errore interno del server. Si è verificato un errore sconosciuto |
| default |Operazione non riuscita. |

## CheckoutProject
Estrae un progetto: consente di estrarre un progetto nel sito

```POST: /_api/ProjectServer/Projects('{project_id}')/checkOut```

| Nome | Tipo di dati | Obbligatorio | Posizione | Valore predefinito | Description |
| --- | --- | --- | --- | --- | --- |
| siteUrl |stringa |yes |query |nessuno |URL del sito radice del sito del progetto, ad esempio: https://sampletenant.sharepoint.com/teams/sampleteam |
| project\_id |string |yes |path |nessuno |ID univoco del progetto a cui aggiungere l'attività |

#### Response
| Name | Descrizione |
| --- | --- |
| 200 |OK |
| 400 |Bad Request |
| 401 |Non autorizzata |
| 403 |Accesso negato |
| 404 |Non trovato |
| 500 |Errore interno del server. Si è verificato un errore sconosciuto |
| default |Operazione non riuscita. |

## PublishProject
Registra e pubblica un progetto: registra e pubblica un progetto esistente nel sito

```POST: /_api/ProjectServer/Projects('{project_id}')/Draft/Publish(true)```

| Nome | Tipo di dati | Obbligatorio | Posizione | Valore predefinito | Descrizione |
| --- | --- | --- | --- | --- | --- |
| siteUrl |stringa |yes |query |nessuno |URL del sito radice del sito del progetto, ad esempio: https://sampletenant.sharepoint.com/teams/sampleteam |
| project\_id |stringa |yes |path |nessuno |ID univoco del progetto da registrare |

#### Response
| Name | Descrizione |
| --- | --- |
| 200 |OK |
| 400 |Bad Request |
| 401 |Non autorizzata |
| 403 |Accesso negato |
| 404 |Non trovato |
| 500 |Errore interno del server. Si è verificato un errore sconosciuto |
| default |Operazione non riuscita. |

## Definizioni oggetti
### TriggerProjectsWrapper
| Nome proprietà | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| value |array |No |

### TriggerProject
| Nome proprietà | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| ProjectStartDate |stringa |No |
| ProjectFinishDate |string |No |
| ProjectCreatedDate |stringa |No |
| ProjectId |string |No |
| ProjectModifiedDate |string |No |
| ProjectType |integer |No |
| ProjectName |string |No |

### TriggerResourcesWrapper
| Nome proprietà | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| value |array |No |

### TriggerResource
| Nome proprietà | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| ResourceId |string |No |
| ResourceBaseCalendar |stringa |No |
| ResourceBookingType |integer |No |
| ResourceCanLevel |boolean |No |
| ResourceCostPerUse |number |No |
| ResourceCreatedDate |stringa |No |
| ResourceEarliestAvailableFrom |stringa |No |
| ResourceEmail |string |No |
| ResourceInitials |stringa |No |
| ResourceIsActive |boolean |No |
| ResourceIsGeneric |boolean |No |
| ResourceLatestAvailableTo |stringa |No |
| ResourceModifiedDate |string |No |
| ResourceName |stringa |No |
| ResourceStatsuName |stringa |No |
| ResourceType |integer |No |
| TypeDescription |string |No |
| TypeName |stringa |No |

### TriggerTasksWrapper
| Nome proprietà | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| value |array |No |

### TriggerTask
| Nome proprietà | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| ProjectId |stringa |No |
| TaskId |stringa |No |
| ProjectName |stringa |No |
| TaskName |string |No |
| TaskCreatedDate |stringa |No |
| TaskModifieddate |stringa |No |
| TaskStartDate |string |No |
| TaskFinishDate |string |No |
| TaskPriority |integer |No |
| TaskIsActive |boolean |No |

### NewProject
| Nome proprietà | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| Name |string |Sì |
| Descrizione |stringa |No |
| Inizia |stringa |No |

### NewReource
| Nome proprietà | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| Name |string |Sì |
| IsBudget |boolean |No |
| IsGeneric |boolean |No |
| IsInactive |boolean |No |

### Project
| Nome proprietà | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| ApprovedStart |stringa |No |
| ApprovedEnd |stringa |No |
| CheckedOutDate |string |No |
| CheckOutDescription |stringa |No |
| CheckOutId |stringa |No |
| CreatedDate |string |No |
| ID |string |No |
| IsCheckedOut |boolean |No |
| LastPublishedDate |string |No |
| LastSavedDate |stringa |No |
| OptimizerDecision |integer |No |
| PlannerDecision |integer |No |
| ProjectType |integer |No |
| Nome |stringa |No |
| WinprojVersion |string |No |

### ProjectsWrapper
| Nome proprietà | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| value |array |No |

### NewTask
| Nome proprietà | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| parameters |non definito |Sì |

### TaskParameters
| Nome proprietà | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| Nome |stringa |Sì |
| Note |stringa |No |
| Inizia |string |No |
| Durata |stringa |No |

### EnterpriseResource
| Nome proprietà | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| CanLevel |boolean |No |
| Codice |stringa |No |
| CostAccrual |integer |No |
| CostCenter |stringa |No |
| Data di creazione |stringa |No |
| DefaultBookingType |integer |No |
| Email |string |No |
| ExternalId |stringa |No |
| Gruppo |stringa |No |
| HireDate |string |No |
| ID |stringa |No |
| Initials |stringa |No |
| IsActive |boolean |No |
| IsBudget |boolean |No |
| IsCheckedOut |boolean |No |
| IsGeneric |boolean |No |
| IsTeam |boolean |No |
| MaterialLabel |stringa |No |
| Data modifica |stringa |No |
| Name |stringa |No |
| Phonetics |string |No |
| ResourceType |integer |No |
| TerminationDate |stringa |No |

### TasksWrapper
| Nome proprietà | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| value |array |No |

### Attività
| Nome proprietà | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| Data di creazione |string |No |
| Data modifica |stringa |No |
| Inizia |stringa |No |
| Fine |stringa |No |
| Nome |stringa |No |
| ID |stringa |No |
| Priorità |integer |No |
| PercentComplete |integer |No |
| Note |stringa |No |
| Contatto |stringa |No |

## Passaggi successivi
[Creare un'app per la logica](../app-service-logic/app-service-logic-create-a-logic-app.md)

<!---HONumber=AcomDC_0824_2016-->