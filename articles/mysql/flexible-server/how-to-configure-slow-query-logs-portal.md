---
title: Configurare log di query lente-portale di Azure-database di Azure per MySQL-server flessibile
description: Questo articolo descrive come configurare e accedere ai log di query lente nel server flessibile database di Azure per MySQL dalla portale di Azure.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: how-to
ms.date: 9/21/2020
ms.openlocfilehash: e2046673cda17c58153ceb12eee31edb83365092
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91565723"
---
# <a name="configure-and-access-slow-query-logs-for-azure-database-for-mysql---flexible-server-using-the-azure-portal"></a>Configurare e accedere ai log di query lente per database di Azure per MySQL-server flessibile usando il portale di Azure

> [!IMPORTANT]
> Il server flessibile di Database di Azure per MySQL è attualmente disponibile in anteprima pubblica.

È possibile configurare, elencare e scaricare i [log di query lente](concepts-slow-query-logs.md) di database di Azure per MySQL flexible Server dalla portale di Azure.

## <a name="prerequisites"></a>Prerequisiti
Per la procedura descritta in questo articolo è necessario disporre di un [server flessibile](quickstart-create-server-portal.md).

## <a name="configure-logging"></a>Configurare la registrazione
Configurare l'accesso al log di query lente MySQL. 

1. Accedere al [portale di Azure](https://portal.azure.com/).

1. Selezionare il server flessibile.

1. Nella sezione **Impostazioni** della barra laterale selezionare **parametri server**.
   :::image type="content" source="./media/how-to-configure-slow-query-logs-portal/server-parameters.png" alt-text="Pagina parametri server.":::

1. Aggiornare il parametro **slow_query_log** a **on**.
   :::image type="content" source="./media/how-to-configure-slow-query-logs-portal/slow-query-log-enable.png" alt-text="Pagina parametri server.":::

1. Modificare gli altri parametri necessari (ad esempio `long_query_time`, `log_slow_admin_statements`). Per ulteriori parametri, vedere la documentazione relativa ai [log di query lente](./concepts-slow-query-logs.md#configure-slow-query-logging) .  
   :::image type="content" source="./media/how-to-configure-slow-query-logs-portal/long-query-time.png" alt-text="Pagina parametri server.":::

1. Selezionare **Salva**. 
   :::image type="content" source="./media/how-to-configure-slow-query-logs-portal/save-parameters.png" alt-text="Pagina parametri server.":::

Dalla pagina **parametri server** è possibile tornare all'elenco dei log chiudendo la pagina.

## <a name="set-up-diagnostics"></a>Configurare la diagnostica

I log di query lente sono integrati con le impostazioni di diagnostica di monitoraggio di Azure per consentire la Reindirizzamento dei log ai log di monitoraggio di Azure, agli hub eventi o all'archiviazione di Azure.

1. Nella sezione **monitoraggio** della barra laterale selezionare impostazioni di **diagnostica**  >  **Aggiungi impostazioni di diagnostica**.

   :::image type="content" source="./media/how-to-configure-slow-query-logs-portal/add-diagnostic-setting.png" alt-text="Pagina parametri server.":::

1. Specificare un nome per l'impostazione di diagnostica.

1. Specificare quali destinazioni inviare i log di query lente (account di archiviazione, Hub eventi o area di lavoro Log Analytics).

1. Selezionare **MySqlSlowLogs** come tipo di log.
    :::image type="content" source="./media/how-to-configure-slow-query-logs-portal/configure-diagnostic-setting.png" alt-text="Pagina parametri server.":::

1. Dopo aver configurato i sink di dati per inviare tramite pipe i log delle query lente a, selezionare **Salva**.
    :::image type="content" source="./media/how-to-configure-slow-query-logs-portal/save-diagnostic-setting.png" alt-text="Pagina parametri server.":::

1. Per accedere ai log di query lente, esplorarli nei sink di dati configurati. Possono essere necessari fino a 10 minuti per la visualizzazione dei log.

Se i log sono stati reindirizzati ai log di monitoraggio di Azure (Log Analytics), vedere alcune [query di esempio](concepts-slow-query-logs.md#analyze-logs-in-azure-monitor-logs) che è possibile usare per l'analisi. 

## <a name="next-steps"></a>Passaggi successivi
<!-- - See [Access slow query Logs in CLI](howto-configure-server-logs-in-cli.md) to learn how to download slow query logs programmatically.-->
- Altre informazioni sui [log di query lente](concepts-slow-query-logs.md)
- Per altre informazioni sulle definizioni di parametri e sulla registrazione di MySQL, vedere la documentazione di MySQL nei [log](https://dev.mysql.com/doc/refman/5.7/en/slow-query-log.html).
- Informazioni sui [log di controllo](concepts-audit-logs.md)
