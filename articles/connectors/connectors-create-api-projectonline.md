---
title: Project Online | Documentazione Microsoft
description: "Creare app per la logica in Servizio app di Azure. Project Online è una soluzione flessibile online per la gestione del portfolio dei progetti (PPM) e per il lavoro di tutti i giorni offerta da Microsoft. Distribuito tramite Office 365, Project Online consente alle organizzazioni di iniziare a usare da subito potenti funzionalità di gestione per pianificare, classificare in ordine di priorità e gestire progetti e investimenti del portfolio di progetti praticamente ovunque e da qualsiasi dispositivo."
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: erikre
editor: 
tags: connectors
ms.assetid: 40ce621e-4925-4653-93bb-71ab9abcbdf1
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 08/18/2016
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 2e30909f2917fc01f6066d8489961ca94f34f429


---
# <a name="get-started-with-the-projectonline-connector"></a>Introduzione al connettore ProjectOnline
Project Online è una soluzione flessibile online per la gestione del portfolio dei progetti (PPM) e per il lavoro di tutti i giorni offerta da Microsoft. Distribuito tramite Office 365, Project Online consente alle organizzazioni di iniziare a usare da subito potenti funzionalità di gestione per pianificare, classificare in ordine di priorità e gestire progetti e investimenti del portfolio di progetti praticamente ovunque e da qualsiasi dispositivo.

> [!NOTE]
> Questa versione dell'articolo si applica alla versione schema 2015-08-01 di anteprima delle app per la logica. 
> 
> 

Per iniziare subito a creare un'app per la logica, vedere [Creare un'app per la logica](../app-service-logic/app-service-logic-create-a-logic-app.md).

## <a name="triggers-and-actions"></a>Trigger e azioni
Il connettore ProjectOnline può essere usato come un'azione e dispone di trigger. Tutti i connettori supportano dati nei formati JSON e XML. 

 Nel connettore ProjectOnline sono disponibili le azioni e/o i trigger seguenti:

### <a name="projectonline-actions"></a>Azioni ProjectOnline
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

### <a name="projectonline-triggers"></a>Trigger di ProjectOnline
È possibile ascoltare questi eventi:

| Trigger | Descrizione |
| --- | --- |
| Quando viene creato un nuovo progetto |Attiva un flusso ogni volta che viene creato un nuovo progetto |
| Quando viene creata una nuova risorsa |Attiva un nuovo flusso quando viene creata una nuova risorsa |
| Quando viene creata una nuova attività |Attiva un flusso quando viene creata una nuova attività |

## <a name="create-a-connection-to-projectonline"></a>Creare una connessione a ProjectOnline
Per creare app per la logica con Project Online, è prima necessario creare una **connessione** e quindi specificare i dettagli per le proprietà seguenti: 

| Proprietà | Obbligatorio | Descrizione |
| --- | --- | --- |
| Token |Sì |Fornire le credenziali ProjectOnline |

> [!INCLUDE [Steps to create a connection to ProjectOnline](../../includes/connectors-create-api-projectonline.md)]
> 
> [!TIP]
> È possibile usare questa connessione in altre app per la logica.
> 
> 

## <a name="reference-for-projectonline"></a>Riferimento per ProjectOnline
Si applica alla versione 1.0

## <a name="onnewproject"></a>OnNewProject
Quando viene creato un nuovo progetto: attiva un flusso ogni volta che viene creato un nuovo progetto 

```GET: /trigger/_api/ProjectData/Projects``` 

