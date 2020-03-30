---
title: Usare il riquadro delle impostazioni di Azure Application Insights Profiler | Microsoft Docs
description: Visualizzare lo stato del Profiler e avviare sessioni di profilatura
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 08/06/2018
ms.reviewer: mbullwin
ms.openlocfilehash: f66a23c0562ec9f1987bd119a45b7b767f2dfe46
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671631"
---
# <a name="configure-application-insights-profiler"></a>Configura Application Insights Profiler

## <a name="updated-profiler-agent"></a>Agente profiler aggiornato
Le funzionalità di trigger funzionano solo con la versione 2.6 o successiva dell'agente del profiler. Se si esegue un servizio app di Azure, l'agente verrà aggiornato automaticamente. È possibile visualizzare la versione dell'agente in esecuzione se si passa all'URL di Kudu per il https://yourwebsite.scm.azurewebsites.net/diagnosticservicessito Web e si aggiunge l'opzione .DiagnosticServices alla fine, in questo modo: . Il processo Web di Application Insights Profiler deve essere versione 2.6 o successiva. È possibile forzare un aggiornamento riavviando l'app Web. 

Se si esegue il profiler in una macchina virtuale o un servizio cloud, è necessario che sia installata l'estensione di Diagnostica di Windows Azure (WAD) versione 16.0.4 o successiva. È possibile controllare la versione di WAD accedendo alla macchina virtuale e cercando questa directory: C: Il nome della directory è la versione di WAD installata. L'agente di macchine virtuali di Azure aggiornerà automaticamente WAD quando saranno disponibili nuove versioni.

## <a name="profiler-settings-page"></a>Pagina Impostazioni profiler

Per aprire il riquadro delle impostazioni di Azure Application Insights Profiler, passare al riquadro Prestazioni di Application Insights e quindi selezionare il **pulsante Configura Profiler.To** open the Azure Application Insights Profiler pane, go to the Application Insights Performance pane, and then select the Configure Profiler button.

![Collegamento alla pagina delle impostazioni del profiler][configure-profiler-entry]

Si apre una pagina simile alla seguente:

![Pagina Impostazioni profiler][configure-profiler-page]

Nella pagina Configura Application Insights Profiler sono disponibili le funzionalità seguenti:The **Configure Application Insights Profiler** page has these features:

| | |
|-|-|
Profilo ora | avvia sessioni di profilatura sessioni per tutte le app collegate a questa istanza di Application Insights.
Trigger | Consente di configurare i trigger che causano l'esecuzione del profiler. 
Sessioni di profilatura recenti | visualizza informazioni sulle ultime sessioni di profilatura.

## <a name="profile-now"></a>Profilo ora
Questa opzione consente di avviare una sessione di profilatura su richiesta. Quando si fa clic su questo collegamento, tutti gli agenti del profiler che inviano dati a questa istanza di Application Insights inizieranno ad acquisire un profilo. Dopo 5-10 minuti, la sessione del profilo verrà visualizzata nell'elenco sottostante.

