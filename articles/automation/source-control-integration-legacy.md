---
title: Integrazione del controllo del codice sorgente in Automazione di Azure - Legacy
description: Questo articolo descrive l'integrazione del controllo del codice sorgente con GitHub in Automazione di Azure.
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 04/01/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: c95af40c3fa3f9dad2bfb5ea4a1b9f585c636928
ms.sourcegitcommit: 3341598aebf02bf45a2393c06b136f8627c2a7b8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/01/2019
ms.locfileid: "58807061"
---
# <a name="source-control-integration-in-azure-automation---legacy"></a>Integrazione del controllo del codice sorgente in Automazione di Azure - Legacy

> [!NOTE]
> È disponibile una nuova esperienza per il controllo del codice sorgente. Per altre informazioni sulla nuova esperienza, vedere [Controllo del codice sorgente (anteprima)](source-control-integration.md).

L'integrazione del controllo del codice sorgente consente di associare runbook nell'account di automazione a un repository di controllo del codice sorgente su GitHub. Il controllo del codice sorgente fornisce un ambiente in cui è possibile collaborare con il team, tenere traccia delle modifiche ed eseguire il rollback a versioni precedenti dei runbook. Ad esempio, il controllo del codice sorgente consente di sincronizzare diversi rami nel controllo del codice sorgente con gli account di automazione di sviluppo, test o produzione e alzare di livello il codice testato nell'ambiente di sviluppo per passarlo all'ambiente di produzione.

Il controllo del codice sorgente consente di effettuare il push del codice da Automazione di Azure al controllo del codice sorgente oppure effettuare il pull dei runbook dal controllo del codice sorgente ad Automazione di Azure. Questo articolo descrive come configurare il controllo del codice sorgente nell'ambiente di Automazione di Azure. Si inizia configurando Automazione di Azure per accedere al repository GitHub e vengono poi descritte varie operazioni che possono essere eseguite usando l'integrazione del controllo del codice sorgente. 

