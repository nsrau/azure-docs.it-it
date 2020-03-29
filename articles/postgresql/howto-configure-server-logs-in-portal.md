---
title: Gestire i log - Portale di Azure - Database di Azure per PostgreSQL - Server singoloManage logs - Azure portal - Azure Database for PostgreSQL - Single Server
description: Questo articolo descrive come configurare e accedere ai log del server (file con estensione log) nel database di Azure per PostgreSQL - Singolo server dal portale di Azure.This article describes how to configure and access the server logs (.log files) in Azure Database for PostgreSQL - Single Server from the Azure portal.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 3da48a9b6d3acb1f2811bc279de7963fa1d83918
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74763692"
---
# <a name="configure-and-access-azure-database-for-postgresql---single-server-logs-from-the-azure-portal"></a>Configurare e accedere al database di Azure per PostgreSQL - Log a server singolo dal portale di AzureConfigure and access Azure Database for PostgreSQL - Single Server logs from the Azure portal

È possibile configurare, elencare e scaricare il database di [Azure per i log PostgreSQL](concepts-server-logs.md) dal portale di Azure.You can configure, list, and download the Azure Database for PostgreSQL logs from the Azure portal.

## <a name="prerequisites"></a>Prerequisiti
I passaggi descritti in questo articolo richiedono la creazione di un database di [Azure per il server PostgreSQL.](quickstart-create-server-database-portal.md)

## <a name="configure-logging"></a>Configurare la registrazione
Configurare l'accesso per i log di query e i log degli errori. 

1. Accedere al [portale](https://portal.azure.com/)di Azure .

2. Selezionare il server di Database di Azure per PostgreSQL.

3. Nella sezione **Monitoraggio** della barra laterale selezionare **Registri server**. 

   ![Screenshot delle opzioni dei registri del server](./media/howto-configure-server-logs-in-portal/1-select-server-logs-configure.png)

4. Per visualizzare i parametri del server, selezionare **Fare clic qui per abilitare i registri e configurare i parametri del registro**.

5. Modificare i parametri che è necessario rettificare. Tutte le modifiche apportate in questa sessione vengono evidenziate in viola.

   Dopo aver modificato i parametri, selezionare **Salva**. In alternativa, è possibile annullare le modifiche. 

   ![Schermata delle opzioni Dei parametri del server](./media/howto-configure-server-logs-in-portal/3-save-discard.png)

Dalla pagina **Parametri server** è possibile tornare all'elenco dei registri chiudendo la pagina.

## <a name="view-list-and-download-logs"></a>Visualizzare l'elenco e scaricare i log
Dopo l'inizio della registrazione, è possibile visualizzare un elenco dei registri disponibili e scaricare singoli file di registro. 

1. Aprire il portale di Azure.

2. Selezionare il server di Database di Azure per PostgreSQL.

3. Nella sezione **Monitoraggio** della barra laterale selezionare **Registri server**. La pagina mostra un elenco dei file di registro.

   ![Screenshot della pagina Registri server con l'elenco dei log evidenziato](./media/howto-configure-server-logs-in-portal/4-server-logs-list.png)

   > [!TIP]
   > La convenzione di denominazione del log è **postgresql-aaaa-mm-gg_hh0000.log**. La data e l'ora utilizzate nel nome del file sono l'ora in cui è stato emesso il registro. I file di registro ruotano ogni ora o 100 MB, a seconda di quale viene prima.

4. Se necessario, utilizzare la casella di ricerca per restringere rapidamente il registro a un registro specifico, in base alla data e all'ora. La ricerca viene eseguita in base al nome del log.

   ![Screenshot della pagina Registri server con casella di ricerca e risultati evidenziati](./media/howto-configure-server-logs-in-portal/5-search.png)

5. Per scaricare singoli file di registro, selezionare l'icona della freccia rivolta verso il basso accanto a ogni file di registro nella riga della tabella.

   ![Screenshot della pagina Registri server con l'icona con la freccia rivolta verso il basso evidenziata](./media/howto-configure-server-logs-in-portal/6-download.png)

## <a name="next-steps"></a>Passaggi successivi
- Per informazioni su come scaricare i log a livello di codice, vedere [Accedere ai log del server nell'interfaccia della riga di comando.](howto-configure-server-logs-using-cli.md)
- Altre informazioni sui log del [server](concepts-server-logs.md) in Database di Azure per PostgreSQL. 
- Per ulteriori informazioni sulle definizioni dei parametri e sulla registrazione PostgreSQL, vedere la documentazione postgreSQL sulla [segnalazione e](https://www.postgresql.org/docs/current/static/runtime-config-logging.html)la registrazione degli errori .

