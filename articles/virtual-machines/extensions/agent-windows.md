---
title: Panoramica dell'agente di macchine virtuali di Azure
description: Panoramica dell'agente di macchine virtuali di Azure
services: virtual-machines-windows
documentationcenter: virtual-machines
author: axayjo
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.assetid: 0a1f212e-053e-4a39-9910-8d622959f594
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 07/20/2019
ms.author: akjosh
ms.openlocfilehash: b1f627668c6bbd1d802eb3a4c11b6171e84887c8
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/14/2019
ms.locfileid: "74073251"
---
# <a name="azure-virtual-machine-agent-overview"></a>Panoramica dell'agente di macchine virtuali di Azure
L'agente di macchine virtuali di Microsoft Azure è un processo protetto e leggero che gestisce l'interazione delle macchine virtuali con il controller di infrastruttura di Azure. L'agente di macchine virtuali svolge un ruolo primario per l'abilitazione e l'esecuzione delle estensioni macchina virtuale di Azure. Le estensioni macchina virtuale rendono possibile la configurazione post-distribuzione della macchina virtuale, ad esempio l'installazione e la configurazione di software. Le estensioni macchina virtuale abilitano anche funzionalità di ripristino, ad esempio la reimpostazione della password amministrativa di una macchina virtuale. Senza l'agente di macchine virtuali di Azure, le estensioni macchina virtuale non possono essere eseguite.

Questo articolo descrive in dettaglio l'installazione, il rilevamento e la rimozione dell'agente di macchine virtuali di Azure.

## <a name="install-the-vm-agent"></a>Installare l'agente di macchine virtuali

### <a name="azure-marketplace-image"></a>Immagine di Azure Marketplace

L'agente di macchine virtuali di Azure viene installato per impostazione predefinita in qualsiasi macchina virtuale di Windows distribuita da un'immagine del Marketplace di Azure. Quando si distribuisce un'immagine del Marketplace di Azure dal portale, da PowerShell, dall'interfaccia della riga di comando o con un modello di Azure Resource Manager, viene installato anche l'agente di macchine virtuali.

Il pacchetto dell'agente Guest di Windows viene suddiviso in due parti:

- Provisioning dell'agente (PA)
- Agente Guest di Windows (WinGA)

Per avviare una macchina virtuale è necessario avere il PA installato nella macchina virtuale, tuttavia non è necessario installare il WinGA. In fase di distribuzione della macchina virtuale, è possibile scegliere di non installare il WinGA. Nell'esempio seguente viene illustrato come selezionare l'opzione *provisionVmAgent* con un modello di Azure Resource Manager:

```json
"resources": [{
"name": "[parameters('virtualMachineName')]",
"type": "Microsoft.Compute/virtualMachines",
"apiVersion": "2016-04-30-preview",
"location": "[parameters('location')]",
"dependsOn": ["[concat('Microsoft.Network/networkInterfaces/', parameters('networkInterfaceName'))]"],
"properties": {
    "osProfile": {
    "computerName": "[parameters('virtualMachineName')]",
    "adminUsername": "[parameters('adminUsername')]",
    "adminPassword": "[parameters('adminPassword')]",
    "windowsConfiguration": {
        "provisionVmAgent": "false"
}
```

Se non si dispone di agenti installati, è possibile usare alcuni servizi di Azure, come ad esempio Backup di Azure o Sicurezza di Azure. Per questi servizi è necessario installare un'estensione. Se è stata distribuita una macchina virtuale senza il WinGA, è possibile installare la versione più recente dell'agente in un secondo momento.

