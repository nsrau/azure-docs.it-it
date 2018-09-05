---
title: Configurare un'identità del servizio gestita in un set di scalabilità di macchine virtuali di Azure tramite un modello
description: Istruzioni dettagliate per la configurazione di un'identità del servizio gestita in un set di scalabilità di macchine virtuali di Azure tramite un modello di Azure Resource Manager.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/20/2018
ms.author: daveba
ms.openlocfilehash: 69b88b5aabbadadea3b04f96d9a21f266c85a52f
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/28/2018
ms.locfileid: "43125385"
---
# <a name="configure-managed-service-identity-on-virtual-machine-scale-using-a-template"></a>Configurare un'identità del servizio gestita in un set di scalabilità di macchine virtuali tramite un modello

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Identità del servizio gestito offre servizi di Azure con un'identità gestita automaticamente in Azure Active Directory. È possibile usare questa identità per l'autenticazione a qualsiasi servizio che supporti l'autenticazione di Azure AD senza dover inserire le credenziali nel codice. 

Questo articolo illustra come eseguire le seguenti operazioni dell'identità del servizio gestita in un set di scalabilità di macchine virtuali di Azure usando il modello di distribuzione Azure Resource Manager:
- Abilitare e disattivare l'identità assegnata dal sistema in un set di scalabilità di macchine virtuali di Azure
- Aggiungere e rimuovere un'identità assegnata dall'utente in un set di scalabilità di macchine virtuali di Azure

## <a name="prerequisites"></a>Prerequisiti

