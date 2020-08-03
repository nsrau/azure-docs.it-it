---
title: Creare raccolte di immagini condivise con l'interfaccia della riga di comando di Azure
description: Questo articolo descrive come usare l'interfaccia della riga di comando di Azure per creare immagini di macchine virtuali condivise in Azure.
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.topic: how-to
ms.workload: infrastructure
ms.date: 05/04/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.custom: devx-track-azurecli
ms.openlocfilehash: ff50f61fe159c518d488da9f9911e40f1ddb8ca3
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/31/2020
ms.locfileid: "87501458"
---
# <a name="create-a-shared-image-gallery-with-the-azure-cli"></a>Creare una raccolta di immagini condivise con l'interfaccia della riga di comando di Azure

La [raccolta di immagini condivise](./linux/shared-image-galleries.md) semplifica la condivisione di immagini personalizzate all'interno dell'organizzazione. Le immagini personalizzate sono come le immagini di marketplace, ma si possono creare autonomamente. Le immagini personalizzate possono essere usate per le configurazioni di avvio, ad esempio il precaricamento e le configurazioni di applicazioni e altre configurazioni del sistema operativo. 

Raccolta immagini condivise consente di condividere le immagini di macchine virtuali personalizzate con altri utenti. Scegliere le immagini che si intende condividere, le aree nelle quali si vuole renderle disponibili e i destinatari. 

[!INCLUDE [virtual-machines-common-shared-images-cli](../../includes/virtual-machines-common-shared-images-cli.md)]


## <a name="next-steps"></a>Passaggi successivi

Creare una versione di immagine da una [macchina virtuale](image-version-vm-cli.md)o da un' [immagine gestita](image-version-managed-image-cli.md) usando l'interfaccia della riga di comando di Azure.

Per altre informazioni sulle raccolte di immagini condivise, vedere [Panoramica](./linux/shared-image-galleries.md). Se si verificano problemi, vedere [Risoluzione dei problemi delle raccolte di immagini condivise](troubleshooting-shared-images.md).
