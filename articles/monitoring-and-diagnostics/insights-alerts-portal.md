---
title: Creare avvisi per i servizi di Azure - Portale di Azure | Documentazione Microsoft
description: Attivare messaggi di posta elettronica o notifiche, chiamare URL di siti Web (webhook) o usare l'automazione quando vengono soddisfatte le condizioni specificate.
author: rboucher
manager: carmonm
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: f7457655-ced6-4102-a9dd-7ddf2265c0e2
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/23/2016
ms.author: robb
ms.translationtype: Human Translation
ms.sourcegitcommit: f41fbee742daf2107b57caa528e53537018c88c6
ms.openlocfilehash: 745a9c016bd037f1051025a2c5a468c3935e4550
ms.contentlocale: it-it
ms.lasthandoff: 03/31/2017

---
# <a name="create-metric-alerts-in-azure-monitor-for-azure-services---azure-portal"></a>Creare avvisi sulle metriche in Monitoraggio di Azure per i servizi di Azure: portale di Azure
> [!div class="op_single_selector"]
> * [Portale](insights-alerts-portal.md)
> * [PowerShell](insights-alerts-powershell.md)
> * [CLI](insights-alerts-command-line-interface.md)
>
>

## <a name="overview"></a>Panoramica
Questo articolo descrive come impostare gli avvisi sulle metriche di Azure tramite il portale di Azure.   

È possibile ricevere avvisi basati su metriche di monitoraggio o eventi nei servizi Azure.

* **Valori metrici** : l'avviso si attiva quando il valore di una specifica metrica supera una soglia assegnata per eccesso o difetto. Vale a dire che si attiva sia quando la condizione viene inizialmente soddisfatta e successivamente quando tale condizione non è più soddisfatta.    
* **Eventi del log attività**: è possibile attivare un avviso per *ogni* evento o solo quando si verifica un determinato evento. Per altre informazioni sugli avvisi sui log attività [fare clic qui](monitoring-activity-log-alerts.md)

È possibile configurare un avviso sulle metriche affinché esegua queste operazioni al momento dell'attivazione:

* inviare un messaggio di posta elettronica all'amministratore e ai coamministratori del servizio
* inviare un messaggio di posta elettronica ad altri indirizzi specificati
* chiamare un webhook
* avviare l'esecuzione di un runbook di Azure (solo dal portale di Azure)

È possibile configurare e ottenere informazioni sulle regole degli avvisi sulle metriche tramite

* [Portale di Azure](insights-alerts-portal.md)
* [PowerShell](insights-alerts-powershell.md)
* [interfaccia della riga di comando](insights-alerts-command-line-interface.md)
* [API REST di Monitoraggio di Azure](https://msdn.microsoft.com/library/azure/dn931945.aspx)

## <a name="create-an-alert-rule-on-a-metric-with-the-azure-portal"></a>Creare una regola di avviso in base a una metrica con il portale di Azure
1. Nel [portale](https://portal.azure.com/), individuare la risorsa da monitorare e selezionarla.

2. Selezionare **Avvisi** o **Regole di avviso** nella sezione MONITORAGGIO. Il testo e l'icona possono lievemente variare per le diverse risorse.  

    ![Monitoraggio](./media/insights-alerts-portal/AlertRulesButton.png)

3. Selezionare il comando **Aggiungi avviso** e compilare i campi.

    ![Aggiungi avviso](./media/insights-alerts-portal/AddAlertOnlyParamsPage.png)

4. Assegnare alla regola di avviso un **Nome** e scegliere una **Descrizione**, che viene visualizzata anche nella notifica inviata tramite posta elettronica.

5. Selezionare la **Metrica** da monitorare e quindi scegliere una **Condizione** e un valore **Soglia**. Scegliere inoltre il **Periodo** di tempo entro il quale la metrica deve essere soddisfatta prima dell'attivazione dell'avviso. Ad esempio, se si usa il periodo "PT5M" e l'avviso deve rilevare una CPU superiore all'80%, l'avviso si attiva quando la CPU resta costantemente sopra all'80% per 5 minuti. Dopo la prima attivazione, l'avviso si attiverà di nuovo quando la CPU resta al di sotto dell'80% per 5 minuti. La CPU viene misurata ogni minuto.   

6. Selezionare la casella di controllo **Invia messaggio a proprietari, collaboratori e lettori** se si desidera che gli amministratori e i coamministratori ricevano un messaggio di posta elettronica quando si attiva l'avviso.

7. Per aggiungere altri indirizzi di posta elettronica ai quali inviare una notifica quando viene attivato l'avviso, completare il campo **E-mail dell'amministratore aggiuntivo** . Separare gli indirizzi di posta elettronica con punti e virgola: *email@contoso.com;email2@contoso.com*

8. Inserire un URI valido nel campo **Webhook** per eseguire la chiamata quando viene attivato l'avviso.

9. Se si usa l'automazione di Azure, è possibile selezionare il runbook da eseguire quando viene generato l'avviso.

10. Al termine fare clic su **OK** per creare l'avviso.   

Dopo pochi minuti l'avviso è funzionante e si attiva come descritto in precedenza.

## <a name="managing-your-alerts"></a>Gestione degli avvisi
Dopo aver creato un avviso, è possibile selezionarlo e:

* Visualizzare un grafico che mostra la soglia della metrica e i valori effettivi del giorno precedente.
* Modificarlo o eliminarlo.
* **Disabilitarlo** o **abilitarlo** per interrompere temporaneamente o riprendere la ricezione delle notifiche relative all'avviso.

## <a name="next-steps"></a>Passaggi successivi
* [Leggere una panoramica del monitoraggio di Azure](monitoring-overview.md) che include anche i tipi di informazioni che è possibile raccogliere e monitorare.
* Altre informazioni sulla [configurazione dei webhook negli avvisi](insights-webhooks-alerts.md).
* Altre informazioni sulla [configurazione di avvisi sugli eventi del log attività](monitoring-activity-log-alerts.md).
* Altre informazioni sui [runbook di automazione di Azure](../automation/automation-starting-a-runbook.md).
* Leggere una [panoramica dei log di diagnostica](monitoring-overview-of-diagnostic-logs.md) e sulla raccolta di metriche dettagliate e ad alta frequenza sul servizio.
* Leggere una [panoramica della raccolta di metriche](insights-how-to-customize-monitoring.md) per verificare che il servizio sia disponibile e reattivo.

