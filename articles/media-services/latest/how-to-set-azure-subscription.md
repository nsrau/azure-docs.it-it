---
title: Come trovare la sottoscrizione di Azure
description: Trovare la sottoscrizione di Azure in modo da poter configurare l'ambiente.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: cli,portal, devx-track-azurecli
ms.openlocfilehash: 07088ebfa5fde4c3c790bd554c1eba8d6844b7d1
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92736073"
---
# <a name="find-your-azure-subscription"></a>Trovare la sottoscrizione di Azure

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="portal"></a>[Portale](#tab/portal/)

## <a name="use-the-azure-portal"></a>Usare il portale di Azure

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Nell'intestazione servizi di Azure selezionare sottoscrizioni. Se non sono elencate sottoscrizioni, potrebbe essere necessario passare Azure AD tenant. Gli ID sottoscrizione sono elencati nella seconda colonna.
1. Copiare l'ID sottoscrizione e incollarlo in un documento di testo di propria scelta per l'uso in un secondo momento.

## <a name="cli"></a>[CLI](#tab/cli/)

## <a name="use-the-azure-cli"></a>Usare l'interfaccia della riga di comando di Azure

<!-- NOTE: The following are in the includes file and are reused in other How To articles. All task based content should be in the includes folder with the "task-" prepended to the file name. -->

### <a name="list-your-azure-subscriptions-with-cli"></a>Elencare le sottoscrizioni di Azure con CLI

[!INCLUDE [List your Azure subscriptions with CLI](./includes/task-list-set-subscription-cli.md)]

### <a name="see-also"></a>Vedere anche

* [Interfaccia della riga di comando di Azure](/cli/azure/ams?view=azure-cli-latest)

---

## <a name="next-steps"></a>Passaggi successivi

[Eseguire lo streaming di un file](stream-files-dotnet-quickstart.md)
