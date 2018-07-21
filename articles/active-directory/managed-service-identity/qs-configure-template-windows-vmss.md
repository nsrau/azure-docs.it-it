---
title: Configurare l'identità del servizio gestito in un set di scalabilità di macchine virtuali di Azure tramite un modello
description: Istruzioni dettagliate per la configurazione dell'identità del servizio gestito (MSI) in un set di scalabilità di macchine virtuali di Azure tramite un modello di Azure Resource Manager.
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
ms.openlocfilehash: ab3982c85cfb008bde08495f8cb8aa86d066d8c0
ms.sourcegitcommit: 7827d434ae8e904af9b573fb7c4f4799137f9d9b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/18/2018
ms.locfileid: "39114855"
---
# <a name="configure-a-vmss-managed-service-identity-by-using-a-template"></a>Configurare un'Identità del servizio gestito di un set di scalabilità di macchine virtuali tramite un modello

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Identità del servizio gestito offre servizi di Azure con un'identità gestita automaticamente in Azure Active Directory. È possibile usare questa identità per l'autenticazione a qualsiasi servizio che supporti l'autenticazione di Azure AD senza dover inserire le credenziali nel codice. 

Questo articolo illustra come eseguire le seguenti operazioni di identità del servizio gestita in un set di scalabilità di macchine virtuali di Azure usando il Modello di distribuzione Azure Resource Manager:
- Abilitare e disabilitare l'identità assegnata dal sistema in un VMSS di Azure
- Aggiungere e rimuovere un'identità assegnata dall'utente in un VMSS di Azure

## <a name="prerequisites"></a>Prerequisiti

