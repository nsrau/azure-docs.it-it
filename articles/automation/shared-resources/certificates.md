---
title: Gestire il certificato in Automazione di AzureManage certificate in Azure Automation
description: I certificati vengono archiviati in modo sicuro in Automazione di Azure in modo che i runbook o le configurazioni DSC possano accedervi per eseguire l'autenticazione in risorse di Azure e di terze parti. Questo articolo illustra nel dettaglio i certificati e spiega come usarli nella creazione testuale e grafica.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: mgoedtel
ms.author: magoedte
ms.date: 04/02/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 69bc1a0333365e15452c6d3b253266d37d99b608
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81732818"
---
# <a name="manage-certificates-in-azure-automation"></a>Gestire i certificati nell'automazione di AzureManage certificates in Azure Automation

I certificati vengono archiviati in modo sicuro in Automazione di Azure in modo che possano essere accessibili da runbook o configurazioni DSC usando l'attività Get-AzAutomationCertificate per le risorse di Azure Resource Manager.Certificates are stored securely in Azure Automation so can be accessed by runbooks or DSC configurations using the [Get-AzAutomationCertificate](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationCertificate?view=azps-3.7.0) activity for Azure Resource Manager resources. L'archiviazione sicura dei certificati consente di creare runbook e configurazioni DSC che usano i certificati per l'autenticazione o di aggiungerli a risorse di Azure o di terze parti.

Gli asset sicuri in Automazione di Azure includono credenziali, certificati, connessioni e variabili crittografate. Questi asset vengono crittografati e archiviati in Automazione di Azure usando una chiave univoca generata per ogni account di automazione. Questa chiave viene archiviata in un insieme di credenziali delle chiavi gestito dal sistema. Prima di archiviare un asset sicuro, la chiave viene caricata da Key Vault e quindi usata per crittografare l'asset. Questo processo è gestito dall'Automazione di Azure.

