---
title: Creare o modificare automaticamente i lab usando i modelli di Azure Resource Manager con PowerShell | Microsoft Docs
description: Informazioni su come usare i modelli di Azure Resource Manager con PowerShell per creare o modificare automaticamente i lab in un&quot;istanza di DevTest Labs
services: devtest-lab,virtual-machines,visual-studio-online
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: dad9944c-0b20-48be-ba80-8f4aa0950903
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/21/2017
ms.author: tarcher
translationtype: Human Translation
ms.sourcegitcommit: a3ca1527eee068e952f81f6629d7160803b3f45a
ms.openlocfilehash: 8ee0301a48265b91f951c96c3339a3c3e3bdb1cb
ms.lasthandoff: 04/27/2017


---

# <a name="create-or-modify-labs-automatically-using-azure-resource-manager-templates-and-powershell"></a>Creare o modificare automaticamente i lab usando i modelli di Azure Resource Manager con PowerShell

DevTest Labs offre molti modelli di Azure Resource Manager e script di PowerShell utili per creare rapidamente e automaticamente nuovi lab o modificare quelli esistenti e quindi distribuire tali risorse.

In questo articolo viene descritto il processo di utilizzo di questi modelli e script per automatizzare la creazione, modifica e distribuzione dei propri lab. In questo articolo viene anche indicato dove è possibile trovare altre informazioni su come usare PowerShell per eseguire alcune attività comuni in DevTest Labs.

## <a name="step-1-gather-your-templates-and-scripts"></a>Passaggio 1: raccogliere modelli e script
È possibile trovare [modelli di Azure Resource Manager](https://github.com/Azure/azure-devtestlab/tree/master/ARMTemplates) e [script di PowerShell](https://github.com/Azure/azure-devtestlab/tree/master/Scripts) preconfigurati nel nostro [repository Github](https://github.com/Azure/azure-devtestlab) pubblico. Usarli così come sono o personalizzarli in base alle proprie esigenze e archiviarli nel proprio [repository Git privato](devtest-lab-add-artifact-repo.md). 

## <a name="step-2-modify-your-azure-resource-manager-template"></a>Passaggio 2: modificare il modello di Azure Resource Manager
Nell'articolo [Creare ambienti con più macchine virtuali e risorse PaaS con i modelli di Azure Resource Manager](devtest-lab-create-environment-from-arm.md) viene illustrato come usare i modelli di Azure Resource Manager all'interno di DevTest Labs per definire l'infrastruttura e la configurazione della soluzione di Azure e distribuire ripetutamente più macchine virtuali in uno stato coerente.

Se ad esempio è stata creata una nuova rete virtuale e si intende applicarla a tutti i propri lab esistenti, è possibile farlo rapidamente tramite un modello di Azure Resource Manager.

## <a name="step-3-deploy-resources-with-powershell"></a>Passaggio 3: distribuire le risorse con PowerShell
Dopo aver personalizzato i modelli e gli script, seguire i passaggi necessari per [distribuire le risorse con i modelli di Azure Resource Manager e Azure PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy). L'articolo contiene informazioni generali sull'uso di Azure PowerShell con i modelli di Azure Resource Manager per distribuire le risorse in Azure.


## <a name="common-tasks-you-can-perform-in-devtest-labs-using-powershell"></a>Attività comuni che è possibile eseguire nei lab di DevTest Labs tramite PowerShell
Esistono molte altre attività comuni che è possibile automatizzare con PowerShell. Nelle sezioni seguenti della documentazione vengono descritti i passaggi necessari per eseguire queste attività.

* [Creare un'immagine personalizzata da un file VHD usando PowerShell](devtest-lab-create-custom-image-from-vhd-using-powershell.md)
* [Caricare un file VHD nell'account di archiviazione del lab usando PowerShell](devtest-lab-upload-vhd-using-powershell.md)
* [Aggiungere un utente esterno a un lab usando PowerShell](devtest-lab-add-devtest-user.md#add-an-external-user-to-a-lab-using-powershell)
* [Creare un ruolo personalizzato lab tramite PowerShell](devtest-lab-grant-user-permissions-to-specific-lab-policies.md#creating-a-lab-custom-role-using-powershell)

### <a name="next-steps"></a>Passaggi successivi
* Informazioni su come creare un [repository Git privato](devtest-lab-add-artifact-repo.md) in cui verranno archiviati i modelli o script personalizzati.
* Esplorare i [modelli di Azure Resource Manager dalla raccolta di modelli di avvio rapido di Azure](https://github.com/Azure/azure-quickstart-templates).

