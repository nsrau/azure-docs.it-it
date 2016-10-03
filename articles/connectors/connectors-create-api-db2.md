<properties
    pageTitle="Aggiungere il connettore DB2 alle app per la logica | Microsoft Azure"
    description="Panoramica del connettore DB2 con i parametri dell'API REST"
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


# Introduzione al connettore DB2
Microsoft Connector for DB2 connette le app per la logica alle risorse archiviate in un database IBM DB2. Questo connettore include un client Microsoft per la comunicazione con computer server DB2 remoti su una rete TCP/IP, con database cloud (ad esempio, IBM Bluemix dashDB o IBM DB2 per Windows in esecuzione nel servizio di virtualizzazione di Azure) e database locali che usano il gateway dati locale. Vedere l'elenco delle piattaforme e delle versioni di IBM DB2 supportate alla fine di questo argomento.

Il connettore DB2 supporta le operazioni di database seguenti:

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
La definizione di un'app per la logica per qualsiasi operazione prevede numerosi passaggi eseguiti tramite il portale di Microsoft Azure. È possibile definire un'azione dell'app per la logica per elencare le tabelle in un database DB2, indicando al connettore di elaborare un'istruzione DB2 relativa allo schema (CALL SYSIBM.SQLTABLES).

### Definire l'istanza dell'app per la logica
1.	Nella **schermata iniziale di Azure** selezionare **+** (segno più), **Web e dispositivi mobili** e quindi **App per la logica**.
2.	Immettere un valore in **Nome** (ad esempio "**Db2getTables**"), **Sottoscrizione**, **Gruppo di risorse**, **Località** e **Piano di servizio app**. Fare clic su **Aggiungi al dashboard** e quindi selezionare **Crea**.

### Definire il trigger e l'azione dell'app per la logica
1.	In **Progettazione app per la logica** fare clic su **LogicApp vuoto** nell'elenco **Modelli**.
2.	Nell'elenco dei **trigger** fare clic su **Ricorrenza**.
3.	Nel trigger **Ricorrenza** fare clic su **Modifica**, fare clic sull'elenco a discesa **Frequenza** per selezionare **Giorno** e quindi fare clic su **Intervallo** per digitare **7**.
4.	Fare clic sulla casella **+ Nuovo passaggio** e quindi su **Aggiungi un'azione**.
5.	Nell'elenco delle **azioni** digitare **db2** nella casella di modifica **Cercare altre azioni** e quindi fare clic su **DB2 - Get tables (Preview)** (DB2 - Ottieni tabelle - anteprima).

	![](./media/connectors-create-api-db2/Db2connectorActions.png)  

6.	Nel riquadro di configurazione **DB2 - Get tables** (DB2- Ottieni tabelle) fare clic sulla **casella di controllo** per abilitare **Connetti tramite gateway dati locale**. Le impostazioni verranno modificate da cloud a locale.
	- Digitare un valore in **Server** sotto forma di indirizzo o alias-due punti-numero di porta, ad esempio **ibmserver01:50000**.
	- Digitare un valore in **Database**, ad esempio **nwind**.
	- Selezionare un valore in **Autenticazione**, ad esempio **Basic**.
	- Digitare un valore in **Nome utente**, ad esempio **db2admin**.
	- Digitare un valore in **Password**, ad esempio **Password1**.
	- Selezionare un valore in **Gateway**, ad esempio **datagateway01**.
7. Fare clic su **Crea** e quindi su **Salva**.

	![](./media/connectors-create-api-db2/Db2connectorOnPremisesDataGatewayConnection.png)  

8.	Nel pannello **Db2getTables** fare clic sul primo elemento dell'elenco **Tutte le esecuzioni** (corrispondente all'esecuzione più recente) in **Riepilogo**.
9.	Nel pannello **Esecuzione dell'app per la logica** fare clic su **Dettagli esecuzione**. Nell'elenco **Azione** fare clic su **Get\_tables**. Verificare che il valore di **Stato** sia **Operazione riuscita**. Fare clic su **Collegamento agli input**. Visualizzare gli input. Fare clic su **Collegamento agli output**. Visualizzare gli output, che dovrebbero includere un elenco di tabelle.

	![](./media/connectors-create-api-db2/Db2connectorGetTablesLogicAppRunOutputs.png)  

