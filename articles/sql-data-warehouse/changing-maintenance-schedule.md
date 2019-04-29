---
title: Pianificazioni della manutenzione di Azure (anteprima) | Microsoft Docs
description: La pianificazione della manutenzione consente ai clienti di programmare tutti gli eventi di manutenzione pianificata necessari usati da Azure SQL Data Warehouse per distribuire nuovi aggiornamenti, patch e funzionalità.
services: sql-data-warehouse
author: antvgski
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: design
ms.date: 04/03/2019
ms.author: anvang
ms.reviewer: jrasnick
ms.openlocfilehash: 4bc1867f0dd18a101b2e764970de4e1904b960e0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60680417"
---
# <a name="change-a-maintenance-schedule"></a>Modificare una pianificazione della manutenzione 

## <a name="portal"></a>Portale
Una pianificazione della manutenzione può essere aggiornata o modificata in qualsiasi momento. Se per l'istanza selezionata è in corso un ciclo di manutenzione attivo, le impostazioni verranno salvate. Diventeranno attive durante il successivo periodo di manutenzione identificato. [Altre informazioni](https://docs.microsoft.com/azure/service-health/resource-health-overview) sul monitoraggio del data warehouse durante un evento di manutenzione attiva. 

Per usare le pianificazioni di manutenzione, è necessario selezionare due finestre di manutenzione durante un periodo di sette giorni. Ogni finestra di manutenzione può essere di tre-otto ore. La manutenzione può verificarsi in qualsiasi momento all'interno di una finestra di manutenzione, ma non viene eseguita all'esterno di intervalli di tempo senza preavviso. In più, si verificherà una breve perdita di connettività mentre il servizio distribuisce nuovo codice nel data warehouse. 

## <a name="identifying-the-primary-and-secondary-windows"></a>Identificazione delle finestre primarie e secondarie

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
- [Altre informazioni](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-log-webhook) sulle azioni webhook per le regole di avviso relative ai log.
- [Altre informazioni](https://docs.microsoft.com/azure/service-health/service-health-overview) sull'integrità dei servizi di Azure.