Affinché un utente attivi manualmente una sessione del profiler, è necessario almeno l'accesso "scrittura" al proprio ruolo per il componente Application Insights.For a user to manually trigger a profiler session, they require at minimum "write" access on their role for the Application Insights component. Nella maggior parte dei casi, si ottiene questo accesso automaticamente e non è necessario alcun lavoro aggiuntivo. In caso di problemi, il ruolo dell'ambito della sottoscrizione da aggiungere sarà il ruolo "Collaboratore componente di Application Insights". Per altre informazioni sul controllo degli accessi ai [ruoli, vedere Monitoraggio di Azure .See more about role access control with Azure Monitoring](https://docs.microsoft.com/azure/azure-monitor/app/resources-roles-access-control).

## <a name="trigger-settings"></a>Impostazioni trigger
![Riquadro a comparsa Impostazioni trigger][trigger-settings-flyout]

Facendo clic sul pulsante Trigger sulla barra dei menu si apre la casella delle impostazioni del trigger. È possibile impostare il trigger per avviare la profilatura quando la percentuale di utilizzo della CPU o della memoria raggiunge il livello impostato.

| | |
|-|-|
Pulsante On/Off | On: il profiler può essere avviato da questo trigger; Disattivato: il profiler non verrà avviato da questo trigger.
Soglia memoria | Quando questa percentuale di memoria è in uso, il profiler verrà avviato.
Duration | Imposta il periodo di tempo per cui il profiler verrà eseguito quando viene attivato.
Cooldown | Imposta il tempo di attesa del profiler prima di controllare nuovamente l'utilizzo della memoria o della CPU dopo l'attivazione.

## <a name="recent-profiling-sessions"></a>Sessioni di profilatura recenti
Questa sezione della pagina mostra informazioni sulle sessioni di profilatura recenti. Una sessione di profilatura rappresenta il periodo di tempo in cui l'agente profiler stava assumendo un profilo su uno dei computer che ospitano l'applicazione. È possibile aprire i profili da una sessione facendo clic su una delle righe. Per ogni sessione, mostriamo:

| | |
|-|-|
Attivato da | Modalità di avvio della sessione, tramite trigger, profilo ora o campionamento predefinito. 
Nome app | Nome dell'applicazione profilata.
Istanza macchina | Nome del computer su cui è stato eseguito l'agente profiler.
Timestamp | Ora in cui il profilo è stato acquisito.
Traccia | Numero di tracce collegate a singole richieste.
% CPU | Percentuale di CPU utilizzata durante l'esecuzione del profiler.
% memoria | Percentuale di memoria utilizzata durante l'esecuzione del profiler.

## <a name="use-web-performance-tests-to-generate-traffic-to-your-application"></a><a id="profileondemand"></a>Usare i test delle prestazioni Web per generare traffico per l'applicazioneUse web performance tests to generate traffic to your application

È possibile attivare il Profiler manualmente con un solo clic. Si supponga di eseguire un test delle prestazioni Web. Sono necessarie tracce per comprendere in che modo l'app Web viene eseguita sotto carico. Il controllo sul momento in cui le tracce vengono acquisite è fondamentale poiché l'utente conosce il momento in cui il test di carico viene eseguito, ma l'intervallo di campionamento casuale potrebbe non rilevarlo.

Le sezioni seguenti illustrano il funzionamento di questo scenario:

### <a name="step-1-generate-traffic-to-your-web-app-by-starting-a-web-performance-test"></a>Passaggio 1: Generare traffico nell'app Web avviando un test delle prestazioni WebStep 1: Generate traffic to your web app by starting a web performance test

Se l'app Web include già il traffico in ingresso o se si desidera solo generare manualmente il traffico, ignorare questa sezione e procedere al passaggio 2.

1. Nel portale di Application Insights selezionare **Configura** > **test delle prestazioni**. 

1. Per avviare un nuovo test delle prestazioni, selezionare il pulsante **Nuovo**.

   ![Creare un nuovo test delle prestazioni][create-performance-test]

1. Nel riquadro **Nuovo test delle prestazioni** configurare l'URL di destinazione del test. Accettare tutte le impostazioni predefinite e selezionare **Esegui test** per avviare l'esecuzione del test di carico.

    ![Configurare il test di carico][configure-performance-test]

    Inizialmente il nuovo test viene inserito in una coda e in seguito viene visualizzato con stato *in corso*.

    ![Test di carico inviato e accodato][load-test-queued]

    ![Test di carico in esecuzione e in corso][load-test-in-progress]

### <a name="step-2-start-a-profiler-on-demand-session"></a>Passaggio 2: Avviare una sessione di profiler su richiestaStep 2: Start a Profiler on-demand session

1. Quando il test è in esecuzione, avviare Profiler per acquisire tracce nell'app Web durante la ricezione del carico.

1. Passare al riquadro **Configura Application Insights Profiler**.


### <a name="step-3-view-traces"></a>Passaggio 3: Visualizzare le tracce

Al termine dell'esecuzione di Profiler, seguire le istruzioni visualizzate in base alla notifica per passare al riquadro Prestazioni e visualizzare le tracce.

## <a name="next-steps"></a>Passaggi successivi
[Abilitare Profiler e visualizzare le tracce](profiler-overview.md?toc=/azure/azure-monitor/toc.json)

[profiler-on-demand]: ./media/profiler-settings/Profiler-on-demand.png
[configure-profiler-entry]: ./media/profiler-settings/configure-profiler-entry.png
[configure-profiler-page]: ./media/profiler-settings/configureBlade.png
[trigger-settings-flyout]: ./media/profiler-settings/CPUTrigger.png
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
