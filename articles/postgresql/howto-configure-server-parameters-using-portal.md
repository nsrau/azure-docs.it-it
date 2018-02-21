---
title: Configurare i parametri del server nel database di Azure per PostgreSQL tramite il portale di Azure | Microsoft Docs
description: Questo articolo illustra come configurare i parametri del server nel database di Azure per PostgreSQL attraverso il portale di Azure.
services: postgresql
author: rachel-msft
ms.author: raagyema
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 11/08/2017
ms.openlocfilehash: 9e8262fbfcde2e69a656e356a7ab241f2d5043ad
ms.sourcegitcommit: 93902ffcb7c8550dcb65a2a5e711919bd1d09df9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/09/2017
---
# <a name="configure-server-parameters-in-azure-portal"></a>Configurare i parametri del server nel portale di Azure
È possibile elencare, visualizzare e aggiornare i parametri di configurazione per un database di Azure per PostgreSQL attraverso il portale di Azure.

## <a name="prerequisites"></a>prerequisiti
Per proseguire con questa guida, è richiesto:
- [Un server di Database di Azure per PostgreSQL](quickstart-create-server-database-portal.md)

## <a name="viewing-and-editing-parameters"></a>Visualizzazione e modifica dei parametri
1. Aprire il [portale di Azure](https://portal.azure.com).

2. Selezionare il server di Database di Azure per PostgreSQL.

3. Nella sezione **IMPOSTAZIONI** selezionare **Parametri del server**. La pagina mostra un elenco di parametri, i relativi valori e le descrizioni.
![Pagina di panoramica per i parametri](./media/howto-configure-server-parameters-in-portal/3-overview-of-parameters.png)

4. Selezionare il pulsante dell'**elenco a discesa** per vedere i valori possibili per i parametri di tipo enumerato, come client_min_messages.
![Elenco a discesa dei parametri di tipo enumerato](./media/howto-configure-server-parameters-in-portal/4-enum-drop-down.png)

5. Selezionare o passare il puntatore sul pulsante **i** (informazioni) per vedere l'intervallo di valori possibili per i parametri numerici come cpu_index_tuple_cost.
![Pulsante informazioni](./media/howto-configure-server-parameters-in-portal/4-information-button.png)

6. Se necessario, usare la **casella di ricerca** per restringere l'elenco a un parametro specifico. La ricerca viene effettuata sul nome e sulla descrizione dei parametri.
![Risultati della ricerca](./media/howto-configure-server-parameters-in-portal/5-search.png)

7. Modificare i valori dei parametri da regolare. Tutte le modifiche apportate in una sessione vengono evidenziate in viola. Dopo aver modificato i valori, è possibile selezionare **Salva**. In alternativa è possibile **annullare** le modifiche.
![Salvataggio o rimozione delle modifiche](./media/howto-configure-server-parameters-in-portal/6-save-and-discard-buttons.png)

8. Se sono stati salvati nuovi valori per i parametri, è possibile ripristinare i valori predefiniti in qualsiasi momento selezionando **Ripristina tutte le impostazioni predefinite**.
![Ripristino di tutte le impostazioni predefinite](./media/howto-configure-server-parameters-in-portal/7-reset-to-default-button.png)

## <a name="next-steps"></a>Passaggi successivi
Vengono fornite informazioni su:
- [Panoramica dei parametri del server nel database di Azure per PostgreSQL](concepts-servers.md)
- [Configurazione dei parametri usando l'interfaccia della riga di comando di Azure](howto-configure-server-parameters-using-cli.md)
