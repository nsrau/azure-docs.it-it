---
title: Configurare avvisi e notifiche (portale di Azure)Setup alerts and notifications (Azure portal)
description: Usare il portale di Azure per creare avvisi per il database SQL in grado di attivare notifiche o eventi di automazione quando vengono soddisfatte le condizioni specificate.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: aamalvea
ms.author: aamalvea
ms.reviewer: jrasnik, carlrab
ms.date: 03/10/2020
ms.openlocfilehash: 67c47b35e84a93d7d9032ad55b425ae2bb6971fe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79209525"
---
# <a name="create-alerts-for-azure-sql-database-and-azure-synapse-analytics-databases-using-azure-portal"></a>Creare avvisi per il database SQL di Azure e i database di Azure Synapse Analytics usando il portale di AzureCreate alerts for Azure SQL Database and Azure Synapse Analytics databases using Azure portal

## <a name="overview"></a>Panoramica

Questo articolo illustra come configurare gli avvisi per database singoli, in pool e di data warehouse nel database SQL di Azure e in Azure Synapse Analytics (in precedenza Azure SQL Data Warehouse) tramite il portale di Azure.This article shows you how to set up alerts for single, pooled, and data warehouse databases in Azure SQL Database and Azure Synapse Analytics (formerly Azure SQL Data Warehouse) using the Azure portal. Gli avvisi possono inviare un messaggio di posta elettronica all'utente o chiamare un webhook quando una o più metriche (ad esempio le dimensioni del database o l'utilizzo della CPU) raggiungono la soglia impostata. Questo articolo include anche le procedure consigliate per impostare i periodi di avviso.

> [!IMPORTANT]
> Questa funzionalità non è disponibile in Istanza gestita. Come alternativa è possibile usare SQL Agent per inviare avvisi tramite posta elettronica per alcune metriche basate su [DMV](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/system-dynamic-management-views).

È possibile ricevere avvisi basati su metriche di monitoraggio o eventi nei servizi Azure.

* **Valori metriche:** l'avviso viene attivato quando il valore di una metrica specificata supera una soglia assegnata in entrambe le direzioni. Vale a dire che si attiva sia quando la condizione viene inizialmente soddisfatta e successivamente quando tale condizione non è più soddisfatta.
* **Eventi del log attività:** un avviso può essere generato a *ogni* evento o solo quando si verifica un determinato numero di eventi.

È possibile configurare un avviso affinché esegua le operazioni seguenti al momento dell'attivazione:

* Inviare notifiche di posta elettronica all'amministratore e ai coamministratori del servizio.
* Inviare un messaggio di posta elettronica ad altri indirizzi specificati.
* Chiamare un webhook

È possibile configurare e ottenere informazioni sulle regole degli avvisi tramite

* [Portale di Azure](../monitoring-and-diagnostics/insights-alerts-portal.md)
* [Powershell](../azure-monitor/platform/alerts-classic-portal.md)
* [interfaccia della riga di comando (CLI)](../azure-monitor/platform/alerts-classic-portal.md)
* [API REST di Monitoraggio di Azure](https://msdn.microsoft.com/library/azure/dn931945.aspx)

## <a name="create-an-alert-rule-on-a-metric-with-the-azure-portal"></a>Creare una regola di avviso in base a una metrica con il portale di Azure

1. Nel [portale](https://portal.azure.com/), individuare la risorsa da monitorare e selezionarla.
2. Selezionare Avvisi nella sezione Monitoraggio.Select **Alerts** in the Monitoring section. Il testo e l'icona possono lievemente variare per le diverse risorse.  

   ![Monitoraggio](media/sql-database-insights-alerts-portal/Alerts.png)
  
3. Selezionare il pulsante **Nuova regola** di avviso per aprire la pagina **Crea regola.**
  ![Creare una regola](media/sql-database-insights-alerts-portal/create-rule.png)

4. Nella sezione **Condizione** fare clic su **Aggiungi**.
  ![Definire la condizione](media/sql-database-insights-alerts-portal/create-rule.png)
5. Nella pagina **Configura logica del segnale, selezionare** un segnale.
  ![Selezionare](media/sql-database-insights-alerts-portal/select-signal.png)il segnale .
6. Dopo aver selezionato un segnale, ad esempio la **percentuale della CPU**, viene visualizzata la pagina **Configura logica segnale.**
  ![Configurare la logica dei segnali](media/sql-database-insights-alerts-portal/configure-signal-logic.png)
7. In questa pagina configurare il tipo di soglia, l'operatore, il tipo di aggregazione, il valore di soglia, la granularità di aggregazione e la frequenza di valutazione. Quindi fare clic su **Fine**.
8. Nella **regola Crea**selezionare un **gruppo di azioni** esistente o crearne uno nuovo. Un gruppo di azioni consente di definire l'azione da eseguire quando si verifica una condizione di avviso.
  ![Definire il gruppo di azioniDefine action group](media/sql-database-insights-alerts-portal/action-group.png)

9. Definire un nome per la regola, fornire una descrizione facoltativa, scegliere un livello di gravità per la regola, scegliere se abilitare la regola al momento della creazione della regola e quindi fare clic su **Crea avviso regola** per creare l'avviso della regola metrica.

Entro 10 minuti, l'avviso è attivo e viene attivato come descritto in precedenza.

## <a name="next-steps"></a>Passaggi successivi

* Altre informazioni sulla [configurazione dei webhook negli avvisi](../azure-monitor/platform/alerts-webhooks.md).
