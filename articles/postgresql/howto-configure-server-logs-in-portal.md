---
title: Gestire i log-portale di Azure-database di Azure per PostgreSQL-server singolo
description: Questo articolo descrive come configurare e accedere ai log del server (file con estensione log) nel database di Azure per PostgreSQL-server singolo dal portale di Azure.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: how-to
ms.date: 5/6/2019
ms.openlocfilehash: cf22a165e9a6eadcd2e5d4cdb0efa301538be7e9
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90907528"
---
# <a name="configure-and-access-azure-database-for-postgresql---single-server-logs-from-the-azure-portal"></a>Configurare e accedere a database di Azure per PostgreSQL: log a server singolo dal portale di Azure

È possibile configurare, elencare e scaricare i [log di database di Azure per PostgreSQL](concepts-server-logs.md) dalla portale di Azure.

## <a name="prerequisites"></a>Prerequisiti
Per la procedura descritta in questo articolo è necessario disporre del [database di Azure per il server PostgreSQL](quickstart-create-server-database-portal.md).

## <a name="configure-logging"></a>Configurare la registrazione
Configurare l'accesso per i log di query e i log degli errori. 

1. Accedere al [portale di Azure](https://portal.azure.com/).

2. Selezionare il server di Database di Azure per PostgreSQL.

3. Nella sezione **monitoraggio** della barra laterale selezionare log del **Server**. 

   :::image type="content" source="./media/howto-configure-server-logs-in-portal/1-select-server-logs-configure.png" alt-text="Screenshot delle opzioni dei log del server":::

4. Per visualizzare i parametri del server, selezionare **fare clic qui per abilitare i log e configurare i parametri del log**.

5. Modificare i parametri che è necessario rettificare. Tutte le modifiche apportate in questa sessione vengono evidenziate in viola.

   Dopo aver modificato i parametri, selezionare **Salva**. In alternativa, è possibile annullare le modifiche. 

   :::image type="content" source="./media/howto-configure-server-logs-in-portal/3-save-discard.png" alt-text="Screenshot delle opzioni dei parametri del server":::

Dalla pagina **parametri server** è possibile tornare all'elenco dei log chiudendo la pagina.

## <a name="view-list-and-download-logs"></a>Visualizzare l'elenco e scaricare i log
Una volta iniziata la registrazione, è possibile visualizzare un elenco di log disponibili e scaricare i singoli file di log. 

1. Aprire il portale di Azure.

2. Selezionare il server di Database di Azure per PostgreSQL.

3. Nella sezione **monitoraggio** della barra laterale selezionare log del **Server**. La pagina Visualizza un elenco dei file di log.

   :::image type="content" source="./media/howto-configure-server-logs-in-portal/4-server-logs-list.png" alt-text="Screenshot della pagina log del server con elenco dei log evidenziato":::

   > [!TIP]
   > La convenzione di denominazione del log è **postgresql-aaaa-mm-gg_hh0000.log**. La data e l'ora utilizzate nel nome del file corrispondono all'ora in cui è stato emesso il log. I file di log ruotano ogni ora o 100 MB, a seconda di quale si verifichi per primo.

4. Se necessario, usare la casella di ricerca per restringere rapidamente a un log specifico, in base alla data e all'ora. La ricerca viene eseguita in base al nome del log.

   :::image type="content" source="./media/howto-configure-server-logs-in-portal/5-search.png" alt-text="Screenshot della pagina log del server, con la casella di ricerca e i risultati evidenziati":::

5. Per scaricare i singoli file di log, selezionare l'icona della freccia rivolta verso il basso accanto a ogni file di log nella riga della tabella.

   :::image type="content" source="./media/howto-configure-server-logs-in-portal/6-download.png" alt-text="Screenshot della pagina log del server con icona con la freccia giù evidenziata":::

## <a name="next-steps"></a>Passaggi successivi
- Vedere [accedere ai log del server nell'interfaccia della](howto-configure-server-logs-using-cli.md) riga di comando per informazioni su come scaricare log a livello di codice.
- Altre informazioni sui [log del server](concepts-server-logs.md) in database di Azure per PostgreSQL. 
- Per altre informazioni sulle definizioni di parametri e sulla registrazione PostgreSQL, vedere la documentazione di PostgreSQL sulla [segnalazione e la registrazione degli errori](https://www.postgresql.org/docs/current/static/runtime-config-logging.html).

