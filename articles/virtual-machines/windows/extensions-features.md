---
title: "Estensioni e funzionalità delle macchine virtuali per Windows in Azure | Documentazione Microsoft"
description: "Informazioni sulle estensioni disponibili per le macchine virtuali di Azure, raggruppate in base a ciò che forniscono o migliorano."
services: virtual-machines-windows
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: azure-service-management,azure-resource-manager
ms.assetid: 999d63ee-890e-432e-9391-25b3fc6cde28
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/06/2017
ms.author: nepeters
ms.custom: H1Hack27Feb2017
ms.translationtype: HT
ms.sourcegitcommit: 398efef3efd6b47c76967563251613381ee547e9
ms.openlocfilehash: 1ce0eebd2585c9457d7f922898d7f2fa3e7ffad7
ms.contentlocale: it-it
ms.lasthandoff: 08/11/2017

---
# <a name="virtual-machine-extensions-and-features-for-windows"></a>Estensioni e funzionalità della macchina virtuale per Windows

Le estensioni della macchina virtuale di Azure sono piccole applicazioni che eseguono attività di configurazione e automazione post-distribuzione sulle macchine virtuali di Azure. Ad esempio, se una macchina virtuale richiede l'installazione di software, la protezione antivirus o la configurazione di Docker, è possibile usare un'estensione di macchina virtuale per completare queste attività. Le estensioni della macchina virtuale di Azure possono essere eseguite tramite l'interfaccia della riga di comando di Azure, PowerShell, i modelli di Azure Resource Manager e il portale di Azure. Le estensioni possono essere unite in bundle con una nuova distribuzione di macchina virtuale o eseguite su un sistema esistente.

Questo documento fornisce una panoramica delle estensioni macchina virtuale, i prerequisiti per l'uso delle estensioni macchina virtuale e le indicazioni su come rilevare, gestire e rimuovere le estensioni macchina virtuale. Questo documento fornisce informazioni generali perché sono disponibili molte estensioni della macchina virtuale, ognuna con una configurazione potenzialmente univoca. I dettagli sono disponibili nel documento specifico della singola estensione.

## <a name="use-cases-and-samples"></a>Casi d'uso ed esempi

Sono disponibili numerose estensioni della macchina virtuale di Azure, ognuna con un caso d'uso specifico. Alcuni esempi di casi d'uso sono:

