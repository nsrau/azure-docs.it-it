---
title: Configurare BYOS (Bring your own Storage) per il profiler & Snapshot Debugger
description: Configurare BYOS (Bring your own Storage) per il profiler & Snapshot Debugger
ms.topic: conceptual
author: renatosalas
ms.author: regutier
ms.date: 04/14/2020
ms.reviewer: mbullwin
ms.openlocfilehash: 50dcd3f438645c99e0ed3cfdded7a101ee5f1852
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/20/2020
ms.locfileid: "86539857"
---
# <a name="configure-bring-your-own-storage-byos-for-application-insights-profiler-and-snapshot-debugger"></a>Configurare Bring your own Storage (BYOS) per Application Insights Profiler e Snapshot Debugger

## <a name="what-is-bring-your-own-storage-byos-and-why-might-i-need-it"></a>Che cos'è Bring your own Storage (BYOS) e perché potrebbe essere necessario? 
Quando si usa Application Insights Profiler o Snapshot Debugger, gli artefatti generati dall'applicazione vengono caricati negli account di archiviazione di Azure tramite la rete Internet pubblica. Tali account sono pagati e controllati da Microsoft per l'elaborazione e l'analisi. Microsoft controlla i criteri di crittografia inattivi e di gestione della durata per tali artefatti.

Con Bring your own storage, questi elementi vengono caricati in un account di archiviazione controllato dall'utente. Ciò significa che è possibile controllare i criteri di crittografia inattivi, i criteri di gestione della durata e l'accesso alla rete. Si sarà tuttavia responsabili dei costi associati all'account di archiviazione.

> [!NOTE]
> Se si Abilita il collegamento privato, è necessario disporre di una risorsa di archiviazione personalizzata. Per ulteriori informazioni sul collegamento privato per Application Insights, [vedere la documentazione di.](../platform/private-link-security.md)
>
> Se si abilitano le chiavi gestite dal cliente, è necessario avere una propria risorsa di archiviazione. Per ulteriori informazioni sulle chiavi gestite dal cliente per Application Insights, [vedere la documentazione.](../platform/customer-managed-keys.md)

## <a name="how-will-my-storage-account-be-accessed"></a>Come verrà eseguito l'accesso all'account di archiviazione?
1. Gli agenti in esecuzione nelle macchine virtuali o nel servizio app caricherà gli artefatti (profili, snapshot e simboli) nei contenitori BLOB nell'account. Questo processo comporta il contatto con il servizio Application Insights Profiler o Snapshot Debugger per ottenere un token di firma di accesso condiviso (SAS) a un nuovo BLOB nell'account di archiviazione.
1. Il servizio Application Insights Profiler o Snapshot Debugger analizzerà il BLOB in ingresso e riscriverà i risultati dell'analisi e i file di log nell'archivio BLOB. A seconda della capacità di calcolo disponibile, questo processo può verificarsi in qualsiasi momento dopo il caricamento.
1. Quando si visualizzano le tracce del profiler o l'analisi del debugger snapshot, il servizio recupererà i risultati dell'analisi dall'archiviazione BLOB.

## <a name="prerequisites"></a>Prerequisiti
* Assicurarsi di creare l'account di archiviazione nello stesso percorso della risorsa Application Insights. Ex. Se la risorsa Application Insights si trova negli Stati Uniti occidentali 2, l'account di archiviazione deve essere anche negli Stati Uniti occidentali 2. 
* Concedere il ruolo "collaboratore dati BLOB di archiviazione" all'applicazione AAD "accesso all'archiviazione Trusted dei servizi di diagnostica" nell'account di archiviazione tramite l'interfaccia utente di controllo di accesso (IAM).
* Se il collegamento privato è abilitato, configurare l'impostazione aggiuntiva per consentire la connessione al servizio Microsoft attendibile dalla rete virtuale. 

## <a name="how-to-enable-byos"></a>Come abilitare BYOS

### <a name="create-storage-account"></a>Creare un account di archiviazione
Creare un nuovo account di archiviazione (se non è già presente) nello stesso percorso della risorsa Application Insights.
Se la risorsa Application Insights è attiva `West US 2` , è necessario che l'account di archiviazione sia in `West US 2` .

### <a name="grant-access-to-diagnostic-services-to-your-storage-account"></a>Concedere l'accesso ai servizi di diagnostica all'account di archiviazione
Un account di archiviazione BYOS verrà collegato a una risorsa Application Insights. Potrebbe essere presente un solo account di archiviazione per ogni risorsa Application Insights ed entrambi devono trovarsi nella stessa posizione. È possibile usare lo stesso account di archiviazione con più di una risorsa Application Insights.

