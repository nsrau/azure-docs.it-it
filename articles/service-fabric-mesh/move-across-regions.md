---
title: Spostare un'applicazione Di tipo Mesh di Service Fabric in un'altra area
description: È possibile spostare le risorse di Service Fabric Mesh distribuendo una copia del modello corrente in una nuova area di Azure.You can move Service Fabric Mesh resources by deploying a copy of your current template to a new Azure region.
author: erikadoyle
ms.author: edoyle
ms.topic: how-to
ms.date: 01/14/2020
ms.custom: subject-moving-resources
ms.openlocfilehash: 376808a6d8f61d4dc03d17061323a473d48053a6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76908163"
---
# <a name="move-a-service-fabric-mesh-application-to-another-azure-region"></a>Spostare un'applicazione mesh di Service Fabric in un'altra area di AzureMove a Service Fabric Mesh application to another Azure region

Questo articolo descrive come spostare l'applicazione Mesh di Service Fabric e le relative risorse in un'area di Azure diversa. È possibile spostare le risorse in un'altra area per diversi motivi. Ad esempio, in risposta a interruzioni, per ottenere funzionalità o servizi disponibili solo in aree specifiche, per soddisfare i requisiti di policy e governance interni o in risposta ai requisiti di pianificazione della capacità.

 Service Fabric Mesh non supporta la possibilità di spostare direttamente le risorse tra le aree di [Azure.Service Fabric Mesh does not support](../azure-resource-manager/management/region-move-support.md#microsoftservicefabricmesh) the ability to directly move resources across Azure regions. Tuttavia, è possibile spostare le risorse indirettamente distribuendo una copia del modello corrente di Azure Resource Manager nella nuova area di destinazione e quindi reindirizzando il traffico in ingresso e le dipendenze all'applicazione Mesh di Service Fabric appena creata.

## <a name="prerequisites"></a>Prerequisiti

* Controller in ingresso (ad esempio [Gateway applicazione](https://docs.microsoft.com/azure/application-gateway/)) da utilizzare come intermediario per il routing del traffico tra i client e l'applicazione Mamesh di Service Fabric
* Disponibilità di Service Fabric Mesh (anteprima)`westus` `eastus`nell'area di Azure di destinazione ( , , o `westeurope`)

## <a name="prepare"></a>Preparazione

1. Creare uno "snapshot" dello stato corrente dell'applicazione Mesh di Service Fabric esportando il modello e i parametri di Azure Resource Manager dalla distribuzione più recente. A tale scopo, seguire i passaggi descritti in [Esportare](../azure-resource-manager/templates/export-template-portal.md#export-template-after-deployment) il modello dopo la distribuzione usando il portale di Azure.To do this, follow the steps in Export template after deployment using the Azure portal. È anche possibile usare [l'interfaccia della riga di comando](../azure-resource-manager/management/manage-resource-groups-cli.md#export-resource-groups-to-templates)di Azure , Azure [PowerShell](../azure-resource-manager/management/manage-resource-groups-powershell.md#export-resource-groups-to-templates)o [l'API REST](https://docs.microsoft.com/rest/api/resources/resourcegroups/exporttemplate).

2. Se applicabile, [esportare altre risorse nello stesso gruppo](https://docs.microsoft.com/azure/azure-resource-manager/templates/export-template-portal#export-template-from-a-resource-group) di risorse per la ridistribuzione nell'area di destinazione.

3. Esaminare (e modificare, se necessario) il modello esportato per assicurarsi che i valori di proprietà esistenti siano quelli che si desidera utilizzare nell'area di destinazione. Il `location` nuovo (area di Azure) è un parametro che verrà fornito durante la ridistribuzione.

## <a name="move"></a>Spostamento

1. Creare un nuovo gruppo di risorse (o usarne uno esistente) nell'area di destinazione.

2. Con il modello esportato seguire i passaggi descritti in [Distribuire risorse da un modello personalizzato](https://docs.microsoft.com/azure/azure-resource-manager/templates/deploy-portal#deploy-resources-from-custom-template) usando il portale di Azure.With your exported template, follow the steps in Deploy resources from custom template using the Azure portal. È anche possibile usare [l'interfaccia della riga di comando](https://docs.microsoft.com/azure/azure-resource-manager/templates/deploy-cli)di Azure , Azure [PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/templates/deploy-powershell)o [l'API REST](https://docs.microsoft.com/azure/azure-resource-manager/templates/deploy-rest).

3. Per istruzioni sullo spostamento di risorse correlate, ad esempio gli account di archiviazione di [Azure,](../storage/common/storage-account-move.md)fare riferimento alle linee guida per i singoli servizi elencati nell'argomento Spostamento di risorse di [Azure tra aree.](../azure-resource-manager/management/move-region.md)

## <a name="verify"></a>Verifica

1. Al termine della distribuzione, testare gli endpoint dell'applicazione per verificare la funzionalità dell'applicazione.

2. È inoltre possibile verificare lo stato dell'applicazione controllando lo stato dell'applicazione ([az mesh app show](https://docs.microsoft.com/cli/azure/ext/mesh/mesh/app?view=azure-cli-latest#ext-mesh-az-mesh-app-show)) ed esaminando i log dell'applicazione e i comandi ( az mesh[code-package-log](https://docs.microsoft.com/cli/azure/ext/mesh/mesh/code-package-log?view=azure-cli-latest)) utilizzando l'interfaccia della riga [di comando di Azure Service Fabric Mesh](https://docs.microsoft.com/azure/service-fabric-mesh/service-fabric-mesh-quickstart-deploy-container#set-up-service-fabric-mesh-cli).

## <a name="commit"></a>Commit

Dopo aver confermato la funzionalità equivalente dell'applicazione Service Fabric Mesh nell'area di destinazione, configurare il controller in ingresso, ad esempio [Gateway applicazione](../application-gateway/redirect-overview.md), per reindirizzare il traffico alla nuova applicazione.

## <a name="clean-up-source-resources"></a>Pulire le risorse di origineClean up source resources

Per completare lo spostamento dell'applicazione Maing FLeSi. [delete the source application and/or parent resource group](../azure-resource-manager/management/delete-resource-group.md)

## <a name="next-steps"></a>Passaggi successivi

* [Spostare le risorse di Azure tra aree](../azure-resource-manager/management/move-region.md)
* [Supporto per lo spostamento di risorse di Azure tra aree](../azure-resource-manager/management/region-move-support.md)
* [Spostare le risorse in un altro gruppo di risorse o un'altra sottoscrizione](../azure-resource-manager/management/move-resource-group-and-subscription.md)
* [Supporto per lo spostamento delle risorse](../azure-resource-manager/management/move-support-resources.md
)