## Definire le connessioni DB2
Il connettore supporta connessioni a database sia locali che cloud con le proprietà di connessione seguenti.

Proprietà | Descrizione
--- | ---
server | La proprietà server obbligatoria accetta un valore stringa che rappresenta un alias o un indirizzo TCP/IP, in formato IPv4 o IPv6, delimitato da due punti e seguito da un numero di porta TCP/IP. 
database | La proprietà database obbligatoria accetta un valore stringa che rappresenta un nome di database relazionale (RDBNAM) DRDA. DB2 per z/OS accetta una stringa di 16 byte (il database è definito come posizione di IBM DB2 per z/OS). DB2 per i5/OS accetta una stringa di 18 byte (il database è definito come database relazionale IBM DB2 per i). DB2 per LUW accetta una stringa di 8 byte.
authentication | La proprietà authentication facoltativa accetta un valore elemento di elenco, ovvero Basic o Windows (kerberos). 
username | La proprietà username obbligatoria accetta un valore stringa. DB2 per z/OS accetta una stringa di 8 byte. DB2 per i accetta una stringa di 10 byte. DB2 per Linux o UNIX accetta una stringa di 8 byte. DB2 per Windows accetta una stringa di 30 byte.
password | La proprietà password obbligatoria accetta un valore stringa.
gateway | La proprietà gateway obbligatoria accetta un valore elemento di elenco, che rappresenta il gateway dati locale definito per le app per la logica nel gruppo di archiviazione.  

## Definire una connessione gateway locale
Il connettore può accedere a un database DB2 locale tramite il gateway locale. Per altre informazioni, vedere gli argomenti relativi al gateway.

1. Nel riquadro di configurazione **Gateway** fare clic sulla **casella di controllo** per abilitare **Connetti tramite gateway**. Le impostazioni verranno modificate da cloud a locale.
2. Digitare un valore in **Server** sotto forma di indirizzo o alias-due punti-numero di porta, ad esempio **ibmserver01:50000**.
3. Digitare un valore in **Database**, ad esempio **nwind**.
4. Selezionare un valore in **Autenticazione**, ad esempio **Basic**.
5. Digitare un valore in **Nome utente**, ad esempio **db2admin**.
6. Digitare un valore in **Password**, ad esempio **Password1**.
7. Selezionare un valore in **Gateway**, ad esempio **datagateway01**.
8. Fare clic su **Crea** per continuare.

	![](./media/connectors-create-api-db2/Db2connectorOnPremisesDataGatewayConnection.png)  

## Definire una connessione cloud
Il connettore può accedere a un database DB2 cloud.

1. Nel riquadro di configurazione **Gateway** lasciare disabilitata (deselezionata) la **casella di controllo** **Connetti tramite gateway**.
2. Digitare un valore in **Nome connessione**, ad esempio **hisdemo2**.
3. Digitare un valore per il nome del server DB2 in **Server** sotto forma di indirizzo o alias-due punti-numero di porta, ad esempio **hisdemo2.cloudapp.net:50000**.
3. Digitare un valore per il nome del database DB2 in **Database**, ad esempio **nwind**.
4. Digitare un valore in **Nome utente**, ad esempio **db2admin**.
5. Digitare un valore in **Password**, ad esempio **Password1**.
6. Fare clic su **Crea** per continuare.

	![](./media/connectors-create-api-db2/Db2connectorCloudConnection.png)  

## Definire l'app per la logica per recuperare tutte le righe con SELECT
È possibile definire un'azione dell'app per la logica per recuperare tutte le righe in una tabella DB2, indicando al connettore di elaborare un'istruzione DB2 SELECT, ad esempio **SELECT * FROM AREA**.

