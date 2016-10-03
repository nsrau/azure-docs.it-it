<properties
    pageTitle="Aggiungere il connettore Informix alle app per la logica | Microsoft Azure"
    description="Panoramica del connettore Informix con i parametri dell'API REST"
    services=""
    documentationCenter="" 
    authors="MandiOhlinger"
    manager="erikre"
    editor=""
    tags="connectors"/> 

<tags
   ms.service="logic-apps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="09/19/2016"
   ms.author="plarsen"/> 


# Introduzione al connettore Informix
Microsoft Connector for Informix connette le app per la logica alle risorse archiviate in un database IBM Informix. Il connettore include un client Microsoft per la comunicazione con computer server Informix remoti su una rete TCP/IP, con database cloud (ad esempio, IBM Informix per Windows in esecuzione nel servizio di virtualizzazione di Azure) e database locali che usano il gateway dati locale. Vedere l'elenco delle piattaforme e delle versioni di IBM Informix supportate alla fine di questo argomento.

Il connettore supporta le operazioni di database seguenti:

- Visualizzazione di un elenco delle tabelle di database
- Lettura di una riga con SELECT
- Lettura di tutte le righe con SELECT
- Aggiunta di una riga con INSERT
- Modifica di una riga con UPDATE
- Rimozione di una riga con DELETE

Questo argomento illustra come usare il connettore in un'app per la logica per elaborare operazioni su database.

>[AZURE.NOTE] Questa versione dell'articolo si applica alla la disponibilità generale delle app per la logica.

Per altre informazioni sulle app per la logica, vedere [Creare un'app per la logica](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Azioni delle app per la logica
Il connettore supporta le azioni delle app per la logica seguenti:

- GetTables
- GetRow
- GetRows
- InsertRow
- UpdateRow
- DeleteRow


## Definire un'app per la logica per elencare le tabelle
La definizione di un'app per la logica per qualsiasi operazione prevede numerosi passaggi eseguiti tramite il portale di Microsoft Azure. È possibile definire un'azione dell'app per la logica per elencare le tabelle in un database Informix, indicando al connettore di elaborare un'istruzione Informix relativa allo schema (CALL SYSIBM.SQLTABLES).

### Definire l'istanza dell'app per la logica
1.	Nella **schermata iniziale di Azure** selezionare **+** (segno più), **Web e dispositivi mobili** e quindi **App per la logica**.
2.	Immettere un valore in **Nome** (ad esempio "**InformixgetTables**"), **Sottoscrizione**, **Gruppo di risorse**, **Località** e **Piano di servizio app**. Fare clic su **Aggiungi al dashboard** e quindi selezionare **Crea**.

### Definire il trigger e l'azione dell'app per la logica
1.	In **Progettazione app per la logica** fare clic su **LogicApp vuoto** nell'elenco **Modelli**.
2.	Nell'elenco dei **trigger** fare clic su **Ricorrenza**.
3.	Nel trigger **Ricorrenza** fare clic su **Modifica**, fare clic sull'elenco a discesa **Frequenza** per selezionare **Giorno** e quindi fare clic su **Intervallo** per digitare **7**.
4.	Fare clic sulla casella **+ Nuovo passaggio** e quindi su **Aggiungi un'azione**.
5.	Nell'elenco delle **azioni** digitare **informix** nella casella di modifica **Cercare altre azioni** e quindi fare clic su **Informix - Get tables (Preview)** (Informix - Ottieni tabelle - anteprima).

	![](./media/connectors-create-api-informix/InformixconnectorActions.png) 

6.	Nel riquadro di configurazione **Informix - Get tables** (Informix - Ottieni tabelle) fare clic sulla **casella di controllo** per abilitare **Connetti tramite gateway dati locale**. Le impostazioni verranno modificate da cloud a locale.
	- Digitare un valore in **Server** sotto forma di indirizzo o alias-due punti-numero di porta, ad esempio **ibmserver01:9089**.
	- Digitare un valore in **Database**, ad esempio **nwind**.
	- Selezionare un valore in **Autenticazione**, ad esempio **Basic**.
	- Digitare un valore in **Nome utente**, ad esempio **informix**.
	- Digitare un valore in **Password**, ad esempio **Password1**.
	- Selezionare un valore in **Gateway**, ad esempio **datagateway01**.
7. Fare clic su **Crea** e quindi su **Salva**.

	![](./media/connectors-create-api-informix/InformixconnectorOnPremisesDataGatewayConnection.png) 

8.	Nel pannello **InformixgetTables** fare clic sul primo elemento dell'elenco **Tutte le esecuzioni** (corrispondente all'esecuzione più recente) in **Riepilogo**.
9.	Nel pannello **Esecuzione dell'app per la logica** fare clic su **Dettagli esecuzione**. Nell'elenco **Azione** fare clic su **Get\_tables**. Verificare che il valore di **Stato** sia **Operazione riuscita**. Fare clic su **Collegamento agli input**. Visualizzare gli input. Fare clic su **Collegamento agli output**. Visualizzare gli output, che dovrebbero includere un elenco di tabelle.

	![](./media/connectors-create-api-informix/InformixconnectorGetTablesLogicAppRunOutputs.png) 

