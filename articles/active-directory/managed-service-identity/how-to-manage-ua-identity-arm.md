---
title: Come creare ed eliminare un'identità del servizio gestita assegnata dall'utente usando Azure Resource Manager
description: Istruzioni dettagliate su come creare ed eliminare un'identità del servizio gestita assegnata dall'utente usando Azure Resource Manager.
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
ms.date: 04/16/2018
ms.author: daveba
ms.openlocfilehash: 42d5b55e0bddf2d027810bfdf146de9bfee8a0fb
ms.sourcegitcommit: bf522c6af890984e8b7bd7d633208cb88f62a841
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/20/2018
ms.locfileid: "39188132"
---
# <a name="create-list-and-delete-a-user-assigned-identity-using-azure-resource-manager"></a>Creare, elencare ed eliminare un'identità del servizio gestita assegnata dall'utente usando Azure Resource Manager

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

L'identità del servizio gestito offre servizi di Azure con un'identità gestita in Azure Active Directory. È possibile usare questa identità per l'autenticazione ai servizi che supportano l'autenticazione di Azure AD senza dover inserire le credenziali nel codice. 

In questo articolo viene creata un'identità del servizio gestita assegnata dall'utente usando Azure Resource Manager.

Non è possibile elencare ed eliminare un'identità del servizio gestita assegnata dall'utente usando un modello di Azure Resource Manager.  Vedere gli articoli seguenti per creare ed elencare un'identità assegnata dall'utente:

- [Elencare le identità assegnate dall'utente](how-to-manage-ua-identity-cli.md#list-user-assigned-identities)
- [Eliminare le identità assegnate dall'utente](how-to-manage-ua-identity-cli.md#delete-a-user-assigned-identity)
## <a name="prerequisites"></a>Prerequisiti

- Se non si ha familiarità con l'identità del servizio gestita, vedere la [sezione sulla panoramica](overview.md). **Assicurarsi di conoscere la [differenza tra identità assegnata dal sistema e identità assegnata dall'utente](overview.md#how-does-it-work)**.
- Se non si ha un account Azure, [registrarsi per ottenere un account gratuito](https://azure.microsoft.com/free/) prima di continuare.
- Per eseguire le operazioni in questo articolo, l'account deve avere l'assegnazione di ruolo seguente:
    - [Collaboratore identità gestita](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) per creare, leggere (elenco), aggiornare ed eliminare un'identità assegnata dall'utente.

## <a name="template-creation-and-editing"></a>Creazione e modifica del modello

Analogamente al portale di Azure e all'esecuzione dello script, i modelli di gestione di Azure Resource Manager offrono la possibilità di distribuire risorse nuove o modificate definite da un gruppo di risorse di Azure. Diverse opzioni sono disponibili per la modifica e la distribuzione dei modelli, sia in locale che basati sul portale incluso quanto segue:

- Usare un [modello personalizzato di Azure Marketplace](../../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template), che consente di creare un modello nuovo o di usare come base un modello comune esistente o un [modello di avvio rapido](https://azure.microsoft.com/documentation/templates/).
- Derivazione da un gruppo di risorse esistente, tramite l'esportazione di un modello da una [distribuzione originale](../../azure-resource-manager/resource-manager-export-template.md#view-template-from-deployment-history) o dallo [stato attuale della distribuzione](../../azure-resource-manager/resource-manager-export-template.md#export-the-template-from-resource-group).
- Usare un [editor JSON, ad esempio il codice di Visual Studio,](../../azure-resource-manager/resource-manager-create-first-template.md) locale e di caricarlo e distribuirlo tramite PowerShell o l'interfaccia della riga di comando.
- Usare il [progetto del gruppo di risorse di Azure](../../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md) di Visual Studio per creare e distribuire un modello. 

## <a name="create-a-user-assigned-identity"></a>Creare un'identità assegnata dall'utente 

Per creare un'identità assegnata dall'utente, usare il modello seguente. Sostituire il valore `<USER ASSIGNED IDENTITY NAME>` con i propri valori:

[!INCLUDE[ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "resourceName": {
          "type": "string",
          "metadata": {
            "description": "<USER ASSIGNED IDENTITY NAME>"
          }
        }
  },
  "resources": [
    {
      "type": "Microsoft.ManagedIdentity/userAssignedIdentities",
      "name": "[parameters('resourceName')]",
      "apiVersion": "2015-08-31-PREVIEW",
      "location": "[resourceGroup().location]"
    }
  ],
  "outputs": {
      "identityName": {
          "type": "string",
          "value": "[parameters('resourceName')]"
      }
  }
}
```
## <a name="related-content"></a>Contenuti correlati

Per informazioni su come assegnare un'identità assegnata dall'utente a una macchina virtuale di Azure tramite un modello di Azure Resource Manager vedere [Configurare un'identità del servizio gestita della macchina virtuale tramite un modello](qs-configure-template-windows-vm.md).


 
