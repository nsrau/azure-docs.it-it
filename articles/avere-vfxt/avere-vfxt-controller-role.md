---
title: Ruolo di controllo degli accessi personalizzato - Avere vFXT per Azure
description: Come creare un ruolo personalizzato di accesso per il controller del cluster Avere vFXT
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 01/29/2019
ms.author: v-erkell
ms.openlocfilehash: c408efa7ca01928e25ac03f5ca63d0aef7d88839
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57770039"
---
# <a name="customized-controller-access-role"></a>Ruolo di controllo degli accessi personalizzato

Il controller del cluster Avere vFXT di Azure usa un'identità gestita e il controllo di accesso basato sui ruoli (RBAC) per consentirgli di creare e gestire il cluster. 

Per impostazione predefinita, al controller del cluster è assegnato il [ruolo di proprietario predefinito](../role-based-access-control/built-in-roles.md#owner). Inoltre, l'accesso del controller ha come ambito il gruppo di risorse: non è possibile modificare gli elementi all'esterno del gruppo di risorse del cluster.

Questo articolo illustra come creare il proprio ruolo di accesso per il controller del cluster invece di usare l'impostazione predefinita. 

## <a name="edit-the-role-prototype"></a>Modificare il prototipo di ruolo

Iniziare dal prototipo di ruolo disponibile all'indirizzo <https://github.com/Azure/Avere/blob/master/src/vfxt/src/roles/AvereContributor.txt>.

```json
{
  "AssignableScopes": [
    "/subscriptions/YOUR SUBSCRIPTION ID HERE"
  ],
  "Name": "Avere custom contributor",
  "IsCustom": true,
  "Description": "Can create and manage an Avere vFXT cluster.",
  "NotActions": [],
  "Actions": [
    "Microsoft.Authorization/*/read",
    "Microsoft.Compute/*/read",
    "Microsoft.Compute/availabilitySets/*",
    "Microsoft.Compute/virtualMachines/*",
    "Microsoft.Compute/disks/*",
    "Microsoft.Insights/alertRules/*",
    "Microsoft.Network/*/read",
    "Microsoft.Network/networkInterfaces/*",
    "Microsoft.Network/virtualNetworks/subnets/join/action",
    "Microsoft.Network/virtualNetworks/subnets/read",
    "Microsoft.Resources/deployments/*",
    "Microsoft.Resources/subscriptions/resourceGroups/read",
    "Microsoft.Resources/subscriptions/resourceGroups/resources/read",
    "Microsoft.Storage/*/read",
    "Microsoft.Storage/storageAccounts/listKeys/action",
    "Microsoft.Support/*"
  ],
  "DataActions": []
}
```

Aggiungere l'ID sottoscrizione per la distribuzione di Avere vFXT in Azure nell'istruzione AssignableScopes. Personalizzare il nome e aggiungere o modificare le definizioni in base alle esigenze. 

Prestare attenzione se l'utente limita i propri privilegi. La creazione del cluster può non riuscire se il controller non dispone di accesso sufficienti. 

Per comprendere di quali privilegi il controller del cluster necessita per creare un cluster, [aprire un ticket di supporto](avere-vfxt-open-ticket.md#open-a-support-ticket-for-your-avere-vfxt). 

Salvare la definizione del ruolo personalizzato come un file JSON. 

## <a name="define-the-role"></a>Definire il ruolo 

Seguire i passaggi seguenti per aggiungere la definizione del ruolo personalizzato alla sottoscrizione. 

1. Aprire Azure Cloud Shell nel portale di Azure o passare a [https://shell.azure.com](https://shell.azure.com).

1. Usare il comando dell'interfaccia della riga di comando di Azure per passare alla sottoscrizione vFXT:

   ```azurecli
   az account set --subscription YOUR_SUBSCRIPTION_ID
   ```

1. Creare il ruolo:

   ```azurecli
   az role definition create --role-definition /avere-contributor-custom.json
   ```

   Usare il nome file e il percorso al posto di ```/avere-contributor-custom.json``` in questo esempio. 

Salvare l'output del comando di definizione di ruolo: contiene l'identificatore del ruolo che è necessario fornire per il modello di creazione del cluster. 

## <a name="find-the-role-id"></a>Trovare l'ID del ruolo

Il modello di distribuzione Avere vFXT necessita che l'identificatore univoco globale del ruolo (GUID) assegni il controller di un ruolo personalizzato. 

Il GUID del ruolo è una stringa di 32 caratteri in questo formato: 8e3af657-a8ff-443c-a75c-2fe8c4bcb635

Per cercare il GUID del ruolo, usare questo comando con il nome del ruolo nel parametro ```--name```.

```azurecli
az role definition list --query '[*].{roleName:roleName, name:name}' -o table --name 'YOUR ROLE NAME'
```
Immettere la stringa nel campo **ID del ruolo di creazione del cluster di Avere** quando si distribuisce Avere vFXT per Azure.

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come distribuire Avere vFXT per Azure in [distribuire il cluster vFXT](avere-vfxt-deploy.md)
