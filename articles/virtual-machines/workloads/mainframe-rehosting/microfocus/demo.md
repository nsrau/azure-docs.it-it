---
title: Configurare Micro Focus CICS BankDemo per Micro Focus Enterprise Developer 4,0 in macchine virtuali di Azure
description: Eseguire l'applicazione Micro Focus BankDemo in macchine virtuali di Azure per imparare a usare Micro Focus Enterprise Server e Enterprise Developer.
author: sread
ms.author: sread
ms.date: 03/30/2020
ms.topic: article
ms.service: multiple
ms.openlocfilehash: 7fb72b9a7d0d655f99d1e5cf194f7c6f26976a37
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2020
ms.locfileid: "95976194"
---
# <a name="set-up-micro-focus-cics-bankdemo-for-micro-focus-enterprise-developer-40-on-azure"></a>Configurare la microfocus CICS BankDemo per Micro Focus Enterprise Developer 4,0 in Azure

Quando si configura Micro Focus Enterprise Server 4,0 e Enterprise Developer 4,0 in Azure, è possibile testare le distribuzioni dei carichi di lavoro IBM z/OS. Questo articolo illustra come configurare CICS BankDemo, un'applicazione di esempio fornita con Enterprise Developer.

CICs sta per il sistema di controllo delle informazioni dei clienti, la piattaforma di transazione utilizzata da molte delle applicazioni mainframe online. L'applicazione BankDemo è ideale per imparare a usare Enterprise Server e Enterprise Developer e come gestire e distribuire un'applicazione effettiva completa con terminali con schermo verde.