### Definire l'istanza dell'app per la logica
1.	Nella **schermata iniziale di Azure** selezionare **+** (segno più), **Web e dispositivi mobili** e quindi **App per la logica**.
2.	Immettere un valore in **Nome** (ad esempio "**Db2getRows**"), **Sottoscrizione**, **Gruppo di risorse**, **Località** e **Piano di servizio app**. Fare clic su **Aggiungi al dashboard** e quindi selezionare **Crea**.

### Definire il trigger e l'azione dell'app per la logica
1.	In **Progettazione app per la logica** fare clic su **LogicApp vuoto** nell'elenco **Modelli**.
2.	Nell'elenco dei **trigger** fare clic su **Ricorrenza**.
3.	Nel trigger **Ricorrenza** fare clic su **Modifica**, fare clic sull'elenco a discesa **Frequenza** per selezionare **Giorno** e quindi fare clic su **Intervallo** per digitare **7**.
4.	Fare clic sulla casella **+ Nuovo passaggio** e quindi su **Aggiungi un'azione**.
5.	Nell'elenco delle **azioni** digitare **db2** nella casella di modifica **Cercare altre azioni** e quindi fare clic su **DB2 - Get rows (Preview)** (DB2 - Ottieni righe - anteprima).
6. Nell'azione **Get rows (Preview)** (Ottieni righe - anteprima) fare clic su **Cambia connessione**.
7. Nel riquadro di configurazione **Connessioni** fare clic su **Crea nuova**.

	![](./media/connectors-create-api-db2/Db2connectorNewConnection.png)  
  
8. Nel riquadro di configurazione **Gateway** lasciare disabilitata (deselezionata) la **casella di controllo** **Connetti tramite gateway**.
	- Digitare un valore in **Nome connessione**, ad esempio **hisdemo2**.
	- Digitare un valore per il nome del server DB2 in **Server** sotto forma di indirizzo o alias-due punti-numero di porta, ad esempio **hisdemo2.cloudapp.net:50000**.
	- Digitare un valore per il nome del database DB2 in **Database**, ad esempio **nwind**.
	- Digitare un valore in **Nome utente**, ad esempio **db2admin**.
	- Digitare un valore in **Password**, ad esempio **Password1**.
9. Fare clic su **Crea** per continuare.

	![](./media/connectors-create-api-db2/Db2connectorCloudConnection.png)  

10. Nell'elenco **Nome tabella** fare clic sulla **freccia in giù** e quindi su **AREA**.
11. Facoltativamente, fare clic su **Mostra le opzioni avanzate** per specificare le opzioni di query.
12. Fare clic su **Save**.

	![](./media/connectors-create-api-db2/Db2connectorGetRowsTableName.png)  

13.	Nel pannello **Db2getRows** fare clic sul primo elemento dell'elenco **Tutte le esecuzioni** (corrispondente all'esecuzione più recente) in **Riepilogo**.
14.	Nel pannello **Esecuzione dell'app per la logica** fare clic su **Dettagli esecuzione**. Nell'elenco **Azione** fare clic su **Get\_rows**. Verificare che il valore di **Stato** sia **Operazione riuscita**. Fare clic su **Collegamento agli input**. Visualizzare gli input. Fare clic su **Collegamento agli output**. Visualizzare gli output, che dovrebbero includere un elenco di righe.

	![](./media/connectors-create-api-db2/Db2connectorGetRowsOutputs.png)  

## Definire l'app per la logica per aggiungere una riga con INSERT
È possibile definire un'azione dell'app per la logica per aggiungere una riga in una tabella DB2, indicando al connettore di elaborare un'istruzione DB2 INSERT, ad esempio **INSERT INTO AREA (AREAID, AREADESC, REGIONID) VALUES ('99999', 'Area 99999', 102)**.

