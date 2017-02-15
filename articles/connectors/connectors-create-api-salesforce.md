---
title: Informazioni su come usare il connettore Salesforce nelle app per la logica | Microsoft Docs
description: Creare app per la logica con Servizio app di Azure. Il connettore Salesforce offre un&quot;API per lavorare con gli oggetti Salesforce.
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: erikre
editor: 
tags: connectors
ms.assetid: 54fe5af8-7d2a-4da8-94e7-15d029e029bf
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 10/05/2016
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: 66fc8f7e1da55dbe6bb1dd8b8d6a535c498c1cf7
ms.openlocfilehash: b6758aa36120c9c187e91ee5d9e7ceb5041eae6a


---
# <a name="get-started-with-the-salesforce-connector"></a>Introduzione al connettore Salesforce
Il connettore Salesforce offre un'API per lavorare con gli oggetti Salesforce.

Per usare [qualsiasi connettore](apis-list.md), è necessario innanzitutto creare un'app per la logica. Come prima operazione [creare un'app per la logica](../logic-apps/logic-apps-create-a-logic-app.md).

## <a name="connect-to-salesforce-connector"></a>Connettersi al connettore Salesforce
Perché l'app per la logica possa accedere a qualsiasi servizio, è necessario creare una *connessione* al servizio. Una [connessione](connectors-overview.md) fornisce la connettività tra un'app per la logica e un altro servizio.  

### <a name="create-a-connection-to-salesforce-connector"></a>Creare una connessione al connettore Salesforce
> [!INCLUDE [Steps to create a connection to Salesforce Connector](../../includes/connectors-create-api-salesforce.md)]
> 
> 

## <a name="use-a-salesforce-connector-trigger"></a>Usare un trigger del connettore Salesforce
Un trigger è un evento che può essere usato per avviare il flusso di lavoro definito in un'app per la logica. [Altre informazioni sui trigger](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts).

> [!INCLUDE [Steps to create a Salesforce trigger](../../includes/connectors-create-api-salesforce-trigger.md)]
> 
> 

## <a name="add-a-condition"></a>Add a condition
> [!INCLUDE [Steps to create a Salesforce condition](../../includes/connectors-create-api-salesforce-condition.md)]
> 
> 

## <a name="use-a-salesforce-connector-action"></a>Usare un'azione del connettore Salesforce
Un'azione è un'operazione eseguita dal flusso di lavoro e definita in un'app per la logica. [Altre informazioni sulle azioni](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts).

> [!INCLUDE [Steps to create a Salesforce action](../../includes/connectors-create-api-salesforce-action.md)]
> 
> 

## <a name="technical-details"></a>Dettagli tecnici
Ecco i dettagli sui trigger, le azioni e le risposte che la connessione supporta:

## <a name="salesforce-connector-triggers"></a>Trigger del connettore Salesforce
Il connettore Salesforce supporta i trigger seguenti:  

