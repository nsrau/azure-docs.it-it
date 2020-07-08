---
title: Gestire i certificati in Automazione di Azure
description: Questo articolo descrive come usare i certificati per l'accesso dai runbook e dalle configurazioni DSC.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: mgoedtel
ms.author: magoedte
ms.date: 04/02/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: ebd4bf0ff91342229edb4295489f0cbd0970699a
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.contentlocale: it-IT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83745037"
---
# <a name="manage-certificates-in-azure-automation"></a>Gestire i certificati in Automazione di Azure

Automazione di Azure archivia i certificati in modo sicuro per l'accesso dai runbook e dalle configurazioni DSC, usando il cmdlet [Get-AzAutomationCertificate](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationCertificate?view=azps-3.7.0) per le risorse di Azure Resource Manager. L'archiviazione sicura dei certificati consente di creare runbook e configurazioni DSC che usano certificati per l'autenticazione oppure consente di aggiungerli a risorse di Azure o di terze parti.

>[!NOTE]
>Gli asset sicuri in Automazione di Azure includono credenziali, certificati, connessioni e variabili crittografate. Questi asset vengono crittografati e archiviati in Automazione usando una chiave univoca generata per ogni account di Automazione. Automazione archivia la chiave nel servizio Key Vault gestito dal sistema. Prima di archiviare un asset sicuro, Automazione carica la chiave da Key Vault e quindi la usa per crittografare l'asset. 

## <a name="powershell-cmdlets-to-access-certificates"></a>Cmdlet di PowerShell per accedere ai certificati

