---
title: Utilizzare i Validator del modello per controllare i modelli per lo Stack di Azure | Documenti Microsoft
description: Modelli di controllo per la distribuzione in Azure Stack
services: azure-stack
documentationcenter: 
author: HeathL17
manager: byronr
editor: 
ms.assetid: d9e6aee1-4cba-4df5-b5a3-6f38da9627a3
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: helaw
ms.openlocfilehash: c99e5ebc2612e10f42bddbbd2f1c17d7404305d3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="check-your-templates-for-azure-stack-with-template-validator"></a>Controllare i modelli per lo Stack di Azure con Validator del modello

*Si applica a: Azure Stack integrate di sistemi Azure Stack Development Kit*

È possibile utilizzare lo strumento di convalida del modello per controllare se il gestore delle risorse Azure [modelli](azure-stack-arm-templates.md) pronti per lo Stack di Azure. Lo strumento di convalida del modello è disponibile come parte degli strumenti di Azure Stack. Scaricare gli strumenti di Azure Stack utilizzando la procedura descritta nel [scaricare gli strumenti da GitHub](azure-stack-powershell-download.md) articolo. 

Per convalidare i modelli, utilizzare i seguenti moduli di PowerShell e il file JSON nella **TemplateValidator** e **CloudCapabilities** cartelle: 

 - AzureRM.CloudCapabilities.psm1 crea un file JSON delle funzionalità cloud che rappresenta i servizi e le versioni in un cloud come Azure Stack.
 - AzureRM.TemplateValidator.psm1 utilizza un file JSON delle funzionalità cloud per testare i modelli per la distribuzione nello Stack di Azure.
 - AzureStackCloudCapabilities_with_AddOns_20170627.json è un file di funzionalità cloud predefinito.  È possibile crearne di proprie o per un'introduzione, utilizzare questo file. 

In questo argomento, eseguire una convalida dei modelli e, facoltativamente, compilare un file di funzionalità del cloud.

## <a name="validate-templates"></a>Convalidare modelli
In questa procedura, convalidare i modelli mediante il modulo AzureRM.TemplateValidator PowerShell. È possibile utilizzare i modelli personalizzati o convalidare la [modelli di avvio rapido di Azure Stack](https://github.com/Azure/AzureStack-QuickStart-Templates).

1.  Importare il modulo PowerShell AzureRM.TemplateValidator.psm1:
    
    ```PowerShell
    cd "c:\AzureStack-Tools-master\TemplateValidator"
    Import-Module .\AzureRM.TemplateValidator.psm1
    ```

2.  Eseguire il validator del modello:

    ```PowerShell
    Test-AzureRMTemplate -TemplatePath <path to template.json or template folder> `
    -CapabilitiesPath <path to cloudcapabilities.json> `
    -Verbose
    ```

Eventuali errori o avvisi di convalida modello vengono registrati nella console di PowerShell e vengono inoltre registrati in un file HTML nella directory di origine. Un esempio di output del report di convalida è simile al seguente:

![report di convalida di esempio](./media/azure-stack-validate-templates/image1.png)

### <a name="parameters"></a>Parametri

| Parametro | Descrizione | Obbligatorio |
| ----- | -----| ----- |
| TemplatePath | Specifica il percorso in modo ricorsivo trovare modelli di gestione risorse | Sì | 
| TemplatePattern | Specifica il nome di file di modello per la corrispondenza. | No |
| CapabilitiesPath | Specifica il percorso di file JSON di capacità cloud | Sì | 
| IncludeComputeCapabilities | Include una valutazione delle risorse IaaS come dimensioni delle macchine Virtuali e le estensioni VM | No |
| IncludeStorageCapabilities | Include la valutazione delle risorse di archiviazione come tipi SKU | No |
| Report | Specifica nome del report HTML generato | No |
| Dettagliato | Registra errori e avvisi nella console | No|


### <a name="examples"></a>esempi
In questo esempio convalida tutte le [modelli di avvio rapido di Azure Stack](https://github.com/Azure/AzureStack-QuickStart-Templates) scaricato localmente e convalida le estensioni rispetto alle funzionalità di Azure Stack Development Kit e dimensioni delle macchine Virtuali.

```PowerShell
test-AzureRMTemplate -TemplatePath C:\AzureStack-Quickstart-Templates `
-CapabilitiesPath .\TemplateValidator\AzureStackCloudCapabilities_with_AddOns_20170627.json.json `
-TemplatePattern MyStandardTemplateName.json`
-IncludeComputeCapabilities`
-Report TemplateReport.html
```

## <a name="build-cloud-capabilities-file"></a>Compilare il file di funzionalità cloud
Valore predefinito di includere i file scaricati *AzureStackCloudCapabilities_with_AddOns_20170627.json* file che descrive le versioni di servizio disponibili nel Kit di sviluppo dello Stack di Azure con i servizi PaaS installati.  Se si installa il provider di risorse aggiuntive, è possibile utilizzare il modulo AzureRM.CloudCapabilities PowerShell per compilare un file JSON, inclusi i nuovi servizi.  

1.  Assicurarsi di disporre della connettività allo Stack di Azure.  Questi passaggi possono essere eseguiti dall'host del kit di sviluppo dello Stack di Azure, oppure è possibile utilizzare [VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn) per la connessione dalla propria workstation. 
2.  Importare il modulo AzureRM.CloudCapabilities PowerShell:

    ```PowerShell
    Import-Module .\CloudCapabilities\AzureRM.CloudCapabilities.psm1
    ``` 

3.  Usare il cmdlet Get-CloudCapabilities per recuperare le versioni di servizio e creare un file JSON delle funzionalità cloud:

    ```PowerShell
    Get-AzureRMCloudCapabilities -Location 'local' -Verbose
    ```             


## <a name="next-steps"></a>Passaggi successivi
 - [Distribuire modelli allo Stack di Azure](azure-stack-arm-templates.md)
 - [Sviluppo di modelli per Azure Stack](azure-stack-develop-templates.md)

