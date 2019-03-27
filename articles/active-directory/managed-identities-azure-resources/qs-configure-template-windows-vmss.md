---
title: Configurare identità gestite per le risorse di Azure in un set di scalabilità di macchine virtuali tramite un modello
description: Istruzioni dettagliate per la configurazione di identità gestite per le risorse di Azure in un set di scalabilità di macchine virtuali tramite un modello di Azure Resource Manager.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/20/2018
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 15b37c4845526227799173b09f468701954fc7b5
ms.sourcegitcommit: f0f21b9b6f2b820bd3736f4ec5c04b65bdbf4236
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/26/2019
ms.locfileid: "58449301"
---
# <a name="configure-managed-identities-for-azure-resources-on-an-azure-virtual-machine-scale-using-a-template"></a>Configurare identità gestita per le risorse di Azure in una scala di macchine virtuali di Azure usando un modello

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Le identità gestite per le risorse di Azure offrono ai servizi di Azure un'identità gestita automaticamente in Azure Active Directory. È possibile usare questa identità per l'autenticazione a qualsiasi servizio che supporti l'autenticazione di Azure AD senza dover inserire le credenziali nel codice. 

Questo articolo illustra come eseguire le seguenti operazioni di identità gestite per le risorse di Azure in un set di scalabilità di macchine virtuali di Azure mediante il modello di distribuzione Azure Resource Manager:
- Abilitare e disabilitare l'identità gestita assegnata dal sistema in un set di scalabilità di macchine virtuali di Azure
- Aggiungere e rimuovere un'identità gestita assegnata dall'utente in un set di scalabilità di macchine virtuali di Azure

## <a name="prerequisites"></a>Prerequisiti