- Applicare le configurazioni dello stato desiderato tramite PowerShell a una macchina virtuale usando l'estensione DSC per Windows. Per altre informazioni, vedere l'argomento relativo all'[Estensione DSC (Desired State Configuration) di Azure](extensions-dsc-overview.md).
- Configurare il monitoraggio di una macchina virtuale con l'estensione della macchina virtuale di Microsoft Monitoring Agent. Per altre informazioni, vedere l'argomento [Connettere macchine virtuali di Azure a Log Analytics](../../log-analytics/log-analytics-azure-vm-extension.md).
- Configurare il monitoraggio dell'infrastruttura di Azure con l'estensione Datadog. Per altre informazioni, vedere il [blog Datadog](https://www.datadoghq.com/blog/introducing-azure-monitoring-with-one-click-datadog-deployment/).
- Configurare una macchina virtuale di Azure con Chef. Per altre informazioni, vedere l'argomento [Automazione della distribuzione delle macchine virtuali di Azure con Chef](chef-automation.md)

Oltre alle estensioni specifiche del processo, è disponibile un'estensione Script personalizzato per le macchine virtuali Linux e Windows. L'estensione Script personalizzato per Windows consente l'esecuzione di qualsiasi script PowerShell su una macchina virtuale. È utile per la progettazione di distribuzioni di Azure che richiedono una configurazione oltre a quella offerta dagli strumenti nativi di Azure. Per altre informazioni, vedere [Estensione Script personalizzato per macchine virtuali Windows](extensions-customscript.md).


## <a name="prerequisites"></a>Prerequisiti

Ogni estensione macchina virtuale può avere un insieme specifico di prerequisiti. Ad esempio, un prerequisito dell'estensione della macchina virtuale Docker è una distribuzione Linux supportata. La documentazione specifica dell'estensione illustra in dettaglio i requisiti delle singole estensioni.

### <a name="azure-vm-agent"></a>Agente di macchine virtuali di Azure
L'agente di macchine virtuali di Azure gestisce l'interazione tra una macchina virtuale di Azure e il controller di infrastruttura di Azure. L'agente di macchine virtuali è responsabile di molti aspetti funzionali della distribuzione e della gestione delle macchine virtuali di Azure, tra cui l'esecuzione delle estensioni delle macchine virtuali. L'agente di macchine virtuali di Azure è preinstallato nelle immagini di Azure Marketplace e può essere installato nei sistemi operativi supportati.

Per informazioni sui sistemi operativi supportati e per le istruzioni di installazione, vedere [Agente delle macchine virtuali di Azure](agent-user-guide.md).

## <a name="discover-vm-extensions"></a>Individuare le estensioni della macchina virtuale
Molte estensioni delle macchine virtuali di diverso tipo sono disponibili per l'uso con macchine virtuali di Azure. Per visualizzare un elenco completo, eseguire il comando seguente con il modulo Azure Resource Manager di PowerShell. Assicurarsi di specificare il percorso desiderato quando si esegue questo comando.

```powershell
Get-AzureRmVmImagePublisher -Location WestUS | `
Get-AzureRmVMExtensionImageType | `
Get-AzureRmVMExtensionImage | Select Type, Version
```

## <a name="run-vm-extensions"></a>Eseguire le estensioni della macchina virtuale

Le estensioni della macchina virtuale di Azure possono essere eseguite nelle macchine virtuali esistenti e sono utili quando è necessario apportare modifiche alla configurazione o ripristinare la connettività in una VM già distribuita. Le estensioni della macchina virtuale possono essere anche unite in bundle con le distribuzioni del modello di Azure Resource Manager. L'uso delle estensioni con i modelli di Resource Manager consente di distribuire e configurare le macchine virtuali di Azure senza che sia necessario l'intervento post-distribuzione.

Per eseguire un'estensione in una macchina virtuale esistente, è possibile usare i metodi seguenti.

### <a name="powershell"></a>PowerShell

Molti comandi di PowerShell vengono utilizzati per l'esecuzione di estensioni singole. Per visualizzare un elenco, eseguire i seguenti comandi PowerShell.

```powershell
get-command Set-AzureRM*Extension* -Module AzureRM.Compute
```

L'output generato sarà simile al seguente:

```powershell
CommandType     Name                                               Version    Source
-----------     ----                                               -------    ------
Cmdlet          Set-AzureRmVMAccessExtension                       2.2.0      AzureRM.Compute
Cmdlet          Set-AzureRmVMADDomainExtension                     2.2.0      AzureRM.Compute
Cmdlet          Set-AzureRmVMAEMExtension                          2.2.0      AzureRM.Compute
Cmdlet          Set-AzureRmVMBackupExtension                       2.2.0      AzureRM.Compute
Cmdlet          Set-AzureRmVMBginfoExtension                       2.2.0      AzureRM.Compute
Cmdlet          Set-AzureRmVMChefExtension                         2.2.0      AzureRM.Compute
Cmdlet          Set-AzureRmVMCustomScriptExtension                 2.2.0      AzureRM.Compute
Cmdlet          Set-AzureRmVMDiagnosticsExtension                  2.2.0      AzureRM.Compute
Cmdlet          Set-AzureRmVMDiskEncryptionExtension               2.2.0      AzureRM.Compute
Cmdlet          Set-AzureRmVMDscExtension                          2.2.0      AzureRM.Compute
Cmdlet          Set-AzureRmVMExtension                             2.2.0      AzureRM.Compute
Cmdlet          Set-AzureRmVMSqlServerExtension                    2.2.0      AzureRM.Compute
```

Nell'esempio seguente si utilizza l'estensione Script personalizzato per scaricare uno script da un archivio GitHub nella macchina virtuale di destinazione e quindi eseguire lo script. Per altre informazioni sull'estensione Script personalizzato, vedere [Custom Script extension overview](extensions-customscript.md) (Panoramica delle estensioni dello script personalizzato).

```powershell
Set-AzureRmVMCustomScriptExtension -ResourceGroupName "myResourceGroup" `
    -VMName "myVM" -Name "myCustomScript" `
    -FileUri "https://raw.githubusercontent.com/neilpeterson/nepeters-azure-templates/master/windows-custom-script-simple/support-scripts/Create-File.ps1" `
    -Run "Create-File.ps1" -Location "West US"
```

In questo esempio, l'estensione Accesso alla macchina virtuale viene usata per reimpostare la password amministrativa di una macchina virtuale Windows. Per altre informazioni sull'estensione Accesso alla macchina virtuale, vedere [Reset Remote Desktop service in a Windows VM](reset-rdp.md) (Reimpostare il servizio Desktop Remoto in una macchina virtuale Windows).

```powershell
$cred=Get-Credential

Set-AzureRmVMAccessExtension -ResourceGroupName "myResourceGroup" -VMName "myVM" -Name "myVMAccess" `
    -Location WestUS -UserName $cred.GetNetworkCredential().Username `
    -Password $cred.GetNetworkCredential().Password -typeHandlerVersion "2.0"
```

Il comando `Set-AzureRmVMExtension` può essere utilizzato per avviare qualsiasi estensione della macchina virtuale. Per altre informazioni, vedere il [riferimento Set-AzureRmVMExtension](https://msdn.microsoft.com/en-us/library/mt603745.aspx).


### <a name="azure-portal"></a>Portale di Azure

Un'estensione macchina virtuale può essere applicata a una macchina virtuale esistente tramite il portale di Azure. A tale scopo, selezionare la macchina virtuale che si desidera utilizzare, scegliere **Estensioni** e fare clic su **Aggiungi**. In questo modo si ottiene un elenco di estensioni disponibili. Selezionare quella desiderata, quindi seguire le istruzioni nella procedura guidata.

L'immagine seguente illustra l'installazione dell'estensione Microsoft Antimalware dal portale di Azure.

![Installare un'estensione antimalware](./media/extensions-features/installantimalwareextension.png)

### <a name="azure-resource-manager-templates"></a>Modelli di Gestione risorse di Azure

Le estensioni macchina virtuale possono essere aggiunte a un modello di Azure Resource Manager ed eseguite con la distribuzione del modello. La distribuzione di estensioni con un modello è utile per la creazione di distribuzioni di Azure completamente configurate. Ad esempio, il codice JSON seguente viene eseguito da un modello di Resource Manager che consente di distribuire un set di macchine virtuali con carico bilanciato e un database SQL di Azure, quindi installa un'applicazione .NET Core in ogni macchina virtuale. L'estensione della macchina virtuale gestisce l'installazione del software.

Per altre informazioni, vedere il [modello di Resource Manager completo](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-windows).

```json
{
    "apiVersion": "2015-06-15",
    "type": "extensions",
    "name": "config-app",
    "location": "[resourceGroup().location]",
    "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'),copyindex())]",
    "[variables('musicstoresqlName')]"
    ],
    "tags": {
    "displayName": "config-app"
    },
    "properties": {
    "publisher": "Microsoft.Compute",
    "type": "CustomScriptExtension",
    "typeHandlerVersion": "1.4",
    "autoUpgradeMinorVersion": true,
    "settings": {
        "fileUris": [
        "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/scripts/configure-music-app.ps1"
        ]
    },
    "protectedSettings": {
        "commandToExecute": "[concat('powershell -ExecutionPolicy Unrestricted -File configure-music-app.ps1 -user ',parameters('adminUsername'),' -password ',parameters('adminPassword'),' -sqlserver ',variables('musicstoresqlName'),'.database.windows.net')]"
    }
    }
}
```

Per altre informazioni, vedere [Authoring Azure Resource Manager templates with Windows VM extensions](template-description.md#extensions) (Creazione di modelli di Azure Resource Manager con le estensioni della macchina virtuale Windows).

## <a name="secure-vm-extension-data"></a>Proteggere i dati dell'estensione della macchina virtuale

Quando si esegue un'estensione macchina virtuale, potrebbe essere necessario includere informazioni riservate, ad esempio le credenziali, i nomi degli account di archiviazione e le chiavi di accesso dell'account di archiviazione. Molte estensioni della macchina virtuale includono una configurazione protetta, che consente di crittografare dati e di decrittografarli solo all'interno della macchina virtuale di destinazione. Ogni estensione ha uno schema di configurazione protetto specifico che sarà descritto in maniera dettagliata nella documentazione specifica dell'estensione.

L'esempio seguente illustra un'istanza dell'estensione Script personalizzato per Windows. Si noti che il comando da eseguire include un insieme di credenziali. In questo esempio, il comando da eseguire non verrà crittografato.


```json
{
    "apiVersion": "2015-06-15",
    "type": "extensions",
    "name": "config-app",
    "location": "[resourceGroup().location]",
    "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'),copyindex())]",
    "[variables('musicstoresqlName')]"
    ],
    "tags": {
    "displayName": "config-app"
    },
    "properties": {
    "publisher": "Microsoft.Compute",
    "type": "CustomScriptExtension",
    "typeHandlerVersion": "1.4",
    "autoUpgradeMinorVersion": true,
    "settings": {
        "fileUris": [
        "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/scripts/configure-music-app.ps1"
        ],
        "commandToExecute": "[concat('powershell -ExecutionPolicy Unrestricted -File configure-music-app.ps1 -user ',parameters('adminUsername'),' -password ',parameters('adminPassword'),' -sqlserver ',variables('musicstoresqlName'),'.database.windows.net')]"
    }
    }
}
```

Proteggere la stringa di esecuzione spostando la proprietà **comando da eseguire** nella configurazione **protetta**.

```json
{
    "apiVersion": "2015-06-15",
    "type": "extensions",
    "name": "config-app",
    "location": "[resourceGroup().location]",
    "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'),copyindex())]",
    "[variables('musicstoresqlName')]"
    ],
    "tags": {
    "displayName": "config-app"
    },
    "properties": {
    "publisher": "Microsoft.Compute",
    "type": "CustomScriptExtension",
    "typeHandlerVersion": "1.4",
    "autoUpgradeMinorVersion": true,
    "settings": {
        "fileUris": [
        "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/scripts/configure-music-app.ps1"
        ]
    },
    "protectedSettings": {
        "commandToExecute": "[concat('powershell -ExecutionPolicy Unrestricted -File configure-music-app.ps1 -user ',parameters('adminUsername'),' -password ',parameters('adminPassword'),' -sqlserver ',variables('musicstoresqlName'),'.database.windows.net')]"
    }
    }
}
```

## <a name="troubleshoot-vm-extensions"></a>Risoluzione dei problemi relativi alle estensioni della macchina virtuale

Ogni estensione macchina virtuale può disporre di passaggi per la risoluzione dei problemi specifici. Ad esempio, quando si usa l'estensione script personalizzato, i dettagli sull'esecuzione dello script sono reperibili in locale nella macchina virtuale in cui è stata eseguita l'estensione. Tutti i passaggi per la risoluzione dei problemi specifici dell'estensione sono descritti in dettaglio nella documentazione specifica dell'estensione.

I passaggi per la risoluzione dei problemi seguenti si applicano a tutte le estensioni macchina virtuale.

### <a name="view-extension-status"></a>Visualizzare lo stato dell'estensione

Dopo l'esecuzione dell'estensione di una macchina virtuale, usare il comando seguente di PowerShell per restituire lo stato dell'estensione. Sostituire i nomi dei parametri di esempio con i valori desiderati. Il parametro `Name` accetta il nome specificato per l'estensione durante l'esecuzione.

```PowerShell
Get-AzureRmVMExtension -ResourceGroupName myResourceGroup -VMName myVM -Name myExtensionName
```

L'output è simile al seguente:

```json
ResourceGroupName       : myResourceGroup
VMName                  : myVM
Name                    : myExtensionName
Location                : westus
Etag                    : null
Publisher               : Microsoft.Azure.Extensions
ExtensionType           : DockerExtension
TypeHandlerVersion      : 1.0
Id                      : /subscriptions/mySubscriptionIS/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM/extensions/myExtensionName
PublicSettings          :
ProtectedSettings       :
ProvisioningState       : Succeeded
Statuses                :
SubStatuses             :
AutoUpgradeMinorVersion : False
ForceUpdateTag          :
```

Lo stato di esecuzione dell'estensione è disponibile anche nel portale di Azure. Per visualizzare lo stato di un'estensione, selezionare la macchina virtuale, scegliere **Estensioni** e selezionare l'estensione desiderata.

### <a name="rerun-vm-extensions"></a>Eseguire nuovamente le estensioni della macchina virtuale

In alcuni casi potrebbe essere necessario rieseguire un'estensione della macchina virtuale. È possibile farlo rimuovendo l'estensione e quindi eseguendo di nuovo l'estensione con il metodo di esecuzione scelto. Per rimuovere un'estensione, eseguire il comando seguente con il modulo Azure PowerShell. Sostituire i nomi dei parametri di esempio con i valori desiderati.

```powershell
Remove-AzureRmVMExtension -ResourceGroupName myResourceGroup -VMName myVM -Name myExtensionName
```

Un'estensione può essere eliminata anche tramite il portale di Azure. A tale scopo, procedere come segue:

1. Selezionare una macchina virtuale.
2. Selezionare **Estensioni**.
3. Selezionare l'estensione desiderata.
4. Selezionare **Disinstalla**.

## <a name="common-vm-extensions-reference"></a>Riferimento alle estensioni della macchina virtuale comuni
| Nome estensione | Descrizione | Altre informazioni |
| --- | --- | --- |
| Estensione Script personalizzato per Windows |Eseguire script su una macchina virtuale di Azure. |[Estensione script personalizzata per Windows](extensions-customscript.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) |
| Estensione DSC per Windows |Estensione PowerShell DSC (Desired State Configuration) |[Estensione DSC per Windows](extensions-dsc-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) |
| Estensione di Diagnostica di Azure |Gestisce Diagnostica di Azure. |[Estensione di Diagnostica di Azure](https://azure.microsoft.com/blog/windows-azure-virtual-machine-monitoring-with-wad-extension/) |
| Estensione dell'accesso alla VM di Azure |Gestire gli utenti e le credenziali |[Estensione dell'accesso alle macchine virtuali per Linux](https://azure.microsoft.com/en-us/blog/using-vmaccess-extension-to-reset-login-credentials-for-linux-vm/) |