## Definire le connessioni Informix
Il connettore supporta connessioni a database sia locali che cloud con le proprietà di connessione seguenti.

Proprietà | Descrizione
--- | ---
server | La proprietà server obbligatoria accetta un valore stringa che rappresenta un alias o un indirizzo TCP/IP, in formato IPv4 o IPv6, delimitato da due punti e seguito da un numero di porta TCP/IP. 
database | La proprietà database obbligatoria accetta un valore stringa che rappresenta un nome di database relazionale (RDBNAM) DRDA. Informix accetta una stringa di 128 byte (il database è definito come nome di database IBM Informix, ovvero come dbname).
authentication | La proprietà authentication facoltativa accetta un valore elemento di elenco, ovvero Basic o Windows (kerberos). 
username | La proprietà username obbligatoria accetta un valore stringa.
password | La proprietà password obbligatoria accetta un valore stringa.
gateway | La proprietà gateway obbligatoria accetta un valore elemento di elenco, che rappresenta il gateway dati locale definito per le app per la logica nel gruppo di archiviazione.  

## Definire una connessione gateway locale
Il connettore può accedere a un database Informix locale tramite il gateway dati locale. Per altre informazioni, vedere gli argomenti relativi al gateway.

1. Nel riquadro di configurazione **Gateway** fare clic sulla **casella di controllo** per abilitare **Connetti tramite gateway**. Le impostazioni verranno modificate da cloud a locale.
2. Digitare un valore in **Server** sotto forma di indirizzo o alias-due punti-numero di porta, ad esempio **ibmserver01:9089**.
3. Digitare un valore in **Database**, ad esempio **nwind**.
4. Selezionare un valore in **Autenticazione**, ad esempio **Basic**.
5. Digitare un valore in **Nome utente**, ad esempio **informix**.
6. Digitare un valore in **Password**, ad esempio **Password1**.
7. Selezionare un valore in **Gateway**, ad esempio **datagateway01**.
8. Fare clic su **Crea** per continuare.

	![](./media/connectors-create-api-informix/InformixconnectorOnPremisesDataGatewayConnection.png) 

## Definire una connessione cloud
Il connettore può accedere a un database Informix cloud.

1. Nel riquadro di configurazione **Gateway** lasciare disabilitata (deselezionata) la **casella di controllo** **Connetti tramite gateway**.
2. Digitare un valore in **Nome connessione**, ad esempio **hisdemo2**.
3. Digitare un valore per il nome del server Informix in **Server** sotto forma di indirizzo o alias-due punti-numero di porta, ad esempio **hisdemo2.cloudapp.net:9089**.
3. Digitare un valore per il nome del database Informix in **Database**, ad esempio **nwind**.
4. Digitare un valore in **Nome utente**, ad esempio **informix**.
5. Digitare un valore in **Password**, ad esempio **Password1**.
6. Fare clic su **Crea** per continuare.

	![](./media/connectors-create-api-informix/InformixconnectorCloudConnection.png) 