I cmdlet nella tabella seguente vengono usati per creare e gestire i certificati di Automazione con PowerShell. Sono inclusi nei [moduli Az](modules.md#az-modules).

|Cmdlet |Descrizione|
| --- | ---|
|[Get-AzAutomationCertificate](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationCertificate?view=azps-3.7.0)|Recupera le informazioni su un certificato da usare in un runbook o in una configurazione DSC. È possibile recuperare solo il certificato tramite il cmdlet `Get-AutomationCertificate` interno.|
|[New-AzAutomationCertificate](https://docs.microsoft.com/powershell/module/Az.Automation/New-AzAutomationCertificate?view=azps-3.7.0)|Crea un nuovo certificato in Automazione.|
|[Remove-AzAutomationCertificate](https://docs.microsoft.com/powershell/module/Az.Automation/Remove-AzAutomationCertificate?view=azps-3.7.0)|Rimuove un certificato da Automazione.|
|[Set-AzAutomationCertificate](https://docs.microsoft.com/powershell/module/Az.Automation/Set-AzAutomationCertificate?view=azps-3.7.0)|Imposta le proprietà per un certificato esistente, inclusi il caricamento del file del certificato e l'impostazione della password per un file con estensione **pfx**.|

Il cmdlet [Add-AzureCertificate](/powershell/module/servicemanagement/azure/add-azurecertificate) può essere usato anche per caricare un certificato di servizio per il servizio cloud specificato.

## <a name="internal-cmdlets-to-access-certificates"></a>Cmdlet interni per accedere ai certificati

Il cmdlet interno nella tabella seguente viene usato per accedere ai certificati nei runbook. Questo cmdlet è incluso nel modulo globale `Orchestrator.AssetManagement.Cmdlets`. Per altre informazioni, vedere [Cmdlet interni](modules.md#internal-cmdlets).

| Cmdlet interni | Descrizione |
|:---|:---|
|`Get-AutomationCertificate`|Ottiene un certificato da usare in un runbook o in una configurazione DSC. Restituisce un oggetto [System.Security.Cryptography.X509Certificates.X509Certificate2](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2).|

> [!NOTE] 
> È consigliabile evitare di usare le variabili nel parametro `Name` di `Get-AutomationCertificate` in un runbook o una configurazione DSC. Tali variabili possono complicare l'individuazione delle dipendenze tra runbook o configurazioni DSC e variabili di Automazione in fase di progettazione.

## <a name="python-2-functions-to-access-certificates"></a>Funzioni di Python 2 per accedere ai certificati

Usare la funzione indicata nella tabella seguente per accedere ai certificati in un runbook Python 2.

| Funzione | Descrizione |
|:---|:---|
| `automationassets.get_automation_certificate` | Recupera informazioni su un asset certificato. |

> [!NOTE]
> È necessario importare il modulo `automationassets` all'inizio del runbook Python per accedere alle funzioni dell'asset.

## <a name="create-a-new-certificate"></a>Creare un nuovo certificato

Quando si crea un nuovo certificato, si carica un file con estensione cer o pfx in Automazione. Se si contrassegna il certificato come esportabile, sarà possibile trasferirlo all'esterno dell'archivio di certificati di Automazione. Se non è esportabile, sarà possibile usarlo solo per la firma all'interno del runbook o della configurazione DSC. Automazione richiede che il certificato abbia il provider **Microsoft Enhanced RSA and AES Cryptographic Provider**.

### <a name="create-a-new-certificate-with-the-azure-portal"></a>Creare un nuovo certificato con il portale di Azure

1. Dall'account di Automazione selezionare **Asset** > **Certificati** > **Aggiungi certificato**.
1. Nel campo **Nome** digitare un nome per il certificato.
1. Per cercare un file con estensione **cer** o **pfx**, in **Carica un file di certificato** scegliere **Seleziona un file**. Se si seleziona un file con estensione **pfx**, specificare una password e indicare se ne è consentita l'esportazione.
1. Selezionare **Crea** per salvare il nuovo asset di certificato.

### <a name="create-a-new-certificate-with-powershell"></a>Creare un nuovo certificato con PowerShell

L'esempio che segue mostra come creare un nuovo certificato di automazione e come contrassegnarlo come esportabile. In questo esempio verrà importato un file con estensione **pfx** esistente.

```powershell-interactive
$certificateName = 'MyCertificate'
$PfxCertPath = '.\MyCert.pfx'
$CertificatePassword = ConvertTo-SecureString -String 'P@$$w0rd' -AsPlainText -Force
$ResourceGroup = "ResourceGroup01"

New-AzAutomationCertificate -AutomationAccountName "MyAutomationAccount" -Name $certificateName -Path $PfxCertPath –Password $CertificatePassword -Exportable -ResourceGroupName $ResourceGroup
```

### <a name="create-a-new-certificate-with-a-resource-manager-template"></a>Creare un nuovo certificato con un modello di Resource Manager

L'esempio seguente illustra come distribuire un certificato nell'account di Automazione usando un modello di Resource Manager tramite PowerShell:

```powershell-interactive
$AutomationAccountName = "<automation account name>"
$PfxCertPath = '<PFX cert path'
$CertificatePassword = '<password>'
$certificateName = '<certificate name>'
$AutomationAccountName = '<automation account name>'
$flags = [System.Security.Cryptography.X509Certificates.X509KeyStorageFlags]::Exportable `
    -bor [System.Security.Cryptography.X509Certificates.X509KeyStorageFlags]::PersistKeySet `
    -bor [System.Security.Cryptography.X509Certificates.X509KeyStorageFlags]::MachineKeySet
# Load the certificate into memory
$PfxCert = New-Object -TypeName System.Security.Cryptography.X509Certificates.X509Certificate2 -ArgumentList @($PfxCertPath, $CertificatePassword, $flags)
# Export the certificate and convert into base 64 string
$Base64Value = [System.Convert]::ToBase64String($PfxCert.Export([System.Security.Cryptography.X509Certificates.X509ContentType]::Pkcs12))
$Thumbprint = $PfxCert.Thumbprint


$json = @"
{
    '`$schema': 'https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#',
    'contentVersion': '1.0.0.0',
    'resources': [
        {
            'name': '$AutomationAccountName/$certificateName',
            'type': 'Microsoft.Automation/automationAccounts/certificates',
            'apiVersion': '2015-10-31',
            'properties': {
                'base64Value': '$Base64Value',
                'thumbprint': '$Thumbprint',
                'isExportable': true
            }
        }
    ]
}
"@

$json | out-file .\template.json
New-AzResourceGroupDeployment -Name NewCert -ResourceGroupName TestAzureAuto -TemplateFile .\template.json
```

## <a name="get-a-certificate"></a>Ottenere un certificato

Per recuperare un certificato, usare il cmdlet `Get-AutomationCertificate` interno. Non è possibile usare il cmdlet [Get-AzAutomationCertificate](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationCertificate?view=azps-3.7.0) poiché restituisce informazioni sull'asset di certificato, ma non il certificato stesso.

### <a name="textual-runbook-example"></a>Esempio di runbook testuale

L'esempio seguente illustra come aggiungere un certificato a un servizio cloud in un runbook. In questo esempio la password viene recuperata da una variabile di automazione crittografata.

```powershell-interactive
$serviceName = 'MyCloudService'
$cert = Get-AutomationCertificate -Name 'MyCertificate'
$certPwd = Get-AzAutomationVariable -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAccount" –Name 'MyCertPassword'
Add-AzureCertificate -ServiceName $serviceName -CertToDeploy $cert
```

### <a name="graphical-runbook-example"></a>Esempio di runbook grafico

Per aggiungere un'attività per il cmdlet `Get-AutomationCertificate` interno a un runbook grafico, fare clic con il pulsante destro del mouse sul certificato nel riquadro Libreria e selezionare **Aggiungi ad area di disegno**.

![Screenshot dell'aggiunta di un certificato all'area di disegno](../media/certificates/automation-certificate-add-to-canvas.png)

La figura seguente mostra un esempio dell'uso di un certificato in un runbook grafico. 

![Screenshot di un esempio di creazione grafica](../media/certificates/graphical-runbook-add-certificate.png)

### <a name="python-2-example"></a>Esempio di Python 2

L'esempio seguente illustra come accedere ai certificati nei runbook Python 2.

```python
# get a reference to the Azure Automation certificate
cert = automationassets.get_automation_certificate("AzureRunAsCertificate")

# returns the binary cert content  
print cert
```

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni sui cmdlet usati per accedere ai certificati, vedere [Gestire i moduli in Automazione di Azure](modules.md).
* Per informazioni generali sui runbook, vedere [Esecuzione di runbook in Automazione di Azure](../automation-runbook-execution.md).
* Per informazioni dettagliate sulle configurazioni DSC, vedere [Panoramica di State Configuration di Automazione di Azure](../automation-dsc-overview.md). 

