---
title: Asset di tipo certificato in Automazione di Azure
description: I certificati possono essere archiviati in modo sicuro in Automazione di Azure, in modo che i runbook o le configurazioni DSC possano accedervi per eseguire l'autenticazione rispetto a risorse di Azure e di terze parti.  Questo articolo illustra nel dettaglio i certificati e spiega come usarli nella creazione testuale e grafica.
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 03/15/2018
ms.topic: article
manager: carmonm
ms.devlang: na
ms.tgt_pltfrm: na
ms.openlocfilehash: d4e205365b884b683928e42d538c085c4df2d6ed
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/17/2018
---
# <a name="certificate-assets-in-azure-automation"></a>Asset di tipo certificato in Automazione di Azure

I certificati possono essere archiviati in modo sicuro in Automazione di Azure, in modo che vi possano accedere i runbook o le configurazioni DSC che usano l'attività **Get-AzureRmAutomationCertificate** per le risorse di Azure Resource Manager. Questa funzionalità consente di creare runbook e configurazioni DSC che usano certificati per l'autenticazione oppure consente di aggiungerli a risorse di Azure o di terze parti.

>[!NOTE]
>Gli asset sicuri in Automazione di Azure includono credenziali, certificati, connessioni e variabili crittografate. Questi asset vengono crittografati e archiviati in Automazione di Azure usando una chiave univoca generata per ogni account di automazione. Questa chiave viene archiviata in Key Vault. Prima di archiviare un asset sicuro, la chiave viene caricata da Key Vault e quindi usata per crittografare l'asset.

## <a name="azurerm-powershell-cmdlets"></a>Cmdlet di PowerShell AzureRM
Per AzureRM, per creare e gestire asset di credenziali di automazione con Windows PowerShell, vengono usati i cmdlet della tabella seguente. Sono inclusi nel [modulo AzureRM.Automation](/powershell/azure/overview), disponibile per l'uso nei runbook di Automazione e nelle configurazioni DSC.

