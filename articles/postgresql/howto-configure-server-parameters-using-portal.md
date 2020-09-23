---
title: Configurare i parametri del server-portale di Azure-database di Azure per PostgreSQL-server singolo
description: Questo articolo descrive come configurare i parametri Postgres nel database di Azure per PostgreSQL tramite il portale di Azure.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: how-to
ms.date: 02/28/2018
ms.openlocfilehash: a2410328b068be6bc5af358fa72ee20eead4c8ed
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90907455"
---
# <a name="configure-server-parameters-in-azure-database-for-postgresql---single-server-via-the-azure-portal"></a>Configurare i parametri del server nel database di Azure per PostgreSQL-server singolo tramite il portale di Azure 
È possibile elencare, visualizzare e aggiornare i parametri di configurazione per un database di Azure per PostgreSQL attraverso il portale di Azure.

## <a name="prerequisites"></a>Prerequisiti
Per proseguire con questa guida, è richiesto:
- [Database di Azure per il server PostgreSQL](quickstart-create-server-database-portal.md)

## <a name="viewing-and-editing-parameters"></a>Visualizzazione e modifica dei parametri
1. Aprire il [portale di Azure](https://portal.azure.com).

2. Selezionare il server di Database di Azure per PostgreSQL.

3. Nella sezione **IMPOSTAZIONI** selezionare **Parametri del server**. La pagina mostra un elenco di parametri, i relativi valori e le descrizioni.
:::image type="content" source="./media/howto-configure-server-parameters-in-portal/3-overview-of-parameters.png" alt-text="Pagina di panoramica per i parametri":::

4. Selezionare il pulsante dell'**elenco a discesa** per vedere i valori possibili per i parametri di tipo enumerato, come client_min_messages.
:::image type="content" source="./media/howto-configure-server-parameters-in-portal/4-enum-drop-down.png" alt-text="Elenco a discesa dei parametri di tipo enumerato":::

5. Selezionare o passare il puntatore sul pulsante **i** (informazioni) per vedere l'intervallo di valori possibili per i parametri numerici come cpu_index_tuple_cost.
:::image type="content" source="./media/howto-configure-server-parameters-in-portal/4-information-button.png" alt-text="Pulsante informazioni":::

6. Se necessario, usare la **casella di ricerca** per restringere l'elenco a un parametro specifico. La ricerca viene effettuata sul nome e sulla descrizione dei parametri.
:::image type="content" source="./media/howto-configure-server-parameters-in-portal/5-search.png" alt-text="Search Results":::

7. Modificare i valori dei parametri da regolare. Tutte le modifiche apportate in una sessione vengono evidenziate in viola. Dopo aver modificato i valori, è possibile selezionare **Salva**. In alternativa è possibile **annullare** le modifiche.
:::image type="content" source="./media/howto-configure-server-parameters-in-portal/6-save-and-discard-buttons.png" alt-text="Salvataggio o rimozione delle modifiche":::

8. Se sono stati salvati nuovi valori per i parametri, è possibile ripristinare i valori predefiniti in qualsiasi momento selezionando **Ripristina tutte le impostazioni predefinite**.
:::image type="content" source="./media/howto-configure-server-parameters-in-portal/7-reset-to-default-button.png" alt-text="Ripristino di tutte le impostazioni predefinite":::

## <a name="next-steps"></a>Passaggi successivi
Vengono fornite informazioni su:
- [Panoramica dei parametri del server nel database di Azure per PostgreSQL](concepts-servers.md)
- [Configurazione dei parametri usando l'interfaccia della riga di comando di Azure](howto-configure-server-parameters-using-cli.md)