> [!NOTE]
> Presto disponibile: istruzioni per la configurazione di [Micro Focus Enterprise Server 5,0](https://techcommunity.microsoft.com/t5/azurecat/micro-focus-enterprise-server-5-0-quick-start-template-on-azure/ba-p/1160110) nelle VM di Azure.

## <a name="prerequisites"></a>Prerequisiti

- Una macchina virtuale con [Enterprise Developer](set-up-micro-focus-azure.md). Tenere presente che Enterprise Developer dispone di un'istanza completa di Enterprise Server a scopo di sviluppo e test. Questa istanza è l'istanza di Enterprise Server utilizzata per la dimostrazione.

- [SQL Server 2017 Express Edition](https://www.microsoft.com/sql-server/sql-server-editions-express). Scaricarlo e installarlo nella macchina virtuale Enterprise Developer. Enterprise Server richiede un database per la gestione delle aree CICS e l'applicazione BankDemo usa anche un database SQL Server denominato BANKDEMO. Questa demo presuppone l'uso di SQL Server Express per entrambi i database. Quando si installa, selezionare l'installazione di base.

- [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017) (SSMS). SSMS viene usato per la gestione dei database e l'esecuzione di uno script T-SQL. Scaricarlo e installarlo nella macchina virtuale Enterprise Developer.

- [Visual studio 2019](https://azure.microsoft.com/downloads/) con la versione più recente di Service Pack o [Visual Studio Community](https://visualstudio.microsoft.com/vs/community/), che è possibile scaricare gratuitamente.

- Rumba desktop o un altro emulatore 3270.

## <a name="configure-the-windows-environment"></a>Configurare l'ambiente Windows

Dopo aver installato Enterprise Developer 4,0 nella macchina virtuale, è necessario configurare l'istanza di Enterprise Server fornita con tale macchina virtuale. A tale scopo, è necessario installare alcune funzionalità di Windows aggiuntive, come indicato di seguito.

1. Usare RDP per accedere alla macchina virtuale Enterprise Server 4,0 creata.

2. Fare clic sull'icona **Cerca** accanto al pulsante **Avvia** e digitare **funzionalità di Windows**. Viene visualizzata la Server Manager aggiunta guidata ruoli e funzionalità.

3. Selezionare **ruolo server Web (IIS)**, quindi controllare le opzioni seguenti:

    - Strumenti di gestione Web
    - Compatibilità di gestione con IIS 6 (selezionare tutte le funzionalità disponibili)
    - Console di gestione IIS
    - Strumenti e script di gestione IIS
    - Servizio Gestione IIS

4. Selezionare **servizi World Wide Web** e selezionare le opzioni seguenti:

     Funzionalità di sviluppo di applicazioni:
    - Estendibilità .NET
    - ASP.NET
    - Funzionalità HTTP comuni: aggiungere tutte le funzionalità disponibili
    - Integrità e diagnostica: aggiungere tutte le funzionalità disponibili
    - Sicurezza:
        - Autenticazione di base
        - Autenticazione di Windows

5. Selezionare il **servizio Attivazione processo Windows** e tutti i relativi elementi figlio.

6. Per le **funzionalità** di, controllare **Microsoft .NET Framework 3.5.1** e selezionare le opzioni seguenti:

    - Attivazione HTTP Windows Communication Foundation
    - Windows Communication Foundation attivazione non HTTP

7. Per le **funzionalità** di, controllare **Microsoft .NET Framework 4,6** e selezionare le opzioni seguenti:

   - Attivazione named pipe
   - Attivazione TCP
   - Condivisione porta TCP

     ![Aggiunta guidata ruoli e funzionalità: servizi ruolo](media/01-demo-roles.png)

8. Dopo aver selezionato tutte le opzioni, fare clic su **Avanti** per installare.

9. Dopo le funzionalità di Windows, passare a **Pannello di controllo \> sistema e \> strumenti di amministrazione sicurezza** e selezionare **Servizi**. Scorrere verso il basso e verificare che i servizi seguenti siano in esecuzione e impostati su **automatico**:

    - **NetTcpPortSharing**
    - **Adattatore listener Net.Pipe**
    - **Adattatore listener Net. TCP**

10. Per configurare IIS e il supporto WAS, dal menu individuare **Micro Focus Enterprise prompt dei comandi per gli sviluppatori (64 bit)** ed eseguire come **amministratore**.

11. Digitare **wassetup – i** e premere **invio**.

12. Dopo l'esecuzione dello script, è possibile chiudere la finestra.

## <a name="configure-the-local-system-account-for-sql-server"></a>Configurare l'account di sistema locale per SQL Server

Alcuni processi server aziendali devono essere in grado di accedere SQL Server e creare database e altri oggetti. Questi processi utilizzano l'account di sistema locale, pertanto è necessario assegnare l'autorità sysadmin a tale account.

1. Avviare **SSMS** e fare clic su **Connetti** per connettersi al server SQLExpress locale usando l'autenticazione di Windows. Dovrebbe essere disponibile nell'elenco **nome server** .

2. A sinistra espandere la cartella **sicurezza** e selezionare account di **accesso**.

3. Selezionare **NT Authority \\ System** e selezionare **proprietà**.

4. Selezionare **ruoli server** e controllare **sysadmin**.

     ![Finestra di Esplora oggetti di SSMS: proprietà dell'account di accesso](media/02-demo-explorer.png)

## <a name="create-the-bankdemo-database-and-all-its-objects"></a>Creare il database BankDemo e tutti i relativi oggetti

1. Aprire **Esplora risorse** e passare a **C: \\ utenti \\ public \\ Documents \\ Micro Focus \\ Enterprise Developer \\ Samples \\ mainframe \\ CICS \\ DotNet \\ BankDemo \\ SQL**.

2. Copiare il contenuto del file **BankDemoCreateAll. SQL** negli Appunti.

3. Aprire **SSMS**. A destra fare clic su **Server** e selezionare **nuova query**.

4. Incollare il contenuto degli Appunti nella nuova casella della **query** .

5. Eseguire l'istruzione SQL facendo clic su **Esegui** nella scheda **comando** sopra la query.

La query deve essere eseguita senza errori. Al termine, si disporrà del database di esempio per l'applicazione BankDemo.

![Output di SQLQuery1. SQL](media/03-demo-query.png)

## <a name="verify-that-the-database-tables-and-objects-have-been-created"></a>Verificare che gli oggetti e le tabelle di database siano stati creati

1. Fare clic con il pulsante destro del mouse sul database **BANKDEMO** e selezionare **Aggiorna**.

2. Espandere il **database** e selezionare **tabelle**. Verrà visualizzata una schermata simile alla seguente.

     ![Tabella BANKDEMO espansa in Esplora oggetti](media/04-demo-explorer.png)

## <a name="build-the-application-in-enterprise-developer"></a>Compilare l'applicazione in Enterprise Developer

1. Aprire Visual Studio e accedere.

2. Nell'opzione di menu **file** selezionare **Apri progetto/soluzione**, passare a **C: \\ utenti \\ public \\ Documents \\ Micro Focus \\ Enterprise Developer \\ Samples \\ mainframe \\ CICS \\ DotNet \\ BankDemo** e selezionare il file **sln** .

3. Esaminare gli oggetti. I programmi COBOL sono visualizzati in Esplora soluzioni con l'estensione CBL insieme a quaderni (CPY) e JCL.

4. Fare clic con il pulsante destro del mouse sul progetto **BankDemo2** e selezionare **Imposta come progetto di avvio**.

    > [!NOTE]
    > Il progetto BankDemo USA HCOSS (opzione di compatibilità host per SQL Server), che non viene usato per questa demo.

5. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul progetto **BankDemo2** e selezionare **Compila**.

    > [!NOTE]
    > La compilazione a livello di soluzione genera errori, perché HCOSS non è stato configurato.

6. Quando il progetto viene compilato, esaminare la finestra di **output** . L'aspetto dovrebbe essere simile all'immagine seguente.

     ![Finestra di output che mostra la compilazione riuscita](media/05-demo-output.png)

## <a name="deploy-the-bankdemo-application-into-the-region-database"></a>Distribuire l'applicazione BankDemo nel database Region

1. Aprire un prompt dei comandi di Enterprise Developer (64 bit) come amministratore.

2. Passare a **% public% \\ Documents \\ Micro Focus \\ Enterprise Developer \\ Samples \\ mainframe \\ CICS \\ DotNet \\ BankDemo**.

3. Al prompt dei comandi eseguire **bankdemodbdeploy** e includere il parametro per il database in cui eseguire la distribuzione, ad esempio:

    ```
    bankdemodbdeploy (local)/sqlexpress
    ```

> [!NOTE]
> Assicurarsi di usare una barra (/) e non una barra rovesciata ( \\ ). Questo script viene eseguito per un periodo di tempo.

![Amministrazione: finestra Prompt dei comandi per gli sviluppatori Enterprise](media/06-demo-cmd.png)

## <a name="create-the-bankdemo-region-in-enterprise-administrator-for-net"></a>Creare l'area BankDemo in Enterprise Administrator per .NET

1. Aprire **Enterprise Server per l'interfaccia utente di amministrazione di .NET** .

2. Per avviare lo snap-in MMC, dal menu **Start** di Windows scegliere **Micro Focus Enterprise Developer \> Configuration \> Enterprise Server per l'amministratore di .NET**. Per Windows Server, scegliere **Micro Focus Enterprise Developer \> Enterprise Server per l'amministratore .NET**.

3. Espandere il contenitore **Regions** nel riquadro sinistro, quindi fare clic con il pulsante destro del mouse su **CICS**.

4. Selezionare **define Region** per creare una nuova area CICS denominata **BANKDEMO**, ospitata nel database (locale).

5. Specificare l'istanza del server di database, fare clic su **Avanti**, quindi immettere il nome dell'area **BANKDEMO**.

     ![Finestra di dialogo Definisci area](media/07-demo-cics.png)

6. Per selezionare il file di definizione dell'area per il database tra più aree, individuare **Region \_ bankdemo \_db.config** in **C: \\ Users \\ public \\ Documents \\ Micro Focus \\ Enterprise Developer \\ Samples \\ mainframe \\ CICS \\ DotNet \\ bankdemo**.

     ![Definisci regione-nome area: BANKDEMO](media/08-demo-cics.png)

7. Fare clic su **Fine**.

## <a name="create-xa-resource-definitions"></a>Creare definizioni di risorse XA

1. Nel riquadro sinistro dell'interfaccia utente di **amministrazione di Enterprise Server per .NET** espandere **sistema** e quindi le **definizioni delle risorse XA**. Questa impostazione definisce il modo in cui l'area interagisce con Enterprise Server e i database dell'applicazione.

2. Fare clic con il pulsante destro del mouse su **definizioni di risorse XA** e scegliere **Aggiungi istanza server**.

3. Nella casella di riepilogo a discesa selezionare **istanza del servizio di database**. Sarà il computer locale SQLEXPRESS.

4. Selezionare l'istanza nel contenitore delle **definizioni di risorse XA (MachineName \\ SQLEXPRESS)** e fare clic su **Aggiungi**.

5. Selezionare **database XA Resource Definition** , quindi digitare **BANKDEMO** per **nome** e **area**.

     ![Schermata nuova definizione risorsa XA database](media/09-demo-xa.png)

6. Fare clic sui puntini di sospensione (**..**.) per visualizzare la creazione guidata stringa di connessione. Per **nome server** Digitare **(local) \\ SQLEXPRESS**. Per **accesso** selezionare **autenticazione di Windows**. Per nome database digitare **BANKDEMO**

     ![Schermata Modifica stringa di connessione](media/10-demo-string.png)

7. Testare la connessione.

## <a name="start-the-bankdemo-region"></a>Avviare l'area BANKDEMO

> [!NOTE]
> Il primo passaggio è importante: è necessario impostare l'area per usare la definizione di risorsa XA appena creata.

1. Passare all' **area CICS BANDEMO** nel **contenitore Regions** e quindi selezionare **Edit Region startup file** dal riquadro **Actions** . Scorrere verso il basso fino alle proprietà SQL e immettere **bankdemo** per il **nome della risorsa XA** oppure usare i puntini di sospensione per selezionarlo.

2. Fare clic sull'icona **Salva** per salvare le modifiche.

3. Fare clic con il pulsante destro del mouse su **area CICS BANKDEMO** nel riquadro della **console** e scegliere **Avvia/arresta area**.

4. Nella parte inferiore della casella **Avvia/arresta area** visualizzata nel riquadro centrale selezionare **Avvia**. Dopo alcuni secondi, l'area viene avviata.

     ![Casella di avvio/arresto SQL](media/11-demo-sql.png)

     ![Schermata di BANKDEMO dell'area CICS](media/12-demo-cics.png)

## <a name="create-a-listener"></a>Creazione di un listener

Creare un listener per le sessioni TN3270 che accedono all'applicazione BankDemo.

1. Nel riquadro sinistro espandere **Editor configurazione** e selezionare **listener**.

2. Fare clic sull'icona **Apri file** e selezionare il file **seelistener.exe.config** . Questo file verrà modificato e caricato ogni volta che viene avviato Enterprise Server.

3. Si notino le due aree definite in precedenza (ESDEMO e JCLDEMO).

4. Per creare una nuova area per BANKDEMO, fare clic con il pulsante destro del mouse su **Regions** e scegliere **Aggiungi area**.

5. Selezionare **area BANKDEMO**.

6. Per aggiungere un canale TN3270, fare clic con il pulsante destro del mouse su **BANKDEMO Region** e scegliere **Aggiungi canale**.

7. Per **nome** immettere **TN3270**. Per **porta** immettere **9024**. L'applicazione ESDEMO usa la porta 9230, quindi è necessario usare una porta diversa.

8. Per salvare il file, fare clic sull'icona **Salva** o scegliere **file** \> **Salva**.

9. Per avviare il listener, fare clic sull'icona **Avvia listener** o scegliere **Opzioni** \> **Avvia listener**.

     ![Finestre dell'editor di configurazione del listener](media/13-demo-listener.png)


## <a name="configure-rumba-to-access-the-bankdemo-application"></a>Configurare Rumba per accedere all'applicazione BankDemo

L'ultima cosa da fare è configurare una sessione 3270 usando Rumba, un emulatore 3270. Questo passaggio consente di accedere all'applicazione BankDemo tramite il listener creato.

1. Dal menu **Start** di Windows, avviare Rumba desktop.

2. Nella voce di menu **Connections (connessioni** ) selezionare **TN3270**.

3. Fare clic su **Inserisci** e digitare **127.0.0.1** per l'indirizzo IP e **9024** per la porta definita dall'utente.

4. Nella parte inferiore della finestra di dialogo fare clic su **Connetti**. Viene visualizzata una schermata CICS nera.

5. Digitare **Bank** per visualizzare la schermata iniziale 3270 per l'applicazione BankDemo.

6. Per User ID digitare **B0001** e per la password digitare anything. Viene visualizzata la prima schermata BANK20.

![Visualizzazione del mainframe visualizzazione del mainframe schermata iniziale ](media/14-demo.png)
 ![ -Rumba-schermata di dimostrazione del sottosistema](media/15-demo.png)

Congratulazioni! Viene ora eseguita un'applicazione CICS in Azure usando Micro Focus Enterprise Server.

## <a name="next-steps"></a>Passaggi successivi

- [Eseguire Enterprise Server in contenitori Docker in Azure](run-enterprise-server-container.md)
- [Migrazione del mainframe-portale](/archive/blogs/azurecat/mainframe-migration-to-azure-portal)
- [Macchine virtuali](../../../linux/overview.md)
- [Risoluzione dei problemi](../../../troubleshooting/index.yml)
- [Demistificazione della migrazione da mainframe ad Azure](https://azure.microsoft.com/resources/demystifying-mainframe-to-azure-migration/en-us/)
