---
title: Progettare il primo database SQL di Azure | Microsoft Docs
description: Informazioni su come progettare il primo database SQL di Azure.
services: sql-database
documentationcenter: 
author: janeng
manager: jstrauss
editor: 
tags: 
ms.assetid: 
ms.service: sql-database
ms.custom: tutorial
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: 
ms.date: 03/23/2017
ms.author: janeng
translationtype: Human Translation
ms.sourcegitcommit: 07635b0eb4650f0c30898ea1600697dacb33477c
ms.openlocfilehash: 313bcf4fbc0ab7f251dd62b1e2151afef8392a55
ms.lasthandoff: 03/28/2017


---

# <a name="design-your-first-azure-sql-database"></a>Progettare il primo database SQL di Azure

In questa esercitazione, l'utente usa il portale di Azure per creare un database in un nuovo server con un firewall a livello di server. Si userà quindi SQL Server Management Studio per creare una tabella, caricare i dati, eseguire query e aggiungere un indice alla tabella. Infine, si useranno i backup automatici del servizio Database SQL per ripristinare il database a un punto precedente, prima di aggiungere la nuova tabella.

Per completare questa esercitazione, assicurarsi che di aver installato la versione più recente di [SQL Server Management Studio](https://msdn.microsoft.com/library/ms174173.aspx) (SSMS) 

## <a name="step-1---log-in-to-the-azure-portal"></a>Passaggio 1: accedere al Portale di Azure

Accedere al [Portale di Azure](https://portal.azure.com/).

## <a name="step-2---create-a-sql-database"></a>Passaggio 2: creare un database SQL

Un database SQL di Azure viene creato con un set definito di [risorse di calcolo e di archiviazione](sql-database-service-tiers.md). Il database viene creato in un [gruppo di risorse di Azure](../azure-resource-manager/resource-group-overview.md) e in un [server logico di database SQL di Azure](sql-database-features.md). 

Seguire questi passaggi per creare un database SQL contenente i dati di esempio di Adventure Works LT. 

1. Fare clic sul pulsante **Nuovo** nell'angolo superiore sinistro del portale di Azure.

2. Selezionare **Database** nella pagina **Nuovo** e quindi **Database SQL** nella pagina **Database**.

3. Compilare il modulo Database SQL con le informazioni obbligatorie: 
   - Nome database: specificare un nome per il database
   - Sottoscrizione: selezionare la sottoscrizione
   - Gruppo di risorse: selezionarne uno nuovo o esistente
   - Origine: selezionare **Sample (AdventureWorksLT)**
   - Server: creare un nuovo server (**Server** deve essere un nome univoco globale)
   - Pool elastico: selezionare **Non ora** per questa guida introduttiva
   - Piano tariffario: selezionare **20 DTU** e **250** GB di memoria
   - Regole di confronto: non è possibile modificare questo valore quando si importa il database di esempio 
   - Aggiungi al dashboard: selezionare questa casella di controllo

      ![creazione del database](./media/sql-database-get-started/create-database-s1.png)

4. Al termine, fare clic su **Crea**. Il provisioning richiede alcuni minuti.
5. Al termine della distribuzione del database SQL, selezionare i **database SQL** nel dashboard oppure scegliendo **Database SQL** dal menu a sinistra e fare clic sul nuovo database nella pagina **Database SQL**. Si apre una pagina di panoramica per il database che visualizza il nome completo del server (ad esempio, **mynewserver20170313.database.windows.net**) e fornisce altre opzioni di configurazione.

      ![Nuovo database sql nuova](./media/sql-database-get-started/new-database-s1-overview.png) 

## <a name="step-3---create-a-server-level-firewall-rule"></a>Passaggio 3: creare una regola del firewall a livello di server

Il servizio Database SQL crea un firewall che impedisce alle applicazioni e agli strumenti esterni di connettersi al server e al database. Seguire questi passaggi per creare una [regola del firewall a livello di server di database SQL](sql-database-firewall-configure.md) per l'indirizzo IP per abilitare la connettività esterna tramite il firewall del database SQL. 

1. Fare clic su **Imposta firewall server** sulla barra degli strumenti del database. Si apre la pagina **Impostazioni del firewall** per il server del database SQL. 

      ![Regola del firewall del server](./media/sql-database-get-started/server-firewall-rule.png) 

2. Fare clic su **Aggiungi IP client** sulla barra degli strumenti e fare clic su **Salva**. Una regola del firewall a livello di server viene creata per l'indirizzo IP corrente.

3. Fare clic su **OK** e quindi sulla **X** per chiudere la pagina Impostazioni del firewall.

È ora possibile connettersi al database e al server usando SQL Server Management Studio o un altro strumento di propria scelta.

## <a name="step-4---get-connection-information"></a>Passaggio 4: ottenere informazioni di connessione

Ottenere il nome completo del server per il server del database SQL di Azure nel portale di Azure. Usare il nome completo del server per connettersi al server tramite SQL Server Management Studio.

1. Accedere al [Portale di Azure](https://portal.azure.com/).
2. Scegliere **Database SQL** dal menu a sinistra, quindi fare clic sul database nella pagina **Database SQL**. 
3. Nel riquadro **Informazioni di base** della pagina del portale di Azure per il database individuare e quindi copiare il **Nome server**.

    <img src="./media/sql-database-connect-query-ssms/connection-information.png" alt="connection information" style="width: 780px;" />

## <a name="step-5---connect-to-the-server-using-ssms"></a>Passaggio 5: connettersi al server utilizzando SSMS

Usare SQL Server Management Studio per stabilire una connessione al server del database SQL di Azure.

1. Digitare **SSMS** nella casella di ricerca di Windows e quindi premere **INVIO** per aprire SSMS.

2. Nella finestra di dialogo **Connetti al server** immettere le informazioni seguenti:
   - **Tipo di server**: specificare il motore del database
   - **Nome server**: immettere il nome completo del server, ad esempio **mynewserver20170313.database.windows.net**
   - **Autenticazione**: specificare l'Autenticazione di SQL Server
   - **Accesso**: immettere l'account dell'amministratore del server
   - **Password**: immettere la password per l'account dell'amministratore del server
 
    <img src="./media/sql-database-connect-query-ssms/connect.png" alt="connect to server" style="width: 780px;" />

3. Fare clic su **Connect**. La finestra Esplora oggetti viene visualizzata in SSMS. 

    <img src="./media/sql-database-connect-query-ssms/connected.png" alt="connected to server" style="width: 780px;" />

4. In Esplora oggetti espandere **Database** e quindi espandere **mySampleDatabase** per visualizzare gli oggetti disponibili nel database di esempio.

## <a name="step-6---create-and-query-a-table"></a>Passaggio 6: creare ed eseguire query su una tabella 
1. In Esplora oggetti fare clic con il pulsante destro del mouse su **mySampleDatabase** e scegliere **Nuova query**. Viene visualizzata una finestra di query vuota, connessa al database.
2. Nella finestra della query digitare la query seguente:

   ```sql 
   CREATE TABLE [dbo].[Students]
   (
     [student_id] int, 
     [name] varchar(100),
     [age] int,
     [email] varchar(100),
     [AddressID] int REFERENCES [SalesLT].[Address] (AddressID)
   );
   ```

   Dopo aver completato la query, è stata creata una tabella vuota nel database Studenti.

3. Nella finestra della query SSMS digitare la query seguente: 

   ```sql
   SELECT name, age, email 
   FROM [dbo].[Students]
   ```

   La tabella Studenti non restituisce alcun dato.

## <a name="step-7---load-data-into-the-table"></a>Passaggio 7: caricare i dati nella tabella 
1. Aprire una finestra del prompt dei comandi.

2. Eseguire il comando di PowerShell seguente per scaricare un file di testo di esempio nella directory corrente.

   ```powershell
   powershell -command "& { (New-Object Net.WebClient).DownloadFile('https://sqldbtutorial.blob.core.windows.net/tutorials/SampleStudentData.txt', 'SampleStudentData.txt'); echo 'Download complete' }" 
   ``` 

3. Al termine, eseguire il comando seguente per inserire 1000 righe nella tabella Studenti, sostituendo i valori per **ServerName**, **DatabaseName**, **UserName** e **Password** con i valori dell'ambiente.

   ```bcp
   bcp Students in SampleStudentData.txt -S <ServerName> -d <DatabaseName> -U <Username> -P <password> -q -c -t ","
   ```

A questo punto, sono stati caricati i dati di esempio nella tabella creata in precedenza.

## <a name="step-8---add-an-index-to-a-table"></a>Passaggio 8: aggiungere un indice alla tabella
Per eseguire una ricerca di valori specifici più efficiente nella tabella, creare un indice nella tabella Studenti. Un indice organizza i dati in modo tale da tale che tutti i dati devono essere esaminati per trovare un valore specifico.

1. Nella finestra della query SSMS digitare la query seguente:

   ```sql 
   CREATE NONCLUSTERED INDEX IX_Age ON Students (age);
   ```

2. Nella finestra della query SSMS digitare la query seguente:

   ```sql
   SELECT name, age, email 
   FROM [dbo].[Students]
   WHERE age > 20
   ```

   Questa query restituisce il nome, l'età e l'indirizzo di posta elettronica di studenti con età superiore ai 20 anni.

## <a name="step-9---restore-a-database-to-a-point-in-time"></a>Passaggio 9: ripristinare un database a un determinato punto nel tempo 
Sui database in Azure si effettuano [backup continui](sql-database-automated-backups.md) che vengono eseguiti automaticamente ogni 5-10 minuti. Tali backup consentono eseguire il ripristino di un database a un determinato punto nel tempo. Il ripristino di un database in un altro punto nel tempo crea un duplicato del database nello stesso server del database originale nel punto nel tempo specificato (entro il periodo di conservazione per il livello di servizio applicato). La procedura seguente consente di ripristinare il database di esempio in un punto precedente all'aggiunta della tabella **Studenti**. 

1. Nella pagina Database SQL del database fare clic su **Ripristina** sulla barra degli strumenti. Si apre la pagina **Ripristina** .

    <img src="./media/sql-database-design-first-database/restore.png" alt="restore" style="width: 780px;" />

2. Compilare il modulo **Ripristina** con le informazioni obbligatorie:
    * Nome database: specificare un nome per il database 
    * Temporizzato: Selezionare la scheda **Temporizzato** del modulo Ripristino 
    * Punto di ripristino: selezionare un punto nel tempo precedente alla modifica del database
    * Server di destinazione: non è possibile modificare questo valore quando si ripristina un database 
    * Pool di database elastici: selezionare **Nessuno**  
    * Piano tariffario: selezionare **20 DTUs** (20 DTU) e **250 GB** di memoria.

    <img src="./media/sql-database-design-first-database/restore-point.png" alt="restore-point" style="width: 780px;" />

3. Fare clic su *OK* per ripristinare il database in un punto precedente all'aggiunta della tabella **Studenti**.

## <a name="next-steps"></a>Passaggi successivi 
Per esempi di attività comuni di PowerShell, vedere gli [esempi di PowerShell per Database SQL](sql-database-powershell-samples.md)

