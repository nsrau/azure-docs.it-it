---
title: Automazione della distribuzione di una macchina virtuale in Amazon Web Services
description: Questo articolo illustra come usare Automazione di Azure per automatizzare la creazione di una VM di Amazon Web Service
services: automation
ms.subservice: process-automation
ms.date: 03/16/2018
ms.topic: conceptual
ms.openlocfilehash: 52a887d2d934aa2f7e13f0c2fdb4332066aee0e7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81604837"
---
# <a name="azure-automation-scenario---provision-an-aws-virtual-machine"></a>Scenario di Automazione di Azure - Provisioning di una macchina virtuale di AWS
In questo articolo si apprenderà come sfruttare Automazione di Azure per effettuare il provisioning di una macchina virtuale nella sottoscrizione di Amazon Web Service (AWS) e come assegnare alla VM un nome specifico. Questa operazione è definita in AWS "assegnazione di tag" alla VM.

## <a name="prerequisites"></a>Prerequisiti
È necessario avere un account di automazione di Azure e una sottoscrizione di Amazon Web Services (AWS). Per altre informazioni sulla configurazione di un account di Automazione di Azure e sulla configurazione delle credenziali della sottoscrizione di AWS, vedere l'articolo relativo alla [configurazione dell'autenticazione in Amazon Web Services](automation-config-aws-account.md). Questo account deve essere creato o aggiornato con le credenziali della sottoscrizione di AWS prima di procedere, quando si fa riferimento a questo account nelle sezioni seguenti.

## <a name="deploy-amazon-web-services-powershell-module"></a>Distribuire il modulo di PowerShell di Amazon Web Services
Il Runbook di provisioning della macchina virtuale usa il modulo di PowerShell di AWS per eseguire le operazioni. Usare la procedura seguente per aggiungere il modulo all'account di automazione configurato con le credenziali della sottoscrizione di AWS.  