### Definire l'istanza dell'app per la logica
1.	Nella **schermata iniziale di Azure** selezionare **+** (segno più), **Web e dispositivi mobili** e quindi **App per la logica**.
2.	Immettere un valore in **Nome** (ad esempio "**Db2insertRow**"), **Sottoscrizione**, **Gruppo di risorse**, **Località** e **Piano di servizio app**. Fare clic su **Aggiungi al dashboard** e quindi selezionare **Crea**.

### Definire il trigger e l'azione dell'app per la logica
1.	In **Progettazione app per la logica** fare clic su **LogicApp vuoto** nell'elenco **Modelli**.
2.	Nell'elenco dei **trigger** fare clic su **Ricorrenza**.
3.	Nel trigger **Ricorrenza** fare clic su **Modifica**, fare clic sull'elenco a discesa **Frequenza** per selezionare **Giorno** e quindi fare clic su **Intervallo** per digitare **7**.
4.	Fare clic sulla casella **+ Nuovo passaggio** e quindi su **Aggiungi un'azione**.
5.	Nell'elenco delle **azioni** digitare **db2** nella casella di modifica **Cercare altre azioni** e quindi fare clic su **DB2 - Insert row (Preview)** (DB2 - Inserisci riga - anteprima).
6. Nell'azione **Get rows (Preview)** (Ottieni righe - anteprima) fare clic su **Cambia connessione**.
7. Nel riquadro di configurazione **Connessioni** fare clic per selezionare una connessione. Ad esempio, fare clic su **hisdemo2**.

	![](./media/connectors-create-api-db2/Db2connectorChangeConnection.png)  

8. Nell'elenco **Nome tabella** fare clic sulla **freccia in giù** e quindi su **AREA**.
9. Immettere valori per tutte le colonne obbligatorie (asterisco rosso). Digitare ad esempio "**99999**" per **AREAID**, "**Area 99999**" e quindi "**102**" per **REGIONID**.
10. Fare clic su **Save**.

	![](./media/connectors-create-api-db2/Db2connectorInsertRowValues.png)  
 
11.	Nel pannello **Db2insertRow** fare clic sul primo elemento dell'elenco **Tutte le esecuzioni** (corrispondente all'esecuzione più recente) in **Riepilogo**.
12.	Nel pannello **Esecuzione dell'app per la logica** fare clic su **Dettagli esecuzione**. Nell'elenco **Azione** fare clic su **Get\_rows**. Verificare che il valore di **Stato** sia **Operazione riuscita**. Fare clic su **Collegamento agli input**. Visualizzare gli input. Fare clic su **Collegamento agli output**. Visualizzare gli output, che dovrebbero includere la nuova riga.

	![](./media/connectors-create-api-db2/Db2connectorInsertRowOutputs.png)  

## Definire l'app per la logica per recuperare una riga con SELECT
È possibile definire un'azione dell'app per la logica per recuperare una riga in una tabella DB2, indicando al connettore di elaborare un'istruzione DB2 SELECT WHERE, ad esempio **SELECT FROM AREA WHERE AREAID = '99999'**.

### Definire l'istanza dell'app per la logica
1.	Nella **schermata iniziale di Azure** selezionare **+** (segno più), **Web e dispositivi mobili** e quindi **App per la logica**.
2.	Immettere un valore in **Nome** (ad esempio "**Db2getRow**"), **Sottoscrizione**, **Gruppo di risorse**, **Località** e **Piano di servizio app**. Fare clic su **Aggiungi al dashboard** e quindi selezionare **Crea**.

### Definire il trigger e l'azione dell'app per la logica
1.	In **Progettazione app per la logica** fare clic su **LogicApp vuoto** nell'elenco **Modelli**.
2.	Nell'elenco dei **trigger** fare clic su **Ricorrenza**.
3.	Nel trigger **Ricorrenza** fare clic su **Modifica**, fare clic sull'elenco a discesa **Frequenza** per selezionare **Giorno** e quindi fare clic su **Intervallo** per digitare **7**.
4.	Fare clic sulla casella **+ Nuovo passaggio** e quindi su **Aggiungi un'azione**.
5.	Nell'elenco delle **azioni** digitare **db2** nella casella di modifica **Cercare altre azioni** e quindi fare clic su **DB2 - Get rows (Preview)** (DB2 - Ottieni righe - anteprima).
6. Nell'azione **Get rows (Preview)** (Ottieni righe - anteprima) fare clic su **Cambia connessione**.
7. Nel riquadro di configurazione **Connessioni** fare clic per selezionare una connessione esistente. Ad esempio, fare clic su **hisdemo2**.

	![](./media/connectors-create-api-db2/Db2connectorChangeConnection.png)  