Per prima cosa, è necessario concedere l'accesso all'account di archiviazione all'Application Insights Profiler e al servizio di Snapshot Debugger. Per concedere l'accesso, aggiungere il ruolo `Storage Blob Data Contributor` all'applicazione AAD denominata `Diagnostic Services Trusted Storage Access` tramite la pagina controllo di accesso (IAM) nell'account di archiviazione, come illustrato nella figura 1,0. 

Passaggi: 
1. Fare clic sul pulsante "Aggiungi" nella sezione "aggiungere un'assegnazione di ruolo" 
1. Selezionare il ruolo "Collaboratore ai dati dei BLOB di archiviazione" 
1. Selezionare "Azure AD utente, gruppo o entità servizio" nella sezione "assegnare l'accesso a" 
1. Eseguire la ricerca & selezionare l'app "accesso all'archiviazione attendibile dei servizi diagnostici" 
1. Salvare le modifiche

_ ![ Figura 1,0](media/profiler-bring-your-own-storage/figure-10.png)_ 
 _Figura 1,0_ 

Una volta aggiunto, il ruolo verrà visualizzato nella sezione "assegnazioni di ruolo", come illustrato nella figura seguente 1,1. 
_ ![ Figura 1,1](media/profiler-bring-your-own-storage/figure-11.png)_ 
 _Figura 1,1_ 

Se si usa anche un collegamento privato, è necessaria una configurazione aggiuntiva per consentire la connessione al servizio Microsoft attendibile dalla rete virtuale. Vedere la [documentazione sulla sicurezza della rete di archiviazione](../../storage/common/storage-network-security.md#trusted-microsoft-services).

### <a name="link-your-storage-account-with-your-application-insights-resource"></a>Collegare l'account di archiviazione alla risorsa Application Insights
Per configurare BYOS per la diagnostica a livello di codice (Profiler/Debugger), sono disponibili due opzioni:

* Utilizzo di cmdlet Azure PowerShell
* Uso dell'interfaccia della riga di comando di Azure
* Uso del modello di Azure Resource Manager

#### <a name="configure-using-azure-powershell-cmdlets"></a>Configurare con i cmdlet di Azure PowerShell

1. Assicurarsi di aver installato AZ PowerShell 4.2.0 o versione successiva.

    Per installare Azure PowerShell, vedere la [documentazione ufficiale di Azure PowerShell](/powershell/azure/install-az-ps).

1. Installare l'estensione Application Insights PowerShell.
    ```powershell
    Install-Module -Name Az.ApplicationInsights -Force
    ```

1. Accedi con il tuo account Azure
    ```powershell
    Connect-AzAccount -Subscription "{subscription_id}"
    ```

    Per altre informazioni su come eseguire l'accesso, vedere la [documentazione di Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount).

1. Rimuovere l'account di archiviazione precedente collegato alla risorsa Application Insights.

    Criterio:
    ```powershell
    $appInsights = Get-AzApplicationInsights -ResourceGroupName "{resource_group_name}" -Name "{storage_account_name}"
    Remove-AzApplicationInsightsLinkedStorageAccount -ResourceId $appInsights.Id
    ```

    Esempio:
    ```powershell
    $appInsights = Get-AzApplicationInsights -ResourceGroupName "byos-test" -Name "byos-test-westus2-ai"
    Remove-AzApplicationInsightsLinkedStorageAccount -ResourceId $appInsights.Id
    ```

1. Connettersi all'account di archiviazione con la risorsa Application Insights.
    
    Criterio:
    ```powershell
    $storageAccount = Get-AzStorageAccount -ResourceGroupName "{resource_group_name}" -Name "{storage_account_name}"
    $appInsights = Get-AzApplicationInsights -ResourceGroupName "{resource_group_name}" -Name "{application_insights_name}"
    New-AzApplicationInsightsLinkedStorageAccount -ResourceId $appInsights.Id -LinkedStorageAccountResourceId $storageAccount.Id
    ```

    Esempio:
    ```powershell
    $storageAccount = Get-AzStorageAccount -ResourceGroupName "byos-test" -Name "byosteststoragewestus2"
    $appInsights = Get-AzApplicationInsights -ResourceGroupName "byos-test" -Name "byos-test-westus2-ai"
    New-AzApplicationInsightsLinkedStorageAccount -ResourceId $appInsights.Id -LinkedStorageAccountResourceId $storageAccount.Id
    ```

#### <a name="configure-using-azure-cli"></a>Configurare con l'interfaccia della riga di comando

1. Assicurarsi di aver installato l'interfaccia della riga di comando di Azure.

    Per installare l'interfaccia della riga di comando di Azure, vedere la [documentazione ufficiale dell'interfaccia](/cli/azure/install-azure-cli)della riga di comando

