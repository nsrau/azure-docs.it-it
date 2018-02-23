---
title: "Come configurare un'identità del servizio gestito assegnata dall'utente per una macchina virtuale di Azure tramite un modello di Azure"
description: "Istruzioni dettagliate per la configurazione di un'identità del servizio gestito assegnata dall'utente per una macchina virtuale di Azure tramite un modello di Azure Resource Manager."
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
ms.date: 12/22/2017
ms.author: daveba
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: e01e4c397e0d0a19280a32fc1e8341b57b47e4eb
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/03/2018
---
# <a name="configure-a-user-assigned-managed-service-identity-msi-for-a-vm-using-an-azure-template"></a>Configurare un'identità del servizio gestito assegnata dall'utente per una macchina virtuale tramite un modello di Azure

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

L'identità del servizio gestito offre servizi di Azure con un'identità gestita in Azure Active Directory. È possibile usare questa identità per l'autenticazione ai servizi che supportano l'autenticazione di Azure AD senza dover inserire le credenziali nel codice. 

In questo articolo viene descritto come abilitare e rimuovere un'identità del servizio gestito assegnata dall'utente per una macchina virtuale di Azure usando un modello di distribuzione di Azure Resource Manager.

## <a name="prerequisites"></a>prerequisiti

[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]

## <a name="enable-msi-during-creation-of-an-azure-vm-or-on-an-existing-vm"></a>Abilitare l'Identità del servizio gestito durante la creazione di una macchina virtuale di Azure o di una macchina virtuale esistente

Analogamente al portale di Azure e all'esecuzione dello script, i modelli di gestione di Azure Resource Manager offrono la possibilità di distribuire risorse nuove o modificate definite da un gruppo di risorse di Azure. Diverse opzioni sono disponibili per la modifica e la distribuzione dei modelli, sia in locale che basati sul portale incluso quanto segue:

   - Usare un [modello personalizzato di Azure Marketplace](~/articles/azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template), che consente di creare un modello nuovo o di usare come base un modello comune esistente o un [modello di avvio rapido](https://azure.microsoft.com/documentation/templates/).
   - Derivazione da un gruppo di risorse esistente, tramite l'esportazione di un modello da una [distribuzione originale](~/articles/azure-resource-manager/resource-manager-export-template.md#view-template-from-deployment-history) o dallo [stato attuale della distribuzione](~/articles/azure-resource-manager/resource-manager-export-template.md#export-the-template-from-resource-group).
   - Usare un [editor JSON, ad esempio il codice di Visual Studio,](~/articles/azure-resource-manager/resource-manager-create-first-template.md) locale e di caricarlo e distribuirlo tramite PowerShell o l'interfaccia della riga di comando.
   - Usare il [progetto del gruppo di risorse di Azure](~/articles/azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md) di Visual Studio per creare e distribuire un modello.  

Indipendentemente dall'opzione scelta, la sintassi dei modelli è la stessa durante la distribuzione iniziale e la ridistribuzione. La creazione e l'assegnazione di un'identità del servizio gestito assegnata dall'utente per una macchina virtuale nuova o esistente vengono eseguite allo stesso modo. Per impostazione predefinita Azure Resource Manager esegue inoltre un [aggiornamento incrementale](~/articles/azure-resource-manager/resource-group-template-deploy.md#incremental-and-complete-deployments) per le distribuzioni:

1. Che si acceda ad Azure in locale o tramite il portale di Azure, usare un account associato alla sottoscrizione di Azure che contiene l'identità del servizio gestito e la macchina virtuale. Assicurarsi anche che l'account appartenga a un ruolo che fornisce le autorizzazioni di scrittura per la sottoscrizione o le risorse, ad esempio il ruolo di "proprietario".

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