> [!NOTE]
> Il controllo del codice sorgente supporta il pull e il push di [Runbook del flusso di lavoro PowerShell](automation-runbook-types.md#powershell-workflow-runbooks) e [Runbook di PowerShell](automation-runbook-types.md#powershell-runbooks). I [Runbook grafici](automation-runbook-types.md#graphical-runbooks) non sono ancora supportati.

Per configurare il controllo del codice sorgente per l'account di automazione sono richiesti due semplici passaggi e solo uno se si ha già un account GitHub. Sono:

## <a name="step-1--create-a-github-repository"></a>Passaggio 1: Creare un repository GitHub

Se si ha già un account GitHub e un repository che si vuole collegare ad Automazione di Azure, accedere con l'account esistente e iniziare dal passaggio 2 seguente. In caso contrario, passare a [GitHub](https://github.com/), effettuare l'iscrizione per un nuovo account, e [crea un nuovo repository](https://help.github.com/articles/create-a-repo/).

## <a name="step-2--set-up-source-control-in-azure-automation"></a>Passaggio 2 - Configurazione del controllo del codice sorgente in Automazione di Azure

1. Dalla pagina Account di automazione nel portale di Azure fare clic su **Controllo codice sorgente** in **Impostazioni account**.

2. Viene aperta la pagina **Controllo codice sorgente** dove è possibile configurare i dettagli dell'account GitHub. Ecco l'elenco dei parametri da configurare:  

   | **Parametro** | **Descrizione** |
   |:--- |:--- |
   | Scegliere l'origine |Selezione l'origine. Attualmente è supportato solo **GitHub** . |
   | Authorization |Fare clic sul pulsante **Autorizza** per concedere ad Automazione di Azure l'accesso al repository GitHub. Se si è già connessi con l'account GitHub in una finestra diversa, verranno usate le credenziali di quell'account. Dopo aver ottenuto l'autorizzazione, nella pagina verrà visualizzato il nome utente di GitHub in **Proprietà autorizzazione**. |
   | Scegliere un archivio |Selezionare un repository GitHub dall'elenco dei repository disponibili. |
   | Scegliere il ramo |Selezionare un ramo dall'elenco di rami disponibili. Se non sono stati creati rami, sarà visualizzato solo il ramo **master** . |
   | Percorso della cartella runbook |Il percorso della cartella runbook specifica il percorso del repository GitHub nel quale si vuole effettuare il push o il pull del codice. Deve essere immesso nel formato **/nomecartella/nomesottocartella**. Solo i runbook nel percorso della cartella runbook saranno sincronizzati con l'account di Automazione. I runbook nelle sottocartelle del percorso della cartella runbook **NON** saranno sincronizzati. Usare **/** per sincronizzare tutti i runbook disponibili nel repository. |
3. Ad esempio, se è disponibile un repository denominato **PowerShellScripts** che contiene una cartella denominata **RootFolder**, che a sua volta contiene una cartella denominata **SubFolder**. Per la sincronizzazione di ogni livello di cartella, è possibile utilizzare le stringhe seguenti:

   1. Per sincronizzare i runbook in **repository**, il percorso della cartella runbook è */*
   2. Per sincronizzare i runbook in **RootFolder**, il percorso della cartella runbook è */RootFolder*
   3. Per sincronizzare i runbook in **SubFolder**, il percorso della cartella runbook è */RootFolder/SubFolder*.
4. Dopo la configurazione, i parametri vengono visualizzati nella pagina **Impostare il controllo codice sorgente.**  

    ![La pagina di controllo di origine che mostra le impostazioni](media/source-control-integration-legacy/automation-SourceControlConfigure.png)
5. Dopo aver fatto clic su **OK**, l'integrazione del controllo del codice sorgente è ora configurata per l'account di Automazione e dovrebbe essere aggiornata con le informazioni personali per GitHub. È quindi possibile fare clic su questa parte della schermata per visualizzare l'intera cronologia dei processi di sincronizzazione del controllo del codice sorgente.  

    ![Valori per la configurazione corrente del controllo origine configurata](media/source-control-integration-legacy/automation-RepoValues.png)
6. Dopo aver impostato controllo del codice sorgente, due [gli asset variabili](automation-variables.md) vengono creati nell'account di automazione. Inoltre, un'applicazione autorizzata viene aggiunto all'account GitHub.

   * La variabile **Microsoft.Azure.Automation.SourceControl.Connection** contiene i valori della stringa di connessione, come illustrato di seguito.  

     | **Parametro** | **Valore** |
     |:--- |:--- |
     | `Name`  |Microsoft.Azure.Automation.SourceControl.Connection |
     | `Type`  |string |
     | `Value` |{"Branch":\<*Nome del ramo*>,"RunbookFolderPath":\<*Percorso della cartella del runbook*>,"ProviderType":\< *con valore 1 per GitHub*>,"Repository":\<*Nome del repository*>,"Username":\<*Nome utente di GitHub*>} |

   * La variabile **Microsoft.Azure.Automation.SourceControl.OAuthToken**contiene il valore sicuro crittografato di OAuthToken.  

     |**Parametro**            |**Valore** |
     |:---|:---|
     | `Name`  | Microsoft.Azure.Automation.SourceControl.OAuthToken |
     | `Type`  | Unknown(Encrypted) |
     | `Value` | <*OAuthToken crittografato*> |  

     ![Una finestra che mostra le variabili di controllo di origine](media/source-control-integration-legacy/automation-Variables.png)  

   * **Controllo del codice sorgente di automazione** come applicazione autorizzata. Per visualizzare l'applicazione: dalla home page di GitHub passare al proprio **profilo** > **Impostazioni** > **Applicazioni**. Questa applicazione consente ad Automazione di Azure di sincronizzare il repository GitHub con un account di automazione.  

     ![Impostazioni dell'applicazione in GitHub](media/source-control-integration-legacy/automation-GitApplication.png)

## <a name="using-source-control-in-automation"></a>Uso del controllo del codice sorgente in Automazione

### <a name="check-in-a-runbook-from-azure-automation-to-source-control"></a>Archiviare un runbook da Automazione di Azure nel controllo del codice sorgente

L'archiviazione del runbook consente di effettuare il push delle modifiche apportate a un runbook in Automazione di Azure nel repository di controllo del codice sorgente. Ecco la procedura per archiviare un runbook:

1. Dall'account di automazione [creare un nuovo runbook testuale](automation-first-runbook-textual.md) o [modificare un runbook testuale esistente](automation-edit-textual-runbook.md). Il runbook può essere un flusso di lavoro PowerShell o un runbook di script PowerShell.  
2. Dopo aver modificato il runbook, salvarlo e fare clic su **Archivia** nella pagina **Modifica**.  

    ![Una finestra che mostra il processo di archiviazione al pulsante di GitHub](media/source-control-integration-legacy/automation-CheckinButton.png)

     > [!NOTE] 
     > L'archiviazione da Automazione di Azure sovrascrive il codice attualmente presente nel controllo del codice sorgente. L'istruzione della riga di comando GIT equivalente per l'archiviazione è **git add + git commit + git push**  

3. Selezionando **Archivia** viene visualizzato un messaggio di conferma. Fare clic su **Sì** per continuare.  

    ![Una finestra di dialogo che conferma il check-in controllo del codice sorgente](media/source-control-integration-legacy/automation-CheckinMessage.png)
4. L'archiviazione avvia il runbook di controllo del codice sorgente: **Sync-MicrosoftAzureAutomationAccountToGitHubV1**. Questo runbook si connette a GitHub ed effettua il push delle modifiche da Automazione di Azure al repository. Per visualizzare la cronologia dei processi di archiviazione, tornare alla scheda **Integrazione del controllo del codice sorgente** e fare clic per aprire la pagina Sincronizzazione repository. Questa pagina mostra tutti i processi di controllo del codice sorgente.  Selezionare il processo che si vuole visualizzare e fare clic per vedere i dettagli.  

    ![Una finestra che mostra i risultati di un processo di sincronizzazione](media/source-control-integration-legacy/automation-CheckinRunbook.png)

   > [!NOTE]
   > I runbook di controllo del codice sorgente sono runbook di automazione speciali che non è possibile visualizzare o modificare. Anche se non compaiono nell'elenco di runbook, i processi di sincronizzazione vengono visualizzati nell'elenco dei processi.

5. Il nome del runbook modificato viene inviato come parametro di input per il runbook archiviato. È possibile [visualizzare i dettagli del processo](automation-runbook-execution.md#viewing-job-status-from-the-azure-portal) espandendo il runbook nella pagina **Sincronizzazione repository**.  

    ![Una finestra che mostra l'input per un processo di sincronizzazione](media/source-control-integration-legacy/automation-CheckinInput.png)
6. Aggiornare il repository GitHub al termine del processo per visualizzare le modifiche.  Nel repository dovrebbe essere presente un commit con un messaggio simile al seguente: **Aggiornato *nome runbook* in Automazione di Azure.**  

### <a name="sync-runbooks-from-source-control-to-azure-automation"></a>Sincronizzare i runbook dal controllo del codice sorgente in Automazione di Azure

Il pulsante di sincronizzazione nella pagina Sincronizzazione repository consente di effettuare il pull di tutti i runbook nel percorso della cartella runbook del repository all'account di Automazione. Lo stesso repository può essere sincronizzato con più account di automazione. Ecco la procedura per sincronizzare un runbook:

1. Nell'account di Automazione dove è stato configurato il controllo del codice sorgente aprire la pagina **Integrazione del controllo del codice sorgente/Sincronizzazione repository** e fare clic su **Sincronizza**.  Viene visualizzato un messaggio di conferma. Fare clic su **Sì** per continuare.  

    ![Verrà sincronizzati con il pulsante Sincronizza con messaggio di conferma di tutti i runbook](media/source-control-integration-legacy/automation-SyncButtonwithMessage.png)

2. La sincronizzazione avvia il runbook: **Sync-MicrosoftAzureAutomationAccountFromGitHubV1**. Questo runbook si connette a GitHub ed effettua il pull delle modifiche dal repository ad Automazione di Azure. Dovrebbe essere presente un nuovo processo nella pagina **Sincronizzazione repository** per questa azione. Per visualizzare i dettagli del processo di sincronizzazione, fare clic per aprire la pagina dei dettagli del processo.  

    ![Una finestra che mostra i risultati di sincronizzazione di un processo di sincronizzazione in un repository GitHub](media/source-control-integration-legacy/automation-SyncRunbook.png)

    > [!NOTE]
    > Una sincronizzazione del controllo del codice sorgente sovrascrive la versione bozza dei runbook attualmente presenti nell'account di automazione per **TUTTI** i runbook attualmente nel controllo del codice sorgente. L'istruzione della riga di comando GIT equivalente per la sincronizzazione è **git pull**

![Una finestra che mostra tutti i log da un processo di sincronizzazione del controllo origine sospesi](media/source-control-integration-legacy/automation-AllLogs.png)

## <a name="disconnecting-source-control"></a>Disconnessione del controllo del codice sorgente

Per disconnettersi dall'account GitHub, aprire la pagina Sincronizzazione repository e fare clic su **Disconnetti**. Dopo la disconnessione dal controllo del codice sorgente, i runbook sincronizzati in precedenza rimangono nell'account di Automazione, ma la pagina Sincronizzazione repository non sarà abilitata.  

  ![Una finestra che mostra il pulsante Disconnetti per disconnettere controllo del codice sorgente](media/source-control-integration-legacy/automation-Disconnect.png)

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'integrazione del controllo del codice sorgente, vedere le risorse seguenti:  

* [Automazione di Azure: Source Control Integration in Azure Automation (Automazione di Azure: Integrazione del controllo del codice sorgente in Automazione di Azure)](https://azure.microsoft.com/blog/azure-automation-source-control-13/)  
* [Scelta del sistema di controllo del codice sorgente preferito](https://www.surveymonkey.com/r/?sm=2dVjdcrCPFdT0dFFI8nUdQ%3d%3d)  
* [Automazione di Azure: Integrating Runbook Source Control using Azure DevOps (Automazione di Azure: Integrazione del controllo del codice sorgente dei runbook tramite Azure DevOps)](https://azure.microsoft.com/blog/azure-automation-integrating-runbook-source-control-using-visual-studio-online/)  
