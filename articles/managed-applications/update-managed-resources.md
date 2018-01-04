---
title: Aggiornare le risorse nelle applicazioni gestite di Azure | Microsoft Docs
description: Viene descritto come usare le risorse nel gruppo di risorse gestite per un'applicazione gestita di Azure.
services: managed-applications
author: tfitzmac
manager: timlt
ms.service: managed-applications
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 10/26/2017
ms.author: tomfitz
ms.openlocfilehash: d3c955d7be0e7e6d45751c0e685bad498e524d94
ms.sourcegitcommit: 4ac89872f4c86c612a71eb7ec30b755e7df89722
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/07/2017
---
# <a name="work-with-resources-in-the-managed-resource-group-for-azure-managed-application"></a>Usare le risorse nel gruppo di risorse gestite per un'applicazione gestita di Azure

In questo articolo viene descritto come aggiornare le risorse distribuite in un'applicazione gestita. L'editore di un'applicazione gestita ha accesso alle risorse nel gruppo di risorse gestite. Per aggiornare queste risorse, è necessario trovare il gruppo di risorse gestite associato a un'applicazione gestita e quindi accedere alla risorsa in tale gruppo.

Questo articolo presuppone la distribuzione dell'applicazione gestita nel progetto di esempio [Applicazione Web gestita (IaaS) con i servizi di gestione di Azure](https://github.com/Azure/azure-managedapp-samples/tree/master/samples/201-managed-web-app). L'applicazione gestita in questione include una macchina virtuale **Standard_D1_v2**. Se l'applicazione gestita non è stata distribuita, è possibile usare comunque questo articolo per acquisire familiarità con i passaggi necessari per eseguire l'aggiornamento di un gruppo di risorse gestite.

La figura seguente mostra l'applicazione gestita distribuita.

![Applicazione gestita distribuita](./media/update-managed-resources/deployed.png)

In questo articolo viene usata l'interfaccia della riga di comando di Azure per:

* Individuare l'applicazione gestita
* Individuare il gruppo di risorse gestite
* Individuare la risorsa o le risorse della macchina virtuale nel gruppo di risorse gestite
* Modificare le dimensioni della macchina virtuale (a dimensioni inferiori se non viene usata oppure a dimensioni superiori a supporto di un carico più elevato)
* Assegnare al gruppo di risorse gestite un criterio che specifichi le posizioni consentite

## <a name="get-managed-application-and-managed-resource-group"></a>Ottenere l'applicazione gestita e il gruppo di risorse gestite

Per ottenere le applicazioni gestite in un gruppo di risorse, usare:

```azurecli-interactive
az managedapp list --query "[?contains(resourceGroup,'DemoApp')]"
```

Per ottenere l'ID del gruppo di risorse gestite, usare:

```azurecli-interactive
az managedapp list --query "[?contains(resourceGroup,'DemoApp')].{ managedResourceGroup:managedResourceGroupId }"
```

## <a name="resize-vms-in-managed-resource-group"></a>Ridimensionare le macchine virtuali nel gruppo di risorse gestite

Per visualizzare le macchine virtuali nel gruppo di risorse gestite, specificare il nome del gruppo di risorse gestite.

```azurecli-interactive
az vm list -g DemoApp6zkevchqk7sfq --query "[].{VMName:name,OSType:storageProfile.osDisk.osType,VMSize:hardwareProfile.vmSize}"
```

Per aggiornare le dimensioni delle macchine virtuali, usare:

```azurecli-interactive
az vm resize --size Standard_D2_v2 --ids $(az vm list -g DemoApp6zkevchqk7sfq --query "[].id" -o tsv)
```

Al termine dell'operazione, verificare che l'applicazione sia in esecuzione in Standard D2 v2.

![Applicazione gestita con Standard D2 v2](./media/update-managed-resources/upgraded.png)

## <a name="apply-policy-to-managed-resource-group"></a>Applicare il criterio al gruppo di risorse gestite

Ottenere il gruppo di risorse gestite e assegnare un criterio a tale ambito. Il criterio **e56962a6-4747-49cd-b67b-bf8b01975c4c** è un criterio predefinito per specificare le posizioni consentite.

```azurecli-interactive
managedGroup=$(az managedapp show --name <app-name> --resource-group DemoApp --query managedResourceGroupId --output tsv)

az policy assignment create --name locationAssignment --policy e56962a6-4747-49cd-b67b-bf8b01975c4c --scope $managedGroup --params '{
                            "listofallowedLocations": {
                                "value": [
                                    "northeurope",
                                    "westeurope"
                                ]
                            }
                        }'
```

Per visualizzare le posizioni consentite, usare:

```azurecli-interactive
az policy assignment show --name locationAssignment --scope $managedGroup --query parameters.listofallowedLocations.value
```

L'assegnazione del criterio viene visualizzato nel portale.

![Visualizzare l'assegnazione di un criterio](./media/update-managed-resources/assignment.png)

## <a name="next-steps"></a>Passaggi successivi

* Per un'introduzione alle applicazioni gestite, vedere [Panoramica delle applicazioni gestite di Azure](overview.md).
* Per i progetti di esempio, vedere [Progetti di esempio per applicazioni gestite di Azure](sample-projects.md).
