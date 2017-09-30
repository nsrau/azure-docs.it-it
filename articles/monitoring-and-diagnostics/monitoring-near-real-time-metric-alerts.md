---
title: Avvisi delle metriche near real time in Monitoraggio di Azure | Microsoft Docs
description: Gli avvisi delle metriche near real time consentono di monitorare le metriche delle risorse di Azure con una frequenza di 1 minuto.
author: snehithm
manager: kmadnani1
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/16/2017
ms.author: snmuvva
ms.custom: 
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: aeeb6c2fb87e6c19991ef243ee7230f4e8f4e251
ms.contentlocale: it-it
ms.lasthandoff: 09/25/2017

---

# <a name="near-real-time-metric-alerts-preview"></a>Avvisi metriche near real time (anteprima)
Monitoraggio di Azure supporta ora un nuovo tipo di avvisi delle metriche denominati Avvisi metriche near real time (anteprima). Questa funzionalità è attualmente in anteprima pubblica.
Questi avvisi sono diversi dagli avvisi delle metriche regolari per alcuni aspetti

- **Migliore latenza** - gli avvisi delle metriche near real time possono monitorare le modifiche in valori di metrica di 1 minuto.
- **Maggiore controllo delle condizioni delle metriche** - gli avvisi delle metriche near real time consentono agli utenti di definire regole di avviso più dettagliate. Gli avvisi ora supportano il monitoraggio dei valori massimo, minimo, medio e totale delle metriche.
- **Monitoraggio combinato di più metriche** - gli avvisi delle metriche near real time possono monitorare più metriche (attualmente due) con una singola regola. L'avviso viene attivato se entrambe le metriche violano le rispettive soglie per il periodo di tempo specificato.
- **Sistema di notifica modulare** - gli avvisi delle metriche near real time usano i [gruppi di azioni](monitoring-action-groups.md). Questa funzionalità consente agli utenti di creare azioni in modo modulare e riusarle per molte regole di avviso.

> [!NOTE]
> Gli avvisi delle metriche near real time sono attualmente disponibili in anteprima pubblica. L'esperienza utente e la funzionalità sono soggette a modifiche.
>

## <a name="what-resources-can-i-create-near-real-time-metric-alerts-for"></a>Per quali risorse è possibile creare gli avvisi delle metriche near real time?
L'elenco completo dei tipi di risorse che sono supportati dagli avvisi delle metriche near real time:

* Microsoft.ApiManagement/service
* Microsoft.Batch/batchAccounts
* Microsoft.Cache/Redis
* Microsoft.Compute/virtualMachines
* Microsoft.Compute/virtualMachineScaleSets
* Microsoft.DataFactory/factories
* Microsoft.DBforMySQL/servers
* Microsoft.DBforPostgreSQL/servers
* Microsoft.EventHub/namespaces
* Microsoft.Logic/workflows
* Microsoft.Network/applicationGateways
* Microsoft.Network/publicipaddresses
* Microsoft.Search/searchServices
* Microsoft.ServiceBus/namespaces
* Microsoft.Sql/servers/elasticpools
* Microsoft.StreamAnalytics/streamingjobs
* Microsoft.Timeseriesinsights
* Microsoft.CognitiveServices/accounts


## <a name="create-a-near-real-time-metric-alert"></a>Creare un avviso delle metriche near real time
Attualmente gli avvisi delle metriche near real time possono essere creati solo tramite il portale di Azure. Il supporto per la configurazione degli avvisi delle metriche near real time tramite PowerShell, l'interfaccia della riga di comando (CLI) e API REST di Monitoraggio di Azure sarà presto disponibile.

1. Nel [portale](https://portal.azure.com/), individuare la risorsa da monitorare e selezionarla. Questa risorsa deve essere uno dei tipi di risorse elencati nella [precedente sezione](#what-resources-can-i-create-near-real-time-metric-alerts-for). È inoltre possibile eseguire la stessa operazione per tutti i tipi di risorse supportate in modo centralizzato da Monitoraggio > Avvisi.

2. Selezionare **Avvisi** o **Regole di avviso** nella sezione MONITORAGGIO. Il testo e l'icona possono lievemente variare per le diverse risorse.
   ![Monitoraggio](./media/insights-alerts-portal/AlertRulesButton.png)

3. Fare clic sul comando **Add near real time metrics alert (preview)** (Aggiungi avvisi delle metriche near real time (anteprima)). Se il comando è disattivato, verificare che la risorsa sia selezionata nel filtro.

    ![Pulsante Add Near Real-Time Metrics Alert (Aggiungi avvisi delle metriche near real time)](./media/monitoring-near-real-time-metric-alerts/AddNRTAlertButton.png)

4. Assegnare alla regola di avviso un **Nome** e scegliere una **Descrizione**, che viene visualizzata anche nella notifica inviata tramite posta elettronica.
5. Selezionare la **Metrica** da monitorare e quindi scegliere una **Condizione**, un'**Aggregazione temporale** e un valore **Soglia** per la metrica. Facoltativamente selezionare un'altra **Metrica** da monitorare e quindi scegliere una **Condizione**, un'**Aggregazione temporale** e un valore **Soglia** per la seconda metrica. 

    ![Add Near Real-Time Metrics Alert1](./media/monitoring-near-real-time-metric-alerts/AddNRTAlert1.png) (Aggiungi avvisi delle metriche near real1) ![Add Near Real-Time Metrics Alert2](./media/monitoring-near-real-time-metric-alerts/AddNRTAlert2.png) (Aggiungi avvisi delle metriche near real2)
6. Scegliere il **Periodo** di tempo entro il quale la regola della metrica deve essere soddisfatta prima dell'attivazione dell'avviso. Se ad esempio si usa il periodo "Negli ultimi 5 minuti" e l'avviso deve rilevare la CPU superiore all'80%, (e la rete superiore ai 500 MB) l'avviso si attiva quando la CPU resta costantemente sopra all'80% per 5 minuti. Dopo la prima attivazione, l'avviso si attiverà di nuovo quando la CPU resta al di sotto dell'80% per 5 minuti. L'avviso viene valutato in base alla **frequenza di valutazione**


6. Scegliere la **gravità** appropriata nell'elenco a discesa.

7. Specificare se si desidera usare un **gruppo di azioni** nuovo o esistente.

8. Se si sceglie di creare un **nuovo** gruppo di azioni, assegnare al gruppo di azioni un nome e un nome breve, specificare le azioni (SMS, posta elettronica o Webhook) e inserire i rispettivi dettagli.


8. Al termine fare clic su **OK** per creare l'avviso.   

Dopo pochi minuti l'avviso è funzionante e si attiva come descritto in precedenza.

## <a name="managing-near-real-time-metric-alerts"></a>Gestione degli avvisi delle metriche near real time
Dopo aver creato un avviso, è possibile selezionarlo e:

* Visualizzare un grafico che mostra la soglia della metrica e i valori effettivi del giorno precedente.
* Modificarlo o eliminarlo.
* **Disabilitarlo** o **abilitarlo** per interrompere temporaneamente o riprendere la ricezione delle notifiche relative all'avviso.




