---
title: Pannello delle impostazioni del profiler di Azure Application Insights | Microsoft Docs
description: Visualizzare lo stato del profiler e avviare sessioni di profilatura
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.reviewer: cawa
ms.date: 08/06/2018
ms.author: mbullwin
ms.openlocfilehash: d96b4a99b2ea66cdeff43f06c1789dd133c2c31a
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/01/2018
ms.locfileid: "52723007"
---
# <a name="configure-application-insights-profiler"></a>Configura Application Insights Profiler

## <a name="profiler-settings-page"></a>Pagina delle impostazioni del profiler

La pagina delle impostazioni del profiler può essere aperta dalla pagina Prestazioni di Application Insights premendo il pulsante **Profiler**.

![Configurare una voce del riquadro di Profiler][configure-profiler-entry]

La pagina Configura Application Insights Profiler contiene quattro funzionalità: 
1. **Profile Now** (Profila adesso): facendo clic su questo pulsante si avviano le sessioni di profilatura per tutte le app che sono collegate a questa istanza di Application Insights
1. **Linked apps** (App collegate): elenco di applicazioni che inviano il profiler a questa risorsa di Application Insights
1. **Sessioni in corso** : quando si preme **Profile Now** (Profila adesso), lo stato della sessione verrà visualizzato qui
1. **Sessioni di profilatura recenti**: mostra le informazioni sulle ultime sessioni di profilatura.

![Profiler su richiesta][profiler-on-demand]

## <a name="app-service-environments-ase"></a>Ambienti del servizio app
A seconda del modo in cui l'ambiente del servizio app è configurato, è possibile bloccare la chiamata per controllare lo stato dell'agente. Questa pagina indicherà che l'agente non è in esecuzione anche se in effetti lo è. Per verificare, è possibile controllare il processo Web nell'applicazione. Se tutte le impostazioni dell'app sono impostate correttamente e l'estensione del sito Application Insights è installata nell'applicazione, il profiler verrà tuttavia eseguito e le sessioni di profilatura recenti dovrebbero essere visualizzate nell'elenco se è presente un livello di traffico adeguato verso l'applicazione.

## <a id="profileondemand"></a> Attivare Profiler manualmente

Profiler può essere attivato manualmente con un solo clic. Si supponga di eseguire un test delle prestazioni Web. Sono necessarie tracce che consentono di comprendere le prestazioni dell'app Web in condizioni di carico. Il controllo sul momento in cui le tracce vengono acquisite è fondamentale poiché l'utente conosce il momento in cui il test di carico viene eseguito, ma l'intervallo di campionamento casuale potrebbe non rilevarlo.
I passaggi seguenti illustrano il funzionamento di questo scenario.

### <a name="optional-step-1-generate-traffic-to-your-web-app-by-starting-a-web-performance-test"></a>(Facoltativo) Passaggio 1: Generare il traffico verso l'app Web avviando un test delle prestazioni Web

Se l'app Web include già il traffico in ingresso o se si desidera solo generare manualmente il traffico, ignorare questa sezione e procedere al passaggio 2.

Passare al portale di Application Insights, **Configura > Test delle prestazioni**. Fare clic sul pulsante Nuovo per avviare un nuovo test delle prestazioni.

![Creare un nuovo test delle prestazioni][create-performance-test]

Nel riquadro **Nuovo test delle prestazioni** configurare l'URL di destinazione del test. Accettare tutte le impostazioni predefinite e avviare l'esecuzione del test di carico.

![Configurare il test di carico][configure-performance-test]

Si noti che inizialmente il nuovo test viene inserito in una coda e in seguito viene visualizzato con stato "in corso".

![Test di carico inviato e accodato][load-test-queued]

![Test di carico in esecuzione e in corso][load-test-in-progress]

### <a name="step-2-start-profiler-on-demand"></a>Passaggio 2: Avvio di Profiler on-demand

Quando il test è in esecuzione, è possibile avviare Profiler per acquisire tracce nell'app Web durante la ricezione del carico.
Passare al riquadro Configura Application Insights Profiler:


### <a name="step-3-view-traces"></a>Passaggio 3: Visualizzare le tracce

Al termine dell'esecuzione di Profiler, seguire le istruzioni visualizzate in base alla notifica per passare alla pagina Prestazioni e visualizzare le tracce.

