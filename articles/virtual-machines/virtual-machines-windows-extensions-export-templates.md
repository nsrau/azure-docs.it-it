---
title: Esportazione di gruppi di risorse di Azure contenenti estensioni macchina virtuale | Microsoft Docs
description: Esportare modelli di Resource Manager che includono estensioni macchina virtuale.
services: virtual-machines-windows
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 7f4e2ca6-f1c7-4f59-a2cc-8f63132de279
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 12/05/2016
ms.author: nepeters
translationtype: Human Translation
ms.sourcegitcommit: 015b6387491e0347b3a3a0ac87bb6b91b06387f0
ms.openlocfilehash: fb1cdfee5094198e3380e120f409175c89d23b6d

---

# <a name="exporting-resource-groups-that-contain-vm-extensions"></a>Esportazione di gruppi di risorse contenenti estensioni macchina virtuale

I gruppi di risorse di Azure possono essere esportati in un nuovo modello di Resource Manager ridistribuibile. Il processo di esportazione interpreta le risorse esistenti e crea un modello di Resource Manager che quando viene distribuito diventa un gruppo di risorse simile. Quando si utilizza l'opzione di esportazione del gruppo di risorse su un gruppo di risorse contenente le estensioni macchina virtuale, diversi elementi dovranno essere considerati come compatibilità dell'estensione e impostazioni protette.

Questo documento illustra il funzionamento del processo di esportazione del gruppo di risorse relativo alle estensioni macchina virtuale, inclusi un elenco di estensioni supportate e i dettagli sulla gestione di dati protetti.

## <a name="supported-virtual-machine-extensions"></a>Estensioni macchina virtuale supportate

Sono disponibili molte estensioni macchina virtuale. Non tutte le estensioni possono essere esportate in un modello di Resource Manager mediante la funzione "Script di automazione". Se un'estensione macchina virtuale non è supportata, deve essere inserita manualmente nel modello esportato.

Le estensioni seguenti possono essere esportate con la funzionalità di script di automazione.

| Estensione ||||
|---|---|---|---|
| Acronis Backup | Datadog Windows Agent | OS Patching For Linux | VM Snapshot Linux
| Acronis Backup Linux | Estensione Docker | Puppet Agent |
| BGInfo | DSC Extension | Site 24x7 Apm Insight |
| BMC CTM Agent Linux | Dynatrace Linux | Site 24x7 Linux Server |
| BMC CTM Agent Windows | Dynatrace Windows | Site 24x7 Windows Server |
| Chef Client | HPE Security Application Defender | Trend Micro DSA |
| Custom Script | IaaS Antimalware | Trend Micro DSA Linux |
| Estensione di script personalizzati | IaaS Diagnostics | VM Access For Linux |
| Custom Script for Linux | Linux Chef Client | VM Access For Linux |
| Datadog Linux Agent | Linux Diagnostic | VM Snapshot |

## <a name="export-the-resource-group"></a>Esportare il gruppo di risorse

Per esportare un gruppo di risorse in un modello riutilizzabile, completare i passaggi seguenti:

1. Accedere al portale di Azure
2. Scegliere Gruppi di risorse dal menu Hub
3. Selezionare il gruppo di risorse di destinazione dall'elenco
4. Nel pannello Gruppo di risorse fare clic su Script di automazione

![Esportazione del modello](./media/virtual-machines-windows-extensions-export-templates/template-export.png)

Lo script di automazione di Azure Resource Manager genera un modello di Resource Manager, un file di parametri e diversi esempi di script di distribuzione, ad esempio l'interfaccia della riga di comando di Azure e PowerShell. A questo punto, il modello esportato può essere scaricato utilizzando il pulsante di download, aggiunto come nuovo modello alla libreria di modelli o ridistribuito utilizzando il pulsante di distribuzione.

## <a name="configure-protected-settings"></a>Configurare le impostazioni protette

Molte estensioni macchina virtuale di Azure includono una configurazione di impostazioni protette, che crittografa i dati sensibili come credenziali e stringhe di configurazione. Le impostazioni protette non vengono esportate con lo script di automazione. Se necessario, le impostazioni protette devono essere reinserite nel modello esportato.

### <a name="step-1---remove-template-parameter"></a>Passaggio 1 - Rimuovere il parametro del modello