1. Aprire il Web browser, accedere a [PowerShell Gallery](https://www.powershellgallery.com/packages/AWSPowerShell/) e fare clic sul pulsante **Deploy to Azure Automation** (Distribuisci in Automazione di Azure).<br><br> ![Importazione del modulo PS per AWS](./media/automation-scenario-aws-deployment/powershell-gallery-download-awsmodule.png)
2. Verrà visualizzata la pagina di accesso di Azure e dopo l'autenticazione si verrà indirizzati al portale di Azure e verrà visualizzata la pagina seguente:<br><br> ![Pagina di importazione del modulo](./media/automation-scenario-aws-deployment/deploy-aws-powershell-module-parameters.png)
3. Selezionare l'account di automazione da usare e fare clic su **OK** per avviare la distribuzione.

   > [!NOTE]
   > Quando automazione di Azure importa un modulo di PowerShell, estrae i cmdlet. Le attività non vengono visualizzate fino a quando l'automazione non ha completamente completato l'importazione del modulo e l'estrazione dei cmdlet. Il processo potrebbe richiedere alcuni minuti.  
   > <br>

1. Nel portale di Azure aprire l'account di automazione.
2. Fare clic sul riquadro **Asset** e nel riquadro Asset selezionare **moduli**.
3. Nella pagina Moduli il modulo **AWSPowerShell** viene visualizzato nell'elenco.

## <a name="create-aws-deploy-vm-runbook"></a>Creare un runbook per la distribuzione di una VM in AWS
Dopo la distribuzione del modulo di PowerShell per AWS, è possibile creare un runbook per automatizzare il provisioning di una macchina virtuale in AWS mediante uno script di PowerShell. I passaggi seguenti illustrano come usare uno script di PowerShell nativo in automazione di Azure.  

> [!NOTE]
> Per altre opzioni e altre informazioni su questo script, vedere la [PowerShell Gallery](https://www.powershellgallery.com/packages/New-AwsVM/).
> 

1. Scaricare lo script di PowerShell New-AwsVM dalla PowerShell Gallery aprendo una sessione di PowerShell e digitando il comando seguente:<br>
   ```powershell
   Save-Script -Name New-AwsVM -Path <path>
   ```
   <br>
2. Dal portale di Azure aprire l'account di automazione e selezionare **manuali operativi** in **automazione processi**.  
3. Nella pagina Runbook selezionare **Aggiungi runbook**.
4. Nel riquadro Aggiungi Runbook selezionare **creazione rapida** per creare un nuovo Runbook.
5. Nel riquadro delle proprietà di Runbook digitare un nome per il Runbook.
6. Dall'elenco a discesa **tipo di Runbook** selezionare **PowerShell**e quindi fare clic su **Crea**.<br><br> ![Riquadro Create runbook](./media/automation-scenario-aws-deployment/runbook-quickcreate-properties.png) (Crea runbook)
6. Quando viene visualizzata la pagina Modifica runbook di PowerShell, copiare e incollare lo script di PowerShell nell'area di creazione del runbook.<br><br> ![Script di PowerShell per il runbook](./media/automation-scenario-aws-deployment/runbook-powershell-script.png)<br>
   
    > [!NOTE]
    > Notare quanto segue quando si usa lo script di PowerShell di esempio:
    > 
    > * Il runbook contiene alcuni valori predefiniti dei parametri. Valutare tutti i valori predefiniti e apportare gli aggiornamenti necessari.
    > * Se le credenziali di AWS sono state archiviate come asset credenziali denominato diversamente `AWScred`da, è necessario aggiornare lo script alla riga 57 in modo che corrisponda di conseguenza.  
    > * Quando si utilizzano i comandi dell'interfaccia della riga di comando di AWS in PowerShell, in particolare con questo runbook di esempio, è necessario specificare l'area di AWS. In caso contrario, i cmdlet avranno esito negativo. Per altre informazioni, vedere l'argomento di AWS [Specify AWS Region](https://docs.aws.amazon.com/powershell/latest/userguide/pstools-installing-specifying-region.html) (Specificare l'area di AWS) nel documento relativo agli strumenti di AWS per PowerShell.  
    >

7. Per recuperare un elenco di nomi di immagine dalla sottoscrizione di AWS, avviare PowerShell ISE e importare il modulo di PowerShell per AWS. Eseguire l'autenticazione in AWS sostituendo `Get-AutomationPSCredential` nell'ambiente ISE con `AWScred = Get-Credential`. Questa istruzione richiede le credenziali ed è possibile specificare l'ID della chiave di accesso per il nome utente e la chiave di accesso segreta per la password. 

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
8. Copiare e incollare uno dei nomi di immagine in una variabile di automazione come a cui viene fatto riferimento in `$InstanceType`Runbook come. Poiché in questo esempio si usa la sottoscrizione a livelli gratuita di AWS, si usa **T2. micro** per l'esempio Runbook.  
9. Salvare il runbook, quindi fare clic su **Pubblica** per pubblicare il runbook e quindi fare clic su **Sì** quando richiesto.

### <a name="testing-the-aws-vm-runbook"></a>Test del runbook per la VM per AWS
Prima di procedere con il test del Runbook, è necessario verificare alcuni elementi:

* È stato creato `AWScred` un asset chiamato per l'autenticazione in AWS oppure lo script è stato aggiornato in modo da fare riferimento al nome dell'asset delle credenziali.    
* Il modulo di PowerShell di AWS è stato importato in automazione di Azure.  
* È stato creato un nuovo Runbook e i valori dei parametri sono stati verificati e aggiornati laddove necessario.  
* **Registrare i record dettagliati** e, facoltativamente, **registrare i record di stato** nella registrazione dell'operazione Runbook e la **traccia** è stata impostata **su on**.<br><br> ![Registrazione e traccia](./media/automation-scenario-aws-deployment/runbook-settings-logging-and-tracing.png)Runbook.  

1. Fare clic su **Avvia** per avviare il Runbook, quindi fare clic su **OK** quando si apre il riquadro Avvia Runbook.
2. Nel riquadro Avvia Runbook specificare un nome per la macchina virtuale. Accettare i valori predefiniti per gli altri parametri preconfigurati nello script. Fare clic su **OK** per avviare il processo del runbook.<br><br> ![Avvio del runbook New-AwsVM](./media/automation-scenario-aws-deployment/runbook-start-job-parameters.png)
3. Verrà aperto un riquadro per il processo del runbook creato. Chiudere questo riquadro.
4. È possibile visualizzare lo stato di avanzamento del processo e visualizzare i flussi di output selezionando **tutti i log** nel riquadro del processo Runbook.<br><br> ![Output del flusso](./media/automation-scenario-aws-deployment/runbook-job-streams-output.png)
5. Per confermare che è in corso il provisioning della macchina virtuale, accedere alla console di gestione di AWS se non si è attualmente connessi.<br><br> ![Macchina virtuale distribuita in una console di AWS](./media/automation-scenario-aws-deployment/aws-instances-status.png)

## <a name="next-steps"></a>Passaggi successivi
* Per iniziare a usare manuali operativi grafici, vedere [il primo Runbook grafico](automation-first-runbook-graphical.md).
* Per iniziare a usare manuali operativi del flusso di lavoro PowerShell, vedere [il primo Runbook del flusso di lavoro PowerShell](automation-first-runbook-textual.md).
* Per altre informazioni sui tipi di runbook, i relativi vantaggi e le limitazioni, vedere [Tipi di runbook di Automazione di Azure](automation-runbook-types.md).
* Per altre informazioni sulla funzionalità di supporto degli script PowerShell, vedere [supporto degli script PowerShell nativi in automazione di Azure](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/).