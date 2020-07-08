---
title: Gestire le connessioni in Automazione di Azure
description: L'articolo descrive come gestire le connessioni di Automazione di Azure a servizi esterni o applicazioni e come usarle per lavorare nei runbook.
services: automation
ms.subservice: shared-capabilities
ms.date: 01/13/2020
ms.topic: conceptual
ms.custom: has-adal-ref
ms.openlocfilehash: 3c5901dbd45cc0ce82c7fcd8117705eaeed7b4ba
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.contentlocale: it-IT
ms.lasthandoff: 05/25/2020
ms.locfileid: "83837128"
---
# <a name="manage-connections-in-azure-automation"></a>Gestire le connessioni in Automazione di Azure

Un asset di connessione di Automazione di Azure contiene le informazioni elencate di seguito. Queste informazioni sono necessarie per la connessione a un servizio esterno o a un'applicazione da un runbook o una configurazione DSC. 

* Informazioni necessarie per l'autenticazione, come nome utente e password
* Informazioni di connessione, come URL o porta

L'asset di connessione raggruppa tutte le proprietà per la connessione a una particolare applicazione, rendendo superflua la creazione di più variabili. L'utente può modificare i valori per una connessione in un'unica posizione e può passare il nome di una connessione a un runbook o a una configurazione DSC in un singolo parametro. Il runbook o la configurazione accede alle proprietà per una connessione usando il cmdlet `Get-AutomationConnection` interno.

Quando si crea una connessione è necessario specificarne il tipo. Il tipo di connessione è un modello che definisce un set di proprietà. È possibile aggiungere un tipo di connessione ad Automazione di Azure usando un modulo di integrazione con un file di metadati. È possibile anche creare un tipo di connessione con l'[API di Automazione di Azure](/previous-versions/azure/reference/mt163818(v=azure.100)) se il modulo di integrazione include un tipo di connessione e viene importato nell'account di Automazione. 

>[!NOTE]
>Gli asset sicuri in Automazione di Azure includono credenziali, certificati, connessioni e variabili crittografate. Tali asset vengono crittografati e archiviati in Automazione di Azure usando una chiave univoca generata per ogni account di Automazione. Automazione di Azure memorizza la chiave nel Key Vault gestito dal sistema. Prima di archiviare un asset sicuro, Automazione carica la chiave dal Key Vault e quindi la usa per crittografare l'asset. 

## <a name="connection-types"></a>Tipi di connessione

In Automazione di Azure sono disponibili i tipi seguenti di connessione:

* `Azure`: rappresenta una connessione usata per gestire le risorse classiche.
* `AzureServicePrincipal`: rappresenta una connessione usata dall'account RunAs di Azure.
* `AzureClassicCertificate`: rappresenta una connessione usata dal classico account RunAs di Azure.

Nella maggior parte dei casi non è necessario creare una risorsa di connessione poiché viene creata quando si crea un [account RunAs](manage-runas-account.md).

## <a name="powershell-cmdlets-to-access-connections"></a>Cmdlet di PowerShell per accedere alle connessioni

