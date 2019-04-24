---
title: Estensione Diagnostica di Azure per Windows | Microsoft Docs
description: Monitorare le macchine virtuali Windows di Azure usando l'estensione Diagnostica di Azure
services: virtual-machines-windows
documentationcenter: ''
author: johnkemnetz
manager: ashwink
editor: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 04/06/2018
ms.author: johnkem
ms.openlocfilehash: 58c520ecbaf764140748167e458c301ab56de375
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60232875"
---
# <a name="azure-diagnostics-extension-for-windows-vms"></a>Estensione Diagnostica di Azure per macchine virtuali Windows

## <a name="overview"></a>Panoramica

L'estensione Diagnostica di Azure per le macchine virtuali consente di raccogliere i dati di monitoraggio, ad esempio i contatori delle prestazioni e i log eventi, dalle macchine virtuali Windows. È possibile specificare in modo granulare i dati da raccogliere e dove inviare i dati, ad esempio in un account di Archiviazione di Azure o un hub eventi di Azure. È anche possibile usare questi dati per creare grafici nel portale di Azure oppure per creare avvisi sulle metriche.

## <a name="prerequisites"></a>Prerequisiti

### <a name="operating-system"></a>Sistema operativo

L'estensione Diagnostica di Azure può essere eseguita in Windows 10 Client, Windows Server 2008 R2, 2012, 2012 R2 e 2016.

### <a name="internet-connectivity"></a>Connettività Internet

L'estensione Diagnostica di Azure richiede che la macchina virtuale di destinazione sia connessa a Internet. 

## <a name="extension-schema"></a>Schema dell'estensione

[Lo schema e i valori delle proprietà dell'estensione Diagnostica di Azure sono illustrati in questo documento.](../../azure-monitor/platform/diagnostics-extension-schema-1dot3.md)

## <a name="template-deployment"></a>Distribuzione del modello

Le estensioni macchina virtuale di Azure possono essere distribuite con i modelli di Azure Resource Manager. Lo schema JSON illustrato nella sezione precedente può essere usato in un modello di Azure Resource Manager per eseguire l'estensione Diagnostica di Azure durante la distribuzione di un modello di Azure Resource Manager. Vedere [Usare monitoraggio e diagnostica con una macchina virtuale Windows e modelli di Azure Resource Manager](extensions-diagnostics-template.md).

## <a name="azure-cli-deployment"></a>Distribuzione dell'interfaccia della riga di comando di Azure

L'interfaccia della riga di comando di Azure può essere usata per distribuire l'estensione Diagnostica di Azure in una macchina virtuale esistente. Sostituire le impostazioni protette e le proprietà delle impostazioni con un oggetto JSON valido dallo schema dell'estensione precedente. 

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name IaaSDiagnostics \
  --publisher Microsoft.Azure.Diagnostics \
  --version 1.9.0.0 --protected-settings protected-settings.json \
  --settings public-settings.json 
```

## <a name="powershell-deployment"></a>Distribuzione PowerShell

Il comando `Set-AzVMDiagnosticsExtension` può essere usato per aggiungere l'estensione Diagnostica di Azure a una macchina virtuale esistente. Vedere anche [Usare PowerShell per abilitare la Diagnostica di Azure in una macchina virtuale che esegue Windows](ps-extensions-diagnostics.md).

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]


```powershell
$vm_resourcegroup = "myvmresourcegroup"
$vm_name = "myvm"
$diagnosticsconfig_path = "DiagnosticsPubConfig.xml"

Set-AzVMDiagnosticsExtension -ResourceGroupName $vm_resourcegroup `
  -VMName $vm_name `
  -DiagnosticsConfigurationPath $diagnosticsconfig_path
```

## <a name="troubleshoot-and-support"></a>Risoluzione dei problemi e supporto

### <a name="troubleshoot"></a>Risolvere problemi

I dati sullo stato delle distribuzioni dell'estensione possono essere recuperati nel portale di Azure e tramite l'interfaccia della riga di comando di Azure. Per visualizzare lo stato di distribuzione delle estensioni per una determinata VM, eseguire il comando seguente nell'interfaccia della riga di comando di Azure.

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

[Vedere questo articolo](../../azure-monitor/platform/diagnostics-extension-troubleshooting.md) per una guida più completa alla risoluzione dei problemi per l'estensione Diagnostica di Azure.

### <a name="support"></a>Supporto

Per ricevere assistenza in relazione a qualsiasi punto di questo articolo, contattare gli esperti di Azure nei [forum MSDN e Stack Overflow relativi ad Azure](https://azure.microsoft.com/support/forums/). In alternativa, è possibile archiviare un evento imprevisto di supporto tecnico di Azure. Accedere al [sito del supporto di Azure](https://azure.microsoft.com/support/options/) e selezionare l'opzione desiderata per ottenere supporto. Per informazioni sull'uso del supporto di Azure, leggere le [Domande frequenti sul supporto di Azure](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>Fasi successive
* [Altre informazioni sull'estensione Diagnostica di Azure](../../azure-monitor/platform/diagnostics-extension-overview.md)
* [Schema e versioni dell'estensione](../../azure-monitor/platform/diagnostics-extension-schema.md)
