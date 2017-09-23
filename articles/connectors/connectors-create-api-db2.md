---
title: Aggiungere il connettore DB2 alle app per la logica | Microsoft Docs
description: Panoramica del connettore DB2 con i parametri dell'API REST
services: 
documentationcenter: 
author: gplarsen
manager: erikre
editor: 
tags: connectors
ms.assetid: 1c6b010c-beee-496d-943a-a99e168c99aa
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 09/26/2016
ms.author: plarsen; ladocs
ms.translationtype: Human Translation
ms.sourcegitcommit: c785ad8dbfa427d69501f5f142ef40a2d3530f9e
ms.openlocfilehash: 4501b3d9a2fdc00582596cb907f7130591e4782e
ms.contentlocale: it-it
ms.lasthandoff: 05/26/2017

---
# <a name="get-started-with-the-db2-connector"></a>Introduzione al connettore DB2
Microsoft Connector for DB2 connette le app per la logica alle risorse archiviate in un database IBM DB2. Il connettore include un client Microsoft per comunicare con computer server DB2 remoti su una rete TCP/IP, tra cui: database cloud, ad esempio IBM Bluemix dashDB o IBM DB2 per Windows in esecuzione nella virtualizzazione Azure, e database locali tramite il gateway dati locale. Vedere l'[elenco](connectors-create-api-db2.md#supported-db2-platforms-and-versions) delle piattaforme e delle versioni di IBM DB2 supportate (in questo argomento).

Il connettore DB2 supporta le operazioni di database seguenti:

* Visualizzazione di un elenco delle tabelle di database
* Lettura di una riga con SELECT
* Lettura di tutte le righe con SELECT
* Aggiunta di una riga con INSERT
* Modifica di una riga con UPDATE
* Rimozione di una riga con DELETE

Questo argomento illustra come usare il connettore in un'app per la logica per elaborare operazioni su database.

Per altre informazioni sulle app per la logica, vedere [Creare un'app per la logica](../logic-apps/logic-apps-create-a-logic-app.md).

## <a name="available-actions"></a>Azioni disponibili
Il connettore DB2 supporta le azioni delle app per la logica seguenti:

* GetTables
* GetRow
* GetRows
* InsertRow
* UpdateRow
* DeleteRow

## <a name="list-tables"></a>Visualizzazione di un elenco di tabelle
La creazione di un'app per la logica per qualsiasi operazione prevede numerosi passaggi eseguiti tramite il portale di Microsoft Azure.

All'interno dell'app per la logica è possibile aggiungere un'azione per elencare le tabelle in un database DB2. Questa azione indica al connettore di elaborare un'istruzione schematica DB2, ad esempio `CALL SYSIBM.SQLTABLES`.

### <a name="create-a-logic-app"></a>Creare un'app per la logica
1. Nella **schermata iniziale di Azure** selezionare **+** (segno più), **Web e dispositivi mobili** e quindi **App per la logica**.
2. Compilare i campi **Nome**, ad esempio `Db2getTables`, **Sottoscrizione**, **Gruppo di risorse**, **Località** e **Piano di servizio app**. Selezionare **Aggiungi al dashboard** e quindi selezionare **Crea**.

### <a name="add-a-trigger-and-action"></a>Aggiungere un trigger e un'azione
1. In **Progettazione app per la logica**, selezionare **LogicApp vuoto** nell'elenco **Modelli**.
2. Nell'elenco dei **trigger** fare clic su **Ricorrenza**. 
3. Nel trigger **Ricorrenza** fare clic su **Modifica**, poi sull'elenco a discesa **Frequenza** per selezionare **Giorno** e infine fare clic su **Intervallo** per digitare **7**.  
4. Fare clic sulla casella **+ Nuovo passaggio** e su **Aggiungi un'azione**.
5. Nell'elenco delle **azioni** digitare `db2` nella casella di modifica **Cercare altre azioni** e fare clic su **DB2 - Ottieni tabelle - anteprima**.
   
   ![](./media/connectors-create-api-db2/Db2connectorActions.png)  
6. Nel riquadro di configurazione **DB2 - Ottieni tabelle** fare clic sulla **casella di controllo** per abilitare **Connetti tramite gateway dati locale**. Notare che le impostazioni verranno modificate da cloud a locale.
   
   * Digitare un valore in **Server** sotto forma di indirizzo o alias-due punti-numero di porta. Ad esempio digitare `ibmserver01:50000`.
   * Digitare un valore in **Database**. Ad esempio digitare `nwind`.
   * Selezionare un valore in **Autenticazione**, ad esempio **Basic**.
   * Digitare un valore in **Nome utente**. Ad esempio digitare `db2admin`.
   * Digitare un valore in **Password**. Ad esempio digitare `Password1`.
   * Selezionare un valore in **Gateway**, ad esempio, selezionare **datagateway01**.
7. Selezionare **Crea** e quindi **Salva**. 
   
    ![](./media/connectors-create-api-db2/Db2connectorOnPremisesDataGatewayConnection.png)
8. Nel pannello **Db2getTables** fare clic sul primo elemento dell'elenco **Tutte le esecuzioni** (corrispondente all'esecuzione più recente) in **Riepilogo**.
9. Nel pannello **Esecuzione dell'app per la logica** fare clic su **Dettagli esecuzione**. Nell'elenco **Azione** fare clic su **Get_tables**. Verificare che il valore di **Stato** sia **Operazione riuscita**. Selezionare il **collegamento Input** per visualizzare gli input. Selezionare il **collegamento Output**per visualizzare gli output, che devono includere un elenco di tabelle.
   
   ![](./media/connectors-create-api-db2/Db2connectorGetTablesLogicAppRunOutputs.png)

