<properties 
   pageTitle="Gestione degli avvisi nei prodotti di monitoraggio Microsoft | Microsoft Azure"
   description="Un avviso indica un problema che richiede attenzione da parte di un amministratore.  In questo articolo vengono descritte le differenze nella modalità di creazione e gestione degli avvisi in System Center Operations Manager (SCOM) e Log Analytics. Inoltre, vengono suggerite le procedure consigliate per usufruire al meglio dei due prodotti, per una strategia di gestione degli avvisi ibrida." 
   services="operations-management-suite"
   documentationCenter=""
   authors="bwren"
   manager="jwhit"
   editor="tysonn" />
<tags 
   ms.service="operations-management-suite"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/06/2016"
   ms.author="bwren" />


# <a name="managing-alerts-with-microsoft-monitoring"></a>Gestione degli avvisi di monitoraggio Microsoft 

Un avviso indica un problema che richiede attenzione da parte di un amministratore.  Esistono delle differenze ben distinte tra System Center Operations Manager (SCOM) e Log Analytics in Operations Management Suite (OMS) in termini della modalità di creazione, gestione e analisi degli avvisi, ma anche per quanto riguarda la modalità di notifica in caso di rilevamento di un problema critico.

## <a name="alerts-in-operations-manager"></a>Avvisi in Operations Manager
Gli avvisi in SCOM vengono generati da singole regole o monitoraggi per indicare un problema specifico.  Un monitoraggio può generare un avviso quando registra uno stato di errore, mentre una regola può generare un avviso per indicare un problema critico che non è direttamente correlato all'integrità di un oggetto gestito.  I Management Pack includono una serie di flussi di lavoro che creano avvisi per l'applicazione o il servizio che gestiscono.  Parte del processo di configurazione di un nuovo Management Pack è l'ottimizzazione, al fine di garantire che non si ricevano troppi avvisi per problemi non considerati critici.

![Visualizzazione degli avvisi SCOM](media/operations-management-suite-monitoring-alerts/scom-alert-view.png)

SCOM fornisce la gestione completa degli avvisi, il cui stato può essere modificato dagli amministratori quando sono impegnati nella risoluzione del problema.  Una volta risolto il problema, l'amministratore imposta l’avviso come chiuso e quindi non verrà più visualizzato tra gli avvisi attivi.  Gli avvisi generati dai monitoraggi possono essere risolti automaticamente quando il monitoraggio torna a uno stato di integrità.

