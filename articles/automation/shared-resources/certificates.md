---
title: Gestire il certificato in automazione di Azure
description: I certificati vengono archiviati in modo sicuro in automazione di Azure in modo che le configurazioni manuali operativi o DSC possano accedervi per eseguire l'autenticazione in Azure e le risorse di terze parti. Questo articolo illustra nel dettaglio i certificati e spiega come usarli nella creazione testuale e grafica.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: mgoedtel
ms.author: magoedte
ms.date: 04/02/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 69bc1a0333365e15452c6d3b253266d37d99b608
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81732818"
---
# <a name="manage-certificates-in-azure-automation"></a>Gestire i certificati in automazione di Azure

I certificati vengono archiviati in modo sicuro in automazione di Azure, in modo che sia possibile accedervi tramite manuali operativi o configurazioni DSC usando l'attività [Get-AzAutomationCertificate](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationCertificate?view=azps-3.7.0) per le risorse Azure Resource Manager. L'archiviazione sicura dei certificati consente di creare manuali operativi e configurazioni DSC che usano certificati per l'autenticazione o di aggiungerli a risorse di Azure o di terze parti.

Gli asset sicuri in Automazione di Azure includono credenziali, certificati, connessioni e variabili crittografate. Questi asset vengono crittografati e archiviati in Automazione di Azure usando una chiave univoca generata per ogni account di automazione. Questa chiave viene archiviata in un Key Vault gestito dal sistema. Prima di archiviare un asset sicuro, la chiave viene caricata da Key Vault e quindi usata per crittografare l'asset. Questo processo è gestito dall'Automazione di Azure.

