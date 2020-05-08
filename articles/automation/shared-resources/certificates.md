---
title: Gestire i certificati in automazione di Azure
description: Automazione di Azure archivia in modo sicuro i certificati, in modo che manuali operativi o le configurazioni DSC possano accedervi per eseguire l'autenticazione in Azure e le risorse di terze parti. In questo articolo vengono illustrati i dettagli dei certificati e le relative modalità di utilizzo nella creazione testuale e grafica.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: mgoedtel
ms.author: magoedte
ms.date: 04/02/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 2793679fb4588d00ea4e37340b19183398cb9d90
ms.sourcegitcommit: 602e6db62069d568a91981a1117244ffd757f1c2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/06/2020
ms.locfileid: "82864318"
---
# <a name="manage-certificates-in-azure-automation"></a>Gestire i certificati in automazione di Azure

Automazione di Azure archivia i certificati in modo sicuro per l'accesso da parte di manuali operativi e delle configurazioni DSC, usando il cmdlet [Get-AzAutomationCertificate](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationCertificate?view=azps-3.7.0) per Azure Resource Manager risorse. L'archiviazione sicura dei certificati consente di creare manuali operativi e configurazioni DSC che usano certificati per l'autenticazione o di aggiungerli a risorse di Azure o di terze parti.

>[!NOTE]
>Gli asset sicuri in Automazione di Azure includono credenziali, certificati, connessioni e variabili crittografate. Questi asset vengono crittografati e archiviati in automazione usando una chiave univoca generata per ogni account di automazione. Automazione archivia la chiave nel servizio Key Vault gestito dal sistema. Prima di archiviare un asset sicuro, l'automazione carica la chiave da Key Vault, quindi la usa per crittografare l'asset. 

>[!NOTE]
>Questo articolo illustra come usare il modulo Azure PowerShell AZ. È comunque possibile usare il modulo AzureRM. Per ulteriori informazioni sulla compatibilità AZ Module e AzureRM, vedere la pagina relativa all' [Introduzione del nuovo Azure PowerShell AZ Module](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Per le istruzioni di installazione del modulo AZ sul ruolo di lavoro ibrido per Runbook, vedere [installare il modulo Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Per l'account di automazione, è possibile aggiornare i moduli alla versione più recente usando [come aggiornare i moduli Azure PowerShell in automazione di Azure](../automation-update-azure-modules.md).

## <a name="powershell-cmdlets-to-access-certificates"></a>Cmdlet di PowerShell per accedere ai certificati

