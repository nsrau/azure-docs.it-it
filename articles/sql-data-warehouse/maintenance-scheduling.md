---
title: Pianificazioni di manutenzione di Azure
description: La pianificazione della manutenzione consente ai clienti di programmare tutti gli eventi di manutenzione pianificata necessari usati da Azure SQL Data Warehouse per distribuire nuovi aggiornamenti, patch e funzionalità.
services: sql-data-warehouse
author: antvgski
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: design
ms.date: 07/16/2019
ms.author: anvang
ms.reviewer: jrasnick
ms.openlocfilehash: 91b202f8a5df841fa3d6aa1f0903999b395f8137
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73686060"
---
# <a name="use-maintenance-schedules-to-manage-service-updates-and-maintenance"></a>Usare le pianificazioni della manutenzione per gestire gli aggiornamenti e la manutenzione dei servizi

Le pianificazioni di manutenzione sono ora disponibili in tutte le aree Azure SQL Data Warehouse. La funzionalità pianificazione della manutenzione integra le notifiche di manutenzione pianificata per l'integrità dei servizi, il monitoraggio Integrità risorse controllo e il servizio di pianificazione Azure SQL Data Warehouse manutenzione.

La pianificazione della manutenzione consente di scegliere una finestra temporale per ricevere al momento opportuno le nuove funzionalità, gli aggiornamenti e le patch. Si sceglie una finestra di manutenzione primaria e secondaria entro un periodo di sette giorni. Per usare questa funzione è necessario identificare una finestra primaria e secondaria all'interno di intervalli giornalieri separati.

È ad esempio possibile pianificare una finestra primaria da sabato 22:00 a domenica 01:00, quindi pianificare una finestra secondaria di mercoledì 19:00 a 22:00. Se SQL Data Warehouse non riesce a eseguire operazioni di manutenzione durante la finestra di manutenzione primaria, cercherà di effettuare tale operazione nell'ambito della finestra di manutenzione secondaria. La manutenzione del servizio può verificarsi durante le finestre primarie e secondarie. Per garantire il completamento rapido di tutte le operazioni di manutenzione, DW400 (c) e livelli di data warehouse inferiori possono completare la manutenzione al di fuori di una finestra di manutenzione designata.

Tutte le istanze di Azure SQL Data Warehouse appena create avranno una pianificazione di manutenzione definita dal sistema applicata durante la distribuzione. È possibile modificare la pianificazione non appena la distribuzione viene completata.

Ogni finestra di manutenzione può essere compresa tra tre e otto ore. La manutenzione può avvenire in qualsiasi momento nell'ambito della finestra All'avvio della manutenzione verranno annullate tutte le sessioni attive e verrà eseguito il rollback delle transazioni di cui non è stato eseguito il commit. È necessario prevedere più brevi perdite di connettività quando il servizio distribuisce nuovo codice nel data warehouse. Si riceverà una notifica subito dopo il completamento della manutenzione del data warehouse.

 Tutte le operazioni di manutenzione devono terminare entro le finestre di manutenzione pianificate. Nessuna manutenzione verrà eseguita al di fuori delle finestre di manutenzione specificate senza preavviso. Se il data warehouse è sospeso durante una manutenzione pianificata, verrà aggiornato durante l'operazione di ripresa. 

## <a name="alerts-and-monitoring"></a>Avvisi e monitoraggio

L'integrazione con le notifiche sull'integrità dei servizi e il monitoraggio per il controllo dell'integrità delle risorse consente ai clienti di essere aggiornati sulle attività di manutenzione imminenti. La nuova automazione si avvale di Monitoraggio di Azure. È possibile decidere come si vuole ricevere una notifica degli eventi di manutenzione imminenti. Inoltre, è possibile scegliere i flussi automatizzati che consentiranno di gestire i tempi di inattività e ridurre al minimo l'effetto operativo.

Una notifica di anticipo di 24 ore precede tutti gli eventi di manutenzione che non sono relativi al DWC400c e ai livelli inferiori. Per ridurre al minimo i tempi di inattività delle istanze, assicurarsi che non siano presenti transazioni con esecuzione prolungata nel data warehouse prima del periodo di manutenzione scelto.

> [!NOTE]
> Nel caso in cui sia necessario distribuire un aggiornamento critico del tempo, i tempi di notifica avanzati possono essere notevolmente ridotti.

Se si riceve un preavviso prima dell'esecuzione della manutenzione, ma SQL Data Warehouse non riesce a eseguire la manutenzione nell'orario prestabilito, si riceverà una notifica di annullamento. La manutenzione verrà quindi ripresa durante il successivo periodo di manutenzione pianificato.

Tutti gli eventi di manutenzione attivi verranno visualizzati nella sezione **Integrità dei servizi - Manutenzione pianificata**. La cronologia dell'integrità dei servizi include un record completo degli eventi precedenti. La manutenzione può essere monitorata tramite il dashboard del portale di controllo dell'integrità dei servizi di Azure durante un evento attivo.

### <a name="maintenance-schedule-availability"></a>Disponibilità della pianificazione della manutenzione

