---
title: Automazione della distribuzione di una macchina virtuale in Amazon Web Services
description: Questo articolo illustra come usare Automazione di Azure per automatizzare la creazione di una VM di Amazon Web Service
services: automation
ms.subservice: process-automation
ms.date: 03/16/2018
ms.topic: conceptual
ms.openlocfilehash: 52a887d2d934aa2f7e13f0c2fdb4332066aee0e7
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/17/2020
ms.locfileid: "81604837"
---
# <a name="azure-automation-scenario---provision-an-aws-virtual-machine"></a>Scenario di Automazione di Azure - Provisioning di una macchina virtuale di AWS
In questo articolo si apprenderà come sfruttare Automazione di Azure per effettuare il provisioning di una macchina virtuale nella sottoscrizione di Amazon Web Service (AWS) e come assegnare alla VM un nome specifico. Questa operazione è definita in AWS "assegnazione di tag" alla VM.

## <a name="prerequisites"></a>Prerequisiti
È necessario disporre di un account di automazione di Azure e di una sottoscrizione Amazon Web Services (AWS). Per altre informazioni sulla configurazione di un account di Automazione di Azure e sulla configurazione delle credenziali della sottoscrizione di AWS, vedere l'articolo relativo alla [configurazione dell'autenticazione in Amazon Web Services](automation-config-aws-account.md). Questo account deve essere creato o aggiornato con le credenziali dell'abbonamento AWS prima di procedere, poiché fai riferimento a questo account nelle sezioni seguenti.

## <a name="deploy-amazon-web-services-powershell-module"></a>Distribuire il modulo di PowerShell di Amazon Web Services
Il runbook per il provisioning delle macchine virtuali usa il modulo AWS PowerShell per svolgere il proprio lavoro. Segui i passaggi seguenti per aggiungere il modulo al tuo account Automation configurato con le credenziali dell'abbonamento AWS.  