### <a name="manual-installation"></a>Installazione manuale
L'agente di macchine virtuali di Windows può essere installato manualmente con un pacchetto di Windows Installer. L'installazione manuale potrebbe essere necessaria quando si crea un'immagine della macchina virtuale personalizzata che viene distribuita in Azure. Per installare manualmente l'agente di macchine virtuali di Windows, [scaricare il programma di installazione dell'agente di macchine virtuali](https://go.microsoft.com/fwlink/?LinkID=394789). L'agente di macchine virtuali è supportato in Windows Server 2008 R2 e versioni successive.

L'agente di macchine virtuali può essere installato facendo doppio clic sul file di Windows Installer. Per eseguire un'installazione automatica dell'agente di macchine virtuali, eseguire il comando seguente:

```cmd
msiexec.exe /i WindowsAzureVmAgent.2.7.1198.778.rd_art_stable.160617-1120.fre /quiet
```

### <a name="prerequisites"></a>prerequisiti
Per eseguire l'agente di macchine virtuali Windows, è necessario almeno Windows Server 2008 R2 (64-bits) con .NET Framework 4,0. Vedere [supporto della versione minima per gli agenti di macchine virtuali in Azure](https://support.microsoft.com/en-us/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support)

## <a name="detect-the-vm-agent"></a>Rilevare l'agente di macchine virtuali

### <a name="powershell"></a>PowerShell

Il modulo PowerShell di Azure Resource Manager può essere usato per recuperare informazioni sulle macchine virtuali di Azure. Per avere informazioni su una macchina virtuale, come ad esempio lo stato del provisioning per l'agente di macchine virtuali di Azure, usare [Get-AzVM](https://docs.microsoft.com/powershell/module/az.compute/get-azvm):

```powershell
Get-AzVM
```

L'output condensato di esempio seguente mostra la proprietà *ProvisionVMAgent* annidata in *OSProfile*. Questa proprietà può essere usata per determinare se l'agente di macchine virtuali è stato distribuito nella macchina virtuale:

```powershell
OSProfile                  :
  ComputerName             : myVM
  AdminUsername            : myUserName
  WindowsConfiguration     :
    ProvisionVMAgent       : True
    EnableAutomaticUpdates : True
```

È possibile usare lo script seguente per restituire un breve elenco di nomi di macchine virtuali e lo stato dell'agente di macchine virtuali:

```powershell
$vms = Get-AzVM

foreach ($vm in $vms) {
    $agent = $vm | Select -ExpandProperty OSProfile | Select -ExpandProperty Windowsconfiguration | Select ProvisionVMAgent
    Write-Host $vm.Name $agent.ProvisionVMAgent
}
```

### <a name="manual-detection"></a>Rilevamento manuale

Quando è connesso a una macchina virtuale di Windows, è possibile usare Gestione attività per esaminare i processi in esecuzione. Per controllare il processo dell'agente di macchine virtuali di Azure, aprire Gestione attività, fare clic sulla scheda *Dettagli* e cercare il nome di processo **WindowsAzureGuestAgent.exe**. La presenza di questo processo indica che l'agente di macchine virtuali è installato.


## <a name="upgrade-the-vm-agent"></a>Aggiornare l'agente di macchine virtuali
L'agente di macchine virtuali di Azure per Windows viene aggiornato automaticamente. In quanto nuove macchine virtuali distribuite in Azure, ricevono la versione più recente dell'agente di macchine virtuali al momento del provisioning della macchina virtuale. Le immagini delle macchine virtuali personalizzate devono essere aggiornate manualmente per includere il nuovo agente di macchine virtuali al momento della creazione dell'immagine.

## <a name="windows-guest-agent-automatic-logs-collection"></a>Raccolta automatica dei log dell'agente guest di Windows
L'agente guest di Windows ha una funzionalità che consente di raccogliere automaticamente alcuni log. Questa funzionalità è controller dal processo CollectGuestLogs. exe. Esiste sia per i servizi cloud PaaS che per le macchine virtuali IaaS e il suo obiettivo è quello di & rapidamente raccogliere automaticamente alcuni log di diagnostica da una macchina virtuale, in modo che possano essere usati per l'analisi offline. I log raccolti sono i registri eventi, i log del sistema operativo, i log di Azure e alcune chiavi del registro di sistema. Produce un file ZIP che viene trasferito nell'host della macchina virtuale. Questo file ZIP può quindi essere esaminato dai team di progettazione e dai professionisti del supporto tecnico per esaminare i problemi relativi alla richiesta del cliente proprietario della macchina virtuale.

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sulle estensioni delle macchine virtuali, vedere [Panoramica sulle funzionalità ed estensioni macchine virtuali di Azure](overview.md).
