<properties
pageTitle="SQL Connector | Microsoft Azure"
description="Creare app per la logica con Servizio app di Azure. SQL Connector costituisce un'API per lavorare con i database SQL."
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
ms.date="05/17/2016"
ms.author="deonhe"/>

# Introduzione al connettore SQL Connector



Il connettore SQL Connector può essere usato da:

- [App per la logica](../app-service-logic/app-service-logic-what-are-logic-apps.md)  
- [PowerApps](http://powerapps.microsoft.com)  
- [Flusso](http://flows.microsoft.com)  

>[AZURE.NOTE] Questa versione dell'articolo si applica alla versione dello schema 2015-08-01-preview delle app per la logica.

Per iniziare subito a creare un'app per la logica, vedere [Creare una nuova app per la logica](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Trigger e azioni

Il connettore SQL Connector può essere usato come azione e include trigger. Tutti i connettori supportano dati nei formati JSON e XML.

 Nel connettore SQL Connector sono disponibili le azioni e/o i trigger seguenti:

### Azioni di SQL Connector
È possibile eseguire queste azioni:

|Azione|Descrizione|
|--- | ---|
|[GetTables](connectors-create-api-sqlconnector.md#gettables)|Recupera le tabelle da un database SQL|
|[PostItem](connectors-create-api-sqlconnector.md#postitem)|Inserisce una nuova riga in una tabella SQL|
|[GetItem](connectors-create-api-sqlconnector.md#getitem)|Recupera una riga singola da una tabella SQL|
|[DeleteItem](connectors-create-api-sqlconnector.md#deleteitem)|Elimina una riga da una tabella SQL|
|[PatchItem](connectors-create-api-sqlconnector.md#patchitem)|Aggiorna una riga esistente in una tabella SQL|
### Trigger di SQL Connector
È possibile ascoltare questi eventi:

|Trigger | Descrizione|
|--- | ---|


## Creare una connessione a SQL Connector
Per creare app per la logica con SQL Connector, è prima necessario creare una **connessione** e quindi fornire i dettagli per le proprietà seguenti:

|Proprietà| Obbligatorio|Descrizione|
| ---|---|---|
|SqlConnectionString|Sì|Fornisce la stringa di connessione SQL|
Dopo aver creato la connessione, è possibile usarla per eseguire le azioni e restare in ascolto dei trigger descritti in questo articolo.

>[AZURE.INCLUDE [Passaggi per creare una connessione a Mashup SQL](../../includes/connectors-create-api-mashupsql.md)]

>[AZURE.TIP] È possibile usare questa connessione in altre app per la logica.

## Informazioni di riferimento per SQL Connector
Si applica alla versione: 1.0

## GetTables
GetTables: recupera le tabelle da un database SQL

```GET: /datasets/default/tables```

Non sono disponibili parametri per questa chiamata
#### Response

|Nome|Descrizione|
|---|---|
|200|OK|
|default|Operazione non riuscita.|


## PostItem
Inserimento riga: inserisce una nuova riga in una tabella SQL

```POST: /datasets/default/tables/{table}/items```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|tabella|string|yes|path|nessuno|Nome della tabella SQL|
|item| |yes|body|nessuno|Riga da inserire nella tabella specificata in SQL|

#### Risposta

|Nome|Descrizione|
|---|---|
|200|OK|
|default|Operazione non riuscita.|


## GetItem
Recupero riga: recupera una riga singola da una tabella SQL

```GET: /datasets/default/tables/{table}/items/{id}```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|tabella|string|yes|path|nessuno|Nome della tabella SQL|
|id|string|yes|path|nessuno|Identificatore univoco della riga da recuperare|

#### Risposta

|Nome|Descrizione|
|---|---|
|200|OK|
|default|Operazione non riuscita.|


## DeleteItem
Eliminazione riga: elimina una riga da una tabella SQL

```DELETE: /datasets/default/tables/{table}/items/{id}```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|tabella|string|yes|path|nessuno|Nome della tabella SQL|
|id|string|yes|path|nessuno|Identificatore univoco della riga da eliminare|

#### Response

|Nome|Descrizione|
|---|---|
|200|OK|
|default|Operazione non riuscita.|


## PatchItem
Aggiornamento riga: aggiorna una riga esistente in una tabella SQL

```PATCH: /datasets/default/tables/{table}/items/{id}```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|tabella|string|yes|path|nessuno|Nome della tabella SQL|
|id|string|yes|path|nessuno|Identificatore univoco della riga da aggiornare|
|item| |yes|body|nessuno|Riga con i valori aggiornati|

#### Response

|Nome|Descrizione|
|---|---|
|200|OK|
|default|Operazione non riuscita.|


## Definizioni oggetto 

### DataSetsMetadata


| Nome proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|tabular|non definito|No |
|BLOB|non definito|No |



### TabularDataSetsMetadata


| Nome proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|una sezione source|string|No |
|displayName|string|No |
|urlEncoding|string|No |
|tableDisplayName|string|No |
|tablePluralName|string|No |



### BlobDataSetsMetadata


| Nome proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|una sezione source|string|No |
|displayName|string|No |
|urlEncoding|string|No |



### TableMetadata


| Nome proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|name|string|No |
|title|string|No |
|x-ms-permission|string|No |
|x-ms-capabilitiesx-ms-capabilities|non definito|No |
|schema|non definito|No |



### TableCapabilitiesMetadata


| Nome proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|sortRestrictions|non definito|No |
|filterRestrictions|non definito|No |
|filterFunctions|array|No |



### Oggetto


| Nome proprietà | Tipo di dati | Obbligatorio |
|---|---|---|



### TableSortRestrictionsMetadata


| Nome proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|sortable|boolean|No |
|unsortableProperties|array|No |
|ascendingOnlyProperties|array|No |



### TableFilterRestrictionsMetadata


| Nome proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|filterable|boolean|No |
|nonFilterableProperties|array|No |
|requiredProperties|array|No |



### DataSetsList


| Nome proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|value|array|No |



### DataSet


| Nome proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|Nome|string|No |
|DisplayName|string|No |



### Tabella


| Nome proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|Nome|string|No |
|DisplayName|string|No |



### Elemento


| Nome proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|ItemInternalId|string|No |



### TablesList


| Nome proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|value|array|No |



### ItemsList


| Nome proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|value|array|No |


## Passaggi successivi
[Creare un'app per la logica](../app-service-logic/app-service-logic-create-a-logic-app.md)

<!---HONumber=AcomDC_0518_2016-->