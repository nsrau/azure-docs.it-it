---
title: Creare un ruolo di Avere senza controller - Avere vFXT per Azure
description: Metodo per creare il ruolo Controllo degli accessi in base al ruolo richiesto senza una macchina virtuale controller del cluster
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: v-erkell
ms.openlocfilehash: 547a0e0ac5254408a4064d627ec4c1e7c354a433
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/31/2018
ms.locfileid: "50670028"
---
# <a name="create-the-avere-vfxt-cluster-runtime-access-role-without-a-controller"></a>Creare il ruolo di accesso del runtime del cluster Avere vFXT senza controller

Questo documento illustra un metodo per creare il ruolo di accesso dei nodi del cluster dalla riga di comando prima di creare la macchina virtuale controller del cluster. 

Per crearlo dal controller del cluster, leggere [Creare il ruolo di accesso dei nodi del cluster](avere-vfxt-deploy.md#create-the-cluster-node-access-role). L'immagine del controller include un file di prototipo del ruolo. È possibile aggiornare il file con l'ID sottoscrizione e usarlo per definire il ruolo in locale nella macchina virtuale controller.

## <a name="create-an-azure-rbac-role"></a>Creare un ruolo Controllo degli accessi in base al ruolo di Azure

Il sistema Avere vFXT usa il [controllo degli accessi in base al ruolo](https://docs.microsoft.com/azure/role-based-access-control/) per fornire ai nodi del cluster vFXT l'autorizzazione per eseguire le attività necessarie.  

Come parte del normale funzionamento del cluster vFXT, i singoli nodi vFXT devono eseguire operazioni come la lettura delle proprietà delle risorse di Azure, la gestione dell'archiviazione e il controllo delle impostazioni dell'interfaccia di rete degli altri nodi. 

Questo ruolo viene usato solo per i nodi vFXT e non per la macchina virtuale controller del cluster.  

Se si vuole creare il ruolo prima del controller, seguire questa procedura: 

1. Aprire Azure Cloud Shell nel portale di Azure o passare a [https://shell.azure.com](https://shell.azure.com).

1. Usare il comando dell'interfaccia della riga di comando di Azure per passare alla sottoscrizione vFXT:

   ```az account set --subscription YOUR_SUBSCRIPTION_ID```

1. Usare questi comandi per scaricare la definizione del ruolo dall'immagine del marketplace e modificarla. 

   ```bash
   wget -O- https://averedistribution.blob.core.windows.net/public/vfxtdistdoc.tgz | tar zxf - avere-cluster.json
   vi avere-cluster.json
   ``` 

4. Modificare il file per includere l'ID sottoscrizione ed eliminare la riga precedente. Salvare il file con il nome ``avere-cluster.json``.

   ![Editor di testo della console che mostra l'ID sottoscrizione e la riga da rimuovere selezionata per l'eliminazione](media/avere-vfxt-edit-role.png)

5. Creare il ruolo:  

   ```bash
   az role definition create --role-definition /avere-cluster.json
   ```

Quando si crea il cluster, specificare il nome del ruolo (in questo caso `avere-cluster`). Lo script di creazione del cluster assegna il ruolo ai nodi del cluster appena creati. 

## <a name="sample-cluster-node-runtime-role-definition"></a>Esempio di definizione del ruolo del runtime dei nodi del cluster

> [!IMPORTANT] 
> Questa definizione di esempio è stata creata da una versione del prodotto precedente a GA. Se la versione disponibile con la distribuzione del prodotto corrente è diversa, usare quella versione.

```json

{
    "AssignableScopes": [
        "/subscriptions/YOUR_SUBSCRIPTION_ID_GOES_HERE"
    ],
    "Name": "avere-cluster",
    "IsCustom": "true",
    "Description": "Avere cluster runtime role",
    "NotActions": [],
    "Actions": [
        "Microsoft.Compute/virtualMachines/read",
        "Microsoft.Network/networkInterfaces/read",
        "Microsoft.Network/networkInterfaces/write",
        "Microsoft.Network/virtualNetworks/subnets/read",
        "Microsoft.Network/virtualNetworks/subnets/join/action",
        "Microsoft.Network/networkSecurityGroups/join/action",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/storageAccounts/blobServices/containers/delete",
        "Microsoft.Storage/storageAccounts/blobServices/containers/read",
        "Microsoft.Storage/storageAccounts/blobServices/containers/write"
    ],
    "DataActions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete",
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read",
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write"
    ]
}

```