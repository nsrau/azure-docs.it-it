<properties
pageTitle="Aggiungere il connettore Excel in PowerApps Enterprise | Microsoft Azure"
description="Panoramica del connettore Excel con i parametri dell'API REST"
services=""    
documentationCenter=""     
authors="msftman"    
manager="erikre"    
editor=""
tags="connectors"/>

<tags
ms.service="multiple"
ms.devlang="na"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="na"
ms.date="05/18/2016"
ms.author="deonhe"/>

# Introduzione al connettore Excel

Connettersi a Excel per inserire una riga, eliminare una riga e altro ancora. Il connettore Excel può essere usato da:

- PowerApps

Con Excel, è possibile:

- Aggiungere il connettore Excel a PowerApps Enterprise, in modo che gli utenti possano usarlo nelle proprie app. 

Per informazioni su come aggiungere un connettore in PowerApps Enterprise, vedere la pagina relativa alla [registrazione dei connettori in PowerApps](../power-apps/powerapps-register-from-available-apis.md).

## Trigger e azioni
Excel include le azioni seguenti. Non sono disponibili trigger.

|Trigger|Actions|
|--- | ---|
|Nessuno | <ul><li>Recupera righe</li><li>Inserisci riga</li><li>Elimina riga</li><li>Recupera riga</li><li>Recupera tabelle</li><li>Aggiorna riga</li></ul>

Tutti i connettori supportano dati nei formati JSON e XML.

## Riferimento all'API REST di Swagger
Si applica alla versione 1.0.

### Inserisce una nuova riga in una tabella Excel
```POST: /datasets/{dataset}/tables/{table}/items```



| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|dataset|string|yes|path|nessuno|Il nome del file Excel|
|tabella|string|yes|path|nessuno|Il nome della tabella Excel|
|item| |yes|body|nessuno|Riga da inserire nella tabella Excel specificata|


### Response

|Nome|Descrizione|
|---|---|
|200|OK|
|default|Operazione non riuscita.|




### Recupera una singola riga da una tabella Excel
```GET: /datasets/{dataset}/tables/{table}/items/{id}```



| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|dataset|string|yes|path|nessuno|Il nome del file Excel|
|tabella|string|yes|path|nessuno|Il nome della tabella Excel|
|id|string|yes|path|nessuno|Identificatore univoco della riga da recuperare|


### Response

|Nome|Descrizione|
|---|---|
|200|OK|
|default|Operazione non riuscita.|




### Elimina una riga da una tabella Excel
```DELETE: /datasets/{dataset}/tables/{table}/items/{id}```



| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|dataset|string|yes|path|nessuno|Il nome del file Excel|
|tabella|string|yes|path|nessuno|Il nome della tabella Excel|
|id|string|yes|path|nessuno|Identificatore univoco della riga da eliminare|


### Response

|Nome|Descrizione|
|---|---|
|200|OK|
|default|Operazione non riuscita.|




### Aggiorna una riga esistente in una tabella Excel
```PATCH: /datasets/{dataset}/tables/{table}/items/{id}```



| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|dataset|string|yes|path|nessuno|Il nome del file Excel|
|tabella|string|yes|path|nessuno|Il nome della tabella Excel|
|id|string|yes|path|nessuno|Identificatore univoco della riga da aggiornare|
|item| |yes|body|nessuno|Riga con i valori aggiornati|


### Response

|Nome|Descrizione|
|---|---|
|200|OK|
|default|Operazione non riuscita.|




## Definizioni oggetto

#### DataSetsMetadata

| Nome | Tipo di dati | Obbligatorio|
|---|---|---|
|tabular|non definito|no|
|BLOB|non definito|no|

#### TabularDataSetsMetadata

| Nome | Tipo di dati |Obbligatorio|
|---|---|---|
|una sezione source|string|no|
|displayName|string|no|
|urlEncoding|string|no|
|tableDisplayName|string|no|
|tablePluralName|string|no|

#### BlobDataSetsMetadata

| Nome | Tipo di dati |Obbligatorio|
|---|---|---|
|una sezione source|string|no|
|displayName|string|no|
|urlEncoding|string|no|

#### TableMetadata

| Nome | Tipo di dati |Obbligatorio|
|---|---|---|
|name|string|no|
|title|string|no|
|x-ms-permission|string|no|
|schema|non definito|no|

#### DataSetsList

| Nome | Tipo di dati |Obbligatorio|
|---|---|---|
|value|array|no|

#### DataSet

| Nome | Tipo di dati |Obbligatorio|
|---|---|---|
|Nome|string|no|
|DisplayName|string|no|

#### Tabella

| Nome | Tipo di dati |Obbligatorio|
|---|---|---|
|Nome|string|no|
|DisplayName|string|no|

#### Item

| Nome | Tipo di dati |Obbligatorio|
|---|---|---|
|ItemInternalId|string|no|

#### TablesList

| Nome | Tipo di dati |Obbligatorio|
|---|---|---|
|value|array|no|

#### ItemsList

| Nome | Tipo di dati |Obbligatorio|
|---|---|---|
|value|array|no|


## Passaggi successivi
[Creare un'app per la logica](../app-service-logic/app-service-logic-create-a-logic-app.md) [Creare un'app di PowerApps](../power-apps/powerapps-get-started-azure-portal.md)

<!---HONumber=AcomDC_0525_2016-->