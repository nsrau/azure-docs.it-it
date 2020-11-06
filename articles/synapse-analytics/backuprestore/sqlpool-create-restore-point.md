---
title: Creare un punto di ripristino definito dall'utente per un pool SQL dedicato
description: Come creare un punto di ripristino per un pool SQL dedicato.
services: synapse-analytics
author: joannapea
manager: igorstan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: sql
ms.date: 10/29/2020
ms.author: joanpo
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: c0835fb48d00fe5277732f34fd6b0de1448f6a78
ms.sourcegitcommit: 46c5ffd69fa7bc71102737d1fab4338ca782b6f1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2020
ms.locfileid: "94332135"
---
# <a name="user-defined-restore-points"></a>Punti di ripristino definiti dall'utente

In questo articolo si apprenderà come creare un nuovo punto di ripristino definito dall'utente per un pool SQL dedicato in Azure sinapsi Analytics usando il portale di Azure.

## <a name="create-user-defined-restore-points-through-the-azure-portal"></a>Creare punti di ripristino definiti dall'utente tramite il portale di Azure

I punti di ripristino definiti dall'utente possono essere creati anche tramite portale di Azure.

1. Accedere al proprio account di [portale di Azure](https://portal.azure.com/) .

2. Passare al pool SQL dedicato per il quale si desidera creare un punto di ripristino.

3. Selezionare **Panoramica** nel riquadro a sinistra e selezionare **+ nuovo punto di ripristino**. Se il pulsante nuovo punto di ripristino non è abilitato, assicurarsi che il pool SQL dedicato non sia sospeso.

    ![Nuovo punto di ripristino](../media/sql-pools/create-sqlpool-restore-point-01.png)

4. Specificare un nome per il punto di ripristino definito dall'utente e fare clic su **applica**. I punti di ripristino definiti dall'utente hanno un periodo di conservazione predefinito di sette giorni.

    ![Nome del punto di ripristino](../media/sql-pools/create-sqlpool-restore-point-02.png)

## <a name="next-steps"></a>Passaggi successivi

- [Ripristinare un pool SQL dedicato esistente](restore-sql-pool.md)

