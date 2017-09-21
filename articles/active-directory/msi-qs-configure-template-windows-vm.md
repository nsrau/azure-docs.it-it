---
title: "Come configurare l'Identità del servizio gestito in una macchina virtuale di Azure tramite un modello"
description: "Istruzioni dettagliate per la configurazione dell'Identità del servizio gestito in una macchina virtuale di Azure, tramite un modello di Azure Resource Manager."
services: active-directory
documentationcenter: 
author: bryanla
manager: mbaldwin
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/14/2017
ms.author: bryanla
ms.translationtype: HT
ms.sourcegitcommit: 47ba7c7004ecf68f4a112ddf391eb645851ca1fb
ms.openlocfilehash: 266458323ca54d9805aea12108faed79e69d30b0
ms.contentlocale: it-it
ms.lasthandoff: 09/14/2017

---

# <a name="configure-a-vm-managed-service-identity-msi-using-a-template"></a>Configurare un'Identità del servizio gestito della macchina virtuale tramite un modello

[!INCLUDE[preview-notice](../../includes/active-directory-msi-preview-notice.md)]

Identità del servizio gestito offre servizi di Azure con un'identità gestita automaticamente in Azure Active Directory. È possibile usare questa identità per l'autenticazione a qualsiasi servizio che supporti l'autenticazione di Azure AD senza dover inserire le credenziali nel codice. 

In questo articolo si apprende come abilitare e rimuovere l'Identità del servizio gestito per una macchina virtuale Windows di Azure tramite un modello di distribuzione di Azure Resource Manager.

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [msi-qs-configure-prereqs](../../includes/msi-qs-configure-prereqs.md)]

## <a name="enable-msi-during-creation-of-an-azure-vm-or-on-an-existing-vm"></a>Abilitare l'Identità del servizio gestito durante la creazione di una macchina virtuale di Azure o di una macchina virtuale esistente

Analogamente ad Azure portale e all'esecuzione dello script, i modelli di gestione di Azure Resource Manager offrono la possibilità di distribuire risorse nuove o modificate definite da un gruppo di risorse di Azure. Diverse opzioni sono disponibili per la modifica e la distribuzione dei modelli, sia in locale che basati sul portale o sul Web. Alcune di queste opzioni sono:

   - Uso di un [modello personalizzato del marketplace di Azure](../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template), che consente di creare un modello nuovo o di usare come base un modello comune esistente o un [modello di avvio rapido](https://azure.microsoft.com/documentation/templates/).
   - Derivazione da un gruppo di risorse esistente, tramite l'esportazione di un modello da una [distribuzione originale](../azure-resource-manager/resource-manager-export-template.md#view-template-from-deployment-history) o dallo [stato attuale della distribuzione](../azure-resource-manager/resource-manager-export-template.md#export-the-template-from-resource-group).
   - Uso di un [editor JSON, ad esempio il codice di Visual Studio,](../azure-resource-manager/resource-manager-create-first-template.md) locale caricato o distribuito tramite PowerShell o l'interfaccia della riga di comando.
   - Uso del [progetto del gruppo di risorse di Azure](../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md) di Visual Studio per creare e distribuire il modello.  

Indipendentemente dal percorso che si segue, la sintassi del modello è la stessa durante la distribuzione iniziale e la ridistribuzione, pertanto l'abilitazione dell'Identità del servizio gestito in una macchina virtuale nuova o esistente viene eseguita nello stesso modo. Per impostazione predefinita, in aggiunta, Azure Resource Manager esegue un [aggiornamento incrementale](../azure-resource-manager/resource-group-template-deploy.md#incremental-and-complete-deployments) per le distribuzioni:

1. Dopo avere caricato il modello in un editor, individuare la risorsa `Microsoft.Compute/virtualMachines` interessata all'interno della sezione `resources`. Quelle in uso potrebbe avere un aspetto leggermente diverso da quelle mostrate in questa schermata, a seconda l'editor usato o del fatto che si stia modificando un modello per una distribuzione nuova o esistente:

   >[!NOTE] 
   > Il passaggio 2 presuppone anche che le variabili `vmName`, `storageAccountName` e `nicName` siano definite nel modello.
   >

   ![Modello prima della schermata di rilevamento della macchina virtuale](./media/msi-qs-configure-template-windows-vm/template-file-before.png) 

2. Aggiungere la proprietà `"identity"` allo stesso livello della proprietà `"type": "Microsoft.Compute/virtualMachines"` usando la sintassi seguente:

   ```JSON
   "identity": { 
       "type": "systemAssigned"
   },
   ```

3. Quindi aggiungere l'estensione dell'Identità di servizio gestito della macchina virtuale come elemento `resources` usando la sintassi seguente:

   >[!NOTE] 
   > Nell'esempio seguente si presuppone la distribuzione di un'estensione della macchina virtuale Windows (`ManagedIdentityExtensionForWindows`). È anche possibile configurare per Linux usando `ManagedIdentityExtensionForLinux`.
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

4. Al termine il modello dovrebbe essere simile all'esempio seguente:

   ![Modello dopo l'immagine](./media/msi-qs-configure-template-windows-vm/template-file-after.png) 

## <a name="remove-msi-from-an-azure-vm"></a>Rimuovere l'Identità del servizio gestito da una macchina virtuale di Azure

Se si dispone di una macchina virtuale per cui l'Identità del servizio gestito non è più necessaria, rimuovere i due elementi aggiunti nell'esempio precedente: la proprietà `"identity"` della macchina virtuale e la risorsa `"Microsoft.Compute/virtualMachines/extensions"`.

## <a name="related-content"></a>Contenuti correlati

- [Panoramica dell'Identità del servizio gestito](msi-overview.md)

Usare la sezione dei commenti seguente per fornire commenti e suggerimenti utili per migliorare e organizzare i contenuti disponibili.


