---
title: Pianificazioni di manutenzione per il pool SQL Synapse
description: La pianificazione della manutenzione consente ai clienti di pianificare gli eventi di manutenzione pianificati necessari utilizzati da Azure Synapse Analytics per implementare nuove funzionalità, aggiornamenti e patch.
services: synapse-analytics
author: antvgski
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 02/02/2019
ms.author: anvang
ms.reviewer: jrasnick
ms.openlocfilehash: 43fc32e910c51e8b70e15aa49584a18e5b703fca
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/03/2020
ms.locfileid: "80631596"
---
# <a name="use-maintenance-schedules-to-manage-service-updates-and-maintenance"></a>Usare le pianificazioni della manutenzione per gestire gli aggiornamenti e la manutenzione dei servizi

La funzionalità di pianificazione della manutenzione integra le notifiche di manutenzione pianificata dell'integrità del servizio, monitoraggio controllo integrità delle risorse e il servizio di pianificazione della manutenzione per il pool SQL Synapse (data warehouse) all'interno di Azure Synapse Analytics.

È consigliabile utilizzare la pianificazione della manutenzione per scegliere un intervallo di tempo in cui è conveniente ricevere nuove funzionalità, aggiornamenti e patch. È necessario scegliere una finestra di manutenzione primaria e una secondaria entro un periodo di sette giorni, ogni finestra deve essere all'interno di intervalli di giorni separati.

Ad esempio, è possibile pianificare una finestra principale da sabato 22:00 a domenica 01:00 e quindi pianificare una finestra secondaria da mercoledì 19:00 alle 22:00. Se la manutenzione non può essere eseguita durante la finestra di manutenzione primaria, tenterà di ripetere la manutenzione durante la finestra di manutenzione secondaria. La manutenzione del servizio può talvolta verificarsi durante le finestre primarie e secondarie. Per garantire il completamento rapido di tutte le operazioni di manutenzione, DW400c e livelli inferiori del data warehouse potrebbero completare la manutenzione all'esterno di una finestra di manutenzione designata.

Tutte le istanze del data warehouse appena create avranno una pianificazione di manutenzione definita dal sistema applicata durante la distribuzione. È possibile modificare la pianificazione non appena la distribuzione viene completata.

Anche se una finestra di manutenzione può essere compresa tra tre e otto ore, ciò non significa che il data warehouse sarà offline per la durata. La manutenzione può avvenire in qualsiasi momento all'interno di tale finestra e ci si dovrebbe aspettare una singola disconnessione durante quel periodo della durata di 5 -6 min come il servizio distribuisce nuovo codice al data warehouse. DW400c e inferiore possono verificarsi più brevi perdite di connettività in vari momenti durante la finestra di manutenzione. All'avvio della manutenzione, tutte le sessioni attive verranno annullate e le transazioni di cui non è stato eseguito il commit verranno annullate. Per ridurre al minimo i tempi di inattività delle istanze, assicurarsi che non siano presenti transazioni con esecuzione prolungata nel data warehouse prima del periodo di manutenzione scelto.

Tutte le operazioni di manutenzione devono terminare all'interno delle finestre di manutenzione specificate, a meno che non sia necessario distribuire un aggiornamento sensibile al tempo. Se il data warehouse è sospeso durante una manutenzione pianificata, verrà aggiornato durante l'operazione di ripresa. Riceverai una notifica immediatamente dopo il completamento della manutenzione del data warehouse.

## <a name="alerts-and-monitoring"></a>Avvisi e monitoraggio

L'integrazione con le notifiche sull'integrità dei servizi e il monitoraggio per il controllo dell'integrità delle risorse consente ai clienti di essere aggiornati sulle attività di manutenzione imminenti. Questa automazione sfrutta I vantaggi di Monitoraggio di Azure.This automation takes advantage of Azure Monitor. È possibile decidere come si vuole ricevere una notifica degli eventi di manutenzione imminenti. Inoltre, è possibile scegliere quali flussi automatici consentono di gestire i tempi di inattività e ridurre al minimo l'impatto operativo.

Una notifica di anticipo di 24 ore precede tutti gli eventi di manutenzione che non sono per i livelli DWC400c e inferiore.

> [!NOTE]
> Nel caso in cui ci sia richiesto di distribuire un aggiornamento critico per il tempo, i tempi di notifica avanzati possono essere significativamente ridotti.

Se hai ricevuto la notifica di anticipo che la manutenzione avrà luogo, ma la manutenzione non può essere eseguita durante il periodo di tempo nella notifica, riceverai una notifica di annullamento. La manutenzione verrà quindi ripresa durante il successivo periodo di manutenzione pianificato.

Tutti gli eventi di manutenzione attivi verranno visualizzati nella sezione **Integrità dei servizi - Manutenzione pianificata**. La cronologia dell'integrità dei servizi include un record completo degli eventi precedenti. La manutenzione può essere monitorata tramite il dashboard del portale di controllo dell'integrità dei servizi di Azure durante un evento attivo.

### <a name="maintenance-schedule-availability"></a>Disponibilità della pianificazione della manutenzione

