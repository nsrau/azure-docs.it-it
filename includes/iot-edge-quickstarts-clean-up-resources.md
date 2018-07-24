---
title: File di inclusione
description: File di inclusione
services: iot-edge
author: wesmc7777
ms.service: iot-edge
ms.topic: include
ms.date: 06/26/2018
ms.author: wesmc
ms.custom: include file
ms.openlocfilehash: 657006360105ac26091c54fe2e0deb523d1e6dea
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/13/2018
ms.locfileid: "39008502"
---
Se si intende continuare con il prossimo articolo consigliato, è possibile conservare le risorse e le configurazioni create e riutilizzarle.

In caso contrario, è possibile eliminare le risorse di Azure e le configurazioni locali create in questo articolo per evitare addebiti. 

> [!IMPORTANT]
> L'eliminazione delle risorse di Azure e dei gruppi di risorse è irreversibile. Quando questi elementi vengono eliminati, il gruppo di risorse e tutte le risorse in esso contenute vengono eliminati in modo permanente. Assicurarsi di non eliminare accidentalmente il gruppo di risorse sbagliato o le risorse errate. Se si è creato l'hub IoT all'interno di un gruppo di risorse esistente che contiene risorse che si vogliono conservare, eliminare solo la risorsa dell'hub IoT invece di eliminare il gruppo di risorse.
>

Per eliminare solo l'hub IoT, eseguire il comando seguente. Sostituire \<YourIoTHub > con il nome dell'hub IoT e \<TestResources > con il nome del gruppo di risorse:

```azurecli-interactive
az iot hub delete --name <YourIoTHub> --resource-group <TestResources>
```


Per eliminare l'intero gruppo di risorse per nome:

1. Accedere al [portale di Azure](https://portal.azure.com) e selezionare **Gruppi di risorse**.

2. Nella casella di testo **Filtra per nome** immettere il nome del gruppo di risorse che contiene l'hub IoT. 

3. Alla destra del gruppo di risorse nell'elenco di risultati selezionare i puntini di sospensione (**...**) e quindi selezionare **Elimina gruppo di risorse**.

    ![Eliminare un gruppo di risorse](./media/iot-edge-quickstarts-clean-up-resources/iot-edge-delete-resource-group.png)

4. Verrà chiesto di confermare l'eliminazione del gruppo di risorse. Immettere di nuovo il nome del gruppo di risorse per confermare e selezionare **Elimina**. Dopo qualche istante il gruppo di risorse e tutte le risorse che contiene vengono eliminati.