## Definire l'app per la logica per recuperare tutte le righe con SELECT
È possibile definire un'azione dell'app per la logica per recuperare tutte le righe in una tabella Informix, indicando al connettore di elaborare un'istruzione Informix SELECT, ad esempio **SELECT * FROM AREA**.

### Definire l'istanza dell'app per la logica
1.	Nella **schermata iniziale di Azure** selezionare **+** (segno più), **Web e dispositivi mobili** e quindi **App per la logica**.
2.	Immettere un valore in **Nome** (ad esempio "**InformixgetRows**"), **Sottoscrizione**, **Gruppo di risorse**, **Località** e **Piano di servizio app**. Fare clic su **Aggiungi al dashboard** e quindi selezionare **Crea**.

### Definire il trigger e l'azione dell'app per la logica
1.	In **Progettazione app per la logica** fare clic su **LogicApp vuoto** nell'elenco **Modelli**.
2.	Nell'elenco dei **trigger** fare clic su **Ricorrenza**.
3.	Nel trigger **Ricorrenza** fare clic su **Modifica**, fare clic sull'elenco a discesa **Frequenza** per selezionare **Giorno** e quindi fare clic su **Intervallo** per digitare **7**.
4.	Fare clic sulla casella **+ Nuovo passaggio** e quindi su **Aggiungi un'azione**.
5.	Nell'elenco delle **azioni** digitare **informix** nella casella di modifica **Cercare altre azioni** e quindi fare clic su **Informix - Get rows (Preview)** (Informix - Ottieni righe - anteprima).
6. Nell'azione **Get rows (Preview)** (Ottieni righe - anteprima) fare clic su **Cambia connessione**.
7. Nel riquadro di configurazione **Connessioni** fare clic su **Crea nuova**.

	![](./media/connectors-create-api-informix/InformixconnectorNewConnection.png) 
  
8. Nel riquadro di configurazione **Gateway** lasciare disabilitata (deselezionata) la **casella di controllo** **Connetti tramite gateway**.
	- Digitare un valore in **Nome connessione**, ad esempio **hisdemo2**.
	- Digitare un valore per il nome del server Informix in **Server** sotto forma di indirizzo o alias-due punti-numero di porta, ad esempio **hisdemo2.cloudapp.net:9089**.
	- Digitare un valore per il nome del database Informix in **Database**, ad esempio **nwind**.
	- Digitare un valore in **Nome utente**, ad esempio **informix**.
	- Digitare un valore in **Password**, ad esempio **Password1**.
9. Fare clic su **Crea** per continuare.

	![](./media/connectors-create-api-informix/InformixconnectorCloudConnection.png) 

10. Nell'elenco **Nome tabella** fare clic sulla **freccia in giù** e quindi su **AREA**.
11. Facoltativamente, fare clic su **Mostra le opzioni avanzate** per specificare le opzioni di query.
12. Fare clic su **Save**.

	![](./media/connectors-create-api-informix/InformixconnectorGetRowsTableName.png) 

13.	Nel pannello **InformixgetRows** fare clic sul primo elemento dell'elenco **Tutte le esecuzioni** (corrispondente all'esecuzione più recente) in **Riepilogo**.
14.	Nel pannello **Esecuzione dell'app per la logica** fare clic su **Dettagli esecuzione**. Nell'elenco **Azione** fare clic su **Get\_rows**. Verificare che il valore di **Stato** sia **Operazione riuscita**. Fare clic su **Collegamento agli input**. Visualizzare gli input. Fare clic su **Collegamento agli output**. Visualizzare gli output, che dovrebbero includere un elenco di righe.

	![](./media/connectors-create-api-informix/InformixconnectorGetRowsOutputs.png) 

