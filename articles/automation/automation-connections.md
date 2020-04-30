---
title: Gestire le connessioni in automazione di Azure
description: Gli asset di connessione di Automazione di Azure contengono le informazioni necessarie per la connessione a un servizio esterno o a un'applicazione da un Runbook o una connessione DSC. Questo articolo illustra nel dettaglio le connessioni e spiega come usarle nella creazione testuale e grafica.
services: automation
ms.subservice: shared-capabilities
ms.date: 01/13/2020
ms.topic: conceptual
ms.openlocfilehash: 39a41a60f4cabe995ebd458c4b906438d1e31bde
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82097116"
---
# <a name="manage-connections-in-azure-automation"></a>Gestire le connessioni in automazione di Azure

Un asset di connessione di Automazione contiene le informazioni necessarie per la connessione a un servizio esterno o a un'applicazione da un Runbook o una configurazione DSC. ad esempio le informazioni necessarie per l'autenticazione, quali nome utente e password, oltre alle informazioni di connessione quali un URL o una porta. Il valore di una connessione consiste nel mantenere tutte le proprietà per la connessione a un'applicazione specifica in un singolo asset, invece di creare più variabili. L'utente può modificare i valori per una connessione in un'unica posizione e può passare il nome di una connessione a un Runbook o a una configurazione DSC in un singolo parametro. È possibile accedere alle proprietà di una connessione in Runbook o nella configurazione DSC con l' `Get-AutomationConnection` attività.

Quando si crea una connessione, è necessario specificare un tipo di connessione. Il tipo di connessione è un modello che definisce un set di proprietà. La connessione definisce i valori per ogni proprietà definita nel rispettivo tipo di connessione. I tipi di connessione vengono aggiunti ad Automazione di Azure nei moduli di integrazione oppure creati con l'[API di Automazione di Azure](/previous-versions/azure/reference/mt163818(v=azure.100)) se il modulo di integrazione include un tipo di connessione e viene importato nell'account di Automazione. In caso contrario, è necessario creare un file di metadati per specificare un tipo di connessione di automazione. Per ulteriori informazioni su questo problema, vedere [moduli di integrazione](automation-integration-modules.md).

>[!NOTE]
>Gli asset sicuri in Automazione di Azure includono credenziali, certificati, connessioni e variabili crittografate. Questi asset vengono crittografati e archiviati in automazione di Azure usando una chiave univoca generata per ogni account di automazione. Questa chiave è archiviata in un Key Vault gestito dal sistema. Prima di archiviare un asset sicuro, la chiave viene caricata da Key Vault e quindi usata per crittografare l'asset. Questo processo è gestito dall'Automazione di Azure.

