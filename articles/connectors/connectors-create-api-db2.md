---
title: Connettersi a DB2 - App per la logica di Azure | Microsoft Docs
description: Gestione delle risorse con le API REST di IBM DB2 e App per la logica di Azure
services: logic-apps
ms.service: logic-apps
author: ecfan
ms.author: estfan
ms.reviewer: plarsen, LADocs
ms.suite: integration
ms.topic: article
ms.date: 08/23/2018
tags: connectors
ms.openlocfilehash: 354e67183a36f511811d74a0685dea2e23d6c0e2
ms.sourcegitcommit: 58c5cd866ade5aac4354ea1fe8705cee2b50ba9f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/24/2018
ms.locfileid: "42818876"
---
# <a name="manage-ibm-db2-resources-with-azure-logic-apps"></a>Gestione delle risorse di IBM DB2 con App per la logica di Azure

Con App per la logica di Azure e il connettore IBM DB2 è possibile creare attività e flussi di lavoro automatizzati in base alle risorse archiviate nel database IBM DB2. I flussi di lavoro possono connettersi alle risorse nel database, leggere ed elencare le tabelle di database, aggiungere righe, modificarle, eliminarle e altro ancora. È possibile includere azioni nelle app per la logica che ottengono risposte dal database e rendono l'output disponibile per altre azioni. 

Questo articolo illustra come creare un'app per la logica che esegue varie operazioni di database. Se non si ha familiarità con le app per la logica, consultare [Informazioni su App per la logica di Azure](../logic-apps/logic-apps-overview.md).

## <a name="supported-platforms-and-versions"></a>Piattaforme e versioni supportate

Il connettore DB2 include un client Microsoft che comunica con server DB2 remoti su una rete TCP/IP. È possibile usarlo per accedere ai database cloud, ad esempio IBM Bluemix dashDB o IBM DB2 per Windows in esecuzione nella virtualizzazione Azure. È anche possibile accedere ai database DB2 locali dopo aver [installato e configurato il gateway dati locale](../logic-apps/logic-apps-gateway-connection.md). 

Il connettore IBM DB2 supporta queste piattaforme e versioni, nonché i prodotti compatibili con IBM DB2, ad esempio, IBM Bluemix dashDB, che supportano DRDA (Distributed Relational Database Architecture) SQLAM (SQL Access Manager) versioni 10 e 11:

| Piattaforma | Version | 
|----------|---------|
| IBM DB2 per z/OS | 11.1, 10.1 |
| IBM DB2 per i | 7.3, 7.2, 7.1 |
| IBM DB2 per LUW | 11, 10.5 |
|||

## <a name="supported-database-operations"></a>Operazioni del database supportate

Il connettore IBM DB2 supporta queste operazioni di database, associabili alle azioni corrispondenti nel connettore:

| Operazione di database | Azione del connettore | 
|--------------------|------------------|
| Visualizzazione di un elenco delle tabelle di database | Ottieni tabelle | 
| Lettura di una riga con SELECT | Ottenere la riga | 
| Lettura di tutte le righe con SELECT | Ottieni righe | 
| Aggiunta di una riga con INSERT | Inserisci riga | 
| Modifica di una riga con UPDATE | Aggiorna riga | 
| Rimozione di una riga con DELETE | Elimina riga | 
|||

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, <a href="https://azure.microsoft.com/free/" target="_blank">iscriversi per creare un account Azure gratuito</a>. 

* Un database IBM DB2, basato sul cloud o locale

