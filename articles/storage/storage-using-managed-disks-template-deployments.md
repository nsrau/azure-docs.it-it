---
title: Uso dei dischi gestiti nei modelli di Azure Resource Manager | Microsoft Docs
description: Illustra come usare i dischi gestiti nei modelli di Azure Resource Manager
services: storage
documentationcenter: 
author: jboeshart
manager: 
ms.service: storage
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage
ms.date: 06/01/2017
ms.author: jaboes
ms.translationtype: Human Translation
ms.sourcegitcommit: 74f34bdbf5707510c682814716aa0b95c19a5503
ms.openlocfilehash: f2c0355068bc6dfd9a4e1aab52e4f4f9f23a9512
ms.contentlocale: it-it
ms.lasthandoff: 06/09/2017

---

# <a name="using-managed-disks-in-azure-resource-manager-templates"></a>Uso di Managed Disks nei modelli di Azure Resource Manager

Questo documento descrive le differenze tra dischi gestiti e non gestiti quando si usano i modelli di Azure Resource Manager per eseguire il provisioning di macchine virtuali. Le informazioni sono utili per aggiornare i modelli esistenti con dischi non gestiti per l'uso dei dischi gestiti. A titolo di riferimento, come guida viene usato il modello [101-vm-simple-windows](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows). Per un eventuale confronto diretto, è possibile visualizzare sia il modello per l'uso di [dischi gestiti](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows/azuredeploy.json), sia una versione precedente per [dischi non gestiti](https://github.com/Azure/azure-quickstart-templates/tree/93b5f72a9857ea9ea43e87d2373bf1b4f724c6aa/101-vm-simple-windows/azuredeploy.json).

## <a name="unmanaged-disks-template-formatting"></a>Formattazione del modello per dischi non gestiti

Per iniziare, viene esaminata la modalità di distribuzione dei dischi non gestiti. Quando si creano dischi non gestiti, è necessario un account di archiviazione in cui salvare i file VHD. È possibile creare un nuovo account di archiviazione o usarne uno già esistente. Questo articolo illustra come creare un nuovo account di archiviazione. A questo scopo, è necessario una risorsa account di archiviazione nel blocco delle risorse, come illustrato di seguito.

```
{
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageAccountName')]",
    "apiVersion": "2016-01-01",
    "location": "[resourceGroup().location]",
    "sku": {
        "name": "Standard_LRS"
    },
    "kind": "Storage",
    "properties": {}
}
```

All'interno dell'oggetto macchina virtuale è necessaria una dipendenza dall'account di archiviazione, per assicurarsi che venga creato prima della macchina virtuale. All'interno della sezione `storageProfile` viene quindi specificato l'URI completo del percorso del disco rigido virtuale, che fa riferimento all'account di archiviazione ed è necessario per il disco del sistema operativo e i dischi dati. 

```
{
    "apiVersion": "2015-06-15",
    "type": "Microsoft.Compute/virtualMachines",
    "name": "[variables('vmName')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
    "[resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]",
    "[resourceId('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
    ],
    "properties": {
        "hardwareProfile": {...},
        "osProfile": {...},
        "storageProfile": {
            "imageReference": {
                "publisher": "MicrosoftWindowsServer",
                "offer": "WindowsServer",
                "sku": "[parameters('windowsOSVersion')]",
                "version": "latest"
            },
            "osDisk": {
                "name": "osdisk",
                "vhd": {
                    "uri": "[concat(reference(resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))).primaryEndpoints.blob, 'vhds/osdisk.vhd')]"
                },
                "caching": "ReadWrite",
                "createOption": "FromImage"
            },
            "dataDisks": [
                {
                    "name": "datadisk1",
                    "diskSizeGB": "1023",
                    "lun": 0,
                    "vhd": {
                        "uri": "[concat(reference(resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))).primaryEndpoints.blob, 'vhds/datadisk1.vhd')]"
                    },
                    "createOption": "Empty"
                }
            ]
        },
        "networkProfile": {...},
        "diagnosticsProfile": {...}
    }
}
```

## <a name="managed-disks-template-formatting"></a>Formattazione del modello per dischi gestiti

Con Azure Managed Disks, il disco diventa una risorsa di primo livello e non richiede più la creazione di un account di archiviazione da parte dell'utente. I dischi gestiti sono esposti nella versione dell'API `2016-04-30-preview` e sono ora il tipo di disco predefinito. Le sezioni seguenti descrivono in modo dettagliato le impostazioni predefinite e illustrano come personalizzare ulteriormente i dischi.

### <a name="default-managed-disk-settings"></a>Impostazioni predefinite per i dischi gestiti

Per creare una macchina virtuale con dischi gestiti, non è più necessario creare la risorsa account di archiviazione ed è possibile aggiornare la risorsa macchina virtuale come indicato di seguito. Si noti in particolare che `apiVersion` corrisponde a `2016-04-30-preview` e che `osDisk` e `dataDisks` non fanno più riferimento a uno specifico URI per il disco rigido virtuale. Quando si distribuisce senza specificare proprietà aggiuntive, il disco userà l'[archiviazione con ridondanza locale Standard]((storage-redundancy.md). Se non si specifica un nome, assume il formato `<VMName>_OsDisk_1_<randomstring>` per il disco del sistema operativo e `<VMName>_disk<#>_<randomstring>` per ogni disco dati. Per impostazione predefinita, la crittografia dischi di Azure è disattivata; la memorizzazione nella cache è impostata su lettura/scrittura per il disco del sistema operativo e disattivata per i dischi dati. Nell'esempio seguente si può osservare che è ancora presente una dipendenza dall'account di archiviazione, ma è solo per l'archiviazione della diagnostica e non è necessaria per l'archiviazione su disco.

```
{
    "apiVersion": "2016-04-30-preview",
    "type": "Microsoft.Compute/virtualMachines",
    "name": "[variables('vmName')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]",
        "[resourceId('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
    ],
    "properties": {
        "hardwareProfile": {...},
        "osProfile": {...},
        "storageProfile": {
            "imageReference": {
                "publisher": "MicrosoftWindowsServer",
                "offer": "WindowsServer",
                "sku": "[parameters('windowsOSVersion')]",
                "version": "latest"
            },
            "osDisk": {
                "createOption": "FromImage"
            },
            "dataDisks": [
                {
                    "diskSizeGB": "1023",
                    "lun": 0,
                    "createOption": "Empty"
                }
            ]
        },
        "networkProfile": {...},
        "diagnosticsProfile": {...}
    }
}
```

### <a name="using-a-top-level-managed-disk-resource"></a>Uso di una risorsa disco gestito di primo livello

Come alternativa alla specifica della configurazione del disco nell'oggetto macchina virtuale, è possibile creare una risorsa disco di primo livello e collegarla nell'ambito della creazione della macchina virtuale. Ad esempio, è possibile creare una risorsa disco come segue per usarla come disco dati.

```
{
    "type": "Microsoft.Compute/disks",
    "name": "[concat(variables('vmName'),'-datadisk1')]",
    "apiVersion": "2016-04-30-preview",
    "location": "[resourceGroup().location]",
    "properties": {
        "creationData": {
            "createOption": "Empty"
        },
        "accountType": "Standard_LRS",
        "diskSizeGB": 1023
    }
}
```

All'interno dell'oggetto macchina virtuale, è quindi possibile fare riferimento all'oggetto disco da collegare. Specificare l'ID risorsa del disco gestito creato nella proprietà `managedDisk` consente di collegare il disco non appena viene creata la macchina virtuale. Per usare questa funzionalità, la proprietà `apiVersion` della risorsa macchina virtuale deve essere `2016-04-30-preview`. Si noti inoltre che è stata creata una dipendenza dalla risorsa disco per garantire che sia correttamente creata prima della creazione della macchina virtuale. 

```
{
    "apiVersion": "2016-04-30-preview",
    "type": "Microsoft.Compute/virtualMachines",
    "name": "[variables('vmName')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]",
        "[resourceId('Microsoft.Network/networkInterfaces/', variables('nicName'))]",
        "[resourceId('Microsoft.Compute/disks/', concat(variables('vmName'),'-datadisk1'))]"
    ],
    "properties": {
        "hardwareProfile": {...},
        "osProfile": {...},
        "storageProfile": {
            "imageReference": {
                "publisher": "MicrosoftWindowsServer",
                "offer": "WindowsServer",
                "sku": "[parameters('windowsOSVersion')]",
                "version": "latest"
            },
            "osDisk": {
                "createOption": "FromImage"
            },
            "dataDisks": [
                {
                    "lun": 0,
                    "name": "[concat(variables('vmName'),'-datadisk1')]",
                    "createOption": "attach",
                    "managedDisk": {
                        "id": "[resourceId('Microsoft.Compute/disks/', concat(variables('vmName'),'-datadisk1'))]"
                    }
                }
            ]
        },
        "networkProfile": {...},
        "diagnosticsProfile": {...}
    }
}
```

### <a name="create-managed-availability-sets-with-vms-using-managed-disks"></a>Creare set di disponibilità gestiti con macchine virtuali che usano dischi gestiti

Per creare set di disponibilità gestiti con macchine virtuali che usano dischi gestiti, aggiungere l'oggetto `sku` alla risorsa set di disponibilità e impostare la proprietà `name` su `Aligned`. In questo modo si garantisce che i dischi per ogni macchina virtuale siano sufficientemente isolati tra loro da evitare singoli punti di errore. Anche per usare questa funzionalità, la proprietà `apiVersion` della risorsa set di disponibilità deve essere `2016-04-30-preview`.

```
{
    "apiVersion": "2016-04-30-preview",
    "type": "Microsoft.Compute/availabilitySets",
    "location": "[resourceGroup().location]",
    "name": "[variables('avSetName')]",
    "properties": {
        "PlatformUpdateDomainCount": "3",
        "PlatformFaultDomainCount": "2"
    },
    "sku": {
        "name": "Aligned"
    }
}
```

### <a name="additional-scenarios-and-customizations"></a>Altri scenari e personalizzazioni

Per informazioni complete sulle specifiche dell'API REST, vedere la [documentazione dell'API REST per la creazione di un disco gestito](/rest/api/manageddisks/disks/disks-create-or-update). La documentazione contiene scenari aggiuntivi e informazioni sui valori predefiniti e accettabili che è possibile inviare all'API tramite distribuzioni di modelli. 

## <a name="next-steps"></a>Passaggi successivi

* Per modelli completi che usano dischi gestiti, visitare i collegamenti seguenti al repository di modelli di avvio rapido di Azure.
    * [Macchina virtuale Windows con disco gestito](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows)
    * [Macchina virtuale Linux con disco gestito](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-linux)
    * [Elenco completo dei modelli con disco gestito](https://github.com/Azure/azure-quickstart-templates/blob/master/managed-disk-support-list.md)
* Per altre informazioni sui dischi gestiti, vedere [Panoramica di Azure Managed Disks](storage-managed-disks-overview.md).
* La documentazione di riferimento del modello per le risorse macchina virtuale è disponibile alla pagina [Microsoft.Compute/virtualMachines template reference](/templates/microsoft.compute/virtualmachines).
* La documentazione di riferimento del modello per le risorse disco è disponibile alla pagina [Microsoft.Compute/disks template reference](/templates/microsoft.compute/disks).
 

