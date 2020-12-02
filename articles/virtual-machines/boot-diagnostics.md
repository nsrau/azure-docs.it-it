---
title: Diagnostica di avvio di Azure
description: Panoramica della diagnostica di avvio di Azure e della diagnostica di avvio gestito
services: virtual-machines
ms.service: virtual-machines
author: mimckitt
ms.author: mimckitt
ms.topic: conceptual
ms.date: 11/06/2020
ms.openlocfilehash: 408ba76c44d1161a4b91ccc037721796c7b94661
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96500751"
---
# <a name="azure-boot-diagnostics"></a>Diagnostica di avvio di Azure

La diagnostica di avvio è una funzionalità di debug per macchine virtuali di Azure (VM) che consente la diagnosi degli errori di avvio della VM. La diagnostica di avvio consente a un utente di osservare lo stato della macchina virtuale durante l'avvio raccogliendo informazioni sul log seriale e schermate.

## <a name="boot-diagnostics-storage-account"></a>Account di archiviazione di diagnostica di avvio
Quando si crea una macchina virtuale in portale di Azure, la diagnostica di avvio è abilitata per impostazione predefinita. L'esperienza di diagnostica di avvio consigliata prevede l'uso di un account di archiviazione gestito, poiché produce miglioramenti significativi delle prestazioni nel tempo per la creazione di una macchina virtuale di Azure. Questo perché verrà usato un account di archiviazione gestito di Azure, rimuovendo il tempo necessario per creare un nuovo account di archiviazione utente per archiviare i dati di diagnostica di avvio.

Un'esperienza di diagnostica di avvio alternativa consiste nell'usare un account di archiviazione gestito dall'utente. Un utente può creare un nuovo account di archiviazione o utilizzarne uno esistente. 

> [!IMPORTANT]
> I BLOB dei dati di diagnostica di avvio (che includono i log e le immagini di snapshot) vengono archiviati in un account di archiviazione gestito. Ai clienti verranno addebitati solo i GiBs usati dai BLOB, non sulle dimensioni del disco di cui è stato effettuato il provisioning. I contatori degli snapshot verranno usati per la fatturazione dell'account di archiviazione gestito. Poiché gli account gestiti vengono creati in con ridondanza locale standard o ZRS standard, ai clienti verranno addebitati i costi di $0,05/GB al mese per la dimensione dei soli BLOB di dati di diagnostica. Per altre informazioni su questo piano tariffario, vedere [prezzi di Managed disks](https://azure.microsoft.com/pricing/details/managed-disks/). I clienti vedranno questo costo associato all'URI della risorsa VM. 

## <a name="boot-diagnostics-view"></a>Visualizzazione diagnostica di avvio
Nel pannello della macchina virtuale l'opzione diagnostica di avvio si trova nella sezione *supporto e risoluzione dei problemi* del portale di Azure. Selezionando diagnostica di avvio vengono visualizzate una schermata e informazioni sul log seriale. Il log seriale contiene la messaggistica del kernel e lo screenshot è uno snapshot dello stato corrente delle macchine virtuali. A seconda che la macchina virtuale esegua Windows o Linux, determina come dovrebbe apparire lo screenshot previsto. Per Windows, gli utenti visualizzeranno uno sfondo del desktop e per Linux, gli utenti visualizzeranno una richiesta di accesso.

:::image type="content" source="./media/boot-diagnostics/boot-diagnostics-linux.png" alt-text="Screenshot della diagnostica di avvio Linux":::
:::image type="content" source="./media/boot-diagnostics/boot-diagnostics-windows.png" alt-text="Screenshot della diagnostica di avvio di Windows":::

## <a name="enable-managed-boot-diagnostics"></a>Abilitare la diagnostica di avvio gestito 
La diagnostica di avvio gestito può essere abilitata tramite i modelli portale di Azure, CLI e ARM. L'abilitazione tramite PowerShell non è ancora supportata. 

### <a name="enable-managed-boot-diagnostics-using-the-azure-portal"></a>Abilitare la diagnostica di avvio gestito usando il portale di Azure
Quando si crea una macchina virtuale nella portale di Azure, l'impostazione predefinita prevede che la diagnostica di avvio sia abilitata usando un account di archiviazione gestito. Per visualizzarlo, passare alla scheda *gestione* durante la creazione della macchina virtuale. 

