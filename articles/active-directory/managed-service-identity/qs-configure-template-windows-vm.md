---
title: Come configurare MSI in una macchina virtuale di Azure tramite un modello
description: Istruzioni dettagliate per la configurazione dell'Identità del servizio gestito (MSI) in una macchina virtuale di Azure, tramite un modello di Azure Resource Manager.
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
ms.date: 09/14/2017
ms.author: daveba
ms.openlocfilehash: d8490dcba35cfeabb3da589f3d079571d5e98d3b
ms.sourcegitcommit: f606248b31182cc559b21e79778c9397127e54df
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/12/2018
ms.locfileid: "38969205"
---
# <a name="configure-a-vm-managed-service-identity-by-using-a-template"></a>Configurare un'Identità del servizio gestito della macchina virtuale tramite un modello

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Identità del servizio gestito offre servizi di Azure con un'identità gestita automaticamente in Azure Active Directory. È possibile usare questa identità per l'autenticazione a qualsiasi servizio che supporti l'autenticazione di Azure AD senza dover inserire le credenziali nel codice. 

Questo articolo illustra come eseguire le seguenti operazioni di identità del servizio gestita in una macchina virtuale di Azure usando il Modello di distribuzione Azure Resource Manager:

## <a name="prerequisites"></a>prerequisiti

