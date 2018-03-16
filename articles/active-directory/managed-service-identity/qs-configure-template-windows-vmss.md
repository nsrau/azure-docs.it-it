---
title: "Configurare l'identità del servizio gestito in un set di scalabilità di macchine virtuali di Azure tramite un modello"
description: "Istruzioni dettagliate per la configurazione dell'identità del servizio gestito (MSI) in un set di scalabilità di macchine virtuali di Azure tramite un modello di Azure Resource Manager."
services: active-directory
documentationcenter: 
author: daveba
manager: mtillman
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/20/2018
ms.author: daveba
ms.openlocfilehash: 7f65c2ce53e30aa8682a9ee798af9001b4f210dc
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/08/2018
---
# <a name="configure-a-vm-managed-service-identity-by-using-a-template"></a>Configurare un'Identità del servizio gestito della macchina virtuale tramite un modello

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Identità del servizio gestito (MSI) offre servizi di Azure con un'identità gestita automaticamente in Azure Active Directory (Azure AD). È possibile usare questa identità per l'autenticazione a qualsiasi servizio che supporti l'autenticazione di Azure AD senza dover inserire le credenziali nel codice. 

In questo articolo si apprende come abilitare e rimuovere l'identità del servizio gestito per un set di scalabilità di macchine virtuali di Azure tramite un modello di distribuzione di Azure Resource Manager.

## <a name="prerequisites"></a>prerequisiti

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

## <a name="enable-msi-during-creation-of-an-azure-virtual-machine-scale-set-or-an-existing-azure-virtual-machine-scale-set"></a>Abilitare l'identità del servizio gestito durante la creazione di un set di scalabilità di macchine virtuali di Azure o in un set di scalabilità di macchine virtuali di Azure esistente

Analogamente al portale di Azure e all'esecuzione dello script, i modelli di gestione di Azure Resource Manager offrono la possibilità di distribuire risorse nuove o modificate definite da un gruppo di risorse di Azure. Diverse opzioni sono disponibili per la modifica e la distribuzione dei modelli, sia in locale che basati sul portale incluso quanto segue:

   - Usare un [modello personalizzato di Azure Marketplace](../../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template), che consente di creare un modello nuovo o di usare come base un modello comune esistente o un [modello di avvio rapido](https://azure.microsoft.com/documentation/templates/).
   - Derivazione da un gruppo di risorse esistente, tramite l'esportazione di un modello da una [distribuzione originale](../../azure-resource-manager/resource-manager-export-template.md#view-template-from-deployment-history) o dallo [stato attuale della distribuzione](../../azure-resource-manager/resource-manager-export-template.md#export-the-template-from-resource-group).
   - Usare un [editor JSON, ad esempio il codice di Visual Studio,](../../azure-resource-manager/resource-manager-create-first-template.md) locale e di caricarlo e distribuirlo tramite PowerShell o l'interfaccia della riga di comando.
   - Usare il [progetto del gruppo di risorse di Azure](../../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md) di Visual Studio per creare e distribuire un modello.  

Indipendentemente dall'opzione scelta, la sintassi dei modelli è la stessa durante la distribuzione iniziale e la ridistribuzione. L'attivazione dell'identità del servizio gestito in un set di scalabilità di macchine virtuali nuovo o esistente viene eseguita nello stesso modo. Per impostazione predefinita Azure Resource Manager esegue inoltre un [aggiornamento incrementale](../../azure-resource-manager/resource-group-template-deploy.md#incremental-and-complete-deployments) per le distribuzioni:

1. Se si accede ad Azure localmente o tramite il portale di Azure, usare un account che sia associato alla sottoscrizione di Azure che contiene il set di scalabilità di macchine virtuali.

2. Dopo avere caricato il modello in un editor, individuare la risorsa `Microsoft.Compute/virtualMachineScaleSets` interessata all'interno della sezione `resources`. Quelle in uso potrebbero avere un aspetto leggermente diverso da quelle mostrate nella schermata seguente, a seconda dell'editor usato o del fatto che si stia modificando un modello per una distribuzione nuova o esistente.
   
   ![Schermata del modello - individuare la macchina virtuale](../media/msi-qs-configure-template-windows-vmss/msi-arm-template-file-before-vmss.png) 

3. Aggiungere la proprietà `"identity"` allo stesso livello della proprietà `"type": "Microsoft.Compute/virtualMachineScaleSets"`. Usare la sintassi seguente:

   ```JSON
   "identity": { 
       "type": "systemAssigned"
   },
   ```

4. Aggiungere quindi l'estensione dell'identità del servizio gestito del set di scalabilità di macchine virtuali come un elemento `extensionsProfile`. Usare la sintassi seguente:

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

5. Al termine il modello dovrebbe essere simile al seguente:

   ![Schermata del modello dopo l'aggiornamento](../media/msi-qs-configure-template-windows-vmss/msi-arm-template-file-after-vmss.png) 

## <a name="remove-msi-from-an-azure-virtual-machine-scale-set"></a>Rimuovere l'identità del servizio gestito da un set di scalabilità di macchine virtuali di Azure

Se si dispone di un set di scalabilità di macchine virtuali per cui non è più necessaria un'identità del servizio gestito:

1. Se si accede ad Azure localmente o tramite il portale di Azure, usare un account che sia associato alla sottoscrizione di Azure che contiene il set di scalabilità di macchine virtuali.

2. Rimuovere i due elementi che sono stati aggiunti nella sezione precedente: le proprietà `"identity"` e `"extensionsProfile"` del set di scalabilità di macchine virtuali.

## <a name="next-steps"></a>Passaggi successivi

- Per un punto di vista più ampio su MSI, leggere [Managed Service Identity overview](overview.md) (Panoramica dell'identità del servizio gestito).

