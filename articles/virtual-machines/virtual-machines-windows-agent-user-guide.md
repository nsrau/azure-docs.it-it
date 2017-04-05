---
title: Panoramica dell&quot;agente di macchine virtuali di Azure | Documentazione Microsoft
description: Panoramica dell&quot;agente di macchine virtuali di Azure
services: virtual-machines-windows
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 0a1f212e-053e-4a39-9910-8d622959f594
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/28/2017
ms.author: nepeters
translationtype: Human Translation
ms.sourcegitcommit: b4802009a8512cb4dcb49602545c7a31969e0a25
ms.openlocfilehash: accfd5f0fec69175e584528ff9f6db66402cb89e
ms.lasthandoff: 03/29/2017


---
# <a name="azure-virtual-machine-agent-overview"></a>Panoramica dell'agente di macchine virtuali di Azure

L'agente di macchine virtuali di Microsoft Azure è un processo protetto e leggero che gestisce l'interazione delle macchine virtuali con il controller di infrastruttura di Azure. L'agente di macchine virtuali svolge un ruolo primario per l'abilitazione e l'esecuzione delle estensioni macchina virtuale di Azure. Le estensioni VM rendono possibile la configurazione post-distribuzione delle macchine virtuali, ad esempio l'installazione e configurazione di software. Le estensioni macchina virtuale abilitano anche funzionalità di ripristino, ad esempio la reimpostazione della password amministrativa di una macchina virtuale. Senza l'agente di macchine virtuali di Azure, le estensioni macchina virtuale non possono essere eseguite.

Questo documento descrive in dettaglio l'installazione, il rilevamento e la rimozione dell'agente di macchine virtuali di Azure.

## <a name="install-the-vm-agent"></a>Installare l'agente di macchine virtuali

### <a name="azure-gallery-image"></a>Immagine della raccolta di Azure

L'agente di macchine virtuali di Azure viene installato per impostazione predefinita in qualsiasi macchina virtuale di Windows distribuita da un'immagine della raccolta di Azure. Quando si distribuisce un'immagine della raccolta di Azure dal portale, da PowerShell, dall'interfaccia della riga di comando o con un modello di Azure Resource Manager, viene installato anche l'agente di macchine virtuali. 

### <a name="manual-installation"></a>Installazione manuale

L'agente di macchine virtuali di Windows può essere installato manualmente usando un pacchetto di Windows Installer. L'installazione manuale potrebbe essere necessaria quando si crea un'immagine di macchina virtuale personalizzata che verrà distribuita in Azure. Per installare manualmente l'agente di macchine virtuali di Windows, scaricare il programma di installazione dell'agente di macchine virtuali da questo percorso: [Download dell'agente di macchine virtuali di Windows Azure](http://go.microsoft.com/fwlink/?LinkID=394789). 

L'agente di macchine virtuali può essere installato facendo doppio clic sul file di Windows Installer. Per eseguire un'installazione automatica dell'agente di macchine virtuali, eseguire il comando seguente.

```cmd
msiexec.exe /i WindowsAzureVmAgent.2.7.1198.778.rd_art_stable.160617-1120.fre /quiet
```

## <a name="detect-the-vm-agent"></a>Rilevare l'agente di macchine virtuali

### <a name="powershell"></a>PowerShell

Il modulo PowerShell di Azure Resource Manager può essere usato per recuperare informazioni sulle macchine virtuali di Azure. Con l'esecuzione di `Get-AzureRmVM` vengono restituite varie informazioni, incluso lo stato di provisioning per l'agente di macchine virtuali di Azure.

```PowerShell
Get-AzureRmVM
```

Quello che segue è solo un subset dell'output di `Get-AzureRmVM`. Notare la proprietà `ProvisionVMAgent` annidata all'interno di `OSProfile`. Questa proprietà può essere usata per determinare se l'agente di macchine virtuali è stato distribuito nella macchina virtuale.

```PowerShell
OSProfile                  :
  ComputerName             : myVM
  AdminUsername            : muUserName
  WindowsConfiguration     :
    ProvisionVMAgent       : True
    EnableAutomaticUpdates : True
```

È possibile usare lo script seguente per restituire un breve elenco di nomi di macchine virtuali e lo stato dell'agente di macchine virtuali.

```PowerShell
$vms = Get-AzureRmVM

foreach ($vm in $vms) {
    $agent = $vm | Select -ExpandProperty OSProfile | Select -ExpandProperty Windowsconfiguration | Select ProvisionVMAgent
    Write-Host $vm.Name $agent.ProvisionVMAgent
}
```

### <a name="manual-detection"></a>Rilevamento manuale

Quando è connesso a una VM di Windows Azure, è possibile usare Gestione attività per esaminare i processi in esecuzione. Per controllare il processo dell'agente di macchine virtuali di Azure, aprire Gestione attività > fare clic sulla scheda Dettagli e cercare il nome di processo `WindowsAzureGuestAgent.exe`. La presenza di questo processo indica che l'agente di macchine virtuali è installato.

## <a name="upgrade-the-vm-agent"></a>Aggiornare l'agente di macchine virtuali

L'agente di macchine virtuali di Azure per Windows viene aggiornato automaticamente. Le nuove macchine virtuali distribuite in Azure ricevono la versione più recente dell'agente di macchine virtuali. Immagini di VM personalizzate devono essere aggiornate manualmente per includere il nuovo agente di macchine virtuali.