| Nome | Tipo di dati | Obbligatorio | Posizione | Valore predefinito | Description |
| --- | --- | --- | --- | --- | --- |
| siteUrl |stringa |yes |query |nessuno |URL del sito radice del sito del progetto (ad esempio, https://sampletenant.sharepoint.com/teams/sampleteam) |

#### <a name="response"></a>Response
| Nome | Descrizione |
| --- | --- |
| 200 |OK |
| 400 |Bad Request |
| 401 |Non autorizzata |
| 403 |Accesso negato |
| 404 |Non trovato |
| 500 |Errore interno del server. Si è verificato un errore sconosciuto |
| default |Operazione non riuscita. |

## <a name="onnewresource"></a>OnNewResource
Quando viene creata una nuova risorsa: attiva un nuovo flusso quando viene creata una nuova risorsa 

```GET: /trigger/_api/ProjectData/Resources``` 

| Nome | Tipo di dati | Obbligatorio | Posizione | Valore predefinito | Description |
| --- | --- | --- | --- | --- | --- |
| siteUrl |stringa |yes |query |nessuno |URL del sito radice del sito del progetto (ad esempio, https://sampletenant.sharepoint.com/teams/sampleteam) |

#### <a name="response"></a>Response
| Nome | Descrizione |
| --- | --- |
| 200 |OK |
| 400 |Bad Request |
| 401 |Non autorizzata |
| 403 |Accesso negato |
| 404 |Non trovato |
| 500 |Errore interno del server. Si è verificato un errore sconosciuto |
| default |Operazione non riuscita. |

## <a name="onnewtask"></a>OnNewTask
Quando viene creata una nuova attività: attiva un flusso quando viene creata una nuova attività 

```GET: /trigger/_api/ProjectData/Tasks``` 

| Nome | Tipo di dati | Obbligatorio | Posizione | Valore predefinito | Description |
| --- | --- | --- | --- | --- | --- |
| siteUrl |stringa |yes |query |nessuno |URL del sito radice del sito del progetto (ad esempio, https://sampletenant.sharepoint.com/teams/sampleteam) |

#### <a name="response"></a>Response
| Nome | Descrizione |
| --- | --- |
| 200 |OK |
| 400 |Bad Request |
| 401 |Non autorizzata |
| 403 |Accesso negato |
| 404 |Non trovato |
| 500 |Errore interno del server. Si è verificato un errore sconosciuto |
| default |Operazione non riuscita. |

## <a name="listprojects"></a>ListProjects
Elenco dei progetti: elenca i progetti nel sito del progetto online 

```GET: /_api/ProjectServer/Projects``` 

| Nome | Tipo di dati | Obbligatorio | Posizione | Valore predefinito | Description |
| --- | --- | --- | --- | --- | --- |
| siteUrl |stringa |yes |query |nessuno |URL del sito radice del sito del progetto (ad esempio, https://sampletenant.sharepoint.com/teams/sampleteam) |

#### <a name="response"></a>Response
| Nome | Descrizione |
| --- | --- |
| 200 |OK |
| 400 |Bad Request |
| 401 |Non autorizzata |
| 403 |Accesso negato |
| 404 |Non trovato |
| 500 |Errore interno del server. Si è verificato un errore sconosciuto |
| default |Operazione non riuscita. |

## <a name="createproject"></a>CreateProject
Crea nuovo progetto: crea un nuovo progetto nel sito del progetto online 

```POST: /_api/ProjectServer/Projects``` 

| Nome | Tipo di dati | Obbligatorio | Posizione | Valore predefinito | Description |
| --- | --- | --- | --- | --- | --- |
| siteUrl |stringa |yes |query |nessuno |URL del sito radice del sito del progetto (ad esempio, https://sampletenant.sharepoint.com/teams/sampleteam) |
| proj | |sì |body |nessuno |Nuovo progetto da creare |

#### <a name="response"></a>Response
| Nome | Descrizione |
| --- | --- |
| 200 |OK |
| 400 |Bad Request |
| 401 |Non autorizzata |
| 403 |Accesso negato |
| 404 |Non trovato |
| 500 |Errore interno del server. Si è verificato un errore sconosciuto |
| default |Operazione non riuscita. |

## <a name="createtask"></a>CreateTask
Crea una nuova attività: crea una nuova attività nel progetto 

```POST: /_api/ProjectServer/Projects('{project_id}')/Draft/Tasks/Add``` 

| Nome | Tipo di dati | Obbligatorio | Posizione | Valore predefinito | Description |
| --- | --- | --- | --- | --- | --- |
| siteUrl |stringa |yes |query |nessuno |URL del sito radice del sito del progetto (ad esempio, https://sampletenant.sharepoint.com/teams/sampleteam) |
| project_id |string |Sì |path |nessuno |ID univoco del progetto a cui aggiungere l'attività |
| attività | |sì |body |nessuno |Nuova attività da aggiungere al progetto |

#### <a name="response"></a>Response
| Nome | Descrizione |
| --- | --- |
| 200 |OK |
| 400 |Bad Request |
| 401 |Non autorizzata |
| 403 |Accesso negato |
| 404 |Non trovato |
| 500 |Errore interno del server. Si è verificato un errore sconosciuto |
| default |Operazione non riuscita. |

## <a name="createresource"></a>CreateResource
Crea una nuova risorsa: crea risorse per l'organizzazione nel sito del progetto online 

```POST: /_api/ProjectServer/EnterpriseResources``` 

| Nome | Tipo di dati | Obbligatorio | Posizione | Valore predefinito | Description |
| --- | --- | --- | --- | --- | --- |
| siteUrl |stringa |yes |query |nessuno |URL del sito radice del sito del progetto (ad esempio, https://sampletenant.sharepoint.com/teams/sampleteam) |
| resource | |sì |body |nessuno |Nuova risorsa per l'organizzazione da aggiungere al progetto |

#### <a name="response"></a>Response
| Nome | Descrizione |
| --- | --- |
| 200 |OK |
| 400 |Bad Request |
| 401 |Non autorizzata |
| 403 |Accesso negato |
| 404 |Non trovato |
| 500 |Errore interno del server. Si è verificato un errore sconosciuto |
| default |Operazione non riuscita. |

## <a name="listtasks"></a>ListTasks
Elenca le attività: elenca le attività pubblicate in un progetto 

```GET: /_api/ProjectServer/Projects('{project_id}')/Tasks``` 

| Nome | Tipo di dati | Obbligatorio | Posizione | Valore predefinito | Description |
| --- | --- | --- | --- | --- | --- |
| siteUrl |stringa |yes |query |nessuno |URL del sito radice del sito del progetto (ad esempio, https://sampletenant.sharepoint.com/teams/sampleteam) |
| project_id |string |Sì |path |nessuno |ID univoco del progetto per recuperare le attività |

#### <a name="response"></a>Response
| Nome | Descrizione |
| --- | --- |
| 200 |OK |
| 400 |Bad Request |
| 401 |Non autorizzata |
| 403 |Accesso negato |
| 404 |Non trovato |
| 500 |Errore interno del server. Si è verificato un errore sconosciuto |
| default |Operazione non riuscita. |

## <a name="checkoutproject"></a>CheckoutProject
Estrae un progetto: consente di estrarre un progetto nel sito 

```POST: /_api/ProjectServer/Projects('{project_id}')/checkOut``` 

| Nome | Tipo di dati | Obbligatorio | Posizione | Valore predefinito | Description |
| --- | --- | --- | --- | --- | --- |
| siteUrl |stringa |yes |query |nessuno |URL del sito radice del sito del progetto (ad esempio, https://sampletenant.sharepoint.com/teams/sampleteam) |
| project_id |string |Sì |path |nessuno |ID univoco del progetto a cui aggiungere l'attività |

#### <a name="response"></a>Response
| Nome | Descrizione |
| --- | --- |
| 200 |OK |
| 400 |Bad Request |
| 401 |Non autorizzata |
| 403 |Accesso negato |
| 404 |Non trovato |
| 500 |Errore interno del server. Si è verificato un errore sconosciuto |
| default |Operazione non riuscita. |

## <a name="publishproject"></a>PublishProject
Registra e pubblica un progetto: registra e pubblica un progetto esistente nel sito 

```POST: /_api/ProjectServer/Projects('{project_id}')/Draft/Publish(true)``` 

| Nome | Tipo di dati | Obbligatorio | Posizione | Valore predefinito | Description |
| --- | --- | --- | --- | --- | --- |
| siteUrl |stringa |yes |query |nessuno |URL del sito radice del sito del progetto (ad esempio, https://sampletenant.sharepoint.com/teams/sampleteam) |
| project_id |string |Sì |path |nessuno |ID univoco del progetto da registrare |

#### <a name="response"></a>Response
| Nome | Descrizione |
| --- | --- |
| 200 |OK |
| 400 |Bad Request |
| 401 |Non autorizzata |
| 403 |Accesso negato |
| 404 |Non trovato |
| 500 |Errore interno del server. Si è verificato un errore sconosciuto |
| default |Operazione non riuscita. |

## <a name="object-definitions"></a>Definizioni oggetti
### <a name="triggerprojectswrapper"></a>TriggerProjectsWrapper
| Nome proprietà | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| value |array |No |

### <a name="triggerproject"></a>TriggerProject
| Nome proprietà | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| ProjectStartDate |stringa |No |
| ProjectFinishDate |string |No |
| ProjectCreatedDate |stringa |No |
| ProjectId |stringa |No |
| ProjectModifiedDate |string |No |
| ProjectType |integer |No |
| ProjectName |string |No |

### <a name="triggerresourceswrapper"></a>TriggerResourcesWrapper
| Nome proprietà | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| value |array |No |

### <a name="triggerresource"></a>TriggerResource
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

### <a name="triggertaskswrapper"></a>TriggerTasksWrapper
| Nome proprietà | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| value |array |No |

### <a name="triggertask"></a>TriggerTask
| Nome proprietà | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| ProjectId |stringa |No |
| TaskId |stringa |No |
| ProjectName |string |No |
| TaskName |string |No |
| TaskCreatedDate |stringa |No |
| TaskModifieddate |stringa |No |
| TaskStartDate |string |No |
| TaskFinishDate |string |No |
| TaskPriority |integer |No |
| TaskIsActive |boolean |No |

### <a name="newproject"></a>NewProject
| Nome proprietà | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| Nome |string |Sì |
| Descrizione |stringa |No |
| Inizia |stringa |No |

### <a name="newreource"></a>NewReource
| Nome proprietà | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| Nome |string |Sì |
| IsBudget |boolean |No |
| IsGeneric |boolean |No |
| IsInactive |boolean |No |

### <a name="project"></a>Project
| Nome proprietà | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| ApprovedStart |stringa |No |
| ApprovedEnd |stringa |No |
| CheckedOutDate |string |No |
| CheckOutDescription |stringa |No |
| CheckOutId |stringa |No |
| CreatedDate |string |No |
| ID |stringa |No |
| IsCheckedOut |boolean |No |
| LastPublishedDate |string |No |
| LastSavedDate |stringa |No |
| OptimizerDecision |integer |No |
| PlannerDecision |integer |No |
| ProjectType |integer |No |
| Name |stringa |No |
| WinprojVersion |string |No |

### <a name="projectswrapper"></a>ProjectsWrapper
| Nome proprietà | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| value |array |No |

### <a name="newtask"></a>NewTask
| Nome proprietà | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| parameters |non definito |Sì |

### <a name="taskparameters"></a>TaskParameters
| Nome proprietà | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| Nome |string |Sì |
| Note |stringa |No |
| Inizia |stringa |No |
| Durata |stringa |No |

### <a name="enterpriseresource"></a>EnterpriseResource
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

### <a name="taskswrapper"></a>TasksWrapper
| Nome proprietà | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| value |array |No |

### <a name="task"></a>Attività
| Nome proprietà | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| Data di creazione |stringa |No |
| Data modifica |stringa |No |
| Inizia |stringa |No |
| Fine |stringa |No |
| Name |stringa |No |
| ID |stringa |No |
| Priorità |integer |No |
| PercentComplete |integer |No |
| Note |stringa |No |
| Contatto |stringa |No |

## <a name="next-steps"></a>Passaggi successivi
[Creare un'app per la logica](../app-service-logic/app-service-logic-create-a-logic-app.md)




<!--HONumber=Nov16_HO3-->