Anche se la pianificazione della manutenzione non è disponibile nell'area selezionata, è possibile visualizzare e modificare la pianificazione di manutenzione in qualsiasi momento. Quando la pianificazione della manutenzione diventa disponibile nella propria area geografica, la pianificazione identificata diventerà immediatamente attiva nel pool Synapse SQL.

## <a name="view-a-maintenance-schedule"></a>Visualizzare una pianificazione della manutenzione

Per impostazione predefinita, a tutte le istanze del data warehouse appena create viene applicata una finestra di manutenzione primaria e secondaria di otto ore durante la distribuzione. Come indicato in precedenza, è possibile modificare le finestre al termine della distribuzione. Nessuna manutenzione verrà eseguita al di fuori delle finestre di manutenzione specificate senza preavviso.

La procedura seguente illustra come visualizzare la pianificazione della manutenzione applicata al pool Synapse SQL:

1. Accedere al [portale di Azure](https://portal.azure.com/).
2. Selezionare il pool SQL Synapse che si desidera visualizzare.
3. Il pool Synapse SQL selezionato viene aperto nel pannello Panoramica.The selected Synapse SQL pool opens on the overview blade. La pianificazione della manutenzione applicata al data warehouse viene visualizzata sotto **Pianificazione manutenzione**.

![Pannello Panoramica](./media/maintenance-scheduling/clear-overview-blade.PNG)

## <a name="change-a-maintenance-schedule"></a>Modificare una pianificazione della manutenzione

Una pianificazione della manutenzione può essere aggiornata o modificata in qualsiasi momento. Se per l'istanza selezionata è in corso un ciclo di manutenzione attivo, le impostazioni verranno salvate. Diventeranno attive durante il successivo periodo di manutenzione identificato. [Altre informazioni](../../service-health/resource-health-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) sul monitoraggio del data warehouse durante un evento di manutenzione attiva.

## <a name="identifying-the-primary-and-secondary-windows"></a>Identificazione delle finestre primarie e secondarie

Le finestre primarie e secondarie devono avere intervalli di giorni separati. Un esempio è una finestra primaria da martedì a giovedì e una finestra secondaria da sabato a domenica.

La procedura seguente illustra come modificare la pianificazione della manutenzione per il pool Synapse SQL:

1. Accedere al [portale di Azure](https://portal.azure.com/).
2. Selezionare il pool SQL Synapse che si desidera aggiornare. La pagina viene aperta nel pannello Panoramica.
Aprire la pagina per le impostazioni di pianificazione della manutenzione selezionando il collegamento Riepilogo pianificazione manutenzione nel pannello Panoramica.Open the page for maintenance schedule settings by selecting the **Maintenance Schedule summary link** on the overview blade. In alternativa, selezionare l'opzione **Pianificazione della manutenzione** nel menu delle risorse a sinistra.

    ![Opzioni del pannello Panoramica](./media/maintenance-scheduling/maintenance-change-option.png)

3. Scegliere l'intervallo di date preferito per la finestra di manutenzione primaria usando le opzioni nella parte superiore della pagina. Con questa selezione si stabilisce se la finestra primaria è prevista nei giorni feriali o durante il fine settimana. La selezione aggiornerà di conseguenza i valori dell'elenco a discesa.
Durante l'anteprima alcune aree potrebbero non supportare ancora il set completo di opzioni **Giorno**.

   ![Pannello delle impostazioni di manutenzione](./media/maintenance-scheduling/maintenance-settings-page.png)

4. Scegliere le finestre di manutenzione primarie e secondarie desiderate usando gli elenchi a discesa seguenti:
   - **Giorno**: giorno preferito per eseguire la manutenzione durante la finestra selezionata.
   - **Ora di inizio**: orario di inizio preferito per la finestra di manutenzione.
   - **Intervallo di tempo**: durata preferita della finestra temporale.

   L'area **Riepilogo della pianificazione** nella parte inferiore del pannello viene aggiornata in base ai valori selezionati.
  
5. Selezionare **Salva**. Viene visualizzato un messaggio, che conferma che la nuova pianificazione è ora attiva.

   Se si salva una pianificazione in un'area che non supporta la pianificazione della manutenzione, viene visualizzato il seguente messaggio. Le impostazioni vengono salvate e diventeranno attive quando la funzione sarà disponibile nell'area selezionata.

   ![Messaggio sulla disponibilità in base all'area geografica](./media/maintenance-scheduling/maintenance-not-active-toast.png)

## <a name="next-steps"></a>Passaggi successivi

- [Altre informazioni](../../azure-monitor/platform/alerts-metric.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) sulla creazione, visualizzazione e gestione degli avvisi con Monitoraggio di Azure.
- [Altre informazioni](../..//azure-monitor/platform/alerts-log-webhook.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) sulle azioni webhook per le regole di avviso relative ai log.
- [Altre informazioni](../..//azure-monitor/platform/action-groups.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) Creazione e gestione di gruppi di azioni.
- [Altre informazioni](../../service-health/service-health-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) su Integrità dei servizi di Azure.Learn more about Azure Service Health.
