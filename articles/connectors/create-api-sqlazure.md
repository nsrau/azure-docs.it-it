<properties
	pageTitle="Aggiungere l'API di SQL Azure alle app per la logica | Microsoft Azure"
	description="Panoramica dell'API di SQL Azure con i parametri dell'API REST."
	services=""
	documentationCenter="" 
	authors="MandiOhlinger"
	manager="erikre"
	editor=""
	tags="connectors"/>

<tags
   ms.service="multiple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="02/25/2016"
   ms.author="mandia"/>


# Introduzione all'API di SQL Azure
Connettersi a SQL Azure per gestire le tabelle e le righe, ad esempio per inserire righe, recuperare tabelle e così via.

L'API di SQL Azure può essere usata da:

- App per la logica 

>[AZURE.NOTE] Questa versione dell'articolo si applica alla versione dello schema 2015-08-01-preview delle app per la logica. Per la versione schema 2014-12-01-preview, fare clic sul [connettore di SQL](../app-service-logic/app-service-logic-connector-sql.md).

Con SQL Azure, è possibile:

- Creare il flusso aziendale in base ai dati ottenuti da SQL Azure. 
- Usare le azioni per recuperare una riga, inserirla e così via. Queste azioni ottengono una risposta e quindi rendono l'output disponibile per altre azioni. Ad esempio, è possibile ottenere una riga di dati da SQL Azure e quindi aggiungere i dati in Excel. 

Per aggiungere un'operazione nelle app per la logica, vedere [Creare un'app per la logica](../app-service-logic/app-service-logic-create-a-logic-app.md).


## Trigger e azioni
SQL include le azioni seguenti. Non sono disponibili trigger.

Trigger | Azioni
--- | ---
Nessuno | <ul><li>Recupera riga</li><li>Recupera righe</li><li>Inserisci riga</li><li>Elimina riga</li><li>Recupera tabelle</li><li>Aggiorna riga</li></ul>

Tutte le API supportano i dati nei formati JSON e XML.

## Creare una connessione a SQL
Quando si aggiunge questa API alle app per la logica, immettere i valori seguenti:

|Proprietà| Obbligatorio|Descrizione|
| ---|---|---|
|Stringa di connessione SQL|Sì|Immettere la stringa di connessione di SQL Azure|

Dopo aver creato la connessione immettere le proprietà di SQL, ad esempio nome tabella. Il **riferimento all'API REST** in questo argomento descrive tali proprietà.

>[AZURE.TIP] È possibile usare questa connessione in altre app per la logica.

## Riferimento all'API REST di Swagger
Si applica alla versione 1.0.

### Ottenere la riga 
Recupera una singola riga da una tabella SQL. ```GET: /datasets/default/tables/{table}/items/{id}```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|tabella|string|yes|path|nessuno|Nome della tabella SQL|
|id|string|yes|path|nessuno|Identificatore univoco della riga da recuperare|

#### Risposta
|Nome|Descrizione|
|---|---|
|200|OK|
|default|Operazione non riuscita.|


### Recupera righe 
Recupera le righe da una tabella SQL. ```GET: /datasets/default/tables/{table}/items```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|tabella|string|yes|path|nessuno|Nome della tabella SQL|
|$skip|integer|no|query|nessuno|Numero di elementi da ignorare (impostazione predefinita = 0)|
|$top|integer|no|query|nessuno|Numero massimo di elementi da recuperare (impostazione predefinita = 256)|
|$filter|string|no|query|nessuno|Query di filtro ODATA per limitare il numero di elementi|
|$orderby|string|no|query|nessuno|Query orderBy ODATA per specificare l'ordine degli elementi|

#### Response
|Nome|Descrizione|
|---|---|
|200|OK|
|default|Operazione non riuscita.|


### Inserisci riga 
Inserisce una nuova riga in una tabella SQL. ```POST: /datasets/default/tables/{table}/items```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|tabella|string|yes|path|nessuno|Nome della tabella SQL|
|item|ItemInternalId: string|sì|body|nessuno|Riga da inserire nella tabella specificata in SQL|

#### Risposta
|Nome|Descrizione|
|---|---|
|200|OK|
|default|Operazione non riuscita.|


### Elimina riga 
Elimina una riga da una tabella SQL. ```DELETE: /datasets/default/tables/{table}/items/{id}```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|tabella|string|yes|path|nessuno|Nome della tabella SQL|
|id|string|yes|path|nessuno|Identificatore univoco della riga da eliminare|

#### Response
|Nome|Descrizione|
|---|---|
|200|OK|
|default|Operazione non riuscita.|


### Recupera tabelle 
Recupera le tabelle da un database SQL. ```GET: /datasets/default/tables```

Non sono disponibili parametri per questa chiamata.

#### Risposta
|Nome|Descrizione|
|---|---|
|200|OK|
|default|Operazione non riuscita.|


### Aggiorna riga 
Aggiorna una riga esistente in una tabella SQL. ```PATCH: /datasets/default/tables/{table}/items/{id}```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|tabella|string|yes|path|nessuno|Nome della tabella SQL|
|id|string|yes|path|nessuno|Identificatore univoco della riga da aggiornare|
|item|ItemInternalId: string|sì|body|nessuno|Riga con i valori aggiornati|

#### Response
|Nome|Descrizione|
|---|---|
|200|OK|
|default|Operazione non riuscita.|

## Definizioni oggetto

#### DataSetsMetadata

|Nome proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|tabular|non definito|no|
|BLOB|non definito|no|

#### TabularDataSetsMetadata

|Nome proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|una sezione source|string|no|
|displayName|string|no|
|urlEncoding|string|no|
|tableDisplayName|string|no|
|tablePluralName|string|no|

#### BlobDataSetsMetadata

|Nome proprietà | Tipo di dati |Obbligatorio |
|---|---|---|
|una sezione source|string|no|
|displayName|string|no|
|urlEncoding|string|no|

#### TableMetadata

|Nome proprietà | Tipo di dati |Obbligatorio |
|---|---|---|
|name|string|no|
|title|string|no|
|x-ms-permission|string|no|
|schema|non definito|no|

#### DataSetsList

|Nome proprietà | Tipo di dati |Obbligatorio |
|---|---|---|
|value|array|no|

#### DataSet

|Nome proprietà | Tipo di dati |Obbligatorio |
|---|---|---|
|Nome|string|no|
|DisplayName|string|no|

#### Table

|Nome proprietà | Tipo di dati |Obbligatorio |
|---|---|---|
|Nome|string|no|
|DisplayName|string|no|

#### Item

|Nome proprietà | Tipo di dati |Obbligatorio |
|---|---|---|
|ItemInternalId|string|no|

#### ItemsList

|Nome proprietà | Tipo di dati |Obbligatorio |
|---|---|---|
|value|array|no|

#### TablesList

|Nome proprietà | Tipo di dati |Obbligatorio |
|---|---|---|
|value|array|no|


## Passaggi successivi

[Creare un'app per la logica](../app-service-logic/app-service-logic-create-a-logic-app.md).

<!---HONumber=AcomDC_0302_2016-->