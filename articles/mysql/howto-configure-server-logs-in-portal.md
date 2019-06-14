---
title: Configurare e accedere ai log query lente per il Database di Azure per MySQL nel portale di Azure
description: Questo articolo descrive come configurare e accedere ai log lento nel Database di Azure per MySQL dal portale di Azure.
author: rachel-msft
ms.author: raagyema
ms.service: mysql
ms.topic: conceptual
ms.date: 05/29/2019
ms.openlocfilehash: b16ac525d41eb2423828a647fdb75fd3f4a80a31
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67052727"
---
# <a name="configure-and-access-slow-query-logs-in-the-azure-portal"></a>Configurare e log query lente di accesso nel portale di Azure

È possibile configurare, elencare e scaricare il [Database di Azure per i log query lente MySQL](concepts-server-logs.md) dal portale di Azure.

## <a name="prerequisites"></a>Prerequisiti
Per proseguire con questa guida, si richiedono:
- [Server del Database di Azure per MySQL](quickstart-create-mysql-server-database-using-azure-portal.md)

## <a name="configure-logging"></a>Configurare la registrazione
Configurare l'accesso al log di query lente MySQL. 

1. Accedere al [portale di Azure](https://portal.azure.com/).

2. Selezionare il server del Database di Azure per MySQL.

3. Nella sezione **Monitoraggio** nella barra laterale selezionare **Log del server**. 
   ![Selezionare i log del server e fare clic per configurare](./media/howto-configure-server-logs-in-portal/1-select-server-logs-configure.png)

4. Selezionare l'intestazione **Fare clic qui per abilitare i log e configurare i parametri dei log** per visualizzare i parametri del server.

5. Modificare i parametri che è necessario rettificare. Tutte le modifiche apportate in questa sessione vengono evidenziate in viola. 

   Dopo avere modificato i parametri, è possibile fare clic su **Salva**. In alternativa è possibile **annullare** le modifiche.

   ![Fare clic su Salva o Annulla](./media/howto-configure-server-logs-in-portal/3-save-discard.png)

6. Tornare all'elenco di log facendo clic sul **pulsante di chiusura** (icona X) nella pagina **Parametri del server**.

## <a name="view-list-and-download-logs"></a>Visualizzare l'elenco e scaricare i log
Quando inizia la registrazione, è possibile visualizzare un elenco di log query lente disponibili e scaricare singoli file di log nel riquadro dei log del Server.

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
- Visualizzare [log di query lente di accesso nell'infrastruttura CLI](howto-configure-server-logs-in-cli.md) per informazioni su come scaricare i log query lente a livello di codice.
- Altre informazioni sulle [rallentare i log di query](concepts-server-logs.md) nel Database di Azure per MySQL.
- Per altre informazioni sulle definizioni di parametri e la registrazione MySQL, vedere la documentazione di MySQL sui [log](https://dev.mysql.com/doc/refman/5.7/en/slow-query-log.html).