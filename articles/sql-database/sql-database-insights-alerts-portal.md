---
title: Usare il portale di Azure per creare avvisi per il database SQL | Documentazione Microsoft
description: Usare il portale di Azure per creare avvisi per il database SQL in grado di attivare notifiche o eventi di automazione quando vengono soddisfatte le condizioni specificate.
author: aamalvea
manager: jhubbard
editor: 
services: sql-database
documentationcenter: 
ms.assetid: f7457655-ced6-4102-a9dd-7ddf2265c0e2
ms.service: sql-database
ms.custom: monitor and tune
ms.workload: On Demand
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/06/2017
ms.author: aamalvea
ms.openlocfilehash: fd21c9b5e573ac6a47fef88c2a9d31c52618ecb8
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/31/2017
---
# <a name="use-azure-portal-to-create-alerts-for-azure-sql-database-and-data-warehouse"></a>Usare il portale di Azure per creare avvisi per il database SQL di Azure e il data warehouse

## <a name="overview"></a>Panoramica
Questo articolo descrive come impostare gli avvisi per il database SQL di Azure e il data warehouse usando il portale di Azure. Questo articolo include anche le procedure consigliate per impostare i periodi di avviso.    

È possibile ricevere avvisi basati su metriche di monitoraggio o eventi nei servizi Azure.

* **Valori metrici** : l'avviso si attiva quando il valore di una specifica metrica supera una soglia assegnata per eccesso o difetto. Vale a dire che si attiva sia quando la condizione viene inizialmente soddisfatta e successivamente quando tale condizione non è più soddisfatta.    
* **Eventi del log attività** : è possibile attivare un avviso per *ogni* evento o solo quando si verifica un determinato numero di eventi.

È possibile configurare un avviso affinché esegua le operazioni seguenti al momento dell'attivazione:

* inviare un messaggio di posta elettronica all'amministratore e ai coamministratori del servizio
* inviare un messaggio di posta elettronica ad altri indirizzi specificati
* chiamare un webhook

È possibile configurare e ottenere informazioni sulle regole degli avvisi tramite