- Se non si ha familiarità con le identità gestite per le risorse di Azure, vedere la [sezione sulla panoramica](overview.md). **Assicurarsi di conoscere la [differenza tra identità assegnata dal sistema e identità gestita assegnata dall'utente](overview.md#how-does-it-work)**.
- Se non si ha un account Azure, [registrarsi per ottenere un account gratuito](https://azure.microsoft.com/free/) prima di continuare.
- Per eseguire le operazioni di gestione illustrate in questo articolo, l'account deve avere le seguenti assegnazioni di controllo degli accessi in base al ruolo:

    > [!NOTE]
    > Non sono necessarie altre assegnazioni di ruoli della directory di Azure AD.

    - [Collaboratore macchina virtuale](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) per creare un set di scalabilità di macchine virtuali e abilitare e rimuovere da un set di scalabilità di macchine virtuali l'identità gestita assegnata dal sistema e/o dall'utente.
    - [Managed Identity Contributor (Collaboratore per identità gestita)](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) per creare un'identità gestita assegnata dall'utente.
    - [Operatore identità gestita](/azure/role-based-access-control/built-in-roles#managed-identity-operator) per assegnare e rimuovere un'identità gestita assegnata dall'utente da e verso un set di scalabilità di macchine virtuali.

## <a name="azure-resource-manager-templates"></a>Modelli di Gestione risorse di Azure

Analogamente al portale di Azure e all'esecuzione dello script, i modelli di gestione di [Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md) offrono la possibilità di distribuire risorse nuove o modificate definite da un gruppo di risorse di Azure. Diverse opzioni sono disponibili per la modifica e la distribuzione dei modelli, sia in locale che basati sul portale incluso quanto segue:

   - Usando un [modello personalizzato da Azure Marketplace](../../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template), che consente di creare un modello da zero o basarla su una comune esistente o [modello di avvio rapido](https://azure.microsoft.com/documentation/templates/).
   - Derivazione da un gruppo di risorse esistente, tramite l'esportazione di un modello da una [distribuzione originale](../../azure-resource-manager/manage-resource-groups-portal.md#export-resource-groups-to-templates) o dallo [stato attuale della distribuzione](../../azure-resource-manager/manage-resource-groups-portal.md#export-resource-groups-to-templates).
   - Usare un [editor JSON, ad esempio il codice di Visual Studio,](../../azure-resource-manager/resource-manager-create-first-template.md) locale e di caricarlo e distribuirlo tramite PowerShell o l'interfaccia della riga di comando.
   - Usare il [progetto del gruppo di risorse di Azure](../../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md) di Visual Studio per creare e distribuire un modello.  

Indipendentemente dall'opzione scelta, la sintassi dei modelli è la stessa durante la distribuzione iniziale e la ridistribuzione. L'abilitazione delle identità gestite per le risorse di Azure in una macchina virtuale nuova o esistente viene eseguita allo stesso modo. Per impostazione predefinita Azure Resource Manager esegue inoltre un [aggiornamento incrementale](../../azure-resource-manager/deployment-modes.md) per le distribuzioni.

## <a name="system-assigned-managed-identity"></a>Identità gestita assegnata dal sistema

In questa sezione si abiliterà e disabiliterà l'identità gestita assegnata dal sistema tramite un modello di Azure Resource Manager.

### <a name="enable-system-assigned-managed-identity-during-creation-the-creation-of-a-virtual-machines-scale-set-or-a-existing-virtual-machine-scale-set"></a>Abilitare l'identità gestita assegnata dal sistema durante la creazione di un set di scalabilità di macchine virtuali o per un set di scalabilità di macchine virtuali esistente

1. Se si accede ad Azure localmente o tramite il portale di Azure, usare un account che sia associato alla sottoscrizione di Azure che contiene il set di scalabilità di macchine virtuali.
2. Per abilitare l'identità gestita assegnata dal sistema, caricare il modello in un editor, individuare la risorsa `Microsoft.Compute/virtualMachinesScaleSets` interessata nella sezione risorse e aggiungere la proprietà `identity` allo stesso livello della proprietà `"type": "Microsoft.Compute/virtualMachinesScaleSets"`. Usare la sintassi seguente:

   ```JSON
   "identity": { 
       "type": "SystemAssigned"
   }
   ```

> [!NOTE]
> Si può facoltativamente il provisioning di identità gestite per estensione del set di scalabilità di macchine virtuali di risorse di Azure, specificando nel `extensionProfile` elemento del modello. Questo passaggio è facoltativo in quanto è possibile usare anche l'endpoint dell'identità del servizio metadati dell'istanza di Azure per recuperare i token.  Per altre informazioni, vedere [eseguire la migrazione dall'estensione della macchina virtuale al servizio metadati dell'istanza di Azure per l'autenticazione](howto-migrate-vm-extension.md).


4. Al termine, le sezioni seguenti dovrebbero essere aggiunte alla sezione risorse del modello e dovrebbero avere un aspetto simile a questo:

   ```json
    "resources": [
        {
            //other resource provider properties...
            "apiVersion": "2018-06-01",
            "type": "Microsoft.Compute/virtualMachineScaleSets",
            "name": "[variables('vmssName')]",
            "location": "[resourceGroup().location]",
            "identity": {
                "type": "SystemAssigned",
            },
           "properties": {
                //other resource provider properties...
                "virtualMachineProfile": {
                    //other virtual machine profile properties...
                    //The following appears only if you provisioned the optional virtual machine scale set extension (to be deprecated)
                    "extensionProfile": {
                        "extensions": [
                            {
                                "name": "ManagedIdentityWindowsExtension",
                                "properties": {
                                  "publisher": "Microsoft.ManagedIdentity",
                                  "type": "ManagedIdentityExtensionForWindows",
                                  "typeHandlerVersion": "1.0",
                                  "autoUpgradeMinorVersion": true,
                                  "settings": {
                                      "port": 50342
                                  }
                                }
                            } 
                        ]
                    }
                }
            }
        }
    ]
   ``` 

### <a name="disable-a-system-assigned-managed-identity-from-an-azure-virtual-machine-scale-set"></a>Disabilitare un'identità gestita assegnata dal sistema da un set di scalabilità di macchine virtuali di Azure

Se è disponibile un set di scalabilità di macchine virtuali per cui non è più necessaria un'identità gestita assegnata dal sistema:

1. Se si accede ad Azure localmente o tramite il portale di Azure, usare un account che sia associato alla sottoscrizione di Azure che contiene il set di scalabilità di macchine virtuali.

2. Caricare il modello in un [editor](#azure-resource-manager-templates) e individuare `Microsoft.Compute/virtualMachineScaleSets`la risorsa interessata`resources` all'interno della sezione. Se si dispone di una macchina virtuale con solo un'identità gestita assegnata dal sistema, è possibile disabilitarla modificando il tipo di identità e impostandolo su `None`.

   **Microsoft.Compute/virtualMachineScaleSets versione API 2018-06-01**

   Se la versione API è `2018-06-01` e la macchina virtuale ha identità gestite assegnate sia dal sistema sia dall'utente, rimuovere `SystemAssigned` dal tipo di identità e mantenere `UserAssigned` insieme ai valori di dizionario userAssignedIdentities.

   **Microsoft.Compute/virtualMachineScaleSets versione API 2018-06-01**

   Se la versione API è `2017-12-01` e il set di scalabilità di macchine virtuali ha identità gestite assegnate sia dal sistema sia dall'utente, rimuovere `SystemAssigned` dal tipo di identità e mantenere `UserAssigned` insieme alla matrice `identityIds` delle identità gestite assegnate dall'utente. 
   
    

   L'esempio seguente illustra come rimuovere un'identità gestita assegnata dal sistema da un set di scalabilità di macchine virtuali senza identità gestite assegnate dall'utente:
   
   ```json
   {
       "name": "[variables('vmssName')]",
       "apiVersion": "2018-06-01",
       "location": "[parameters(Location')]",
       "identity": {
           "type": "None"
        }

   }
   ```

## <a name="user-assigned-managed-identity"></a>Identità gestita assegnata dall'utente

In questa sezione verrà associata un'identità gestita assegnata dall'utente a un set di scalabilità di macchine virtuali mediante il modello di Azure Resource Manager.

> [!Note]
> Per creare un'identità gestita assegnata dall'utente usando un modello di Azure Resource Manager, vedere [Creare un'identità gestita assegnata dall'utente](how-to-manage-ua-identity-arm.md#create-a-user-assigned-managed-identity).

### <a name="assign-a-user-assigned-managed-identity-to-a-virtual-machine-scale-set"></a>Associare l'identità gestita assegnata dall'utente a un set di scalabilità di macchine virtuali

1. Nell'elemento `resources` aggiungere la voce seguente per associare un'identità gestita assegnata dall'utente al set di scalabilità di macchine virtuali.  Assicurarsi di sostituire `<USERASSIGNEDIDENTITY>` con il nome dell'identità gestita assegnata dall'utente che è stata creata.
   
   **Microsoft.Compute/virtualMachineScaleSets versione API 2018-06-01**

   Se la versione API è `2018-06-01`, le identità gestite assegnate dall'utente vengono archiviate nel formato dizionario `userAssignedIdentities` e il valore `<USERASSIGNEDIDENTITYNAME>` deve essere archiviato in una variabile definita nella sezione `variables` del modello.

   ```json
   {
       "name": "[variables('vmssName')]",
       "apiVersion": "2018-06-01",
       "location": "[parameters(Location')]",
       "identity": {
           "type": "userAssigned",
           "userAssignedIdentities": {
               "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',variables('<USERASSIGNEDIDENTITYNAME>'))]": {}
           }
       }
    
   }
   ```   

   **Microsoft.Compute/virtualMachineScaleSets versione API 2017-12-01**
    
   Se `apiVersion` è `2017-12-01` o una versione precedente, le identità gestite assegnate dall'utente vengono archiviate nella matrice `identityIds` e il valore `<USERASSIGNEDIDENTITYNAME>` deve essere archiviato in una variabile definita nella sezione variabili del modello.

   ```json
   {
       "name": "[variables('vmssName')]",
       "apiVersion": "2017-03-30",
       "location": "[parameters(Location')]",
       "identity": {
           "type": "userAssigned",
           "identityIds": [
               "[resourceID('Micrososft.ManagedIdentity/userAssignedIdentities/',variables('<USERASSIGNEDIDENTITY>'))]"
           ]
       }

   }
   ``` 
> [!NOTE]
> Si può facoltativamente il provisioning di identità gestite per estensione del set di scalabilità di macchine virtuali di risorse di Azure, specificando nel `extensionProfile` elemento del modello. Questo passaggio è facoltativo in quanto è possibile usare anche l'endpoint dell'identità del servizio metadati dell'istanza di Azure per recuperare i token.  Per altre informazioni, vedere [eseguire la migrazione dall'estensione della macchina virtuale al servizio metadati dell'istanza di Azure per l'autenticazione](howto-migrate-vm-extension.md).

3. Al termine il modello dovrebbe essere simile al seguente:
   
   **Microsoft.Compute/virtualMachineScaleSets versione API 2018-06-01**   

   ```json
   "resources": [
        {
            //other resource provider properties...
            "apiVersion": "2018-06-01",
            "type": "Microsoft.Compute/virtualMachineScaleSets",
            "name": "[variables('vmssName')]",
            "location": "[resourceGroup().location]",
            "identity": {
                "type": "UserAssigned",
                "userAssignedIdentities": {
                    "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',variables('<USERASSIGNEDIDENTITYNAME>'))]": {}
                }
            },
           "properties": {
                //other virtual machine properties...
                "virtualMachineProfile": {
                    //other virtual machine profile properties...
                    //The following appears only if you provisioned the optional virtual machine scale set extension (to be deprecated)
                    "extensionProfile": {
                        "extensions": [
                            {
                                "name": "ManagedIdentityWindowsExtension",
                                "properties": {
                                  "publisher": "Microsoft.ManagedIdentity",
                                  "type": "ManagedIdentityExtensionForWindows",
                                  "typeHandlerVersion": "1.0",
                                  "autoUpgradeMinorVersion": true,
                                  "settings": {
                                      "port": 50342
                                  }
                                }
                            } 
                        ]
                    }
                }
            }
        }
    ]
   ```

   **Microsoft.Compute/virtualMachines versione API 2017-12-01**

   ```json
   "resources": [
        {
            //other resource provider properties...
            "apiVersion": "2017-12-01",
            "type": "Microsoft.Compute/virtualMachineScaleSets",
            "name": "[variables('vmssName')]",
            "location": "[resourceGroup().location]",
            "identity": {
                "type": "UserAssigned",
                "identityIds": [
                    "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',variables('<USERASSIGNEDIDENTITYNAME>'))]"
                ]
            },
           "properties": {
                //other virtual machine properties...
                "virtualMachineProfile": {
                    //other virtual machine profile properties...
                    //The following appears only if you provisioned the optional virtual machine scale set extension (to be deprecated)    
                    "extensionProfile": {
                        "extensions": [
                            {
                                "name": "ManagedIdentityWindowsExtension",
                                "properties": {
                                  "publisher": "Microsoft.ManagedIdentity",
                                  "type": "ManagedIdentityExtensionForWindows",
                                  "typeHandlerVersion": "1.0",
                                  "autoUpgradeMinorVersion": true,
                                  "settings": {
                                      "port": 50342
                                  }
                                }
                            } 
                        ]
                    }
                }
            }
        }
    ]
   ```
   ### <a name="remove-user-assigned-managed-identity-from-an-azure-virtual-machine-scale-set"></a>Rimuovere un'identità gestita assegnata dall'utente da un set di scalabilità di macchine virtuali di Azure

Se è disponibile un set di scalabilità di macchine virtuali per cui non è più necessaria un'identità gestita assegnata dall'utente:

1. Se si accede ad Azure localmente o tramite il portale di Azure, usare un account che sia associato alla sottoscrizione di Azure che contiene il set di scalabilità di macchine virtuali.

2. Caricare il modello in un [editor](#azure-resource-manager-templates) e individuare `Microsoft.Compute/virtualMachineScaleSets`la risorsa interessata`resources` all'interno della sezione. Se si dispone di un set di scalabilità di macchine virtuali con solo un'identità gestita assegnata dall'utente, è possibile disabilitarla modificando il tipo di identità e impostandolo su `None`.

   L'esempio seguente illustra come rimuovere tutte le identità gestite assegnate dall'utente da una macchina virtuale senza identità gestite assegnate dal sistema:

   ```json
   {
       "name": "[variables('vmssName')]",
       "apiVersion": "2018-06-01",
       "location": "[parameters(Location')]",
       "identity": {
           "type": "None"
        }
   }
   ```
   
   **Microsoft.Compute/virtualMachineScaleSets versione API 2018-06-01**
    
   Per rimuovere una singola identità gestita assegnata dall'utente da un set di scalabilità di macchine virtuali, rimuoverla dal dizionario `userAssignedIdentities`.

   Se si dispone di un'identità assegnata dal sistema, mantenerla nel valore `type` del valore `identity`.

   **Microsoft.Compute/virtualMachineScaleSets versione API 2017-12-01**

   Per rimuovere una singola identità gestita assegnata dall'utente da un set di scalabilità di macchine virtuali, rimuoverla dalla matrice `identityIds`.

   Se si dispone di un'identità gestita assegnata dal sistema, mantenerla nel valore `type` del valore `identity`.
   
## <a name="next-steps"></a>Passaggi successivi

- [Panoramica delle identità gestite per le risorse di Azure](overview.md).