## <a name="troubleshooting-on-demand-profiler"></a>Risoluzione dei problemi di Profiler su richiesta

In alcuni casi è possibile visualizzare un messaggio di errore di timeout di Profiler dopo una sessione su richiesta:

![Errore di timeout di Profiler][profiler-timeout]

L'errore può essere visualizzato per i due motivi seguenti.

1. La sessione di Profiler su richiesta ha esito positivo, ma Application Insights ha impiegato un tempo più lungo per elaborare i dati raccolti. Se l'elaborazione dei dati non è stata completata in 15 minuti, nel portale viene visualizzato un messaggio di timeout. Se dopo un certo periodo di tempo, le tracce di Profiler vengono visualizzate, ignorare il messaggio di errore. Microsoft sta lavorando per risolvere il problema.

1. L'app Web dispone di una versione precedente dell'agente di Profiler priva della funzionalità di esecuzione su richiesta. Se Application Insights Profiler è stato abilitato in precedenza, è probabile che sia necessario aggiornare l'agente di Profiler per iniziare a usare la funzionalità su richiesta.
  
Seguire questa procedura per verificare e installare la versione di Profiler più recente.

1. Passare alle impostazioni dell'app di Servizi app e verificare che siano specificate le impostazioni seguenti:
    * **APPINSIGHTS_INSTRUMENTATIONKEY**: sostituire il valore con la chiave di strumentazione appropriata per Application Insights.
    * **APPINSIGHTS_PORTALINFO**: ASP.NET
    * **APPINSIGHTS_PROFILERFEATURE_VERSION**: 1.0.0. Se una di queste impostazioni non è specificata, andare al riquadro di abilitazione di Application Insights per installare l'estensione del sito più recente.

1. Andare al riquadro Application Insights nel portale di Servizi app.

    ![Abilitare Application Insights nel portale di Servizi app][enable-app-insights]

1. Se nella pagina seguente viene visualizzato un pulsante "Aggiorna", fare clic per aggiornare l'estensione del sito di Application Insights che consente di installare l'agente di Profiler più recente.

    ![Aggiornare l'estensione del sito][update-site-extension]

1. Fare clic su **Modifica** per garantire che Profiler venga attivato e selezionare **OK** per salvare le modifiche.

    ![Modificare e salvare Application Insights][change-and-save-appinsights]

1. Tornare alla scheda **Impostazioni app** del servizio app per verificare che siano specificate le impostazioni seguenti:
    * **APPINSIGHTS_INSTRUMENTATIONKEY**: sostituire il valore con la chiave di strumentazione appropriata per Application Insights.
    * **APPINSIGHTS_PORTALINFO**: ASP.NET
    * **APPINSIGHTS_PROFILERFEATURE_VERSION**: 1.0.0

    ![Impostazioni dell'app per Profiler][app-settings-for-profiler]

1. Facoltativamente, selezionare la versione dell'estensione e verificare che non sia disponibile alcun aggiornamento.

    ![Verifica dell'aggiornamento dell'estensione][check-for-extension-update]

## <a name="next-steps"></a>Passaggi successivi
[Abilitare Profiler e visualizzare le tracce](app-insights-profiler-overview.md?toc=/azure/azure-monitor/toc.json)

[profiler-on-demand]: ./media/app-insights-profiler/Profiler-on-demand.png
[configure-profiler-entry]: ./media/app-insights-profiler/configure-profiler-entry.png
[create-performance-test]: ./media/app-insights-profiler/new-performance-test.png
[configure-performance-test]: ./media/app-insights-profiler/configure-performance-test.png
[load-test-queued]: ./media/app-insights-profiler/load-test-queued.png
[load-test-in-progress]: ./media/app-insights-profiler/load-test-inprogress.png
[enable-app-insights]: ./media/app-insights-profiler/enable-app-insights-blade-01.png
[update-site-extension]: ./media/app-insights-profiler/update-site-extension-01.png
[change-and-save-appinsights]: ./media/app-insights-profiler/change-and-save-appinsights-01.png
[app-settings-for-profiler]: ./media/app-insights-profiler/appsettings-for-profiler-01.png
[check-for-extension-update]: ./media/app-insights-profiler/check-extension-update-01.png
[profiler-timeout]: ./media/app-insights-profiler/profiler-timeout.png