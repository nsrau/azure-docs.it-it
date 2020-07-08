---
title: Bus di servizio di Azure - Aggiornare automaticamente le unità di messaggistica
description: Questo articolo illustra come usare un runbook di automazione di Azure per aggiornare automaticamente le unità di messaggistica di uno spazio dei nomi del bus di servizio.
ms.topic: how-to
ms.date: 06/23/2020
ms.openlocfilehash: 52f5b13b482739bfa56ff606f684fd5a9c7d3b6e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85341498"
---
# <a name="automatically-update-messaging-units-of-an-azure-service-bus-namespace"></a>Aggiornare automaticamente le unità di messaggistica di uno spazio dei nomi del bus di servizio di Azure 
Questo articolo illustra come aggiornare automaticamente le [unità di messaggistica](service-bus-premium-messaging.md) di uno spazio dei nomi del bus di servizio in base all'uso delle risorse (CPU o memoria). 

Nell'esempio riportato in questo articolo viene descritto come aumentare le unità di messaggistica per uno spazio dei nomi del bus di servizio quando l'uso della CPU dello spazio dei nomi supera il 75%. I passaggi più importanti sono:

1. Creare un runbook di Automazione di Azure con uno script di PowerShell che aumenta le unità di messaggistica per uno spazio dei nomi del bus di servizio. 
2. Creare un avviso sull'uso della CPU nello spazio dei nomi del bus di servizio, che richiama lo script di PowerShell quando l'uso della CPU dello spazio dei nomi supera il 75%. 

> [!IMPORTANT]
> Questo articolo si applica solo al livello **Premium** del bus di servizio di Azure. 


## <a name="create-a-service-bus-namespace"></a>Creare uno spazio dei nomi del bus di servizio
Creare uno spazio dei nomi del bus di servizio al livello Premium. Per creare lo spazio dei nomi, seguire i passaggi nell'articolo [Creare uno spazio dei nomi nel portale di Azure](service-bus-quickstart-portal.md#create-a-namespace-in-the-azure-portal). 

## <a name="create-an-azure-automation-account"></a>Creare un account di Automazione di Azure
Creare un account di Automazione di Azure seguendo le istruzioni riportate nell'articolo [Creare un account di Automazione di Azure](../automation/automation-quickstart-create-account.md). 

## <a name="import-azservice-module-from-gallery"></a>Importare il modulo AZ. Service dalla raccolta
Importare i moduli `Az.Accounts` e `Az.ServiceBus` dalla raccolta nell'account di automazione. Il modulo `Az.ServiceBus` dipende dal modulo `Az.Accounts`, che quindi deve essere installato per primo. 

