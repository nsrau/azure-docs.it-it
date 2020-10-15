---
title: includere file
description: includere file
services: functions
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 11/26/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: aa9a715fdafc143a116458691965087b016dec1f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "83343355"
---
Eseguire la procedura seguente per visualizzare e aggiornare la versione del runtime usata attualmente da un'app per le funzioni.

1. Nel [portale di Azure](https://portal.azure.com) passare all'app per le funzioni.

1. In **Impostazioni**scegliere **configurazione**. Nella scheda **impostazioni runtime di funzione** individuare la **versione di runtime**. Si noti la versione runtime specifica. Nell'esempio seguente la versione è impostata su `~3`.

    :::image type="content" source="./media/functions-view-update-version-portal/functions-view-runtime-version.png" alt-text="Visualizzare la versione di Runtime." border="true":::

1. Per bloccare l'app per le funzioni alla versione 1.x del runtime, scegliere **~1** in **Versione del runtime**. Questa opzione è disabilitata quando si dispone di funzioni nell'app.

1. Quando si modifica la versione del runtime, tornare alla scheda **Panoramica** e scegliere **Riavvia** per riavviare l'app.  L'app per le funzioni viene riavviare e usa la versione 1.x del runtime e quando si creano funzioni vengono usati i modelli della versione 1.x.

    :::image type="content" source="./media/functions-view-update-version-portal/functions-restart-function-app.png" alt-text="Visualizzare la versione di Runtime." border="true":::
