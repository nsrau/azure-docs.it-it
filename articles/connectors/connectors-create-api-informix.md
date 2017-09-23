---
title: Aggiungere il connettore Informix alle app per la logica | Documentazione Microsoft
description: Panoramica del connettore Informix con i parametri dell'API REST
services: 
documentationcenter: 
author: gplarsen
manager: anneta
editor: 
tags: connectors
ms.assetid: ca2393f0-3073-4dc2-8438-747f5bc59689
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 09/26/2016
ms.author: plarsen; ladocs
ms.translationtype: Human Translation
ms.sourcegitcommit: c785ad8dbfa427d69501f5f142ef40a2d3530f9e
ms.openlocfilehash: b2e755b5b1b4939eac90ac55ba8398c5687124c8
ms.contentlocale: it-it
ms.lasthandoff: 05/26/2017

---
# <a name="get-started-with-the-informix-connector"></a>Introduzione al connettore Informix
Microsoft Connector for Informix connette le app per la logica alle risorse archiviate in un database IBM Informix. Il connettore Informix include un client Microsoft per comunicare con computer server Informix remoti su una rete TCP/IP, tra cui: database cloud, ad esempio IBM Informix per Windows in esecuzione nella virtualizzazione Azure, e database locali tramite il gateway dati locale. Vedere l'[elenco](connectors-create-api-informix.md#supported-informix-platforms-and-versions) delle piattaforme e delle versioni di IBM Informix supportate (in questo argomento).

Il connettore supporta le operazioni di database seguenti:

* Visualizzazione di un elenco delle tabelle di database
* Lettura di una riga con SELECT
* Lettura di tutte le righe con SELECT
* Aggiunta di una riga con INSERT
* Modifica di una riga con UPDATE
* Rimozione di una riga con DELETE

Questo argomento illustra come usare il connettore in un'app per la logica per elaborare operazioni su database.

Per altre informazioni sulle app per la logica, vedere [Creare un'app per la logica](../logic-apps/logic-apps-create-a-logic-app.md).

## <a name="available-actions"></a>Azioni disponibili
Il connettore supporta le azioni delle app per la logica seguenti:

* GetTables
* GetRow
* GetRows
* InsertRow
* UpdateRow
* DeleteRow

## <a name="list-tables"></a>Visualizzazione di un elenco di tabelle
La creazione di un'app per la logica per qualsiasi operazione prevede numerosi passaggi eseguiti tramite il portale di Microsoft Azure.

All'interno dell'app per la logica è possibile aggiungere un'azione per elencare le tabelle in un database di Informix. Questa azione indica al connettore di elaborare un'istruzione schematica di Informix, ad esempio `CALL SYSIBM.SQLTABLES`.

### <a name="create-a-logic-app"></a>Creare un'app per la logica
1. Nella **schermata iniziale di Azure** selezionare **+** (segno più), **Web e dispositivi mobili** e quindi **App per la logica**.
2. Compilare i campi **Nome**, ad esempio `InformixgetTables`, **Sottoscrizione**, **Gruppo di risorse**, **Località** e **Piano di servizio app**. Selezionare **Aggiungi al dashboard** e quindi selezionare **Crea**.

### <a name="add-a-trigger-and-action"></a>Aggiungere un trigger e un'azione
1. In **Progettazione app per la logica**, selezionare **LogicApp vuoto** nell'elenco **Modelli**.
2. Nell'elenco dei **trigger** fare clic su **Ricorrenza**. 
3. Nel trigger **Ricorrenza**, fare clic su **Modifica**, poi sull'elenco a discesa **Frequenza** per selezionare **Giorno** e infine fare clic su **Intervallo** per digitare **7**.  
4. Fare clic sulla casella **+ Nuovo passaggio** e su **Aggiungi un'azione**.
5. Nell'elenco delle **azioni** digitare **informix** nella casella di modifica **Cercare altre azioni** e quindi fare clic su **Informix - Ottieni tabelle - anteprima**.
   
   ![](./media/connectors-create-api-informix/InformixconnectorActions.png)  
6. Nel riquadro di configurazione **Informix - Ottieni tabelle** fare clic sulla **casella di controllo** per abilitare **Connetti tramite gateway dati locale**. Notare che le impostazioni verranno modificate da cloud a locale.
   
   * Digitare un valore in **Server** sotto forma di indirizzo o alias-due punti-numero di porta. Ad esempio digitare `ibmserver01:9089`.
   * Digitare un valore in **Database**. Ad esempio digitare `nwind`.
   * Selezionare un valore in **Autenticazione**, ad esempio **Basic**.
   * Digitare un valore in **Nome utente**. Ad esempio digitare `informix`.
   * Digitare un valore in **Password**. Ad esempio digitare `Password1`.
   * Selezionare un valore in **Gateway**, ad esempio, selezionare **datagateway01**.
7. Selezionare **Crea** e quindi **Salva**. 
   
    ![](./media/connectors-create-api-informix/InformixconnectorOnPremisesDataGatewayConnection.png)
8. Nel pannello **InformixgetTables** fare clic sul primo elemento dell'elenco **Tutte le esecuzioni** (corrispondente all'esecuzione più recente) in **Riepilogo**.
9. Nel pannello **Esecuzione dell'app per la logica** fare clic su **Dettagli esecuzione**. Nell'elenco **Azione** fare clic su **Get_tables**. Verificare che il valore di **Stato** sia **Operazione riuscita**. Selezionare il **collegamento Input** per visualizzare gli input. Selezionare il **collegamento Output**per visualizzare gli output, che devono includere un elenco di tabelle.
   
   ![](./media/connectors-create-api-informix/InformixconnectorGetTablesLogicAppRunOutputs.png)

