---
title: File di inclusione
description: File di inclusione
services: functions
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 11/26/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: d06bda1826964b019edb156375885c7f389ca6ec
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2019
ms.locfileid: "67180796"
---
Eseguire la procedura seguente per visualizzare e aggiornare la versione del runtime usata attualmente da un'app per le funzioni.

1. Nel [portale di Azure](https://portal.azure.com) passare all'app per le funzioni.

1. In **Funzionalità configurate** scegliere **Impostazioni dell'app per le funzioni**.

    ![Selezionare Impostazioni dell'app per le funzioni](./media/functions-view-update-version-portal/add-update-app-setting.png)

1. Nella scheda **Impostazioni dell'app per le funzioni** individuare la **Versione runtime**. Notare la versione specifica del runtime e la versione principale richiesta. Nell'esempio seguente la versione è impostata su `~2`.

   ![Selezionare Impostazioni dell'app per le funzioni](./media/functions-view-update-version-portal/function-app-view-version.png)

1. Per bloccare l'app per le funzioni alla versione 1.x del runtime, scegliere **~1** in **Versione del runtime**. Questa opzione è disabilitata quando si dispone di funzioni nell'app.

1. Quando si modifica la versione del runtime, tornare alla scheda **Panoramica** e scegliere **Riavvia** per riavviare l'app.  L'app per le funzioni viene riavviare e usa la versione 1.x del runtime e quando si creano funzioni vengono usati i modelli della versione 1.x.