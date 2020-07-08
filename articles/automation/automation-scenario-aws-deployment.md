---
title: Distribuire una macchina virtuale di Amazon Web Services con un runbook di Automazione di Azure
description: Questo articolo illustra come automatizzare la creazione di una macchina virtuale di Amazon Web Services.
services: automation
ms.subservice: process-automation
ms.date: 03/16/2018
ms.topic: conceptual
ms.openlocfilehash: d67e2639c2d4b168babeb7c29ef977d39d9e11cb
ms.sourcegitcommit: 9b5c20fb5e904684dc6dd9059d62429b52cb39bc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85855306"
---
# <a name="deploy-an-amazon-web-services-vm-with-a-runbook"></a>Distribuire una macchina virtuale di Amazon Web Services con un runbook

In questo articolo si apprenderà come sfruttare Automazione di Azure per effettuare il provisioning di una macchina virtuale nella sottoscrizione di Amazon Web Service (AWS) e come assegnare alla VM un nome specifico. Questa operazione è definita in AWS "assegnazione di tag" alla VM.

## <a name="prerequisites"></a>Prerequisiti

È necessario avere un account di Automazione di Azure e una sottoscrizione di Amazon Web Services (AWS). Per altre informazioni sulla configurazione di un account di Automazione di Azure e sulla configurazione delle credenziali della sottoscrizione di AWS, vedere l'articolo relativo alla [configurazione dell'autenticazione in Amazon Web Services](automation-config-aws-account.md). Questo account deve essere creato o aggiornato con le credenziali della sottoscrizione di AWS prima di continuare, perché nelle sezioni seguenti si farà riferimento all'account.

## <a name="deploy-amazon-web-services-powershell-module"></a>Distribuire il modulo di PowerShell di Amazon Web Services

Per eseguire le operazioni, il runbook di provisioning della macchina virtuale usa il modulo di PowerShell di AWS. Seguire questa procedura per aggiungere il modulo all'account di Automazione configurato con le credenziali della sottoscrizione di AWS.  

