---
title: Panoramica dell'agente di macchine virtuali di Azure
description: Panoramica dell'agente di macchine virtuali di Azure
services: virtual-machines-windows
documentationcenter: virtual-machines
author: mimckitt
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
ms.openlocfilehash: 3d9c178201ab0c22ed4eab9cf65f7d48e59e1359
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78246114"
---
# <a name="azure-virtual-machine-agent-overview"></a>Panoramica dell'agente di macchine virtuali di Azure
L'agente di macchine virtuali di Microsoft Azure è un processo protetto e leggero che gestisce l'interazione delle macchine virtuali con il controller di infrastruttura di Azure. L'agente di macchine virtuali svolge un ruolo primario per l'abilitazione e l'esecuzione delle estensioni macchina virtuale di Azure. Le estensioni macchina virtuale rendono possibile la configurazione post-distribuzione della macchina virtuale, ad esempio l'installazione e la configurazione di software. Le estensioni macchina virtuale abilitano anche funzionalità di ripristino, ad esempio la reimpostazione della password amministrativa di una macchina virtuale. Senza l'agente di macchine virtuali di Azure, le estensioni macchina virtuale non possono essere eseguite.

Questo articolo descrive in dettaglio l'installazione e il rilevamento dell'agente di macchine virtuali di Azure.This article details installation and detection of the Azure Virtual Machine Agent.

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

> [!NOTE]
> È importante aggiornare l'opzione AllowExtensionOperations dopo aver installato manualmente VMAgent in una macchina virtuale distribuita dall'immagine senza l'abilitazione di ProvisionVMAgent.It is important to update the AllowExtensionOperations option after manually installing the VMAgent on a VM Agent that was deployed from image without ProvisionVMAgent enable.

```powershell
$vm.OSProfile.AllowExtensionOperations = $true
$vm | Update-AzVM
```

### <a name="prerequisites"></a>Prerequisiti
- L'agente di windows VM deve essere eseguito almeno Windows Server 2008 R2 (64 bit), con .Net Framework 4.0. Vedere [Supporto della versione minima per gli agenti di macchine virtuali in AzureSee Minimum version support for virtual machine agents in Azure](https://support.microsoft.com/en-us/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support)

- Verificare che la macchina virtuale abbia accesso all'indirizzo IP 168.63.129.16.Ensure your VM has access to IP address 168.63.129.16. Per ulteriori informazioni, vedere [Che cos'è l'indirizzo IP 168.63.129.16](https://docs.microsoft.com/azure/virtual-network/what-is-ip-address-168-63-129-16).

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

## <a name="windows-guest-agent-automatic-logs-collection"></a>Raccolta automaticamente registri dell'agente guest di Windows
Agente guest di Windows dispone di una funzionalità per raccogliere automaticamente alcuni registri. Questa funzionalità è controller dal processo CollectGuestLogs.exe. Esiste sia per PaaS Cloud Services che per Le macchine virtuali IaaS e il suo obiettivo è quello di & raccogliere rapidamente alcuni log di diagnostica da una macchina virtuale, in modo che possano essere usati per l'analisi offline. I log raccolti sono registri eventi, registri del sistema operativo, registri di Azure e alcune chiavi del Registro di sistema. Produce un file zip che viene trasferito all'host della macchina virtuale. Questo file zip può quindi essere esaminato dai team di progettazione e professionisti del supporto tecnico per analizzare i problemi su richiesta del cliente proprietario della macchina virtuale.

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sulle estensioni macchina virtuale, vedere [Azure virtual machine extensions and features overview](overview.md) (Panoramica delle funzionalità e delle estensioni macchina virtuale di Azure).
