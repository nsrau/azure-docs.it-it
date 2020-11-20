---
title: Risoluzione degli errori delle estensioni VM Windows
description: Informazioni sulla risoluzione degli errori delle estensioni della macchina virtuale Windows di Azure
services: virtual-machines-windows
documentationcenter: ''
author: kundanap
manager: gwallace
editor: ''
tags: top-support-issue,azure-resource-manager
ms.assetid: 878ab9b6-c3e6-40be-82d4-d77fecd5030f
ms.service: virtual-machines-windows
ms.subservice: extensions
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/29/2016
ms.author: kundanap
ms.openlocfilehash: bca826cda8dfe47c341886faaf4a0d66f09d37d2
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2020
ms.locfileid: "94966344"
---
# <a name="troubleshooting-azure-windows-vm-extension-failures"></a>Risoluzione degli errori delle estensioni di macchina virtuale Windows di Azure
[!INCLUDE [virtual-machines-common-extensions-troubleshoot](../../../includes/virtual-machines-common-extensions-troubleshoot.md)]

## <a name="viewing-extension-status"></a>Visualizzazione dello stato dell'estensione
I modelli di Azure Resource Manager possono essere eseguiti da Azure PowerShell. Una volta che il modello viene eseguito, sarà possibile visualizzare lo stato dell'estensione da Azure Resource Explorer o dagli strumenti da riga di comando.

Esempio:

Azure PowerShell:

```azurepowershell
Get-AzVM -ResourceGroupName $RGName -Name $vmName -Status
```

Di seguito è riportato l'output di esempio:

```output
Extensions:  {
  "ExtensionType": "Microsoft.Compute.CustomScriptExtension",
  "Name": "myCustomScriptExtension",
  "SubStatuses": [
    {
      "Code": "ComponentStatus/StdOut/succeeded",
      "DisplayStatus": "Provisioning succeeded",
      "Level": "Info",
      "Message": "    Directory: C:\\temp\\n\\n\\nMode                LastWriteTime     Length Name
          \\n----                -------------     ------ ----                              \\n-a---          9/1/2015   2:03 AM         11
          test.txt                          \\n\\n",
                  "Time": null
      },
    {
      "Code": "ComponentStatus/StdErr/succeeded",
      "DisplayStatus": "Provisioning succeeded",
      "Level": "Info",
      "Message": "",
      "Time": null
    }
  ]
}
```

## <a name="troubleshooting-extension-failures"></a>Risoluzione degli errori delle estensioni

### <a name="rerun-the-extension-on-the-vm"></a>Eseguire nuovamente l'estensione nella macchina virtuale
Se si eseguono gli script nella macchina virtuale usando l'estensione script personalizzato, è possibile riscontrare in alcuni casi un errore in cui la creazione della macchina virtuale è riuscita, ma lo script ha avuto esito negativo. In queste condizioni, il metodo consigliato per risolvere il problema consiste nel rimuovere l'estensione ed eseguire nuovamente il modello.
Nota: In futuro, questa funzionalità potrebbe essere migliorata in modo da eliminare la necessità di disinstallazione dell'estensione.

#### <a name="remove-the-extension-from-azure-powershell"></a>Rimuovere l'estensione da Azure PowerShell
```azurepowershell
Remove-AzVMExtension -ResourceGroupName $RGName -VMName $vmName -Name "myCustomScriptExtension"
```

Una volta rimossa l'estensione, il modello può essere eseguito nuovamente per eseguire gli script nella macchina virtuale.

### <a name="trigger-a-new-goalstate-to-the-vm"></a>Attivare un nuovo GoalState alla macchina virtuale
È possibile notare che un'estensione non è stata eseguita o che non è in esecuzione a causa di un "generatore di certificati CRP di Windows Azure" mancante (il certificato viene usato per proteggere il trasporto delle impostazioni protette dell'estensione).
Il certificato verrà rigenerato automaticamente riavviando l'agente guest di Windows dall'interno della macchina virtuale:
- Aprire Gestione attività
- Vai alla scheda Dettagli
- Individuare il processo di WindowsAzureGuestAgent.exe
- Fare clic con il pulsante destro del mouse e scegliere "Termina attività". Il processo verrà riavviato automaticamente


È anche possibile attivare un nuovo GoalState per la VM eseguendo un "aggiornamento vuoto":

Azure PowerShell:

```azurepowershell
$vm = Get-AzureRMVM -ResourceGroupName <RGName> -Name <VMName>  
Update-AzureRmVM -ResourceGroupName <RGName> -VM $vm  
```

Interfaccia della riga di comando di Azure:

```azurecli
az vm update -g <rgname> -n <vmname>
```

Se un "aggiornamento vuoto" non funziona, è possibile aggiungere un nuovo disco dati vuoto alla macchina virtuale dalla portale di gestione di Azure e quindi rimuoverlo successivamente dopo che il certificato è stato aggiunto nuovamente.
