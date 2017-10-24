---
title: Automazione della distribuzione di una macchina virtuale in Amazon Web Services | Documentazione Microsoft
description: Questo articolo illustra come usare Automazione di Azure per automatizzare la creazione di una VM di Amazon Web Service
services: automation
documentationcenter: 
author: eslesar
manager: carmonm
editor: 
ms.assetid: 1d85c01a-d795-4523-8194-84fc15b53838
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/29/2017
ms.author: tiandert; bwren
ms.openlocfilehash: 828f9e2cc9a39e54933cd0e0db7273efa460d0c7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-automation-scenario---provision-an-aws-virtual-machine"></a>Scenario di Automazione di Azure - Provisioning di una macchina virtuale di AWS
In questo articolo viene illustrato come sfruttare Automazione di Azure per effettuare il provisioning di una macchina virtuale nella sottoscrizione di Amazon Web Service (AWS) e come assegnare alla VM un nome specifico. Questa operazione è definita in AWS "assegnazione di tag" alla VM.

## <a name="prerequisites"></a>Prerequisiti
Per le finalità di questo articolo, è necessario avere un account di Automazione di Azure e una sottoscrizione di AWS. Per altre informazioni sulla configurazione di un account di Automazione di Azure e sulla configurazione delle credenziali della sottoscrizione di AWS, vedere l'articolo relativo alla [configurazione dell'autenticazione in Amazon Web Services](automation-config-aws-account.md).  Questo account deve essere creato o aggiornato con le credenziali della sottoscrizione di AWS prima di continuare, perché nei passaggi seguenti verrà fatto riferimento all'account.

## <a name="deploy-amazon-web-services-powershell-module"></a>Distribuire il modulo di PowerShell di Amazon Web Services
Il runbook di provisioning della macchina virtuale usa il modulo di PowerShell di AWS per l'esecuzione delle operazioni. Seguire questa procedura per aggiungere il modulo all'account di Automazione configurato con le credenziali della sottoscrizione di AWS.  

