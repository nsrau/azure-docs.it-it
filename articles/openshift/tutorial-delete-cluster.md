---
title: Esercitazione - Eliminare un cluster Azure Red Hat OpenShift | Microsoft Docs
description: Questa esercitazione illustra come eliminare un cluster Azure Red Hat OpenShift usando l'interfaccia della riga di comando di Azure
services: container-service
author: tylermsft
ms.author: twhitney
manager: jeconnoc
ms.topic: tutorial
ms.service: openshift
ms.date: 05/06/2019
ms.openlocfilehash: 679be645f3c97d30df65c9f410d286d243bca73b
ms.sourcegitcommit: e6d53649bfb37d01335b6bcfb9de88ac50af23bd
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/09/2019
ms.locfileid: "65466565"
---
# <a name="tutorial-delete-an-azure-red-hat-openshift-cluster"></a>Esercitazione: Eliminare un cluster di Azure Red Hat OpenShift

L'esercitazione è terminata. La procedura seguente illustra come eliminare il cluster di esempio e le risorse associate quando si termina il test, in modo da non ricevere addebiti per risorse inutilizzate.

Nella terza parte della serie si apprenderà come:

> [!div class="checklist"]
> * Eliminare un cluster di Azure Red Hat OpenShift

In questa serie di esercitazioni si apprenderà come:
> [!div class="checklist"]
> * [Creare un cluster di Azure Red Hat OpenShift](tutorial-create-cluster.md)
> * [Ridimensionare un cluster di Azure Red Hat OpenShift](tutorial-scale-cluster.md)
> * Eliminare un cluster di Azure Red Hat OpenShift

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare questa esercitazione:

* Creare un cluster seguendo l'esercitazione [Creare un cluster di Azure Red Hat OpenShift](tutorial-create-cluster.md).

## <a name="step-1-sign-in-to-azure"></a>Passaggio 1: Accedere ad Azure

Se si esegue l'interfaccia della riga di comando di Azure in locale, eseguire `az login` per accedere ad Azure.

```bash
az login
```

Se si ha accesso a più sottoscrizioni, eseguire `az account set -s {subscription ID}` sostituendo `{subscription ID}` con la sottoscrizione che si vuole usare.

## <a name="step-2-delete-the-cluster"></a>Passaggio 2: Eliminare il cluster

Aprire un terminale Bash e impostare la variabile CLUSTER_NAME per il nome del cluster:

```bash
CLUSTER_NAME=yourclustername
```

Ora eliminare il cluster:

```bash
az openshift delete --resource-group $CLUSTER_NAME --name $CLUSTER_NAME
```

Verrà chiesto se si vuole eliminare il cluster. Dopo aver confermato con `y`, l'eliminazione del cluster richiederà diversi minuti. Al termine del comando, l'intero gruppo di risorse e le risorse al suo interno, tra cui il cluster, verranno eliminate.

## <a name="deleting-a-cluster-using-the-azure-portal"></a>Eliminazione di un cluster con il portale di Azure

In alternativa, è possibile eliminare il gruppo di risorse associato del cluster tramite il portale di Azure online. Il nome del gruppo di risorse è uguale a quello del cluster.

Attualmente, la risorsa `Microsoft.ContainerService/openShiftManagedClusters` creata con il cluster è nascosta nel portale di Azure. Nella vista `Resource group` spuntare la casella `Show hidden types` per visualizzare il gruppo di risorse.

![Screenshot della casella di controllo di tipo nascosto](./media/aro-portal-hidden-type.png)

L'eliminazione del gruppo di risorse comporterà l'eliminazione di tutte le risorse correlate create con un cluster di Azure Red Hat OpenShift.

## <a name="next-steps"></a>Passaggi successivi

In questa parte dell'esercitazione si è appreso come:
> [!div class="checklist"]
> * Eliminare un cluster di Azure Red Hat OpenShift

Altre informazioni sull'uso di OpenShift sono disponibili nella [documentazione ufficiale di Red Hat OpenShift](https://docs.openshift.com/aro/welcome/index.html)