## <a name="create-the-connections"></a>Creare le connessioni
Il connettore supporta le connessioni ai database ospitati locali e cloud mediante le proprietà di connessione seguenti. 

| Proprietà | Descrizione |
| --- | --- |
| server |Obbligatorio. Accetta un valore stringa che rappresenta un alias o un indirizzo TCP/IP, in formato IPv4 o IPv6, delimitato da due punti e seguito da un numero di porta TCP/IP. |
| database |Obbligatorio. Accetta un valore stringa che rappresenta un nome di database relazionale (RDBNAM) DRDA. DB2 per z/OS accetta una stringa di 16 byte (il database è definito come posizione di IBM DB2 per z/OS). DB2 per i5/OS accetta una stringa di 18 byte (il database è definito come database relazionale IBM DB2 per i). DB2 per LUW accetta una stringa di 8 byte. |
| authentication |facoltativo. Accetta un valore elemento di elenco, ovvero Basic o Windows (kerberos). |
| username |Obbligatorio. Accetta il valore della stringa. DB2 per z/OS accetta una stringa di 8 byte. DB2 per i accetta una stringa di 10 byte. DB2 per Linux o UNIX accetta una stringa di 8 byte. DB2 per Windows accetta una stringa di 30 byte. |
| password |Obbligatorio. Accetta il valore della stringa. |
| gateway |Obbligatorio. Accetta un valore elemento di elenco, che rappresenta il gateway dati locale definito per le app per la logica nel gruppo di archiviazione. |

## <a name="create-the-on-premises-gateway-connection"></a>Creare una connessione gateway locale
Il connettore può accedere a un database DB2 locale tramite il gateway locale. Per altre informazioni, vedere gli argomenti relativi al gateway. 

1. Nel riquadro di configurazione **Gateway** fare clic sulla **casella di controllo** per abilitare la **Connessione tramite gateway**. Notare che le impostazioni verranno modificate da cloud a locale.
2. Digitare un valore in **Server** sotto forma di indirizzo o alias-due punti-numero di porta. Ad esempio digitare `ibmserver01:50000`.
3. Digitare un valore in **Database**. Ad esempio digitare `nwind`.
4. Selezionare un valore in **Autenticazione**, ad esempio **Basic**.
5. Digitare un valore in **Nome utente**. Ad esempio digitare `db2admin`.
6. Digitare un valore in **Password**. Ad esempio digitare `Password1`.
7. Selezionare un valore in **Gateway**, ad esempio, selezionare **datagateway01**.
8. Fare clic su **Crea** per continuare. 
   
    ![](./media/connectors-create-api-db2/Db2connectorOnPremisesDataGatewayConnection.png)

## <a name="create-the-cloud-connection"></a>Creare la connessione cloud
Il connettore può accedere a un database DB2 cloud. 