| Trigger | Descrizione |
| --- | --- |
| [Quando viene creato un oggetto](connectors-create-api-salesforce.md#when-an-object-is-created) |Questa operazione attiva un flusso quando viene creato un oggetto. |
| [Quando viene modificato un oggetto](connectors-create-api-salesforce.md#when-an-object-is-modified) |Questa operazione attiva un flusso quando viene modificato un oggetto. |

## <a name="salesforce-connector-actions"></a>Azioni del connettore Salesforce
Il connettore Salesforce supporta le azioni seguenti:

| Azione | Descrizione |
| --- | --- |
| [Recupera oggetti](connectors-create-api-salesforce.md#get-objects) |Questa operazione recupera oggetti di un determinato tipo ad esempio "Lead". |
| [Crea oggetto](connectors-create-api-salesforce.md#create-object) |Questa operazione crea un oggetto. |
| [Recupera oggetto](connectors-create-api-salesforce.md#get-object) |Questa operazione recupera un oggetto. |
| [Elimina oggetto](connectors-create-api-salesforce.md#delete-object) |Questa operazione elimina un oggetto. |
| [Aggiorna oggetto](connectors-create-api-salesforce.md#update-object) |Questa operazione aggiorna un oggetto. |
| [Recupera tipi di oggetto](connectors-create-api-salesforce.md#get-object-types) |Questa operazione elenca i tipi di oggetto disponibili. |

### <a name="action-details"></a>Informazioni dettagliate sulle azioni
Ecco i dettagli per le azioni e i trigger per questo connettore con le relative risposte:

### <a name="get-objects"></a>Recupera oggetti
Questa operazione recupera oggetti di un determinato tipo ad esempio "Lead". 

| Nome proprietà | Nome visualizzato | Descrizione |
| --- | --- | --- |
| table* |Tipo di oggetto |Tipo di oggetto Salesforce, ad esempio "Lead" |
| $filter |Query di filtro |Query di filtro ODATA per limitare il numero di elementi |
| $orderby |Ordina per |Query orderBy ODATA per specificare l'ordine degli elementi |
| $skip |Ignora conteggio |Numero di elementi da ignorare (impostazione predefinita = 0) |
| $top |Numero massimo di Get |Numero massimo di elementi da recuperare (impostazione predefinita = 256) |

* indica che la proprietà è obbligatoria

#### <a name="output-details"></a>Dettagli dell'output
ItemsList

| Nome proprietà | Tipo di dati |
| --- | --- |
| value |array |

### <a name="create-object"></a>Crea oggetto
Questa operazione crea un oggetto. 

| Nome proprietà | Nome visualizzato | Descrizione |
| --- | --- | --- |
| table* |Tipo di oggetto |Tipo di oggetto, ad esempio "Lead" |
| item* |Oggetto |Oggetto da creare |

* indica che la proprietà è obbligatoria

#### <a name="output-details"></a>Dettagli dell'output
Elemento

| Nome proprietà | Tipo di dati |
| --- | --- |
| ItemInternalId |string |

### <a name="get-object"></a>Recupera oggetto
Questa operazione recupera un oggetto. 

| Nome proprietà | Nome visualizzato | Descrizione |
| --- | --- | --- |
| table* |Tipo di oggetto |Tipo di oggetto Salesforce, ad esempio "Lead" |
| id* |ID oggetto |Identificatore dell'oggetto da recuperare |

* indica che la proprietà è obbligatoria

#### <a name="output-details"></a>Dettagli dell'output
Elemento

| Nome proprietà | Tipo di dati |
| --- | --- |
| ItemInternalId |string |

### <a name="delete-object"></a>Elimina oggetto
Questa operazione elimina un oggetto. 

| Nome proprietà | Nome visualizzato | Descrizione |
| --- | --- | --- |
| table* |Tipo di oggetto |Tipo di oggetto, ad esempio "Lead" |
| id* |ID oggetto |Identificatore dell'oggetto da eliminare |

* indica che la proprietà è obbligatoria

### <a name="update-object"></a>Aggiorna oggetto
Questa operazione aggiorna un oggetto. 

| Nome proprietà | Nome visualizzato | Descrizione |
| --- | --- | --- |
| table* |Tipo di oggetto |Tipo di oggetto, ad esempio "Lead" |
| id* |ID oggetto |Identificatore dell'oggetto da aggiornare |
| item* |Oggetto |Oggetto con le proprietà modificate |

* indica che la proprietà è obbligatoria

#### <a name="output-details"></a>Dettagli dell'output
Elemento

| Nome proprietà | Tipo di dati |
| --- | --- |
| ItemInternalId |string |

### <a name="when-an-object-is-created"></a>Quando viene creato un oggetto
Questa operazione attiva un flusso quando viene creato un oggetto. 

| Nome proprietà | Nome visualizzato | Descrizione |
| --- | --- | --- |
| table* |Tipo di oggetto |Tipo di oggetto, ad esempio "Lead" |
| $filter |Query di filtro |Query di filtro ODATA per limitare il numero di elementi |
| $orderby |Ordina per |Query orderBy ODATA per specificare l'ordine degli elementi |
| $skip |Ignora conteggio |Numero di elementi da ignorare (impostazione predefinita = 0) |
| $top |Numero massimo di Get |Numero massimo di elementi da recuperare (impostazione predefinita = 256) |

* indica che la proprietà è obbligatoria

#### <a name="output-details"></a>Dettagli dell'output
ItemsList

| Nome proprietà | Tipo di dati |
| --- | --- |
| value |array |

### <a name="when-an-object-is-modified"></a>Quando viene modificato un oggetto
Questa operazione attiva un flusso quando viene modificato un oggetto. 

| Nome proprietà | Nome visualizzato | Descrizione |
| --- | --- | --- |
| table* |Tipo di oggetto |Tipo di oggetto, ad esempio "Lead" |
| $filter |Query di filtro |Query di filtro ODATA per limitare il numero di elementi |
| $orderby |Ordina per |Query orderBy ODATA per specificare l'ordine degli elementi |
| $skip |Ignora conteggio |Numero di elementi da ignorare (impostazione predefinita = 0) |
| $top |Numero massimo di Get |Numero massimo di elementi da recuperare (impostazione predefinita = 256) |

* indica che la proprietà è obbligatoria

#### <a name="output-details"></a>Dettagli dell'output
ItemsList

| Nome proprietà | Tipo di dati |
| --- | --- |
| value |array |

### <a name="get-object-types"></a>Recupera tipi di oggetto
Questa operazione elenca i tipi di oggetto disponibili. 

Non sono disponibili parametri per questa chiamata

#### <a name="output-details"></a>Dettagli dell'output
TablesList

| Nome proprietà | Tipo di dati |
| --- | --- |
| value |array |

## <a name="http-responses"></a>Risposte HTTP
Le azioni e i trigger riportati sopra possono restituire uno o più dei seguenti codici di stato HTTP: 

| Nome | Descrizione |
| --- | --- |
| 200 |OK |
| 202 |Accepted |
| 400 |Bad Request |
| 401 |Non autorizzata |
| 403 |Accesso negato |
| 404 |Non trovato |
| 500 |Errore interno del server. Si è verificato un errore sconosciuto. |
| default |Operazione non riuscita. |

## <a name="next-steps"></a>Passaggi successivi
[Creare un'app per la logica](../logic-apps/logic-apps-create-a-logic-app.md)




<!--HONumber=Feb17_HO2-->