## <a name="create-the-connections"></a>Creare le connessioni
Il connettore supporta le connessioni al database locale e cloud mediante le proprietà di connessione seguenti. 

| Proprietà | Descrizione |
| --- | --- |
| server |Obbligatorio. Accetta un valore stringa che rappresenta un alias o un indirizzo TCP/IP, in formato IPv4 o IPv6, delimitato da due punti e seguito da un numero di porta TCP/IP. |
| database |Obbligatorio. Accetta un valore stringa che rappresenta un nome di database relazionale (RDBNAM) DRDA. Informix accetta una stringa di 128 byte (il database è definito come nome di database IBM Informix, ovvero come dbname). |
| authentication |facoltativo. Accetta un valore elemento di elenco, ovvero Basic o Windows (kerberos). |
| username |Obbligatorio. Accetta il valore della stringa. |
| password |Obbligatorio. Accetta il valore della stringa. |
| gateway |Obbligatorio. Accetta un valore elemento di elenco, che rappresenta il gateway dati locale definito per le app per la logica nel gruppo di archiviazione. |

## <a name="create-the-on-premises-gateway-connection"></a>Creare una connessione gateway locale
Il connettore può accedere a un database Informix locale tramite il gateway dati locale. Per altre informazioni, vedere gli argomenti relativi al gateway. 

1. Nel riquadro di configurazione **Gateway** fare clic sulla **casella di controllo** per abilitare la **Connessione tramite gateway**. Le impostazioni verranno modificate da cloud a locale.
2. Digitare un valore in **Server** sotto forma di indirizzo o alias-due punti-numero di porta. Ad esempio digitare `ibmserver01:9089`.
3. Digitare un valore in **Database**. Ad esempio digitare `nwind`.
4. Selezionare un valore in **Autenticazione**, ad esempio **Basic**.
5. Digitare un valore in **Nome utente**. Ad esempio digitare `informix`.
6. Digitare un valore in **Password**. Ad esempio digitare `Password1`.
7. Selezionare un valore in **Gateway**, ad esempio, selezionare **datagateway01**.
8. Fare clic su **Crea** per continuare. 
   
    ![](./media/connectors-create-api-informix/InformixconnectorOnPremisesDataGatewayConnection.png)

## <a name="create-the-cloud-connection"></a>Creare la connessione cloud
Il connettore può accedere a un database Informix cloud. 

1. Nel riquadro di configurazione **Gateway** lasciare **disabilitata (deselezionata)** la casella di controllo **Connessione tramite gateway**. 
2. Digitare un valore in **Nome connessione**. Ad esempio digitare `hisdemo2`.
3. Digitare un valore per il **nome del server Informix**, sotto forma di indirizzo o alias-due punti-numero di porta. Ad esempio digitare `hisdemo2.cloudapp.net:9089`.
4. Digitare un valore per il nome del database Informix in **Database**. Ad esempio digitare `nwind`.
5. Digitare un valore in **Nome utente**. Ad esempio digitare `informix`.
6. Digitare un valore in **Password**. Ad esempio digitare `Password1`.
7. Fare clic su **Crea** per continuare. 
   
    ![](./media/connectors-create-api-informix/InformixconnectorCloudConnection.png)

