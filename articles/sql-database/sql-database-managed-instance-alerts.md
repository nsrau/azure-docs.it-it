---
title: Avvisi e notifiche di installazione per Istanza gestita (portale di Azure)
description: Usare il portale di Azure per creare avvisi SQL Istanza gestita, che possono attivare notifiche o automazione quando vengono soddisfatte le condizioni specificate.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik, carlrab
ms.date: 04/02/2020
ms.openlocfilehash: a332627d149a36ba5d5beb2626023e58a221f0d6
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80639198"
---
# <a name="create-alerts-for-azure-sql-managed-instance-using-the-azure-portal"></a>Creare avvisi per Istanza gestita SQL di Azure usando il portale di Azure

## <a name="overview"></a>Panoramica

Questo articolo illustra come configurare gli avvisi per i database nel database SQL di Azure Istanza gestita usando il portale di Azure. Gli avvisi possono inviare un messaggio di posta elettronica o chiamare un webhook quando una metrica, ad esempio l'utilizzo della CPU o delle dimensioni di archiviazione dell'istanza, raggiunge la soglia. Questo articolo include anche le procedure consigliate per impostare i periodi di avviso.

È possibile ricevere avvisi basati su metriche di monitoraggio o eventi nei servizi Azure.

* **Valori metrici** : l'avviso si attiva quando il valore di una metrica specificata supera una soglia assegnata in entrambe le direzioni. Vale a dire che si attiva sia quando la condizione viene inizialmente soddisfatta e successivamente quando tale condizione non è più soddisfatta.
* **Eventi del log attività** : è possibile attivare un avviso per *ogni* evento o solo quando si verifica un determinato numero di eventi.

È possibile configurare un avviso affinché esegua le operazioni seguenti al momento dell'attivazione:

* Inviare notifiche di posta elettronica all'amministratore e ai coamministratori del servizio.
* Inviare un messaggio di posta elettronica ad altri indirizzi specificati.
* Chiamare un webhook

È possibile configurare e ottenere informazioni sulle regole degli avvisi tramite

* [Azure portal](../monitoring-and-diagnostics/insights-alerts-portal.md)
* [PowerShell](../azure-monitor/platform/alerts-classic-portal.md)
* [interfaccia della riga di comando (CLI)](../azure-monitor/platform/alerts-classic-portal.md)
* [API REST di Monitoraggio di Azure](https://msdn.microsoft.com/library/azure/dn931945.aspx)

## <a name="create-an-alert-rule-on-a-metric-with-the-azure-portal"></a>Creare una regola di avviso in base a una metrica con il portale di Azure

1. Nel [portale](https://portal.azure.com/), individuare la risorsa da monitorare e selezionarla.
2. Selezionare **avvisi** nella sezione monitoraggio. Il testo e l'icona possono lievemente variare per le diverse risorse.  

   ![Monitoraggio](media/sql-database-insights-alerts-portal/Alerts.png)
  
3. Selezionare il pulsante **nuova regola di avviso** per aprire la pagina **Crea regola** .
   ![Crea regola](media/sql-database-insights-alerts-portal/create-rule.png)

4. Nella sezione **condizione** fare clic su **Aggiungi**.
   ![Definire la condizione](media/sql-database-insights-alerts-portal/create-rule.png)
5. Nella pagina **Configura logica** per i segnali selezionare un segnale.
   ![Seleziona segnale](media/sql-database-insights-alerts-portal/select-signal.png)
6. Dopo aver selezionato un segnale, ad esempio **percentuale CPU**, viene visualizzata la pagina **Configura logica segnale** .
   ![Configurare la logica dei segnali](media/sql-database-insights-alerts-portal/configure-signal-logic.png)
7. In questa pagina, configurare il tipo di soglia, l'operatore, il tipo di aggregazione, il valore soglia, la granularità delle aggregazioni e la frequenza di valutazione. Quindi fare clic su **fine**.
8. Nella **regola crea**selezionare un **gruppo di azioni** esistente o crearne uno nuovo. Un gruppo di azione consente di definire l'azione da intraprendere quando si verifica una condizione di avviso.
  ![Definire il gruppo di azioni](media/sql-database-insights-alerts-portal/action-group.png)

9. Definire un nome per la regola, fornire una descrizione facoltativa, scegliere un livello di gravità per la regola, scegliere se abilitare la regola al momento della creazione della regola, quindi fare clic su **Crea avviso regola** per creare l'avviso della regola metrica.

Entro 10 minuti, l'avviso è attivo e viene attivato come descritto in precedenza.

## <a name="next-steps"></a>Passaggi successivi

* Altre informazioni sulla [configurazione dei webhook negli avvisi](../azure-monitor/platform/alerts-webhooks.md).