1. Installare l'estensione dell'interfaccia della riga di comando Application Insights.
    ```powershell
    az extension add -n application-insights
    ```

1. Connettersi all'account di archiviazione con la risorsa Application Insights.

    Criterio:
    ```powershell
    az monitor app-insights component linked-storage link --resource-group "{resource_group_name}" --app "{application_insights_name}" --storage-account "{storage_account_name}"
    ```
    
    Esempio:
    ```powershell
    az monitor app-insights component linked-storage link --resource-group "byos-test" --app "byos-test-westus2-ai" --storage-account "byosteststoragewestus2"
    ```
    
    Output previsto:
    ```powershell
    {
      "id": "/subscriptions/{subscription}/resourcegroups/byos-test/providers/microsoft.insights/components/byos-test-westus2-ai/linkedstorageaccounts/serviceprofiler",
      "linkedStorageAccount": "/subscriptions/{subscription}/resourceGroups/byos-test/providers/Microsoft.Storage/storageAccounts/byosteststoragewestus2",
      "name": "serviceprofiler",
      "resourceGroup": "byos-test",
      "type": "microsoft.insights/components/linkedstorageaccounts"
    }
    ```

    > [!NOTE]
    > Per eseguire gli aggiornamenti sugli account di archiviazione collegati alla risorsa Application Insights, vedere la [documentazione dell'interfaccia](/cli/azure/ext/application-insights/monitor/app-insights/component/linked-storage)della riga di comando di Application Insights.

#### <a name="configure-using-azure-resource-manager-template"></a>Configurare usando Azure Resource Manager modello

1. Creare un file di modello di Azure Resource Manager con il contenuto seguente (byos.template.jssu).
    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "applicationinsights_name": {
          "type": "String"
        },
        "storageaccount_name": {
          "type": "String"
        }
      },
      "variables": {},
      "resources": [
        {
          "name": "[concat(parameters('applicationinsights_name'), '/serviceprofiler')]",
          "type": "Microsoft.Insights/components/linkedStorageAccounts",
          "apiVersion": "2020-03-01-preview",
          "properties": {
            "linkedStorageAccount": "[resourceId('Microsoft.Storage/storageAccounts', parameters('storageaccount_name'))]"
          }
        }
      ],
      "outputs": {}
    }
    ```

1. Eseguire il comando di PowerShell seguente per distribuire il modello precedente (creare un account di archiviazione collegato).

    Criterio:
    ```powershell
    New-AzResourceGroupDeployment -ResourceGroupName "{your_resource_name}" -TemplateFile "{local_path_to_arm_template}"
    ```

    Esempio:
    ```powershell
    New-AzResourceGroupDeployment -ResourceGroupName "byos-test" -TemplateFile "D:\Docs\byos.template.json"
    ```

1. Quando richiesto nella console di PowerShell, fornire i parametri seguenti:
    
    |           Parametro           |                                Descrizione                               |
    |-------------------------------|--------------------------------------------------------------------------|
    | application_insights_name     | Nome della risorsa Application Insights per abilitare BYOS.            |
    | storage_account_name          | Nome della risorsa dell'account di archiviazione da usare come BYOS. |
    
    Output previsto:
    ```powershell
    Supply values for the following parameters:
    (Type !? for Help.)
    application_insights_name: byos-test-westus2-ai
    storage_account_name: byosteststoragewestus2
    
    DeploymentName          : byos.template
    ResourceGroupName       : byos-test
    ProvisioningState       : Succeeded
    Timestamp               : 4/16/2020 1:24:57 AM
    Mode                    : Incremental
    TemplateLink            :
    Parameters              :
                              Name                            Type                       Value
                              ==============================  =========================  ==========
                              application_insights_name        String                     byos-test-westus2-ai
                              storage_account_name             String                     byosteststoragewestus2
                              
    Outputs                 :
    DeploymentDebugLogLevel :
    ```

1. Abilitare la diagnostica a livello di codice (Profiler/Debugger) nel carico di lavoro di interesse tramite la portale di Azure. (Servizio app > Application Insights) _ ![ Figura 2,0](media/profiler-bring-your-own-storage/figure-20.png)_ 
 _Figura 2,0_

## <a name="troubleshooting"></a>Risoluzione dei problemi
### <a name="template-schema-schema_uri-isnt-supported"></a>Lo schema di modello ' {schema_uri}' non è supportato.
* Verificare che la `$schema` proprietà del modello sia valida. Deve seguire il modello seguente:`https://schema.management.azure.com/schemas/{schema_version}/deploymentTemplate.json#`
* Verificare che l'oggetto `schema_version` del modello sia compreso nei valori validi: `2014-04-01-preview, 2015-01-01, 2018-05-01, 2019-04-01, 2019-08-01` .
    Messaggio di errore:
    ```powershell
    New-AzResourceGroupDeployment : 11:53:49 AM - Error: Code=InvalidTemplate; Message=Deployment template validation failed: 'Template schema
    'https://schema.management.azure.com/schemas/2020-01-01/deploymentTemplate.json#' is not supported. Supported versions are
    '2014-04-01-preview,2015-01-01,2018-05-01,2019-04-01,2019-08-01'. Please see https://aka.ms/arm-template for usage details.'.
    ```