8. Nell'elenco **Nome tabella** fare clic sulla **freccia in giù** e quindi su **AREA**.
9. Immettere valori per tutte le colonne obbligatorie (asterisco rosso). Digitare ad esempio "**99999**" per **AREAID**.
10. Facoltativamente, fare clic su **Mostra le opzioni avanzate** per specificare le opzioni di query.
11. Fare clic su **Save**.

	![](./media/connectors-create-api-db2/Db2connectorGetRowValues.png)  

12.	Nel pannello **Db2getRow** fare clic sul primo elemento dell'elenco **Tutte le esecuzioni** (corrispondente all'esecuzione più recente) in **Riepilogo**.
13.	Nel pannello **Esecuzione dell'app per la logica** fare clic su **Dettagli esecuzione**. Nell'elenco **Azione** fare clic su **Get\_rows**. Verificare che il valore di **Stato** sia **Operazione riuscita**. Fare clic su **Collegamento agli input**. Visualizzare gli input. Fare clic su **Collegamento agli output**. Visualizzare gli output, che dovrebbero includere la riga.

	![](./media/connectors-create-api-db2/Db2connectorGetRowOutputs.png)  

## Definire l'app per la logica per modificare una riga con UPDATE
È possibile definire un'azione dell'app per la logica per modificare una riga in una tabella DB2, indicando al connettore di elaborare un'istruzione DB2 UPDATE, ad esempio **UPDATE AREA SET AREAID = '99999', AREADESC = 'Area 99999', REGIONID = 102)**.

### Definire l'istanza dell'app per la logica
1.	Nella **schermata iniziale di Azure** selezionare **+** (segno più), **Web e dispositivi mobili** e quindi **App per la logica**.
2.	Immettere un valore in **Nome** (ad esempio "**Db2updateRow**"), **Sottoscrizione**, **Gruppo di risorse**, **Località** e **Piano di servizio app**. Fare clic su **Aggiungi al dashboard** e quindi selezionare **Crea**.

### Definire il trigger e l'azione dell'app per la logica
1.	In **Progettazione app per la logica** fare clic su **LogicApp vuoto** nell'elenco **Modelli**.
2.	Nell'elenco dei **trigger** fare clic su **Ricorrenza**.
3.	Nel trigger **Ricorrenza** fare clic su **Modifica**, fare clic sull'elenco a discesa **Frequenza** per selezionare **Giorno** e quindi fare clic su **Intervallo** per digitare **7**.
4.	Fare clic sulla casella **+ Nuovo passaggio** e quindi su **Aggiungi un'azione**.
5.	Nell'elenco delle **azioni** digitare **db2** nella casella di modifica **Cercare altre azioni** e quindi fare clic su **DB2 - Update row (Preview)** (DB2 - Aggiorna riga - anteprima).
6. Nell'azione **Get rows (Preview)** (Ottieni righe - anteprima) fare clic su **Cambia connessione**.
7. Nel riquadro di configurazione **Connessioni** fare clic per selezionare una connessione esistente. Ad esempio, fare clic su **hisdemo2**.

	![](./media/connectors-create-api-db2/Db2connectorChangeConnection.png)  

8. Nell'elenco **Nome tabella** fare clic sulla **freccia in giù** e quindi su **AREA**.
9. Immettere valori per tutte le colonne obbligatorie (asterisco rosso). Digitare ad esempio "**99999**" per **AREAID**, "**Updated 99999**" e quindi "**102**" per **REGIONID**.
10. Fare clic su **Save**.

	![](./media/connectors-create-api-db2/Db2connectorUpdateRowValues.png)  

