---
title: " Risolvere gli avvisi delle VM di Azure con runbook di automazione | Documentazione Microsoft"
description: Questo articolo illustra come integrare gli avvisi delle macchine virtuali di Azure con i runbook di Automazione di Azure e risolvere automaticamente problemi.
services: automation
documentationcenter: 
author: mgoedtel
manager: jwhit
editor: tysonn
ms.assetid: 1f7baa7f-7283-4a4f-9385-3f5cd1062c7f
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/14/2016
ms.author: csand;magoedte
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 9abb6ee8eed01ef84ee10fc2c70ea23bf482dd1c


---
# <a name="azure-automation-scenario---remediate-azure-vm-alerts"></a>Scenario di Automazione di Azure: risolvere gli avvisi delle macchine virtuali di Azure
In Automazione di Azure e Macchine virtuali di Azure è stata rilasciata una nuova funzionalità che consente di configurare avvisi delle macchine virtuali (VM) per l'esecuzione di runbook di automazione. Questa nuova funzionalità consente di eseguire automaticamente correzioni standard in risposta ad avvisi della VM, ad esempio per riavviarla o arrestarla.

In precedenza, durante la creazione della regola di avviso della macchina virtuale era possibile [specificare un webhook di automazione](https://azure.microsoft.com/blog/using-azure-automation-to-take-actions-on-azure-alerts/) a un runbook per l'esecuzione di quest'ultimo a ogni attivazione dell'avviso. Era tuttavia necessario creare il runbook e il webhook per il runbook e quindi copiare e incollare il webhook durante la creazione della regola di avviso. Con questa nuova versione il processo è molto più semplice, perché è possibile scegliere un runbook direttamente da un elenco durante la creazione della regola di avviso e scegliere un account di automazione che eseguirà il runbook o creare facilmente un account.

Questo articolo illustra come è possibile configurare facilmente un avviso delle VM di Azure e configurare un runbook di automazione da eseguire ogni volta che viene attivato l'avviso. Gli scenari di esempio includono il riavvio di una VM quando l'utilizzo della memoria supera una soglia a causa di un'applicazione nella VM con una perdita di memoria o l'arresto di una VM quando il tempo di CPU dell'utente è sceso sotto l'1% nell'ora precedente e non è in uso. Verrà anche illustrato come la creazione automatica di un'entità servizio nell'account di automazione semplifichi l'uso di runbook nel processo di correzione degli avvisi di Azure.

## <a name="create-an-alert-on-a-vm"></a>Creare un avviso in una VM
Per configurare un avviso per l'avvio di un runbook quando la soglia corrispondente viene raggiunta, eseguire questa procedura.

> [!NOTE]
> Con questa versione sono supportate solo le macchine virtuali V2, mentre il supporto per le VM classiche verrà aggiunto a breve.  
> 
> 

1. Accedere al portale di Azure e fare clic su **Macchine virtuali**.  
2. Selezionare una macchina virtuale.  Verrà visualizzato il pannello del dashboard della macchina virtuale e il pannello **Impostazioni** a destra.  
3. Nel pannello **Impostazioni** selezionare **Regole di avviso** nella sezione Monitoraggio.
4. Nel pannello **Regole di avviso** fare clic su **Aggiungi avviso**.

Verrà visualizzato il pannello **Aggiungi una regola di avviso** dove è possibile configurare le condizioni per l'avviso e scegliere una o tutte queste opzioni: inviare un messaggio di posta elettronica a un utente, usare un webhook per inoltrare l'avviso a un altro sistema, e/o eseguire un runbook di automazione nel tentativo di risolvere il problema.

## <a name="configure-a-runbook"></a>Configurare un runbook
Per configurare un runbook da eseguire quando viene raggiunta la soglia di avviso della VM, selezionare **Runbook di automazione**. Nel pannello **Configura runbook** è possibile selezionare il runbook da eseguire e l'account di automazione in cui eseguire il runbook.

![Configurare un runbook di automazione e creare un nuovo account di automazione](media/automation-azure-vm-alert-integration/ConfigureRunbookNewAccount.png)

> [!NOTE]
> Per questa versione è possibile scegliere fra tre runbook forniti dal servizio: Restart VM, Stop VM o Remove VM (elimina).  In una versione futura sarà possibile selezionare altri runbook o uno dei runbook personalizzati.
> 
> 

![Runbook da scegliere](media/automation-azure-vm-alert-integration/RunbooksToChoose.png)

Dopo aver selezionato uno dei tre runbook disponibili, viene visualizzato l'elenco a discesa **Account di automazione** dove è possibile selezionare un account di automazione per l'esecuzione del runbook. I runbook devono essere eseguiti nel contesto di un [account di automazione](automation-security-overview.md) presente nella sottoscrizione di Azure. È possibile selezionare un account di automazione già creato o crearne automaticamente uno nuovo.

I runbook forniti si autenticano in Azure con un'entità servizio. Se si sceglie di eseguire il runbook in uno degli account di automazione esistenti, l'entità servizio verrà creata automaticamente. Se si sceglie di creare un nuovo account di automazione, verranno creati automaticamente l'account e l'entità servizio. In entrambi i casi, nell'account di automazione verranno creati anche due asset, ovvero un asset del certificato denominato **AzureRunAsCertificate** e un asset della connessione denominato **AzureRunAsConnection**. I runbook useranno **AzureRunAsConnection** per l'autenticazione in Azure per eseguire l'azione di gestione nella VM.

> [!NOTE]
> All'entità servizio creata nell'ambito della sottoscrizione viene assegnato il ruolo Collaboratore. Questo ruolo è necessario per concedere all'account l'autorizzazione di esecuzione dei runbook di automazione per gestire le VM di Azure.  La creazione di un account di automazione e/o di un'entità servizio è un evento singolo. Una volta creati, si potrà usare l'account per eseguire i runbook per altri avvisi delle VM di Azure.
> 
> 

Quando fa clic su **OK** , l'avviso viene configurato e se è stata selezionata l'opzione per creare un nuovo account di automazione, questo verrà creato insieme all'entità servizio.  Il completamento di questa operazione può richiedere alcuni secondi.  

![Runbook in corso di configurazione](media/automation-azure-vm-alert-integration/RunbookBeingConfigured.png)

Una volta completata la configurazione, il nome del runbook verrà visualizzato nel pannello **Aggiungi una regola di avviso** .

![Runbook configurati](media/automation-azure-vm-alert-integration/RunbookConfigured.png)

Fare clic su **OK** nel pannello **Aggiungi una regola di avviso**. La regola di avviso verrà creata e attivata se la macchina virtuale è in esecuzione.

### <a name="enable-or-disable-a-runbook"></a>Abilitare o disabilitare un runbook
Se è stato configurato un runbook per un avviso, è possibile disabilitarlo senza rimuovere la configurazione del runbook. In questo modo si può mantenere l'avviso di esecuzione, testare eventualmente alcune regole di avviso e successivamente riabilitare il runbook.

## <a name="create-a-runbook-that-works-with-an-azure-alert"></a>Creare un runbook che funzioni con un avviso di Azure
Quando si sceglie un runbook nell'ambito di una regola di avviso di Azure, il runbook deve contenere la logica per gestire i dati di avviso che gli vengono passati.  Quando un runbook viene configurato in una regola di avviso, viene creato un webhook per il runbook. Il webhook viene usato per avviare il runbook ogni volta che viene attivato l'avviso.  La chiamata effettiva per avviare il runbook è una richiesta HTTP POST all'URL del webhook. Il corpo della richiesta POST contiene un oggetto in formato JSON con proprietà utili relative all'avviso.  Come mostrato di seguito, i dati dell'avviso contengono dettagli come subscriptionID resourceGroupName, resourceName e resourceType.

### <a name="example-of-alert-data"></a>Esempio di dati di avviso
```
{
    "WebhookName": "AzureAlertTest",
    "RequestBody": "{
    \"status\":\"Activated\",
    \"context\": {
        \"id\":\"/subscriptions/<subscriptionId>/resourceGroups/MyResourceGroup/providers/microsoft.insights/alertrules/AlertTest\",
        \"name\":\"AlertTest\",
        \"description\":\"\",
        \"condition\": {
            \"metricName\":\"CPU percentage guest OS\",
            \"metricUnit\":\"Percent\",
            \"metricValue\":\"4.26337916666667\",
            \"threshold\":\"1\",
            \"windowSize\":\"60\",
            \"timeAggregation\":\"Average\",
            \"operator\":\"GreaterThan\"},
        \"subscriptionId\":\<subscriptionID> \",
        \"resourceGroupName\":\"TestResourceGroup\",
        \"timestamp\":\"2016-04-24T23:19:50.1440170Z\",
        \"resourceName\":\"TestVM\",
        \"resourceType\":\"microsoft.compute/virtualmachines\",
        \"resourceRegion\":\"westus\",
        \"resourceId\":\"/subscriptions/<subscriptionId>/resourceGroups/TestResourceGroup/providers/Microsoft.Compute/virtualMachines/TestVM\",
        \"portalLink\":\"https://portal.azure.com/#resource/subscriptions/<subscriptionId>/resourceGroups/TestResourceGroup/providers/Microsoft.Compute/virtualMachines/TestVM\"
        },
    \"properties\":{}
    }",
    "RequestHeader": {
        "Connection": "Keep-Alive",
        "Host": "<webhookURL>"
    }
}
```

Quando il servizio di webhook di Automazione riceve la richiesta HTTP POST, estrae i dati di avviso e li passa al runbook nel parametro di input runbook WebhookData.  Di seguito viene riportato un runbook di esempio che illustra come usare il parametro WebhookData, estrarre i dati di avviso e usarli per gestire le risorse di Azure che hanno attivato l'avviso.

### <a name="example-runbook"></a>Runbook di esempio
```
#  This runbook will restart an ARM (V2) VM in response to an Azure VM alert.

[OutputType("PSAzureOperationResponse")]

param ( [object] $WebhookData )

if ($WebhookData)
{
    # Get the data object from WebhookData
    $WebhookBody = (ConvertFrom-Json -InputObject $WebhookData.RequestBody)

    # Assure that the alert status is 'Activated' (alert condition went from false to true)
    # and not 'Resolved' (alert condition went from true to false)
    if ($WebhookBody.status -eq "Activated")
    {
        # Get the info needed to identify the VM
        $AlertContext = [object] $WebhookBody.context
        $ResourceName = $AlertContext.resourceName
        $ResourceType = $AlertContext.resourceType
        $ResourceGroupName = $AlertContext.resourceGroupName
        $SubId = $AlertContext.subscriptionId

        # Assure that this is the expected resource type
        Write-Verbose "ResourceType: $ResourceType"
        if ($ResourceType -eq "microsoft.compute/virtualmachines")
        {
            # This is an ARM (V2) VM

            # Authenticate to Azure with service principal and certificate
            $ConnectionAssetName = "AzureRunAsConnection"
            $Conn = Get-AutomationConnection -Name $ConnectionAssetName
            if ($Conn -eq $null) {
                throw "Could not retrieve connection asset: $ConnectionAssetName. Check that this asset exists in the Automation account."
            }
            Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint | Write-Verbose
            Set-AzureRmContext -SubscriptionId $SubId -ErrorAction Stop | Write-Verbose

            # Restart the VM
            Restart-AzureRmVM -Name $ResourceName -ResourceGroupName $ResourceGroupName
        } else {
            Write-Error "$ResourceType is not a supported resource type for this runbook."
        }
    } else {
        # The alert status was not 'Activated' so no action taken
        Write-Verbose ("No action taken. Alert status: " + $WebhookBody.status)
    }
} else {
    Write-Error "This runbook is meant to be started from an Azure alert only."
}
```

## <a name="summary"></a>Riepilogo
Quando si configura un avviso in una VM di Azure, è possibile configurare facilmente un runbook di automazione per eseguire automaticamente un'azione di correzione quando viene attivato l'avviso. Per questa versione è possibile scegliere un runbook per riavviare, arrestare o eliminare una macchina virtuale, a seconda dello scenario avviso. Questo è solo l'inizio dell'abilitazione di scenari in cui si controllano le azioni, come notifica, risoluzione dei problemi e correzione, che verranno eseguite automaticamente quando viene attivato un avviso.

## <a name="next-steps"></a>Passaggi successivi
* Per iniziare a usare runbook grafici, vedere [Il primo runbook grafico](automation-first-runbook-graphical.md)
* Per iniziare a usare i runbook del flusso di lavoro PowerShell, vedere [Il primo runbook PowerShell](automation-first-runbook-textual.md)
* Per altre informazioni sui tipi di runbook, i relativi vantaggi e le limitazioni, vedere [Tipi di runbook di Automazione di Azure](automation-runbook-types.md)




<!--HONumber=Nov16_HO3-->