## Definire l'app per la logica per aggiungere una riga con INSERT
È possibile definire un'azione dell'app per la logica per aggiungere una riga in una tabella Informix, indicando al connettore di elaborare un'istruzione Informix INSERT, ad esempio **INSERT INTO AREA (AREAID, AREADESC, REGIONID) VALUES ('99999', 'Area 99999', 102)**.

### Definire l'istanza dell'app per la logica
1.	Nella **schermata iniziale di Azure** selezionare **+** (segno più), **Web e dispositivi mobili** e quindi **App per la logica**.
2.	Immettere un valore in **Nome** (ad esempio "**InformixinsertRow**"), **Sottoscrizione**, **Gruppo di risorse**, **Località** e **Piano di servizio app**. Fare clic su **Aggiungi al dashboard** e quindi selezionare **Crea**.

### Definire il trigger e l'azione dell'app per la logica
1.	In **Progettazione app per la logica** fare clic su **LogicApp vuoto** nell'elenco **Modelli**.
2.	Nell'elenco dei **trigger** fare clic su **Ricorrenza**.
3.	Nel trigger **Ricorrenza** fare clic su **Modifica**, fare clic sull'elenco a discesa **Frequenza** per selezionare **Giorno** e quindi fare clic su **Intervallo** per digitare **7**.
4.	Fare clic sulla casella **+ Nuovo passaggio** e quindi su **Aggiungi un'azione**.
5.	Nell'elenco delle **azioni** digitare **informix** nella casella di modifica **Cercare altre azioni** e quindi fare clic su **Informix - Insert row (Preview)** (Informix - Inserisci riga - anteprima).
6. Nell'azione **Get rows (Preview)** (Ottieni righe - anteprima) fare clic su **Cambia connessione**.
7. Nel riquadro di configurazione **Connessioni** fare clic per selezionare una connessione. Ad esempio, fare clic su **hisdemo2**.

	![](./media/connectors-create-api-informix/InformixconnectorChangeConnection.png) 

8. Nell'elenco **Nome tabella** fare clic sulla **freccia in giù** e quindi su **AREA**.
9. Immettere valori per tutte le colonne obbligatorie (asterisco rosso). Digitare ad esempio "**99999**" per **AREAID**, "**Area 99999**" e quindi "**102**" per **REGIONID**.
10. Fare clic su **Save**.

	![](./media/connectors-create-api-informix/InformixconnectorInsertRowValues.png) 
 
11.	Nel pannello **InformixinsertRow** fare clic sul primo elemento dell'elenco **Tutte le esecuzioni** (corrispondente all'esecuzione più recente) in **Riepilogo**.
12.	Nel pannello **Esecuzione dell'app per la logica** fare clic su **Dettagli esecuzione**. Nell'elenco **Azione** fare clic su **Get\_rows**. Verificare che il valore di **Stato** sia **Operazione riuscita**. Fare clic su **Collegamento agli input**. Visualizzare gli input. Fare clic su **Collegamento agli output**. Visualizzare gli output, che dovrebbero includere la nuova riga.

	![](./media/connectors-create-api-informix/InformixconnectorInsertRowOutputs.png) 

## Definire l'app per la logica per recuperare una riga con SELECT
È possibile definire un'azione dell'app per la logica per recuperare una riga in una tabella Informix, indicando al connettore di elaborare un'istruzione Informix SELECT WHERE, ad esempio **SELECT FROM AREA WHERE AREAID = '99999'**.

### Definire l'istanza dell'app per la logica
1.	Nella **schermata iniziale di Azure** selezionare **+** (segno più), **Web e dispositivi mobili** e quindi **App per la logica**.
2.	Immettere un valore in **Nome** (ad esempio "**InformixgetRow**"), **Sottoscrizione**, **Gruppo di risorse**, **Località** e **Piano di servizio app**. Fare clic su **Aggiungi al dashboard** e quindi selezionare **Crea**.

