---
title: Impostare i Micro messa a fuoco CICS BankDemo per Micro messa a fuoco Enterprise Developer 4.0 in macchine virtuali di Azure
description: Eseguire l'applicazione di Micro messa a fuoco BankDemo in macchine virtuali (VM) per informazioni su come usare Micro Focus Enterprise Server e Enterprise Developer.
author: sread
ms.author: sread
ms.date: 04/02/2019
ms.topic: article
ms.service: multiple
ms.openlocfilehash: 4491fc137c2c85e2be605f5e58fde6fd422efbbe
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/07/2019
ms.locfileid: "67621344"
---
# <a name="set-up-micro-focus-cics-bankdemo-for-micro-focus-enterprise-developer-40-on-azure"></a>Impostare i Micro messa a fuoco CICS BankDemo per Micro messa a fuoco Enterprise Developer 4.0 in Azure

Quando si configura Micro messa a fuoco Enterprise Server 4.0 e 4.0 per gli sviluppatori Enterprise in Azure, è possibile testare le distribuzioni dei carichi di lavoro di IBM z/OS. Questo articolo illustra come configurare BankDemo CICS, un'applicazione di esempio fornito con gli sviluppatori aziendali.

CICs è l'acronimo di sistema di controllo di informazioni dei clienti, la piattaforma di transazione utilizzata da molte delle applicazioni mainframe in linea. L'applicazione BankDemo è molto utile per apprendere il funzionamento Enterprise Server e gli sviluppatori aziendali e su come gestire e distribuire un'applicazione reale comprensivi di terminali schermo verde.

## <a name="prerequisites"></a>Prerequisiti

- Una macchina virtuale con [sviluppatore aziendale](set-up-micro-focus-azure.md). Tenere presente che gli sviluppatori aziendali dispone di un'istanza completa del Server Enterprise su di esso per scopi di sviluppo e test. Questa istanza è l'istanza del Server dell'organizzazione usato per la demo.

