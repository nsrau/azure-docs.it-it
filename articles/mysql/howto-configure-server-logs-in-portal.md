---
title: Accedere ai log delle query lente - Portale di Azure - Database di Azure per MySQLAccess slow query logs - Azure portal - Azure Database for MySQL
description: Questo articolo descrive come configurare e accedere ai log lenti in Database di Azure per MySQL dal portale di Azure.This article describes how to configure and access the slow logs in Azure Database for MySQL from the Azure portal.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 0261ff7ca8a60dc5fd986a64b9944f9cb9f101e3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80062488"
---
# <a name="configure-and-access-slow-query-logs-from-the-azure-portal"></a>Configurare e accedere alogi di query lenti dal portale di AzureConfigure and access slow query logs from the Azure portal

È possibile configurare, elencare e scaricare il database di Azure per i log delle query lente MySQL dal portale di Azure.You can configure, list, and download the [Azure Database for MySQL slow query logs](concepts-server-logs.md) from the Azure portal.

## <a name="prerequisites"></a>Prerequisiti
I passaggi descritti in questo articolo richiedono il layout [di Azure Database per il server MySQL](quickstart-create-mysql-server-database-using-azure-portal.md).

## <a name="configure-logging"></a>Configurare la registrazione
Configurare l'accesso al log di query lente MySQL. 

1. Accedere al [portale](https://portal.azure.com/)di Azure .

2. Selezionare il server del Database di Azure per MySQL.

3. Nella sezione **Monitoraggio** della barra laterale selezionare **Registri server**. 
   ![Screenshot delle opzioni dei registri del server](./media/howto-configure-server-logs-in-portal/1-select-server-logs-configure.png)

4. Per visualizzare i parametri del server, selezionare **Fare clic qui per abilitare i registri e configurare i parametri del registro**.

5. Modificare i parametri che è necessario rettificare. Tutte le modifiche apportate in questa sessione vengono evidenziate in viola. 

   Dopo aver modificato i parametri, selezionare **Salva**. In alternativa, è possibile annullare le modifiche.

   ![Schermata delle opzioni Dei parametri del server](./media/howto-configure-server-logs-in-portal/3-save-discard.png)

Dalla pagina **Parametri server** è possibile tornare all'elenco dei registri chiudendo la pagina.

## <a name="view-list-and-download-logs"></a>Visualizzare l'elenco e scaricare i log
Dopo l'inizio della registrazione, è possibile visualizzare un elenco dei log di query lenti disponibili e scaricare singoli file di registro.

1. Aprire il portale di Azure.

2. Selezionare il server del Database di Azure per MySQL.

3. Nella sezione **Monitoraggio** della barra laterale selezionare **Registri server**. La pagina mostra un elenco dei file di registro.

   ![Screenshot della pagina Registri server con l'elenco dei log evidenziato](./media/howto-configure-server-logs-in-portal/4-server-logs-list.png)

   > [!TIP]
   > La convenzione di denominazione del log è **mysql-slow-< nome server>-yyyymmddhh.log**. La data e l'ora utilizzate nel nome del file sono l'ora in cui è stato emesso il registro. I file di registro vengono ruotati ogni 24 ore o 7,5 GB, a seconda dell'evento che si trova per primo. 

4. Se necessario, utilizzare la casella di ricerca per restringere rapidamente il registro a un registro specifico, in base alla data e all'ora. La ricerca viene eseguita in base al nome del log.

5. Per scaricare singoli file di registro, selezionare l'icona della freccia rivolta verso il basso accanto a ogni file di registro nella riga della tabella.

   ![Screenshot della pagina Registri server con l'icona con la freccia rivolta verso il basso evidenziata](./media/howto-configure-server-logs-in-portal/5-download.png)

## <a name="set-up-diagnostic-logs"></a>Configurare i log di diagnostica

1. Nella sezione **Monitoraggio** della barra laterale selezionare **Impostazioni** > di diagnostica**Aggiungi impostazioni di diagnostica**.

   ![Screenshot delle opzioni delle impostazioni di diagnostica](./media/howto-configure-server-logs-in-portal/add-diagnostic-setting.png)

1. Specificare un nome per l'impostazione di diagnostica.

1. Specificare i sink di dati da inviare ai log delle query lenti (account di archiviazione, hub eventi o area di lavoro di Log Analytics).

1. Selezionare **MySqlSlowLogs** come tipo di registro.
![Screenshot delle opzioni di configurazione delle impostazioni di diagnostica](./media/howto-configure-server-logs-in-portal/configure-diagnostic-setting.png)

1. Dopo aver configurato i sink di dati in cui reindirizzare i log delle query lente, selezionare **Salva**.
![Screenshot delle opzioni di configurazione delle impostazioni di diagnostica con l'opzione Salva evidenziata](./media/howto-configure-server-logs-in-portal/save-diagnostic-setting.png)

1. Accedere ai log delle query lente esplorandoli nei sink di dati configurati. La visualizzazione dei registri può richiedere fino a 10 minuti.

## <a name="next-steps"></a>Passaggi successivi
- Per informazioni su come scaricare i log delle query lente a livello di codice, vedere [Access slow query Logs in CLI.](howto-configure-server-logs-in-cli.md)
- Altre informazioni sui log di query lenti in Database di Azure per MySQL.Learn more about [slow query logs](concepts-server-logs.md) in Azure Database for MySQL.
- Per ulteriori informazioni sulle definizioni dei parametri e sulla registrazione MySQL, vedere la documentazione di MySQL sui [log](https://dev.mysql.com/doc/refman/5.7/en/slow-query-log.html).