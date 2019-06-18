---
title: File di inclusione (flussi dispositivo)
description: File di inclusione
services: iot-hub
author: rezas
ms.service: iot-hub
ms.topic: include
ms.date: 01/15/2019
ms.author: rezas
ms.custom: include file
ms.openlocfilehash: 155e36bf4198eb18f5505483ea13a287f88a5022
ms.sourcegitcommit: 4cdd4b65ddbd3261967cdcd6bc4adf46b4b49b01
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/06/2019
ms.locfileid: "66733233"
---
Se si prevede di continuare con il prossimo articolo consigliato, è possibile conservare le risorse già create e riutilizzarle.

In caso contrario, per evitare addebiti, è possibile eliminare le risorse di Azure create in questo articolo. 

> [!IMPORTANT]
> L'eliminazione di un gruppo di risorse è irreversibile. Il gruppo di risorse e tutte le risorse in esso contenute vengono eliminati in modo permanente. Assicurarsi di non eliminare accidentalmente il gruppo di risorse sbagliato o le risorse errate. Se l'hub IoT è stato creato all'interno di un gruppo di risorse esistente che contiene risorse che si vogliono conservare, eliminare solo la risorsa hub IoT invece dell'intero gruppo.
>

Per eliminare un gruppo di risorse per nome:

1. Accedere al [portale di Azure](https://portal.azure.com) e selezionare **Gruppi di risorse**.

1. Nella casella di testo **Filtra per nome** immettere il nome del gruppo di risorse che contiene l'hub IoT. 

1. Nell'elenco dei risultati, a destra del gruppo di risorse, selezionare i puntini di sospensione ( **...** ) e quindi **Elimina gruppo di risorse**.

    ![Pulsante "Elimina gruppo di risorse"](./media/iot-hub-quickstarts-clean-up-resources-device-streams/iot-hub-delete-resource-group.png)

1. Per confermare l'eliminazione del gruppo di risorse, reimmettere il relativo nome e quindi selezionare **Elimina**. Dopo alcuni secondi il gruppo di risorse viene eliminato insieme a tutte le risorse al suo interno.