Per istruzioni dettagliate, vedere [Importare un modulo dalla raccolta di moduli](../automation/automation-runbook-gallery.md#import-a-module-from-the-module-gallery-with-the-azure-portal).

## <a name="create-and-publish-a-powershell-runbook"></a>Creare e pubblicare un runbook di PowerShell

1. Creare un runbook di PowerShell seguendo le istruzioni riportate nell'articolo [Creare un runbook di PowerShell](../automation/automation-quickstart-create-runbook.md). 

    Ecco uno script di PowerShell di esempio che è possibile usare per aumentare le unità di messaggistica per uno spazio dei nomi del bus di servizio. Questo script di PowerShell in un runbook di automazione aumenta le unità di messaggistica da 1 a 2, da 2 a 4 o da 4 a 8. I valori consentiti per questa proprietà sono: 1, 2, 4 e 8. È possibile creare un altro runbook per ridurre le unità di messaggistica.

    I parametri **namespaceName** e **resourceGroupName** vengono usati per testare lo script separatamente dallo scenario di avviso. 
    
    Il parametro **WebHookData** consente all'avviso di passare informazioni, come il nome del gruppo di risorse, il nome della risorsa e così via, in fase di esecuzione. 

    ```powershell
    [OutputType("PSAzureOperationResponse")]
    param
    (
        [Parameter (Mandatory=$false)]
        [object] $WebhookData,
    
        [Parameter (Mandatory = $false)]
        [String] $namespaceName,
    
        [Parameter (Mandatory = $false)]
        [String] $resourceGroupName
    )
    
    
    if ($WebhookData)
    {
        # Get the data object from WebhookData
        $WebhookBody = (ConvertFrom-Json -InputObject $WebhookData.RequestBody)
    
        # Get the alert schema ID
        $schemaId = $WebhookBody.schemaId

        # If it's a metric alert
        if ($schemaId -eq "AzureMonitorMetricAlert") {

            # Get the resource group name from the alert context
            $resourceGroupName = $WebhookBody.resourceGroupName
            
            # Get the namespace name from the alert context
            $namespaceName = $WebhookBody.resourceName
        }
    }
    
    # Connect to Azure account
    $connection = Get-AutomationConnection -Name AzureRunAsConnection
    
    while(!($connectionResult) -And ($logonAttempt -le 10))
    {
        $LogonAttempt++
        # Logging in to Azure...
        $connectionResult =    Connect-AzAccount `
                                    -ServicePrincipal `
                                    -Tenant $connection.TenantID `
                                    -ApplicationId $connection.ApplicationID `
                                    -CertificateThumbprint $connection.CertificateThumbprint
    
        Start-Sleep -Seconds 30
    }
    
    # Get the current capacity (number of messaging units) of the namespace
    $sbusns=Get-AzServiceBusNamespace `
        -Name $namespaceName `
        -ResourceGroupName $resourceGroupName
    
    $currentCapacity = $sbusns.Sku.Capacity
    
    # Increase the capacity
    # Capacity can be one of these values: 1, 2, 4, 8
    if ($currentCapacity -eq 1) {
        $newMU = 2
    }
    elseif ($currentCapacity -eq 2) {
        $newMU = 4
    }
    elseif ($currentCapacity -eq 4) {
        $newMU = 8    
    }
    else {
    
    }
    
    # Update the capacity of the namespace
    Set-AzServiceBusNamespace `
            -Location eastus `
            -SkuName Premium `
            -Name $namespaceName `
            -SkuCapacity $newMU `
            -ResourceGroupName $resourceGroupName

    ```
2. [Testare la cartella di lavoro](../automation/manage-runbooks.md#test-a-runbook) specificando i valori per i parametri **namespaceName** e **resourceGroupName**. Verificare che le unità di messaggistica nello spazio dei nomi siano aggiornate. 
3. Dopo aver eseguito il test, [pubblicare la cartella di lavoro](..//automation/manage-runbooks.md#publish-a-runbook) in modo che sia disponibile in un secondo momento per l'aggiunta come un'azione per un avviso nello spazio dei nomi. 

## <a name="create-an-alert-on-the-namespace-to-trigger-the-runbook"></a>Creare un avviso sullo spazio dei nomi per attivare il runbook
Per configurare un avviso sullo spazio dei nomi del bus di servizio per attivare il runbook di automazione creato, vedere l'articolo [Usare un avviso per attivare un runbook di Automazione di Azure](../automation/automation-create-alert-triggered-runbook.md). È ad esempio possibile creare un avviso per la metrica **Uso della CPU per spazio dei nomi** o **Uso delle dimensioni della memoria per spazio dei nomi** e aggiungere un'azione per attivare il runbook di automazione creato. Per informazioni dettagliate su queste metriche, vedere [Metriche di utilizzo delle risorse](service-bus-metrics-azure-monitor.md#resource-usage-metrics).

La procedura seguente illustra come creare un avviso che attiva il runbook di automazione quando lo spazio dei nomi **Utilizzo CPU** supera il **75%** .

1. Nella pagina **Spazio dei nomi del bus di servizio** per lo spazio dei nomi selezionare **Avvisi** nel menu a sinistra e quindi selezionare **+ Nuova regola di avviso** sulla barra degli strumenti. 
    
    ![Pagina Avvisi - Pulsante Nuova regola di avviso](./media/automate-update-messaging-units/alerts-page.png)
2. Nella pagina **Crea regola di avviso** fare clic su **Seleziona condizione**. 

    ![Pagina Crea regola di avviso - Selezione della condizione](./media/automate-update-messaging-units/alert-rule-select-condition.png) 
3. Nella pagina **Configura logica dei segnali** selezionare **CPU** per il segnale. 

    ![Configura logica dei segnali - Selezione della CPU](./media/automate-update-messaging-units/configure-signal-logic.png)
4. Immettere un **valore di soglia** (in questo esempio, è **75%** ) e selezionare **Fine**. 

    ![Configurare il segnale CPU](./media/automate-update-messaging-units/cpu-signal-configuration.png)
5. A questo punto, nella pagina **Crea avviso** fare clic su **Seleziona gruppo di azioni**.
    
    ![Seleziona gruppo di azioni](./media/automate-update-messaging-units/select-action-group-button.png)
6. Selezionare il pulsante **Crea gruppo di azioni** sulla barra degli strumenti. 

    ![Pulsante Crea gruppo di azioni](./media/automate-update-messaging-units/create-action-group-button.png)
7. Nella pagina **Aggiungi gruppo di azioni** procedere come segue:
    1. Immettere un **nome** per il gruppo di azioni. 
    2. Immettere un **nome breve** per il gruppo di azioni.
    3. Selezionare la **sottoscrizione** in cui creare il gruppo di azioni.
    4. Selezionare il **gruppo di risorse**. 
    5. Nella sezione **Azioni** immettere un **nome per l'azione** e selezionare **Runbook di automazione** per **Tipo di azione**. 

        ![Pagina Aggiungi gruppo di azioni](./media/automate-update-messaging-units/add-action-group-page.png)
8. Nella pagina **Configura runbook** procedere come segue:
    1. Per **Origine runbook** selezionare **Utente**. 
    2. Per **Sottoscrizione** selezionare la **sottoscrizione** di Azure che contiene l'account di automazione. 
    3. Per **Account di Automazione** selezionare l'**account di automazione**.
    4. Per **Runbook** selezionare il runbook. 
    5. Selezionare **OK** nella pagina **Configura runbook**. 
        ![Configura runbook](./media/automate-update-messaging-units/configure-runbook.png)
9. Selezionare **OK** nella pagina **Aggiungi gruppo di azioni**. 
5. A questo punto, nella pagina **Crea regola di avviso** immettere un **nome per la regola** e quindi selezionare **Crea regola di avviso**. 
    ![Crea regola di avviso](./media/automate-update-messaging-units/create-alert-rule.png)

    > [!NOTE]
    > A questo punto, quando l'uso della CPU dello spazio dei nomi è superiore al 75%, l'avviso attiva il runbook di automazione, che aumenta le unità di messaggistica dello spazio dei nomi del bus di servizio. Analogamente, è possibile creare un avviso per un altro runbook di automazione, che riduce le unità di messaggistica se l'uso della CPU dello spazio dei nomi scende al di sotto del 25%. 

## <a name="next-steps"></a>Passaggi successivi
Per informazioni sulle unità di messaggistica, vedere [Messaggistica Premium](service-bus-premium-messaging.md)
