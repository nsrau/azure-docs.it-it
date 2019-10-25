---
title: Usare il riquadro delle impostazioni di Azure Application Insights Profiler | Microsoft Docs
description: Visualizzare lo stato del Profiler e avviare sessioni di profilatura
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 08/06/2018
ms.reviewer: mbullwin
ms.openlocfilehash: b383ef8c92325b0ad6561bee9b654c78e4054338
ms.sourcegitcommit: 8e271271cd8c1434b4254862ef96f52a5a9567fb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72820480"
---
# <a name="configure-application-insights-profiler"></a>Configura Application Insights Profiler

## <a name="updated-profiler-agent"></a>Agente Profiler aggiornato
Le funzionalità del trigger funzionano solo con la versione 2,6 o una versione successiva dell'agente del profiler. Se si esegue un servizio app Azure, l'agente verrà aggiornato automaticamente. È possibile visualizzare la versione dell'agente in esecuzione se si passa all'URL Kudu per il sito Web e si aggiunge \DiagnosticServices alla fine, come segue: https://yourwebsite.scm.azurewebsites.net/diagnosticservices. Il Application Insights Profiler processo Web dovrebbe essere 2,6 o versione successiva. È possibile forzare un aggiornamento riavviando l'app Web. 

Se il profiler viene eseguito in una macchina virtuale o in un servizio cloud, è necessario che sia installata la versione 16.0.4 di Windows Diagnostica di Azure (WAD) o versione successiva. È possibile controllare la versione di WAD eseguendo l'accesso alla macchina virtuale e cercando la directory seguente: C:\Packages\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\1.16.0.4. Il nome della directory è la versione di WAD installata. L'agente di macchine virtuali di Azure aggiornerà automaticamente WAD quando saranno disponibili nuove versioni.

## <a name="profiler-settings-page"></a>Pagina delle impostazioni del profiler

Per aprire il riquadro impostazioni del profiler di applicazione Azure Insights, passare al riquadro prestazioni Application Insights e quindi selezionare il pulsante **Configura Profiler** .

![Collegamento per aprire la pagina delle impostazioni del profiler][configure-profiler-entry]

Verrà visualizzata una pagina simile alla seguente:

![Pagina delle impostazioni del profiler][configure-profiler-page]

La pagina **configura Application Insights Profiler** presenta le seguenti funzionalità:

| | |
|-|-|
Profilo ora | avvia sessioni di profilatura sessioni per tutte le app collegate a questa istanza di Application Insights.
Trigger | Consente di configurare i trigger che provocano l'esecuzione del profiler. 
Sessioni di profilatura recenti | visualizza informazioni sulle ultime sessioni di profilatura.

## <a name="profile-now"></a>Profilo ora
Questa opzione consente di avviare una sessione di profilatura su richiesta. Quando si fa clic su questo collegamento, tutti gli agenti del profiler che inviano dati a questa istanza di Application Insights avvierà l'acquisizione di un profilo. Da 5 a 10 minuti, la sessione del profilo viene visualizzata nell'elenco seguente.

Per attivare manualmente una sessione del profiler, un utente richiede almeno l'accesso "Write" per il proprio ruolo per il componente Application Insights. Nella maggior parte dei casi, questo accesso viene eseguito automaticamente e non è necessario alcun lavoro aggiuntivo. In caso di problemi, il ruolo ambito della sottoscrizione da aggiungere sarà il ruolo "Application Insights collaboratore componente". [Vedere altre informazioni sul controllo di accesso ai ruoli con monitoraggio di Azure](https://docs.microsoft.com/azure/azure-monitor/app/resources-roles-access-control).

## <a name="trigger-settings"></a>Impostazioni del trigger
![Riquadro a comparsa Impostazioni trigger][trigger-settings-flyout]

Facendo clic sul pulsante trigger sulla barra dei menu si apre la casella impostazioni trigger. È possibile configurare il trigger per avviare la profilatura quando la percentuale di utilizzo della CPU o della memoria raggiunge il livello impostato.

| | |
|-|-|
Pulsante attiva/disattiva | On: Profiler può essere avviato da questo trigger. Disattivato: il profiler non verrà avviato da questo trigger.
Soglia memoria | Quando questa percentuale di memoria è in uso, il Profiler verrà avviato.
Duration | Imposta il periodo di tempo durante il quale il profiler viene eseguito quando viene attivato.
Cooldown | Imposta il tempo di attesa del profiler prima di verificare l'utilizzo della memoria o della CPU dopo che è stato attivato.

## <a name="recent-profiling-sessions"></a>Sessioni di profilatura recenti
Questa sezione della pagina Mostra informazioni sulle sessioni di profilatura recenti. Una sessione di profilatura rappresenta il periodo di tempo durante il quale l'agente del profiler sta prendendo un profilo in uno dei computer che ospitano l'applicazione. È possibile aprire i profili da una sessione facendo clic su una delle righe. Per ogni sessione, viene illustrato quanto segue:

| | |
|-|-|
Attivato da | Come è stata avviata la sessione, da un trigger, da un profilo ora o da un campionamento predefinito. 
Nome app | Nome dell'applicazione profilata.
Istanza del computer | Nome del computer in cui è stato eseguito l'agente del profiler.
Timestamp | Ora di acquisizione del profilo.
Tracee | Numero di tracce associate a singole richieste.
% CPU | Percentuale di CPU utilizzata durante l'esecuzione del profiler.
Memoria | Percentuale di memoria utilizzata durante l'esecuzione del profiler.

## <a id="profileondemand"></a>Usare i test delle prestazioni Web per generare il traffico all'applicazione

È possibile attivare il Profiler manualmente con un solo clic. Si supponga di eseguire un test delle prestazioni Web. Sono necessarie tracce per comprendere in che modo l'app Web è in esecuzione in condizioni di carico. Il controllo sul momento in cui le tracce vengono acquisite è fondamentale poiché l'utente conosce il momento in cui il test di carico viene eseguito, ma l'intervallo di campionamento casuale potrebbe non rilevarlo.

Le sezioni seguenti illustrano il funzionamento di questo scenario:

### <a name="step-1-generate-traffic-to-your-web-app-by-starting-a-web-performance-test"></a>Passaggio 1: generare il traffico all'app Web avviando un test delle prestazioni Web

Se l'app Web include già il traffico in ingresso o se si desidera solo generare manualmente il traffico, ignorare questa sezione e procedere al passaggio 2.

1. Nel portale di Application Insights selezionare **Configura** > **Test delle prestazioni**. 

1. Per avviare un nuovo test delle prestazioni, selezionare il pulsante **Nuovo**.

   ![Creare un nuovo test delle prestazioni][create-performance-test]

1. Nel riquadro **Nuovo test delle prestazioni** configurare l'URL di destinazione del test. Accettare tutte le impostazioni predefinite e selezionare **Esegui test** per avviare l'esecuzione del test di carico.

    ![Configurare il test di carico][configure-performance-test]

    Inizialmente il nuovo test viene inserito in una coda e in seguito viene visualizzato con stato *in corso*.

    ![Test di carico inviato e accodato][load-test-queued]

    ![Test di carico in esecuzione e in corso][load-test-in-progress]

### <a name="step-2-start-a-profiler-on-demand-session"></a>Passaggio 2: avviare una sessione su richiesta del profiler

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