## <a name="fetch-all-rows-using-select"></a>Recuperare tutte le righe con SELECT
È possibile creare un'azione di app per la logica al fine di recuperare tutte le righe della tabella di Informix. Questa azione indica al connettore di elaborare un'istruzione SELECT di Informix, ad esempio `SELECT * FROM AREA`.

### <a name="create-a-logic-app"></a>Creare un'app per la logica
1. Nella **schermata iniziale di Azure** selezionare **+** (segno più), **Web e dispositivi mobili** e quindi **App per la logica**.
2. Immettere un valore in **Nome** (ad esempio "**InformixgetRows**"), **Sottoscrizione**, **Gruppo di risorse**, **Località** e **Piano di servizio app**. Selezionare **Aggiungi al dashboard** e quindi selezionare **Crea**.

### <a name="add-a-trigger-and-action"></a>Aggiungere un trigger e un'azione
1. In **Progettazione app per la logica**, selezionare **LogicApp vuoto** nell'elenco **Modelli**.
2. Nell'elenco dei **trigger** fare clic su **Ricorrenza**. 
3. Nel trigger **Ricorrenza**, fare clic su **Modifica**, poi sull'elenco a discesa **Frequenza** per selezionare **Giorno** e infine fare clic su **Intervallo** per digitare **7**. 
4. Fare clic sulla casella **+ Nuovo passaggio** e su **Aggiungi un'azione**.
5. Nell'elenco delle **azioni** digitare **informix** nella casella di modifica **Cercare altre azioni** e fare clic su **Informix - Ottieni righe - anteprima**.
6. Nell'azione **Ottieni righe - anteprima** fare clic su **Cambia connessione**.
7. Nel riquadro di configurazione **Connessioni** fare clic su **Crea nuova**. 
   
    ![](./media/connectors-create-api-informix/InformixconnectorNewConnection.png)
8. Nel riquadro di configurazione **Gateway** lasciare **disabilitata (deselezionata)** la casella di controllo **Connessione tramite gateway**.
   
   * Digitare un valore in **Nome connessione**. Ad esempio digitare `HISDEMO2`.
   * Digitare un valore per il **nome del server Informix**, sotto forma di indirizzo o alias-due punti-numero di porta. Ad esempio digitare `HISDEMO2.cloudapp.net:9089`.
   * Digitare un valore per il nome del database Informix in **Database**. Ad esempio digitare `NWIND`.
   * Digitare un valore in **Nome utente**. Ad esempio digitare `informix`.
   * Digitare un valore in **Password**. Ad esempio digitare `Password1`.
9. Fare clic su **Crea** per continuare.
   
    ![](./media/connectors-create-api-informix/InformixconnectorCloudConnection.png)
10. Nell'elenco **Nome tabella** fare clic sulla **freccia in giù** e selezionare **AREA**.
11. Se lo si desidera, fare clic su **Mostra le opzioni avanzate** per specificare le opzioni di query.
12. Selezionare **Salva**. 
    
    ![](./media/connectors-create-api-informix/InformixconnectorGetRowsTableName.png)
13. Nel pannello **InformixgetRows** fare clic sul primo elemento dell'elenco **Tutte le esecuzioni** (corrispondente all'esecuzione più recente) in **Riepilogo**.
14. Nel pannello **Esecuzione dell'app per la logica** fare clic su **Dettagli esecuzione**. Nell'elenco **Azione** fare clic su **Get_rows**. Verificare che il valore di **Stato** sia **Operazione riuscita**. Selezionare il **collegamento Input** per visualizzare gli input. Selezionare il **collegamento Output**per visualizzare gli output, che devono includere un elenco di righe.
    
    ![](./media/connectors-create-api-informix/InformixconnectorGetRowsOutputs.png)

