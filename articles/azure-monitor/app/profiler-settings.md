---
title: Usare il riquadro delle impostazioni di Azure Application Insights Profiler | Microsoft Docs
description: Visualizzare lo stato del Profiler e avviare sessioni di profilatura
services: application-insights
documentationcenter: ''
author: cweining
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.reviewer: mbullwin
ms.date: 08/06/2018
ms.author: cweining
ms.openlocfilehash: 8c9fba14bd3f7d3b55a245f8e647f0eae1f8ef83
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58118426"
---
# <a name="configure-application-insights-profiler"></a>Configura Application Insights Profiler

## <a name="profiler-settings-pane"></a>Riquadro delle impostazioni del Profiler

Per aprire il riquadro impostazioni di Azure Application Insights Profiler, passare al riquadro delle prestazioni di Application Insights e quindi selezionare il pulsante **Profiler**.

![Configurare il riquadro Profiler][configure-profiler-entry]

Il riquadro **Configura Application Insights Profiler** contiene quattro funzionalità: 
* **Profila ora**: avvia sessioni di profilatura sessioni per tutte le app collegate a questa istanza di Application Insights.
* **App collegate**: elenca le applicazioni che inviano dati di profilatura a questa risorsa di Application Insights.
* **Sessioni in corso**: visualizza lo stato della sessione quando si seleziona **Profila ora**. 
* **Sessioni di profilatura recenti**: visualizza informazioni sulle ultime sessioni di profilatura.

![Profiler su richiesta][profiler-on-demand]

## <a name="app-service-environment"></a>Ambiente del servizio app
A seconda del modo in cui è configurato l'ambiente del servizio app di Azure, la chiamata per controllare lo stato dell'agente potrebbe essere bloccata. Nel riquadro potrebbe essere visualizzato un messaggio che indica che l'agente non è in esecuzione anche quando lo è. Per verificare, è possibile controllare il processo Web nell'applicazione. Se tutti i valori delle impostazioni delle app sono corretti e nell'applicazione è installata l'estensione del sito Application Insights, il Profiler è in esecuzione. Se l'applicazione riceve traffico sufficiente, le sessioni di profilatura recenti dovrebbero essere visualizzate in un elenco.

## <a id="profileondemand"></a> Attivare Profiler manualmente

È possibile attivare il Profiler manualmente con un solo clic. Si supponga di eseguire un test delle prestazioni Web. Sono necessarie tracce che consentano di comprendere le prestazioni dell'app Web in condizioni di carico. Il controllo sul momento in cui le tracce vengono acquisite è fondamentale poiché l'utente conosce il momento in cui il test di carico viene eseguito, ma l'intervallo di campionamento casuale potrebbe non rilevarlo.

Le sezioni seguenti illustrano il funzionamento di questo scenario:

### <a name="step-1-optional-generate-traffic-to-your-web-app-by-starting-a-web-performance-test"></a>Passaggio 1: (facoltativo) Generare traffico verso l'app Web avviando un test delle prestazioni Web

Se l'app Web include già il traffico in ingresso o se si desidera solo generare manualmente il traffico, ignorare questa sezione e procedere al passaggio 2.

1. Nel portale di Application Insights selezionare **Configura** > **Test delle prestazioni**. 

1. Per avviare un nuovo test delle prestazioni, selezionare il pulsante **Nuovo**.

   ![Creare un nuovo test delle prestazioni][create-performance-test]

1. Nel riquadro **Nuovo test delle prestazioni** configurare l'URL di destinazione del test. Accettare tutte le impostazioni predefinite e selezionare **Esegui test** per avviare l'esecuzione del test di carico.

    ![Configurare il test di carico][configure-performance-test]

    Inizialmente il nuovo test viene inserito in una coda e in seguito viene visualizzato con stato *in corso*.

    ![Test di carico inviato e accodato][load-test-queued]

    ![Test di carico in esecuzione e in corso][load-test-in-progress]

### <a name="step-2-start-a-profiler-on-demand-session"></a>Passaggio 2: Avviare una sessione di Profiler su richiesta

1. Quando il test è in esecuzione, avviare Profiler per acquisire tracce nell'app Web durante la ricezione del carico.

1. Passare al riquadro **Configura Application Insights Profiler**.


### <a name="step-3-view-traces"></a>Passaggio 3: Visualizzare le tracce

