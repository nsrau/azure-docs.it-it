---
title: Estensioni e funzionalità delle macchine virtuali di Azure per Windows | Microsoft Docs
description: Informazioni sulle estensioni disponibili per le macchine virtuali di Azure, raggruppate in base a ciò che forniscono o migliorano.
services: virtual-machines-windows
documentationcenter: ''
author: roiyz-msft
manager: jeconnoc
editor: ''
tags: azure-service-management,azure-resource-manager
ms.assetid: 999d63ee-890e-432e-9391-25b3fc6cde28
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/30/2018
ms.author: roiyz
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ce13f053c2adee6a9a347a4162b60cc6d6b40eda
ms.sourcegitcommit: 04716e13cc2ab69da57d61819da6cd5508f8c422
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/02/2019
ms.locfileid: "58849759"
---
# <a name="virtual-machine-extensions-and-features-for-windows"></a>Estensioni e funzionalità della macchina virtuale per Windows

Le estensioni macchina virtuale di Azure sono piccole applicazioni che eseguono attività di configurazione e automazione post-distribuzione nelle macchine virtuali di Azure. Ad esempio, se una macchina virtuale richiede l'installazione di software, la protezione antivirus o l'esecuzione di uno script al suo interno, è possibile usare un'estensione macchina virtuale. Le estensioni macchina virtuale di Azure possono essere eseguite tramite l'interfaccia della riga di comando di Azure, PowerShell, i modelli di Azure Resource Manager e il portale di Azure. Le estensioni possono essere aggregate con una nuova distribuzione di macchina virtuale o eseguite su un sistema esistente.

Questo articolo offre una panoramica delle estensioni macchina virtuale, i prerequisiti per l'uso di queste estensioni di Azure e le indicazioni su come rilevare, gestire e rimuovere le estensioni. Questo articolo offre informazioni generali perché sono disponibili molte estensioni macchina virtuale, ognuna con una configurazione potenzialmente univoca. I dettagli sono disponibili nel documento specifico della singola estensione.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="use-cases-and-samples"></a>Casi d'uso ed esempi

Sono disponibili numerose estensioni della macchina virtuale di Azure, ognuna con uno specifico caso d'uso. Di seguito sono riportati alcuni esempi:

- Applicare le configurazioni dello stato desiderato tramite PowerShell a una macchina virtuale usando l'estensione DSC per Windows. Per altre informazioni, vedere l'argomento relativo all'[Estensione DSC (Desired State Configuration) di Azure](dsc-overview.md).
- Configurare il monitoraggio di una macchina virtuale con l'estensione macchina virtuale Microsoft Monitoring Agent. Per altre informazioni, vedere [connettere macchine virtuali di Azure per i log di monitoraggio di Azure](../../log-analytics/log-analytics-azure-vm-extension.md).
- Configurare una macchina virtuale di Azure usando Chef. Per altre informazioni, vedere l'argomento [Automazione della distribuzione delle macchine virtuali di Azure con Chef](../windows/chef-automation.md).
- Configurare il monitoraggio dell'infrastruttura di Azure con l'estensione Datadog. Per altre informazioni, vedere il [blog Datadog](https://www.datadoghq.com/blog/introducing-azure-monitoring-with-one-click-datadog-deployment/).


Oltre alle estensioni specifiche del processo, è disponibile un'estensione Script personalizzato per le macchine virtuali Linux e Windows. L'estensione Script personalizzato per Windows consente l'esecuzione di qualsiasi script PowerShell in una macchina virtuale. Gli script personalizzati sono utili per la progettazione di distribuzioni di Azure che richiedono una configurazione in aggiunta a quella offerta dagli strumenti nativi di Azure. Per altre informazioni, vedere [Estensione Script personalizzato per macchine virtuali Windows](custom-script-windows.md).

## <a name="prerequisites"></a>Prerequisiti

Per gestire l'estensione nella macchina virtuale, è necessario aver installato l'agente Windows di Azure. Alcune estensioni individuali hanno prerequisiti, ad esempio l'accesso alle risorse o alle dipendenze.

### <a name="azure-vm-agent"></a>Agente VM di Azure

L'agente di macchine virtuali di Azure gestisce le interazioni tra una macchina virtuale di Azure e il controller di infrastruttura di Azure. L'agente di macchine virtuali è responsabile di molti aspetti funzionali della distribuzione e della gestione delle macchine virtuali di Azure, tra cui l'esecuzione delle estensioni macchina virtuale. L'agente di macchine virtuali di Azure è preinstallato nelle immagini di Azure Marketplace e può essere installato manualmente nei sistemi operativi supportati. L'agente di macchine virtuali di Azure per Windows è noto come agente guest di Windows.

Per informazioni sui sistemi operativi supportati e per le istruzioni di installazione, vedere [Agente delle macchine virtuali di Azure](agent-windows.md).

#### <a name="supported-agent-versions"></a>Versioni supportate dell'agente

Per garantire la migliore esperienza possibile, sono previsti requisiti minimi per le versioni supportate dell'agente. Per altre informazioni, vedere [questo articolo](https://support.microsoft.com/en-us/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support).

#### <a name="supported-oses"></a>Sistemi operativi supportati

L'agente guest di Windows viene eseguito su più sistemi operativi, tuttavia il framework delle estensioni prevede un limite per i sistemi operativi. Per altre informazioni, vedere [questo articolo](https://support.microsoft.com/en-us/help/4078134/azure-extension-supported-operating-systems
).