- Se non si ha familiarità con l'identità del servizio gestita, vedere la [sezione sulla panoramica](overview.md). **Assicurarsi di conoscere la [differenza tra identità assegnata dal sistema e identità assegnata dall'utente](overview.md#how-does-it-work)**.
- Se non si ha un account Azure, [registrarsi per ottenere un account gratuito](https://azure.microsoft.com/free/) prima di continuare.

## <a name="azure-resource-manager-templates"></a>Modelli di Gestione risorse di Azure

Analogamente al portale di Azure e all'esecuzione dello script, i modelli di gestione di [Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md) offrono la possibilità di distribuire risorse nuove o modificate definite da un gruppo di risorse di Azure. Diverse opzioni sono disponibili per la modifica e la distribuzione dei modelli, sia in locale che basati sul portale incluso quanto segue:

   - Usare un [modello personalizzato di Azure Marketplace](../../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template), che consente di creare un modello nuovo o di usare come base un modello comune esistente o un [modello di avvio rapido](https://azure.microsoft.com/documentation/templates/).
   - Derivazione da un gruppo di risorse esistente, tramite l'esportazione di un modello da una [distribuzione originale](../../azure-resource-manager/resource-manager-export-template.md#view-template-from-deployment-history) o dallo [stato attuale della distribuzione](../../azure-resource-manager/resource-manager-export-template.md#export-the-template-from-resource-group).
   - Usare un [editor JSON, ad esempio il codice di Visual Studio,](../../azure-resource-manager/resource-manager-create-first-template.md) locale e di caricarlo e distribuirlo tramite PowerShell o l'interfaccia della riga di comando.
   - Usare il [progetto del gruppo di risorse di Azure](../../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md) di Visual Studio per creare e distribuire un modello.  

Indipendentemente dall'opzione scelta, la sintassi dei modelli è la stessa durante la distribuzione iniziale e la ridistribuzione. L'attivazione MSI in una macchina virtuale nuova o esistente viene eseguita nello stesso modo. Per impostazione predefinita Azure Resource Manager esegue inoltre un [aggiornamento incrementale](../../azure-resource-manager/resource-group-template-deploy.md#incremental-and-complete-deployments) per le distribuzioni.

## <a name="system-assigned-identity"></a>Identità assegnata dal sistema

In questa sezione, si abiliterà e disabiliterà il sistema di identità assegnata tramite un modello di Azure Resource Manager.

### <a name="enable-system-assigned-identity-during-creation-of-an-azure-vmss-or-an-existing-azure-vmss"></a>Abilitare l'identità assegnata dal sistema durante la creazione di un set di scalabilità di macchine virtuali di Azure oppure in un set di scalabilità di macchine virtuali esistente

1. Caricare il modello in un editor, individuare la risorsa interessata `Microsoft.Compute/virtualMachineScaleSets` all'interno della sezione `resources`. Quelle in uso potrebbero avere un aspetto leggermente diverso da quelle mostrate nella schermata seguente, a seconda dell'editor usato o del fatto che si stia modificando un modello per una distribuzione nuova o esistente.
   
   ![Schermata del modello - individuare la macchina virtuale](../managed-service-identity/media/msi-qs-configure-template-windows-vmss/msi-arm-template-file-before-vmss.png) 

2. Per abilitare l'identità assegnata dal sistema, aggiungere la proprietà `"identity"` allo stesso livello della proprietà `"type": "Microsoft.Compute/virtualMachineScaleSets"`. Usare la sintassi seguente:

   ```JSON
   "identity": { 
       "type": "systemAssigned"
   },
   ```

3. (Facoltativo) Aggiungere l'estensione dell'identità del servizio gestito del set di scalabilità di macchine virtuali come elemento `extensionsProfile`. Questo passaggio è facoltativo in quanto è possibile usare anche l'identità del servizio metadati dell'istanza di Azure per recuperare i token.  Usare la sintassi seguente:

   >[!NOTE] 
   > Nell'esempio seguente si presuppone la distribuzione di un'estensione del set di scalabilità di macchine virtuali Windows (`ManagedIdentityExtensionForWindows`). È possibile eseguire la configurazione anche per Linux usando invece `ManagedIdentityExtensionForLinux` per gli elementi `"name"` e `"type"`.
   >

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

4. Al termine il modello dovrebbe essere simile al seguente:

   ![Schermata del modello dopo l'aggiornamento](../managed-service-identity/media/msi-qs-configure-template-windows-vmss/msi-arm-template-file-after-vmss.png) 

### <a name="disable-a-system-assigned-identity-from-an-azure-virtual-machine-scale-set"></a>Disabilitare un'identità assegnata dal sistema da un set di scalabilità di macchine virtuali di Azure

> [!NOTE]
> La disabilitazione dell'identità del servizio gestita in una macchina virtuale non è attualmente supportata. Nel frattempo è possibile alternare tra l'uso delle identità assegnate dal sistema e delle identità assegnate dall'utente.

Se si dispone di un set di scalabilità di macchine virtuali che non ha più bisogno di un'identità assegnata dal sistema ma che ha ancora bisogno di identità assegnate dagli utenti:

- Caricare il modello in un editor e modificare il tipo di identità in `'UserAssigned'`

## <a name="user-assigned-identity"></a>Identità assegnata dall'utente

In questa sezione si assegna un'identità assegnata dall'utente a un set di scalabilità di macchine virtuali di Azure usando il modello di Azure Resource Manager.

> [!Note]
> Per creare un'identità del servizio gestita assegnata dall'utente usando un modello di Azure Resource Manager, vedere [Creare un'identità assegnata dall'utente](how-to-manage-ua-identity-arm.md#create-a-user-assigned-identity).

### <a name="assign-a-user-assigned-identity-to-an-azure-vmss"></a>Assegnare a un set di scalabilità di macchine virtuali di Azure un'identità assegnata dall'utente

1. Nell'elemento `resources`, aggiungere la voce seguente per assegnare un'identità assegnata dall'utente al set di scalabilità di macchine virtuali.  Assicurarsi di sostituire `<USERASSIGNEDIDENTITY>` con il nome dell'identità assegnata dall'utente creata.

   > [!Important]
   > Il valore `<USERASSIGNEDIDENTITYNAME>` mostrato nell'esempio seguente deve essere archiviato in una variabile.  Inoltre, per l'implementazione attualmente supportata di assegnazione di identità assegnate all'utente in una macchina virtuale di un modello di Gestione risorse, la versione dell'api deve corrispondere alla versione nell'esempio seguente. 

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
3.  Al termine il modello dovrebbe essere simile al seguente:
   
      ![Screenshot dell'identità assegnata dall'utente](./media/qs-configure-template-windows-vmss/qs-configure-template-windows-final.PNG)

## <a name="next-steps"></a>Passaggi successivi

- Per un punto di vista più ampio su MSI, leggere [Managed Service Identity overview](overview.md) (Panoramica dell'identità del servizio gestito).

