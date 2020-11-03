---
title: Arrestare/avviare portale di Azure-server flessibile per database di Azure per MySQL
description: Questo articolo descrive come arrestare/avviare le operazioni nel database di Azure per MySQL tramite il portale di Azure.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 09/29/2020
ms.openlocfilehash: 15b08ea67afe0d307470b5a4fb0f7d26e0f4ea82
ms.sourcegitcommit: 80034a1819072f45c1772940953fef06d92fefc8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/03/2020
ms.locfileid: "93241923"
---
# <a name="stopstart-an-azure-database-for-mysql---flexible-server-preview"></a>Arrestare/avviare un database di Azure per MySQL-server flessibile (anteprima)

> [!IMPORTANT]
> Il server flessibile di Database di Azure per MySQL è attualmente disponibile in anteprima pubblica.

In questo articolo viene fornita una procedura dettagliata per eseguire l'arresto e l'avvio del server flessibile.

## <a name="prerequisites"></a>Prerequisiti

Per completare questa guida, è necessario:

-   È necessario disporre di un server flessibile per database di Azure per MySQL.

## <a name="stop-a-running-server"></a>Arrestare un server in esecuzione

1.  Nella [portale di Azure](https://portal.azure.com/)scegliere il server flessibile che si desidera arrestare.

2.  Nella pagina **Panoramica** fare clic sul pulsante **Arresta** sulla barra degli strumenti.
    
    :::image type="content" source="media/how-to-stop-start-server-portal/stop-server.png" alt-text="Arrestare il server flessibile."::: 

3.  Fare clic su **Sì** per confermare l'arresto del server.

    :::image type="content" source="media/how-to-stop-start-server-portal/confirm-stop.png" alt-text="Arrestare il server flessibile."::: 

> [!NOTE]
> Una volta arrestato il server, le altre operazioni di gestione non sono disponibili per il server flessibile.

## <a name="start-a-stopped-server"></a>Avviare un server arrestato

1.  Nella [portale di Azure](https://portal.azure.com/)scegliere il server flessibile che si desidera avviare.

2.  Nella pagina **Panoramica** fare clic sul pulsante **Avvia** sulla barra degli strumenti.

    :::image type="content" source="media/how-to-stop-start-server-portal/start-server.png" alt-text="Arrestare il server flessibile.":::  

> [!NOTE]
> Una volta avviato il server, tutte le operazioni di gestione sono ora disponibili per il server flessibile.

## <a name="next-steps"></a>Passaggi successivi
- Altre informazioni sulla [rete nel server flessibile database di Azure per MySQL](./concepts-networking.md)
- [Creare e gestire la rete virtuale del server flessibile di Database di Azure per MySQL tramite il portale di Azure](./how-to-manage-virtual-network-portal.md).