## <a name="add-one-row-using-insert"></a>Aggiunta di una riga con INSERT
È possibile creare un'azione di app per la logica per aggiungere una riga nella tabella di Informix. Questa azione indica al connettore di elaborare un'istruzione INSERT di Informix, ad esempio `INSERT INTO AREA (AREAID, AREADESC, REGIONID) VALUES ('99999', 'Area 99999', 102)`.

### <a name="create-a-logic-app"></a>Creare un'app per la logica
1. Nella **schermata iniziale di Azure** selezionare **+** (segno più), **Web e dispositivi mobili** e quindi **App per la logica**.
2. Compilare i campi **Nome**, ad esempio `InformixinsertRow`, **Sottoscrizione**, **Gruppo di risorse**, **Località** e **Piano di servizio app**. Selezionare **Aggiungi al dashboard** e quindi selezionare **Crea**.

### <a name="add-a-trigger-and-action"></a>Aggiungere un trigger e un'azione
1. In **Progettazione app per la logica**, selezionare **LogicApp vuoto** nell'elenco **Modelli**.
2. Nell'elenco dei **trigger** fare clic su **Ricorrenza**. 
3. Nel trigger **Ricorrenza**, fare clic su **Modifica**, poi sull'elenco a discesa **Frequenza** per selezionare **Giorno** e infine fare clic su **Intervallo** per digitare **7**. 
4. Fare clic sulla casella **+ Nuovo passaggio** e su **Aggiungi un'azione**.
5. Nell'elenco delle **azioni** digitare **informix** nella casella di modifica **Cercare altre azioni** e fare clic su **Informix - Inserisci riga - anteprima**.
6. Nell'azione **Ottieni righe - anteprima** fare clic su **Cambia connessione**. 
7. Nel riquadro di configurazione **Connessioni** fare clic per selezionare una connessione. ad esempio, selezionare **hisdemo2**.
   
    ![](./media/connectors-create-api-informix/InformixconnectorChangeConnection.png)
8. Nell'elenco **Nome tabella** fare clic sulla **freccia in giù** e selezionare **AREA**.
9. Immettere valori per tutte le colonne obbligatorie (asterisco rosso). Ad esempio digitare `99999` per **AREAID** e `Area 99999``102` per **REGIONID**. 
10. Selezionare **Salva**.
    
    ![](./media/connectors-create-api-informix/InformixconnectorInsertRowValues.png)
11. Nel pannello **InformixinsertRow**, all'interno dell'elenco **Tutte le esecuzioni** di **Riepilogo**, selezionare il primo elemento elencato (che corrisponde all'esecuzione più recente).
12. Nel pannello **Esecuzione dell'app per la logica** fare clic su **Dettagli esecuzione**. Nell'elenco **Azione** fare clic su **Get_rows**. Verificare che il valore di **Stato** sia **Operazione riuscita**. Selezionare il **collegamento Input** per visualizzare gli input. Selezionare il **collegamento Output**e visualizzare gli output, che devono includere una riga nuova.
    
    ![](./media/connectors-create-api-informix/InformixconnectorInsertRowOutputs.png)

## <a name="fetch-one-row-using-select"></a>Recuperare una riga con SELECT
È possibile creare un'azione di app per la logica per recuperare una riga nella tabella di Informix. Questa azione indica al connettore di elaborare un'istruzione SELECT WHERE di Informix, ad esempio `SELECT FROM AREA WHERE AREAID = '99999'`.

### <a name="create-a-logic-app"></a>Creare un'app per la logica
1. Nella **schermata iniziale di Azure** selezionare **+** (segno più), **Web e dispositivi mobili** e quindi **App per la logica**.
2. Compilare i campi **Nome**, ad esempio `InformixgetRow`, **Sottoscrizione**, **Gruppo di risorse**, **Località** e **Piano di servizio app**. Selezionare **Aggiungi al dashboard** e quindi selezionare **Crea**.

### <a name="add-a-trigger-and-action"></a>Aggiungere un trigger e un'azione
1. In **Progettazione app per la logica**, selezionare **LogicApp vuoto** nell'elenco **Modelli**.
2. Nell'elenco dei **trigger** fare clic su **Ricorrenza**. 
3. Nel trigger **Ricorrenza**, fare clic su **Modifica**, poi sull'elenco a discesa **Frequenza** per selezionare **Giorno** e infine fare clic su **Intervallo** per digitare **7**. 
4. Fare clic sulla casella **+ Nuovo passaggio** e su **Aggiungi un'azione**.
5. Nell'elenco delle **azioni** digitare **informix** nella casella di modifica **Cercare altre azioni** e fare clic su **Informix - Ottieni righe - anteprima**.
6. Nell'azione **Ottieni righe - anteprima** fare clic su **Cambia connessione**. 
7. Nel riquadro di configurazione **Connessioni** fare clic per selezionare una connessione esistente. ad esempio, selezionare **hisdemo2**.
   
    ![](./media/connectors-create-api-informix/InformixconnectorChangeConnection.png)
8. Nell'elenco **Nome tabella** fare clic sulla **freccia in giù** e selezionare **AREA**.
9. Immettere valori per tutte le colonne obbligatorie (asterisco rosso). Ad esempio digitare `99999` per **AREAID**. 
10. Se lo si desidera, fare clic su **Mostra le opzioni avanzate** per specificare le opzioni di query.
11. Selezionare **Salva**. 
    
    ![](./media/connectors-create-api-informix/InformixconnectorGetRowValues.png)
12. Nel pannello **InformixgetRow** fare clic sul primo elemento dell'elenco **Tutte le esecuzioni** (corrispondente all'esecuzione più recente) in **Riepilogo**.
13. Nel pannello **Esecuzione dell'app per la logica** fare clic su **Dettagli esecuzione**. Nell'elenco **Azione** fare clic su **Get_rows**. Verificare che il valore di **Stato** sia **Operazione riuscita**. Selezionare il **collegamento Input** per visualizzare gli input. Selezionare il **collegamento Output**e visualizzare gli output, che devono includere una riga.
    
    ![](./media/connectors-create-api-informix/InformixconnectorGetRowOutputs.png)