* [Portale di Azure](../monitoring-and-diagnostics/insights-alerts-portal.md)
* [PowerShell](../monitoring-and-diagnostics/insights-alerts-powershell.md)
* [interfaccia della riga di comando](../monitoring-and-diagnostics/insights-alerts-command-line-interface.md)
* [API REST di Monitoraggio di Azure](https://msdn.microsoft.com/library/azure/dn931945.aspx)

## <a name="create-an-alert-rule-on-a-metric-with-the-azure-portal"></a>Creare una regola di avviso in base a una metrica con il portale di Azure
1. Nel [portale](https://portal.azure.com/), individuare la risorsa da monitorare e selezionarla.
2. Questo passaggio per il database SQL e i pool elastici è diverso rispetto a quello per SQL Data Warehouse: 

   - **SOLO PER IL DATABASE SQL E I POOL ELASTICI**: selezionare **Avvisi** o **Regole di avviso** nella sezione MONITORAGGIO. Il testo e l'icona possono lievemente variare per le diverse risorse.  
   
     ![Monitoraggio](../monitoring-and-diagnostics/media/insights-alerts-portal/AlertRulesButton.png)
  
   - **SOLO PER SQL DATA WAREHOUSE**: selezionare **Monitoraggio** nella sezione ATTIVITÀ COMUNI. Fare clic sul grafo **Utilizzo di DWU**.

     ![ATTIVITÀ COMUNI](../monitoring-and-diagnostics/media/insights-alerts-portal/AlertRulesButtonDW.png)

3. Selezionare il comando **Aggiungi avviso** e compilare i campi.
   
    ![Aggiungi avviso](../monitoring-and-diagnostics/media/insights-alerts-portal/AddDBAlertPage.png)
4. Assegnare alla regola di avviso un **Nome** e scegliere una **Descrizione**, che viene visualizzata anche nella notifica inviata tramite posta elettronica.
5. Selezionare la **Metrica** da monitorare e quindi scegliere una **Condizione** e un valore **Soglia**. Scegliere inoltre il **Periodo** di tempo entro il quale la regola della metrica deve essere soddisfatta prima dell'attivazione dell'avviso. Ad esempio, se si usa il periodo "PT5M" e l'avviso deve rilevare una CPU superiore all'80%, l'avviso si attiva quando la CPU **media** resta sopra all'80% per 5 minuti. Dopo la prima attivazione, l'avviso si attiverà di nuovo quando la CPU media è al di sotto dell'80% per più di 5 minuti. La CPU viene misurata ogni minuto. Consultare la tabella seguente per gli intervalli di tempo supportati e il tipo di aggregazione usato da ogni avviso. Non tutti gli avvisi usano il valore medio.   
6. Selezionare la casella di controllo **Invia messaggio a proprietari, collaboratori e lettori** se si desidera che gli amministratori e i coamministratori ricevano un messaggio di posta elettronica quando si attiva l'avviso.
7. Per aggiungere altri indirizzi di posta elettronica ai quali inviare una notifica quando viene attivato l'avviso, completare il campo **E-mail dell'amministratore aggiuntivo** . Separare gli indirizzi di posta elettronica con punti e virgola: *email@contoso.com;email2@contoso.com*
8. Inserire un URI valido nel campo **Webhook** per eseguire la chiamata quando viene attivato l'avviso.
9. Al termine fare clic su **OK** per creare l'avviso.   

Dopo pochi minuti l'avviso è funzionante e si attiva come descritto in precedenza.

## <a name="managing-your-alerts"></a>Gestione degli avvisi
Dopo aver creato un avviso, è possibile selezionarlo e:

* Visualizzare un grafico che mostra la soglia della metrica e i valori effettivi del giorno precedente.
* Modificarlo o eliminarlo.
* **Disabilitarlo** o **abilitarlo** per interrompere temporaneamente o riprendere la ricezione delle notifiche relative all'avviso.


## <a name="sql-database-alert-values"></a>Valori degli avvisi per il database SQL

| Tipo di risorsa | Nome della metrica | Nome descrittivo | Tipo di aggregazione | Intervallo di tempo minimo per l'avviso|
| --- | --- | --- | --- | --- |
| Database SQL | cpu_percent | Percentuale CPU | Media | 5 minuti |
| Database SQL | physical_data_read_percent | Percentuale di I/O di dati | Media | 5 minuti |
| Database SQL | log_write_percent | Percentuale I/O registro | Media | 5 minuti |
| Database SQL | dtu_consumption_percent | Percentuale di DTU | Media | 5 minuti |
| Database SQL | storage | Dimensioni totali database | Massima | 30 minuti |
| Database SQL | connection_successful | Connessioni riuscite | Totale | 10 minuti |
| Database SQL | connection_failed | Connessioni non riuscite | Totale | 10 minuti |
| Database SQL | blocked_by_firewall | Blocco da parte del firewall | Totale | 10 minuti |
| Database SQL | deadlock | Deadlock | Totale | 10 minuti |
| Database SQL | storage_percent | Percentuale di dimensioni del database | Massima | 30 minuti |
| Database SQL | xtp_storage_percent | Percentuale archiviazione OLTP interna alla memoria (anteprima) | Media | 5 minuti |
| Database SQL | workers_percent | Percentuale ruoli di lavoro | Media | 5 minuti |
| Database SQL | sessions_percent | Percentuale sessioni | Media | 5 minuti |
| Database SQL | dtu_limit | Limite DTU | Media | 5 minuti |
| Database SQL | dtu_used | Uso DTU | Media | 5 minuti |
||||||
| Pool elastico | cpu_percent | Percentuale CPU | Media | 10 minuti |
| Pool elastico | physical_data_read_percent | Percentuale di I/O di dati | Media | 10 minuti |
| Pool elastico | log_write_percent | Percentuale I/O registro | Media | 10 minuti |
| Pool elastico | dtu_consumption_percent | Percentuale di DTU | Media | 10 minuti |
| Pool elastico | storage_percent | Percentuale archiviazione | Media | 10 minuti |
| Pool elastico | workers_percent | Percentuale ruoli di lavoro | Media | 10 minuti |
| Pool elastico | eDTU_limit | Limite eDTU | Media | 10 minuti |
| Pool elastico | storage_limit | Limite archiviazione | Media | 10 minuti |
| Pool elastico | eDTU_used | Uso eDTU | Media | 10 minuti |
| Pool elastico | storage_used | Uso archiviazione | Media | 10 minuti |
||||||               
| SQL Data Warehouse | cpu_percent | Percentuale CPU | Media | 10 minuti |
| SQL Data Warehouse | physical_data_read_percent | Percentuale di I/O di dati | Media | 10 minuti |
| SQL Data Warehouse | storage | Dimensioni totali database | Massima | 10 minuti |
| SQL Data Warehouse | connection_successful | Connessioni riuscite | Totale | 10 minuti |
| SQL Data Warehouse | connection_failed | Connessioni non riuscite | Totale | 10 minuti |
| SQL Data Warehouse | blocked_by_firewall | Blocco da parte del firewall | Totale | 10 minuti |
| SQL Data Warehouse | service_level_objective | Obiettivo del livello di servizio del database | Totale | 10 minuti |
| SQL Data Warehouse | dwu_limit | Limite DWU | Massima | 10 minuti |
| SQL Data Warehouse | dwu_consumption_percent | Percentuale DWU | Media | 10 minuti |
| SQL Data Warehouse | dwu_used | Uso DWU | Media | 10 minuti |
||||||


## <a name="next-steps"></a>Passaggi successivi
* [Leggere una panoramica del monitoraggio di Azure](../monitoring-and-diagnostics/monitoring-overview.md) che include anche i tipi di informazioni che è possibile raccogliere e monitorare.
* Altre informazioni sulla [configurazione dei webhook negli avvisi](../monitoring-and-diagnostics/insights-webhooks-alerts.md).
* Leggere una [panoramica dei log di diagnostica](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md) e sulla raccolta di metriche dettagliate e ad alta frequenza sul servizio.
* Leggere una [panoramica della raccolta di metriche](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md) per verificare che il servizio sia disponibile e reattivo.