>[!NOTE]
>Questo articolo è stato aggiornato per usare il nuovo modulo Az di Azure PowerShell. È comunque possibile usare il modulo AzureRM, che continuerà a ricevere correzioni di bug almeno fino a dicembre 2020. Per altre informazioni sul nuovo modulo Az e sulla compatibilità di AzureRM, vedere [Introduzione del nuovo modulo Az di Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Per le istruzioni di installazione del modulo Az sul ruolo di lavoro ibrido per runbook, vedere [Installare il modulo Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Per aggiornare i moduli dell'account di Automazione alla versione più recente, vedere [Come aggiornare i moduli Azure PowerShell in Automazione di Azure](../automation-update-azure-modules.md).

## <a name="az-powershell-cmdlets"></a>AZ PowerShell cmdlets

Per AZ, i cmdlet della tabella seguente vengono usati per creare e gestire asset delle credenziali di automazione con Windows PowerShell. Vengono forniti come parte del [modulo AZ. Automation](/powershell/azure/overview), disponibile per l'uso nei manuali operativi di automazione e nelle configurazioni DSC.

|Cmdlet |Descrizione|
| --- | ---|
|[Add-AzureCertificate](/powershell/module/servicemanagement/azure/add-azurecertificate)|Carica un certificato di servizio per il servizio cloud specificato.|
|[Get-AzAutomationCertificate](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationCertificate?view=azps-3.7.0)|Recupera le informazioni su un certificato da usare in un runbook o in una configurazione DSC. È possibile recuperare solo il certificato utilizzando l' `Get-AutomationCertificate` attività.|
|[New-AzAutomationCertificate](https://docs.microsoft.com/powershell/module/Az.Automation/New-AzAutomationCertificate?view=azps-3.7.0)|Consente di creare un nuovo certificato in automazione di Azure.|
|[Remove-AzAutomationCertificate](https://docs.microsoft.com/powershell/module/Az.Automation/Remove-AzAutomationCertificate?view=azps-3.7.0)|Rimuove un certificato da Automazione di Azure.|
|[Set-AzAutomationCertificate](https://docs.microsoft.com/powershell/module/Az.Automation/Set-AzAutomationCertificate?view=azps-3.7.0)|Imposta le proprietà per un certificato esistente, incluso il caricamento del file di certificato e l'impostazione della password per un file con **estensione pfx** .|

## <a name="activities"></a>attività

Le attività incluse nella tabella seguente vengono usate per accedere ai certificati in un runbook e nelle configurazioni DSC.

| attività | Descrizione |
|:---|:---|
|`Get-AutomationCertificate`|Ottiene un certificato da usare in un runbook o in una configurazione DSC. Restituisce un oggetto [System.Security.Cryptography.X509Certificates.X509Certificate2](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2).|

> [!NOTE] 
> È consigliabile evitare di usare le `Name` variabili nel parametro `Get-AutomationCertificate` di in una configurazione Runbook o DSC. L'uso di variabili in questo parametro complica l'individuazione delle dipendenze tra manuali operativi o le configurazioni DSC e le variabili di automazione in fase di progettazione.

## <a name="python-2-functions"></a>Funzioni di Python 2

La funzione nella tabella seguente viene usata per accedere ai certificati in un Runbook Python 2.

| Funzione | Descrizione |
|:---|:---|
| `automationassets.get_automation_certificate` | Recupera informazioni su un asset certificato. |

> [!NOTE]
> È necessario importare il `automationassets` modulo all'inizio di Python Runbook per accedere alle funzioni di asset.

## <a name="creating-a-new-certificate"></a>Creazione di un nuovo certificato

Quando si crea un nuovo certificato, si carica un file con estensione cer o pfx in Automazione di Azure. Se si contrassegna il certificato come esportabile, sarà possibile trasferirlo all'esterno dell'archivio di certificati di Automazione di Azure. Se non è esportabile, può essere usato solo per la firma all'interno della configurazione Runbook o DSC. Automazione di Azure richiede che il certificato disponga del provider **Microsoft Enhanced RSA e AES Cryptographic Provider**.

### <a name="create-a-new-certificate-with-the-azure-portal"></a>Creare un nuovo certificato con il portale di Azure

1. Nell'account di automazione fare clic su **Asset** per aprire la pagina asset.
2. Selezionare **certificati** per aprire la pagina certificati.
3. Fare clic su **Aggiungi un certificato** nella parte superiore della pagina.
4. Digitare un nome per il certificato nel campo **nome** .
5. Per cercare un file con estensione **CER** o **PFX** , fare clic su **selezionare un** file in **caricare un file di certificato**. Se si seleziona un file con **estensione pfx**, specificare una password e indicare se è possibile esportarla.
6. Fare clic su **Crea** per salvare il nuovo asset di certificato.

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

## <a name="using-a-certificate"></a>Utilizzo di un certificato

Per usare un certificato, usare l' `Get-AutomationCertificate` attività. Non è possibile usare il cmdlet [Get-AzAutomationCertificate](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationCertificate?view=azps-3.7.0) , poiché restituisce informazioni sull'asset del certificato, ma non il certificato stesso.

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

Aggiungere un' `Get-AutomationCertificate` attività a un Runbook grafico facendo clic con il pulsante destro del mouse sul certificato nel riquadro libreria e selezionando **Aggiungi ad area di disegno**.

![Aggiungere il certificato all'area di disegno](../media/certificates/automation-certificate-add-to-canvas.png)

La figura seguente mostra un esempio dell'uso di un certificato in un runbook grafico. Questo è lo stesso dell'esempio precedente in cui viene illustrato come aggiungere un certificato a un servizio cloud da un Runbook testuale.

![Esempio di creazione grafica](../media/certificates/graphical-runbook-add-certificate.png)

### <a name="python-2-example"></a>Esempio di Python 2

Nell'esempio seguente viene illustrato come accedere ai certificati in python2 manuali operativi.

```python
# get a reference to the Azure Automation certificate
cert = automationassets.get_automation_certificate("AzureRunAsCertificate")

# returns the binary cert content  
print cert
```

## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni sull'uso dei collegamenti per controllare il flusso logico delle attività eseguite dal Runbook, vedere [collegamenti nella creazione grafica](../automation-graphical-authoring-intro.md#links-and-workflow). 