1. Nel riquadro di configurazione **Gateway** lasciare **disabilitata (deselezionata)** la casella di controllo **Connessione tramite gateway**. 
2. Digitare un valore in **Nome connessione**. Ad esempio digitare `hisdemo2`.
3. Digitare un valore per il **nome del server DB2** sotto forma di indirizzo o alias-due punti-numero di porta. Ad esempio digitare `hisdemo2.cloudapp.net:50000`.
4. Digitare un valore per il nome del database DB2 in **Database**. Ad esempio digitare `nwind`.
5. Digitare un valore in **Nome utente**. Ad esempio digitare `db2admin`.
6. Digitare un valore in **Password**. Ad esempio digitare `Password1`.
7. Fare clic su **Crea** per continuare. 
   
    ![](./media/connectors-create-api-db2/Db2connectorCloudConnection.png)

## <a name="fetch-all-rows-using-select"></a>Recuperare tutte le righe con SELECT
È possibile definire un'azione di app per la logica al fine di recuperare tutte le righe della tabella DB2. Questa azione indica al connettore di elaborare un'istruzione DB2 SELECT, ad esempio `SELECT * FROM AREA`.

### <a name="create-a-logic-app"></a>Creare un'app per la logica
1. Nella **schermata iniziale di Azure** selezionare **+** (segno più), **Web e dispositivi mobili** e quindi **App per la logica**.
2. Compilare i campi **Nome**, ad esempio `Db2getRows`, **Sottoscrizione**, **Gruppo di risorse**, **Località** e **Piano di servizio app**. Selezionare **Aggiungi al dashboard** e quindi selezionare **Crea**.

### <a name="add-a-trigger-and-action"></a>Aggiungere un trigger e un'azione
1. In **Progettazione app per la logica**, selezionare **LogicApp vuoto** nell'elenco **Modelli**.
2. Nell'elenco dei **trigger** fare clic su **Ricorrenza**. 
3. Nel trigger **Ricorrenza**, fare clic su **Modifica**, poi sull'elenco a discesa **Frequenza** per selezionare **Giorno** e infine fare clic su **Intervallo** per digitare **7**. 
4. Fare clic sulla casella **+ Nuovo passaggio** e su **Aggiungi un'azione**.
5. Nell'elenco delle **azioni** digitare `db2` nella casella di modifica **Cercare altre azioni** e fare clic su **DB2 - Ottieni righe - anteprima**.
6. Nell'azione **Ottieni righe - anteprima** fare clic su **Cambia connessione**.
7. Nel riquadro di configurazione **Connessioni** fare clic su **Crea nuova**. 
   
    ![](./media/connectors-create-api-db2/Db2connectorNewConnection.png)
8. Nel riquadro di configurazione **Gateway** lasciare **disabilitata (deselezionata)** la casella di controllo **Connessione tramite gateway**.
   
   * Digitare un valore in **Nome connessione**. Ad esempio digitare `HISDEMO2`.
   * Digitare un valore per il **nome del server DB2** sotto forma di indirizzo o alias-due punti-numero di porta. Ad esempio digitare `HISDEMO2.cloudapp.net:50000`.
   * Digitare un valore per il nome del database DB2 in **Database**. Ad esempio digitare `NWIND`.
   * Digitare un valore in **Nome utente**. Ad esempio digitare `db2admin`.
   * Digitare un valore in **Password**. Ad esempio digitare `Password1`.
9. Fare clic su **Crea** per continuare.
   
    ![](./media/connectors-create-api-db2/Db2connectorCloudConnection.png)
10. Nell'elenco **Nome tabella** fare clic sulla **freccia in giù** e selezionare **AREA**.
11. Se lo si desidera, fare clic su **Mostra le opzioni avanzate** per specificare le opzioni di query.
12. Selezionare **Salva**. 
    
    ![](./media/connectors-create-api-db2/Db2connectorGetRowsTableName.png)
13. Nel pannello **Db2getRows** fare clic sul primo elemento dell'elenco **Tutte le esecuzioni** (corrispondente all'esecuzione più recente) in **Riepilogo**.
14. Nel pannello **Esecuzione dell'app per la logica** fare clic su **Dettagli esecuzione**. Nell'elenco **Azione** fare clic su **Get_rows**. Verificare che il valore di **Stato** sia **Operazione riuscita**. Selezionare il **collegamento Input** per visualizzare gli input. Selezionare il **collegamento Output**per visualizzare gli output, che devono includere un elenco di righe.
    
    ![](./media/connectors-create-api-db2/Db2connectorGetRowsOutputs.png)

