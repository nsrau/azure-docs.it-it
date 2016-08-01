<properties
    pageTitle="Aggiungere il connettore del database SQL di Azure alle app per la logica | Microsoft Azure"
    description="Panoramica del connettore del database SQL di Azure con i parametri dell'API REST"
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
   ms.date="07/18/2016"
   ms.author="mandia"/>


# Introduzione al connettore del database SQL di Azure
Usando il connettore del database SQL di Azure, creare flussi di lavoro per l'organizzazione che gestiscano i dati nelle tabelle. Vedere anche:

- Creare il flusso di lavoro aggiungendo un nuovo cliente in un database di clienti o aggiornando un ordine in un database di ordini.
- Usare le azioni per ottenere una riga di dati, inserire una nuova riga e persino eliminare una riga. Ad esempio, quando viene creato un record in Dynamics CRM Online (trigger), inserire una riga in un database SQL di Azure (azione).

Questo argomento illustra come usare il connettore del database SQL in un'app per la logica ed elenca le azioni.

>[AZURE.NOTE] Questa versione dell'articolo riguarda la disponibilità generale (GA) delle app per la logica.

Per altre informazioni sulle app per la logica, vedere [Creare un'app per la logica](../app-service-logic/app-service-logic-create-a-logic-app.md).

>[AZURE.INCLUDE [Elementi necessari per iniziare](../../includes/connectors-create-api-sqlazure.md)]

## Connettersi al database SQL di Azure

Prima che l'app per la logica possa accedere a qualsiasi servizio, è necessario creare innanzitutto una *connessione* al servizio. Una connessione fornisce la connettività tra un'app per la logica e un altro servizio. Ad esempio, per connettersi al database SQL, si crea una *connessione* al database SQL. Per creare una connessione, immettere le credenziali che si usano normalmente per accedere al servizio a cui connettersi. Pertanto, per creare la connessione al database SQL, immettere le credenziali del database SQL.

Quando si aggiunge questo connettore all'app per la logica, si crea la connessione al database SQL. La prima volta che si aggiunge questo connettore, vengono richieste le informazioni di connessione:

![](./media/connectors-create-api-sqlazure/connection-details.png)

#### Creare la connessione

1. Immettere i dettagli del database SQL. Le proprietà con l'asterisco sono obbligatorie.

	| Proprietà | Dettagli |
|---|---|
| Connessione tramite gateway | Lasciare deselezionata. Si usa quando ci si connette a un SQL Server locale. |
| Nome connessione* | Immettere un nome per la connessione. | 
| Nome di SQL Server* | Immettere il nome del server, simile a *nomeserver.database.windows.net*. Il nome del server è visualizzato nelle proprietà del database SQL nel portale di Azure ed è mostrato anche nella stringa di connessione. | 
| Nome del database SQL* | Immettere il nome che è stato assegnato al database SQL. Questo nome è riportato nelle proprietà del database SQL nella stringa di connessione: Initial Catalog =*yoursqldbname*. | 
| Nome utente* | Immettere il nome utente creato quando è stato creato il database SQL. Questo nome è riportato nelle proprietà del database SQL nel portale di Azure. | 
| Password* | Immettere la password creata quando è stato creato il database SQL. | 

	Queste credenziali vengono usate per autorizzare l'app per la logica a connettersi e per l'accesso ai dati SQL. Al termine i dettagli della connessione saranno simili ai seguenti:

	![Passaggio di creazione della connessione a SQL Azure](./media/connectors-create-api-sqlazure/sample-connection.png)

2. Selezionare **Create**.

## Usare un trigger

Questo connettore non include trigger. Usare altri trigger per avviare l'app per la logica, fra cui un trigger di ricorrenza, un trigger HTTP webhook, i trigger disponibili con altri connettori e altro ancora. Nella sezione [Creare un'app per la logica](../app-service-logic/app-service-logic-create-a-logic-app.md) è riportato un esempio.