:::image type="content" source="./media/boot-diagnostics/boot-diagnostics-enable-portal.png" alt-text="Screenshot che Abilita la diagnostica di avvio gestito durante la creazione della macchina virtuale.":::

### <a name="enable-managed-boot-diagnostics-using-cli"></a>Abilitare la diagnostica di avvio gestito tramite CLI
Diagnostica di avvio con un account di archiviazione gestito è supportato nell'interfaccia della riga di comando di Azure 2.12.0 e versioni successive. Se non si desidera immettere un nome o un URI per un account di archiviazione, verrà utilizzato un account gestito. Per ulteriori informazioni ed esempi di codice, vedere la documentazione dell'interfaccia della riga [di comando per diagnostica di avvio](/cli/azure/vm/boot-diagnostics?preserve-view=true&view=azure-cli-latest).

### <a name="enable-managed-boot-diagnostics-using-azure-resource-manager-arm-templates"></a>Abilitare la diagnostica di avvio gestito usando modelli di Azure Resource Manager (ARM)
Tutti gli elementi dopo la versione API 2020-06-01 supportano la diagnostica di avvio gestito. Per altre informazioni, vedere [visualizzazione dell'istanza di diagnostica di avvio](/rest/api/compute/virtualmachines/createorupdate#bootdiagnostics).

```ARM Template
            "name": "[parameters('virtualMachineName')]",
            "type": "Microsoft.Compute/virtualMachines",
            "apiVersion": "2020-06-01",
            "location": "[parameters('location')]",
            "dependsOn": [
                "[concat('Microsoft.Network/networkInterfaces/', parameters('networkInterfaceName'))]"
            ],
            "properties": {
                "hardwareProfile": {
                    "vmSize": "[parameters('virtualMachineSize')]"
                },
                "storageProfile": {
                    "osDisk": {
                        "createOption": "fromImage",
                        "managedDisk": {
                            "storageAccountType": "[parameters('osDiskType')]"
                        }
                    },
                    "imageReference": {
                        "publisher": "Canonical",
                        "offer": "UbuntuServer",
                        "sku": "18.04-LTS",
                        "version": "latest"
                    }
                },
                "networkProfile": {
                    "networkInterfaces": [
                        {
                            "id": "[resourceId('Microsoft.Network/networkInterfaces', parameters('networkInterfaceName'))]"
                        }
                    ]
                },
                "osProfile": {
                    "computerName": "[parameters('virtualMachineComputerName')]",
                    "adminUsername": "[parameters('adminUsername')]",
                    "linuxConfiguration": {
                        "disablePasswordAuthentication": true
                    }
                },
                "diagnosticsProfile": {
                    "bootDiagnostics": {
                        "enabled": true
                    }
                }
            }
        }
    ],

```

## <a name="limitations"></a>Limitazioni
- La diagnostica di avvio è disponibile solo per le macchine virtuali Azure Resource Manager.
- La diagnostica di avvio gestito non supporta le macchine virtuali che usano dischi del sistema operativo non gestiti.
- La diagnostica di avvio non supporta gli account di archiviazione Premium, se viene usato un account di archiviazione Premium per la diagnostica di avvio, gli utenti riceveranno un `StorageAccountTypeNotSupported` errore all'avvio della macchina virtuale. 
- Gli account di archiviazione gestiti sono supportati nella versione API Gestione risorse "2020-06-01" e versioni successive.
- La console seriale di Azure non è attualmente compatibile con un account di archiviazione gestito per la diagnostica di avvio. Scopri di più sulla [console seriale di Azure](./troubleshooting/serial-console-overview.md).
- Il portale supporta solo l'uso della diagnostica di avvio con un account di archiviazione gestito per le macchine virtuali a istanza singola.

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sulla [console seriale di Azure](./troubleshooting/serial-console-overview.md) e su come usare la diagnostica di avvio per [risolvere i problemi relativi alle macchine virtuali in Azure](./troubleshooting/boot-diagnostics.md).