1. Aprire il Web browser, accedere a [PowerShell Gallery](https://www.powershellgallery.com/packages/AWSPowerShell/) e fare clic sul pulsante **Deploy to Azure Automation** (Distribuisci in Automazione di Azure).<br><br> ![Importazione del modulo PS per AWS](./media/automation-scenario-aws-deployment/powershell-gallery-download-awsmodule.png)
2. Verrà visualizzata la pagina di accesso di Azure e dopo l'autenticazione si verrà indirizzati al portale di Azure e verrà visualizzata la pagina seguente:<br><br> ![Pagina di importazione del modulo](./media/automation-scenario-aws-deployment/deploy-aws-powershell-module-parameters.png)
3. Selezionare l'account di Automazione da usare e fare clic su **OK** per avviare la distribuzione.

   > [!NOTE]
   > Quando Automazione di Azure importa un modulo di PowerShell, estrae i cmdlet. Le attività vengono visualizzate solo quando Automazione ha terminato completamente l'importazione del modulo e l'estrazione dei cmdlet. Il processo potrebbe richiedere alcuni minuti.  
   > <br>

1. Nel portale di Azure aprire l'account di automazione.
2. Fare clic sul riquadro **Risorse** e nel riquadro Risorse selezionare **Moduli**.
3. Nell'elenco nella pagina Moduli è visualizzato il modulo **AWSPowerShell**.

## <a name="create-aws-deploy-vm-runbook"></a>Creare un runbook per la distribuzione di una VM in AWS

Dopo la distribuzione del modulo di PowerShell per AWS, è possibile creare un runbook per automatizzare il provisioning di una macchina virtuale in AWS mediante uno script di PowerShell. La procedura seguente illustra come usare lo script di PowerShell nativo in Automazione di Azure.  

> [!NOTE]
> Per altre opzioni e altre informazioni su questo script, vedere la [PowerShell Gallery](https://www.powershellgallery.com/packages/New-AwsVM/).
> 

1. Scaricare lo script di PowerShell New-AwsVM da PowerShell Gallery aprendo una sessione di PowerShell e digitando il comando seguente:<br>
   ```powershell
   Save-Script -Name New-AwsVM -Path <path>
   ```
   <br>
2. Nel portale di Azure aprire l'account di Automazione e selezionare **Runbook** in **Automazione processi**.  
3. Nella pagina Runbook selezionare **Aggiungi runbook**.
4. Nella pagina Aggiungi runbook selezionare **Creazione rapida** per creare un nuovo runbook.
5. Nel riquadro delle proprietà del runbook digitare un nome per quest'ultimo.
6. Nell'elenco a discesa **Tipo di runbook** selezionare **PowerShell** e quindi fare clic su **Crea**.<br><br> ![Riquadro Create runbook](./media/automation-scenario-aws-deployment/runbook-quickcreate-properties.png) (Crea runbook)
7. Quando viene visualizzata la pagina Modifica runbook di PowerShell, copiare e incollare lo script di PowerShell nell'area di creazione del runbook.<br><br> ![Script di PowerShell per il runbook](./media/automation-scenario-aws-deployment/runbook-powershell-script.png)<br>
   
    Notare quanto segue quando si usa lo script di PowerShell di esempio:

    * Il runbook contiene alcuni valori predefiniti dei parametri. Valutare tutti i valori predefiniti e apportare gli aggiornamenti necessari.
    * Se le credenziali di AWS sono state archiviate come asset credenziali con un nome diverso da `AWScred`, è necessario aggiornare lo script alla riga 57 in modo che il nome corrisponda.  
    * Quando si utilizzano i comandi dell'interfaccia della riga di comando di AWS in PowerShell, in particolare con questo runbook di esempio, è necessario specificare l'area di AWS. In caso contrario, i cmdlet avranno esito negativo. Per altre informazioni, vedere l'argomento di AWS [Specify AWS Region](https://docs.aws.amazon.com/powershell/latest/userguide/pstools-installing-specifying-region.html) (Specificare l'area di AWS) nel documento relativo agli strumenti di AWS per PowerShell.  

8. Per recuperare un elenco di nomi di immagine dalla sottoscrizione di AWS, avviare PowerShell ISE e importare il modulo di PowerShell per AWS. Eseguire l'autenticazione ad AWS sostituendo `Get-AutomationPSCredential` nell'ambiente ISE con `AWScred = Get-Credential`. Questa istruzione chiede le credenziali. È possibile specificare l'ID chiave di accesso come nome utente e la chiave di accesso segreta come password. 

      ```powershell
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
      ```
        
    Viene restituito l'output seguente:<br><br>
   ![Ottenere immagini AWS](./media/automation-scenario-aws-deployment/powershell-ise-output.png)<br>  
9. Copiare e incollare il nome di immagine in una variabile di Automazione indicata nel runbook come `$InstanceType`. Dato che, in questo esempio, si usa la sottoscrizione a livelli gratuita di AWS, per l'esempio di runbook verrà usato il valore **t2.micro**.  
10. Salvare il runbook, quindi fare clic su **Pubblica** per pubblicare il runbook e quindi fare clic su **Sì** quando richiesto.

### <a name="test-the-aws-vm-runbook"></a>Testare il runbook della macchina virtuale di AWS

1. Verificare che il runbook crei un asset denominato `AWScred` per l'autenticazione in AWS o aggiornare lo script in modo che faccia riferimento al nome dell'asset credenziali.    
2. Verificare il nuovo runbook e assicurarsi che tutti i valori dei parametri siano stati aggiornati come necessario.
Assicurarsi che il modulo di PowerShell di AWS sia stato importato in Automazione di Azure.  
3. In Automazione di Azure impostare **Registra record dettagliati** e, facoltativamente, **Registra record di avanzamento** nell'operazione **Registrazione e traccia** del runbook su **Sì**.<br><br> ![Registrazione e traccia per i runbook](./media/automation-scenario-aws-deployment/runbook-settings-logging-and-tracing.png).  
4. Per avviare il runbook, fare clic su **Avvia** e quindi su **OK** quando si apre il riquadro Avvia runbook.
5. Nel riquadro Avvia runbook specificare un nome di macchina virtuale. Accettare i valori predefiniti per gli altri parametri preconfigurati nello script. Fare clic su **OK** per avviare il processo del runbook.<br><br> ![Avvio del runbook New-AwsVM](./media/automation-scenario-aws-deployment/runbook-start-job-parameters.png)
6. Verrà aperto un riquadro per il processo del runbook creato. Chiudere questo riquadro.
7. Per visualizzare lo stato del processo e i flussi di output, selezionare **Tutti i log** nella pagina Processo del runbook.<br><br> ![Output del flusso](./media/automation-scenario-aws-deployment/runbook-job-streams-output.png)
8. Per verificare che il provisioning della macchina virtuale sia in corso, accedere alla Console di gestione AWS se non lo si è già fatto.<br><br> ![Macchina virtuale distribuita in una console di AWS](./media/automation-scenario-aws-deployment/aws-instances-status.png)

## <a name="next-steps"></a>Passaggi successivi
 
* Per informazioni sui runbook supportati, vedere[Tipi di runbook di Automazione di Azure](automation-runbook-types.md).
* Per usare i runbook, vedere [Gestire runbook in Automazione di Azure](manage-runbooks.md).
* Per informazioni dettagliate su PowerShell, vedere [Documentazione di PowerShell](https://docs.microsoft.com/powershell/scripting/overview).
* Per il supporto di script, vedere [Supporto di script nativi di PowerShell in Automazione di Azure](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/)
* Per informazioni di riferimento sui cmdlet di PowerShell, vedere [Az.Automation](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation
).