11.	Nel pannello **Db2updateRow** fare clic sul primo elemento dell'elenco **Tutte le esecuzioni** (corrispondente all'esecuzione più recente) in **Riepilogo**.
12.	Nel pannello **Esecuzione dell'app per la logica** fare clic su **Dettagli esecuzione**. Nell'elenco **Azione** fare clic su **Get\_rows**. Verificare che il valore di **Stato** sia **Operazione riuscita**. Fare clic su **Collegamento agli input**. Visualizzare gli input. Fare clic su **Collegamento agli output**. Visualizzare gli output, che dovrebbero includere la nuova riga.

	![](./media/connectors-create-api-db2/Db2connectorUpdateRowOutputs.png)  

## Definire l'app per la logica per rimuovere una riga con DELETE
È possibile definire un'azione dell'app per la logica per recuperare una riga in una tabella DB2, indicando al connettore di elaborare un'istruzione DB2 DELETE, ad esempio **DELETE FROM AREA WHERE AREAID = '99999'**.

### Definire l'istanza dell'app per la logica
1.	Nella **schermata iniziale di Azure** selezionare **+** (segno più), **Web e dispositivi mobili** e quindi **App per la logica**.
2.	Immettere un valore in **Nome** (ad esempio "**Db2deleteRow**"), **Sottoscrizione**, **Gruppo di risorse**, **Località** e **Piano di servizio app**. Fare clic su **Aggiungi al dashboard** e quindi selezionare **Crea**.

### Definire il trigger e l'azione dell'app per la logica
1.	In **Progettazione app per la logica** fare clic su **LogicApp vuoto** nell'elenco **Modelli**.
2.	Nell'elenco dei **trigger** fare clic su **Ricorrenza**.
3.	Nel trigger **Ricorrenza** fare clic su **Modifica**, fare clic sull'elenco a discesa **Frequenza** per selezionare **Giorno** e quindi fare clic su **Intervallo** per digitare **7**.
4.	Fare clic sulla casella **+ Nuovo passaggio** e quindi su **Aggiungi un'azione**.
5.	Nell'elenco delle **azioni** fare clic su **db2** nella casella di modifica **Cercare altre azioni** e quindi fare clic su **DB2 - Delete row (Preview)** (DB2 - Elimina riga - anteprima).
6. Nell'azione **Get rows (Preview)** (Ottieni righe - anteprima) fare clic su **Cambia connessione**.
7. Nel riquadro di configurazione **Connessioni** fare clic per selezionare una connessione esistente. Ad esempio, fare clic su **hisdemo2**.

	![](./media/connectors-create-api-db2/Db2connectorChangeConnection.png)  

8. Nell'elenco **Nome tabella** fare clic sulla **freccia in giù** e quindi su **AREA**.
9. Immettere valori per tutte le colonne obbligatorie (asterisco rosso). Digitare ad esempio "**99999**" per **AREAID**.
10. Fare clic su **Save**.

	![](./media/connectors-create-api-db2/Db2connectorDeleteRowValues.png)  

11.	Nel pannello **Db2deleteRow** fare clic sul primo elemento dell'elenco **Tutte le esecuzioni** (corrispondente all'esecuzione più recente) in **Riepilogo**.
12.	Nel pannello **Esecuzione dell'app per la logica** fare clic su **Dettagli esecuzione**. Nell'elenco **Azione** fare clic su **Get\_rows**. Verificare che il valore di **Stato** sia **Operazione riuscita**. Fare clic su **Collegamento agli input**. Visualizzare gli input. Fare clic su **Collegamento agli output**. Visualizzare gli output, che dovrebbero includere la riga eliminata.

	![](./media/connectors-create-api-db2/Db2connectorDeleteRowOutputs.png)  

## Dettagli tecnici