## <a name="change-one-row-using-update"></a>Modificare una riga con UPDATE
È possibile creare un'azione di app per la logica per modificare una riga nella tabella di Informix. Questa azione indica al connettore di elaborare un'istruzione UPDATE di Informix, ad esempio `UPDATE AREA SET AREAID = '99999', AREADESC = 'Area 99999', REGIONID = 102)`.

### <a name="create-a-logic-app"></a>Creare un'app per la logica
1. Nella **schermata iniziale di Azure** selezionare **+** (segno più), **Web e dispositivi mobili** e quindi **App per la logica**.
2. Compilare i campi **Nome**, ad esempio `InformixupdateRow`, **Sottoscrizione**, **Gruppo di risorse**, **Località** e **Piano di servizio app**. Selezionare **Aggiungi al dashboard** e quindi selezionare **Crea**.

### <a name="add-a-trigger-and-action"></a>Aggiungere un trigger e un'azione
1. In **Progettazione app per la logica**, selezionare **LogicApp vuoto** nell'elenco **Modelli**.
2. Nell'elenco dei **trigger** fare clic su **Ricorrenza**. 
3. Nel trigger **Ricorrenza**, fare clic su **Modifica**, poi sull'elenco a discesa **Frequenza** per selezionare **Giorno** e infine fare clic su **Intervallo** per digitare **7**. 
4. Fare clic sulla casella **+ Nuovo passaggio** e su **Aggiungi un'azione**.
5. Nell'elenco delle **azioni** digitare **informix** nella casella di modifica **Cercare altre azioni** e quindi fare clic su **Informix - Aggiorna riga - anteprima**.
6. Nell'azione **Ottieni righe - anteprima** fare clic su **Cambia connessione**. 
7. Nel riquadro di configurazione **Connessioni** fare clic per selezionare una connessione esistente. ad esempio, selezionare **hisdemo2**.
   
    ![](./media/connectors-create-api-informix/InformixconnectorChangeConnection.png)
8. Nell'elenco **Nome tabella** fare clic sulla **freccia in giù** e selezionare **AREA**.
9. Immettere valori per tutte le colonne obbligatorie (asterisco rosso). Ad esempio digitare `99999` per **AREAID** e `Updated 99999``102` per **REGIONID**. 
10. Selezionare **Salva**. 
    
    ![](./media/connectors-create-api-informix/InformixconnectorUpdateRowValues.png)
