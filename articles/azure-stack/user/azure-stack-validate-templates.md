---
title: Usare uno strumento di convalida del modello per cercare i modelli di Azure Stack | Microsoft Docs
description: Controllare modelli per la distribuzione in Azure Stack
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: d9e6aee1-4cba-4df5-b5a3-6f38da9627a3
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/27/2018
ms.author: sethm
ms.reviewer: unknown
ms.openlocfilehash: 4ec617fe49a3b55ba0608b77a8d1226fae025e6c
ms.sourcegitcommit: f4b78e2c9962d3139a910a4d222d02cda1474440
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/12/2019
ms.locfileid: "54246450"
---
# <a name="check-your-templates-for-azure-stack-with-the-template-validation-tool"></a>Controllare i modelli per Azure Stack con lo strumento di convalida modello

*Si applica a: Azure Stack Development Kit e i sistemi integrati di Azure Stack*

È possibile usare lo strumento di convalida del modello per controllare se il gestore di risorse di Azure [modelli](azure-stack-arm-templates.md) sono pronti per la distribuzione in Azure Stack. Lo strumento di convalida del modello è disponibile come parte degli strumenti di Azure Stack. Scaricare gli strumenti di Azure Stack usando i passaggi descritti nel [scaricare gli strumenti da GitHub](azure-stack-powershell-download.md) articolo.

## <a name="overview"></a>Panoramica

Per convalidare un modello, è necessario creare un cloud le funzionalità prima di file e quindi eseguire lo strumento di convalida. Si usano i moduli di PowerShell seguenti da strumenti di Azure Stack:

- Nel **CloudCapabilities** cartella:<br>         `AzureRM.CloudCapabilities.psm1` Crea un file JSON delle funzionalità cloud che rappresentano i servizi e le versioni in un cloud di Azure Stack.
- Nel **TemplateValidator** cartella:<br>
`AzureRM.TemplateValidator.psm1` Usa un file JSON delle funzionalità cloud per testare i modelli per la distribuzione in Azure Stack.

## <a name="build-the-cloud-capabilities-file"></a>Compilare il file di capacità cloud

Prima di utilizzare il validator del modello, eseguire la **AzureRM.CloudCapabilities** modulo di PowerShell per creare un file JSON.

>[!NOTE]
>Se si aggiorna il sistema integrato, o aggiungere eventuali nuovi servizi o le estensioni virtuale, è necessario eseguire nuovamente questo modulo.

1. Assicurarsi di avere la connettività ad Azure Stack. Questi passaggi possono essere eseguiti dall'host Azure Stack development kit oppure è possibile usare una [VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn) per la connessione dalla workstation.
2. Importa i **AzureRM.CloudCapabilities** modulo di PowerShell:

    ```PowerShell
    Import-Module .\CloudCapabilities\AzureRM.CloudCapabilities.psm1
    ```

3. Usare il `Get-CloudCapabilities` cmdlet per recuperare le versioni del servizio e creare un file JSON delle funzionalità cloud. Se non si specifica **- OutputPath**, il file AzureCloudCapabilities.Json viene creato nella directory corrente. Usare il percorso effettivo:

    ```PowerShell
    Get-AzureRMCloudCapability -Location <your location> -Verbose
    ```

## <a name="validate-templates"></a>Convalidare i modelli

Usare la procedura per convalidare i modelli usando la **AzureRM.TemplateValidator** modulo di PowerShell. È possibile usare i propri modelli o convalidare la [modelli di avvio rapido di Azure Stack](https://github.com/Azure/AzureStack-QuickStart-Templates).

1. Importa i **AzureRM.TemplateValidator.psm1** modulo di PowerShell:

    ```PowerShell
    cd "c:\AzureStack-Tools-master\TemplateValidator"
    Import-Module .\AzureRM.TemplateValidator.psm1
    ```

2. Eseguire il validator del modello:

    ```PowerShell
    Test-AzureRMTemplate -TemplatePath <path to template.json or template folder> `
    -CapabilitiesPath <path to cloudcapabilities.json> `
    -Verbose
    ```

Gli avvisi di convalida di modelli o gli errori vengono registrati nella console di PowerShell e un file HTML nella directory di origine. La schermata seguente mostra un esempio di un report di convalida:

![Report di convalida modello](./media/azure-stack-validate-templates/image1.png)

### <a name="parameters"></a>Parametri

Il validator del modello supporta i parametri seguenti.

| Parametro | DESCRIZIONE | Obbligatorio |
| ----- | -----| ----- |
| TemplatePath | Specifica il percorso in modo ricorsivo trova i modelli Azure Resource Manager | Sì | 
| TemplatePattern | Specifica il nome del file di modello in modo che corrispondano. | No  |
| CapabilitiesPath | Specifica il percorso di file JSON di capacità cloud | Sì | 
| IncludeComputeCapabilities | Include la valutazione delle risorse IaaS, ad esempio dimensioni delle macchine Virtuali ed estensioni di VM | No  |
| IncludeStorageCapabilities | Include la valutazione delle risorse di archiviazione, ad esempio tipi di SKU | No  |
| Report | Specifica nome del report HTML generato | No  |
| Dettagliato | Registra errori e avvisi nella console | No |

### <a name="examples"></a>Esempi

In questo esempio verifica tutte le [modelli di avvio rapido di Azure Stack](https://github.com/Azure/AzureStack-QuickStart-Templates) scaricato nell'archivio locale. L'esempio convalida anche dimensioni di macchine virtuali ed estensioni rispetto alle funzionalità di Azure Stack Development Kit:

```PowerShell
test-AzureRMTemplate -TemplatePath C:\AzureStack-Quickstart-Templates `
-CapabilitiesPath .\TemplateValidator\AzureStackCloudCapabilities_with_AddOns_20170627.json `
-TemplatePattern MyStandardTemplateName.json `
-IncludeComputeCapabilities `
-Report TemplateReport.html
```

## <a name="next-steps"></a>Passaggi successivi

- [Distribuire modelli di Azure Stack](azure-stack-arm-templates.md)
- [Sviluppo di modelli per Azure Stack](azure-stack-develop-templates.md)