1. Aprire il Web browser, accedere a [PowerShell Gallery](http://www.powershellgallery.com/packages/AWSPowerShell/) e fare clic sul pulsante **Deploy to Azure Automation** (Distribuisci in Automazione di Azure).<br><br> ![Importazione del modulo PS per AWS](./media/automation-scenario-aws-deployment/powershell-gallery-download-awsmodule.png)
2. Verrà visualizzata la pagina di accesso di Azure e dopo l'autenticazione si verrà indirizzati al portale di Azure e verrà visualizzata la pagina seguente.<br><br> ![Pagina di importazione del modulo](./media/automation-scenario-aws-deployment/deploy-aws-powershell-module-parameters.png)
3. Selezionare il gruppo di risorse dall'elenco a discesa **Gruppo di risorse** e nel riquadro Parametri fornire le informazioni seguenti:
   
   * Nell'elenco a discesa **account di automazione nuovo o esistente (stringa)** selezionare **Esistente**.  
   * Nella casella **Automation Account Name (string)** (Nome dell'account di Automazione - stringa) digitare il nome esatto dell'account di Automazione che include le credenziali per la sottoscrizione di AWS.  Ad esempio, se è stato creato un account dedicato denominato **AWSAutomation**, digitare tale valore nella casella.
   * Selezionare l'area appropriata dall'elenco a discesa **Posizione dell'account di Automazione** .
4. Dopo avere completato l'immissione delle informazioni necessarie, fare clic su **Crea**.
   
   > [!NOTE]
   > Durante l'importazione di un modulo di PowerShell in Automazione di Azure, vengono estratti anche i cmdlet e queste attività verranno visualizzate solo dopo il completamento dell'importazione del modulo e l'estrazione dei cmdlet. Il processo potrebbe richiedere alcuni minuti.  
   > <br>
   > 
   > 
5. Nel portale di Azure aprire l'account di Automazione a cui si fa riferimento nel passaggio 3.
6. Fare clic sul riquadro **Asset** e nel riquadro **Assets** (Risorse) selezionare il riquadro **Moduli**.
7. Nella pagina **Moduli** il modulo **AWSPowerShell** viene visualizzato nell'elenco.

## <a name="create-aws-deploy-vm-runbook"></a>Creare un runbook per la distribuzione di una VM in AWS
Dopo la distribuzione del modulo di PowerShell per AWS, è possibile creare un runbook per automatizzare il provisioning di una macchina virtuale in AWS mediante uno script di PowerShell. La procedura seguente illustra come sfruttare lo script di PowerShell nativo in Automazione di Azure.  

> [!NOTE]
> Per altre opzioni e altre informazioni su questo script, vedere la [PowerShell Gallery](https://www.powershellgallery.com/packages/New-AwsVM/DisplayScript).
> 

1. Scaricare lo script New-AwsVM di PowerShell da PowerShell Gallery aprendo una sessione di PowerShell e digitando quanto segue:<br>
   ```
   Save-Script -Name New-AwsVM -Path <path>
   ```
   <br>
2. Dal portale di Azure aprire l'account di automazione e selezionare **Runbook** nella sezione **Automazione processi** a sinistra.  
3. Nella pagina **Runbook** selezionare **Aggiungi runbook**.
4. Nel riquadro **Aggiungi runbook** selezionare **Creazione rapida** (Crea un nuovo runbook).
5. Nel riquadro delle proprietà di **Runbook** digitare un nome per il runbook nella casella Nome, nell'elenco a discesa **Tipo di runbook** selezionare **PowerShell** e quindi fare clic su **Crea**.<br><br> ![Riquadro Create runbook](./media/automation-scenario-aws-deployment/runbook-quickcreate-properties.png) (Crea runbook)
6. Quando viene visualizzata la pagina Modifica runbook di PowerShell, copiare e incollare lo script di PowerShell nell'area di creazione del runbook.<br><br> ![Script di PowerShell per il runbook](./media/automation-scenario-aws-deployment/runbook-powershell-script.png)<br>
   
    > [!NOTE]
    > Notare quanto segue quando si utilizza lo script di PowerShell di esempio:
    > 
    > * Il runbook contiene alcuni valori predefiniti dei parametri. Valutare tutti i valori predefiniti e apportare gli aggiornamenti necessari.
    > * Se le credenziali di AWS sono state archiviate come asset delle credenziali con nome diverso rispetto ad **AWScred**, sarà necessario aggiornare lo script alla riga 57 in modo che il valore corrisponda.  
    > * Quando si utilizzano i comandi dell'interfaccia della riga di comando di AWS in PowerShell, in particolare con questo runbook di esempio, è necessario specificare l'area di AWS. In caso contrario, i cmdlet avranno esito negativo.  Per altre informazioni, vedere l'argomento di AWS [Specify AWS Region](http://docs.aws.amazon.com/powershell/latest/userguide/pstools-installing-specifying-region.html) (Specificare l'area di AWS) nel documento relativo agli strumenti di AWS per PowerShell.  
    >

7. Per recuperare un elenco di nomi di immagine dalla sottoscrizione di AWS, avviare PowerShell ISE e importare il modulo di PowerShell per AWS.  Eseguire l'autenticazione in AWS sostituendo **Get-AutomationPSCredential** nell'ambiente ISE con **AWScred = Get-Credential**.  Alla richiesta delle credenziali, specificare il proprio **ID chiave di accesso** come nome utente e la propria **chiave di accesso segreta** come password.  Vedere l'esempio seguente:  

        #Sample to get the AWS VM available images
        #Please provide the path where you have downloaded the AWS PowerShell module
        Import-Module AWSPowerShell
        $AwsRegion = "us-west-2"
        $AwsCred = Get-Credential
        $AwsAccessKeyId = $AwsCred.UserName
        $AwsSecretKey = $AwsCred.GetNetworkCredential().Password
   
        # Set up the environment to access AWS
        Set-AwsCredentials -AccessKey $AwsAccessKeyId -SecretKey $AwsSecretKey -StoreAs AWSProfile
        Set-DefaultAWSRegion -Region $AwsRegion
   
        Get-EC2ImageByName -ProfileName AWSProfile

    Viene restituito l'output seguente:<br><br>
   ![Ottenere immagini AWS](./media/automation-scenario-aws-deployment/powershell-ise-output.png)<br>  
8. Copiare e incollare uno dei nomi di immagine in una variabile di Automazione, indicato nel runbook come **$InstanceType**. Dato che in questo esempio si usa la sottoscrizione a livelli gratuita di AWS, per l'esempio di runbook verrà usato il valore **t2.micro** .  
9. Salvare il runbook, quindi fare clic su **Pubblica** per pubblicare il runbook e quindi fare clic su **Sì** quando richiesto.

### <a name="testing-the-aws-vm-runbook"></a>Test del runbook per la VM per AWS
Prima di continuare il test del runbook, è necessario verificare alcuni elementi. In particolare:  

* Gli asset per l'autenticazione in AWS sono stati creati e sono stati denominati **AWScred** o lo script è stato aggiornato per fare riferimento al nome dell'asset delle credenziali.    
* Il modulo di PowerShell di AWS è stato importato in Automazione di Azure  
* È stato creato un nuovo runbook e i valori dei parametri sono stati verificati e aggiornati, se necessario  
* Le opzioni **Registra record dettagliati** e, facoltativamente, **Registra record di avanzamento** nell'impostazione **Registrazione e traccia** sono state impostate su **Sì**.<br><br> ![Registrazione e traccia per i runbook](./media/automation-scenario-aws-deployment/runbook-settings-logging-and-tracing.png)  

1. Per avviare il runbook, fare clic su **Avvia** e quindi su **OK** quando si apre il riquadro Avvia runbook.
2. Nel riquadro Avvia runbook fornire un valore **VMname**.  Accettare i valori predefiniti per gli altri parametri preconfigurati nello script in precedenza.  Fare clic su **OK** per avviare il processo del runbook.<br><br> ![Avvio del runbook New-AwsVM](./media/automation-scenario-aws-deployment/runbook-start-job-parameters.png)
3. Viene aperto un riquadro del processo per il processo del runbook appena creato. Chiudere questo riquadro.
4. Per visualizzare lo stato di avanzamento del processo e i **Flussi** di output, selezionare il riquadro **Tutti i log** nella pagina del processo del runbook.<br><br> ![Output del flusso](./media/automation-scenario-aws-deployment/runbook-job-streams-output.png)
5. Per confermare che è in corso il provisioning della VM, accedere ad AWS Management Console se non è già stato effettuato l'accesso.<br><br> ![Macchina virtuale distribuita in una console di AWS](./media/automation-scenario-aws-deployment/aws-instances-status.png)

## <a name="next-steps"></a>Passaggi successivi
* Per iniziare a usare runbook grafici, vedere [Il primo runbook grafico](automation-first-runbook-graphical.md)
* Per iniziare a usare i runbook del flusso di lavoro PowerShell, vedere [Il primo runbook del flusso di lavoro PowerShell](automation-first-runbook-textual.md)
* Per altre informazioni sui tipi di runbook, i relativi vantaggi e le limitazioni, vedere [Tipi di runbook di Automazione di Azure](automation-runbook-types.md)
* Per altre informazioni sulla funzionalità di supporto degli script PowerShell, vedere il blog relativo al [supporto di script PowerShell nativi in Automazione di Azure](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/)

