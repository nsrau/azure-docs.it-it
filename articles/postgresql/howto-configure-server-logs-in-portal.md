---
title: Configurare e accedere ai log del server per Database di Azure per PostgreSQL - singolo Server nel portale di Azure
description: Questo articolo descrive come configurare e accedere ai log del server in Database di Azure per PostgreSQL - Server singolo dal portale di Azure.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 13a8c1ee4b7ca114211f93245d74866e5aa5d52c
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65067441"
---
# <a name="configure-and-access-azure-database-for-postgresql---single-server-logs-in-the-azure-portal"></a>Configurare e accedere ai Database di Azure per PostgreSQL - log di Server singolo nel portale di Azure

È possibile configurare, elencare e scaricare il [Database di Azure per PostgreSQL log](concepts-server-logs.md) dal portale di Azure.

## <a name="prerequisites"></a>Prerequisiti
Per proseguire con questa guida, si richiedono:
- [Un server di Database di Azure per PostgreSQL](quickstart-create-server-database-portal.md)

## <a name="configure-logging"></a>Configurare la registrazione
Configurare l'accesso per i log di query e i log degli errori. 

1. Accedere al [portale di Azure](https://portal.azure.com/).

2. Selezionare il server di Database di Azure per PostgreSQL.

3. Nella sezione **Monitoraggio** della barra laterale selezionare **Log del server**. 

   ![Selezionare Log del server e quindi "Fare clic qui per abilitare..."](./media/howto-configure-server-logs-in-portal/1-select-server-logs-configure.png)

4. Selezionare l'intestazione **Fare clic qui per abilitare i log e configurare i parametri dei log** per visualizzare i parametri del server.

5. Modificare i parametri che è necessario rettificare. Tutte le modifiche apportate in questa sessione vengono evidenziate in viola.

   Dopo avere modificato i parametri, è possibile fare clic su **Salva**. In alternativa è possibile **annullare** le modifiche. 

   ![Elenco di parametri lungo con le modifiche da salvare o annullare](./media/howto-configure-server-logs-in-portal/3-save-discard.png)

6. Tornare all'elenco di log facendo clic sul **pulsante di chiusura** (icona X) nella pagina **Parametri del server**.

## <a name="view-list-and-download-logs"></a>Visualizzare l'elenco e scaricare i log
Quando inizia la registrazione, è possibile visualizzare un elenco di log disponibili e scaricare i singoli file di log nel riquadro Log del server. 

1. Aprire il Portale di Azure.

2. Selezionare il server di Database di Azure per PostgreSQL.

3. Nella sezione **Monitoraggio** della barra laterale selezionare **Log del server**. Nella pagina viene visualizzato un elenco di file di log, come illustrato:

   ![Elenco log del server](./media/howto-configure-server-logs-in-portal/4-server-logs-list.png)

   > [!TIP]
   > La convenzione di denominazione del log è **postgresql-aaaa-mm-gg_hh0000.log**. Data e ora usate nel nome del file indicano l'ora in cui è stato generato il log. I file di log ruotano ogni ora oppure ogni 100 MB di spazio, a seconda della condizione che si verifica per prima.

4. Se necessario, usare la **casella di ricerca** per restringere l'elenco a un log specifico in base a data/ora. La ricerca viene eseguita in base al nome del log.

   ![Esempio di ricerca dei nomi dei log](./media/howto-configure-server-logs-in-portal/5-search.png)

5. Scaricare i singoli file di log usando il pulsante per **scaricare** (icona con la freccia giù) accanto a ogni file di log nella riga della tabella, come illustrato:

   ![Fare clic sull'icona per scaricare](./media/howto-configure-server-logs-in-portal/6-download.png)

## <a name="next-steps"></a>Passaggi successivi
- Per informazioni su come scaricare i log a livello di codice, vedere [Configurare e accedere ai log del server usando l'interfaccia della riga di comando di Azure](howto-configure-server-logs-using-cli.md).
- Altre informazioni sui [Log del server](concepts-server-logs.md) in Database di Azure per PostgreSQL. 
- Per altre informazioni sulle definizioni di parametri e sulla registrazione PostPostgreSQL, vedere la documentazione di PostgreSQL in merito a [Error reporting and Logging](https://www.postgresql.org/docs/current/static/runtime-config-logging.html) (Creazione di report e registrazione di errori).