>[!NOTE]
>Questo articolo è stato aggiornato per usare il nuovo modulo Az di Azure PowerShell. È comunque possibile usare il modulo AzureRM, che continuerà a ricevere correzioni di bug almeno fino a dicembre 2020. Per altre informazioni sul nuovo modulo Az e sulla compatibilità di AzureRM, vedere [Introduzione del nuovo modulo Az di Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Per istruzioni sull'installazione del modulo Az nel ruolo di lavoro ibrido per runbook, vedere [Installare il modulo di Azure PowerShell.For](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)Az module installation instructions on your Hybrid Runbook Worker, see Install the Azure PowerShell Module . Per l'account di automazione, è possibile aggiornare i moduli alla versione più recente usando Come aggiornare i moduli di [Azure PowerShell in Automazione di Azure.](../automation-update-azure-modules.md)

## <a name="az-powershell-cmdlets"></a>Cmdlet di Az PowerShell

Per Az, i cmdlet nella tabella seguente vengono utilizzati per creare e gestire le risorse delle credenziali di automazione con Windows PowerShell. Vengono spediti come parte del [modulo Az.Automation](/powershell/azure/overview), disponibile per l'utilizzo nei runbook di automazione e nelle configurazioni DSC.

|Cmdlet |Descrizione|
| --- | ---|
|[Add-AzureCertificate](/powershell/module/servicemanagement/azure/add-azurecertificate)|Carica un certificato di servizio per il servizio cloud specificato.|
|[Get-AzAutomationCertificateGet-AzAutomationCertificate](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationCertificate?view=azps-3.7.0)|Recupera le informazioni su un certificato da usare in un runbook o in una configurazione DSC. È possibile recuperare il certificato `Get-AutomationCertificate` stesso solo utilizzando l'attività.|
|[Nuovo-AzAutomationCertificateNew-AzAutomationCertificate](https://docs.microsoft.com/powershell/module/Az.Automation/New-AzAutomationCertificate?view=azps-3.7.0)|Crea un nuovo certificato in Automazione di Azure.Creates a new certificate in Azure Automation.|
|[Remove-AzAutomationCertificate](https://docs.microsoft.com/powershell/module/Az.Automation/Remove-AzAutomationCertificate?view=azps-3.7.0)|Rimuove un certificato da Automazione di Azure.|
|[Set-AzAutomationCertificate](https://docs.microsoft.com/powershell/module/Az.Automation/Set-AzAutomationCertificate?view=azps-3.7.0)|Imposta le proprietà per un certificato esistente, incluso il caricamento del file del certificato e l'impostazione della password per un file **con estensione pfx.**|

## <a name="activities"></a>attività

Le attività incluse nella tabella seguente vengono usate per accedere ai certificati in un runbook e nelle configurazioni DSC.

| attività | Descrizione |
|:---|:---|
|`Get-AutomationCertificate`|Ottiene un certificato da usare in un runbook o in una configurazione DSC. Restituisce un oggetto [System.Security.Cryptography.X509Certificates.X509Certificate2](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2).|

> [!NOTE] 
> È consigliabile evitare `Name` di `Get-AutomationCertificate` utilizzare variabili nel parametro di in un runbook o nella configurazione DSC. L'utilizzo di variabili in questo parametro complica l'individuazione delle dipendenze tra runbook o configurazioni DSC e variabili di automazione in fase di progettazione.

## <a name="python-2-functions"></a>Funzioni di Python 2

La funzione nella tabella seguente viene utilizzata per accedere ai certificati in un runbook di Python 2.The function in the following table is used to access certificates in a Python 2 runbook.

| Funzione | Descrizione |
|:---|:---|
| `automationassets.get_automation_certificate` | Recupera informazioni su un asset certificato. |

> [!NOTE]
> È necessario `automationassets` importare il modulo all'inizio del runbook Python per accedere alle funzioni delle risorse.

## <a name="creating-a-new-certificate"></a>Creazione di un nuovo certificato

Quando si crea un nuovo certificato, si carica un file con estensione cer o pfx in Automazione di Azure. Se si contrassegna il certificato come esportabile, sarà possibile trasferirlo all'esterno dell'archivio di certificati di Automazione di Azure. Se non è esportabile, può essere utilizzato solo per la firma all'interno del runbook o della configurazione DSC. Automazione di Azure richiede che il certificato disponga del provider **Microsoft Enhanced RSA e AES Cryptographic Provider**.

### <a name="create-a-new-certificate-with-the-azure-portal"></a>Creare un nuovo certificato con il portale di AzureCreate a new certificate with the Azure portal

1. Dal tuo account Automation, fai clic su **Risorse** per aprire la pagina Asset.
2. Selezionare Certificati per aprire la pagina **Certificati.Select Certificates** to open the Certificates page.
3. Fare clic su **Aggiungi un certificato** nella parte superiore della pagina.
4. Digitare un nome per il certificato nel campo **Nome.**
5. Per cercare un file **con estensione cer** o **pfx,** fare clic su **Seleziona un file** in Carica un file di **certificato**. Se si seleziona un file **con estensione pfx,** specificare una password e indicare se è possibile esportarlo.
6. Fare clic su **Crea** per salvare il nuovo asset di certificato.

### <a name="create-a-new-certificate-with-powershell"></a>Creare un nuovo certificato con PowerShellCreate a new certificate with PowerShell

L'esempio che segue mostra come creare un nuovo certificato di automazione e come contrassegnarlo come esportabile. In questo esempio viene importato un file **con estensione pfx** esistente.

```powershell-interactive
$certificateName = 'MyCertificate'
$PfxCertPath = '.\MyCert.pfx'
$CertificatePassword = ConvertTo-SecureString -String 'P@$$w0rd' -AsPlainText -Force
$ResourceGroup = "ResourceGroup01"

New-AzAutomationCertificate -AutomationAccountName "MyAutomationAccount" -Name $certificateName -Path $PfxCertPath –Password $CertificatePassword -Exportable -ResourceGroupName $ResourceGroup
```

### <a name="create-a-new-certificate-with-a-resource-manager-template"></a>Creare un nuovo certificato con un modello di Resource ManagerCreate a new certificate with a Resource Manager template

L'esempio seguente illustra come distribuire un certificato all'account di automazione usando un modello di Resource Manager tramite PowerShell:The following example demonstrates how to deploy a certificate to your Automation account using a Resource Manager template through PowerShell:

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

## <a name="using-a-certificate"></a>Utilizzo di un certificato

Per utilizzare un certificato, usare l'attività. `Get-AutomationCertificate` Non è possibile utilizzare il cmdlet [Get-AzAutomationCertificate,](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationCertificate?view=azps-3.7.0) poiché restituisce informazioni sull'asset del certificato ma non sul certificato stesso.

### <a name="textual-runbook-example"></a>Esempio di runbook testuale

L'esempio seguente mostra come aggiungere un certificato a un servizio cloud in un runbook. In questo esempio la password viene recuperata da una variabile di automazione crittografata.

```powershell-interactive
$serviceName = 'MyCloudService'
$cert = Get-AutomationCertificate -Name 'MyCertificate'
$certPwd = Get-AzAutomationVariable -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAccount" –Name 'MyCertPassword'
Add-AzureCertificate -ServiceName $serviceName -CertToDeploy $cert
```

### <a name="graphical-runbook-example"></a>Esempio di runbook grafico

Aggiungere `Get-AutomationCertificate` un'attività a un runbook grafico facendo clic con il pulsante destro del mouse sul certificato nel riquadro Libreria e selezionando **Aggiungi all'area di disegno**.

![Aggiungere il certificato all'area di disegno](../media/certificates/automation-certificate-add-to-canvas.png)

La figura seguente mostra un esempio dell'uso di un certificato in un runbook grafico. Si tratta dello stesso dell'esempio precedente che illustra come aggiungere un certificato a un servizio cloud da un runbook testuale.

![Esempio di creazione grafica](../media/certificates/graphical-runbook-add-certificate.png)

### <a name="python-2-example"></a>Esempio Python 2

Nell'esempio seguente viene illustrato come accedere ai certificati nei runbook Python2.The following example shows how to access certificates in Python2 runbooks.

```python
# get a reference to the Azure Automation certificate
cert = automationassets.get_automation_certificate("AzureRunAsCertificate")

# returns the binary cert content  
print cert
```

## <a name="next-steps"></a>Passaggi successivi

- Per ulteriori informazioni sull'utilizzo dei collegamenti per controllare il flusso logico delle attività eseguite dal runbook, vedere [Collegamenti nella creazione grafica](../automation-graphical-authoring-intro.md#links-and-workflow). 