## <a name="add-one-row-using-insert"></a>Aggiunta di una riga con INSERT
È possibile definire un'azione di app per la logica al fine di aggiungere una riga nella tabella DB2. Questa azione indica al connettore di elaborare un'istruzione DB2 INSERT, ad esempio `INSERT INTO AREA (AREAID, AREADESC, REGIONID) VALUES ('99999', 'Area 99999', 102)`.

### <a name="create-a-logic-app"></a>Creare un'app per la logica
1. Nella **schermata iniziale di Azure** selezionare **+** (segno più), **Web e dispositivi mobili** e quindi **App per la logica**.
2. Compilare i campi **Nome**, ad esempio `Db2insertRow`, **Sottoscrizione**, **Gruppo di risorse**, **Località** e **Piano di servizio app**. Selezionare **Aggiungi al dashboard** e quindi selezionare **Crea**.

### <a name="add-a-trigger-and-action"></a>Aggiungere un trigger e un'azione
1. In **Progettazione app per la logica**, selezionare **LogicApp vuoto** nell'elenco **Modelli**.
2. Nell'elenco dei **trigger** fare clic su **Ricorrenza**. 
3. Nel trigger **Ricorrenza**, fare clic su **Modifica**, poi sull'elenco a discesa **Frequenza** per selezionare **Giorno** e infine fare clic su **Intervallo** per digitare **7**. 
4. Fare clic sulla casella **+ Nuovo passaggio** e su **Aggiungi un'azione**.
5. Nell'elenco delle **azioni** digitare **db2** nella casella di modifica **Cercare altre azioni** e quindi fare clic su **DB2 - Inserisci riga - anteprima**.
6. Nell'azione **DB2 - Ottieni righe (anteprima)** fare clic su **Cambia connessione**. 
7. Nel riquadro di configurazione **Connessioni**selezionare una connessione, ad esempio, selezionare **hisdemo2**.
   
    ![](./media/connectors-create-api-db2/Db2connectorChangeConnection.png)
8. Nell'elenco **Nome tabella** fare clic sulla **freccia in giù** e selezionare **AREA**.
9. Immettere valori per tutte le colonne obbligatorie (asterisco rosso). Ad esempio digitare `99999` per **AREAID** e `Area 99999``102` per **REGIONID**. 
10. Selezionare **Salva**.
    
    ![](./media/connectors-create-api-db2/Db2connectorInsertRowValues.png)
11. Nel pannello **Db2insertRow** fare clic sul primo elemento dell'elenco **Tutte le esecuzioni** (corrispondente all'esecuzione più recente) in **Riepilogo**.
12. Nel pannello **Esecuzione dell'app per la logica** fare clic su **Dettagli esecuzione**. Nell'elenco **Azione** fare clic su **Get_rows**. Verificare che il valore di **Stato** sia **Operazione riuscita**. Selezionare il **collegamento Input** per visualizzare gli input. Selezionare il **collegamento Output**e visualizzare gli output, che devono includere una riga nuova.
    
    ![](./media/connectors-create-api-db2/Db2connectorInsertRowOutputs.png)

## <a name="fetch-one-row-using-select"></a>Recuperare una riga con SELECT
È possibile definire un'azione di app per la logica al fine di recuperare una riga nella tabella DB2. Questa azione indica al connettore di elaborare un'istruzione DB2 SELECT WHERE, ad esempio `SELECT FROM AREA WHERE AREAID = '99999'`.

### <a name="create-a-logic-app"></a>Creare un'app per la logica
1. Nella **schermata iniziale di Azure** selezionare **+** (segno più), **Web e dispositivi mobili** e quindi **App per la logica**.
2. Immettere un valore in **Nome** (ad esempio "**Db2getRow**"), **Sottoscrizione**, **Gruppo di risorse**, **Località**, **Piano di servizio app**. Selezionare **Aggiungi al dashboard** e quindi selezionare **Crea**.

### <a name="add-a-trigger-and-action"></a>Aggiungere un trigger e un'azione
1. In **Progettazione app per la logica**, selezionare **LogicApp vuoto** nell'elenco **Modelli**. 
2. Nell'elenco dei **trigger** fare clic su **Ricorrenza**. 
3. Nel trigger **Ricorrenza**, fare clic su **Modifica**, poi sull'elenco a discesa **Frequenza** per selezionare **Giorno** e infine fare clic su **Intervallo** per digitare **7**. 
4. Fare clic sulla casella **+ Nuovo passaggio** e su **Aggiungi un'azione**.
5. Nell'elenco delle **azioni** digitare **db2** nella casella di modifica **Cercare altre azioni** e fare clic **DB2 - Ottieni righe - anteprima**.
6. Nell'azione **Ottieni righe - anteprima** fare clic su **Cambia connessione**. 
7. Nel riquadro di configurazione **Connessioni** selezionare una connessione esistente. ad esempio, selezionare **hisdemo2**.
   
    ![](./media/connectors-create-api-db2/Db2connectorChangeConnection.png)
