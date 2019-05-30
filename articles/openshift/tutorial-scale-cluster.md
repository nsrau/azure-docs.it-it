---
title: Esercitazione - Ridimensionare un cluster Azure Red Hat OpenShift | Microsoft Docs
description: Informazioni su come ridimensionare un cluster Microsoft Azure Red Hat OpenShift usando l'interfaccia della riga di comando di Azure
services: container-service
author: jimzim
ms.author: jzim
manager: jeconnoc
ms.topic: tutorial
ms.service: openshift
ms.date: 05/06/2019
ms.openlocfilehash: b25e17e7064006a1421142dfcd32997cb4426e8e
ms.sourcegitcommit: 009334a842d08b1c83ee183b5830092e067f4374
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/29/2019
ms.locfileid: "66305975"
---
# <a name="tutorial-scale-an-azure-red-hat-openshift-cluster"></a>Esercitazione: Ridimensionare un cluster di Azure Red Hat OpenShift

Questa è la seconda di una serie di esercitazioni. Si apprenderà come creare un cluster Microsoft Azure Red Hat OpenShift usando l'interfaccia della riga di comando di Azure, come ridimensionarlo e quindi eliminarlo per pulire le risorse.

Nella seconda parte della serie si apprenderà come:

> [!div class="checklist"]
> * Ridimensionare un cluster Red Hat OpenShift

In questa serie di esercitazioni si apprenderà come:
> [!div class="checklist"]
> * [Creare un cluster di Azure Red Hat OpenShift](tutorial-create-cluster.md)
> * Ridimensionare un cluster di Azure Red Hat OpenShift
> * [Eliminare un cluster di Azure Red Hat OpenShift](tutorial-delete-cluster.md)

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare questa esercitazione:

* Creare un cluster seguendo l'esercitazione [Creare un cluster di Azure Red Hat OpenShift](tutorial-create-cluster.md).

## <a name="step-1-sign-in-to-azure"></a>Passaggio 1: Accedere ad Azure

Se si esegue l'interfaccia della riga di comando di Azure in locale, eseguire `az login` per accedere ad Azure.

```bash
az login
```

Se si ha accesso a più sottoscrizioni, eseguire `az account set -s {subscription ID}` sostituendo `{subscription ID}` con la sottoscrizione che si vuole usare.

## <a name="step-2-scale-the-cluster-with-additional-nodes"></a>Passaggio 2: Ridimensionare il cluster con nodi aggiuntivi

Da un terminale Bash impostare la variabile CLUSTER_NAME per il nome del cluster:

```bash
CLUSTER_NAME=yourclustername
```

A questo punto è possibile ridimensionare il cluster a cinque nodi tramite l'interfaccia della riga di comando di Azure:

```bash
az openshift scale --resource-group $CLUSTER_NAME --name $CLUSTER_NAME --compute-count 5
```

Dopo alcuni minuti, `az openshift scale` verrà completato correttamente e restituirà una documentazione JSON contenente i dettagli del cluster ridimensionato.

## <a name="next-steps"></a>Passaggi successivi

In questa parte dell'esercitazione si è appreso come:

> [!div class="checklist"]
> * Ridimensionare un cluster di Azure Red Hat OpenShift

Passare all'esercitazione successiva:
> [!div class="nextstepaction"]
> [Eliminare un cluster di Azure Red Hat OpenShift](tutorial-delete-cluster.md)