- [Edizione di SQL Server 2017 Express](https://www.microsoft.com/sql-server/sql-server-editions-express). Scaricarlo e installarlo nella macchina virtuale per gli sviluppatori dell'organizzazione. Server Enterprise richiede un database per la gestione delle aree CICS e l'applicazione BankDemo Usa anche un database di SQL Server denominato BANKDEMO. In questa demo presuppone il che uso di SQL Server Express per entrambi i database. Durante l'installazione, selezionare l'installazione di base.

- [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017) (SSMS). SQL Server Management Studio viene utilizzato per la gestione dei database ed eseguendo uno script T-SQL. Scaricarlo e installarlo nella macchina virtuale per gli sviluppatori dell'organizzazione.

- [Visual Studio 2019](https://azure.microsoft.com/downloads/) con il service pack più recente oppure [Visual Studio Community](https://visualstudio.microsoft.com/vs/community/), che è possibile scaricare gratuitamente.

- Rumba Desktop o in un altro emulatore 3270.

## <a name="configure-the-windows-environment"></a>Configurare l'ambiente di Windows

Dopo l'installazione di Enterprise Developer 4.0 nella macchina virtuale, è necessario configurare l'istanza del Server Enterprise fornita con il prodotto. A tale scopo, è necessario installare alcune funzionalità aggiuntive di Windows come indicato di seguito.

1. Usare RDP per accedere al Server 4.0 Enterprise VM creata.

2. Fare clic sui **ricerca** icona accanto al **avviare** pulsante e il tipo **funzionalità Windows**. Verrà aperto Server Manager Aggiunta guidata ruoli e funzionalità.

3. Selezionare **ruolo Server Web (IIS)** e quindi selezionare le opzioni seguenti:

    - Strumenti di gestione Web
    - Compatibilità Gestione IIS 6 (selezionare tutte le funzionalità disponibili)
    - Console di gestione IIS
    - Strumenti e script di gestione IIS
    - Servizio di gestione IIS

4. Selezionare **servizi World Wide Web**e selezionare le opzioni seguenti:

     Funzionalità per lo sviluppo dell'applicazione:
    - Estendibilità .NET
    - ASP.NET
    - Funzionalità HTTP comuni: Aggiungere tutte le funzionalità disponibili
    - Integrità e diagnostica: Aggiungere tutte le funzionalità disponibili
    - Sicurezza:
        - Autenticazione di base
        - Autenticazione di Windows

5. Selezionare **servizio Attivazione processo Windows** e tutti i relativi elementi figlio.

6. Per la **caratteristiche**, controllare **Microsoft .NET framework 3.5.1**e selezionare le opzioni seguenti:

    - Attivazione di Windows Communication Foundation HTTP
    - Windows Communication Foundation Non-HTTP Activation

7. Per la **caratteristiche**, controllare **Microsoft .NET framework 4.6**e selezionare le opzioni seguenti:

   - Attivazione Named Pipe
   - Attivazione TCP
   - Condivisione delle porte TCP

     ![Aggiunta guidata ruoli e funzionalità: Servizi ruolo](media/01-demo-roles.png)

8. Dopo aver selezionato tutte le opzioni, fare clic su **successivo** per l'installazione.

9. Dopo le funzionalità di Windows, passare a **Pannello di controllo \> sistema e sicurezza \> strumenti di amministrazione**e selezionare **Services**. Scorrere verso il basso e assicurarsi che i servizi seguenti sono in esecuzione e impostano su **automatica**:

    - **NetTcpPortSharing**
    - **Adattatore Listener NET. pipe**
    - **Net.tcp Listener Adapter**

10. Per configurare il supporto IIS e WAS, dal menu di scelta individuare **Micro messa a fuoco Enterprise prompt dei comandi sviluppatore (64 bit)** ed eseguiti come **amministratore**.

11. Tipo di **wassetup – i** , quindi premere **invio**.

12. Dopo l'esecuzione dello script, è possibile chiudere la finestra.

## <a name="configure-the-local-system-account-for-sql-server"></a>Configurare l'account sistema locale per SQL Server

Alcuni processi Enterprise Server devono essere in grado di accedere in SQL Server e creare database e altri oggetti. Questi processi utilizzano l'account sistema locale, pertanto è necessario specificare autorità sysadmin a tale account.

1. Avviare il **SSMS** e fare clic su **Connect** per connettersi al Server SQLEXPRESS locale utilizzando l'autenticazione di Windows. Dovrebbe essere disponibile nel **nome Server** elenco.

2. A sinistra, espandere la **sicurezza** cartella e selezionare **accessi**.

3. Selezionare **NT AUTHORITY\\SYSTEM** e selezionare **proprietà**.

4. Selezionare **ruoli predefiniti del Server** e controllare **sysadmin**.

     ![Finestra di Esplora oggetti di SSMS: Proprietà account di accesso](media/02-demo-explorer.png)

## <a name="create-the-bankdemo-database-and-all-its-objects"></a>Creare il database BankDemo e tutti i relativi oggetti

1. Aprire **Windows Explorer** e passare alla **c:\\utenti\\pubblica\\documenti\\Micro Focus\\Enterprise Developer\\ Campioni\\Mainframe\\CICS\\DotNet\\BankDemo\\SQL**.

2. Copiare il contenuto della **BankDemoCreateAll.SQL** file negli Appunti.

3. Aprire **SSMS**. A destra, fare clic su **Server** e selezionare **nuova Query**.

4. Incollare il contenuto degli Appunti nel **nuova Query** casella.

5. Eseguire il codice SQL facendo **Execute** dalle **comando** scheda sopra la query.

La query dovrebbe eseguita senza errori. Al termine, si dispone di database di esempio per l'applicazione BankDemo.

![SQLQuery1.sql output](media/03-demo-query.png)

## <a name="verify-that-the-database-tables-and-objects-have-been-created"></a>Verificare che siano stati creati gli oggetti e tabelle di database

1. Fare doppio clic il **BANKDEMO** del database e selezionare **aggiornare**.

2. Espandere la **Database** e selezionare **tabelle**. Dovrebbe essere simile al seguente.

     ![Tabella BANKDEMO espansa in Esplora oggetti](media/04-demo-explorer.png)

## <a name="build-the-application-in-enterprise-developer"></a>Compilare l'applicazione in Enterprise Developer

1. Aprire Visual Studio e accedere.

2. Sotto il **File** opzione di menu, selezionare **Apri progetto/soluzione**, passare a **c:\\utenti\\pubbliche\\documenti\\Micro Messa a fuoco\\Developer Enterprise\\campioni\\Mainframe\\CICS\\DotNet\\BankDemo**e selezionare il **sln**file.

3. Richiedere del tempo per esaminare gli oggetti. Programmi COBOL vengono visualizzati in Esplora soluzioni con l'estensione CBL insieme copybook (CPY) e JCL.

4. Fare doppio clic il **BankDemo2** progetto e selezionare **imposta come progetto di avvio**.

    > [!NOTE]
    > Il progetto BankDemo viene utilizzato HCOSS (opzione di compatibilità Host per SQL Server,) che non viene usato per questa demo.

5. Nella **Esplora soluzioni**, fare doppio clic il **BankDemo2** progetto e selezionare **compilare**.

    > [!NOTE]
    > Compilazione a livello di soluzione comporta errori, come HCOSS non è stato configurato.

6. Quando viene compilato il progetto, esaminare i **Output** finestra. Dovrebbe essere simile all'immagine seguente.

     ![Finestra di output con completamento della compilazione](media/05-demo-output.png)

## <a name="deploy-the-bankdemo-application-into-the-region-database"></a>Distribuire l'applicazione BankDemo nel database di area

1. Aprire un prompt dei comandi di Enterprise Developer (64 bit) come amministratore.

2. Passare al **pubblica %\\documenti\\Micro Focus\\Enterprise Developer\\samples\\Mainframe\\CICS\\DotNet\\ BankDemo**.

3. Al prompt dei comandi, eseguire **bankdemodbdeploy** e includere il parametro per il database da distribuire, ad esempio:

    ```
    bankdemodbdeploy (local)/sqlexpress
    ```

> [!NOTE]
> Assicurarsi di usare una barra (/) non una barra rovesciata (\\). Questo script viene eseguito per un periodo di tempo.

![Amministrazione: Finestra del prompt dei comandi di Enterprise Developer](media/06-demo-cmd.png)

## <a name="create-the-bankdemo-region-in-enterprise-administrator-for-net"></a>Creazione dell'area BankDemo in amministratore dell'organizzazione per .NET

1. Aprire il **Enterprise Server per l'amministrazione .NET** dell'interfaccia utente.

2. Per avviare lo snap-in MMC, dalla finestra di Windows **avviare** menu, scegliere **Micro messa a fuoco Enterprise Developer \> Configuration \> Enterprise Server per l'amministrazione .NET**. (Per Windows Server, scegliere **Micro messa a fuoco Enterprise Developer \> Enterprise Server per l'amministrazione .NET**).

3. Espandere la **aree** contenitore nel riquadro a sinistra e quindi scelta **CICS**.

4. Selezionare **regione definiscono** per creare una nuova area CICS chiamato **BANKDEMO**, ospitato nel database (locale).

5. Specificare l'istanza del server di database, fare clic su **successivo**, quindi immettere il nome dell'area **BANKDEMO**.

     ![Definire una finestra di dialogo di area](media/07-demo-cics.png)

6. Per selezionare il file di definizione di area per il database tra più aree, individuare **regione\_bankdemo\_db.config** nel **c:\\utenti\\pubblico\\ I documenti\\Micro Focus\\Enterprise Developer\\esempi\\Mainframe\\CICS\\DotNet\\BankDemo**.

     ![Definire l'area: nome dell'area: BANKDEMO](media/08-demo-cics.png)

7. Scegliere **Fine**.

## <a name="create-xa-resource-definitions"></a>Creare le definizioni delle risorse XA

1. Nel riquadro sinistro della finestra di **Enterprise Server per l'amministrazione di .NET** dell'interfaccia utente, espandere **System**e quindi **le definizioni delle risorse XA**. Questa impostazione definisce il modo in cui l'area interagisce con Enterprise Server e i database dell'applicazione.

2. Fare clic su **definizioni delle risorse XA** e selezionare **istanza del Server di aggiungere**.

3. Nella casella di riepilogo a discesa, selezionare **istanza del servizio Database**. Il computer locale SQL Express sarà.

4. Selezionare l'istanza sotto il **definizioni delle risorse XA (machinename\\sqlexpress)** contenitore e fare clic su **Add**.

5. Selezionare **definizione di risorsa di Database XA** e quindi digitare **BANKDEMO** per il **Name** e **area**.

     ![Nuova schermata di definizione di risorsa XA Database](media/09-demo-xa.png)

6. Fare clic sui puntini di sospensione ( **...** ) per visualizzare la procedura guidata di stringa di connessione. Per la **nome Server**, digitare **(locale)\\SQLEXPRESS**. Per la **Logon**, selezionare **l'autenticazione di Windows**. Per nome del database, digitare **BANKDEMO**

     ![Schermata di stringa di connessione di modifica](media/10-demo-string.png)

7. Testare la connessione.

## <a name="start-the-bankdemo-region"></a>Avviare l'area BANKDEMO

> [!NOTE]
> Il primo passaggio è importante: È necessario impostare l'area da usare la definizione di risorsa XA appena creato.

1. Passare al **BANDEMO CICS area** sotto il **contenitore aree**e quindi selezionare **modifica File di avvio di area** dal **azioni** riquadro. Scorrere verso il basso le proprietà di SQL e immettere **bankdemo** per il **il nome di risorsa XA**, oppure usare i puntini di sospensione per selezionarlo.

2. Scegliere il **salvare** icona per salvare le modifiche.

3. Fare doppio clic su **BANKDEMO CICS regione** nel **Console** riquadro e selezionare **area avvio/arresto**.

4. Nella parte inferiore della **area di avvio/arresto** casella visualizzata nel riquadro centrale, seleziona **avviare**. Dopo alcuni secondi, viene avviata l'area.

     ![Finestra di avvio/arresto di SQL](media/11-demo-sql.png)

     ![Area CICS BANKDEMO - schermata introduttiva](media/12-demo-cics.png)

## <a name="create-a-listener"></a>Creare un listener

Creare un listener per le sessioni che accedono all'applicazione BankDemo TN3270.

1. Nel riquadro sinistro, espandere **editor di configurazione** e selezionare **Listener**.

2. Fare clic sui **Apri File** icona e selezionare il **seelistener.exe.config** file. Questo file verrà modificato e viene caricato ogni volta che viene avviato Server dell'organizzazione.

3. Si noti che le due aree definite in precedenza (ESDEMO e JCLDEMO).

4. Per creare una nuova area per BANKDEMO, fare doppio clic su **aree**e selezionare **Aggiungi area**.

5. Selezionare **BANKDEMO area**.

6. Aggiungere un canale TN3270 facendo **regione BANKDEMO** e selezionando **Add Channel**.

7. Per la **Name**, immettere **TN3270**. Per la **porta**, immettere **9024**. L'applicazione ESDEMO Usa porta 9230, pertanto è necessario usare una porta diversa.

8. Per salvare il file, scegliere il **salvare** icona oppure scegliere **File** \> **Salva**.

9. Per avviare il listener, fare clic sui **avviare Listener** icona oppure scegliere **opzioni** \> **Listener avviare**.

     ![Finestre dell'Editor di configurazione del listener](media/13-demo-listener.png)


## <a name="configure-rumba-to-access-the-bankdemo-application"></a>Configurare Rumba per accedere all'applicazione BankDemo

L'ultimo aspetto da eseguire viene configurata in una sessione di 3270 tramite Rumba, un emulatore 3270. Questo passaggio consente di accedere all'applicazione BankDemo tramite il listener che è stato creato.

1. Dalla finestra di Windows **avviare** menu, avviare Rumba Desktop.

2. Sotto il **connessioni** voce di menu, selezionare **TN3270**.

3. Fare clic su **inserire** e digitare **127.0.0.1** per l'indirizzo IP e **9024** per la porta definita dall'utente.

4. Nella parte inferiore della finestra di dialogo, fare clic su **Connect**. Viene visualizzata una schermata nera CICS.

5. Tipo di **bank** per visualizzare la schermata iniziale 3270 di applicazione BankDemo.

6. Per l'ID utente, digitare **B0001** e la password, digitare un testo qualsiasi. Verrà visualizzata la prima schermata BANK20.

![Schermata di visualizzazione iniziale mainframe](media/14-demo.png)
![schermata visualizzazione Mainframe - Rumba - sottosistema dimostrazione](media/15-demo.png)

Congratulazioni! Ora eseguono un'applicazione CICS in Azure usando Micro Focus Enterprise Server.

## <a name="next-steps"></a>Passaggi successivi

- [Eseguire Server Enterprise in contenitori Docker in Azure](run-enterprise-server-container.md)
- [Migrazione del mainframe - portale](https://blogs.msdn.microsoft.com/azurecat/2018/11/16/mainframe-migration-to-azure-portal/)
- [Macchine virtuali](https://docs.microsoft.com/azure/virtual-machines/linux/overview)
- [Risoluzione dei problemi](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/)
- [Demistificazione di mainframe alla migrazione ad Azure](https://azure.microsoft.com/resources/demystifying-mainframe-to-azure-migration/en-us/)