Alcune estensioni non sono supportate in tutti i sistemi operativi e possono generare il *codice di errore 51, "Sistema operativo non supportato"*. Vedere la documentazione della singola estensione per informazioni sul supporto.

#### <a name="network-access"></a>Accesso alla rete

I pacchetti di estensioni vengono scaricati dal repository delle estensioni di Archiviazione di Azure, mentre i caricamenti dello stato delle estensioni vengono pubblicati in Archiviazione di Azure. Se si usa una versione [supportata](https://support.microsoft.com/en-us/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support) degli agenti, non è necessario consentire l'accesso ad Archiviazione di Azure nell'area della macchina virtuale, perché è possibile usare l'agente per reindirizzare la comunicazione al controller di infrastruttura di Azure. Se è in uso una versione non supportata dell'agente, è necessario consentire l'accesso in uscita ad Archiviazione di Azure in tale area dalla macchina virtuale.

> [!IMPORTANT]
> Se l'accesso a *168.63.129.16* è stato bloccato con il firewall guest, le estensioni non rispettano quanto specificato sopra.

Gli agenti possono essere usati solo per caricare i pacchetti di estensioni e lo stato della creazione di report. Ad esempio, se l'installazione di un'estensione richiede il download di uno script da GitHub (script personalizzato) o ha bisogno di accedere ad Archiviazione di Azure (Backup di Azure), allora è necessario aprire altre porte del firewall/gruppo di sicurezza di rete. Estensioni diverse hanno requisiti diversi, perché sono applicazioni indipendenti. È possibile consentire l'accesso ad Archiviazione di Azure per le estensioni usando i tag di servizio del NSG per [Archiviazione](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags).

L'agente guest di Windows non dispone del supporto del server proxy per il reindirizzamento delle richieste al traffico dell'agente.

## <a name="discover-vm-extensions"></a>Individuare le estensioni della macchina virtuale

Sono disponibili molte estensioni diverse delle macchine virtuali da usare con macchine virtuali di Azure. Per visualizzare un elenco completo usare [Get-AzVMExtensionImage](https://docs.microsoft.com/powershell/module/az.compute/get-azvmextensionimage). L'esempio seguente elenca tutte le estensioni disponibili nella posizione *WestUS*:

```powershell
Get-AzVmImagePublisher -Location "WestUS" | `
Get-AzVMExtensionImageType | `
Get-AzVMExtensionImage | Select Type, Version
```

## <a name="run-vm-extensions"></a>Eseguire le estensioni della macchina virtuale

Le estensioni macchina virtuale di Azure vengono eseguite nelle macchine virtuali esistenti e questo è utile quando è necessario apportare modifiche alla configurazione o ripristinare la connettività in una macchina virtuale già distribuita. Le estensioni della macchina virtuale possono essere anche unite in bundle con le distribuzioni del modello di Azure Resource Manager. L'uso delle estensioni con i modelli di Resource Manager consente di distribuire e configurare le macchine virtuali di Azure senza l'intervento post-distribuzione.

Per eseguire un'estensione in una macchina virtuale esistente, è possibile usare i metodi seguenti.

### <a name="powershell"></a>PowerShell

Molti comandi di PowerShell vengono utilizzati per l'esecuzione di estensioni singole. Per visualizzare un elenco, usare [Get-Command](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/get-command) e applicare il filtro *Estensione*:

```powershell
Get-Command Set-Az*Extension* -Module Az.Compute
```

L'output generato sarà simile al seguente:

```powershell
CommandType     Name                                          Version    Source
-----------     ----                                          -------    ------
Cmdlet          Set-AzVMAccessExtension                       4.5.0      Az.Compute
Cmdlet          Set-AzVMADDomainExtension                     4.5.0      Az.Compute
Cmdlet          Set-AzVMAEMExtension                          4.5.0      Az.Compute
Cmdlet          Set-AzVMBackupExtension                       4.5.0      Az.Compute
Cmdlet          Set-AzVMBginfoExtension                       4.5.0      Az.Compute
Cmdlet          Set-AzVMChefExtension                         4.5.0      Az.Compute
Cmdlet          Set-AzVMCustomScriptExtension                 4.5.0      Az.Compute
Cmdlet          Set-AzVMDiagnosticsExtension                  4.5.0      Az.Compute
Cmdlet          Set-AzVMDiskEncryptionExtension               4.5.0      Az.Compute
Cmdlet          Set-AzVMDscExtension                          4.5.0      Az.Compute
Cmdlet          Set-AzVMExtension                             4.5.0      Az.Compute
Cmdlet          Set-AzVMSqlServerExtension                    4.5.0      Az.Compute
Cmdlet          Set-AzVmssDiskEncryptionExtension             4.5.0      Az.Compute
```

Nell'esempio seguente si utilizza l'estensione Script personalizzato per scaricare uno script da un archivio GitHub nella macchina virtuale di destinazione e quindi eseguire lo script. Per altre informazioni sull'estensione Script personalizzato, vedere [Custom Script extension overview](custom-script-windows.md) (Panoramica delle estensioni dello script personalizzato).

```powershell
Set-AzVMCustomScriptExtension -ResourceGroupName "myResourceGroup" `
    -VMName "myVM" -Name "myCustomScript" `
    -FileUri "https://raw.githubusercontent.com/neilpeterson/nepeters-azure-templates/master/windows-custom-script-simple/support-scripts/Create-File.ps1" `
    -Run "Create-File.ps1" -Location "West US"
```

Nell'esempio seguente l'estensione Accesso alla macchina virtuale viene usata per reimpostare la password amministrativa di una macchina virtuale Windows definendo una password temporanea. Per altre informazioni sull'estensione Accesso alla macchina virtuale, vedere [Reset Remote Desktop service in a Windows VM](../windows/reset-rdp.md) (Reimpostare il servizio Desktop Remoto in una macchina virtuale Windows). Dopo l'esecuzione, è consigliabile reimpostare la password al primo accesso:

```powershell
$cred=Get-Credential

Set-AzVMAccessExtension -ResourceGroupName "myResourceGroup" -VMName "myVM" -Name "myVMAccess" `
    -Location WestUS -UserName $cred.GetNetworkCredential().Username `
    -Password $cred.GetNetworkCredential().Password -typeHandlerVersion "2.0"
```

Il comando `Set-AzVMExtension` può essere utilizzato per avviare qualsiasi estensione della macchina virtuale. Per altre informazioni, vedere il [riferimento Set-AzVMExtension](https://docs.microsoft.com/powershell/module/az.compute/set-azvmextension).


### <a name="azure-portal"></a>Portale di Azure

Le estensioni macchina virtuale possono essere applicate a una macchina virtuale esistente tramite il portale di Azure. Selezionare la macchina virtuale nel portale, scegliere **Estensioni**, quindi selezionare **Aggiungi**. Scegliere l'estensione desiderata dall'elenco di quelle disponibili e quindi seguire le istruzioni della procedura guidata.

L'esempio seguente illustra l'installazione dell'estensione Microsoft Antimalware dal portale di Azure:

![Installare un'estensione antimalware](./media/features-windows/installantimalwareextension.png)

### <a name="azure-resource-manager-templates"></a>Modelli di Gestione risorse di Azure

Le estensioni della macchina virtuale possono essere aggiunte a un modello di Azure Resource Manager ed eseguite con la distribuzione del modello. Quando si distribuisce un'estensione con un modello, è possibile creare distribuzioni di Azure completamente configurate. Ad esempio, il codice JSON seguente proviene da un modello di Resource Manager che consente di distribuire un set di macchine virtuali con bilanciamento del carico e un database SQL di Azure, quindi installa un'applicazione .NET Core in ogni macchina virtuale. L'estensione della macchina virtuale gestisce l'installazione del software.

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
    "typeHandlerVersion": "1.9",
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

Per altre informazioni sulla creazione di modelli di Resource Manager, vedere [Creare modelli di Azure Resource Manager con estensioni macchina virtuale Windows](../windows/template-description.md#extensions).

## <a name="secure-vm-extension-data"></a>Proteggere i dati dell'estensione della macchina virtuale

Quando si esegue un'estensione macchina virtuale, potrebbe essere necessario includere informazioni riservate, ad esempio credenziali, nomi degli account di archiviazione e chiavi di accesso degli account di archiviazione. Molte estensioni macchina virtuale includono una configurazione protetta, che consente di crittografare dati e di decrittografarli solo all'interno della macchina virtuale di destinazione. Ogni estensione dispone di uno schema di configurazione protetta specifico, descritto in dettaglio nella documentazione specifica dell'estensione.

L'esempio seguente illustra un'istanza dell'estensione Script personalizzato per Windows. Il comando da eseguire include un set di credenziali. In questo esempio il comando da eseguire non è crittografato:

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
    "typeHandlerVersion": "1.9",
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

Lo spostamento della proprietà del **comando da eseguire** nella configurazione **protetta** consente di proteggere la stringa di esecuzione, come mostrato nell'esempio seguente:

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
    "typeHandlerVersion": "1.9",
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

### <a name="how-do-agents-and-extensions-get-updated"></a>Aggiornamento di agenti ed estensioni

Agenti ed estensioni condividono il meccanismo di aggiornamento. Alcuni aggiornamenti non richiedono regole del firewall aggiuntive.

Quando è disponibile un aggiornamento, viene installato nella macchina virtuale solo se sono presenti una modifica alle estensioni e altre modifiche al modello di macchina virtuale, ad esempio:

- Dischi dati
- Estensioni
- Contenitore della diagnostica di avvio
- Segreti del sistema operativo guest
- Dimensioni macchina virtuale
- Profilo di rete

I server di pubblicazione rendono disponibili gli aggiornamenti nelle varie aree geografiche in momenti diversi, quindi è possibile che le macchine virtuali in aree diverse eseguano versioni diverse.

#### <a name="listing-extensions-deployed-to-a-vm"></a>Elencare le estensioni distribuite in una macchina virtuale

```powershell
$vm = Get-AzVM -ResourceGroupName "myResourceGroup" -VMName "myVM"
$vm.Extensions | select Publisher, VirtualMachineExtensionType, TypeHandlerVersion
```

```powershell
Publisher             VirtualMachineExtensionType          TypeHandlerVersion
---------             ---------------------------          ------------------
Microsoft.Compute     CustomScriptExtension                1.9
```

#### <a name="agent-updates"></a>Aggiornamenti dell'agente

L'agente guest di Windows contiene solo il *codice Extension Handling*; il *codice Windows Provisioning* è separato. È possibile disinstallare l'agente guest di Windows. Non è possibile disabilitare l'aggiornamento automatico dell'agente guest di Windows.

Il *codice Extension Handling* è responsabile della comunicazione con l'infrastruttura di Azure e della gestione delle operazioni di estensione macchina virtuale, ad esempio installazioni, stato della creazione di report, aggiornamento e rimozione delle singole estensioni. Gli aggiornamenti contengono correzioni per la sicurezza, correzioni di bug e miglioramenti al *codice Extension Handling*.

Per verificare la versione in esecuzione, vedere [Detecting installed Windows Guest Agent](agent-windows.md#detect-the-vm-agent) (Rilevamento dell'agente guest di Windows installato).

#### <a name="extension-updates"></a>Aggiornamenti delle estensioni

Quando è disponibile l'aggiornamento di un'estensione, l'agente guest di Windows lo scarica e lo installa. Gli aggiornamenti automatici delle estensioni sono *secondari* oppure *aggiornamenti rapidi*. È possibile accettare o rifiutare esplicitamente gli aggiornamenti *secondari* delle estensioni quando si effettua il provisioning dell'estensione. L'esempio seguente mostra come aggiornare automaticamente le versioni secondarie in un modello di Resource Manager con *autoUpgradeMinorVersion": true,'*:

```json
    "properties": {
    "publisher": "Microsoft.Compute",
    "type": "CustomScriptExtension",
    "typeHandlerVersion": "1.9",
    "autoUpgradeMinorVersion": true,
    "settings": {
        "fileUris": [
        "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/scripts/configure-music-app.ps1"
        ]
    },
```

Per ottenere le più recenti versioni secondarie delle correzioni di bug, si consiglia vivamente di selezionare sempre l'aggiornamento automatico nelle distribuzioni delle estensioni. Non è possibile rifiutare esplicitamente gli aggiornamenti rapidi che contengono correzioni di bug chiave o correzioni per la sicurezza.

### <a name="how-to-identify-extension-updates"></a>Come identificare gli aggiornamenti delle estensioni

#### <a name="identifying-if-the-extension-is-set-with-autoupgrademinorversion-on-a-vm"></a>Identificare se è impostato autoUpgradeMinorVersion per l'estensione in una macchina virtuale

È possibile determinare dal modello di macchina virtuale se il provisioning dell'estensione è stato eseguito con 'autoUpgradeMinorVersion'. Per controllare, usare [Get-AzVm](https://docs.microsoft.com/powershell/module/az.compute/get-azvm) e specificare il nome del gruppo di risorse e della macchina virtuale come indicato di seguito:

```powerShell
 $vm = Get-AzVm -ResourceGroupName "myResourceGroup" -VMName "myVM"
 $vm.Extensions
```

L'output di esempio seguente mostra che *autoUpgradeMinorVersion* è impostato su *true*:

```powershell
ForceUpdateTag              :
Publisher                   : Microsoft.Compute
VirtualMachineExtensionType : CustomScriptExtension
TypeHandlerVersion          : 1.9
AutoUpgradeMinorVersion     : True
```

#### <a name="identifying-when-an-autoupgrademinorversion-occurred"></a>Identificare quando è stato eseguito autoUpgradeMinorVersion

Per vedere quando è stato eseguito un aggiornamento dell'estensione, esaminare i log dell'agente nella macchina virtuale in *C:\WindowsAzure\Logs\WaAppAgent.log*

Nell'esempio seguente nella macchina virtuale era installato *Microsoft.Compute.CustomScriptExtension 1.8*. Era disponibile un aggiornamento rapido alla versione *1.9*:

```powershell
[INFO]  Getting plugin locations for plugin 'Microsoft.Compute.CustomScriptExtension'. Current Version: '1.8', Requested Version: '1.9'
[INFO]  Auto-Upgrade mode. Highest public version for plugin 'Microsoft.Compute.CustomScriptExtension' with requested version: '1.9', is: '1.9'
```

## <a name="agent-permissions"></a>Autorizzazioni dell'agente

Per svolgere le attività, è necessario che l'agente venga eseguito come *sistema locale*.

## <a name="troubleshoot-vm-extensions"></a>Risoluzione dei problemi relativi alle estensioni della macchina virtuale

Ogni estensione macchina virtuale può richiedere passaggi per la risoluzione dei problemi specifici per l'estensione. Ad esempio, quando si usa l'estensione di script personalizzato, i dettagli sull'esecuzione dello script sono disponibili in locale nella macchina virtuale in cui è stata eseguita l'estensione. Tutti i passaggi per la risoluzione dei problemi specifici dell'estensione sono descritti in dettaglio nella documentazione specifica dell'estensione.

I passaggi seguenti per la risoluzione dei problemi sono validi per tutte le estensioni macchina virtuale.

1. Per controllare il log dell'agente guest di Windows, esaminare l'attività di quando il provisioning dell'estensione è stato eseguito in *C:\WindowsAzure\Logs\WaAppAgent.txt*

2. Per altri dettagli, controllare i log effettivi dell'estensione in *C:\WindowsAzure\Logs\Plugins\<extensionName>*

3. Controllare le sezioni della documentazione sulla risoluzione dei problemi specifica per l'estensione relative a codici di errore, problemi noti e così via.

4. Esaminare i log di sistema. Controllare la presenza di altre operazioni che potrebbero aver interferito con l'estensione, ad esempio un'installazione a esecuzione prolungata di un'altra applicazione che ha richiesto l'accesso di gestione pacchetti esclusiva.

### <a name="common-reasons-for-extension-failures"></a>Motivi comuni che causano errori delle estensioni

1. Il limite di tempo per l'esecuzione delle estensioni è di 20 minuti. Fanno eccezione le estensioni Script personalizzato, Chef e DSC che hanno 90 minuti. Se la distribuzione supera questo tempo, viene contrassegnata come timeout. Il problema potrebbe essere causato da macchine virtuali con risorse insufficienti, altre configurazioni di macchine virtuali o attività di avvio che utilizzano grandi quantità di risorse mentre l'estensione sta tentando di effettuare il provisioning.

2. Prerequisiti minimi non soddisfatti. Alcune estensioni hanno dipendenze negli SKU di macchine virtuali, ad esempio le immagini HPC. Le estensioni possono prevedere determinati requisiti di accesso alla rete, ad esempio la comunicazione con servizi pubblici o Archiviazione di Azure. Altri esempi potrebbero essere l'accesso al repository di pacchetti, l'esaurimento dello spazio su disco o restrizioni di sicurezza.

3. Accesso esclusivo alla gestione pacchetti. In alcuni casi è possibile riscontrare un conflitto tra la configurazione della macchina virtuale a esecuzione prolungata e l'installazione dell'estensione, in cui entrambi gli elementi hanno bisogno di accesso esclusivo alla gestione pacchetti.

### <a name="view-extension-status"></a>Visualizzare lo stato dell'estensione

Dopo l'esecuzione di un'estensione macchina virtuale da una macchina virtuale, usare [Get-AzVM](https://docs.microsoft.com/powershell/module/az.compute/get-azvm) per restituire lo stato dell'estensione. *Substatuses[0]* indica che il provisioning dell'estensione ha avuto esito positivo, vale a dire che l'estensione è stata distribuita alla macchina virtuale, ma l'esecuzione dell'estensione nella macchina virtuale ha avuto esito negativo (*Substatuses[1]*).

```powershell
Get-AzVM -ResourceGroupName "myResourceGroup" -VMName "myVM" -Status
```

L'output è simile a quello dell'esempio seguente:

```powershell
Extensions[0]           :
  Name                  : CustomScriptExtension
  Type                  : Microsoft.Compute.CustomScriptExtension
  TypeHandlerVersion    : 1.9
  Substatuses[0]        :
    Code                : ComponentStatus/StdOut/succeeded
    Level               : Info
    DisplayStatus       : Provisioning succeeded
    Message             : Windows PowerShell \nCopyright (C) Microsoft Corporation. All rights reserved.\n
  Substatuses[1]        :
    Code                : ComponentStatus/StdErr/succeeded
    Level               : Info
    DisplayStatus       : Provisioning succeeded
    Message             : The argument 'cseTest%20Scriptparam1.ps1' to the -File parameter does not exist. Provide the path to an existing '.ps1' file as an argument to the

-File parameter.
  Statuses[0]           :
    Code                : ProvisioningState/failed/-196608
    Level               : Error
    DisplayStatus       : Provisioning failed
    Message             : Finished executing command
```

Lo stato di esecuzione dell'estensione è disponibile anche nel portale di Azure. Per visualizzare lo stato di un'estensione, selezionare la macchina virtuale, scegliere **Estensioni** e selezionare l'estensione desiderata.

### <a name="rerun-vm-extensions"></a>Eseguire nuovamente le estensioni della macchina virtuale

In alcuni casi potrebbe essere necessario ripetere l'esecuzione della macchina virtuale. È possibile eseguire tale operazione rimuovendo l'estensione e quindi eseguendola di nuovo con il metodo scelto. Per rimuovere un'estensione, usare [Remove-AzVMExtension](https://docs.microsoft.com/powershell/module/az.compute/Remove-AzVMExtension) come indicato di seguito:

```powershell
Remove-AzVMExtension -ResourceGroupName "myResourceGroup" -VMName "myVM" -Name "myExtensionName"
```

È anche possibile rimuovere un'estensione nel portale di Azure come indicato di seguito:

1. Selezionare una macchina virtuale.
2. Scegliere **Estensioni**.
3. Selezionare l'estensione desiderata.
4. Scegliere **Disinstalla**.

## <a name="common-vm-extensions-reference"></a>Riferimento alle estensioni della macchina virtuale comuni
| Nome estensione | DESCRIZIONE | Altre informazioni |
| --- | --- | --- |
| Estensione Script personalizzato per Windows |Eseguire script su una macchina virtuale di Azure. |[Estensione script personalizzata per Windows](custom-script-windows.md) |
| Estensione DSC per Windows |Estensione PowerShell DSC (Desired State Configuration) |[Estensione DSC per Windows](dsc-overview.md) |
| Estensione di Diagnostica di Azure |Gestisce Diagnostica di Azure. |[Estensione di Diagnostica di Azure](https://azure.microsoft.com/blog/windows-azure-virtual-machine-monitoring-with-wad-extension/) |
| Estensione dell'accesso alla VM di Azure |Gestire gli utenti e le credenziali |[Estensione dell'accesso alle macchine virtuali per Linux](https://azure.microsoft.com/blog/using-vmaccess-extension-to-reset-login-credentials-for-linux-vm/) |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle estensioni macchina virtuale, vedere [Azure virtual machine extensions and features overview](overview.md) (Panoramica delle funzionalità e delle estensioni macchina virtuale di Azure).
