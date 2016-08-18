<properties
pageTitle="Informazioni su come usare il connettore Salesforce nelle app per la logica | Microsoft Azure"
description="Creare app per la logica con Servizio app di Azure. Il connettore Salesforce offre un'API per lavorare con gli oggetti Salesforce."
services="logic-apps"	
documentationCenter=".net,nodejs,java" 	
authors="msftman"	
manager="erikre"	
editor=""
tags="connectors" />

<tags
ms.service="logic-apps"
ms.devlang="multiple"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="integration"
ms.date="07/22/2016"
ms.author="deonhe"/>

# Introduzione al connettore Salesforce

Il connettore Salesforce offre un'API per lavorare con gli oggetti Salesforce.

Per usare [qualsiasi connettore](./apis-list.md), è necessario innanzitutto creare un'app per la logica. Come prima operazione [creare un'app per la logica](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Connettersi al connettore Salesforce

Perché l'app per la logica possa accedere a qualsiasi servizio, è necessario creare una *connessione* al servizio. Una [connessione](./connectors-overview.md) fornisce la connettività tra un'app per la logica e un altro servizio.

### Creare una connessione al connettore Salesforce

>[AZURE.INCLUDE [Passaggi per creare una connessione al connettore Salesforce](../../includes/connectors-create-api-salesforce.md)]

## Usare un trigger del connettore Salesforce

Un trigger è un evento che può essere usato per avviare il flusso di lavoro definito in un'app per la logica. [Altre informazioni sui trigger](../app-service-logic/app-service-logic-what-are-logic-apps.md#logic-app-concepts).

>[AZURE.INCLUDE [Passaggi per creare un trigger di Salesforce](../../includes/connectors-create-api-salesforce-trigger.md)]

## Aggiungere una condizione 
>[AZURE.INCLUDE [Passaggi per creare una condizione di Salesforce](../../includes/connectors-create-api-salesforce-condition.md)]

## Usare un'azione del connettore Salesforce

Un'azione è un'operazione eseguita dal flusso di lavoro e definita in un'app per la logica. [Altre informazioni sulle azioni](../app-service-logic/app-service-logic-what-are-logic-apps.md#logic-app-concepts).

>[AZURE.INCLUDE [Passaggi per creare un'azione di Salesforce](../../includes/connectors-create-api-salesforce-action.md)]

## Dettagli tecnici

Ecco i dettagli sui trigger, le azioni e le risposte che la connessione supporta:

## Trigger del connettore Salesforce

Il connettore Salesforce supporta i trigger seguenti:

|Trigger | Descrizione|
|--- | ---|
|[Quando viene creato un oggetto](connectors-create-api-salesforceconnector.md#when-an-object-is-created)|Questa operazione attiva un flusso quando viene creato un oggetto.|
|[Quando viene modificato un oggetto](connectors-create-api-salesforceconnector.md#when-an-object-is-modified)|Questa operazione attiva un flusso quando viene modificato un oggetto.|


## Azioni del connettore Salesforce

Il connettore Salesforce supporta le azioni seguenti:


|Azione|Descrizione|
|--- | ---|
|[Recupera oggetti](connectors-create-api-salesforceconnector.md#get-objects)|Questa operazione recupera oggetti di un determinato tipo ad esempio "Lead".|
|[Crea oggetto](connectors-create-api-salesforceconnector.md#create-object)|Questa operazione crea un oggetto.|
|[Recupera oggetto](connectors-create-api-salesforceconnector.md#get-object)|Questa operazione recupera un oggetto.|
|[Elimina oggetto](connectors-create-api-salesforceconnector.md#delete-object)|Questa operazione elimina un oggetto.|
|[Aggiorna oggetto](connectors-create-api-salesforceconnector.md#update-object)|Questa operazione aggiorna un oggetto.|
|[Recupera tipi di oggetto](connectors-create-api-salesforceconnector.md#get-object-types)|Questa operazione elenca i tipi di oggetto disponibili.|
### Informazioni dettagliate sulle azioni

Ecco i dettagli per le azioni e i trigger per questo connettore con le relative risposte:



### Recupera oggetti
Questa operazione recupera oggetti di un determinato tipo ad esempio "Lead".


|Nome proprietà| Nome visualizzato|Descrizione|
| ---|---|---|
|table*|Tipo di oggetto|Tipo di oggetto Salesforce, ad esempio "Lead"|
|$filter|Query di filtro|Query di filtro ODATA per limitare il numero di elementi|
|$orderby|Ordina per|Query orderBy ODATA per specificare l'ordine degli elementi|
|$skip|Ignora conteggio|Numero di elementi da ignorare (impostazione predefinita = 0)|
|$top|Numero massimo di Get|Numero massimo di elementi da recuperare (impostazione predefinita = 256)|

* indica che la proprietà è obbligatoria

#### Dettagli dell'output

ItemsList


| Nome proprietà | Tipo di dati |
|---|---|
|value|array|




### Crea oggetto
Questa operazione crea un oggetto.


|Nome proprietà| Nome visualizzato|Descrizione|
| ---|---|---|
|table*|Tipo di oggetto|Tipo di oggetto, ad esempio "Lead"|
|item*|Object|Oggetto da creare|

* indica che la proprietà è obbligatoria

#### Dettagli dell'output

Item


| Nome proprietà | Tipo di dati |
|---|---|
|ItemInternalId|string|




### Recupera oggetto
Questa operazione recupera un oggetto.


|Nome proprietà| Nome visualizzato|Descrizione|
| ---|---|---|
|table*|Tipo di oggetto|Tipo di oggetto Salesforce, ad esempio "Lead"|
|id*|ID oggetto|Identificatore dell'oggetto da recuperare|

* indica che la proprietà è obbligatoria

#### Dettagli dell'output

Item


| Nome proprietà | Tipo di dati |
|---|---|
|ItemInternalId|string|




### Elimina oggetto
Questa operazione elimina un oggetto.


|Nome proprietà| Nome visualizzato|Descrizione|
| ---|---|---|
|table*|Tipo di oggetto|Tipo di oggetto, ad esempio "Lead"|
|id*|ID oggetto|Identificatore dell'oggetto da eliminare|

* indica che la proprietà è obbligatoria




### Aggiorna oggetto
Questa operazione aggiorna un oggetto.


|Nome proprietà| Nome visualizzato|Descrizione|
| ---|---|---|
|table*|Tipo di oggetto|Tipo di oggetto, ad esempio "Lead"|
|id*|ID oggetto|Identificatore dell'oggetto da aggiornare|
|item*|Object|Oggetto con le proprietà modificate|

* indica che la proprietà è obbligatoria

#### Dettagli dell'output

Item


| Nome proprietà | Tipo di dati |
|---|---|
|ItemInternalId|string|




### Quando viene creato un oggetto
Questa operazione attiva un flusso quando viene creato un oggetto.


|Nome proprietà| Nome visualizzato|Descrizione|
| ---|---|---|
|table*|Tipo di oggetto|Tipo di oggetto, ad esempio "Lead"|
|$filter|Query di filtro|Query di filtro ODATA per limitare il numero di elementi|
|$orderby|Ordina per|Query orderBy ODATA per specificare l'ordine degli elementi|
|$skip|Ignora conteggio|Numero di elementi da ignorare (impostazione predefinita = 0)|
|$top|Numero massimo di Get|Numero massimo di elementi da recuperare (impostazione predefinita = 256)|

* indica che la proprietà è obbligatoria

#### Dettagli dell'output

ItemsList


| Nome proprietà | Tipo di dati |
|---|---|
|value|array|




### Quando viene modificato un oggetto
Questa operazione attiva un flusso quando viene modificato un oggetto.


|Nome proprietà| Nome visualizzato|Descrizione|
| ---|---|---|
|table*|Tipo di oggetto|Tipo di oggetto, ad esempio "Lead"|
|$filter|Query di filtro|Query di filtro ODATA per limitare il numero di elementi|
|$orderby|Ordina per|Query orderBy ODATA per specificare l'ordine degli elementi|
|$skip|Ignora conteggio|Numero di elementi da ignorare (impostazione predefinita = 0)|
|$top|Numero massimo di Get|Numero massimo di elementi da recuperare (impostazione predefinita = 256)|

* indica che la proprietà è obbligatoria

#### Dettagli dell'output

ItemsList


| Nome proprietà | Tipo di dati |
|---|---|
|value|array|




### Recupera tipi di oggetto
Questa operazione elenca i tipi di oggetto disponibili.


Non sono disponibili parametri per questa chiamata

#### Dettagli dell'output

TablesList


| Nome proprietà | Tipo di dati | 
|---|---|
|value|array|



## Risposte HTTP

Le azioni e i trigger riportati sopra possono restituire uno o più dei seguenti codici di stato HTTP:

|Nome|Descrizione|
|---|---|
|200|OK|
|202|Accepted|
|400|Bad Request|
|401|Non autorizzata|
|403|Accesso negato|
|404|Non trovato|
|500|Errore interno del server. Si è verificato un errore sconosciuto.|
|default|Operazione non riuscita.|






## Passaggi successivi
[Creare un'app per la logica](../app-service-logic/app-service-logic-create-a-logic-app.md)

<!-----HONumber=AcomDC_0803_2016-->