8. Nell'elenco **Nome tabella** fare clic sulla **freccia in giù** e selezionare **AREA**.
9. Immettere valori per tutte le colonne obbligatorie (asterisco rosso). Ad esempio digitare `99999` per **AREAID**. 
10. Se lo si desidera, fare clic su **Mostra le opzioni avanzate** per specificare le opzioni di query.
11. Selezionare **Salva**. 
    
    ![](./media/connectors-create-api-db2/Db2connectorGetRowValues.png)
12. Nel pannello **Db2getRow** fare clic sul primo elemento dell'elenco **Tutte le esecuzioni** (corrispondente all'esecuzione più recente) in **Riepilogo**.
13. Nel pannello **Esecuzione dell'app per la logica** fare clic su **Dettagli esecuzione**. Nell'elenco **Azione** fare clic su **Get_rows**. Verificare che il valore di **Stato** sia **Operazione riuscita**. Selezionare il **collegamento Input** per visualizzare gli input. Selezionare il **collegamento Output**e visualizzare gli output, che devono includere una riga.
    
    ![](./media/connectors-create-api-db2/Db2connectorGetRowOutputs.png)

## <a name="change-one-row-using-update"></a>Modificare una riga con UPDATE
È possibile definire un'azione di app per la logica al fine di cambiare una riga nella tabella DB2. Questa azione indica al connettore di elaborare un'istruzione DB2 UPDATE, ad esempio `UPDATE AREA SET AREAID = '99999', AREADESC = 'Area 99999', REGIONID = 102)`.

### <a name="create-a-logic-app"></a>Creare un'app per la logica
1. Nella **schermata iniziale di Azure** selezionare **+** (segno più), **Web e dispositivi mobili** e quindi **App per la logica**.
2. Compilare i campi **Nome**, ad esempio `Db2updateRow`, **Sottoscrizione**, **Gruppo di risorse**, **Località** e **Piano di servizio app**. Selezionare **Aggiungi al dashboard** e quindi selezionare **Crea**.

### <a name="add-a-trigger-and-action"></a>Aggiungere un trigger e un'azione
1. In **Progettazione app per la logica**, selezionare **LogicApp vuoto** nell'elenco **Modelli**.
2. Nell'elenco dei **trigger** fare clic su **Ricorrenza**. 
3. Nel trigger **Ricorrenza**, fare clic su **Modifica**, poi sull'elenco a discesa **Frequenza** per selezionare **Giorno** e infine fare clic su **Intervallo** per digitare **7**. 
4. Fare clic sulla casella **+ Nuovo passaggio** e su **Aggiungi un'azione**.
5. Nell'elenco delle **azioni** digitare **db2** nella casella di modifica **Cercare altre azioni** e quindi fare clic su **DB2 - Aggiorna riga - anteprima**.
6. Nell'azione **DB2 - Aggiorna righe (anteprima)** fare clic su **Cambia connessione**. 
7. Nel riquadro di configurazione **Connessioni** fare clic per selezionare una connessione esistente. ad esempio, selezionare **hisdemo2**.
   
    ![](./media/connectors-create-api-db2/Db2connectorChangeConnection.png)
8. Nell'elenco **Nome tabella** fare clic sulla **freccia in giù** e selezionare **AREA**.
9. Immettere valori per tutte le colonne obbligatorie (asterisco rosso). Ad esempio digitare `99999` per **AREAID** e `Updated 99999``102` per **REGIONID**. 
10. Selezionare **Salva**. 
    
    ![](./media/connectors-create-api-db2/Db2connectorUpdateRowValues.png)
11. Nel pannello **Db2updateRow** fare clic sul primo elemento dell'elenco **Tutte le esecuzioni** (corrispondente all'esecuzione più recente) in **Riepilogo**.
12. Nel pannello **Esecuzione dell'app per la logica** fare clic su **Dettagli esecuzione**. Nell'elenco **Azione** fare clic su **Get_rows**. Verificare che il valore di **Stato** sia **Operazione riuscita**. Selezionare il **collegamento Input** per visualizzare gli input. Selezionare il **collegamento Output**e visualizzare gli output, che devono includere una riga nuova.
    
    ![](./media/connectors-create-api-db2/Db2connectorUpdateRowOutputs.png)

