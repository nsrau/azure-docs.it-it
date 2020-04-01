---
title: Configurare Micro Focus CICS BankDemo per Micro Focus Enterprise Developer 4.0 in Macchine virtuali di Azure
description: Eseguire l'applicazione Micro Focus BankDemo in Macchine virtuali di Azure per imparare a usare Micro Focus Enterprise Server e Enterprise Developer.
author: sread
ms.author: sread
ms.date: 03/30/2020
ms.topic: article
ms.service: multiple
ms.openlocfilehash: db9d6bab2f383102434512aa63d7566cff1f579b
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/31/2020
ms.locfileid: "80411081"
---
# <a name="set-up-micro-focus-cics-bankdemo-for-micro-focus-enterprise-developer-40-on-azure"></a>Configurare Micro Focus CICS BankDemo per Micro Focus Enterprise Developer 4.0 in AzureSet up Micro Focus CICS BankDemo for Micro Focus Enterprise Developer 4.0 on Azure

Quando si configura Micro Focus Enterprise Server 4.0 e Enterprise Developer 4.0 in Azure, è possibile testare le distribuzioni dei carichi di lavoro IBM z/OS. In questo articolo viene illustrato come configurare CICS BankDemo, un'applicazione di esempio fornita con Enterprise Developer.

CICs è l'acronimo di Customer Information Control System, la piattaforma di transazione utilizzata da molte delle applicazioni mainframe online. L'applicazione BankDemo è ideale per imparare come operano Enterprise Server ed Enterprise Developer e come gestire e distribuire un'applicazione effettiva completa di terminali a schermo verde.