![Stato dell'avviso](media/operations-management-suite-monitoring-alerts/scom-alert-status.png)

## <a name="alerts-in-log-analytics"></a>Avvisi in Log Analytics
Un avviso in Log Analytics viene creato da una query di log eseguita automaticamente a intervalli regolari.  È possibile creare una regola di avviso di una qualsiasi query di log.  Se la query restituisce risultati che corrispondono ai criteri specificati, viene creato un avviso.  Potrebbe trattarsi di una query specifica che crea un avviso se viene rilevato un particolare evento oppure è possibile utilizzare una query più generale che controlli la presenza di un qualsiasi evento di errore relativo a una determinata applicazione.

Gli avvisi di Log Analytics vengono scritti nel repository di OMS come un evento e possono essere recuperati con una query di log.  Lo stato è diverso da quello degli eventi SCOM, in modo da poter indicare se il problema è stato risolto.

![Avviso OMS](media/operations-management-suite-monitoring-alerts/oms-alert.png)

Quando SCOM viene utilizzato come origine dati per Log Analytics, gli avvisi SCOM vengono scritti nel repository di OMS non appena creati e modificati.  

![Avviso SCOM](media/operations-management-suite-monitoring-alerts/scom-alert.png)

La [soluzione Alert Management](http://technet.microsoft.com/library/mt484092.aspx) fornisce un riepilogo di avvisi attivi e varie query comuni per recuperare diversi set di avvisi.  In questo modo, l’analisi degli avvisi è più efficace rispetto a un report in SCOM.  È possibile eseguire il drill-down dai dati di riepilogo a quelli dettagliati e creare query ad hoc per recuperare diversi set di avvisi.

![soluzione Alert Management](media/operations-management-suite-monitoring-alerts/alert-management.png)

## <a name="notifications"></a>Notifiche
Le notifiche in SCOM inviano un messaggio di posta elettronica o un SMS in risposta agli avvisi che corrispondono a particolari criteri.  È possibile creare diverse sottoscrizioni di notifica al fine di inviare notifiche ad altrettante diverse persone a seconda di tali criteri, come l’oggetto monitorato, la gravità dell'avviso, il tipo di problema rilevato o l'ora del giorno.

Alcune sottoscrizioni possono essere utilizzate per implementare una strategia di notifica completa per un numero elevato di Management Pack.

![Avvisi SCOM](media/operations-management-suite-monitoring-alerts/alerts-overview-scom.png)

Log Analytics può inviarti una notifica tramite posta elettronica per comunicarti che è stato creato un avviso impostando un'azione di notifica di posta elettronica per ogni [regola di avviso](http://technet.microsoft.com/library/mt614775.aspx).  Non è possibile effettuare la sottoscrizione a più avvisi con una sola regola, come invece accade in SCOM.  È inoltre necessario crearsi le proprie regole di avviso poiché OMS non ne fornisce di preconfigurate.

![Avvisi Log Analytics](media/operations-management-suite-monitoring-alerts/alerts-overview-oms.png)

Tuttavia, non è possibile gestire completamente gli avvisi SCOM in Log Analytics, poiché possono essere modificati solo nella console operatore.  Log Analytics è però utile come parte di un processo di gestione degli avvisi per fornire gli strumenti di analisi di cui SCOM da solo non dispone.

## <a name="alert-remediation"></a>Correzione tramite avvisi
[correzione](http://technet.microsoft.com/library/mt614775.aspx) si riferisce a un tentativo di correggere automaticamente il problema identificato da un avviso.
  
SCOM consente di eseguire diagnostica e ripristini in risposta a uno stato non integro riscontrato dal monitoraggio.  Ciò accade simultaneamente al monitoraggio che crea l'avviso.  Diagnostica e ripristini, in genere, vengono implementati come uno script eseguito sull'agente.  La diagnostica tenta di raccogliere ulteriori informazioni sul problema rilevato, mentre il ripristino tenta di risolvere il problema.

Log Analytics consente di avviare un [runbook di automazione di Azure](https://azure.microsoft.com/documentation/services/automation/) o di chiamare un webhook in risposta a un avviso di Log Analytics.  I runbook possono includere logica complessa implementata in PowerShell.  Lo script viene eseguito in Azure e può accedere a qualsiasi risorsa di Azure o a risorse esterne disponibili nel cloud.  Automazione di Azure ha la possibilità di eseguire i runbook in un server nel data center locale, ma questa funzionalità non è attualmente disponibile quando si avvia il runbook in risposta agli avvisi di Log Analytics.

Sia i ripristini in SCOM sia i runbook in OMS possono contenere gli script di PowerShell, ma i ripristini sono più difficili da creare e gestire perché devono essere contenuti all'interno di un Management Pack.  I runbook vengono archiviati in Automazione di Azure, che fornisce funzionalità per la creazione, l’esecuzione di test e la gestione dei runbook.

Se si utilizza SCOM come un'origine dati per Log Analytics, è possibile creare un avviso Log Analytics tramite una query di log per recuperare gli avvisi SCOM archiviati nel repository OMS.  Questo consente di eseguire un runbook di Automazione di Azure in risposta a un avviso SCOM.  Naturalmente, poiché il runbook viene eseguito in Azure, questa non è una strategia attuabile in caso di ripristini di problemi a livello locale.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni dettagliate sugli [avvisi in System Center Operations Manager (SCOM)](https://technet.microsoft.com/library/hh212913.aspx).


<!--HONumber=Oct16_HO2-->


