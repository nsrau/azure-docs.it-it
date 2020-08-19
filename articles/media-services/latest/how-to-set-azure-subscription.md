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
ms.date: 03/15/2020
ms.author: inhenkel
ms.custom: cli,portal
ms.openlocfilehash: fadb290d1bd1126c3bfc0cd76d6e6b862d1396e3
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/18/2020
ms.locfileid: "88556295"
---
# <a name="find-your-azure-subscription"></a>Trova la tua sottoscrizione di Azure

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