> [!NOTE]
> Prossimamente: istruzioni per la configurazione di [Micro Focus Enterprise Server 5.0](https://techcommunity.microsoft.com/t5/azurecat/micro-focus-enterprise-server-5-0-quick-start-template-on-azure/ba-p/1160110) nelle macchine virtuali di Azure.Coming soon: Instructions for setting up Micro Focus Enterprise Server 5.0 on Azure VMs.

## <a name="prerequisites"></a>Prerequisiti

- Una macchina virtuale con [Enterprise Developer](set-up-micro-focus-azure.md). Tenere presente che Enterprise Developer dispone di un'istanza completa di Enterprise Server su di esso a scopo di sviluppo e test. Questa istanza è l'istanza di Enterprise Server utilizzata per la demo.

- [SQL Server 2017 Express Edition](https://www.microsoft.com/sql-server/sql-server-editions-express). Scaricarlo e installarlo nella macchina virtuale Enterprise Developer. Enterprise Server richiede un database per la gestione delle aree CICS e l'applicazione BankDemo utilizza anche un database di SQL Server denominato BANKDEMO. In questa demo si presuppone l'utilizzo di SQL Server Express per entrambi i database. Durante l'installazione, selezionare l'installazione di base.

- [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017) (SSMS). SSMS viene utilizzato per la gestione dei database e l'esecuzione di uno script T-SQL. Scaricarlo e installarlo nella macchina virtuale Enterprise Developer.

- [Visual Studio 2019](https://azure.microsoft.com/downloads/) con il service pack più recente o [Visual Studio Community](https://visualstudio.microsoft.com/vs/community/), che è possibile scaricare gratuitamente.

- Rumba Desktop o un altro emulatore 3270.

## <a name="configure-the-windows-environment"></a>Configurare l'ambiente Windows

Dopo aver installato Enterprise Developer 4.0 nella macchina virtuale, è necessario configurare l'istanza di Enterprise Server fornita con essa. Per farlo, è necessario installare alcune funzionalità aggiuntive di Windows come segue.

1. Utilizzare RDP per accedere alla macchina virtuale Enterprise Server 4.0 creata.

2. Fare clic sull'icona **Cerca** accanto al pulsante **Start** e digitare **Funzionalità di Windows**. Verrà visualizzata l'Aggiunta guidata ruoli e funzionalità di Server Manager.

3. Selezionare **Ruolo server Web (IIS)**, quindi selezionare le opzioni seguenti:

    - Strumenti di gestione Web
    - Compatibilità di gestione con IIS 6 (selezionare tutte le funzionalità disponibili)
    - Console di gestione IIS
    - Strumenti e script di gestione IIS
    - Servizio di gestione IIS

4. Selezionare **Servizi sul World Wide Web**e selezionare le opzioni seguenti:

     Funzionalità di sviluppo delle applicazioni:Application Development Features:
    - Estendibilità .NET
    - ASP.NET
    - Funzionalità HTTP comuni: aggiungere tutte le funzionalità disponibiliCommon HTTP Features: Add all available features
    - Integrità e diagnostica: aggiungere tutte le funzionalità disponibili
    - Sicurezza:
        - Autenticazione di base
        - Autenticazione di Windows

5. Selezionare **Servizio Attivazione processo Windows** e tutti i relativi elementi figlio.

6. Per **Caratteristiche**, controllare **Microsoft .NET Framework 3.5.1**e selezionare le opzioni seguenti:

    - Attivazione HTTP di Windows Communication Foundation
    - Attivazione non HTTP di Windows Communication Foundation

7. Per **Funzionalità**, selezionare **Microsoft .NET Framework 4.6**e selezionare le opzioni seguenti:

   - Attivazione named pipe
   - Attivazione TCP
   - Condivisione porta TCP

     ![Aggiunta guidata ruoli e funzionalità: Servizi ruolo](media/01-demo-roles.png)

8. Dopo aver selezionato tutte le opzioni, fare clic su **Avanti** per eseguire l'installazione.

9. Dopo le funzionalità di Windows, passare a Strumenti di amministrazione di **sistema e protezione \> \> **del Pannello di controllo e selezionare **Servizi**. Scorrere verso il basso e assicurarsi che i seguenti servizi siano in esecuzione e impostati su **Automatico:**

    - **NetTcpPortSharing**
    - **Adattatore listener Net.Pipe**
    - **Adattatore listener Net.tcp**

10. Per configurare il supporto di IIS e WAS, dal menu individuare **Micro Focus Enterprise Developer Command Prompt (64 bit)** ed eseguito come **amministratore**.

11. Digitare **wassetup –i** e premere **Invio**.

12. Dopo l'esecuzione dello script, è possibile chiudere la finestra.

## <a name="configure-the-local-system-account-for-sql-server"></a>Configurare l'account di sistema locale per SQL ServerConfigure the local system account for SQL Server

Alcuni processi di Enterprise Server devono essere in grado di accedere a SQL Server e creare database e altri oggetti. Questi processi utilizzano l'account di sistema locale, pertanto è necessario assegnare l'autorizzazione sysadmin a tale account.

1. Avviare **SSMS** e fare clic su **Connetti** per connettersi al server SQLEXPRESS locale utilizzando l'autenticazione di Windows. Dovrebbe essere disponibile nell'elenco **Nome server.**

2. A sinistra, espandere la cartella **Protezione** e selezionare **Account di accesso**.

3. Selezionare **NT AUTHORITY SYSTEM (SISTEMA NT AUTHORITY)\\** e scegliere Properties **(Proprietà).**

4. Selezionare **Ruoli del server** e controllare **sysadmin**.

     ![Finestra Esplora oggetti SSMS: Proprietà di accesso](media/02-demo-explorer.png)

## <a name="create-the-bankdemo-database-and-all-its-objects"></a>Creare il database BankDemo e tutti i relativi oggetti

1. Aprire **Esplora risorse** e passare a **C:\\\\Documenti\\\\\\\\pubblici\\per gli\\utenti\\\\Micro\\Focus Enterprise Developer Samples Mainframe CICS DotNet BankDemo SQL**.

2. Copiare il contenuto del file **BankDemoCreateAll.SQL** negli Appunti.

3. Aprire **SSMS**. A destra, fare clic su **Server** e selezionare **Nuova query**.

4. Incollare il contenuto degli Appunti nella casella **Nuova query.**

5. Eseguire il codice SQL facendo clic su **Esegui** dalla scheda **Comando** sopra la query.

La query deve essere eseguita senza errori. Al termine, si dispone del database di esempio per l'applicazione BankDemo.

![Output di SQLQuery1.sql](media/03-demo-query.png)

## <a name="verify-that-the-database-tables-and-objects-have-been-created"></a>Verificare che le tabelle e gli oggetti di database siano stati creati

1. Fare clic con il pulsante destro del mouse sul database **BANKDEMO** e scegliere **Aggiorna**.

2. Espandere il **Database** e selezionare **Tabelle**. Si dovrebbe vedere qualcosa di simile al seguente.

     ![Tabella BANKDEMO espansa in Esplora oggetti](media/04-demo-explorer.png)

## <a name="build-the-application-in-enterprise-developer"></a>Creare l'applicazione in Enterprise Developer

1. Aprire Visual Studio e accedere.

2. Nell'opzione di menu **File** selezionare **Apri progetto/soluzione**, passare a **C:\\\\Documenti pubblici\\\\Micro\\Focus Enterprise Developer\\Samples\\Mainframe\\CICS\\DotNet\\BankDemo**e selezionare il file **sln.**

3. Prenditi un po' di tempo per esaminare gli oggetti. I programmi COBOL sono mostrati in Esplora soluzioni con l'estensione CBL insieme a CopyBooks (CPY) e JCL.

4. Fare clic con il pulsante destro del mouse sul progetto **BankDemo2** e scegliere Imposta come progetto di **avvio**.

    > [!NOTE]
    > Il progetto BankDemo utilizza HCOSS (Host Compatibility Option for SQL Server), che non viene utilizzato per questa demo.

5. In **Esplora soluzioni**fare clic con il pulsante destro del mouse sul progetto **BankDemo2** e **scegliere Compila**.

    > [!NOTE]
    > La compilazione a livello di soluzione genera errori, poiché HCOSS non è stato configurato.

6. Quando il progetto viene compilato, esaminare il **Output** finestra. L'aspetto dovrebbe essere simile a quanto riportato di seguito.

     ![Finestra di output che mostra la compilazione riuscitaOutput window showing successful build](media/05-demo-output.png)

## <a name="deploy-the-bankdemo-application-into-the-region-database"></a>Distribuire l'applicazione BankDemo nel database dell'area

1. Aprire un prompt dei comandi di Enterprise Developer (64 bit) come amministratore.

2. Passare **all'esempio\\\\\\\\%PUBLIC%\\Documents Micro\\Focus Enterprise Developer samples Mainframe CICS\\\\DotNet BankDemo**.

3. Al prompt dei comandi, eseguire **bankdemodbdeploy** e includere il parametro per il database in cui eseguire la distribuzione, ad esempio:

    ```
    bankdemodbdeploy (local)/sqlexpress
    ```

> [!NOTE]
> Assicurarsi di utilizzare una barra (/)\\non una barra rovesciata( ). Questo script viene eseguito per un po'.

![Amministrazione: finestra del prompt dei comandi di Enterprise Developer](media/06-demo-cmd.png)

## <a name="create-the-bankdemo-region-in-enterprise-administrator-for-net"></a>Creare l'area BankDemo in Enterprise Administrator per .NET

1. Aprire l'interfaccia utente di amministrazione di **Enterprise Server per .NET.**

2. Per avviare lo snap-in MMC, scegliere **Micro Focus \> \> Enterprise Developer Configuration Enterprise Server per .NET Admin**dal menu **Start** di Windows. Per Windows Server, scegliere **Micro \> Focus Enterprise Developer Enterprise Server per .NET Admin**.

3. Espandere il contenitore **Regioni** nel riquadro sinistro, quindi fare clic con il pulsante destro del mouse su **CICS**.

4. Selezionare **Definisci area** per creare una nuova area CICS denominata **BANKDEMO**, ospitata nel database (locale).

5. Specificare l'istanza del server di database, fare clic su **Avanti**, quindi immettere il nome dell'area **BANKDEMO**.

     ![Finestra di dialogo Definisci regione](media/07-demo-cics.png)

6. Per selezionare il file di definizione delle regioni per il database tra aree, individuare **l'area\_\_bankdemo db.config** in **C:\\Users\\Public\\Documents\\Micro Focus\\Enterprise Developer\\Samples\\Mainframe\\CICS\\DotNet\\BankDemo**.

     ![Definisci regione - Nome regione: BANKDEMO](media/08-demo-cics.png)

7. Fare clic su **Fine**.

## <a name="create-xa-resource-definitions"></a>Creare definizioni di risorse XACreate XA Resource Definitions

1. Nel riquadro sinistro dell'interfaccia utente **Amministrazione di Enterprise Server for .NET** espandere **System**e quindi **XA Resource Definitions**. Questa impostazione definisce il modo in cui l'area interagisce con Enterprise Server e i database dell'applicazione.

2. Fare clic con il pulsante destro del mouse su **Definizioni risorse XA** e selezionare **Aggiungi istanza server**.

3. Nella casella di riepilogo a discesa selezionare **Istanza servizio database**. Sarà il computer locale SQLEXPRESS.

4. Selezionare l'istanza dal contenitore **Definizioni risorse\\XA (nomecomputer sqlexpress)** e fare clic su **Aggiungi**.

5. Selezionare **Database XA Resource Definition (Definizione risorsa XA del database),** quindi digitare **BANKDEMO** **(Nome** e **Regione).**

     ![Schermata Nuova definizione risorsa XA del database](media/09-demo-xa.png)

6. Fare clic sui puntini di sospensione (**...**) per visualizzare la procedura guidata Stringa di connessione. Per **Nome server**, digitare **\\(locale) SQLEXPRESS**. Per **Accesso**, selezionare **Autenticazione di Windows**. Per il nome del database, digitare **BANKDEMO**

     ![Schermata Modifica stringa di connessione](media/10-demo-string.png)

7. Verificare la connessione.

## <a name="start-the-bankdemo-region"></a>Avviare la regione BANKDEMO

> [!NOTE]
> Il primo passaggio è importante: è necessario impostare l'area per l'utilizzo della definizione di risorsa XA appena creata.

1. Passare **all'area BANDEMO CICS** in **Contenitore regioni**, quindi selezionare Modifica file di **avvio area** dal riquadro **Azioni** . Scorrere verso il basso fino ché le proprietà SQL e immettere **bankdemo** come nome di **risorsa XA**oppure utilizzare i lipsia per selezionarla.

2. Fare clic sull'icona **Salva** per salvare le modifiche.

3. Fare clic con il pulsante destro del mouse su **Area CICS BANKDEMO** nel riquadro **della console** e **scegliere Avvia/Arresta area**.

4. Nella parte inferiore della casella **Area di avvio/arresto** visualizzata nel riquadro centrale selezionare **Inizio**. Dopo alcuni secondi, la regione inizia.

     ![Casella Avvio/Arresto SQL](media/11-demo-sql.png)

     ![CICS Region BANKDEMO - Schermata iniziale](media/12-demo-cics.png)

## <a name="create-a-listener"></a>Creare un listenerCreate a listener

Creare un listener per le sessioni TN3270 che accedono all'applicazione BankDemo.

1. Nel riquadro sinistro espandere **Editor di configurazione** e selezionare **Listener**.

2. Fare clic sull'icona **Apri file** e selezionare il file **seelistener.exe.config.** Questo file verrà modificato e caricato a ogni avvio di Enterprise Server.

3. Si notino le due regioni definite in precedenza (ESDEMO e JCLDEMO).

4. Per creare una nuova regione per BANKDEMO, fare clic con il pulsante destro del mouse su **Regioni**e selezionare **Aggiungi regione**.

5. Selezionare **Regione BANKDEMO**.

6. Aggiungere un canale TN3270 facendo clic con il pulsante destro del mouse su **Area BANKDEMO** e selezionando **Aggiungi canale**.

7. In **Nome**, immettere **TN3270**. In **Porta**immettere **9024**. L'applicazione ESDEMO utilizza la porta 9230, pertanto è necessario utilizzare una porta diversa.

8. Per salvare il file, fate clic sull'icona **Salva** o scegliete **Salva** **file** \> .

9. Per avviare il listener, fate clic sull'icona **Avvia listener** o scegliete **Opzioni avvia** \> **listener**.

     ![Finestre dell'editor di configurazione del listener](media/13-demo-listener.png)


## <a name="configure-rumba-to-access-the-bankdemo-application"></a>Configurare Rumba per accedere all'applicazione BankDemo

L'ultima cosa che devi fare è configurare una sessione 3270 usando Rumba, un emulatore 3270. Questo passaggio consente di accedere all'applicazione BankDemo tramite il listener creato.

1. Dal menu **Start** di Windows, avviare Rumba Desktop.

2. Nella voce di menu **Connessioni** selezionare **TN3270**.

3. Fare clic su **Inserisci** e digitare **127.0.0.1** per l'indirizzo IP e **9024** per la porta definita dall'utente.

4. Nella parte inferiore della finestra di dialogo fare clic su **Connetti**. Viene visualizzata una schermata CICS nera.

5. Digitare **bank** per visualizzare la schermata 3270 iniziale per l'applicazione BankDemo.

6. Per ID utente, digitare **B0001** e per la password, digitare qualsiasi cosa. Si apre la prima schermata BANK20.

![Visualizzazione mainframe](media/14-demo.png)
![Schermata di benvenuto Visualizzazione mainframe Display - Rumba - Schermata dimostrazione sottosistema](media/15-demo.png)

Congratulazioni! A questo punto si esegue un'applicazione CICS in Azure usando Micro Focus Enterprise Server.

## <a name="next-steps"></a>Passaggi successivi

- [Eseguire Enterprise Server nei contenitori Docker in AzureRun Enterprise Server in Docker containers on Azure](run-enterprise-server-container.md)
- [Migrazione mainframe - Portale](https://blogs.msdn.microsoft.com/azurecat/2018/11/16/mainframe-migration-to-azure-portal/)
- [Macchine virtuali](https://docs.microsoft.com/azure/virtual-machines/linux/overview)
- [Risoluzione dei problemi](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/)
- [Demistificazione della migrazione da mainframe ad AzureDemistifying mainframe to Azure migration](https://azure.microsoft.com/resources/demystifying-mainframe-to-azure-migration/en-us/)
