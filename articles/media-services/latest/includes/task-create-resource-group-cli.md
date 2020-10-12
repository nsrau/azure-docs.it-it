---
author: IngridAtMicrosoft
ms.service: media-services
ms.topic: include
ms.date: 08/17/2020
ms.author: inhenkel
ms.custom: CLI
ms.openlocfilehash: d567a4f7d9b9429887d6396cb2b03dfc34c6ac93
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88602229"
---
<!-- Create a resource group -->

Usare il comando seguente per creare un gruppo di risorse. Selezionare l'area geografica che verrà usata per archiviare i record di supporti e metadati per l'account di servizi multimediali. Questa area verrà usata per elaborare e riprodurre in streaming il contenuto multimediale.

```azurecli
az group create --name amsResourceGroup --location westus2
```
