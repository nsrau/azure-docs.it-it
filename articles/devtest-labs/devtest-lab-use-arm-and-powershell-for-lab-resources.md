---
title: Creare o modificare Lab usando modelli di Azure Resource Manager
description: Informazioni su come usare i modelli di Azure Resource Manager con PowerShell per creare o modificare automaticamente i lab in un'istanza di DevTest Labs
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 645f1d0717514d2c7e7b16844513327127e4e1a8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87272641"
---
# <a name="create-or-modify-labs-automatically-using-azure-resource-manager-templates-and-powershell"></a>Creare o modificare automaticamente i lab usando i modelli di Azure Resource Manager con PowerShell

DevTest Labs offre molti modelli di Azure Resource Manager e script di PowerShell utili per creare rapidamente e automaticamente nuovi lab o modificare quelli esistenti e quindi distribuire tali risorse.

In questo articolo viene descritto il processo di utilizzo di questi modelli e script per automatizzare la creazione, modifica e distribuzione dei propri lab. In questo articolo viene anche indicato dove è possibile trovare altre informazioni su come usare PowerShell per eseguire alcune attività comuni in DevTest Labs.

## <a name="step-1-gather-your-templates-and-scripts"></a>Passaggio 1: raccogliere modelli e script
È possibile trovare [modelli di Azure Resource Manager](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates) predefiniti e [script di PowerShell](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/Scripts) nel [repository GitHub](https://github.com/Azure/azure-devtestlab)pubblico. Usarli così come sono o personalizzarli in base alle proprie esigenze e archiviarli nel proprio [repository Git privato](devtest-lab-add-artifact-repo.md).

## <a name="step-2-modify-your-azure-resource-manager-template"></a>Passaggio 2: modificare il modello di Azure Resource Manager
Se non è mai stato creato un modello, è possibile seguire i passaggi forniti in [Creare il primo modello di Azure Resource Manager](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md).

Inoltre, l'articolo [Procedure consigliate per la creazione di modelli di Azure Resource Manager](../azure-resource-manager/templates/template-best-practices.md) offre molti suggerimenti e linee guida per creare modelli di Azure Resource Manager affidabili e semplici da usare. In genere, si userà una variazione di uno degli approcci o esempi forniti, modificando il modello in base alle esigenze.

## <a name="step-3-deploy-resources-with-powershell"></a>Passaggio 3: distribuire le risorse con PowerShell
Dopo aver personalizzato i modelli e gli script, seguire i passaggi necessari per [distribuire le risorse con i modelli di Azure Resource Manager e Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md). L'articolo contiene informazioni generali sull'uso di Azure PowerShell con i modelli di Azure Resource Manager per distribuire le risorse in Azure.


## <a name="common-tasks-you-can-perform-in-devtest-labs-using-powershell"></a>Attività comuni che è possibile eseguire nei lab di DevTest Labs tramite PowerShell
Esistono molte altre attività comuni che è possibile automatizzare con PowerShell. Nelle sezioni seguenti della documentazione vengono descritti i passaggi necessari per eseguire queste attività.

* [Creare un'immagine personalizzata da un file VHD usando PowerShell](devtest-lab-create-custom-image-from-vhd-using-powershell.md)
* [Caricare un file VHD nell'account di archiviazione del lab usando PowerShell](devtest-lab-upload-vhd-using-powershell.md)
* [Aggiungere un utente esterno a un lab usando PowerShell](devtest-lab-add-devtest-user.md#add-an-external-user-to-a-lab-using-powershell)
* [Creare un ruolo personalizzato lab tramite PowerShell](devtest-lab-grant-user-permissions-to-specific-lab-policies.md#creating-a-lab-custom-role-using-powershell)

### <a name="next-steps"></a>Passaggi successivi
* Informazioni su come creare un [repository Git privato](devtest-lab-add-artifact-repo.md) in cui verranno archiviati i modelli o script personalizzati.
* Esplorare i [modelli di Azure Resource Manager dalla raccolta di modelli di avvio rapido di Azure](https://github.com/Azure/azure-quickstart-templates).