### Definire il trigger e l'azione dell'app per la logica
1.	In **Progettazione app per la logica** fare clic su **LogicApp vuoto** nell'elenco **Modelli**.
2.	Nell'elenco dei **trigger** fare clic su **Ricorrenza**.
3.	Nel trigger **Ricorrenza** fare clic su **Modifica**, fare clic sull'elenco a discesa **Frequenza** per selezionare **Giorno** e quindi fare clic su **Intervallo** per digitare **7**.
4.	Fare clic sulla casella **+ Nuovo passaggio** e quindi su **Aggiungi un'azione**.
5.	Nell'elenco delle **azioni** digitare **informix** nella casella di modifica **Cercare altre azioni** e quindi fare clic su **Informix - Get rows (Preview)** (Informix - Ottieni righe - anteprima).
6. Nell'azione **Get rows (Preview)** (Ottieni righe - anteprima) fare clic su **Cambia connessione**.
7. Nel riquadro di configurazione **Connessioni** fare clic per selezionare una connessione esistente. Ad esempio, fare clic su **hisdemo2**.

	![](./media/connectors-create-api-informix/InformixconnectorChangeConnection.png) 

8. Nell'elenco **Nome tabella** fare clic sulla **freccia in giù** e quindi su **AREA**.
9. Immettere valori per tutte le colonne obbligatorie (asterisco rosso). Digitare ad esempio "**99999**" per **AREAID**.
10. Facoltativamente, fare clic su **Mostra le opzioni avanzate** per specificare le opzioni di query.
11. Fare clic su **Save**.

	![](./media/connectors-create-api-informix/InformixconnectorGetRowValues.png) 

12.	Nel pannello **InformixgetRow** fare clic sul primo elemento dell'elenco **Tutte le esecuzioni** (corrispondente all'esecuzione più recente) in **Riepilogo**.
13.	Nel pannello **Esecuzione dell'app per la logica** fare clic su **Dettagli esecuzione**. Nell'elenco **Azione** fare clic su **Get\_rows**. Verificare che il valore di **Stato** sia **Operazione riuscita**. Fare clic su **Collegamento agli input**. Visualizzare gli input. Fare clic su **Collegamento agli output**. Visualizzare gli output, che dovrebbero includere la riga.

	![](./media/connectors-create-api-informix/InformixconnectorGetRowOutputs.png) 

## Definire l'app per la logica per modificare una riga con UPDATE
È possibile definire un'azione dell'app per la logica per modificare una riga in una tabella Informix, indicando al connettore di elaborare un'istruzione Informix UPDATE, ad esempio **UPDATE AREA SET AREAID = '99999', AREADESC = 'Area 99999', REGIONID = 102)**.

### Definire l'istanza dell'app per la logica
1.	Nella **schermata iniziale di Azure** selezionare **+** (segno più), **Web e dispositivi mobili** e quindi **App per la logica**.
2.	Immettere un valore in **Nome** (ad esempio "**InformixupdateRow**"), **Sottoscrizione**, **Gruppo di risorse**, **Località** e **Piano di servizio app**. Fare clic su **Aggiungi al dashboard** e quindi selezionare **Crea**.

### Definire il trigger e l'azione dell'app per la logica
1.	In **Progettazione app per la logica** fare clic su **LogicApp vuoto** nell'elenco **Modelli**.
2.	Nell'elenco dei **trigger** fare clic su **Ricorrenza**.
3.	Nel trigger **Ricorrenza** fare clic su **Modifica**, fare clic sull'elenco a discesa **Frequenza** per selezionare **Giorno** e quindi fare clic su **Intervallo** per digitare **7**.
4.	Fare clic sulla casella **+ Nuovo passaggio** e quindi su **Aggiungi un'azione**.
5.	Nell'elenco delle **azioni** digitare **informix** nella casella di modifica **Cercare altre azioni** e quindi fare clic su **Informix - Update row (Preview)** (Informix - Aggiorna riga - anteprima).
6. Nell'azione **Get rows (Preview)** (Ottieni righe - anteprima) fare clic su **Cambia connessione**.
7. Nel riquadro di configurazione **Connessioni** fare clic per selezionare una connessione esistente. Ad esempio, fare clic su **hisdemo2**.

	![](./media/connectors-create-api-informix/InformixconnectorChangeConnection.png) 