I cmdlet nella tabella seguente creano e gestiscono i certificati di automazione con PowerShell. Vengono forniti come parte dei [moduli AZ](modules.md#az-modules).

|Cmdlet |Descrizione|
| --- | ---|
|[Get-AzAutomationCertificate](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationCertificate?view=azps-3.7.0)|Recupera le informazioni su un certificato da usare in un runbook o in una configurazione DSC. È possibile recuperare solo il certificato utilizzando il cmdlet Internal `Get-AutomationCertificate` .|
|[New-AzAutomationCertificate](https://docs.microsoft.com/powershell/module/Az.Automation/New-AzAutomationCertificate?view=azps-3.7.0)|Crea un nuovo certificato in automazione.|
|[Remove-AzAutomationCertificate](https://docs.microsoft.com/powershell/module/Az.Automation/Remove-AzAutomationCertificate?view=azps-3.7.0)|Rimuove un certificato dall'automazione.|
|[Set-AzAutomationCertificate](https://docs.microsoft.com/powershell/module/Az.Automation/Set-AzAutomationCertificate?view=azps-3.7.0)|Imposta le proprietà per un certificato esistente, incluso il caricamento del file di certificato e l'impostazione della password per un file con **estensione pfx** .|

Il cmdlet [Add-AzureCertificate](/powershell/module/servicemanagement/azure/add-azurecertificate) può essere usato anche per caricare un certificato di servizio per il servizio cloud specificato.

## <a name="internal-cmdlets-to-access-certificates"></a>Cmdlet interni per accedere ai certificati

Il cmdlet Internal nella tabella seguente viene usato per accedere ai certificati nei manuali operativi. Questo cmdlet viene visualizzato con il modulo `Orchestrator.AssetManagement.Cmdlets`globale. Per ulteriori informazioni, vedere [cmdlet interni](modules.md#internal-cmdlets).

| Cmdlet interno | Descrizione |
|:---|:---|
|`Get-AutomationCertificate`|Ottiene un certificato da usare in un runbook o in una configurazione DSC. Restituisce un oggetto [System.Security.Cryptography.X509Certificates.X509Certificate2](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2).|

> [!NOTE] 
> È consigliabile evitare di usare le `Name` variabili nel parametro `Get-AutomationCertificate` di in una configurazione Runbook o DSC. Tali variabili possono complicare l'individuazione delle dipendenze tra manuali operativi o le configurazioni DSC e le variabili di automazione in fase di progettazione.

## <a name="python-2-functions-to-access-certificates"></a>Funzioni di Python 2 per accedere ai certificati

Usare la funzione nella tabella seguente per accedere ai certificati in un Runbook Python 2.

| Funzione | Descrizione |
|:---|:---|
| `automationassets.get_automation_certificate` | Recupera informazioni su un asset certificato. |

> [!NOTE]
> È necessario importare il `automationassets` modulo all'inizio di Python Runbook per accedere alle funzioni di asset.

## <a name="create-a-new-certificate"></a>Creazione di un nuovo certificato

Quando si crea un nuovo certificato, si carica un file con estensione cer o pfx in automazione. Se si contrassegna il certificato come esportabile, è possibile trasferirlo all'esterno dell'archivio certificati di automazione. Se non è esportabile, può essere usato solo per la firma all'interno della configurazione Runbook o DSC. L'automazione richiede che il certificato disponga del provider **Microsoft Enhanced RSA e AES Cryptographic Provider**.

### <a name="create-a-new-certificate-with-the-azure-portal"></a>Creare un nuovo certificato con il portale di Azure

1. Dall'account di automazione selezionare **Asset** > **certificati** > **Aggiungi un certificato**.
1. Nel campo **nome** Digitare un nome per il certificato.
1. Per cercare un file con estensione **CER** o **PFX** , in **caricare un**file di certificato scegliere **selezionare un file**. Se si seleziona un file con **estensione pfx** , specificare una password e indicare se è possibile esportarla.
1. Selezionare **Crea** per salvare il nuovo asset di certificato.

### <a name="create-a-new-certificate-with-powershell"></a>Creare un nuovo certificato con PowerShell

L'esempio che segue mostra come creare un nuovo certificato di automazione e come contrassegnarlo come esportabile. In questo esempio viene importato un file con **estensione pfx** esistente.

```powershell-interactive
$certificateName = 'MyCertificate'
$PfxCertPath = '.\MyCert.pfx'
$CertificatePassword = ConvertTo-SecureString -String 'P@$$w0rd' -AsPlainText -Force
$ResourceGroup = "ResourceGroup01"

New-AzAutomationCertificate -AutomationAccountName "MyAutomationAccount" -Name $certificateName -Path $PfxCertPath –Password $CertificatePassword -Exportable -ResourceGroupName $ResourceGroup
```

### <a name="create-a-new-certificate-with-a-resource-manager-template"></a>Creare un nuovo certificato con un modello di Gestione risorse

L'esempio seguente illustra come distribuire un certificato nell'account di automazione usando un modello di Gestione risorse tramite PowerShell:

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

Per recuperare un certificato, usare il cmdlet `Get-AutomationCertificate` Internal. Non è possibile usare il cmdlet [Get-AzAutomationCertificate](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationCertificate?view=azps-3.7.0) , perché restituisce le informazioni sull'asset del certificato, ma non il certificato stesso.

### <a name="textual-runbook-example"></a>Esempio di Runbook testuale

Nell'esempio seguente viene illustrato come aggiungere un certificato a un servizio cloud in un Runbook. In questo esempio la password viene recuperata da una variabile di automazione crittografata.

```powershell-interactive
$serviceName = 'MyCloudService'
$cert = Get-AutomationCertificate -Name 'MyCertificate'
$certPwd = Get-AzAutomationVariable -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAccount" –Name 'MyCertPassword'
Add-AzureCertificate -ServiceName $serviceName -CertToDeploy $cert
```

### <a name="graphical-runbook-example"></a>Esempio di Runbook grafico

Aggiungere un'attività per il cmdlet `Get-AutomationCertificate` Internal a un Runbook grafico facendo clic con il pulsante destro del mouse sul certificato nel riquadro libreria e selezionando **Aggiungi ad area di disegno**.

![Screenshot dell'aggiunta di un certificato all'area di disegno](../media/certificates/automation-certificate-add-to-canvas.png)

La figura seguente mostra un esempio dell'uso di un certificato in un runbook grafico. 

![Screenshot di un esempio di creazione grafica](../media/certificates/graphical-runbook-add-certificate.png)

### <a name="python-2-example"></a>Esempio di Python 2

L'esempio seguente illustra come accedere ai certificati in Python 2 manuali operativi.

```python
# get a reference to the Azure Automation certificate
cert = automationassets.get_automation_certificate("AzureRunAsCertificate")

# returns the binary cert content  
print cert
```

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni sui cmdlet usati per accedere ai certificati, vedere [gestire i moduli in automazione di Azure](modules.md).
* Per informazioni generali su manuali operativi, vedere [esecuzione di Runbook in automazione di Azure](../automation-runbook-execution.md).
* Per informazioni dettagliate sulle configurazioni DSC, vedere [Cenni preliminari sulla configurazione dello stato](../automation-dsc-overview.md). 