11. Nel pannello **InformixupdateRow** fare clic sul primo elemento dell'elenco **Tutte le esecuzioni** (corrispondente all'esecuzione più recente) in **Riepilogo**.
12. Nel pannello **Esecuzione dell'app per la logica** fare clic su **Dettagli esecuzione**. Nell'elenco **Azione** fare clic su **Get_rows**. Verificare che il valore di **Stato** sia **Operazione riuscita**. Selezionare il **collegamento Input** per visualizzare gli input. Selezionare il **collegamento Output**e visualizzare gli output, che devono includere una riga nuova.
    
    ![](./media/connectors-create-api-informix/InformixconnectorUpdateRowOutputs.png)

## <a name="remove-one-row-using-delete"></a>Rimozione di una riga con DELETE
È possibile creare un'azione di app per la logica per rimuovere una riga nella tabella di Informix. Questa azione indica al connettore di elaborare un'istruzione DELETE di Informix, ad esempio `DELETE FROM AREA WHERE AREAID = '99999'`.

### <a name="create-a-logic-app"></a>Creare un'app per la logica
1. Nella **schermata iniziale di Azure** selezionare **+** (segno più), **Web e dispositivi mobili** e quindi **App per la logica**.
2. Compilare i campi **Nome**, ad esempio `InformixdeleteRow`, **Sottoscrizione**, **Gruppo di risorse**, **Località** e **Piano di servizio app**. Selezionare **Aggiungi al dashboard** e quindi selezionare **Crea**.

### <a name="add-a-trigger-and-action"></a>Aggiungere un trigger e un'azione
1. In **Progettazione app per la logica**, selezionare **LogicApp vuoto** nell'elenco **Modelli**.
2. Nell'elenco dei **trigger** fare clic su **Ricorrenza**. 
3. Nel trigger **Ricorrenza**, fare clic su **Modifica**, poi sull'elenco a discesa **Frequenza** per selezionare **Giorno** e infine fare clic su **Intervallo** per digitare **7**. 
4. Fare clic sulla casella **+ Nuovo passaggio** e su **Aggiungi un'azione**.
5. Nell'elenco delle **azioni** digitare**informix** nella casella di modifica **Cercare altre azioni** e quindi fare clic su **Informix - Elimina riga - anteprima**.
6. Nell'azione **Ottieni righe - anteprima** fare clic su **Cambia connessione**. 
7. Nel riquadro di configurazione **Connessioni** selezionare una connessione esistente. ad esempio, selezionare **hisdemo2**.
   
    ![](./media/connectors-create-api-informix/InformixconnectorChangeConnection.png)
8. Nell'elenco **Nome tabella** fare clic sulla **freccia in giù** e selezionare **AREA**.
9. Immettere valori per tutte le colonne obbligatorie (asterisco rosso). Ad esempio digitare `99999` per **AREAID**. 
10. Selezionare **Salva**. 
    
    ![](./media/connectors-create-api-informix/InformixconnectorDeleteRowValues.png)
11. Nel pannello **InformixdeleteRow** fare clic sul primo elemento dell'elenco **Tutte le esecuzioni** (corrispondente all'esecuzione più recente) in **Riepilogo**.
12. Nel pannello **Esecuzione dell'app per la logica** fare clic su **Dettagli esecuzione**. Nell'elenco **Azione** fare clic su **Get_rows**. Verificare che il valore di **Stato** sia **Operazione riuscita**. Selezionare il **collegamento Input** per visualizzare gli input. Selezionare il **collegamento Output**e visualizzare gli output, che devono includere la riga eliminata.
    
    ![](./media/connectors-create-api-informix/InformixconnectorDeleteRowOutputs.png)

## <a name="supported-informix-platforms-and-versions"></a>Piattaforme e versioni di Informix supportate
Il connettore supporta le versioni di IBM Informix seguenti, in caso di configurazione per il supporto di connessioni client DRDA (Distributed Relational Database Architecture).

* IBM Informix 12.1
* IBM Informix 11.7

## <a name="connector-specific-details"></a>Dettagli specifici del connettore

Per visualizzare eventuali azioni e trigger definiti in Swagger ed eventuali limiti, vedere i [dettagli del connettore](/connectors/informix/). 

## <a name="next-steps"></a>Passaggi successivi
[Creare un'app per la logica](../logic-apps/logic-apps-create-a-logic-app.md). Esplorare gli altri connettori disponibili nelle app per la logica nell' [elenco delle API](apis-list.md).


