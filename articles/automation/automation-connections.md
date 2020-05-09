---
title: Gestire le connessioni in automazione di Azure
description: Gli asset di connessione di Automazione di Azure contengono le informazioni necessarie per la connessione a un servizio esterno o a un'applicazione da un Runbook o una connessione DSC. Questo articolo illustra nel dettaglio le connessioni e spiega come usarle nella creazione testuale e grafica.
services: automation
ms.subservice: shared-capabilities
ms.date: 01/13/2020
ms.topic: conceptual
ms.custom: has-adal-ref
ms.openlocfilehash: 1a50c5d3d6e068054cfc8381b220d38471a7eb6c
ms.sourcegitcommit: 309a9d26f94ab775673fd4c9a0ffc6caa571f598
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/09/2020
ms.locfileid: "82996586"
---
# <a name="manage-connections-in-azure-automation"></a>Gestire le connessioni in automazione di Azure

Un asset della connessione di automazione di Azure contiene le informazioni elencate di seguito. Queste informazioni sono necessarie per la connessione a un servizio esterno o a un'applicazione da una configurazione Runbook o DSC. 

* Informazioni necessarie per l'autenticazione, ad esempio nome utente e password
* Informazioni di connessione, ad esempio URL o porta

L'asset di connessione mantiene insieme tutte le proprietà per la connessione a un'applicazione specifica, rendendo superflua la creazione di più variabili. È possibile modificare i valori per una connessione in un'unica posizione ed è possibile passare il nome di una connessione a un Runbook o a una configurazione DSC in un singolo parametro. Il Runbook o la configurazione accede alle proprietà per una connessione usando il cmdlet `Get-AutomationConnection` interno.

Quando si crea una connessione, è necessario specificare un tipo di connessione. Il tipo di connessione è un modello che definisce un set di proprietà. È possibile aggiungere un tipo di connessione ad automazione di Azure usando un modulo di integrazione con un file di metadati. È anche possibile creare un tipo di connessione usando l' [API di automazione di Azure](/previous-versions/azure/reference/mt163818(v=azure.100)) se il modulo di integrazione include un tipo di connessione e viene importato nell'account di automazione. 

>[!NOTE]
>Gli asset sicuri in Automazione di Azure includono credenziali, certificati, connessioni e variabili crittografate. Questi asset vengono crittografati e archiviati in automazione di Azure usando una chiave univoca generata per ogni account di automazione. Automazione di Azure archivia la chiave nel Key Vault gestito dal sistema. Prima di archiviare un asset sicuro, l'automazione carica la chiave da Key Vault e la usa per crittografare l'asset. 

## <a name="connection-types"></a>Tipi di connessione

Automazione di Azure rende disponibili i tipi di connessione predefiniti seguenti:

* `Azure`: Rappresenta una connessione utilizzata per gestire le risorse classiche.
* `AzureServicePrincipal`: Rappresenta una connessione usata dall'account RunAs di Azure.
* `AzureClassicCertificate`: Rappresenta una connessione usata dall'account RunAs classico di Azure.

Nella maggior parte dei casi, non è necessario creare una risorsa di connessione perché viene creata quando si crea un [account RunAs](manage-runas-account.md).

## <a name="powershell-cmdlets-to-access-connections"></a>Cmdlet di PowerShell per accedere alle connessioni

