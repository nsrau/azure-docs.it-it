---
title: Restart-portale di Azure-database di Azure per PostgreSQL-server flessibile
description: Questo articolo descrive come eseguire operazioni di riavvio nel database di Azure per PostgreSQL tramite il portale di Azure.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: 952bd6dddf9f276ed1a4a18f03799147f1902198
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90936931"
---
# <a name="restart-azure-database-for-postgresql---flexible-server"></a>Riavviare il database di Azure per PostgreSQL-server flessibile

> [!IMPORTANT]
> Il server flessibile di Database di Azure per PostgreSQL è disponibile in anteprima

In questo articolo viene fornita una procedura dettagliata per eseguire il riavvio del server flessibile. Questa operazione è utile per applicare le modifiche dei parametri statici che richiedono il riavvio del server di database. La procedura è identica per i server configurati con la disponibilità elevata con ridondanza della zona. 

> [!IMPORTANT]
> Quando è configurato con la disponibilità elevata, il server primario e quello di standby vengono riavviati nello stesso momento.

## <a name="pre-requisites"></a>Prerequisiti

Per completare questa guida, è necessario:

-   È necessario disporre di un server flessibile.

## <a name="restart-your-flexible-server"></a>Riavviare il server flessibile

Per riavviare il server flessibile, attenersi alla seguente procedura.

1.  Nella [portale di Azure](https://portal.azure.com/)scegliere il server flessibile che si desidera riavviare.

2.  Fare clic su **Panoramica** nel pannello a sinistra e fare clic su **Riavvia**.
   
     :::image type="content" source="./media/how-to-restart-server-portal/restart-base-page.png" alt-text="Riavvia selezione":::

3.  Verrà visualizzato un messaggio di conferma popup.

4.  Se si desidera continuare, fare clic su **Sì** .
   
     :::image type="content" source="./media/how-to-restart-server-portal/restart-pop-up.png" alt-text="Conferma riavvio":::
 
6.  Verrà visualizzata una notifica che l'operazione di riavvio è stata avviata.

## <a name="next-steps"></a>Passaggi successivi

-   Scopri di più sulla [continuità aziendale](./concepts-business-continuity.md)
-   Informazioni sulla [disponibilità elevata con ridondanza della zona](./concepts-high-availability.md)