1. Aprire il Web browser, accedere a [PowerShell Gallery](https://www.powershellgallery.com/packages/AWSPowerShell/) e fare clic sul pulsante **Deploy to Azure Automation** (Distribuisci in Automazione di Azure).<br><br> ![Importazione del modulo PS per AWS](./media/automation-scenario-aws-deployment/powershell-gallery-download-awsmodule.png)
2. Verrà visualizzata la pagina di accesso di Azure e dopo l'autenticazione si verrà indirizzati al portale di Azure e verrà visualizzata la pagina seguente:<br><br> ![Pagina di importazione del modulo](./media/automation-scenario-aws-deployment/deploy-aws-powershell-module-parameters.png)
3. Selezionare l'account di automazione da utilizzare e fare clic **su OK** per avviare la distribuzione.

   > [!NOTE]
   > Quando Automazione di Azure importa un modulo di PowerShell, estrae i cmdlet. Le attività non vengono visualizzate fino a quando l'automazione non ha completato completamente l'importazione del modulo e l'estrazione dei cmdlet. Il processo potrebbe richiedere alcuni minuti.  
   > <br>

1. Nel portale di Azure aprire l'account di automazione.
2. Fare clic sul riquadro **Risorse** e nel riquadro Risorse selezionare **Moduli**.
3. Nella pagina Moduli il modulo **AWSPowerShell** viene visualizzato nell'elenco.

## <a name="create-aws-deploy-vm-runbook"></a>Creare un runbook per la distribuzione di una VM in AWS
Dopo la distribuzione del modulo di PowerShell per AWS, è possibile creare un runbook per automatizzare il provisioning di una macchina virtuale in AWS mediante uno script di PowerShell. I passaggi seguenti illustrano come usare lo script di PowerShell nativo in Automazione di Azure.The steps below demonstrate how to use native PowerShell script in Azure Automation.  

> [!NOTE]
> Per altre opzioni e altre informazioni su questo script, vedere la [PowerShell Gallery](https://www.powershellgallery.com/packages/New-AwsVM/).
> 

1. Scaricare lo script di PowerShell New-AWSVM da PowerShell Gallery aprendo una sessione di PowerShell e digitando il comando seguente:<br>
   ```powershell
   Save-Script -Name New-AwsVM -Path <path>
   ```
   <br>
2. Nel portale di Azure aprire l'account di automazione e selezionare **Runbooks** in **Process Automation**.  
3. Nella pagina Runbook selezionare **Aggiungi runbook**.
4. Nel riquadro Aggiungi un runbook selezionare **Creazione rapida** per creare un nuovo runbook.
5. Nel riquadro Proprietà Runbook digitare un nome per il runbook.
6. Nell'elenco a discesa **Tipo di runbook** selezionare **PowerShell**e quindi fare clic su **Crea**.<br><br> ![Riquadro Create runbook](./media/automation-scenario-aws-deployment/runbook-quickcreate-properties.png) (Crea runbook)
6. Quando viene visualizzata la pagina Modifica runbook di PowerShell, copiare e incollare lo script di PowerShell nell'area di creazione del runbook.<br><br> ![Script di PowerShell per il runbook](./media/automation-scenario-aws-deployment/runbook-powershell-script.png)<br>
   
    > [!NOTE]
    > Notare quanto segue quando si usa lo script di PowerShell di esempio:
    > 
    > * Il runbook contiene alcuni valori predefiniti dei parametri. Valutare tutti i valori predefiniti e apportare gli aggiornamenti necessari.
    > * Se le credenziali AWS sono state archiviate come `AWScred`asset delle credenziali con un nome diverso rispetto a , è necessario aggiornare lo script alla riga 57 in modo che corrisponda di conseguenza.  
    > * Quando si utilizzano i comandi dell'interfaccia della riga di comando di AWS in PowerShell, in particolare con questo runbook di esempio, è necessario specificare l'area di AWS. In caso contrario, i cmdlet avranno esito negativo. Per altre informazioni, vedere l'argomento di AWS [Specify AWS Region](https://docs.aws.amazon.com/powershell/latest/userguide/pstools-installing-specifying-region.html) (Specificare l'area di AWS) nel documento relativo agli strumenti di AWS per PowerShell.  
    >

7. Per recuperare un elenco di nomi di immagine dalla sottoscrizione di AWS, avviare PowerShell ISE e importare il modulo di PowerShell per AWS. Eseguire l'autenticazione `Get-AutomationPSCredential` con AWS sostituendo `AWScred = Get-Credential`nell'ambiente ISE con . Questa istruzione richiede le credenziali ed è possibile fornire l'ID della chiave di accesso per il nome utente e la chiave di accesso segreta per la password. 

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
8. Copiare e incollare uno dei nomi di immagine in una `$InstanceType`variabile di automazione come riferimento nel runbook come . Poiché, in questo esempio, si utilizza la sottoscrizione a livelli AWS gratuita, si utilizza **t2.micro** per l'esempio di runbook.  
9. Salvare il runbook, quindi fare clic su **Pubblica** per pubblicare il runbook e quindi fare clic su **Sì** quando richiesto.

### <a name="testing-the-aws-vm-runbook"></a>Test del runbook per la VM per AWS
Prima di procedere con il test del runbook, è necessario verificare alcuni aspetti:Before you proceed with testing the runbook, you need to verify a few things:

* È stata `AWScred` creata una risorsa chiamata per l'autenticazione con AWS oppure lo script è stato aggiornato per fare riferimento al nome dell'asset delle credenziali.    
* Il modulo AWS PowerShell è stato importato in Automazione di Azure.The AWS PowerShell module has been imported in Azure Automation.  
* È stato creato un nuovo runbook e i valori dei parametri sono stati verificati e aggiornati dove necessario.  
* **Registrare i record dettagliati** e, facoltativamente, **Registrare i record di stato** nell'operazione del runbook Registrazione e **traccia** sono stati impostati su **Attivato**.<br><br> ![Registrazione e traccia](./media/automation-scenario-aws-deployment/runbook-settings-logging-and-tracing.png)runbook .  

1. Fare clic su **Start** per avviare il runbook, quindi fare clic **su OK** all'apertura del riquadro Avvia Runbook.
2. Nel riquadro Avvia Runbook specificare un nome di macchina virtuale. Accettare i valori predefiniti per gli altri parametri preconfigurati nello script. Fare clic su **OK** per avviare il processo del runbook.<br><br> ![Avvio del runbook New-AwsVM](./media/automation-scenario-aws-deployment/runbook-start-job-parameters.png)
3. Viene aperto un riquadro Processo per il processo runbook creato. Chiudere questo riquadro.
4. È possibile visualizzare lo stato del processo e visualizzare i flussi di output selezionando **Tutti i registri** nel riquadro Processo del runbook.<br><br> ![Output del flusso](./media/automation-scenario-aws-deployment/runbook-job-streams-output.png)
5. Per verificare che sia in corso il provisioning della macchina virtuale, accedi alla Console di gestione AWS se non sei attualmente connesso.<br><br> ![Macchina virtuale distribuita in una console di AWS](./media/automation-scenario-aws-deployment/aws-instances-status.png)

## <a name="next-steps"></a>Passaggi successivi
* Per iniziare a utilizzare i runbook grafici, vedere [Il mio primo runbook grafico](automation-first-runbook-graphical.md).
* Per iniziare a usare i runbook del flusso di lavoro di PowerShell, vedere [Il mio primo runbook del flusso di lavoro](automation-first-runbook-textual.md)di PowerShell .
* Per altre informazioni sui tipi di runbook e sui relativi vantaggi e limitazioni, vedere [Tipi di runbook di Automazione di Azure.](automation-runbook-types.md)
* Per altre informazioni sulla funzionalità di supporto degli script di PowerShell, vedere Supporto di [script di PowerShell nativi in Automazione di Azure.For](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/)more information on PowerShell script support feature, see Native PowerShell script support in Azure Automation .