I cmdlet nella tabella seguente creano e gestiscono le connessioni di automazione con PowerShell. Vengono forniti come parte dei [moduli AZ](shared-resources/modules.md#az-modules).

|Cmdlet|Descrizione|
|---|---|
|[Get-AzAutomationConnection](https://docs.microsoft.com/powershell/module/az.automation/get-azautomationconnection?view=azps-3.7.0)|Recupera le informazioni su una connessione.|
|[New-AzAutomationConnection](https://docs.microsoft.com/powershell/module/az.automation/new-azautomationconnection?view=azps-3.7.0)|Crea una nuova connessione.|
|[Remove-AzAutomationConnection](https://docs.microsoft.com/powershell/module/Az.Automation/Remove-AzAutomationConnection?view=azps-3.7.0)|Rimuove una connessione esistente.|
|[Set-AzAutomationConnectionFieldValue](https://docs.microsoft.com/powershell/module/Az.Automation/Set-AzAutomationConnectionFieldValue?view=azps-3.7.0)|Imposta il valore di un campo specifico per una connessione esistente.|

## <a name="internal-cmdlets-to-access-connections"></a>Cmdlet interni per accedere alle connessioni

Il cmdlet Internal nella tabella seguente viene usato per accedere alle connessioni nelle configurazioni manuali operativi e DSC. Questo cmdlet viene visualizzato con il modulo `Orchestrator.AssetManagement.Cmdlets`globale. Per ulteriori informazioni, vedere [cmdlet interni](shared-resources/modules.md#internal-cmdlets).

|Cmdlet interno|Descrizione|
|---|---|
|`Get-AutomationConnection` | Recupera i valori dei diversi campi nella connessione e li restituisce come [Hashtable](https://go.microsoft.com/fwlink/?LinkID=324844). È quindi possibile usare questa tabella hash con i comandi appropriati nella configurazione di Runbook o DSC.|

>[!NOTE]
>Evitare di usare variabili con `Name` il parametro `Get-AutomationConnection`di. L'uso di variabili in questo caso può complicare l'individuazione delle dipendenze tra manuali operativi o configurazioni DSC e gli asset di connessione in fase di progettazione.

## <a name="python-2-functions-to-access-connections"></a>Funzioni di Python 2 per accedere alle connessioni

La funzione nella tabella seguente viene usata per accedere alle connessioni in un Runbook Python 2.

| Funzione | Descrizione |
|:---|:---|
| `automationassets.get_automation_connection` | Recupera una connessione. Restituisce un dizionario con le proprietà della connessione. |

> [!NOTE]
> È necessario importare il `automationassets` modulo nella parte superiore del Runbook Python per accedere alle funzioni di asset.

## <a name="create-a-new-connection"></a>Crea una nuova connessione

### <a name="create-a-new-connection-with-the-azure-portal"></a>Crea una nuova connessione con il portale di Azure

Per creare una nuova connessione nella portale di Azure:

1. Dall'account di automazione fare clic su **connessioni** in **risorse condivise**.
2. Fare clic su **+ Aggiungi una connessione** nella pagina connessioni.
4. Nel campo **tipo** nel riquadro nuova connessione selezionare il tipo di connessione da creare. Le scelte disponibili `Azure`sono `AzureServicePrincipal`, e `AzureClassicCertificate`. 
5. Il modulo presenta le proprietà per il tipo di connessione scelto. Completare il modulo e fare clic su **Crea** per salvare la nuova connessione.

### <a name="create-a-new-connection-with-windows-powershell"></a>Creare una nuova connessione con Windows PowerShell

Creare una nuova connessione con Windows PowerShell usando il `New-AzAutomationConnection` cmdlet. Questo cmdlet dispone di `ConnectionFieldValues` un parametro che prevede una tabella hash che definisce i valori per ogni proprietà definita dal tipo di connessione.

È possibile usare i comandi di esempio seguenti come alternativa alla creazione dell'account RunAs dal portale per creare un nuovo asset di connessione.

```powershell
$ConnectionAssetName = "AzureRunAsConnection"
$ConnectionFieldValues = @{"ApplicationId" = $Application.ApplicationId; "TenantId" = $TenantID.TenantId; "CertificateThumbprint" = $Cert.Thumbprint; "SubscriptionId" = $SubscriptionId}
New-AzAutomationConnection -ResourceGroupName $ResourceGroup -AutomationAccountName $AutomationAccountName -Name $ConnectionAssetName -ConnectionTypeName AzureServicePrincipal -ConnectionFieldValues $ConnectionFieldValues
```

Quando si crea l'account di automazione, per impostazione predefinita vengono inclusi diversi moduli globali, insieme al tipo `AzureServicePrincipal` di connessione per `AzureRunAsConnection` creare l'asset di connessione. Se si tenta di creare un nuovo asset di connessione per connettersi a un servizio o a un'applicazione con un metodo di autenticazione diverso, l'operazione ha esito negativo perché il tipo di connessione non è già definito nell'account di automazione. Per ulteriori informazioni sulla creazione di un tipo di connessione personalizzato per un modulo personalizzato, vedere [aggiungere un tipo di connessione](#add-a-connection-type).

## <a name="add-a-connection-type"></a>Aggiungere un tipo di connessione

Se la configurazione di Runbook o DSC si connette a un servizio esterno, è necessario definire un tipo di connessione in un [modulo personalizzato](shared-resources/modules.md#custom-modules) denominato modulo di integrazione. Questo modulo include un file di metadati che specifica le proprietà del tipo di connessione ed è denominato ** &lt;ModuleName&gt;-Automation. JSON**, che si trova nella cartella del modulo del file compresso con **estensione zip** . Questo file contiene i campi di una connessione necessari per la connessione al sistema o al servizio rappresentato dal modulo. Utilizzando questo file, è possibile impostare i nomi dei campi, i tipi di dati, lo stato della crittografia e lo stato facoltativo per il tipo di connessione. 

L'esempio seguente è un modello nel formato di file **JSON** che definisce le proprietà di nome utente e password per un tipo di connessione `MyModuleConnection`personalizzato denominato:

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

## <a name="get-a-connection-in-a-runbook-or-dsc-configuration"></a>Ottenere una connessione in una configurazione Runbook o DSC

Recuperare una connessione in un Runbook o una configurazione DSC con il `Get-AutomationConnection` cmdlet interno. Questo cmdlet è preferibile `Get-AzAutomationConnection` al cmdlet, perché recupera i valori di connessione anziché le informazioni sulla connessione. 

### <a name="textual-runbook-example"></a>Esempio di Runbook testuale

L'esempio seguente illustra come usare l'account RunAs per l'autenticazione con Azure Resource Manager risorse in Runbook. Usa un asset di connessione che rappresenta l'account RunAs, che fa riferimento all'entità servizio basata su certificato.

```powershell
$Conn = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
```

### <a name="graphical-runbook-examples"></a>Esempi di Runbook grafici

È possibile aggiungere un'attività per il cmdlet `Get-AutomationConnection` Internal a un Runbook grafico. Fare clic con il pulsante destro del mouse sulla connessione nel riquadro libreria dell'editor grafico e selezionare **Aggiungi ad area di disegno**.

![Aggiunta all'area di disegno](media/automation-connections/connection-add-canvas.png)

Nell'immagine seguente viene illustrato un esempio di utilizzo di un oggetto Connection in un Runbook grafico. In questo esempio viene `Constant value` usato il set di `Get RunAs Connection` dati per l'attività, che usa un oggetto connessione per l'autenticazione. Viene usato un [collegamento alla pipeline](automation-graphical-authoring-intro.md#links-and-workflow) perché il `ServicePrincipalCertificate` set di parametri prevede un singolo oggetto.

![Recupero delle connessioni](media/automation-connections/automation-get-connection-object.png)

### <a name="python-2-runbook-example"></a>Esempio di Runbook Python 2

L'esempio seguente illustra come eseguire l'autenticazione usando la connessione RunAs in un Runbook Python 2.

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

* Per altre informazioni sui cmdlet usati per accedere alle connessioni, vedere [gestire i moduli in automazione di Azure](shared-resources/modules.md).
* Per informazioni generali su manuali operativi, vedere [esecuzione di Runbook in automazione di Azure](automation-runbook-execution.md).
* Per informazioni dettagliate sulle configurazioni DSC, vedere [Cenni preliminari sulla configurazione dello stato](automation-dsc-overview.md).