- Se non si ha familiarità con l'identità del servizio gestita, vedere la [sezione sulla panoramica](overview.md). **Assicurarsi di conoscere la [differenza tra identità assegnata dal sistema e identità assegnata dall'utente](overview.md#how-does-it-work)**.
- Se non si ha un account Azure, [registrarsi per ottenere un account gratuito](https://azure.microsoft.com/free/) prima di continuare.
- Per eseguire le operazioni di gestione illustrate in questo articolo, l'account deve avere le assegnazioni di ruolo seguenti:
    - [Collaboratore macchina virtuale](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) per creare un set di scalabilità di macchine virtuali e abilitare e rimuovere da un set di scalabilità di macchine virtuali l'identità gestita assegnata dal sistema e/o dall'utente.
    - [Collaboratore identità gestita](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) per creare un'identità assegnata dall'utente.
    - [Operatore identità gestita](/azure/role-based-access-control/built-in-roles#managed-identity-operator) per assegnare e rimuovere un'identità assegnata dall'utente da e verso un set di scalabilità di macchine virtuali.

## <a name="azure-resource-manager-templates"></a>Modelli di Gestione risorse di Azure

Analogamente al portale di Azure e all'esecuzione dello script, i modelli di gestione di [Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md) offrono la possibilità di distribuire risorse nuove o modificate definite da un gruppo di risorse di Azure. Diverse opzioni sono disponibili per la modifica e la distribuzione dei modelli, sia in locale che basati sul portale incluso quanto segue:

   - Usare un [modello personalizzato di Azure Marketplace](../../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template), che consente di creare un modello nuovo o di usare come base un modello comune esistente o un [modello di avvio rapido](https://azure.microsoft.com/documentation/templates/).
   - Derivazione da un gruppo di risorse esistente, tramite l'esportazione di un modello da una [distribuzione originale](../../azure-resource-manager/resource-manager-export-template.md#view-template-from-deployment-history) o dallo [stato attuale della distribuzione](../../azure-resource-manager/resource-manager-export-template.md#export-the-template-from-resource-group).
   - Usare un [editor JSON, ad esempio il codice di Visual Studio,](../../azure-resource-manager/resource-manager-create-first-template.md) locale e di caricarlo e distribuirlo tramite PowerShell o l'interfaccia della riga di comando.
   - Usare il [progetto del gruppo di risorse di Azure](../../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md) di Visual Studio per creare e distribuire un modello.  

Indipendentemente dall'opzione scelta, la sintassi dei modelli è la stessa durante la distribuzione iniziale e la ridistribuzione. L'abilitazione di un'identità del servizio gestita in una macchina virtuale nuova o esistente viene eseguita allo stesso modo. Per impostazione predefinita Azure Resource Manager esegue inoltre un [aggiornamento incrementale](../../azure-resource-manager/deployment-modes.md) per le distribuzioni.

## <a name="system-assigned-identity"></a>Identità assegnata dal sistema

In questa sezione, si abiliterà e disabiliterà il sistema di identità assegnata tramite un modello di Azure Resource Manager.

### <a name="enable-system-assigned-identity-during-creation-the-creation-of-a-virtual-machines-scale-set-or-a-existing-virtual-machine-scale-set"></a>Abilitare l'identità assegnata dal sistema durante la creazione di un set di scalabilità di macchine virtuali o di un set di scalabilità di macchine virtuali esistente

1. Se si accede ad Azure localmente o tramite il portale di Azure, usare un account che sia associato alla sottoscrizione di Azure che contiene il set di scalabilità di macchine virtuali.
   
2. Per abilitare l'identità assegnata dal sistema, caricare il modello in un editor, individuare la risorsa interessata `Microsoft.Compute/virtualMachinesScaleSets` nella sezione risorse e aggiungere la proprietà `identity` allo stesso livello della proprietà `"type": "Microsoft.Compute/virtualMachines"`. Usare la sintassi seguente:

   ```JSON
   "identity": { 
       "type": "SystemAssigned"
   }
   ```

3. (Facoltativo) Aggiungere l'estensione dell'identità del servizio gestita del set di scalabilità di macchine virtuali come elemento `extensionsProfile`. Questo passaggio è facoltativo in quanto è possibile usare anche l'identità del servizio metadati dell'istanza di Azure per recuperare i token.  Usare la sintassi seguente:

   >[!NOTE] 
   > Nell'esempio seguente si presuppone la distribuzione di un'estensione del set di scalabilità di macchine virtuali Windows (`ManagedIdentityExtensionForWindows`). È possibile eseguire la configurazione anche per Linux usando invece `ManagedIdentityExtensionForLinux` per gli elementi `"name"` e `"type"`.
   >

   ```json
   "extensionProfile": {
        "extensions": [
            {
                "name": "MSIWindowsExtension",
                "properties": {
                    "publisher": "Microsoft.ManagedIdentity",
                    "type": "ManagedIdentityExtensionForWindows",
                    "typeHandlerVersion": "1.0",
                    "autoUpgradeMinorVersion": true,
                    "settings": {
                        "port": 50342
                    },
                    "protectedSettings": {}
                }
            }
   ```

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

### <a name="disable-a-system-assigned-identity-from-an-azure-virtual-machine-scale-set"></a>Disabilitare un'identità assegnata dal sistema da un set di scalabilità di macchine virtuali di Azure

Se si dispone di un set di scalabilità di macchine virtuali per cui non è più necessaria un'identità del servizio gestita:

1. Se si accede ad Azure localmente o tramite il portale di Azure, usare un account che sia associato alla sottoscrizione di Azure che contiene il set di scalabilità di macchine virtuali.

2. Caricare il modello in un [editor](#azure-resource-manager-templates) e individuare `Microsoft.Compute/virtualMachineScaleSets`la risorsa interessata`resources` all'interno della sezione. Se si dispone di una macchina virtuale con solo un'identità assegnata dal sistema, è possibile disabilitarla modificando il tipo di identità e impostandolo su `None`.

   **Microsoft.Compute/virtualMachineScaleSets versione API 2018-06-01**

   Se la versione API è `2018-06-01` e la macchina virtuale ha identità assegnate sia dal sistema sia dall'utente, rimuovere `SystemAssigned` dal tipo di identità e mantenere `UserAssigned` insieme ai valori del dizionario userAssignedIdentities.

   **Microsoft.Compute/virtualMachineScaleSets versione API 2018-06-01 e precedenti**

   Se la versione API è `2017-12-01` e il set di scalabilità di macchine virtuali ha identità assegnate sia dal sistema sia dall'utente, rimuovere `SystemAssigned` dal tipo di identità e mantenere `UserAssigned` insieme alla matrice `identityIds` delle identità assegnate dall'utente. 
   
    

   L'esempio seguente illustra come rimuovere un'identità assegnata dal sistema da un set di scalabilità di macchine virtuali senza identità assegnate dall'utente:
   
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

## <a name="user-assigned-identity"></a>Identità assegnata dall'utente

In questa sezione verrà associata un'identità assegnata dall'utente a un set di scalabilità di macchine virtuali usando il modello di Azure Resource Manager.

> [!Note]
> Per creare un'identità del servizio gestita assegnata dall'utente usando un modello di Azure Resource Manager, vedere [Creare un'identità assegnata dall'utente](how-to-manage-ua-identity-arm.md#create-a-user-assigned-identity).

### <a name="assign-a-user-assigned-identity-to-an-azure-vmss"></a>Assegnare a un set di scalabilità di macchine virtuali di Azure un'identità assegnata dall'utente

1. Nell'elemento `resources` aggiungere la voce seguente per associare un'identità assegnata dall'utente al set di scalabilità di macchine virtuali.  Assicurarsi di sostituire `<USERASSIGNEDIDENTITY>` con il nome dell'identità assegnata dall'utente creata.
   
   **Microsoft.Compute/virtualMachineScaleSets versione API 2018-06-01**

   Se la versione API è `2018-06-01`, le identità assegnate dall'utente vengono archiviate nel formato dizionario `userAssignedIdentities` e il valore `<USERASSIGNEDIDENTITYNAME>` deve essere archiviato in una variabile definita nella sezione `variables` del modello.

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
    
   Se `apiVersion` è `2017-12-01` o una versione precedente, le identità assegnate dall'utente vengono archiviate nella matrice `identityIds` e il valore `<USERASSIGNEDIDENTITYNAME>` deve essere archiviato in una variabile definita nella sezione variabili del modello.

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

2. (Facoltativo) Aggiungere la voce seguente sotto l'elemento `extensionProfile` per assegnare l'estensione di identità gestita al set di scalabilità di macchine virtuali. Questo passaggio è facoltativo in quanto è possibile usare anche l'endpoint dell'identità del servizio metadati dell'istanza di Azure per recuperare i token. Usare la sintassi seguente:
   
    ```JSON
       "extensionProfile": {
            "extensions": [
                {
                    "name": "MSIWindowsExtension",
                    "properties": {
                        "publisher": "Microsoft.ManagedIdentity",
                        "type": "ManagedIdentityExtensionForWindows",
                        "typeHandlerVersion": "1.0",
                        "autoUpgradeMinorVersion": true,
                        "settings": {
                            "port": 50342
                        },
                        "protectedSettings": {}
                    }
                }
    ```

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

   **Microsoft.Compute/virtualMachines versione API 2017-12-01 e precedenti**

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
### <a name="remove-user-assigned-identity-from-an-azure-virtual-machine-scale-set"></a>Rimuovere un'identità assegnata dall'utente da un set di scalabilità di macchine virtuali di Azure

Se si dispone di un set di scalabilità di macchine virtuali per cui non è più necessaria un'identità del servizio gestita:

1. Se si accede ad Azure localmente o tramite il portale di Azure, usare un account che sia associato alla sottoscrizione di Azure che contiene il set di scalabilità di macchine virtuali.

2. Caricare il modello in un [editor](#azure-resource-manager-templates) e individuare `Microsoft.Compute/virtualMachineScaleSets`la risorsa interessata`resources` all'interno della sezione. Se si dispone di un set di scalabilità di macchine virtuali con solo un'identità assegnata dall'utente, è possibile disabilitarla modificando il tipo di identità e impostandolo su `None`.

   L'esempio seguente illustra come rimuovere tutte le identità assegnate dall'utente da una macchina virtuale senza identità assegnate dal sistema:

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
    
   Per rimuovere una singola identità assegnata dall'utente da un set di scalabilità di macchine virtuali, rimuoverla dal dizionario `userAssignedIdentities`.

   Se si dispone di un'identità assegnata dal sistema, mantenerla nel valore `type` del valore `identity`.

   **Microsoft.Compute/virtualMachineScaleSets versione API 2017-12-01**

   Per rimuovere una singola identità assegnata dall'utente da un set di scalabilità di macchine virtuali, rimuoverla dalla matrice `identityIds`.

   Se si dispone di un'identità assegnata dal sistema, mantenerla nel valore `type` del valore `identity`.
   
## <a name="next-steps"></a>Passaggi successivi

- Per un punto di vista più ampio sull'identità del servizio gestita, vedere [Managed Service Identity overview](overview.md) (Panoramica dell'identità del servizio gestita).