- Se non si ha familiarità con l'identità del servizio gestita, vedere la [sezione sulla panoramica](overview.md). **Assicurarsi di conoscere la [differenza tra identità assegnata dal sistema e identità assegnata dall'utente](overview.md#how-does-it-work)**.
- Se non si ha un account Azure, [registrarsi per ottenere un account gratuito](https://azure.microsoft.com/free/) prima di continuare.

## <a name="azure-resource-manager-templates"></a>Modelli di Gestione risorse di Azure

Analogamente al portale di Azure e all'esecuzione dello script, i modelli di gestione di [Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md) offrono la possibilità di distribuire risorse nuove o modificate definite da un gruppo di risorse di Azure. Diverse opzioni sono disponibili per la modifica e la distribuzione dei modelli, sia in locale che basati sul portale incluso quanto segue:

   - Usare un [modello personalizzato di Azure Marketplace](../../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template), che consente di creare un modello nuovo o di usare come base un modello comune esistente o un [modello di avvio rapido](https://azure.microsoft.com/documentation/templates/).
   - Derivazione da un gruppo di risorse esistente, tramite l'esportazione di un modello da una [distribuzione originale](../../azure-resource-manager/resource-manager-export-template.md#view-template-from-deployment-history) o dallo [stato attuale della distribuzione](../../azure-resource-manager/resource-manager-export-template.md#export-the-template-from-resource-group).
   - Usare un [editor JSON, ad esempio il codice di Visual Studio,](../../azure-resource-manager/resource-manager-create-first-template.md) locale e di caricarlo e distribuirlo tramite PowerShell o l'interfaccia della riga di comando.
   - Usare il [progetto del gruppo di risorse di Azure](../../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md) di Visual Studio per creare e distribuire un modello.  

Indipendentemente dall'opzione scelta, la sintassi dei modelli è la stessa durante la distribuzione iniziale e la ridistribuzione. L'abilitazione di un sistema o di un'identità assegnata dall'utente in una macchina virtuale nuova o esistente viene eseguita allo stesso modo. Per impostazione predefinita Azure Resource Manager esegue inoltre un [aggiornamento incrementale](../../azure-resource-manager/resource-group-template-deploy.md#incremental-and-complete-deployments) per le distribuzioni.

## <a name="system-assigned-identity"></a>Identità assegnata dal sistema

In questa sezione, si abiliterà e disabiliterà un sistema di identità assegnata tramite un modello di Azure Resource Manager.

### <a name="enable-system-assigned-identity-during-creation-of-an-azure-vm-or-on-an-existing-vm"></a>Abilitare l'identità assegnata dal sistema durante la creazione di una macchina virtuale di Azure oppure in una macchina virtuale esistente

1. Se si accede ad Azure localmente o tramite il portale di Azure, usare un account che sia associato alla sottoscrizione di Azure che contiene la VM. Assicurarsi anche che l'account appartenga a un ruolo che fornisce le autorizzazioni di scrittura nella VM, ad esempio "Collaboratore macchine virtuali".

2. Dopo avere caricato il modello in un editor, individuare la risorsa `Microsoft.Compute/virtualMachines` interessata all'interno della sezione `resources`. Quelle in uso potrebbero avere un aspetto leggermente diverso da quelle mostrate nella schermata seguente, a seconda dell'editor usato o del fatto che si stia modificando un modello per una distribuzione nuova o esistente.

   >[!NOTE] 
   > In questo esempio si presuppone che le variabili, ad esempio `vmName`, `storageAccountName` e `nicName`, siano state definite nel modello.
   >

   ![Schermata del modello - individuare la macchina virtuale](../media/msi-qs-configure-template-windows-vm/template-file-before.png) 

3. Per abilitare identità assegnate dal sistema, aggiungere la proprietà `"identity"` allo stesso livello della proprietà `"type": "Microsoft.Compute/virtualMachines"`. Usare la sintassi seguente:

   ```JSON
   "identity": { 
       "type": "systemAssigned"
   },
   ```

4. (Facoltativo) Aggiungere l'estensione MSI della macchina virtuale come elemento `resources`. Questo passaggio è facoltativo in quanto è possibile usare anche l'endpoint dell'identità del servizio metadati dell'istanza di Azure per recuperare i token.  Usare la sintassi seguente:

   >[!NOTE] 
   > Nell'esempio seguente si presuppone la distribuzione di un'estensione della macchina virtuale Windows (`ManagedIdentityExtensionForWindows`). È possibile eseguire la configurazione anche per Linux usando invece `ManagedIdentityExtensionForLinux` per gli elementi `"name"` e `"type"`.
   >

   ```JSON
   { 
       "type": "Microsoft.Compute/virtualMachines/extensions",
       "name": "[concat(variables('vmName'),'/ManagedIdentityExtensionForWindows')]",
       "apiVersion": "2016-03-30",
       "location": "[resourceGroup().location]",
       "dependsOn": [
           "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
       ],
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

5. Al termine il modello dovrebbe essere simile al seguente:

   ![Schermata del modello dopo l'aggiornamento](../media/msi-qs-configure-template-windows-vm/template-file-after.png)

### <a name="assign-a-role-the-vms-system-assigned-identity"></a>Assegnare un ruolo identità assegnata dal sistema della macchina virtuale

Dopo avere abilitato l'identità assegnata dal sistema sulla macchina virtuale, è possibile autorizzare, ad esempio, un ruolo di accesso **Lettore** al gruppo di risorse in cui è stato creata.

1. Se si accede ad Azure localmente o tramite il portale di Azure, usare un account che sia associato alla sottoscrizione di Azure che contiene la VM. Assicurarsi inoltre che l'account appartenga a un ruolo che fornisce le autorizzazioni di scrittura nella VM (ad esempio, "Collaboratore macchine virtuali").
 
2. Caricare il modello in un' [editor](#azure-resource-manager-templates) e aggiungere le informazioni seguenti per assegnare alla macchina virtuale l'accesso come **Lettore** al gruppo di risorse in cui è stato creata.  La struttura del modello può variare a seconda dell'editor e del modello di distribuzione scelto.
   
   Sotto la sezione `parameters` aggiungere quanto segue:

    ```JSON
    "builtInRoleType": {
          "type": "string",
          "defaultValue": "Reader"
        },
        "rbacGuid": {
          "type": "string"
        }
    ```

    Sotto la sezione `variables` aggiungere quanto segue:

    ```JSON
    "Reader": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'acdd72a7-3385-48ef-bd42-f606fba81ae7')]"
    ```

    Sotto la sezione `resources` aggiungere quanto segue:

    ```JSON
    {
        "apiVersion": "2017-09-01",
         "type": "Microsoft.Authorization/roleAssignments",
         "name": "[parameters('rbacGuid')]",
         "properties": {
                "roleDefinitionId": "[variables(parameters('builtInRoleType'))]",
                "principalId": "[reference(variables('vmResourceId'), '2017-12-01', 'Full').identity.principalId]",
                "scope": "[resourceGroup().id]"
          },
          "dependsOn": [
                "[concat('Microsoft.Compute/virtualMachines/', parameters('vmName'))]"
            ]
    }
    ```

### <a name="disable-a-system-assigned-identity-from-an-azure-vm"></a>Disabilitare un'identità assegnata dal sistema in una macchina virtuale di Azure

Se si dispone di una macchina virtuale per cui non è più necessaria un'identità del servizio gestito:

1. Se si accede ad Azure localmente o tramite il portale di Azure, usare un account che sia associato alla sottoscrizione di Azure che contiene la VM. Assicurarsi anche che l'account appartenga a un ruolo che fornisce le autorizzazioni di scrittura nella VM, ad esempio "Collaboratore macchine virtuali".

2. Caricare il modello in un [editor](#azure-resource-manager-templates) e individuare `Microsoft.Compute/virtualMachines`la risorsa interessata`resources` all'interno della sezione. Se si dispone di una macchina virtuale con solo un'identità assegnata dal sistema, è possibile disabilitarla modificando il tipo di identità a `None`.  Se la macchina virtuale ha sia identità assegnate dal sistema, sia assegnate all'utente, rimuovere `SystemAssigned` dal tipo di identità e mantenere `UserAssigned` insieme alla matrice `identityIds` delle identità assegnate dall'utente.  L'esempio seguente illustra come si rimuove un'identità assegnata dal sistema da una macchina virtuale senza identità assegnate all'utente:
   
   ```JSON
    {
      "apiVersion": "2017-12-01",
      "type": "Microsoft.Compute/virtualMachines",
      "name": "[parameters('vmName')]",
      "location": "[resourceGroup().location]",
      "identity": { 
          "type": "None"
    }
   ```

## <a name="user-assigned-identity"></a>Identità assegnata dall'utente

In questa sezione si assegna un'identità assegnata dall'utente a una macchina virtuale di Azure tramite il modello di Azure Resource Manager.

> [!Note]
> Per creare un'identità del servizio gestita assegnata dall'utente usando un modello di Azure Resource Manager, vedere [Creare un'identità assegnata dall'utente](how-to-manage-ua-identity-arm.md#create-a-user-assigned-identity).

 ### <a name="assign-a-user-assigned-identity-to-an-azure-vm"></a>Assegnare a una macchina virtuale di Azure un'identità assegnata all'utente

1. Nell'elemento `resources`, aggiungere la voce seguente per assegnare un'identità assegnata dall'utente alla macchina virtuale.  Assicurarsi di sostituire `<USERASSIGNEDIDENTITY>` con il nome dell'identità assegnata dall'utente creata.
    ```json
    {
        "apiVersion": "2017-12-01",
        "type": "Microsoft.Compute/virtualMachines",
        "name": "[variables('vmName')]",
        "location": "[resourceGroup().location]",
        "identity": {
            "type": "userAssigned",
            "identityIds": [
                "[resourceID('Micrososft.ManagedIdentity/userAssignedIdentities/',variables('<USERASSIGNEDIDENTITYNAME>'))]"
            ]
        },
    ```
    
2. (Facoltativo) Successivamente, sotto l'elemento `resources`, aggiungere la voce seguente per assegnare l'estensione di identità gestita alla macchina virtuale. Questo passaggio è facoltativo in quanto è possibile usare anche l'endpoint dell'identità del servizio metadati dell'istanza di Azure per recuperare i token. Usare la sintassi seguente:
    ```json
    {
        "type": "Microsoft.Compute/virtualMachines/extensions",
        "name": "[concat(variables('vmName'),'/ManagedIdentityExtensionForWindows')]",
        "apiVersion": "2015-05-01-preview",
        "location": "[resourceGroup().location]",
        "dependsOn": [
            "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
        ],
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
    ```
    
3.  Al termine il modello dovrebbe essere simile al seguente:

      ![Screenshot dell'identità assegnata dall'utente](./media/qs-configure-template-windows-vm/qs-configure-template-windows-vm-ua-final.PNG)


## <a name="related-content"></a>Contenuti correlati

- Per un punto di vista più ampio su MSI, leggere [Managed Service Identity overview](overview.md) (Panoramica dell'identità del servizio gestito).

