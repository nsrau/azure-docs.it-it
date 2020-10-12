---
title: Creare un'entità servizio di onboarding abilitata per Azure Arc (anteprima)
services: azure-arc
ms.service: azure-arc
ms.date: 05/19/2020
ms.topic: article
author: mlearned
ms.author: mlearned
description: "Creare un'entità servizio di onboarding abilitata per Azure Arc "
keywords: Kubernetes, Arc, Azure, contenitori
ms.openlocfilehash: 02689dba32c8cc91e4a4a4de4dee98bc990b4dd6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87050080"
---
# <a name="create-an-azure-arc-enabled-onboarding-service-principal-preview"></a>Creare un'entità servizio di onboarding abilitata per Azure Arc (anteprima)

## <a name="overview"></a>Panoramica

Per l'onboarding di cluster Kubernetes in Azure Arc è possibile usare entità servizio con un'assegnazione di ruolo con privilegi limitati. Questo approccio è utile nelle pipeline di integrazione continua e distribuzione continua (CI/CD), come Azure Pipelines e GitHub Actions.

I passaggi seguenti costituiscono una procedura dettagliata sull'uso delle entità servizio per l'onboarding di cluster Kubernetes in Azure Arc.

## <a name="create-a-new-service-principal"></a>Creare una nuova entità servizio

Creare una nuova entità servizio con un nome informativo. Tenere presente che questo nome deve essere univoco per il tenant di Azure Active Directory:

```console
az ad sp create-for-RBAC --skip-assignment --name "https://azure-arc-for-k8s-onboarding"
```

**Output:**

```console
{
  "appId": "22cc2695-54b9-49c1-9a73-2269592103d8",
  "displayName": "azure-arc-for-k8s-onboarding",
  "name": "https://azure-arc-for-k8s-onboarding",
  "password": "09d3a928-b223-4dfe-80e8-fed13baa3b3d",
  "tenant": "72f988bf-86f1-41af-91ab-2d7cd011db47"
}
```

## <a name="assign-permissions"></a>Assegnare le autorizzazioni

Dopo aver creato la nuova entità servizio, assegnare il ruolo "cluster Kubernetes-Azure Arc onboarding" all'entità appena creata. Si tratta di un ruolo predefinito di Azure con autorizzazioni limitate che consente all'entità solo di registrare i cluster in Azure. L'entità non può aggiornare, eliminare o modificare altri cluster o risorse all'interno della sottoscrizione.

Considerate le capacità limitate, i clienti possono riutilizzare facilmente questa entità per l'onboarding di più cluster.

È possibile limitare ulteriormente le autorizzazioni passando l'argomento di `--scope` appropriato durante l'assegnazione del ruolo. In questo modo i clienti possono limitare la registrazione del cluster. Gli scenari seguenti sono supportati dai diversi parametri di `--scope`:

| Risorsa  | Argomento di `scope`| Effetto |
| ------------- | ------------- | ------------- |
| Sottoscrizione | `--scope /subscriptions/0b1f6471-1bf0-4dda-aec3-111122223333` | L'entità servizio può registrare qualsiasi cluster in un gruppo di risorse esistente nella sottoscrizione specificata |
| Gruppo di risorse | `--scope /subscriptions/0b1f6471-1bf0-4dda-aec3-111122223333/resourceGroups/myGroup`  | L'entità servizio può __solo__ registrare cluster nel gruppo di risorse `myGroup` |

```console
az role assignment create \
    --role 34e09817-6cbe-4d01-b1a2-e0eac5743d41 \      # this is the id for the built-in role
    --assignee 22cc2695-54b9-49c1-9a73-2269592103d8 \  # use the appId from the new SP
    --scope /subscriptions/<<SUBSCRIPTION_ID>>         # apply the appropriate scope
```

**Output:**

```console
{
  "canDelegate": null,
  "id": "/subscriptions/<<SUBSCRIPTION_ID>>/providers/Microsoft.Authorization/roleAssignments/fbd819a9-01e8-486b-9eb9-f177ba400ba6",
  "name": "fbd819a9-01e8-486b-9eb9-f177ba400ba6",
  "principalId": "ddb0ddb4-ba84-4cde-b936-affc272a4b90",
  "principalType": "ServicePrincipal",
  "roleDefinitionId": "/subscriptions/<<SUBSCRIPTION_ID>>/providers/Microsoft.Authorization/roleDefinitions/34e09817-6cbe-4d01-b1a2-e0eac5743d41",
  "scope": "/subscriptions/<<SUBSCRIPTION_ID>>",
  "type": "Microsoft.Authorization/roleAssignments"
}
```

## <a name="use-service-principal-with-the-azure-cli"></a>Usare l'entità servizio con l'interfaccia della riga di comando di Azure

Fare riferimento all'entità servizio appena creata:

```console
az login --service-principal -u mySpnClientId -p mySpnClientSecret --tenant myTenantID
az connectedk8s connect -n myConnectedClusterName -g myResoureGroupName
```

## <a name="next-steps"></a>Passaggi successivi

* [Usare Criteri di Azure per gestire la configurazione del cluster](./use-azure-policy.md)
