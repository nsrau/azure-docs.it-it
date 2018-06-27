---
title: Usare il portale di Azure per creare avvisi in versione classica per i servizi di Azure | Microsoft Docs
description: Attivare messaggi di posta elettronica o notifiche, chiamare URL di siti Web (webhook) o usare l'automazione quando vengono soddisfatte le condizioni specificate.
author: rboucher
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/23/2016
ms.author: robb
ms.component: alerts
ms.openlocfilehash: d0e5512eb3f963898ded6d7155f8b75cfb6ef911
ms.sourcegitcommit: d8ffb4a8cef3c6df8ab049a4540fc5e0fa7476ba
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/20/2018
ms.locfileid: "36287430"
---
# <a name="use-the-azure-portal-to-create-classic-metric-alerts-in-azure-monitor-for-azure-services"></a>Usare il portale di Azure per creare avvisi per le metriche in versione classica in Monitoraggio di Azure per i servizi di Azure 

> [!div class="op_single_selector"]
> * [di Microsoft Azure](insights-alerts-portal.md)
> * [PowerShell](insights-alerts-powershell.md)
> * [Interfaccia della riga di comando](insights-alerts-command-line-interface.md)
>

> [!NOTE]
> Questo articolo descrive come creare avvisi delle metriche classici di tipo precedente. Monitoraggio di Azure supporta ora i [nuovi avvisi delle metriche](monitoring-near-real-time-metric-alerts.md). 


Questo articolo descrive come configurare avvisi per le metriche di Azure in versione classica tramite il portale di Azure. 

È possibile ricevere un avviso in base alle metriche per i servizi di Azure oppure per gli eventi che si verificano in Azure.

* **Valori delle metriche**: l'avviso si attiva quando il valore di una metrica specifica supera una soglia assegnata per eccesso o per difetto. Vale a dire che si attiva sia quando la condizione viene inizialmente soddisfatta che quando tale condizione non è più soddisfatta.    

* **Eventi del log attività**: è possibile attivare un avviso per *ogni* evento o solo quando si verifica un determinato evento. Altre informazioni sugli [avvisi del log attività](monitoring-activity-log-alerts.md).

È possibile configurare un avviso classico delle metriche affinché esegua queste operazioni al momento dell'attivazione:

* Inviare notifiche di posta elettronica all'amministratore e ai coamministratori del servizio.
* Inviare un messaggio di posta elettronica a indirizzi aggiuntivi specificati.
* Chiamare un webhook.
* Avviare l'esecuzione di un runbook di Azure (solo dal portale di Azure).

È possibile configurare le regole di avviso per le metriche in versione classica e ottenere informazioni su di esse dalle posizioni seguenti: 

* [Portale di Azure](insights-alerts-portal.md)
* [PowerShell](insights-alerts-powershell.md)
* [Interfaccia della riga di comando di Azure](insights-alerts-command-line-interface.md)
* [API REST di Monitoraggio di Azure](https://msdn.microsoft.com/library/azure/dn931945.aspx)

## <a name="create-an-alert-rule-on-a-metric-with-the-azure-portal"></a>Creare una regola di avviso in base a una metrica con il portale di Azure
1. Nel [portale](https://portal.azure.com/) individuare la risorsa da monitorare e quindi selezionarla.

2. Nella sezione **MONITORAGGIO** selezionare **Avvisi (versione classica)**. Il testo e l'icona possono variare leggermente per le diverse risorse. Se l'opzione **Avvisi (versione classica)** non è disponibile, cercarla in **Avvisi** o **Regole di avviso**.

    ![Monitoraggio](./media/insights-alerts-portal/AlertRulesButton.png)

3. Selezionare il comando **Aggiungi avviso per la metrica (versione classica)** e compilare i campi.

    ![Aggiungi avviso](./media/insights-alerts-portal/AddAlertOnlyParamsPage.png)

4. Specificare un valore per **Nome** per la regola di avviso. Compilare il campo **Descrizione**. La descrizione viene visualizzata anche nei messaggi di posta elettronica di notifica.

5. In **Metrica** selezionare la metrica da monitorare. Selezionare i valori relativi a **Condizione** e **Soglia** per la metrica. Scegliere inoltre il **Periodo** di tempo entro il quale la regola della metrica deve essere soddisfatta prima dell'attivazione dell'avviso. Se, ad esempio, si usa il periodo "Negli ultimi 5 minuti" e l'avviso deve rilevare un utilizzo della CPU superiore all'80%, l'avviso si attiva quando la CPU resta costantemente sopra all'80% per 5 minuti. Dopo la prima attivazione, l'avviso si attiverà di nuovo quando la CPU resta al di sotto dell'80% per 5 minuti. La misurazione della metrica relativa alla CPU avviene ogni minuto.

6. Selezionare **Invia messaggio di posta elettronica a proprietari, collaboratori e lettori** se si vuole che gli amministratori e i coamministratori ricevano una notifica tramite posta elettronica quando si attiva l'avviso.

7. Per aggiungere altri indirizzi di posta elettronica ai quali inviare una notifica quando viene attivato l'avviso, usare il campo **Indirizzi di posta elettronica aggiuntivi dell'amministratore**. Separare gli indirizzi di posta elettronica con punti e virgola, con il formato seguente: *email@contoso.com; email2@contoso.com*

8. Inserire un URI valido nel campo **Webhook** per eseguire la chiamata quando viene attivato l'avviso.

9. Se si usa Automazione di Azure, è possibile selezionare un runbook da eseguire quando viene attivato l'avviso.

10. Fare clic su **OK** per creare l'avviso.   

Dopo pochi minuti l'avviso è funzionante e si attiva come descritto in precedenza.

## <a name="manage-your-alerts"></a>Gestire gli avvisi
Dopo aver creato un avviso, è possibile selezionarlo ed eseguire una delle attività seguenti:

* Visualizzare un grafico che mostra la soglia della metrica e i valori effettivi del giorno precedente.
* Modificarlo o eliminarlo.
* **Disabilitarlo** o **abilitarlo** per interrompere temporaneamente o riprendere la ricezione delle notifiche relative all'avviso.

## <a name="next-steps"></a>Passaggi successivi
* [Leggere una panoramica del monitoraggio di Azure](monitoring-overview.md) che include i tipi di informazioni che è possibile raccogliere e monitorare.
* Leggere altre informazioni sui [nuovi avvisi per le metriche](monitoring-near-real-time-metric-alerts.md).
* Leggere altre informazioni sulla [configurazione dei webhook negli avvisi](insights-webhooks-alerts.md).
* Leggere altre informazioni sulla [configurazione di avvisi per gli eventi del log attività](monitoring-activity-log-alerts.md).
* Leggere altre informazioni sui [runbook di Automazione di Azure](../automation/automation-starting-a-runbook.md).
* Leggere una [panoramica dei log di diagnostica](monitoring-overview-of-diagnostic-logs.md) e raccogliere metriche dettagliate e ad alta frequenza sul servizio.
* Leggere una [panoramica della raccolta delle metriche](insights-how-to-customize-monitoring.md) per verificare che il servizio sia disponibile e reattivo.
