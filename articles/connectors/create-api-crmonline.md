<properties
pageTitle="Aggiungere l'API di Dynamics CRM Online a PowerApps Enterprise o alle app per la logica | Microsoft Azure"
description="Panoramica dell'API di CRM Online con i parametri dell'API REST."
services=""	
documentationCenter="" 	
authors="msftman"	
manager="erikre"	
editor="" tags="connectors" />

<tags
ms.service="multiple"
ms.devlang="na"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="integration"
ms.date="03/02/2016"
ms.author="deonhe"/>

# Introduzione all'API di CRM
Connettersi a Dynamics CRM Online per creare un nuovo record, aggiornare un elemento e così via. L'API di CRM Online può essere usata da:

- App per la logica
- PowerApps

> [AZURE.SELECTOR]
- [App per la logica](../articles/connectors/create-api-crmonline.md)
- [PowerApps Enterprise](../articles/power-apps/powerapps-create-api-crmonline.md)

Con CRM Online è possibile:

- Creare il flusso aziendale in base ai dati ottenuti da CRM Online. 
- Usare azioni per eliminare un record, recuperare entità e così via. Queste azioni ottengono una risposta e quindi rendono l'output disponibile per altre azioni. Ad esempio, quando un elemento viene aggiornato in CRM, è possibile inviare un messaggio di posta elettronica tramite Office 365.