Anche se la pianificazione della manutenzione non è disponibile nell'area selezionata, è possibile visualizzare e modificare la pianificazione di manutenzione in qualsiasi momento. Quando la pianificazione della manutenzione sarà disponibile nella propria area, la pianificazione identificata diventerà immediatamente attiva nel data warehouse.

## <a name="view-a-maintenance-schedule"></a>Visualizzare una pianificazione della manutenzione 

### <a name="portal"></a>di Microsoft Azure

Per impostazione predefinita, a tutte le istanze di Azure SQL Data Warehouse create di recente viene applicata una finestra di manutenzione primaria e secondaria di otto ore durante la distribuzione. Come indicato in precedenza, è possibile modificare le finestre non appena la distribuzione è completa. Nessuna manutenzione verrà eseguita al di fuori delle finestre di manutenzione specificate senza preavviso.

Per visualizzare la pianificazione della manutenzione applicata al data warehouse, completare i passaggi seguenti:

1.  Accedere al [portale di Azure](https://portal.azure.com/).
2.  Selezionare il data warehouse che si vuole visualizzare. 
3.  Il data warehouse selezionato verrà aperto nel pannello Panoramica. La pianificazione di manutenzione applicata al data warehouse viene visualizzata sotto la **pianificazione della manutenzione**.

![Pannello Panoramica](media/sql-data-warehouse-maintenance-scheduling/clear-overview-blade.PNG)

## <a name="change-a-maintenance-schedule"></a>Modificare una pianificazione della manutenzione 

### <a name="portal"></a>di Microsoft Azure
Una pianificazione della manutenzione può essere aggiornata o modificata in qualsiasi momento. Se per l'istanza selezionata è in corso un ciclo di manutenzione attivo, le impostazioni verranno salvate. Diventeranno attive durante il successivo periodo di manutenzione identificato. [Altre informazioni](https://docs.microsoft.com/azure/service-health/resource-health-overview) sul monitoraggio del data warehouse durante un evento di manutenzione attiva. 

### <a name="identifying-the-primary-and-secondary-windows"></a>Identificazione delle finestre primarie e secondarie

Le finestre primarie e secondarie devono avere intervalli di giorni separati. Un esempio è una finestra primaria da martedì a giovedì e una finestra secondaria da sabato a domenica.

Per modificare la pianificazione della manutenzione per il data warehouse, completare i passaggi seguenti:
1.  Accedere al [portale di Azure](https://portal.azure.com/).
2.  Selezionare il data warehouse che si vuole aggiornare. La pagina viene aperta nel pannello Panoramica. 
3.  Aprire la pagina delle impostazioni di pianificazione di manutenzione selezionando il collegamento di **riepilogo Pianificazione della manutenzione (anteprima)** nel pannello Panoramica. In alternativa, selezionare l'opzione **Pianificazione della manutenzione** nel menu delle risorse a sinistra.  

    ![Opzioni del pannello Panoramica](media/sql-data-warehouse-maintenance-scheduling/maintenance-change-option.png)

4. Scegliere l'intervallo di date preferito per la finestra di manutenzione primaria usando le opzioni nella parte superiore della pagina. Con questa selezione si stabilisce se la finestra primaria è prevista nei giorni feriali o durante il fine settimana. La selezione aggiornerà di conseguenza i valori dell'elenco a discesa. Durante l'anteprima alcune aree potrebbero non supportare ancora il set completo di opzioni **Giorno**.

   ![Pannello delle impostazioni di manutenzione](media/sql-data-warehouse-maintenance-scheduling/maintenance-settings-page.png)

5. Scegliere le finestre di manutenzione primarie e secondarie desiderate usando gli elenchi a discesa seguenti:
   - **Giorno**: giorno preferito per eseguire la manutenzione durante la finestra selezionata.
   - **Ora di inizio**: orario di inizio preferito per la finestra di manutenzione.
   - **Intervallo di tempo**: durata preferita della finestra temporale.

   L'area **Riepilogo della pianificazione** nella parte inferiore del pannello viene aggiornata in base ai valori selezionati. 
  
6. Selezionare **Salva**. Viene visualizzato un messaggio, che conferma che la nuova pianificazione è ora attiva. 

   Se si salva una pianificazione in un'area che non supporta la pianificazione della manutenzione, viene visualizzato il seguente messaggio. Le impostazioni vengono salvate e diventeranno attive quando la funzione sarà disponibile nell'area selezionata.    

   ![Messaggio sulla disponibilità in base all'area geografica](media/sql-data-warehouse-maintenance-scheduling/maintenance-notactive-toast.png)

## <a name="next-steps"></a>Passaggi successivi
- [Altre informazioni](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-usage) sulla creazione, visualizzazione e gestione degli avvisi con Monitoraggio di Azure.
- [Altre informazioni](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-log-webhook) sulle azioni webhook per le regole di avviso relative ai log.
- [Altre informazioni](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups) Creazione e gestione di gruppi di azioni.
- [Altre informazioni](https://docs.microsoft.com/azure/service-health/service-health-overview) sull'integrità dei servizi di Azure.