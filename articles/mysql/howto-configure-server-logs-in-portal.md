---
title: Configurare e accedere ai log del server per il Database di Azure per MySQL nel Portale di Azure | Microsoft Docs
description: Questo articolo descrive come configurare e accedere ai log del server nel Database di Azure per MySQL dal Portale di Azure.
services: mysql
author: rachel-msft
ms.author: raagyema
manager: jhubbard
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 10/19/2017
ms.openlocfilehash: 89674c133b458c16fbdacd771be24830624dde7c
ms.sourcegitcommit: 2d1153d625a7318d7b12a6493f5a2122a16052e0
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/20/2017
---
# <a name="configure-and-access-server-logs-in-the-azure-portal"></a>Configurare e accedere ai log del server nel portale di Azure

È possibile configurare, elencare e scaricare i [log del server di Database di Azure per PostgreSQL](concepts-server-logs.md) dal portale di Azure.

## <a name="prerequisites"></a>prerequisiti
Per proseguire con questa guida, si richiedono:
- [Server del Database di Azure per MySQL](quickstart-create-mysql-server-database-using-azure-portal.md)

## <a name="configure-logging"></a>Configurare la registrazione
Configurare l'accesso al log di query lente MySQL. 

1. Accedere al [portale di Azure](http://portal.azure.com/).

2. Selezionare il server del Database di Azure per MySQL.

3. Nella sezione **Monitoraggio** nella barra laterale selezionare **Log del server**. 
   ![Selezionare i log del server e fare clic per configurare](./media/howto-configure-server-logs-in-portal/1-select-server-logs-configure.png)

4. Selezionare l'intestazione **Fare clic qui per abilitare i log e configurare i parametri dei log** per visualizzare i parametri del server.

5. Selezionare **Mostra più** per visualizzare un elenco esteso dei parametri disponibili. 

   Per altre informazioni sulle definizioni di parametri, vedere la documentazione MySQL sui [log](https://dev.mysql.com/doc/refman/5.7/en/slow-query-log.html).

   ![Fare clic su mostra più per un elenco più lungo](./media/howto-configure-server-logs-in-portal/2-show-more.png)

6. Modificare i parametri che è necessario adattare. Tutte le modifiche apportate in questa sessione vengono evidenziate in viola. 

   Dopo avere modificato i parametri, è possibile fare clic su **Salva**. In alternativa è possibile **annullare** le modifiche.

   ![Fare clic su Salva o Annulla](./media/howto-configure-server-logs-in-portal/3-save-discard.png)

7. Tornare all'elenco di log facendo clic sul **pulsante di chiusura** (icona X) nella pagina **Parametri del server**.

## <a name="view-list-and-download-logs"></a>Visualizzare l'elenco e scaricare i log
Quando inizia la registrazione, è possibile visualizzare un elenco di log disponibili e scaricare i singoli file di log nel riquadro Log del server. 

1. Aprire il Portale di Azure.

2. Selezionare il server del Database di Azure per MySQL.

3. Nella sezione **Monitoraggio** della barra laterale selezionare **Log del server**. Nella pagina viene visualizzato un elenco di file di log, come illustrato:

   ![Elenco di log](./media/howto-configure-server-logs-in-portal/4-server-logs-list.png)

   > [!TIP]
   > La convenzione di denominazione del log è **mysql-slow-< nome server>-yyyymmddhh.log**. Data e ora usate nel nome del file indicano l'ora in cui è stato generato il log. I file di log vengono ruotati ogni 24 ore o 7,5 GB, a seconda del valore raggiunto per primo.

4. Se necessario, usare la **casella di ricerca** per restringere l'elenco a un log specifico in base a data/ora. La ricerca viene eseguita in base al nome del log.

5. Scaricare i singoli file di log usando il pulsante per **scaricare** (icona con la freccia giù) accanto a ogni file di log nella riga della tabella, come illustrato:

   ![Fare clic sull'icona per scaricare](./media/howto-configure-server-logs-in-portal/5-download.png)


## <a name="next-steps"></a>Passaggi successivi
- Per informazioni su come scaricare i log a livello di codice, vedere [Configurare e accedere ai log del server usando l'interfaccia della riga di comando di Azure](howto-configure-server-logs-in-cli.md).
- Altre informazioni sui [log del server](concepts-server-logs.md) nel Database di Azure per MySQL. 
- Per altre informazioni sulle definizioni di parametri e la registrazione MySQL, vedere la documentazione di MySQL sui [log](https://dev.mysql.com/doc/refman/5.7/en/slow-query-log.html).