* Conoscenza di base di [come creare le app per la logica](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* L'app per la logica in cui si intende accedere al database DB2. Questo connettore offre solo azioni, quindi per avviare l'app per la logica, usare un trigger distinto, ad esempio il trigger **Ricorrenza**.
Gli esempi di questo articolo usano il trigger **Ricorrenza**.

<a name="add-db2-action"></a>

## <a name="add-db2-action---get-tables"></a>Aggiungere un'azione DB2 - Ottieni tabelle

1. Nel [portale di Azure](https://portal.azure.com) aprire l'app per la logica in Progettazione app per la logica, se ancora chiusa.

1. Nel trigger scegliere **Nuovo passaggio**.

1. Nella casella di ricerca immettere "db2" come filtro. Per questo esempio, nell'elenco di azioni, selezionare questa azione: **Ottieni tabelle (anteprima)**
   
   ![Seleziona azione](./media/connectors-create-api-db2/select-db2-action.png)

   A questo punto viene chiesto di fornire i dettagli della connessione per il database DB2. 

1. Seguire questi passaggi per la creazione di connessioni per [database cloud](#cloud-connection) o [database locali](#on-premises-connection).

<a name="cloud-connection"></a>

## <a name="connect-to-cloud-db2"></a>Connettersi a DB2 - Cloud

Per configurare la connessione, specificare questi dettagli quando richiesto, scegliere **Crea** e quindi salvare l'app per la logica:

| Proprietà | Obbligatoria | DESCRIZIONE | 
|----------|----------|-------------| 
| **Connetti tramite gateway dati locale** | No  | Si applica solo alle connessioni locali. | 
| **Connection Name** (Nome connessione) | Yes | Il nome della connessione, ad esempio, "MyLogicApp-DB2-connection" |
| **Server** | Yes | L'indirizzo o l'alias-due punti-numero di porta del server DB2, ad esempio, "myDB2server.cloudapp.net:50000" <p><p>**Nota**: questo valore è una stringa che rappresenta un alias o un indirizzo TCP/IP, in formato IPv4 o IPv6, seguito da due punti e un numero di porta TCP/IP. |
| **Database** | Yes | Il nome del database <p><p>**Nota**: questo valore è una stringa che rappresenta un nome di database relazionale (RDBNAM) DRDA: <p>- DB2 per z/OS accetta una stringa di 16 byte, in cui il database è definito come posizione di IBM DB2 per z/OS. <br>- DB2 per i accetta una stringa di 18 byte, in cui il database è definito come database relazionale IBM DB2 per i. <br>- DB2 per LUW accetta una stringa di 8 byte. |
| **Nome utente** | Yes | Il nome utente del database <p><p>**Nota**: questo valore è una stringa la cui lunghezza è basata sul database specifico: <p><p>- DB2 per z/OS accetta una stringa di 8 byte. <br>- DB2 per i accetta una stringa di 10 byte. <br>- DB2 per Linux o UNIX accetta una stringa di 8 byte. <br>- DB2 per Windows accetta una stringa di 30 byte. | 
| **Password** | Yes | La password amministrativa per il database | 
|||| 

Ad esempio: 

![I dettagli di connessione per i database basati sul cloud](./media/connectors-create-api-db2/create-db2-cloud-connection.png)

<a name="on-premises-connection"></a>

## <a name="connect-to-on-premises-db2"></a>Connettersi a DB2 - Locale

Prima di creare la connessione, è necessario aver già installato il gateway dati locale. In caso contrario non sarà possibile completare la configurazione della connessione. Con un'installazione del gateway, continuare fornendo questi dettagli di connessione e scegliere **Create**.

| Proprietà | Obbligatoria | DESCRIZIONE | 
|----------|----------|-------------| 
| **Connetti tramite gateway dati locale** | Yes | Si applica quando si desidera una connessione locale e mostra le proprietà della connessione locale. | 
| **Connection Name** (Nome connessione) | Yes | Il nome della connessione, ad esempio, "MyLogicApp-DB2-connection" | 
| **Server** | Yes | L'indirizzo o l'alias-due punti-numero di porta del server DB2, ad esempio, "myDB2server:50000" <p><p>**Nota**: questo valore è una stringa che rappresenta un alias o un indirizzo TCP/IP, in formato IPv4 o IPv6, seguito da due punti e un numero di porta TCP/IP. | 
| **Database** | Yes | Il nome del database <p><p>**Nota**: questo valore è una stringa che rappresenta un nome di database relazionale (RDBNAM) DRDA: <p>- DB2 per z/OS accetta una stringa di 16 byte, in cui il database è definito come posizione di IBM DB2 per z/OS. <br>- DB2 per i accetta una stringa di 18 byte, in cui il database è definito come database relazionale IBM DB2 per i. <br>- DB2 per LUW accetta una stringa di 8 byte. | 
| **Autenticazione** | Yes | Il tipo di autenticazione per la connessione, ad esempio, "Base" <p><p>**Nota**: selezionare questo valore dall'elenco, che include Base o Windows (Kerberos). | 
| **Nome utente** | Yes | Il nome utente del database <p><p>**Nota**: questo valore è una stringa la cui lunghezza è basata sul database specifico: <p><p>- DB2 per z/OS accetta una stringa di 8 byte. <br>- DB2 per i accetta una stringa di 10 byte. <br>- DB2 per Linux o UNIX accetta una stringa di 8 byte. <br>- DB2 per Windows accetta una stringa di 30 byte. | 
| **Password** | Yes | La password amministrativa per il database | 
| **Gateway** | Yes | Il nome del gateway dati locale installato <p><p>**Nota**: selezionare questo valore dall'elenco, che include tutti i gateway dati installati all'interno del gruppo di risorse e della sottoscrizione di Azure. | 
|||| 

Ad esempio: 

![Dettagli di connessione dei database locali](./media/connectors-create-api-db2/create-db2-on-premises-connection.png)

### <a name="view-output-tables"></a>Visualizzare le tabelle di output

Per eseguire manualmente l'app per la logica, sulla barra degli strumenti di progettazione scegliere **Esegui**. Al termine dell'esecuzione dell'app per la logica sarà possibile visualizzare l'output dell'esecuzione.

1. Selezionare **Panoramica** dal menu dell'app per la logica. 

1. In **Riepilogo**, nella sezione **Cronologia esecuzioni**, selezionare l'esecuzione più recente, ovvero il primo elemento nell'elenco. 

   ![Visualizzare la cronologia di esecuzione](./media/connectors-create-api-db2/run-history.png)

1. In **Esecuzione dell'app per la logica** è ora possibile esaminare lo stato, gli input e gli output per ogni passaggio nell'app per la logica. Espandere l'azione **Ottieni tabelle**.

   ![Espandere l'azione](./media/connectors-create-api-db2/expand-action-step.png)

1. Per visualizzare gli input, scegliere **Mostra input non elaborati**. 

1. Per visualizzare gli output, scegliere **Mostra output non elaborati**. 

   Gli output includono un elenco di tabelle. 
   
   ![Visualizzare le tabelle di output](./media/connectors-create-api-db2/db2-connector-get-tables-outputs.png)

## <a name="get-row"></a>Ottenere la riga

Per recuperare un record in una tabella di database DB2, usare l'azione **Ottieni riga** nell'app per la logica. Questa azione esegue un'istruzione `SELECT WHERE` DB2, ad esempio `SELECT FROM AREA WHERE AREAID = '99999'`.

1. Se non sono mai state usate azioni DB2 nell'app per la logica, rivedere i passaggi nella sezione [Aggiungere un'azione DB2 - Ottieni tabelle](#add-db2-action), ma aggiungere l'azione **Ottieni riga** e tornare qui per continuare. 

   Dopo aver aggiunto l'azione **Ottieni riga**, ecco come verrà visualizzata l'app per la logica di esempio:

   ![Azione Ottieni riga](./media/connectors-create-api-db2/db2-get-row-action.png)

1. Specificare valori per tutte le proprietà obbligatorie (*). Dopo aver selezionato una tabella, l'azione mostrerà le proprietà pertinenti specifiche per i record al suo interno.

   | Proprietà | Obbligatoria | DESCRIZIONE | 
   |----------|----------|-------------| 
   | **Nome tabella** | Yes | La tabella che contiene il record desiderato, ad esempio "AREA" in questo esempio | 
   | **ID area** | Yes | L'ID che contiene il record desiderato, ad esempio "99999" in questo esempio | 
   |||| 

   ![Selezionare una tabella](./media/connectors-create-api-db2/db2-get-row-action-select-table.png)

1. Al termine, nella barra degli strumenti della finestra di progettazione scegliere **Salva**. 

### <a name="view-output-row"></a>Visualizzare la riga di output

Per eseguire manualmente l'app per la logica, sulla barra degli strumenti di progettazione scegliere **Esegui**. Al termine dell'esecuzione dell'app per la logica sarà possibile visualizzare l'output dell'esecuzione.

1. Selezionare **Panoramica** dal menu dell'app per la logica. 

1. In **Riepilogo**, nella sezione **Cronologia esecuzioni**, selezionare l'esecuzione più recente, ovvero il primo elemento nell'elenco. 

1. In **Esecuzione dell'app per la logica** è ora possibile esaminare lo stato, gli input e gli output per ogni passaggio nell'app per la logica. Espandere l'azione **Ottieni riga**.

1. Per visualizzare gli input, scegliere **Mostra input non elaborati**. 

1. Per visualizzare gli output, scegliere **Mostra output non elaborati**. 

   Gli output includono la riga specificata. 
   
   ![Visualizzare la riga di output](./media/connectors-create-api-db2/db2-connector-get-row-outputs.png)

## <a name="get-rows"></a>Ottieni righe

Per recuperare tutti i record in una tabella di database DB2, usare l'azione **Ottieni righe** nell'app per la logica. Questa azione esegue un'istruzione `SELECT` DB2, ad esempio `SELECT * FROM AREA`.

1. Se non sono mai state usate azioni DB2 nell'app per la logica, rivedere i passaggi nella sezione [Aggiungere un'azione DB2 - Ottieni tabelle](#add-db2-action), ma aggiungere l'azione **Ottieni righe** e tornare qui per continuare. 

   Dopo aver aggiunto l'azione **Ottieni righe**, ecco come verrà visualizzata l'app per la logica di esempio:

   ![Azione Ottieni righe](./media/connectors-create-api-db2/db2-get-rows-action.png)

1. Aprire l'elenco **Nome tabella** e selezionare la tabella desiderata, ovvero "AREA" in questo esempio: 

   ![Selezionare una tabella](./media/connectors-create-api-db2/db2-get-rows-action-select-table.png)

1. Per specificare un filtro o eseguire una query per i risultati, scegliere **Mostra opzioni avanzate**.

1. Al termine, nella barra degli strumenti della finestra di progettazione scegliere **Salva**. 

### <a name="view-output-rows"></a>Visualizzare le righe di output

Per eseguire manualmente l'app per la logica, sulla barra degli strumenti di progettazione scegliere **Esegui**. Al termine dell'esecuzione dell'app per la logica sarà possibile visualizzare l'output dell'esecuzione.

1. Selezionare **Panoramica** dal menu dell'app per la logica. 

1. In **Riepilogo**, nella sezione **Cronologia esecuzioni**, selezionare l'esecuzione più recente, ovvero il primo elemento nell'elenco. 

1. In **Esecuzione dell'app per la logica** è ora possibile esaminare lo stato, gli input e gli output per ogni passaggio nell'app per la logica. Espandere l'azione **Ottieni righe**.

1. Per visualizzare gli input, scegliere **Mostra input non elaborati**. 

1. Per visualizzare gli output, scegliere **Mostra output non elaborati**. 

   L'output include tutti i record nella tabella specificata.
   
   ![Visualizzare le righe di output](./media/connectors-create-api-db2/db2-connector-get-rows-outputs.png)

## <a name="insert-row"></a>Inserisci riga

Per aggiungere un singolo record in una tabella di database DB2, usare l'azione **Ottieni riga** nell'app per la logica. Questa azione esegue un'istruzione `INSERT` DB2, ad esempio `INSERT INTO AREA (AREAID, AREADESC, REGIONID) VALUES ('99999', 'Area 99999', 102)`.

1. Se non sono mai state usate azioni DB2 nell'app per la logica, rivedere i passaggi nella sezione [Aggiungere un'azione DB2 - Ottieni tabelle](#add-db2-action), ma aggiungere l'azione **Inserisci riga** e tornare qui per continuare. 

   Dopo aver aggiunto l'azione **Inserisci riga**, ecco come verrà visualizzata l'app per la logica di esempio:

   ![Azione Inserisci riga](./media/connectors-create-api-db2/db2-insert-row-action.png)

1. Specificare valori per tutte le proprietà obbligatorie (*). Dopo aver selezionato una tabella, l'azione mostrerà le proprietà pertinenti specifiche per i record al suo interno. 

   Ecco le proprietà per l'esempio:

   | Proprietà | Obbligatoria | DESCRIZIONE | 
   |----------|----------|-------------| 
   | **Nome tabella** | Yes | La tabella in cui aggiungere il record, ad esempio "AREA" | 
   | **ID area** | Yes | L'ID per l'area da aggiungere, ad esempio "99999" | 
   | **Area description** (Descrizione area) | Yes | La descrizione dell'area da aggiungere, ad esempio "Area 99999" | 
   | **Region ID** (ID area) | Yes | L'ID dell'area da aggiungere, ad esempio "102" | 
   |||| 

   Ad esempio: 

   ![Selezionare una tabella](./media/connectors-create-api-db2/db2-insert-row-action-select-table.png)

1. Al termine, nella barra degli strumenti della finestra di progettazione scegliere **Salva**. 

### <a name="view-insert-row-outputs"></a>Visualizzare gli output di Inserisci riga

Per eseguire manualmente l'app per la logica, sulla barra degli strumenti di progettazione scegliere **Esegui**. Al termine dell'esecuzione dell'app per la logica sarà possibile visualizzare l'output dell'esecuzione.

1. Selezionare **Panoramica** dal menu dell'app per la logica. 

1. In **Riepilogo**, nella sezione **Cronologia esecuzioni**, selezionare l'esecuzione più recente, ovvero il primo elemento nell'elenco. 

1. In **Esecuzione dell'app per la logica** è ora possibile esaminare lo stato, gli input e gli output per ogni passaggio nell'app per la logica. Espandere l'azione **Inserisci riga**.

1. Per visualizzare gli input, scegliere **Mostra input non elaborati**. 

1. Per visualizzare gli output, scegliere **Mostra output non elaborati**. 

   Gli output includono tutti i record aggiunti alla tabella specificata.
   
   ![Visualizzare l'output con la riga inserita](./media/connectors-create-api-db2/db2-connector-insert-row-outputs.png)

## <a name="update-row"></a>Aggiorna riga

Per aggiornare un singolo record in una tabella di database DB2, usare l'azione **Aggiorna riga** nell'app per la logica. Questa azione esegue un'istruzione `UPDATE` DB2, ad esempio `UPDATE AREA SET AREAID = '99999', AREADESC = 'Updated 99999', REGIONID = 102)`.

1. Se non sono mai state usate azioni DB2 nell'app per la logica, rivedere i passaggi nella sezione [Aggiungere un'azione DB2 - Ottieni tabelle](#add-db2-action), ma aggiungere l'azione **Aggiorna riga** e tornare qui per continuare. 

   Dopo aver aggiunto l'azione **Aggiorna riga**, ecco come verrà visualizzata l'app per la logica di esempio:

   ![Azione Aggiorna riga](./media/connectors-create-api-db2/db2-update-row-action.png)

1. Specificare valori per tutte le proprietà obbligatorie (*). Dopo aver selezionato una tabella, l'azione mostrerà le proprietà pertinenti specifiche per i record al suo interno. 

   Ecco le proprietà per l'esempio:

   | Proprietà | Obbligatoria | DESCRIZIONE | 
   |----------|----------|-------------| 
   | **Nome tabella** | Yes | La tabella in cui aggiornare il record, ad esempio "AREA" | 
   | **ID riga** | Yes | L'ID del record da aggiornare, ad esempio "99999" | 
   | **ID area** | Yes | Il nuovo ID di area, ad esempio "99999" | 
   | **Area description** (Descrizione area) | Yes | La nuova descrizione di area, ad esempio "Updated 99999" (99999 aggiornata) | 
   | **Region ID** (ID area) | Yes | Il nuovo ID di area, ad esempio "102" | 
   |||| 

   Ad esempio: 

   ![Selezionare una tabella](./media/connectors-create-api-db2/db2-update-row-action-select-table.png)

1. Al termine, nella barra degli strumenti della finestra di progettazione scegliere **Salva**. 

### <a name="view-update-row-outputs"></a>Visualizzare gli output di Aggiorna riga

Per eseguire manualmente l'app per la logica, sulla barra degli strumenti di progettazione scegliere **Esegui**. Al termine dell'esecuzione dell'app per la logica sarà possibile visualizzare l'output dell'esecuzione.

1. Selezionare **Panoramica** dal menu dell'app per la logica. 

1. In **Riepilogo**, nella sezione **Cronologia esecuzioni**, selezionare l'esecuzione più recente, ovvero il primo elemento nell'elenco. 

1. In **Esecuzione dell'app per la logica** è ora possibile esaminare lo stato, gli input e gli output per ogni passaggio nell'app per la logica. Espandere l'azione **Aggiorna riga**.

1. Per visualizzare gli input, scegliere **Mostra input non elaborati**. 

1. Per visualizzare gli output, scegliere **Mostra output non elaborati**. 

   Gli output includono i record aggiornati nella tabella specificata.
   
   ![Visualizzare l'output con la riga aggiornata](./media/connectors-create-api-db2/db2-connector-update-row-outputs.png)

## <a name="delete-row"></a>Elimina riga

Per eliminare un singolo record da una tabella di database DB2, usare l'azione **Elimina riga** nell'app per la logica. Questa azione esegue un'istruzione `DELETE` DB2, ad esempio `DELETE FROM AREA WHERE AREAID = '99999'`.

1. Se non sono mai state usate azioni DB2 nell'app per la logica, rivedere i passaggi nella sezione [Aggiungere un'azione DB2 - Ottieni tabelle](#add-db2-action), ma aggiungere l'azione **Elimina riga** e tornare qui per continuare. 

   Dopo aver aggiunto l'azione **Elimina riga**, ecco come verrà visualizzata l'app per la logica di esempio:

   ![Azione Elimina riga](./media/connectors-create-api-db2/db2-delete-row-action.png)

1. Specificare valori per tutte le proprietà obbligatorie (*). Dopo aver selezionato una tabella, l'azione mostrerà le proprietà pertinenti specifiche per i record al suo interno. 

   Ecco le proprietà per l'esempio:

   | Proprietà | Obbligatoria | DESCRIZIONE | 
   |----------|----------|-------------| 
   | **Nome tabella** | Yes | La tabella in cui eliminare il record, ad esempio "AREA" | 
   | **ID riga** | Yes | L'ID del record da eliminare, ad esempio "99999" | 
   |||| 

   Ad esempio: 

   ![Selezionare una tabella](./media/connectors-create-api-db2/db2-delete-row-action-select-table.png)

1. Al termine, nella barra degli strumenti della finestra di progettazione scegliere **Salva**. 

### <a name="view-delete-row-outputs"></a>Visualizzare gli output di Elimina riga

Per eseguire manualmente l'app per la logica, sulla barra degli strumenti di progettazione scegliere **Esegui**. Al termine dell'esecuzione dell'app per la logica sarà possibile visualizzare l'output dell'esecuzione.

1. Selezionare **Panoramica** dal menu dell'app per la logica. 

1. In **Riepilogo**, nella sezione **Cronologia esecuzioni**, selezionare l'esecuzione più recente, ovvero il primo elemento nell'elenco. 

1. In **Esecuzione dell'app per la logica** è ora possibile esaminare lo stato, gli input e gli output per ogni passaggio nell'app per la logica. Espandere l'azione **Elimina riga**.

1. Per visualizzare gli input, scegliere **Mostra input non elaborati**. 

1. Per visualizzare gli output, scegliere **Mostra output non elaborati**. 

   Gli output non includono più il record eliminato dalla tabella specificata.
   
   ![Visualizzare l'output con la riga eliminata](./media/connectors-create-api-db2/db2-connector-delete-row-outputs.png)

## <a name="connector-reference"></a>Informazioni di riferimento sui connettori

Per informazioni tecniche, ad esempio su trigger, azioni e limiti indicati nel file Swagger del connettore, vedere la [pagina di riferimento del connettore](/connectors/db2/). 

## <a name="get-support"></a>Supporto

* In caso di domande, visitare il [forum di App per la logica di Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Per votare o inviare idee relative alle funzionalità, visitare il [sito dei commenti e suggerimenti degli utenti di App per la logica](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su altri [connettori di App per la logica](../connectors/apis-list.md)
