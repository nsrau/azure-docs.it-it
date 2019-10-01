---
title: Configurare e accedere ai log di query lente per database di Azure per MySQL in portale di Azure
description: Questo articolo descrive come configurare e accedere ai log lenti in database di Azure per MySQL dalla portale di Azure.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 09/30/2019
ms.openlocfilehash: b3986c19ec008437f3230b3674ce60d1dfba2024
ms.sourcegitcommit: 6fe40d080bd1561286093b488609590ba355c261
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/01/2019
ms.locfileid: "71703434"
---
# <a name="configure-and-access-slow-query-logs-in-the-azure-portal"></a>Configurare e accedere ai log di query lente nel portale di Azure

È possibile configurare, elencare e scaricare i [log di query lente di database di Azure per MySQL](concepts-server-logs.md) dalla portale di Azure.

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
Una volta avviata la registrazione, è possibile visualizzare un elenco di log di query lente disponibili e scaricare i singoli file di log nel riquadro log del server.

1. Aprire il Portale di Azure.

2. Selezionare il server del Database di Azure per MySQL.

3. Nella sezione **Monitoraggio** della barra laterale selezionare **Log del server**. Nella pagina viene visualizzato un elenco di file di log, come illustrato:

   ![Elenco di log](./media/howto-configure-server-logs-in-portal/4-server-logs-list.png)

   > [!TIP]
   > La convenzione di denominazione del log è **mysql-slow-< nome server>-yyyymmddhh.log**. Data e ora usate nel nome del file indicano l'ora in cui è stato generato il log. I file di log vengono ruotati ogni 24 ore o 7,5 GB, a seconda del valore raggiunto per primo.

4. Se necessario, usare la **casella di ricerca** per restringere l'elenco a un log specifico in base a data/ora. La ricerca viene eseguita in base al nome del log.

5. Scaricare i singoli file di log usando il pulsante per **scaricare** (icona con la freccia giù) accanto a ogni file di log nella riga della tabella, come illustrato:

   ![Fare clic sull'icona per scaricare](./media/howto-configure-server-logs-in-portal/5-download.png)

## <a name="set-up-diagnostic-logs"></a>Configurare i log di diagnostica

1. Nella sezione **monitoraggio** della barra laterale selezionare impostazioni di **diagnostica**.

1. Fare clic su "+ Aggiungi impostazione di diagnostica" @no__t-impostazione di diagnostica 0Add @ no__t-1

1. Specificare un nome per l'impostazione di diagnostica.

1. Specificare i sink di dati per l'invio dei log delle query lente (account di archiviazione, Hub eventi e/o area di lavoro Log Analytics).

1. Selezionare "MySqlSlowLogs" come tipo di log.
impostazione di diagnostica ![Configure @ no__t-1

1. Dopo aver configurato i sink di dati per inviare tramite pipe i log delle query lente a, è possibile fare clic su **Salva**.
impostazione di diagnostica ![Save @ no__t-1

1. Per accedere ai log di query lente, esplorarli nei sink di dati configurati. Potrebbero essere necessari fino a 10 minuti per la visualizzazione dei log.

## <a name="next-steps"></a>Passaggi successivi
- Vedere [accedere ai log di query lente nell'interfaccia della](howto-configure-server-logs-in-cli.md) riga di comando per informazioni su come scaricare i log di query lente a livello di codice
- Altre informazioni sui [log di query lente](concepts-server-logs.md) nel database di Azure per MySQL.
- Per altre informazioni sulle definizioni di parametri e la registrazione MySQL, vedere la documentazione di MySQL sui [log](https://dev.mysql.com/doc/refman/5.7/en/slow-query-log.html).