I cmdlet nella tabella seguente vengono usati per creare e gestire le connessioni di Automazione con PowerShell. Sono inclusi nei [moduli Az](shared-resources/modules.md#az-modules).

|Cmdlet|Descrizione|
|---|---|
|[Get-AzAutomationConnection](https://docs.microsoft.com/powershell/module/az.automation/get-azautomationconnection?view=azps-3.7.0)|Recupera informazioni su una connessione.|
|[New-AzAutomationConnection](https://docs.microsoft.com/powershell/module/az.automation/new-azautomationconnection?view=azps-3.7.0)|Crea una nuova connessione.|
|[Remove-AzAutomationConnection](https://docs.microsoft.com/powershell/module/Az.Automation/Remove-AzAutomationConnection?view=azps-3.7.0)|Rimuove una connessione esistente.|
|[Set-AzAutomationConnectionFieldValue](https://docs.microsoft.com/powershell/module/Az.Automation/Set-AzAutomationConnectionFieldValue?view=azps-3.7.0)|Imposta il valore di un campo specifico per una connessione esistente.|

## <a name="internal-cmdlets-to-access-connections"></a>Cmdlet interni per accedere alle connessioni

Il cmdlet interno nella tabella seguente viene usato per accedere alle connessioni nei runbook e nelle configurazioni DSC. Questo cmdlet include il modulo globale `Orchestrator.AssetManagement.Cmdlets`. Per altre informazioni, vedere [Cmdlet interni](shared-resources/modules.md#internal-cmdlets).

|Cmdlet interno|Descrizione|
|---|---|
|`Get-AutomationConnection` | Recupera i valori dei diversi campi nella connessione e li restituisce come una [tabella hash](https://go.microsoft.com/fwlink/?LinkID=324844). È quindi possibile usare tale tabella hash con i comandi appropriati nel runbook o nella configurazione DSC.|

>[!NOTE]
>Evitare di usare variabili con il parametro `Name` di `Get-AutomationConnection`. In questo caso l'uso delle variabili può complicare l'individuazione delle dipendenze tra runbook o configurazioni DSC e asset di connessione in fase di progettazione.

## <a name="python-2-functions-to-access-connections"></a>Funzioni di Python 2 per accedere alle connessioni

La funzione nella tabella seguente viene usata per accedere alle connessioni in un runbook Python 2.

| Funzione | Descrizione |
|:---|:---|
| `automationassets.get_automation_connection` | Recupera una connessione. Restituisce un dizionario con le proprietà della connessione. |

> [!NOTE]
> È necessario importare il modulo `automationassets` nella parte superiore del runbook Python per accedere alle funzioni dell'asset.

## <a name="create-a-new-connection"></a>Crea una nuova connessione

### <a name="create-a-new-connection-with-the-azure-portal"></a>Creare una nuova connessione con il portale di Azure

Per creare una nuova connessione nel portale di Azure:

1. Dall'account di Automazione, fare clic su **Connessioni** in **Risorse condivise**.
2. Nella pagina Connessioni, fare clic su **+ Aggiungi connessione**.
4. Nel campo **Tipo** nel riquadro Nuova connessione, selezionare il tipo di connessione da creare. Le opzioni disponibili sono `Azure`, `AzureServicePrincipal` e `AzureClassicCertificate`. 
5. Il modulo presenta le proprietà per il tipo di connessione selezionato. Completare il modulo e fare clic su **Crea** per salvare la nuova connessione.

### <a name="create-a-new-connection-with-windows-powershell"></a>Creare una nuova connessione con Windows PowerShell

Creare una nuova connessione con Windows PowerShell usando il cmdlet `New-AzAutomationConnection`. Questo cmdlet presenta un parametro `ConnectionFieldValues` che prevede una tabella hash che definisce i valori per ciascuna proprietà definita dal tipo di connessione.

È possibile usare i comandi di esempio seguenti come alternativa alla creazione dell'account RunAs dal portale per creare un nuovo asset di connessione.

```powershell
$ConnectionAssetName = "AzureRunAsConnection"
$ConnectionFieldValues = @{"ApplicationId" = $Application.ApplicationId; "TenantId" = $TenantID.TenantId; "CertificateThumbprint" = $Cert.Thumbprint; "SubscriptionId" = $SubscriptionId}
New-AzAutomationConnection -ResourceGroupName $ResourceGroup -AutomationAccountName $AutomationAccountName -Name $ConnectionAssetName -ConnectionTypeName AzureServicePrincipal -ConnectionFieldValues $ConnectionFieldValues
```

Quando si crea l'account di Automazione, per impostazione predefinita vengono inclusi diversi moduli globali insieme al tipo di connessione `AzureServicePrincipal` per creare l'asset di connessione `AzureRunAsConnection`. Se si tenta di creare un nuovo asset di connessione a un servizio o a un'applicazione con un metodo di autenticazione diverso, la creazione avrà esito negativo in quanto il tipo di connessione non è già definito nell'account di Automazione. Per altre informazioni sulla creazione di un tipo di connessione personale per un modulo personalizzato, vedere [Aggiungere un tipo di connessione](#add-a-connection-type).

## <a name="add-a-connection-type"></a>Aggiungere un tipo di connessione

Se il runbook o la configurazione DSC si connette a un servizio esterno, è necessario definire un tipo di connessione in un [modulo personalizzato](shared-resources/modules.md#custom-modules) denominato modulo di integrazione. Questo modulo include un file di metadati che specifica le proprietà del tipo di connessione denominato **&lt;NomeModulo&gt;-Automation.json**, situato nella cartella dei moduli del file **ZIP**. Questo file contiene i campi di una connessione necessari per connettersi al sistema o al servizio che il modulo rappresenta. Usando questo file è possibile impostare i nomi dei campi, i tipi di dati, lo stato della crittografia e uno stato opzionale per il tipo di connessione. 

L'esempio seguente rappresenta un modello nel formato di file **.json** che definisce le proprietà nome utente e password per un tipo di connessione personalizzato denominato `MyModuleConnection`:

```json
{
   "ConnectionFields": [
   {
      "IsEncrypted":  false,
      "IsOptional":  true,
      "Name":  "Username",
      "TypeName":  "System.String"
   },
   {
      "IsEncrypted":  true,
      "IsOptional":  false,
      "Name":  "Password",
      "TypeName":  "System.String"
   }
   ],
   "ConnectionTypeName":  "MyModuleConnection",
   "IntegrationModuleName":  "MyModule"
}
```

## <a name="get-a-connection-in-a-runbook-or-dsc-configuration"></a>Stabilire una connessione in un runbook o in una configurazione DSC

Recuperare una connessione in un runbook o in una configurazione DSC con il cmdlet `Get-AutomationConnection` interno. Questo cmdlet è preferibile rispetto al cmdlet `Get-AzAutomationConnection` in quanto recupera i valori di connessione anziché le informazioni sulla connessione. 

### <a name="textual-runbook-example"></a>Esempio di runbook testuale

L'esempio seguente mostra come usare l'account RunAs per eseguire l'autenticazione con le risorse di Azure Resource Manager nel runbook. Usa un asset di connessione per rappresentare l'account RunAs, che fa riferimento all'entità servizio basata su certificato.

```powershell
$Conn = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
```

### <a name="graphical-runbook-examples"></a>Esempi di runbook grafico

È possibile aggiungere un'attività per il cmdlet `Get-AutomationConnection` interno a un runbook grafico. Fare clic con il pulsante destro del mouse sulla connessione nel riquadro Libreria dell'editor grafico e selezionare **Aggiungi ad area di disegno**.

![Aggiunta all'area di disegno](media/automation-connections/connection-add-canvas.png)

L'immagine seguente mostra un esempio dell'uso di un oggetto di connessione in un runbook grafico. Questo esempio usa il set di dati `Constant value` per l'attività `Get RunAs Connection`, che usa un oggetto di connessione per l'autenticazione. Viene usato un [collegamento pipeline](automation-graphical-authoring-intro.md#use-links-for-workflow) poiché il parametro `ServicePrincipalCertificate` impostato prevede un singolo oggetto.

![Recupero delle connessioni](media/automation-connections/automation-get-connection-object.png)

### <a name="python-2-runbook-example"></a>Esempio di runbook Python 2

L'esempio seguente illustra come eseguire l'autenticazione usando la connessione RunAs in un runbook Python 2.

```python
""" Tutorial to show how to authenticate against Azure resource manager resources """
import azure.mgmt.resource
import automationassets

def get_automation_runas_credential(runas_connection):
    """ Returns credentials to authenticate against Azure resoruce manager """
    from OpenSSL import crypto
    from msrestazure import azure_active_directory
    import adal

    # Get the Azure Automation Run As service principal certificate
    cert = automationassets.get_automation_certificate("AzureRunAsCertificate")
    pks12_cert = crypto.load_pkcs12(cert)
    pem_pkey = crypto.dump_privatekey(
        crypto.FILETYPE_PEM, pks12_cert.get_privatekey())

    # Get Run As connection information for the Azure Automation service principal
    application_id = runas_connection["ApplicationId"]
    thumbprint = runas_connection["CertificateThumbprint"]
    tenant_id = runas_connection["TenantId"]

    # Authenticate with service principal certificate
    resource = "https://management.core.windows.net/"
    authority_url = ("https://login.microsoftonline.com/" + tenant_id)
    context = adal.AuthenticationContext(authority_url)
    return azure_active_directory.AdalAuthentication(
        lambda: context.acquire_token_with_client_certificate(
            resource,
            application_id,
            pem_pkey,
            thumbprint)
    )


# Authenticate to Azure using the Azure Automation Run As service principal
runas_connection = automationassets.get_automation_connection(
    "AzureRunAsConnection")
azure_credential = get_automation_runas_credential(runas_connection)
```

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni sui cmdlet usati per accedere alle connessioni, vedere [Gestire i moduli in Automazione di Azure](shared-resources/modules.md).
* Per informazioni generali sui runbook, vedere [Esecuzione di runbook in Automazione di Azure](automation-runbook-execution.md).
* Per informazioni dettagliate sulle configurazioni DSC, vedere [Panoramica di State Configuration](automation-dsc-overview.md).