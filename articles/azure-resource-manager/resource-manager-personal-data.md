---
title: Dati personali di Azure Resource Manager | Microsoft Docs
description: Informazioni su come gestire i dati personali associati alle operazioni di Azure Resource Manager.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/14/2018
ms.author: tomfitz
ms.openlocfilehash: 71928be07080ed14fdcb93f33ea64d2572955b53
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/20/2018
ms.locfileid: "34358626"
---
# <a name="manage-personal-data-associated-with-azure-resource-manager"></a>Gestire i dati personali associati a Azure Resource Manager

Per evitare di esporre informazioni riservate, eliminare eventuali informazioni personali fornite in distribuzioni, gruppi di risorse o tag. In Azure Resource Manager è possibile eseguire operazioni che consentono di gestire i dati personali eventualmente forniti in distribuzioni, gruppi di risorse o tag.

[!INCLUDE [Handle personal data](../../includes/gdpr-intro-sentence.md)]

## <a name="delete-personal-data-in-deployment-history"></a>Eliminare i dati personali nella cronologia di distribuzione

Per le distribuzioni, Resource Manager conserva i valori dei parametri e i messaggi di stato nella cronologia di distribuzione. Questi valori sono salvati in modo permanente finché non si elimina la distribuzione dalla cronologia. Per vedere se sono stati forniti dati personali in questi valori, elencare le distribuzioni. Se si trovano dati personali, eliminare le distribuzioni dalla cronologia.

Per elencare le **distribuzioni** nella cronologia, usare:

* [List By Resource Group](/rest/api/resources/deployments/listbyresourcegroup)
* [Get-AzureRmResourceGroupDeployment](/powershell/module/azurerm.resources/Get-AzureRmResourceGroupDeployment)
* [az group deployment list](/cli/azure/group/deployment#az-group-deployment-list)

Per eliminare le **distribuzioni** dalla cronologia, usare:

* [Eliminazione](/rest/api/resources/deployments/delete)
* [Remove-AzureRmResourceGroupDeployment](/powershell/module/azurerm.resources/Remove-AzureRmResourceGroupDeployment)
* [az group deployment delete](/cli/azure/group/deployment#az-group-deployment-delete)

## <a name="delete-personal-data-in-resource-group-names"></a>Eliminare i dati personali nei nomi dei gruppi di risorse

Il nome del gruppo di risorse è salvato in modo permanente finché non si elimina il gruppo di risorse. Per vedere se sono stati forniti dati personali nei nomi, elencare i gruppi di risorse. Se si trovano dati personali, [spostare le risorse](resource-group-move-resources.md) in un nuovo gruppo di risorse ed eliminare il gruppo di risorse con i dati personali nel nome.

Per elencare i **gruppi di risorse**, usare:

* [Elenco](/rest/api/resources/resourcegroups/list)
* [Get-AzureRmResourceGroup](/powershell/module/azurerm.resources/Get-AzureRmResourceGroup)
* [az group list](/cli/azure/group#az-group-list)

Per eliminare i **gruppi di risorse**, usare:

* [Eliminazione](/rest/api/resources/resourcegroups/delete)
* [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/Remove-AzureRmResourceGroup)
* [az group delete](/cli/azure/group#az-group-delete)

## <a name="delete-personal-data-in-tags"></a>Eliminare i dati personali nei tag

I nomi e i valori dei tag sono salvati in modo permanente finché non si elimina o si modifica il tag. Per vedere se sono stati forniti dati personali nei tag, elencare i tag. Se si trovano dati personali, eliminare i tag.

Per elencare i **tag**, usare:

* [Elenco](/rest/api/resources/tags/list)
* [Get-AzureRmTag](/powershell/module/azurerm.tags/get-azurermtag)
* [az tag list](/cli/azure/tag#az-tag-list)

Per eliminare i **tag**, usare:

* [Eliminazione](/rest/api/resources/tags/delete)
* [Remove-AzureRmTag](/powershell/module/azurerm.tags/remove-azurermtag)
* [az tag delete](/cli/azure/tag#az-tag-delete)

## <a name="next-steps"></a>Passaggi successivi
* Per una panoramica di Azure Resource Manager, vedere [Panoramica di Azure Resource Manager](resource-group-overview.md).