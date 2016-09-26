<properties 
    pageTitle=" Integrazione del controllo del codice sorgente in Automazione di Azure | Microsoft Azure"
    description="Questo articolo descrive l'integrazione del controllo del codice sorgente con GitHub in Automazione di Azure."
    services="automation"
    documentationCenter=""
    authors="mgoedtel"
    manager="jwhit"
    editor="tysonn" />    
<tags 
    ms.service="automation"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="infrastructure-services"
    ms.date="09/12/2016"
    ms.author="magoedte;sngun" />

# Integrazione del controllo del codice sorgente in Automazione di Azure

L'integrazione del controllo del codice sorgente consente di associare runbook nell'account di automazione a un repository di controllo del codice sorgente su GitHub. Il controllo del codice sorgente fornisce un ambiente in cui è possibile collaborare con il team, tenere traccia delle modifiche ed eseguire il rollback a versioni precedenti dei runbook. Ad esempio, il controllo del codice sorgente consente di sincronizzare diversi rami nel controllo del codice sorgente con gli account di automazione di sviluppo, test o produzione e alzare di livello il codice testato nell'ambiente di sviluppo per passarlo all'ambiente di produzione.

Il controllo del codice sorgente consente di effettuare il push del codice da Automazione di Azure al controllo del codice sorgente oppure effettuare il pull dei runbook dal controllo del codice sorgente ad Automazione di Azure. Questo articolo descrive come configurare il controllo del codice sorgente nell'ambiente di Automazione di Azure. Si inizierà configurando l'automazione di Azure per accedere all'archivio GitHub e si eseguiranno i passaggi di diverse operazioni che possono essere eseguite utilizzando l'integrazione del controllo del codice sorgente.