>[!NOTE]
>Questo articolo è stato aggiornato per usare il nuovo modulo Az di Azure PowerShell. È comunque possibile usare il modulo AzureRM, che continuerà a ricevere correzioni di bug almeno fino a dicembre 2020. Per altre informazioni sul nuovo modulo Az e sulla compatibilità di AzureRM, vedere [Introduzione del nuovo modulo Az di Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Per le istruzioni di installazione del modulo Az sul ruolo di lavoro ibrido per runbook, vedere [Installare il modulo Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Per aggiornare i moduli dell'account di Automazione alla versione più recente, vedere [Come aggiornare i moduli Azure PowerShell in Automazione di Azure](automation-update-azure-modules.md).

## <a name="connection-types"></a>Tipi di connessione

In automazione di Azure sono disponibili tre tipi di connessioni predefinite:

* **Azure** - questa connessione può essere usata per gestire le risorse classiche.
* **AzureClassicCertificate** - questa connessione è usata dall'account **AzureClassicRunAs**.
* **AzureServicePrincipal** - questa connessione è usata dall'account **AzureRunAs**.

Nella maggior parte dei casi, non è necessario creare una risorsa di connessione perché viene creata quando si crea un [account RunAs](manage-runas-account.md).

## <a name="windows-powershell-cmdlets"></a>Cmdlet di Windows PowerShell

I cmdlet della tabella seguente vengono usati per creare e gestire connessioni di automazione con Windows PowerShell. Vengono forniti come parte del [modulo Azure PowerShell](/powershell/azure/overview), disponibile per l'uso nei manuali operativi di automazione e nelle configurazioni DSC.

|Cmdlet|Descrizione|
|---|---|
|[Get-AzAutomationConnection](https://docs.microsoft.com/powershell/module/az.automation/get-azautomationconnection?view=azps-3.7.0)|Recupera una connessione. Include una tabella hash con i valori dei campi di connessione.|
|[New-AzAutomationConnection](https://docs.microsoft.com/powershell/module/az.automation/new-azautomationconnection?view=azps-3.7.0)|Crea una nuova connessione.|
|[Remove-AzAutomationConnection](https://docs.microsoft.com/powershell/module/Az.Automation/Remove-AzAutomationConnection?view=azps-3.7.0)|Rimuove una connessione esistente.|
|[Set-AzAutomationConnectionFieldValue](https://docs.microsoft.com/powershell/module/Az.Automation/Set-AzAutomationConnectionFieldValue?view=azps-3.7.0)|Imposta il valore di un campo specifico per una connessione esistente.|

## <a name="activities"></a>attività

Le attività incluse nella tabella seguente vengono usate per accedere alle connessioni in un Runbook o configurazione DSC.

|attività|Descrizione|
|---|---|
|`Get-AutomationConnection` | Ottiene una connessione da usare. Restituisce una tabella hash con le proprietà della connessione.|

>[!NOTE]
>Evitare di usare variabili con `Name` il parametro `Get-AutomationConnection`di. L'uso di questo parametro può complicare l'individuazione delle dipendenze tra manuali operativi o configurazioni DSC e gli asset di connessione in fase di progettazione.

## <a name="python-2-functions"></a>Funzioni di Python 2

La funzione nella tabella seguente viene usata per accedere alle connessioni in un Runbook Python 2.

| Funzione | Descrizione |
|:---|:---|
| `automationassets.get_automation_connection` | Recupera una connessione. Restituisce un dizionario con le proprietà della connessione. |

> [!NOTE]
> È necessario importare il `automationassets` modulo nella parte superiore del Runbook Python per accedere alle funzioni di asset.

## <a name="creating-a-new-connection"></a>Creazione di una nuova connessione

### <a name="create-a-new-connection-with-the-azure-portal"></a>Crea una nuova connessione con il portale di Azure

1. Dall'account di automazione fare clic sulla parte **Asset** per aprire il pannello **Asset** .
2. Fare clic sulla parte **Connessioni** per aprire il pannello **Connessioni**.
3. Fare clic su **Aggiungi connessione** nella parte superiore del pannello.
4. Dall'elenco a discesa **Tipo** selezionare il tipo di connessione da creare. Il modulo presenta le proprietà per il tipo di connessione specifico.
5. Completare il modulo e fare clic su **Crea** per salvare la nuova connessione.

### <a name="create-a-new-connection-with-windows-powershell"></a>Creare una nuova connessione con Windows PowerShell

Creare una nuova connessione con Windows PowerShell usando il `New-AzAutomationConnection` cmdlet. Questo cmdlet ha un parametro denominato `ConnectionFieldValues` che prevede una [tabella hash](https://technet.microsoft.com/library/hh847780.aspx) che definisce i valori per ogni proprietà definita dal tipo di connessione.

È possibile usare i comandi di esempio seguenti come alternativa alla creazione dell'account RunAs dal portale per creare un nuovo asset di connessione.

```powershell
$ConnectionAssetName = "AzureRunAsConnection"
$ConnectionFieldValues = @{"ApplicationId" = $Application.ApplicationId; "TenantId" = $TenantID.TenantId; "CertificateThumbprint" = $Cert.Thumbprint; "SubscriptionId" = $SubscriptionId}
New-AzAutomationConnection -ResourceGroupName $ResourceGroup -AutomationAccountName $AutomationAccountName -Name $ConnectionAssetName -ConnectionTypeName AzureServicePrincipal -ConnectionFieldValues $ConnectionFieldValues
```

Quando si crea l'account di automazione, per impostazione predefinita vengono inclusi diversi moduli globali, insieme al tipo `AzureServicePrincipal` di connessione per `AzureRunAsConnection` creare l'asset di connessione. Se si tenta di creare un nuovo asset di connessione per connettersi a un servizio o a un'applicazione con un metodo di autenticazione diverso, l'operazione ha esito negativo perché il tipo di connessione non è già definito nell'account di automazione. Per ulteriori informazioni sulla creazione di un tipo di connessione personalizzato per un modulo di [PowerShell Gallery](https://www.powershellgallery.com) personalizzato, vedere [moduli di integrazione](automation-integration-modules.md).

## <a name="using-a-connection-in-a-runbook-or-dsc-configuration"></a>Uso di una connessione in un Runbook o in una configurazione DSC

Recuperare una connessione in un Runbook o una configurazione DSC con `Get-AutomationConnection` il cmdlet. Non è possibile usare `Get-AzAutomationConnection` l'attività. Questa attività recupera i valori dei diversi campi nella connessione e li restituisce come [tabella hash](https://go.microsoft.com/fwlink/?LinkID=324844). Questa tabella hash può quindi essere usata con i comandi appropriati nella configurazione di Runbook o DSC.

### <a name="textual-runbook-sample"></a>Esempio di Runbook testuale

I comandi di esempio seguenti mostrano come usare l'account RunAs menzionato in precedenza per autenticarsi con le risorse di Azure Resource Manager nel proprio Runbook. Usa l'asset di connessione che rappresenta l'account RunAs, il quale fa riferimento all'entità servizio basata sui certificati, non alle credenziali.

```powershell
$Conn = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
```

> [!NOTE]
> Per manuali operativi di PowerShell non grafici, `Add-AzAccount` e `Add-AzureRMAccount` sono alias per [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-3.5.0). È possibile usare questi cmdlet oppure è possibile [aggiornare i moduli](automation-update-azure-modules.md) nell'account di Automazione alle versioni più recenti. Potrebbe essere necessario aggiornare i moduli, anche se è stato appena creato un nuovo account di Automazione.

### <a name="graphical-runbook-samples"></a>Esempi di Runbook grafici

Per aggiungere un' `Get-AutomationConnection` attività a un Runbook grafico, fare clic con il pulsante destro del mouse sulla connessione nel riquadro libreria dell'editor grafico e scegliere **Aggiungi ad area di disegno**.

![Aggiunta all'area di disegno](media/automation-connections/connection-add-canvas.png)

La figura seguente mostra un esempio dell'uso di una connessione in un Runbook grafico. Si tratta dello stesso esempio illustrato in precedenza per l'autenticazione tramite l'account RunAs con un Runbook testuale. In questo esempio viene `Constant value` utilizzato il set di `Get RunAs Connection` dati per l'attività che utilizza un oggetto connessione per l'autenticazione. Viene usato un [collegamento alla pipeline](automation-graphical-authoring-intro.md#links-and-workflow) perché il `ServicePrincipalCertificate` set di parametri prevede un singolo oggetto.

![Recupero delle connessioni](media/automation-connections/automation-get-connection-object.png)

### <a name="python-2-runbook-sample"></a>Esempio di Python 2 Runbook

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

- Consultare la sezione [Collegamenti nella creazione grafica](automation-graphical-authoring-intro.md#links-and-workflow) per informazioni su come dirigere e controllare il flusso di logica nei runbook.
* Per informazioni di riferimento sui cmdlet di PowerShell, vedere [Az.Automation](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation
).
- Vedere [Moduli di integrazione](automation-integration-modules.md) per ulteriori informazioni sull'uso dei moduli di PowerShell da parte di Automazione di Azure e per conoscere le procedure consigliate per creare i propri moduli di PowerShell come moduli di integrazione in Automazione di Azure.