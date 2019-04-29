---
title: Asset di tipo certificato in Automazione di Azure
description: I certificati sono in modo sicuro in automazione di Azure in modo che siano accessibili i runbook o configurazioni DSC per l'autenticazione con Azure e le risorse di terze parti.  Questo articolo illustra nel dettaglio i certificati e spiega come usarli nella creazione testuale e grafica.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: georgewallace
ms.author: gwallace
ms.date: 04/02/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: d230fa97d009f0ee2a3bc86a0b6b7c8d40687a46
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61216070"
---
# <a name="certificate-assets-in-azure-automation"></a>Asset di tipo certificato in Automazione di Azure

I certificati vengono archiviati in modo sicuro in automazione di Azure in modo che siano accessibili i runbook o configurazioni DSC tramite il **Get-AzureRmAutomationCertificate** attività per le risorse di Azure Resource Manager. Questa funzionalità consente di creare runbook e configurazioni DSC che usano certificati per l'autenticazione oppure consente di aggiungerli a risorse di Azure o di terze parti.

>[!NOTE]
>Gli asset sicuri in Automazione di Azure includono credenziali, certificati, connessioni e variabili crittografate. Questi asset vengono crittografati e archiviati in Automazione di Azure usando una chiave univoca generata per ogni account di automazione. Questa chiave è archiviata in un Key Vault gestito dal sistema. Prima di archiviare un asset sicuro, la chiave viene caricata da Key Vault e quindi usata per crittografare l'asset. Questo processo è gestito da Automazione di Azure.

## <a name="azurerm-powershell-cmdlets"></a>Cmdlet di PowerShell AzureRM

Per AzureRM, per creare e gestire asset di credenziali di automazione con Windows PowerShell, vengono usati i cmdlet della tabella seguente. Sono inclusi come parte del [modulo azurerm. Automation](/powershell/azure/overview), che è disponibile per l'uso nei runbook di automazione e nelle configurazioni DSC.

|Cmdlets|DESCRIZIONE|
|:---|:---|
|[Get-AzureRmAutomationCertificate](/powershell/module/azurerm.automation/get-azurermautomationcertificate)|Recupera le informazioni su un certificato da usare in un runbook o in una configurazione DSC. È possibile recuperare solo il certificato stesso dall'attività Get-AutomationCertificate.|
|[New-AzureRmAutomationCertificate](/powershell/module/azurerm.automation/new-azurermautomationcertificate)|Crea un nuovo certificato in Automazione di Azure.|
[Remove-AzureRmAutomationCertificate](/powershell/module/azurerm.automation/remove-azurermautomationcertificate)|Rimuove un certificato da Automazione di Azure.|
|[Set-AzureRmAutomationCertificate](/powershell/module/azurerm.automation/set-azurermautomationcertificate)|Imposta le proprietà per un certificato esistente, inclusi il caricamento del file del certificato e l'impostazione della password per un file con estensione pfx.|
|[Add-AzureCertificate](/powershell/module/servicemanagement/azure/add-azurecertificate)|Carica un certificato di servizio per il servizio cloud specificato.|

## <a name="activities"></a>attività

Le attività incluse nella tabella seguente vengono usate per accedere ai certificati in un runbook e nelle configurazioni DSC.

| attività | DESCRIZIONE |
|:---|:---|
|Get-AutomationCertificate|Ottiene un certificato da usare in un runbook o in una configurazione DSC. Restituisce un oggetto [System.Security.Cryptography.X509Certificates.X509Certificate2](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2).|

> [!NOTE] 
> È consigliabile evitare di usare le variabili nel parametro –Name di **Get-AutomationCertificate** in un runbook o una configurazione DSC perché questo può rendere complessa l'individuazione delle dipendenze tra i runbook o le configurazioni DSC e le variabili di automazione in fase di progettazione.

## <a name="python2-functions"></a>Funzioni Python2

La funzione nella tabella seguente viene usata per accedere ai certificati in un runbook Python2.

| Funzione | DESCRIZIONE |
|:---|:---|
| automationassets.get_automation_certificate | Recupera informazioni su un asset certificato. |

> [!NOTE]
> È necessario importare il modulo **automationassets** all'inizio del runbook Python per poter accedere alle funzioni dell'asset.

## <a name="creating-a-new-certificate"></a>Creazione di un nuovo certificato

