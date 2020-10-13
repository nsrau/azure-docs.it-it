---
title: Configurare avvisi e notifiche nel portale di Azure
description: Usare il portale di Azure per creare avvisi, che possono attivare notifiche o automazione quando vengono soddisfatte le condizioni specificate.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: aamalvea
ms.author: aamalvea
ms.reviewer: jrasnik, sstein
ms.date: 05/04/2020
ms.openlocfilehash: 2fdaaf153f242a25e1ad9d8f926d6ac77d285df2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91444912"
---
# <a name="create-alerts-for-azure-sql-database-and-azure-synapse-analytics-using-the-azure-portal"></a>Creare avvisi per il database SQL di Azure e l'analisi delle sinapsi di Azure usando il portale di Azure
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]


## <a name="overview"></a>Panoramica

Questo articolo illustra come configurare gli avvisi per i database nel database SQL di Azure e in Azure sinapsi Analytics (in precedenza SQL Data Warehouse) usando il portale di Azure. Gli avvisi possono inviare un messaggio di posta elettronica all'utente o chiamare un webhook quando una o più metriche (ad esempio le dimensioni del database o l'utilizzo della CPU) raggiungono la soglia impostata.

> [!NOTE]
> Per istruzioni specifiche di Azure SQL Istanza gestita, vedere [creare avvisi per istanza gestita SQL di Azure](../managed-instance/alerts-create.md).

È possibile ricevere avvisi basati su metriche di monitoraggio o eventi nei servizi Azure.

* **Valori metrici** : l'avviso si attiva quando il valore di una metrica specificata supera una soglia assegnata in entrambe le direzioni. Vale a dire che si attiva sia quando la condizione viene inizialmente soddisfatta e successivamente quando tale condizione non è più soddisfatta.
* **Eventi del log attività** : è possibile attivare un avviso per *ogni* evento o solo quando si verifica un determinato numero di eventi.

È possibile configurare un avviso affinché esegua le operazioni seguenti al momento dell'attivazione:

* Inviare notifiche di posta elettronica all'amministratore e ai coamministratori del servizio.
* Inviare un messaggio di posta elettronica ad altri indirizzi specificati.
* Chiamare un webhook

È possibile configurare e ottenere informazioni sulle regole degli avvisi tramite

* [Il portale di Azure](../../azure-monitor/platform/alerts-classic-portal.md)
* [PowerShell](../../azure-monitor/platform/alerts-classic-portal.md)
* [Interfaccia della riga di comando (CLI)](../../azure-monitor/platform/alerts-classic-portal.md)
* [API REST di Monitoraggio di Azure](https://msdn.microsoft.com/library/azure/dn931945.aspx)

## <a name="create-an-alert-rule-on-a-metric-with-the-azure-portal"></a>Creare una regola di avviso in base a una metrica con il portale di Azure

1. Nel [portale](https://portal.azure.com/), individuare la risorsa da monitorare e selezionarla.
2. Selezionare **avvisi** nella sezione monitoraggio. Il testo e l'icona possono lievemente variare per le diverse risorse.  

   ![Monitoraggio](./media/alerts-insights-configure-portal/Alerts.png)
  
3. Selezionare il pulsante **nuova regola di avviso** per aprire la pagina **Crea regola** .
  ![Creare una regola](./media/alerts-insights-configure-portal/create-rule.png)

4. Nella sezione **condizione** fare clic su **Aggiungi**.
  ![Definire la condizione](./media/alerts-insights-configure-portal/create-rule.png)
5. Nella pagina **Configura logica** per i segnali selezionare un segnale.
  ![Seleziona segnale](./media/alerts-insights-configure-portal/select-signal.png)
6. Dopo aver selezionato un segnale, ad esempio **percentuale CPU**, viene visualizzata la pagina **Configura logica segnale** .
  ![Configurare la logica dei segnali](./media/alerts-insights-configure-portal/configure-signal-logic.png)
7. In questa pagina, configurare il tipo di soglia, l'operatore, il tipo di aggregazione, il valore soglia, la granularità delle aggregazioni e la frequenza di valutazione. Fare quindi clic su **Done**.
8. Nella **regola crea**selezionare un **gruppo di azioni** esistente o crearne uno nuovo. Un gruppo di azione consente di definire l'azione da intraprendere quando si verifica una condizione di avviso.
  ![Definire il gruppo di azioni](./media/alerts-insights-configure-portal/action-group.png)

9. Definire un nome per la regola, fornire una descrizione facoltativa, scegliere un livello di gravità per la regola, scegliere se abilitare la regola al momento della creazione della regola, quindi fare clic su **Crea avviso regola** per creare l'avviso della regola metrica.

Entro 10 minuti, l'avviso è attivo e viene attivato come descritto in precedenza.

## <a name="next-steps"></a>Passaggi successivi

* Altre informazioni sulla [configurazione dei webhook negli avvisi](../../azure-monitor/platform/alerts-webhooks.md).