Per informazioni su come aggiungere un'API in PowerApps Enterprise, vedere l'articolo relativo alla [registrazione di un'API in PowerApps](../power-apps/powerapps-register-from-available-apis.md).

Per aggiungere un'operazione nelle app per la logica, vedere [Creare un'app per la logica](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Trigger e azioni
L'API di CRM include le azioni seguenti. Non sono disponibili trigger.

| Trigger | Azioni|
| --- | --- |
|Nessuno| <ul><li>Crea un nuovo record</li><li>Recupera record</li><li>Elimina un record</li><li>Recupera un record</li><li>Recupera entità</li><li>Aggiorna un elemento</li></ul>

Tutte le API supportano i dati nei formati JSON e XML.

## Creare una connessione a CRM Online


### Aggiungere configurazioni aggiuntive in PowerApps
Quando si aggiunge CRM Online a PowerApps Enterprise, immettere i valori **ID client** e **Chiave app** dell'applicazione Dynamics CRM Online per Azure Active Directory (AAD). Il valore dell'**URL di reindirizzamento** viene usato anche nell'applicazione CRM Online. Se non si ha un'applicazione, è possibile usare la procedura seguente per creare l'applicazione:

1. Nel [Azure Portal](https://portal.azure.com) aprire **Active Directory** e selezionare il nome del tenant dell'organizzazione.
2. Nella scheda Applicazioni selezionare **Aggiungi**. In **Aggiungi applicazione**:  

	1. Immettere un **Nome** per l'applicazione.  
	2. Lasciare il tipo di applicazione come **Web**.  
	3. Selezionare **Avanti**.

	![Aggiunta dell'applicazione AAD - info app][9]

3. In **Proprietà app**:

	1. Immettere l'**URL DI ACCESSO** dell'applicazione. Poiché si intende eseguire l'autenticazione con AAD per PowerApps, impostare l'URL di accesso su _https://login.windows.net_.
	2. Immettere un valore valido di **URI ID APP** per l'app.  
	3. Selezionare **OK**.  

	![Aggiunta dell'applicazione AAD - proprietà app][10]

4. Nella nuova applicazione, selezionare **Configura**.
5. In _OAuth 2_ impostare l'**URL di risposta** sul valore dell'URL di reindirizzamento visualizzato quando si aggiunge l'API di CRM Online nel portale di Azure: ![Configurazione dell'app AAD Contoso][12]

A questo punto, copiare e incollare i valori di **ID client** e **Chiave app** nella configurazione di CRM Online nel portale di Azure.

### Aggiungere configurazioni aggiuntive nelle app per la logica
Quando si aggiunge questa API alle app per la logica, è necessario accedere a Dynamics CRM Online.

Seguire questi passaggi per accedere a CRM Online e completare la configurazione della **connessione** nell'app per la logica:

1. Selezionare **Ricorrenza**
2. Selezionare una **Frequenza** e immettere un **Intervallo**
3. Selezionare **Aggiungi un'azione**  
![Configurare CRM Online][13]
4. Immettere CRM nella casella di ricerca e attendere che la ricerca restituisca tutte le voci con CRM nel nome
5. Selezionare **Dynamics CRM Online - Crea un nuovo record**
6. Selezionare **Accedi a Dynamics CRM Online**:  
![Configurare CRM Online][14]
7. Fornire le credenziali di CRM Online per accedere e autorizzare l'applicazione
![Configurare CRM Online][15]  
8. Dopo l'accesso, tornare all'app per la logica per completarla aggiungendo gli altri trigger e azioni necessari.
9. Salvare il lavoro selezionando **Salva** nella barra dei menu visualizzata in alto.


Dopo aver creato la connessione immettere le proprietà di CRM Online, ad esempio tabella o set di dati. Il **riferimento all'API REST** in questo argomento descrive tali proprietà.

>[AZURE.TIP] È possibile usare questa connessione in altre app per la logica.

## Riferimento all'API REST di Swagger
Si applica alla versione 1.0.

### Crea un nuovo record 
Crea un nuovo record in un'entità. ```POST: /datasets/{dataset}/tables/{table}/items```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|dataset|string|yes|path|nessuno|Nome univoco per l'organizzazione CRM contoso.crm|
|table|string|yes|path|nessuno|Nome dell'entità|
|item| |yes|body|nessuno|Record da creare|

#### Risposta
|Nome|Descrizione|
|---|---|
|200|OK|
|default|Operazione non riuscita.|


### Recupera record 
 Recupera record per un'entità. ```GET: /datasets/{dataset}/tables/{table}/items```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|dataset|string|yes|path|nessuno|Nome univoco per l'organizzazione CRM contoso.crm|
|table|string|yes|path|nessuno|Nome dell'entità|
|$skip|integer|no|query|nessuno|Numero di elementi da ignorare. Il valore predefinito è 0.|
|$top|integer|no|query|nessuno|Numero massimo di elementi da recuperare. Il valore predefinito è 100.|
|$filter|string|no|query|nessuno|Query di filtro ODATA per limitare il numero di elementi.|
|$orderby|string|no|query|nessuno|Query orderBy ODATA per specificare l'ordine degli elementi.|

#### Risposta
|Nome|Descrizione|
|---|---|
|200|OK|
|default|Operazione non riuscita.|




### Restituisci il set di dati 
 Restituisce i set di dati. ```GET: /datasets```

Non sono disponibili parametri per questa chiamata.

#### Risposta
|Nome|Descrizione|
|---|---|
|200|OK|
|default|Operazione non riuscita.|



### Recupera un elemento tabella 
Usata per recuperare un record specifico presente per un'entità CRM. ```GET: /datasets/{dataset}/tables/{table}/items/{id}```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|dataset|string|yes|path|nessuno|Nome univoco per l'organizzazione CRM contoso.crm|
|table|string|yes|path|nessuno|Nome dell'entità|
|id|string|yes|path|nessuno|Identificatore del record|

#### Risposta
|Nome|Descrizione|
|---|---|
|200|OK|
|default|Operazione non riuscita.|

### Elimina un elemento da un elenco 
Elimina un elemento da un elenco. ```DELETE: /datasets/{dataset}/tables/{table}/items/{id}```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|dataset|string|yes|path|nessuno|Nome univoco per l'organizzazione CRM contoso.crm|
|table|string|yes|path|nessuno|Nome dell'entità|
|id|string|yes|path|nessuno|Identificatore del record|

#### Risposta
|Nome|Descrizione|
|---|---|
|200|OK|
|default|Operazione non riuscita.|



### Aggiungi patch a un elemento tabella esistente 
Usato per l'aggiornamento parziale di un record esistente per un'entità CRM. ```PATCH: /datasets/{dataset}/tables/{table}/items/{id}```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|dataset|string|yes|path|nessuno|Nome univoco per l'organizzazione CRM contoso.crm|
|table|string|yes|path|nessuno|Nome dell'entità|
|id|string|yes|path|nessuno|Identificatore del record|
|item| |yes|body|nessuno|Record da aggiornare|

#### Risposta
|Nome|Descrizione|
|---|---|
|200|OK|
|default|Operazione non riuscita.|

### Recupera entità 
Usato per recuperare l'elenco delle entità presenti in un'istanza CRM. ```GET: /datasets/{dataset}/tables```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|dataset|string|yes|path|nessuno|Nome univoco per l'organizzazione CRM contoso.crm|

#### Risposta
|Nome|Descrizione|
|---|---|
|200|OK|
|default|Operazione non riuscita.|


## Definizioni oggetto

#### DataSetsMetadata

|Nome proprietà | Tipo di dati | Obbligatorio|
|---|---|---|
|tabular|non definito|no|
|BLOB|non definito|no|

#### TabularDataSetsMetadata

|Nome proprietà | Tipo di dati |Obbligatorio|
|---|---|---|
|una sezione source|string|no|
|displayName|string|no|
|urlEncoding|string|no|
|tableDisplayName|string|no|
|tablePluralName|string|no|

#### BlobDataSetsMetadata

|Nome proprietà | Tipo di dati |Obbligatorio|
|---|---|---|
|una sezione source|string|no|
|displayName|string|no|
|urlEncoding|string|no|


#### TableMetadata

|Nome proprietà | Tipo di dati |Obbligatorio|
|---|---|---|
|name|string|no|
|title|string|no|
|x-ms-permission|string|no|
|schema|non definito|no|

#### DataSetsList

|Nome proprietà | Tipo di dati |Obbligatorio|
|---|---|---|
|value|array|no|

#### DataSet

|Nome proprietà | Tipo di dati |Obbligatorio|
|---|---|---|
|Nome|string|no|
|DisplayName|string|no|


#### Table

|Nome proprietà | Tipo di dati |Obbligatorio|
|---|---|---|
|Nome|string|no|
|DisplayName|string|no|

#### Item

|Nome proprietà | Tipo di dati |Obbligatorio|
|---|---|---|
|ItemInternalId|string|no|

#### ItemsList

|Nome proprietà | Tipo di dati |Obbligatorio|
|---|---|---|
|value|array|no|


#### TablesList

|Nome proprietà | Tipo di dati |Obbligatorio|
|---|---|---|
|value|array|no|


## Passaggi successivi
Dopo aver aggiunto l'API di CRM Online a PowerApps Enterprise, [concedere autorizzazioni agli utenti](../power-apps/powerapps-manage-api-connection-user-access.md) per l'uso dell'API nelle app.

[Creare un'app per la logica](../app-service-logic/app-service-logic-create-a-logic-app.md)


[9]: ./media/create-api-crmonline/aad-tenant-applications-add-appinfo.png
[10]: ./media/create-api-crmonline/aad-tenant-applications-add-app-properties.png
[12]: ./media/create-api-crmonline/contoso-aad-app-configure.png
[13]: ./media/create-api-crmonline/crmconfig1.png
[14]: ./media/create-api-crmonline/crmconfig2.png
[15]: ./media/create-api-crmonline/crmconfig3.png

<!---HONumber=AcomDC_0302_2016-->