Quando si crea un nuovo certificato, si carica un file con estensione cer o pfx in Automazione di Azure. Se si contrassegna il certificato come esportabile, sarà possibile trasferirlo all'esterno dell'archivio di certificati di Automazione di Azure. Se non è esportabile, quindi utilizzabile solo per la firma all'interno del runbook o configurazione DSC. Automazione di Azure richiede che il certificato disponga di un provider: **Microsoft Enhanced RSA and AES Cryptographic Provider (RSA avanzato di Microsoft e provider del servizio di crittografia AES)**.

### <a name="to-create-a-new-certificate-with-the-azure-portal"></a>Per creare un nuovo certificato con il portale di Azure

1. Dall'account di automazione, fare clic sul **Assets** riquadro per aprire il **asset** pagina.
2. Fare clic sui **certificati** riquadro per aprire il **certificati** pagina.
3. Fare clic su **aggiungere un certificato** nella parte superiore della pagina.
4. Digitare un nome per il certificato nella casella **Nome** .
5. Per cercare un file con estensione cer o pfx, fare clic su **Selezionare un file** in **Caricare un file di certificato**. Se si seleziona un file con estensione pfx, specificare una password e se può essere esportato.
6. Fare clic su **Crea** per salvare il nuovo asset di certificato.

### <a name="to-create-a-new-certificate-with-powershell"></a>Per creare un nuovo certificato con PowerShell

L'esempio che segue mostra come creare un nuovo certificato di automazione e come contrassegnarlo come esportabile. Verrà importato un file con estensione pfx esistente.

```powershell-interactive
$certificateName = 'MyCertificate'
$PfxCertPath = '.\MyCert.pfx'
$CertificatePassword = ConvertTo-SecureString -String 'P@$$w0rd' -AsPlainText -Force
$ResourceGroup = "ResourceGroup01"

New-AzureRmAutomationCertificate -AutomationAccountName "MyAutomationAccount" -Name $certificateName -Path $PfxCertPath –Password $CertificatePassword -Exportable -ResourceGroupName $ResourceGroup
```

### <a name="create-a-new-certificate-with-resource-manager-template"></a>Creare un nuovo certificato con il modello di Resource Manager

L'esempio seguente illustra come distribuire un certificato a un Account di automazione usando un modello di Resource Manager tramite PowerShell:

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
New-AzureRmResourceGroupDeployment -Name NewCert -ResourceGroupName TestAzureAuto -TemplateFile .\template.json
```

## <a name="using-a-certificate"></a>Utilizzo di un certificato

Per usare un certificato, usare l'attività **Get-AutomationCertificate**. Non è possibile usare la [Get-AzureRmAutomationCertificate](/powershell/module/azurerm.automation/get-azurermautomationcertificate) cmdlet poiché restituisce informazioni sull'asset certificato ma non il certificato stesso.

### <a name="textual-runbook-sample"></a>Esempio di Runbook testuale

Il codice di esempio seguente mostra come aggiungere un certificato a un servizio cloud in un Runbook. In questo esempio la password viene recuperata da una variabile di automazione crittografata.

```powershell-interactive
$serviceName = 'MyCloudService'
$cert = Get-AutomationCertificate -Name 'MyCertificate'
$certPwd = Get-AzureRmAutomationVariable -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAccount" –Name 'MyCertPassword'
Add-AzureCertificate -ServiceName $serviceName -CertToDeploy $cert
```

### <a name="graphical-runbook-sample"></a>Esempio di Runbook grafico

Si aggiunge un **Get-AutomationCertificate** a un runbook con interfaccia grafico facendo clic sul certificato nel riquadro della libreria e selezionando **Aggiungi a canvas**.

![Aggiungere il certificato all'area di disegno](../media/certificates/automation-certificate-add-to-canvas.png)

La figura seguente mostra un esempio dell'uso di un certificato in un runbook grafico. Questo è lo stesso dell'esempio precedente che illustra come aggiungere un certificato a un servizio cloud da un runbook testuale.

![Esempio di creazione grafica](../media/certificates/graphical-runbook-add-certificate.png)

### <a name="python2-sample"></a>Esempio Python2

L'esempio seguente illustra come accedere ai certificati nei runbook Python2.

```python
# get a reference to the Azure Automation certificate
cert = automationassets.get_automation_certificate("AzureRunAsCertificate")

# returns the binary cert content  
print cert
```

## <a name="next-steps"></a>Passaggi successivi

- Per ulteriori informazioni sull'utilizzo di collegamenti per controllare il flusso logico delle attività per le quali è progettato il runbook, vedere [Collegamenti nella creazione grafica](../automation-graphical-authoring-intro.md#links-and-workflow). 