Al termine dell'esecuzione di Profiler, seguire le istruzioni visualizzate in base alla notifica per passare al riquadro Prestazioni e visualizzare le tracce.

## <a name="troubleshoot-the-profiler-on-demand-session"></a>Risolvere i problemi relativi alla sessione di Profiler su richiesta

In alcuni casi è possibile visualizzare un messaggio di errore di timeout di Profiler dopo una sessione su richiesta:

![Errore di timeout di Profiler][profiler-timeout]

È possibile ricevere questo errore per uno dei motivi seguenti:

* La sessione di Profiler su richiesta ha esito positivo, ma Application Insights ha impiegato un tempo più lungo del previsto per elaborare i dati raccolti.  

  Se i dati non vero elaborati entro 15 minuti, il portale visualizza un messaggio di timeout. Dopo un certo periodo di tempo, le tracce di Profiler vengono visualizzate. Se si riceve un messaggio di errore, ignorarlo per il momento. Microsoft sta lavorando per risolvere il problema.

* L'app Web dispone di una versione precedente dell'agente del Profiler priva della funzionalità di esecuzione su richiesta.  

  Se Application Insights Profiler è stato abilitato in precedenza, può essere necessario aggiornare l'agente del Profiler per iniziare a usare la funzionalità su richiesta.
  
Passare al riquadro **Impostazioni app** di Servizi app e verificare le impostazioni seguenti:
* **APPINSIGHTS_INSTRUMENTATIONKEY**: sostituire il valore con la chiave di strumentazione appropriata per Application Insights.
* **APPINSIGHTS_PORTALINFO**: ASP.NET
* **APPINSIGHTS_PROFILERFEATURE_VERSION**: 1.0.0

Se uno qualsiasi dei valori precedenti non è impostato, installare l'estensione del sito più recente seguendo questa procedura:

1. Andare al riquadro **Application Insights** nel portale di Servizi app.

    ![Abilitare Application Insights nel portale di Servizi app][enable-app-insights]

1. Se nel riquadro **Application Insights** compare un pulsante **Aggiorna**, selezionarlo per aggiornare l'estensione del sito Application Insights che consente di installare l'agente del Profiler più recente.

    ![Aggiornare l'estensione del sito][update-site-extension]

1. Per assicurarsi che Profiler sia attivato, selezionare **Modifica**, quindi selezionare **OK** per salvare le modifiche.

    ![Modificare e salvare Application Insights][change-and-save-appinsights]

1. Tornare al riquadro **Impostazioni app** del servizio app per assicurarsi che siano impostati i valori seguenti:
   * **APPINSIGHTS_INSTRUMENTATIONKEY**: sostituire il valore con la chiave di strumentazione appropriata per Application Insights.
   * **APPINSIGHTS_PORTALINFO**: ASP.NET 
   * **APPINSIGHTS_PROFILERFEATURE_VERSION**: 1.0.0

     ![Impostazioni dell'app per Profiler][app-settings-for-profiler]

1. Facoltativamente, selezionare **Estensioni**e quindi controllare la versione dell'estensione e determinare se è disponibile un aggiornamento.

    ![Verifica dell'aggiornamento dell'estensione][check-for-extension-update]

## <a name="next-steps"></a>Passaggi successivi
[Abilitare Profiler e visualizzare le tracce](profiler-overview.md?toc=/azure/azure-monitor/toc.json)

[profiler-on-demand]: ./media/profiler-settings/Profiler-on-demand.png
[configure-profiler-entry]: ./media/profiler-settings/configure-profiler-entry.png
[create-performance-test]: ./media/profiler-settings/new-performance-test.png
[configure-performance-test]: ./media/profiler-settings/configure-performance-test.png
[load-test-queued]: ./media/profiler-settings/load-test-queued.png
[load-test-in-progress]: ./media/profiler-settings/load-test-inprogress.png
[enable-app-insights]: ./media/profiler-settings/enable-app-insights-blade-01.png
[update-site-extension]: ./media/profiler-settings/update-site-extension-01.png
[change-and-save-appinsights]: ./media/profiler-settings/change-and-save-appinsights-01.png
[app-settings-for-profiler]: ./media/profiler-settings/appsettings-for-profiler-01.png
[check-for-extension-update]: ./media/profiler-settings/check-extension-update-01.png
[profiler-timeout]: ./media/profiler-settings/profiler-timeout.png