|Cmdlets|DESCRIZIONE|
|:---|:---|
|[Get-AzureRmAutomationCertificate](https://docs.microsoft.com/powershell/module/azurerm.automation/get-azurermautomationcertificate)|Recupera le informazioni su un certificato da usare in un runbook o in una configurazione DSC. È possibile recuperare solo il certificato stesso dall'attività Get-AutomationCertificate.|
|[New-AzureRmAutomationCertificate](https://docs.microsoft.com/powershell/module/azurerm.automation/new-azurermautomationcertificate)|Crea un nuovo certificato in Automazione di Azure.|
[Remove-AzureRmAutomationCertificate](https://docs.microsoft.com/powershell/module/azurerm.automation/remove-azurermautomationcertificate)|Rimuove un certificato da Automazione di Azure.|Crea un nuovo certificato in Automazione di Azure.
|[Set-AzureRmAutomationCertificate](https://docs.microsoft.com/powershell/module/azurerm.automation/set-azurermautomationcertificate)|Imposta le proprietà per un certificato esistente, inclusi il caricamento del file del certificato e l'impostazione della password per un file con estensione pfx.|
|[Add-AzureCertificate](https://msdn.microsoft.com/library/azure/dn495214.aspx)|Carica un certificato di servizio per il servizio cloud specificato.|

## <a name="activities"></a>attività
Le attività incluse nella tabella seguente vengono usate per accedere ai certificati in un runbook e nelle configurazioni DSC.

| attività | DESCRIZIONE |
|:---|:---|
|Get-AutomationCertificate|Ottiene un certificato da usare in un runbook o in una configurazione DSC. Restituisce un oggetto [System.Security.Cryptography.X509Certificates.X509Certificate2](https://msdn.microsoft.com/library/system.security.cryptography.x509certificates.x509certificate2.aspx).|

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

Quando si crea un nuovo certificato, si carica un file con estensione cer o pfx in Automazione di Azure. Se si contrassegna il certificato come esportabile, sarà possibile trasferirlo all'esterno dell'archivio di certificati di Automazione di Azure. Se non è esportabile, sarà possibile usarlo solo per la firma entro il runbook o configurazione DSC. Automazione di Azure richiede che il certificato abbia un provider: **Microsoft Enhanced RSA and AES Cryptographic Provider**.

### <a name="to-create-a-new-certificate-with-the-azure-portal"></a>Per creare un nuovo certificato con il portale di Azure

1. Dall'account di automazione fare clic sul riquadro **Asset** per aprire il pannello **Asset**.
1. Fare clic sul riquadro **Certificati** per aprire il pannello **Certificati**.
1. Fare clic su **Aggiungi certificato** nella parte superiore del pannello.
1. Digitare un nome per il certificato nella casella **Nome** .
1. Per cercare un file con estensione cer o pfx, fare clic su **Selezionare un file** in **Caricare un file di certificato**. Se si seleziona un file con estensione pfx, specificare una password e indicare se ne è consentita l'esportazione.
1. Fare clic su **Crea** per salvare il nuovo asset di certificato.

### <a name="to-create-a-new-certificate-with-windows-powershell"></a>Per creare un nuovo certificato con Windows PowerShell

L'esempio che segue mostra come creare un nuovo certificato di automazione e come contrassegnarlo come esportabile. Verrà importato un file con estensione pfx esistente.

```powershell-interactive
$certName = 'MyCertificate'
$certPath = '.\MyCert.pfx'
$certPwd = ConvertTo-SecureString -String 'P@$$w0rd' -AsPlainText -Force
$ResourceGroup = "ResourceGroup01"

New-AzureRmAutomationCertificate -AutomationAccountName "MyAutomationAccount" -Name $certName -Path $certPath –Password $certPwd -Exportable -ResourceGroupName $ResourceGroup
```

## <a name="using-a-certificate"></a>Utilizzo di un certificato

Per usare un certificato, usare l'attività **Get-AutomationCertificate**. Non è possibile usare il cmdlet [Get-AzureRmAutomationCertificate](https://msdn.microsoft.com/library/mt603765.aspx), poiché restituisce informazioni sull'asset di certificato, ma non il certificato stesso.

### <a name="textual-runbook-sample"></a>Esempio di Runbook testuale

Il codice di esempio seguente mostra come aggiungere un certificato a un servizio cloud in un Runbook. In questo esempio la password viene recuperata da una variabile di automazione crittografata.

```powershell-interactive
$serviceName = 'MyCloudService'
$cert = Get-AutomationCertificate -Name 'MyCertificate'
$certPwd = Get-AzureRmAutomationVariable -ResourceGroupName "ResouceGroup01" `
–AutomationAccountName "MyAutomationAccount" –Name 'MyCertPassword'
Add-AzureCertificate -ServiceName $serviceName -CertToDeploy $cert
```

### <a name="graphical-runbook-sample"></a>Esempio di Runbook grafico

Per aggiungere un'attività **Get-AutomationCertificate** a un runbook grafico, fare clic con il pulsante destro del mouse sul certificato nel riquadro della libreria dell'editor grafico e scegliere **Aggiungi ad area di disegno**.

![Aggiungere il certificato all'area di disegno](media/automation-certificates/automation-certificate-add-to-canvas.png)

La figura seguente mostra un esempio dell'uso di un certificato in un runbook grafico. Si tratta dello stesso esempio illustrato in precedenza per l'aggiunta di un certificato a un servizio cloud da un runbook testuale.

![Esempio di creazione grafica ](media/automation-certificates/graphical-runbook-add-certificate.png)

### <a name="python2-sample"></a>Esempio Python2
L'esempio seguente illustra come accedere ai certificati nei runbook Python2.

```python
# get a reference to the Azure Automation certificate
cert = automationassets.get_automation_certificate("AzureRunAsCertificate")

# returns the binary cert content  
print cert 
```

## <a name="next-steps"></a>Passaggi successivi

- Per ulteriori informazioni sull'utilizzo di collegamenti per controllare il flusso logico delle attività per le quali è progettato il runbook, vedere [Collegamenti nella creazione grafica](automation-graphical-authoring-intro.md#links-and-workflow). 
