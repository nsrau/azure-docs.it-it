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
ms.openlocfilehash: 01e2b1608d55d7852db947181482d34d6d8010d2
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2019
ms.locfileid: "55215046"
---
# <a name="manage-personal-data-associated-with-azure-resource-manager"></a>Gestire i dati personali associati a Azure Resource Manager

Per evitare di esporre informazioni riservate, eliminare eventuali informazioni personali fornite in distribuzioni, gruppi di risorse o tag. In Azure Resource Manager è possibile eseguire operazioni che consentono di gestire i dati personali eventualmente forniti in distribuzioni, gruppi di risorse o tag.

[!INCLUDE [Handle personal data](../../includes/gdpr-intro-sentence.md)]

## <a name="delete-personal-data-in-deployment-history"></a>Eliminare i dati personali nella cronologia di distribuzione

Per le distribuzioni, Resource Manager conserva i valori dei parametri e i messaggi di stato nella cronologia di distribuzione. Questi valori sono salvati in modo permanente finché non si elimina la distribuzione dalla cronologia. Per vedere se sono stati forniti dati personali in questi valori, elencare le distribuzioni. Se si trovano dati personali, eliminare le distribuzioni dalla cronologia.

Per elencare le **distribuzioni** nella cronologia, usare:

* [List By Resource Group](/rest/api/resources/deployments/listbyresourcegroup)
* [Get-AzResourceGroupDeployment](/powershell/module/az.resources/Get-AzResourceGroupDeployment)
* [az group deployment list](/cli/azure/group/deployment#az-group-deployment-list)

Per eliminare le **distribuzioni** dalla cronologia, usare:

* [Elimina](/rest/api/resources/deployments/delete)
* [Remove-AzResourceGroupDeployment](/powershell/module/az.resources/Remove-AzResourceGroupDeployment)
* [az group deployment delete](/cli/azure/group/deployment#az-group-deployment-delete)

## <a name="delete-personal-data-in-resource-group-names"></a>Eliminare i dati personali nei nomi dei gruppi di risorse

Il nome del gruppo di risorse è salvato in modo permanente finché non si elimina il gruppo di risorse. Per vedere se sono stati forniti dati personali nei nomi, elencare i gruppi di risorse. Se si trovano dati personali, [spostare le risorse](resource-group-move-resources.md) in un nuovo gruppo di risorse ed eliminare il gruppo di risorse con i dati personali nel nome.

Per elencare i **gruppi di risorse**, usare:

* [Elenco](/rest/api/resources/resourcegroups/list)
* [Get-AzResourceGroup](/powershell/module/az.resources/Get-AzResourceGroup)
* [az group list](/cli/azure/group#az-group-list)

Per eliminare i **gruppi di risorse**, usare:

* [Elimina](/rest/api/resources/resourcegroups/delete)
* [Remove-AzResourceGroup](/powershell/module/az.resources/Remove-AzResourceGroup)
* [az group delete](/cli/azure/group#az-group-delete)

## <a name="delete-personal-data-in-tags"></a>Eliminare i dati personali nei tag

I nomi e i valori dei tag sono salvati in modo permanente finché non si elimina o si modifica il tag. Per vedere se sono stati forniti dati personali nei tag, elencare i tag. Se si trovano dati personali, eliminare i tag.

Per elencare i **tag**, usare:

* [Elenco](/rest/api/resources/tags/list)
* [Get-AzTag](/powershell/module/az.resources/Get-AzTag)
* [az tag list](/cli/azure/tag#az-tag-list)

Per eliminare i **tag**, usare:

* [Elimina](/rest/api/resources/tags/delete)
* [Remove-AzTag](/powershell/module/az.resources/Remove-AzTag)
* [az tag delete](/cli/azure/tag#az-tag-delete)

## <a name="next-steps"></a>Passaggi successivi
* Per una panoramica di Azure Resource Manager, vedere [Panoramica di Azure Resource Manager](resource-group-overview.md).