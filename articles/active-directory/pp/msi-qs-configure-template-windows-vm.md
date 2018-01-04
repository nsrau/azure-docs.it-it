---
title: Come configurare un file MSI assegnati dall'utente per una macchina virtuale di Azure utilizzando un modello di Azure
description: "Passaggio da istruzioni dettagliate per la configurazione di un utente assegnato gestiti servizio identità (MSI) per una macchina virtuale di Azure, utilizzando un modello di gestione risorse di Azure."
services: active-directory
documentationcenter: 
author: bryanla
manager: mtillman
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/22/2017
ms.author: bryanla
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: d97f0fa2d6c1c92aaa3d5c74dd6715de00d32438
ms.sourcegitcommit: a648f9d7a502bfbab4cd89c9e25aa03d1a0c412b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/22/2017
---
# <a name="configure-a-user-assigned-managed-service-identity-msi-for-a-vm-using-an-azure-template"></a>Configurare un utente assegnato gestiti servizio identità (MSI) per una macchina virtuale, utilizzando un modello di Azure

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Identità del servizio gestito fornisce servizi di Azure con un'identità gestita in Azure Active Directory. È possibile utilizzare questa identità di autenticazione ai servizi che supportano l'autenticazione di Azure AD, senza la necessità di credenziali nel codice. 

In questo articolo informazioni su come attivare e rimuovere un file MSI assegnati dall'utente per una macchina virtuale di Azure, utilizzando un modello di distribuzione Azure Resource Manager.

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]

## <a name="enable-msi-during-creation-of-an-azure-vm-or-on-an-existing-vm"></a>Abilitare l'Identità del servizio gestito durante la creazione di una macchina virtuale di Azure o di una macchina virtuale esistente

Analogamente al portale di Azure e all'esecuzione dello script, i modelli di gestione di Azure Resource Manager offrono la possibilità di distribuire risorse nuove o modificate definite da un gruppo di risorse di Azure. Diverse opzioni sono disponibili per la modifica e la distribuzione dei modelli, sia in locale che basati sul portale incluso quanto segue:

   - Usare un [modello personalizzato di Azure Marketplace](~/articles/azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template), che consente di creare un modello nuovo o di usare come base un modello comune esistente o un [modello di avvio rapido](https://azure.microsoft.com/documentation/templates/).
   - Derivazione da un gruppo di risorse esistente, tramite l'esportazione di un modello da una [distribuzione originale](~/articles/azure-resource-manager/resource-manager-export-template.md#view-template-from-deployment-history) o dallo [stato attuale della distribuzione](~/articles/azure-resource-manager/resource-manager-export-template.md#export-the-template-from-resource-group).
   - Usare un [editor JSON, ad esempio il codice di Visual Studio,](~/articles/azure-resource-manager/resource-manager-create-first-template.md) locale e di caricarlo e distribuirlo tramite PowerShell o l'interfaccia della riga di comando.
   - Usare il [progetto del gruppo di risorse di Azure](~/articles/azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md) di Visual Studio per creare e distribuire un modello.  

Indipendentemente dall'opzione scelta, la sintassi dei modelli è la stessa durante la distribuzione iniziale e la ridistribuzione. Creazione e l'assegnazione di un file MSI utente assegnato a una macchina virtuale nuova o esistente, viene eseguita nello stesso modo. Per impostazione predefinita Azure Resource Manager esegue inoltre un [aggiornamento incrementale](~/articles/azure-resource-manager/resource-group-template-deploy.md#incremental-and-complete-deployments) per le distribuzioni:

1. Se si accede a Azure localmente o tramite il portale di Azure, utilizzare un account che viene associato alla sottoscrizione di Azure che contiene i file MSI e macchina virtuale. Verificare inoltre che l'account appartiene a un ruolo che fornisce le autorizzazioni di scrittura nella sottoscrizione o le risorse (ad esempio, il ruolo di "proprietario").

2. Dopo avere caricato il modello in un editor, individuare la risorsa `Microsoft.Compute/virtualMachines` interessata all'interno della sezione `resources`. Quelle in uso potrebbero avere un aspetto leggermente diverso da quelle mostrate nella schermata seguente, a seconda dell'editor usato o del fatto che si stia modificando un modello per una distribuzione nuova o esistente.

   >[!NOTE] 
   > In questo esempio si presuppone che le variabili, ad esempio `vmName`, `storageAccountName` e `nicName`, siano state definite nel modello.
   >

   ![Schermata del modello - individuare la macchina virtuale](~/articles/active-directory/media/msi-qs-configure-template-windows-vm/template-file-before.png) 

3. Aggiungere la proprietà `"identity"` allo stesso livello della proprietà `"type": "Microsoft.Compute/virtualMachines"`. Usare la sintassi seguente:

   ```JSON
   "identity": { 
       "type": "systemAssigned"
   },
   ```

4. Quindi aggiungere l'estensione MSI della macchina virtuale come elemento `resources`. Usare la sintassi seguente:

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

   ![Schermata del modello dopo l'aggiornamento](~/articles/active-directory/media/msi-qs-configure-template-windows-vm/template-file-after.png) 

## <a name="remove-msi-from-an-azure-vm"></a>Rimuovere l'Identità del servizio gestito da una macchina virtuale di Azure

Se si dispone di una macchina virtuale per cui non è più necessario MSI:

1. Se si accede ad Azure localmente o tramite il portale di Azure, usare un account che sia associato alla sottoscrizione di Azure che contiene la VM. Assicurarsi anche che l'account appartenga a un ruolo che fornisce le autorizzazioni di scrittura nella VM, ad esempio "Collaboratore macchine virtuali".

2. Rimuovere i due elementi che sono stati aggiunti nella sezione precedente: la proprietà `"identity"` della VM e la risorsa `"Microsoft.Compute/virtualMachines/extensions"`.

## <a name="related-content"></a>Contenuti correlati

- Per un punto di vista più ampio su MSI, leggere [Managed Service Identity overview](msi-overview.md) (Panoramica dell'identità del servizio gestito).