Quando viene esportato il gruppo di risorse, viene creato un singolo parametro del modello per fornire un valore alle impostazioni protette esportate. Questo parametro può essere rimosso. Per rimuovere il parametro, esaminare l'elenco di parametri ed eliminare il parametro che è simile a questo esempio JSON.

```json
"extensions_extensionname_protectedSettings": {
    "defaultValue": null,
    "type": "SecureObject"
}
```

### <a name="step-2---get-protected-settings-properties"></a>Passaggio 2 - Proteggere le proprietà delle impostazioni

Poiché ogni impostazione protetta contiene un set di proprietà obbligatorie, è necessario raccogliere in un elenco tali proprietà. Ogni parametro della configurazione delle impostazioni protette è disponibile nello [schema di Azure Resource Manager su GitHub](https://raw.githubusercontent.com/Azure/azure-resource-manager-schemas/master/schemas/2015-08-01/Microsoft.Compute.json). Tale schema include solo il set di parametri per le estensioni elencate nella sezione panoramica di questo documento. 

All'interno del repository dello schema, cercare l'estensione desiderata, per questo esempio `IaaSDiagnostics`. Dopo aver individuato le estensioni dell'oggetto `protectedSettings`, prendere nota di ogni parametro. Nell'esempio dell'estensione `IaasDiagnostic`, i parametri richiesti sono `storageAccountName`, `storageAccountKey` e `storageAccountEndPoint`.

```json
"protectedSettings": {
    "type": "object",
    "properties": {
        "storageAccountName": {
            "type": "string"
        },
        "storageAccountKey": {
            "type": "string"
        },
        "storageAccountEndPoint": {
            "type": "string"
        }
    },
    "required": [
        "storageAccountName",
        "storageAccountKey",
        "storageAccountEndPoint"
    ]
}
```

### <a name="step-3---re-create-the-protected-configuration"></a>Passaggio 3 - Ricreare la configurazione protetta

Nel modello esportato, cercare `protectedSettings` e sostituire l'oggetto delle impostazioni protette esportate con uno nuovo che includa i parametri dell'estensione necessari e un valore per ognuno di essi.

Nell'esempio dell'estensione `IaasDiagnostic`, la nuova configurazione delle impostazioni protette dovrebbe essere simile all'esempio seguente:

```json
"protectedSettings": {
    "storageAccountName": "[parameters('storageAccountName')]",
    "storageAccountKey": "[parameters('storageAccountKey')]",
    "storageAccountEndPoint": "https://core.windows.net"
}
```

La risorsa dell'estensione finale è simile all'esempio JSON seguente:

```json
{
    "name": "Microsoft.Insights.VMDiagnosticsSettings",
    "type": "extensions",
    "location": "[resourceGroup().location]",
    "apiVersion": "[variables('apiVersion')]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
    ],
    "tags": {
        "displayName": "AzureDiagnostics"
    },
    "properties": {
        "publisher": "Microsoft.Azure.Diagnostics",
        "type": "IaaSDiagnostics",
        "typeHandlerVersion": "1.5",
        "autoUpgradeMinorVersion": true,
        "settings": {
            "xmlCfg": "[base64(concat(variables('wadcfgxstart'), variables('wadmetricsresourceid'), variables('vmName'), variables('wadcfgxend')))]",
            "storageAccount": "[parameters('existingdiagnosticsStorageAccountName')]"
        },
        "protectedSettings": {
            "storageAccountName": "[parameters('storageAccountName')]",
            "storageAccountKey": "[parameters('storageAccountKey')]",
            "storageAccountEndPoint": "https://core.windows.net"
        }
    }
}
```

Se si utilizzano parametri del modello per fornire i valori delle proprietà, questi devono essere creati. Durante la creazione di parametri del modello per i valori delle impostazioni protette, assicurarsi di utilizzare il tipo di parametro `SecureObject`, in modo che i valori sensibili siano protetti. Per altre informazioni sull'utilizzo dei parametri, vedere [Creazione di modelli di Azure Resource Manager](../resource-group-authoring-templates.md).

Nell'esempio dell'estensione `IaasDiagnostic`, i parametri seguenti devono essere creati nella sezione dei parametri del modello di Resource Manager.

```json
"storageAccountName": {
    "defaultValue": null,
    "type": "SecureObject"
},
"storageAccountKey": {
    "defaultValue": null,
    "type": "SecureObject"
}
```

A questo punto, il modello può essere distribuito utilizzando qualsiasi metodo di distribuzione del modello.



<!--HONumber=Dec16_HO1-->