8. Nell'elenco **Nome tabella** fare clic sulla **freccia in giù** e quindi su **AREA**.
9. Immettere valori per tutte le colonne obbligatorie (asterisco rosso). Digitare ad esempio "**99999**" per **AREAID**, "**Updated 99999**" e quindi "**102**" per **REGIONID**.
10. Fare clic su **Save**.

	![](./media/connectors-create-api-informix/InformixconnectorUpdateRowValues.png) 

11.	Nel pannello **InformixupdateRow** fare clic sul primo elemento dell'elenco **Tutte le esecuzioni** (corrispondente all'esecuzione più recente) in **Riepilogo**.
12.	Nel pannello **Esecuzione dell'app per la logica** fare clic su **Dettagli esecuzione**. Nell'elenco **Azione** fare clic su **Get\_rows**. Verificare che il valore di **Stato** sia **Operazione riuscita**. Fare clic su **Collegamento agli input**. Visualizzare gli input. Fare clic su **Collegamento agli output**. Visualizzare gli output, che dovrebbero includere la nuova riga.

	![](./media/connectors-create-api-informix/InformixconnectorUpdateRowOutputs.png) 

## Definire l'app per la logica per rimuovere una riga con DELETE
È possibile definire un'azione dell'app per la logica per rimuovere una riga in una tabella Informix, indicando al connettore di elaborare un'istruzione Informix DELETE, ad esempio **DELETE FROM AREA WHERE AREAID = '99999'**.

### Definire l'istanza dell'app per la logica
1.	Nella **schermata iniziale di Azure** selezionare **+** (segno più), **Web e dispositivi mobili** e quindi **App per la logica**.
2.	Immettere un valore in **Nome** (ad esempio "**InformixdeleteRow**"), **Sottoscrizione**, **Gruppo di risorse**, **Località** e **Piano di servizio app**. Fare clic su **Aggiungi al dashboard** e quindi selezionare **Crea**.

### Definire il trigger e l'azione dell'app per la logica
1.	In **Progettazione app per la logica** fare clic su **LogicApp vuoto** nell'elenco **Modelli**.
2.	Nell'elenco dei **trigger** fare clic su **Ricorrenza**.
3.	Nel trigger **Ricorrenza** fare clic su **Modifica**, fare clic sull'elenco a discesa **Frequenza** per selezionare **Giorno** e quindi fare clic su **Intervallo** per digitare **7**.
4.	Fare clic sulla casella **+ Nuovo passaggio** e quindi su **Aggiungi un'azione**.
5.	Nell'elenco delle **azioni** digitare **informix** nella casella di modifica **Cercare altre azioni** e quindi fare clic su **Informix - Delete row (Preview)** (Informix - Elimina riga - anteprima).
6. Nell'azione **Get rows (Preview)** (Ottieni righe - anteprima) fare clic su **Cambia connessione**.
7. Nel riquadro di configurazione **Connessioni** fare clic per selezionare una connessione esistente. Ad esempio, fare clic su **hisdemo2**.

	![](./media/connectors-create-api-informix/InformixconnectorChangeConnection.png) 

8. Nell'elenco **Nome tabella** fare clic sulla **freccia in giù** e quindi su **AREA**.
9. Immettere valori per tutte le colonne obbligatorie (asterisco rosso). Digitare ad esempio "**99999**" per **AREAID**.
10. Fare clic su **Save**.

	![](./media/connectors-create-api-informix/InformixconnectorDeleteRowValues.png) 

11.	Nel pannello **InformixdeleteRow** fare clic sul primo elemento dell'elenco **Tutte le esecuzioni** (corrispondente all'esecuzione più recente) in **Riepilogo**.
12.	Nel pannello **Esecuzione dell'app per la logica** fare clic su **Dettagli esecuzione**. Nell'elenco **Azione** fare clic su **Get\_rows**. Verificare che il valore di **Stato** sia **Operazione riuscita**. Fare clic su **Collegamento agli input**. Visualizzare gli input. Fare clic su **Collegamento agli output**. Visualizzare gli output, che dovrebbero includere la riga eliminata.

	![](./media/connectors-create-api-informix/InformixconnectorDeleteRowOutputs.png) 