>[AZURE.NOTE] Il controllo del codice sorgente supporta il pull e il push di [Runbook del flusso di lavoro PowerShell](automation-runbook-types.md#powershell-workflow-runbooks) e [Runbook di PowerShell](automation-runbook-types.md#powershell-runbooks). I [Runbook grafici](automation-runbook-types.md#graphical-runbooks) non sono ancora supportati.<br><br>


Per configurare il controllo del codice sorgente per l'account di automazione sono richiesti due semplici passaggi e solo uno se si ha già un account GitHub. Vale a dire:
## Passaggio 1: Creare un repository GitHub

Se si ha già un account GitHub e un repository che si vuole collegare ad Automazione di Azure, accedere con l'account esistente e iniziare dal passaggio 2 seguente. In caso contrario, passare a [GitHub](https://github.com/), iscriversi per ottenere un nuovo account e [creare un nuovo repository](https://help.github.com/articles/create-a-repo/).


## Passaggio 2 - Configurazione del controllo del codice sorgente in Automazione di Azure

1. Nel pannello Account di automazione del portale di Azure fare clic su **Impostare il controllo codice sorgente.**
 
    ![Impostare il controllo codice sorgente](media/automation-source-control-integration/automation_01_SetUpSourceControl.png)

2. Viene aperto il pannello **Controllo codice sorgente** dove è possibile configurare i dettagli dell'account GitHub. Ecco l'elenco dei parametri da configurare:

    |**Parametro** |**Descrizione** |
    |:---|:---| 
    |Scegliere l'origine | Selezione l'origine. Attualmente è supportato solo **GitHub**. |
    |Autorizzazione | Fare clic sul pulsante **Autorizza** per concedere ad Automazione di Azure l'accesso al repository GitHub. Se si è già connessi con l'account GitHub in una finestra diversa, verranno usate le credenziali di quell'account. Una volta configurata l'autorizzazione, nel pannello sarà visualizzato il nome utente di GitHub in **Proprietà autorizzazione**. |
    |Scegliere un archivio | Selezionare un repository GitHub dall'elenco dei repository disponibili. |
    |Scegliere il ramo | Selezionare un ramo dall'elenco di rami disponibili. Se non sono stati creati rami, sarà visualizzato solo il ramo **master**. |
    |Percorso della cartella runbook | Il percorso della cartella runbook specifica il percorso del repository GitHub nel quale si vuole effettuare il push o il pull del codice. Deve essere immesso nel formato **/nomecartella/nomesottocartella**. Solo i runbook nel percorso della cartella runbook saranno sincronizzati con l'account di Automazione. I runbook nelle sottocartelle del percorso della cartella runbook **NON** saranno sincronizzati. Usare **/** per sincronizzare tutti i runbook disponibili nel repository. |


3. Ad esempio, se è disponibile un repository denominato **PowerShellScripts** che contiene una cartella denominata **RootFolder**, che a sua volta contiene una cartella denominata **SubFolder**. Per la sincronizzazione di ogni livello di cartella, è possibile utilizzare le stringhe seguenti:

    1. Per sincronizzare i runbook in **repository**, il percorso della cartella runbook è */*
    2. Per sincronizzare i runbook in **RootFolder**, il percorso della cartella runbook è */RootFolder*
    3. Per sincronizzare i runbook in **SubFolder**, il percorso della cartella runbook è */RootFolder/SubFolder*.
  

4. Dopo aver configurato i parametri, saranno visualizzati nel pannello **Impostare il controllo codice sorgente.**
 
    ![Pannello Configura](media/automation-source-control-integration/automation_02_SourceControlConfigure.png)


5. Dopo aver fatto clic su OK, l'integrazione del controllo del codice sorgente è ora configurata per l'account di Automazione e deve essere aggiornata con le informazioni su GitHub. È quindi possibile fare clic su questa parte della schermata per visualizzare l'intera cronologia dei processi di sincronizzazione del controllo del codice sorgente.

    ![Valori del repository](media/automation-source-control-integration/automation_03_RepoValues.png)

6. Dopo aver configurato il controllo del codice sorgente, nell'account di automazione verranno create le risorse di automazione seguenti: due [asset di tipo variabile](automation-variables.md).
      
    * La variabile **Microsoft.Azure.Automation.SourceControl.Connection** contiene i valori della stringa di connessione, come illustrato di seguito.

    |**Parametro** |**Valore** |
    |:---|:---|
    | Nome | Microsoft.Azure.Automation.SourceControl.Connection |
    | Tipo | String |
    | Valore | {"Branch":<*Nome del ramo*>,"RunbookFolderPath":<*Percorso della cartella runbook*>,"ProviderType":<*ha un valore 1 per GitHub*>,"Repository":<*Nome del repository*>,"Username":<*Nome utente di GitHub*>} | <br>


    * La variabile **Microsoft.Azure.Automation.SourceControl.OAuthToken** contiene il valore sicuro crittografato di OAuthToken.

    |**Parametro** |**Valore** |
    |:---|:---|
    | Nome | Microsoft.Azure.Automation.SourceControl.OAuthToken |
    | Tipo | Unknown(Encrypted) |
    | Valore | <*OAuthToken crittografato*> |  

    ![Variabili](media/automation-source-control-integration/automation_04_Variables.png)

    * All'account GitHub viene aggiunta **Controllo del codice sorgente di automazione** come applicazione autorizzata. Per visualizzare l'applicazione: dalla home page di GitHub passare al proprio **profilo** > **Settings** > **Applications**. Questa applicazione consente ad Automazione di Azure di sincronizzare il repository GitHub con un account di automazione.

    ![Applicazione Git](media/automation-source-control-integration/automation_05_GitApplication.png)


## Uso del controllo del codice sorgente in Automazione


### Archiviare un runbook da Automazione di Azure nel controllo del codice sorgente

L'archiviazione del runbook consente di effettuare il push delle modifiche apportate a un runbook in Automazione di Azure nel repository di controllo del codice sorgente. Ecco la procedura per archiviare un runbook:

1. Dall'account di automazione [creare un nuovo runbook testuale](automation-first-runbook-textual.md) o [modificare un runbook testuale esistente](automation-edit-textual-runbook.md). Il runbook può essere un flusso di lavoro PowerShell o un runbook di script PowerShell.

2. Dopo aver modificato il runbook, salvarlo e fare clic su **Archivia** nel pannello **Modifica**.

    ![Pulsante Archivia](media/automation-source-control-integration/automation_06_CheckinButton.png)


     >[AZURE.NOTE] L'archiviazione da Automazione di Azure sovrascriverà il codice attualmente presente nel controllo del codice sorgente. L'istruzione della riga di comando Git equivalente per l'archiviazione è **git add + git commit + git push**

3. Quando si fa clic su **Archivia**, viene visualizzato un messaggio. Fare clic su Sì per continuare.

    ![Messaggio di archiviazione](media/automation-source-control-integration/automation_07_CheckinMessage.png)

4. L'archiviazione avvia il runbook di controllo del codice sorgente: **Sync-MicrosoftAzureAutomationAccountToGitHubV1**. Questo runbook si connette a GitHub ed effettua il push delle modifiche da Automazione di Azure al repository. Per visualizzare la cronologia dei processi di archiviazione, tornare alla scheda **Integrazione del controllo del codice sorgente** e fare clic per aprire il pannello di sincronizzazione del repository. Questo pannello mostra tutti i processi di controllo del codice sorgente. Selezionare il processo che si vuole visualizzare e fare clic per vedere i dettagli.

    ![Archiviazione del runbook](media/automation-source-control-integration/automation_08_CheckinRunbook.png)

    >[AZURE.NOTE] I runbook di controllo del codice sorgente sono runbook di automazione speciali che non è possibile visualizzare o modificare. Mentre verranno visualizzati nell'elenco di runbook, verranno visualizzati i processi di sincronizzazione nell'elenco dei processi.
 
5. Il nome del runbook modificato viene inviato come parametro di input al runbook archiviato. È possibile [visualizzare i dettagli del processo](automation-runbook-execution.md#viewing-job-status-using-the-azure-management-portal) espandendo il runbook nel pannello **Sincronizzazione repository**.

    ![Archiviazione dell'input](media/automation-source-control-integration/automation_09_CheckinInput.png)

6. Aggiornare il repository GitHub al termine del processo per visualizzare le modifiche. Nel repository dovrebbe essere presente un commit con un messaggio simile al seguente: **Aggiornato *nome runbook* in Automazione di Azure.**



### Sincronizzare i runbook dal controllo del codice sorgente in Automazione di Azure 

Il pulsante di sincronizzazione nel pannello di sincronizzazione del repository consente di effettuare il pull di tutti i runbook nel percorso della cartella runbook del repository all'account di automazione. Lo stesso repository può essere sincronizzato con più account di automazione. Ecco la procedura per sincronizzare un runbook:

1. Nell'account di automazione dove è stato configurato il controllo del codice sorgente aprire il pannello **Integrazione del controllo del codice sorgente/Sincronizzazione repository** e fare clic su **Sincronizza**. Verrà visualizzato un messaggio, fare clic su **Sì** per continuare.

    ![Pulsante Sincronizza](media/automation-source-control-integration/automation_10_SyncButtonwithMessage.png)

2. La sincronizzazione avvia il runbook: **Sync-MicrosoftAzureAutomationAccountFromGitHubV1**. Questo runbook si connette a GitHub ed effettua il pull delle modifiche dal repository ad Automazione di Azure. Dovrebbe essere presente un nuovo processo nel pannello **Sincronizzazione repository** per questa azione. Per visualizzare i dettagli del processo di sincronizzazione, fare clic per aprire il pannello dei dettagli del processo.
 
    ![Sincronizzazione dei runbook](media/automation-source-control-integration/automation_11_SyncRunbook.png)

 
    >[AZURE.NOTE] Una sincronizzazione del controllo del codice sorgente sovrascrive la versione bozza dei runbook attualmente presenti nell'account di automazione per **TUTTI** i runbook attualmente nel controllo del codice sorgente. L'istruzione della riga di comando Git equivalente per la sincronizzazione è **git pull**


## Risoluzione dei problemi di controllo del codice sorgente

In caso di errori nel processo di archiviazione, il relativo stato sarà sospeso e si potranno visualizzare altri dettagli sull'errore nel pannello dei processi. La parte **Tutti i log** mostrerà tutti i flussi di PowerShell associati a quel processo. In questo modo saranno forniti i dettagli necessari per risolvere gli eventuali problemi relativi al controllo o alla sincronizzazione. Verrà anche mostrata la sequenza di azioni che si è verificata durante la sincronizzazione o l'archiviazione di un runbook.

![Immagine AllLogs](media/automation-source-control-integration/automation_13_AllLogs.png)

## Disconnessione del controllo del codice sorgente

Per disconnettersi dall'account GitHub, aprire il pannello di sincronizzazione del repository e fare clic su **Disconnetti**. Dopo la disconnessione dal controllo del codice sorgente, i runbook sincronizzati in precedenza rimarranno nell'account di automazione, ma il pannello di sincronizzazione del repository non sarà abilitato.

  ![Pulsante Disconnetti](media/automation-source-control-integration/automation_12_Disconnect.png)



## Passaggi successivi

Per altre informazioni sull'integrazione del controllo del codice sorgente, vedere le risorse seguenti:
- [Automazione di Azure: Integrazione del controllo del codice sorgente in Automazione di Azure](https://azure.microsoft.com/blog/azure-automation-source-control-13/)
- [Scelta del sistema di controllo del codice sorgente preferito](https://www.surveymonkey.com/r/?sm=2dVjdcrCPFdT0dFFI8nUdQ%3d%3d)
- [Automazione di Azure: Integrazione del controllo del codice sorgente dei runbook usando Visual Studio Team Services](https://azure.microsoft.com/blog/azure-automation-integrating-runbook-source-control-using-visual-studio-online/)

<!---HONumber=AcomDC_0914_2016-->