## Usare un'azione
	
Un'azione è un'operazione eseguita dal flusso di lavoro e definita in un'app per la logica.

1. Selezionare il segno più. Sono disponibili varie opzioni: **Add an action** (Aggiungi azione), **Add a condition** (Aggiungi condizione) e le opzioni **More** (Altro).

	![](./media/connectors-create-api-sqlazure/add-action.png)

2. Selezionare **Add an action** (Aggiungi azione).

3. Nella casella di testo digitare "sql" per ottenere l'elenco di tutte le azioni disponibili.

	![](./media/connectors-create-api-sqlazure/sql-1.png)

4. In questo esempio scegliere **SQL Server - Get row** (SQL Server - Ottieni riga). Se esiste già una connessione, selezionare il **nome della tabella** dall'elenco a discesa e immettere l'**ID riga** che si desidera restituire.

	![](./media/connectors-create-api-sqlazure/sample-table.png)

	Se viene chiesto di inserire le informazioni di connessione, immettere i dettagli per creare la connessione. La sezione [Creare la connessione](connectors-create-api-sqlazure.md#create-the-connection) di questo argomento descrive queste proprietà.

	> [AZURE.NOTE] In questo esempio si restituisce una riga da una tabella. Per visualizzare i dati in questa riga aggiungere un'altra azione che crea un file usando i campi della tabella. Ad esempio, aggiungere un'azione OneDrive che usa i campi Nome e Cognome per creare un nuovo file nell'account di archiviazione cloud.

5. **Salvare** le modifiche (angolo in alto a sinistra della barra degli strumenti). L'app per la logica viene salvata e può essere attivata automaticamente.


## Dettagli tecnici

## Azioni
Un'azione è un'operazione eseguita dal flusso di lavoro e definita in un'app per la logica. Il connettore del database SQL include le azioni seguenti.

|Azione|Descrizione|
|--- | ---|
|[ExecuteProcedure](connectors-create-api-sqlazure.md#execute-stored-procedure)|Esegue una stored procedure in SQL|
|[GetRow](connectors-create-api-sqlazure.md#get-row)|Recupera una riga singola da una tabella SQL|
|[GetRows](connectors-create-api-sqlazure.md#get-rows)|Recupera righe da una tabella SQL|
|[InsertRow](connectors-create-api-sqlazure.md#insert-row)|Inserisce una nuova riga in una tabella SQL|
|[DeleteRow](connectors-create-api-sqlazure.md#delete-row)|Elimina una riga da una tabella SQL|
|[GetTables](connectors-create-api-sqlazure.md#get-tables)|Recupera le tabelle da un database SQL|
|[UpdateRow](connectors-create-api-sqlazure.md#update-row)|Aggiorna una riga esistente in una tabella SQL|

### Informazioni dettagliate sulle azioni

In questa sezione si vedranno i dettagli relativi a ogni azione, incluse le proprietà di input obbligatorie o facoltative e gli output corrispondenti associati al connettore.


#### Esegui stored procedure
Esegue una stored procedure in SQL.

| Nome proprietà| Nome visualizzato |Descrizione|
| ---|---|---|
|procedure* | Nome della stored procedure | Il nome della stored procedure da eseguire |
|parameters* | Parametri di input | I parametri sono dinamici e basati sulla stored procedure selezionata. <br/><br/> Ad esempio, se si usa il database di esempio Adventure Works, scegliere la stored procedure *ufnGetCustomerInformation*. Viene visualizzato il parametro di input **ID cliente**. Immettere "6" o uno degli altri ID cliente. |

L'asterisco (*) indica che la proprietà è obbligatoria.

##### Dettagli dell'output
ProcedureResult: riporta il risultato dell'esecuzione della stored procedure

| Nome proprietà | Tipo di dati | Descrizione |
|---|---|---|
|OutputParameters|oggetto|Valori di output dei parametri |
|ReturnCode|integer|Codice restituito di una routine |
|ResultSets|oggetto| Set di risultati|


#### Ottenere la riga 
Recupera una riga singola da una tabella SQL.

| Nome proprietà| Nome visualizzato |Descrizione|
| ---|---|---|
|table* | Nome tabella |Nome della tabella SQL|
|id* | ID di riga |Identificatore univoco della riga da recuperare|

L'asterisco (*) indica che la proprietà è obbligatoria.

##### Dettagli dell'output
Item

| Nome proprietà | Tipo di dati |
|---|---|
|ItemInternalId|string|


#### Ottieni righe 
Recupera righe da una tabella SQL.

|Nome proprietà| Nome visualizzato|Descrizione|
| ---|---|---|
|table*|Nome tabella|Nome della tabella SQL|
|$skip|Ignora conteggio|Numero di elementi da ignorare (impostazione predefinita = 0)|
|$top|Numero massimo di Get|Numero massimo di elementi da recuperare (impostazione predefinita = 256)|
|$filter|Query di filtro|Query di filtro ODATA per limitare il numero di elementi|
|$orderby|Ordina per|Query orderBy ODATA per specificare l'ordine degli elementi|

L'asterisco (*) indica che la proprietà è obbligatoria.

##### Dettagli dell'output
ItemsList

| Nome proprietà | Tipo di dati |
|---|---|
|value|array|


#### Inserisci riga 
Inserisce una nuova riga in una tabella SQL.

|Nome proprietà| Nome visualizzato|Descrizione|
| ---|---|---|
|table*|Nome tabella|Nome della tabella SQL|
|item*|Riga|Riga da inserire nella tabella specificata in SQL|

L'asterisco (*) indica che la proprietà è obbligatoria.

##### Dettagli dell'output
Item

| Nome proprietà | Tipo di dati |
|---|---|
|ItemInternalId|string|


#### Elimina riga 
Elimina una riga da una tabella SQL.

|Nome proprietà| Nome visualizzato|Descrizione|
| ---|---|---|
|table*|Nome tabella|Nome della tabella SQL|
|id*|ID di riga|Identificatore univoco della riga da eliminare|

L'asterisco (*) indica che la proprietà è obbligatoria.

##### Dettagli dell'output
Nessuna.

#### Ottieni tabelle 
Recupera tabelle da un database SQL.

Non sono disponibili parametri per questa chiamata.

##### Dettagli dell'output 
TablesList

| Nome proprietà | Tipo di dati |
|---|---|
|value|array|

#### Aggiorna riga 
Aggiorna una riga esistente in una tabella SQL.

|Nome proprietà| Nome visualizzato|Descrizione|
| ---|---|---|
|table*|Nome tabella|Nome della tabella SQL|
|id*|ID di riga|Identificatore univoco della riga da aggiornare|
|item*|Riga|Riga con i valori aggiornati|

L'asterisco (*) indica che la proprietà è obbligatoria.

##### Dettagli dell'output  
Item

| Nome proprietà | Tipo di dati |
|---|---|
|ItemInternalId|string|


### Risposte HTTP

Quando si effettuano chiamate alle diverse azioni, è possibile ottenere determinate risposte. La tabella seguente indica le risposte e le relative descrizioni:

|Nome|Descrizione|
|---|---|
|200|OK|
|202|Accepted|
|400|Bad Request|
|401|Non autorizzata|
|403|Accesso negato|
|404|Non trovato|
|500|Errore interno del server. Si è verificato un errore sconosciuto|
|default|Operazione non riuscita.|


## Passaggi successivi

[Creare un'app per la logica](../app-service-logic/app-service-logic-create-a-logic-app.md). Esplorare gli altri connettori disponibili nelle app per la logica esaminando il nostro [elenco di API](apis-list.md).

<!---HONumber=AcomDC_0720_2016-->