## Dettagli tecnici

## Azioni
Un'azione è un'operazione eseguita dal flusso di lavoro e definita in un'app per la logica. Il connettore di database Informix include le azioni seguenti.

|Azione|Descrizione|
|--- | ---|
|[GetRow](connectors-create-api-informix.md#get-row)|Recupera una singola riga da una tabella Informix|
|[GetRows](connectors-create-api-informix.md#get-rows)|Recupera righe da una tabella Informix|
|[InsertRow](connectors-create-api-informix.md#insert-row)|Inserisce una nuova riga in una tabella Informix|
|[DeleteRow](connectors-create-api-informix.md#delete-row)|Elimina una riga da una tabella Informix|
|[GetTables](connectors-create-api-informix.md#get-tables)|Recupera le tabelle da un database Informix|
|[UpdateRow](connectors-create-api-informix.md#update-row)|Aggiorna una riga esistente in una tabella Informix|

### Informazioni dettagliate sulle azioni

In questa sezione si vedranno i dettagli relativi a ogni azione, incluse le proprietà di input obbligatorie o facoltative e gli output corrispondenti associati al connettore.

#### Ottenere la riga 
Recupera una singola riga da una tabella Informix.

| Nome proprietà| Nome visualizzato |Descrizione|
| ---|---|---|
|table* | Nome tabella |Nome della tabella Informix|
|id* | ID di riga |Identificatore univoco della riga da recuperare|

L'asterisco (*) indica che la proprietà è obbligatoria.

##### Dettagli dell'output
Item

| Nome proprietà | Tipo di dati |
|---|---|
|ItemInternalId|string|


#### Ottieni righe 
Recupera righe da una tabella Informix.

|Nome proprietà| Nome visualizzato|Descrizione|
| ---|---|---|
|table*|Nome tabella|Nome della tabella Informix|
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
Inserisce una nuova riga in una tabella Informix.

|Nome proprietà| Nome visualizzato|Descrizione|
| ---|---|---|
|table*|Nome tabella|Nome della tabella Informix|
|item*|Riga|Riga da inserire nella tabella specificata in Informix|

L'asterisco (*) indica che la proprietà è obbligatoria.

##### Dettagli dell'output
Item

| Nome proprietà | Tipo di dati |
|---|---|
|ItemInternalId|string|


#### Elimina riga 
Elimina una riga da una tabella Informix.

|Nome proprietà| Nome visualizzato|Descrizione|
| ---|---|---|
|table*|Nome tabella|Nome della tabella Informix|
|id*|ID di riga|Identificatore univoco della riga da eliminare|

L'asterisco (*) indica che la proprietà è obbligatoria.

##### Dettagli output
Nessuna.

#### Ottieni tabelle 
Recupera le tabelle da un database Informix.

Non sono disponibili parametri per questa chiamata.

##### Dettagli dell'output 
TablesList

| Nome proprietà | Tipo di dati |
|---|---|
|value|array|

#### Aggiorna riga 
Aggiorna una riga esistente in una tabella Informix.

|Nome proprietà| Nome visualizzato|Descrizione|
| ---|---|---|
|table*|Nome tabella|Nome della tabella Informix|
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


## Piattaforme e versioni di Informix supportate
Il connettore supporta le versioni di IBM Informix seguenti, in caso di configurazione per il supporto di connessioni client DRDA (Distributed Relational Database Architecture).
- IBM Informix 12.1
- IBM Informix 11.7

 
## Passaggi successivi

[Creare un'app per la logica](../app-service-logic/app-service-logic-create-a-logic-app.md). Esplorare gli altri connettori disponibili nelle app per la logica nell'[elenco delle API](apis-list.md).

<!---HONumber=AcomDC_0921_2016-->