### <a name="no-registered-resource-provider-found-for-location-location"></a>Non sono stati trovati provider di risorse registrati per la località' {location}'.
* Verificare che la `apiVersion` della risorsa `microsoft.insights/components` sia `2015-05-01` .
* Verificare che la `apiVersion` della risorsa `linkedStorageAccount` sia `2020-03-01-preview` .
    Messaggio di errore:
    ```powershell
    New-AzResourceGroupDeployment : 6:18:03 PM - Resource microsoft.insights/components 'byos-test-westus2-ai' failed with message '{
      "error": {
        "code": "NoRegisteredProviderFound",
        "message": "No registered resource provider found for location 'westus2' and API version '2020-03-01-preview' for type 'components'. The supported api-versions are '2014-04-01,
    2014-08-01, 2014-12-01-preview, 2015-05-01, 2018-05-01-preview'. The supported locations are ', eastus, southcentralus, northeurope, westeurope, southeastasia, westus2, uksouth,
    canadacentral, centralindia, japaneast, australiaeast, koreacentral, francecentral, centralus, eastus2, eastasia, westus, southafricanorth, northcentralus, brazilsouth, switzerlandnorth,
    australiasoutheast'."
      }
    }'
    ```
### <a name="storage-account-location-should-match-ai-component-location"></a>Il percorso dell'account di archiviazione deve corrispondere al percorso del componente intelligenza artificiale.
* Verificare che il percorso della risorsa Application Insights corrisponda all'account di archiviazione.
    Messaggio di errore:
    ```powershell
    New-AzResourceGroupDeployment : 1:01:12 PM - Resource microsoft.insights/components/linkedStorageAccounts 'byos-test-centralus-ai/serviceprofiler' failed with message '{
      "error": {
        "code": "BadRequest",
        "message": "Storage account location should match AI component location",
        "innererror": {
          "trace": [
            "System.ArgumentException"
          ]
        }
      }
    }'
    ```

Per la risoluzione dei problemi generali del profiler, vedere la [documentazione relativa ai problemi del profiler](profiler-troubleshooting.md)

Per informazioni generali Snapshot Debugger sulla risoluzione dei problemi, vedere la [documentazione relativa alla risoluzione dei problemi snapshot debugger](snapshot-debugger-troubleshoot.md). 

## <a name="faqs"></a>Domande frequenti
* Se è abilitato Profiler o snapshot e quindi è stato abilitato BYOS, verrà eseguita la migrazione dei dati nell'account di archiviazione?
    _No._

* BYOS funzionerà con la crittografia inattiva e la chiave gestita dal cliente?
    _Sì, per essere precisi, BYOS è un requisito per abilitare Profiler/Debugger con chiavi Customer-Manager._

* BYOS funzionerà in un ambiente isolato da Internet?
    _Sì. In realtà, BYOS è un requisito per gli scenari di rete isolata._

* BYOS funzionerà quando sono abilitate le chiavi gestite dal cliente e il collegamento privato? 
    _Sì, può essere possibile._

* Se è stato abilitato BYOS, è possibile tornare indietro usando gli account di archiviazione dei servizi diagnostici per archiviare i dati raccolti? 
    _Sì, è possibile, ma in questo momento non è supportata la migrazione dei dati da BYOS._

* Dopo l'abilitazione di BYOS, verranno presi in riscontro tutti i costi correlati, ovvero l'archiviazione e la rete? 
    _Sì_
