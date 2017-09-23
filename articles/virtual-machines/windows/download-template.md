---
title: Scaricare il modello per una macchina virtuale di Azure | Microsoft Docs
description: Scaricare il modello per una macchina virtuale per facilitare l'automazione delle distribuzioni nel modello di distribuzione di Resource Manager
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 51ef4f51-0942-4249-afea-4a3f87ce1ff8
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 03/22/2017
ms.author: cynthn
ms.translationtype: Human Translation
ms.sourcegitcommit: 197ebd6e37066cb4463d540284ec3f3b074d95e1
ms.openlocfilehash: 9e4c0c3cf0e233447369a24b1d5fe27495abd1cf
ms.contentlocale: it-it
ms.lasthandoff: 03/31/2017

---
# <a name="download-the-template-for-a-vm"></a>Scaricare il modello per una VM
Quando si crea una macchina virtuale in Azure con il portale o con PowerShell, viene creato automaticamente un modello di Resource Manager. È possibile usare questo modello per duplicare rapidamente una distribuzione. Il modello contiene informazioni su tutte le risorse in un gruppo di risorse. Per una macchina virtuale, questo significa che il modello contiene tutto ciò che viene creato in supporto della macchina virtuale all'interno del gruppo di risorse, comprese le risorse di rete.

## <a name="download-the-template-using-the-portal"></a>Scaricare il modello usando il portale
1. Accedere al [Portale di Azure](https://portal.azure.com/).
2. Selezionare **Macchine virtuali** nel menu dell'hub.
3. Selezionare la macchina virtuale dall'elenco.
4. Selezionare **Script di automazione**.
5. Selezionare **Scarica** e salvare il file .zip nel computer locale.
6. Aprire il file .zip ed estrarne i file in una cartella. Il file .zip contiene:
   
   * deploy.ps1
   * deploy.sh 
   * deployer.rb
   * DeploymentHelper.cs
   * parameters.json
   * template.json

Il file template.json è il modello.

## <a name="download-the-template-using-powershell"></a>Scaricare il modello con PowerShell
È anche possibile scaricare il file di modello .json usando il cmdlet [Export-AzureRMResourceGroup](https://msdn.microsoft.com/library/mt715427.aspx). È possibile usare il parametro `-path` per fornire il nome file e il percorso per il file .json. In questo esempio viene illustrato come scaricare il modello per il gruppo di risorse denominato **myResourceGroup** nella cartella **C:\users\public\downloads** del computer locale.

```powershell
    Export-AzureRmResourceGroup -ResourceGroupName "myResourceGroup" -Path "C:\users\public\downloads"
```

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sulla distribuzione di risorse usando i modelli, vedere [Procedura dettagliata del modello di Resource Manager](../../azure-resource-manager/resource-manager-template-walkthrough.md).


