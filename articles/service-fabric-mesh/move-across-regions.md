---
title: Spostare un'applicazione Service Fabric mesh in un'altra area
description: È possibile spostare Service Fabric risorse mesh distribuendo una copia del modello corrente in una nuova area di Azure.
author: erikadoyle
ms.author: edoyle
ms.topic: how-to
ms.date: 01/14/2020
ms.custom: subject-moving-resources
ms.openlocfilehash: c842a065f108a924c6bffd70d6c2edbbd31b6dff
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86260154"
---
# <a name="move-a-service-fabric-mesh-application-to-another-azure-region"></a>Spostare un'applicazione Service Fabric mesh in un'altra area di Azure

Questo articolo descrive come spostare l'applicazione Service Fabric mesh e le relative risorse in un'area di Azure diversa. È possibile spostare le risorse in un'altra area per diversi motivi. Ad esempio, in risposta a interruzioni, per ottenere funzionalità o servizi disponibili solo in aree specifiche, per soddisfare i requisiti di governance e criteri interni oppure in risposta ai requisiti della pianificazione della capacità.

 [Service Fabric mesh non supporta](../azure-resource-manager/management/region-move-support.md#microsoftservicefabricmesh) la possibilità di spostare direttamente le risorse tra le aree di Azure. Tuttavia, è possibile spostare le risorse indirettamente distribuendo una copia del modello di Azure Resource Manager corrente nella nuova area di destinazione e quindi reindirizzando il traffico in ingresso e le dipendenze all'applicazione Service Fabric mesh appena creata.

## <a name="prerequisites"></a>Prerequisiti

* Il controller di ingresso, ad esempio il [gateway applicazione](../application-gateway/index.yml), funge da intermediario per il routing del traffico tra i client e l'applicazione Service Fabric mesh
* Disponibilità di Service Fabric mesh (anteprima) nell'area di Azure di destinazione ( `westus` , `eastus` o `westeurope` )

## <a name="prepare"></a>Preparazione

1. Eseguire uno "snapshot" dello stato corrente dell'applicazione Service Fabric mesh esportando il modello di Azure Resource Manager e i parametri dalla distribuzione più recente. A tale scopo, seguire la procedura descritta in [esportare il modello dopo la distribuzione](../azure-resource-manager/templates/export-template-portal.md#export-template-after-deployment) usando il portale di Azure. È anche possibile usare l'interfaccia della riga di comando di [Azure](../azure-resource-manager/management/manage-resource-groups-cli.md#export-resource-groups-to-templates), [Azure PowerShell](../azure-resource-manager/management/manage-resource-groups-powershell.md#export-resource-groups-to-templates)o l' [API REST](/rest/api/resources/resourcegroups/exporttemplate).

2. Se applicabile, [esportare altre risorse nello stesso gruppo di risorse](../azure-resource-manager/templates/export-template-portal.md#export-template-from-a-resource-group) per la ridistribuzione nell'area di destinazione.

3. Esaminare (e modificare, se necessario) il modello esportato per assicurarsi che i valori di proprietà esistenti siano quelli che si vuole usare nell'area di destinazione. La nuova `location` (area di Azure) è un parametro che verrà fornito durante la ridistribuzione.

## <a name="move"></a>Spostamento

1. Creare un nuovo gruppo di risorse (o utilizzarne uno esistente) nell'area di destinazione.

2. Con il modello esportato, seguire la procedura descritta in [distribuire risorse da un modello personalizzato](../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template) usando il portale di Azure. È anche possibile usare l'interfaccia della riga di comando di [Azure](../azure-resource-manager/templates/deploy-cli.md), [Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md)o l' [API REST](../azure-resource-manager/templates/deploy-rest.md).

3. Per informazioni aggiuntive sullo stato di trasferimento di risorse correlate, ad esempio gli [account di archiviazione di Azure](../storage/common/storage-account-move.md), fare riferimento alle linee guida per i singoli servizi elencati nell'argomento trasferimento di risorse di [Azure tra aree](../azure-resource-manager/management/move-region.md).

## <a name="verify"></a>Verifica

1. Al termine della distribuzione, testare gli endpoint dell'applicazione per verificare la funzionalità dell'applicazione.

2. È anche possibile verificare lo stato dell'applicazione controllando lo stato dell'applicazione ([AZ mesh App Show](/cli/azure/ext/mesh/mesh/app?view=azure-cli-latest#ext-mesh-az-mesh-app-show)) e esaminando i comandi log applicazioni e ([AZ mesh code-package-log](/cli/azure/ext/mesh/mesh/code-package-log?view=azure-cli-latest)) usando l'interfaccia della riga di comando di [Azure Service Fabric mesh](./service-fabric-mesh-quickstart-deploy-container.md#set-up-service-fabric-mesh-cli).

## <a name="commit"></a>Commit

Dopo aver confermato la funzionalità equivalente dell'applicazione Service Fabric Mesh nell'area di destinazione, configurare il controller di ingresso (ad esempio, il [gateway applicazione](../application-gateway/redirect-overview.md)) per reindirizzare il traffico alla nuova applicazione.

## <a name="clean-up-source-resources"></a>Pulire le risorse di origine

Per completare lo spostamento dell'applicazione Service Fabric mesh, [eliminare l'applicazione di origine e/o il gruppo di risorse padre](../azure-resource-manager/management/delete-resource-group.md).

## <a name="next-steps"></a>Passaggi successivi

* [Spostare le risorse di Azure tra aree](../azure-resource-manager/management/move-region.md)
* [Supporto per lo trasferimento di risorse di Azure tra aree](../azure-resource-manager/management/region-move-support.md)
* [Spostare le risorse in un nuovo gruppo di risorse o una nuova sottoscrizione](../azure-resource-manager/management/move-resource-group-and-subscription.md)
* [Supporto delle operazioni di spostamento per le risorse](../azure-resource-manager/management/move-support-resources.md
)