## Azioni
Un'azione è un'operazione eseguita dal flusso di lavoro e definita in un'app per la logica. Il connettore di database DB2 include le azioni seguenti.

|Azione|Descrizione|
|--- | ---|
|[GetRow](connectors-create-api-db2.md#get-row)|Recupera una singola riga da una tabella DB2|
|[GetRows](connectors-create-api-db2.md#get-rows)|Recupera righe da una tabella DB2|
|[InsertRow](connectors-create-api-db2.md#insert-row)|Inserisce una nuova riga in una tabella DB2|
|[DeleteRow](connectors-create-api-db2.md#delete-row)|Elimina una riga da una tabella DB2|
|[GetTables](connectors-create-api-db2.md#get-tables)|Recupera le tabelle da un database DB2|
|[UpdateRow](connectors-create-api-db2.md#update-row)|Aggiorna una riga esistente in una tabella DB2|

### Informazioni dettagliate sulle azioni

In questa sezione si vedranno i dettagli relativi a ogni azione, incluse le proprietà di input obbligatorie o facoltative e gli output corrispondenti associati al connettore.

#### Ottenere la riga 
Recupera una singola riga da una tabella DB2.

| Nome proprietà| Nome visualizzato |Descrizione|
| ---|---|---|
|table* | Nome tabella |Nome della tabella DB2|
|id* | ID di riga |Identificatore univoco della riga da recuperare|

L'asterisco (*) indica che la proprietà è obbligatoria.

##### Dettagli dell'output
Item

| Nome proprietà | Tipo di dati |
|---|---|
|ItemInternalId|string|


#### Ottieni righe 
Recupera righe da una tabella DB2.

|Nome proprietà| Nome visualizzato|Descrizione|
| ---|---|---|
|table*|Nome tabella|Nome della tabella DB2|
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
Inserisce una nuova riga in una tabella DB2.

|Nome proprietà| Nome visualizzato|Descrizione|
| ---|---|---|
|table*|Nome tabella|Nome della tabella DB2|
|item*|Riga|Riga da inserire nella tabella specificata in DB2|

L'asterisco (*) indica che la proprietà è obbligatoria.

##### Dettagli dell'output
Item

| Nome proprietà | Tipo di dati |
|---|---|
|ItemInternalId|string|


#### Elimina riga 
Elimina una riga da una tabella DB2.

|Nome proprietà| Nome visualizzato|Descrizione|
| ---|---|---|
|table*|Nome tabella|Nome della tabella DB2|
|id*|ID di riga|Identificatore univoco della riga da eliminare|

L'asterisco (*) indica che la proprietà è obbligatoria.

##### Dettagli output
Nessuna.

#### Ottieni tabelle 
Recupera le tabelle da un database DB2.

Non sono disponibili parametri per questa chiamata.

##### Dettagli dell'output 
TablesList

| Nome proprietà | Tipo di dati |
|---|---|
|value|array|

#### Aggiorna riga 
Aggiorna una riga esistente in una tabella DB2.

|Nome proprietà| Nome visualizzato|Descrizione|
| ---|---|---|
|table*|Nome tabella|Nome della tabella DB2|
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


## Piattaforme e versioni di DB2 supportate
Il connettore supporta le piattaforme e le versioni di IBM DB2 seguenti, nonché i prodotti compatibili con IBM DB2 (ad esempio, IBM Bluemix dashDB) che supportano DRDA (Distributed Relational Database Architecture) SQLAM (SQL Access Manager) versione 10 e 11.

- IBM DB2 per z/OS 11.1
- IBM DB2 per z/OS 10.1
- IBM DB2 per i 7.3
- IBM DB2 per i 7.2
- IBM DB2 per i 7.1
- IBM DB2 per LUW 11
- IBM DB2 per LUW 10.5


## Passaggi successivi

[Creare un'app per la logica](../app-service-logic/app-service-logic-create-a-logic-app.md). Esplorare gli altri connettori disponibili nelle app per la logica nell'[elenco delle API](apis-list.md).

<!---HONumber=AcomDC_0921_2016-->