## <a name="remove-one-row-using-delete"></a>Rimozione di una riga con DELETE
È possibile definire un'azione di app per la logica al fine di rimuovere una riga nella tabella DB2. Questa azione indica al connettore di elaborare un'istruzione DB2 DELETE, ad esempio `DELETE FROM AREA WHERE AREAID = '99999'`.

### <a name="create-a-logic-app"></a>Creare un'app per la logica
1. Nella **schermata iniziale di Azure** selezionare **+** (segno più), **Web e dispositivi mobili** e quindi **App per la logica**.
2. Compilare i campi **Nome**, ad esempio `Db2deleteRow`, **Sottoscrizione**, **Gruppo di risorse**, **Località** e **Piano di servizio app**. Selezionare **Aggiungi al dashboard** e quindi selezionare **Crea**.

### <a name="add-a-trigger-and-action"></a>Aggiungere un trigger e un'azione
1. In **Progettazione app per la logica**, selezionare **LogicApp vuoto** nell'elenco **Modelli**. 
2. Nell'elenco dei **trigger** fare clic su **Ricorrenza**. 
3. Nel trigger **Ricorrenza**, fare clic su **Modifica**, poi sull'elenco a discesa **Frequenza** per selezionare **Giorno** e infine fare clic su **Intervallo** per digitare **7**. 
4. Fare clic sulla casella **+ Nuovo passaggio** e su **Aggiungi un'azione**.
5. Nell'elenco delle **azioni** selezionare **db2** nella casella di modifica **Cercare altre azioni** e quindi fare clic su **DB2 - Elimina riga - anteprima**.
6. Nell'azione **DB2 - Elimina righe (anteprima)** fare clic su **Cambia connessione**. 
7. Nel riquadro di configurazione **Connessioni** selezionare una connessione esistente. ad esempio, selezionare **hisdemo2**.
   
    ![](./media/connectors-create-api-db2/Db2connectorChangeConnection.png)
8. Nell'elenco **Nome tabella** fare clic sulla **freccia in giù** e selezionare **AREA**.
9. Immettere valori per tutte le colonne obbligatorie (asterisco rosso). Ad esempio digitare `99999` per **AREAID**. 
10. Selezionare **Salva**. 
    
    ![](./media/connectors-create-api-db2/Db2connectorDeleteRowValues.png)
11. Nel pannello **Db2deleteRow** fare clic sul primo elemento dell'elenco **Tutte le esecuzioni** (corrispondente all'esecuzione più recente) in **Riepilogo**.
12. Nel pannello **Esecuzione dell'app per la logica** fare clic su **Dettagli esecuzione**. Nell'elenco **Azione** fare clic su **Get_rows**. Verificare che il valore di **Stato** sia **Operazione riuscita**. Selezionare il **collegamento Input** per visualizzare gli input. Selezionare il **collegamento Output**e visualizzare gli output, che devono includere la riga eliminata.
    
    ![](./media/connectors-create-api-db2/Db2connectorDeleteRowOutputs.png)

## <a name="supported-db2-platforms-and-versions"></a>Piattaforme e versioni di DB2 supportate
Il connettore supporta le piattaforme e le versioni di IBM DB2 seguenti, nonché i prodotti compatibili con IBM DB2 (ad esempio, IBM Bluemix dashDB) che supportano DRDA (Distributed Relational Database Architecture) SQLAM (SQL Access Manager) versione 10 e 11:

* IBM DB2 per z/OS 11.1
* IBM DB2 per z/OS 10.1
* IBM DB2 per i 7.3
* IBM DB2 per i 7.2
* IBM DB2 per i 7.1
* IBM DB2 per LUW 11
* IBM DB2 per LUW 10.5

## <a name="connector-specific-details"></a>Dettagli specifici del connettore

Per visualizzare eventuali azioni e trigger definiti in Swagger ed eventuali limiti, vedere i [dettagli del connettore](/connectors/db2/). 

## <a name="next-steps"></a>Passaggi successivi
[Creare un'app per la logica](../logic-apps/logic-apps-create-a-logic-app.md). Esplorare gli altri connettori disponibili nelle app per la logica nell' [elenco delle API](apis-list.md).


