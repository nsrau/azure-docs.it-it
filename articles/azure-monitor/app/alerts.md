---
title: Impostare gli avvisi in Application Insights di Azure | Microsoft Docs
description: Ricevere notifiche su tempi di risposta più lenti, eccezioni e altre prestazioni o modifiche nell'uso delle app Web.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.reviewer: lagayhar
ms.assetid: f8ebde72-f819-4ba5-afa2-31dbd49509a5
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 01/23/2019
ms.author: mbullwin
ms.openlocfilehash: 8026576478b16b753ba960155c383ffec62c61ce
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/11/2019
ms.locfileid: "59491543"
---
# <a name="set-alerts-in-application-insights"></a>Impostare gli avvisi in Application Insights
[Application Insights di Azure][start] può informare di eventuali cambiamenti nelle prestazioni e nelle metriche di utilizzo dell'app Web in uso. 

Application Insights esegue il monitoraggio dell'app attiva in un'[ampia varietà di piattaforme][platforms] per consentire di diagnosticare i problemi di prestazioni e acquisire informazioni sui modelli di utilizzo.

Sono disponibili più tipi di avvisi:

* [**Gli avvisi delle metriche** ](../../azure-monitor/platform/alerts-metric-overview.md) indicano quando una metrica supera un valore di soglia per un determinato periodo, ad esempio tempi di risposta, conteggio delle eccezioni, utilizzo della CPU o visualizzazioni pagina.
* [**Gli avvisi del log** ](../../azure-monitor/platform/alerts-unified-log.md) viene usato per descrivere gli avvisi in cui il segnale di avviso si basa su una query Kusto personalizzata.
* I [**test Web**][availability] indicano quando il sito non è disponibile in Internet o risponde lentamente. [Altre informazioni][availability].
* Gli avvisi della [**diagnostica proattiva**](../../azure-monitor/app/proactive-diagnostics.md) vengono configurati automaticamente per informare su schemi di prestazioni insoliti.

## <a name="set-a-metric-alert"></a>Impostare un avviso metrica
Aprire la scheda regole di avviso e quindi usare il pulsante Aggiungi.

![Nella scheda regole di avviso, scegliere Aggiungi avviso. Impostare l'app come la risorsa da misurare, specificare un nome per l'avviso e scegliere una metrica.](./media/alerts/01-set-metric.png)

* Impostare la risorsa prima delle altre proprietà. **Scegliere la risorsa "(components)"** per impostare avvisi sulle metriche relative a prestazioni e utilizzo.
* Il nome assegnato all'avviso deve essere univoco all'interno del gruppo di risorse (non solo dell'applicazione).
* Prendere nota delle unità in cui viene chiesto di immettere il valore soglia.
* Se si seleziona la casella "Invia messaggio di posta elettronica a proprietari...", vengono inviati avvisi tramite posta elettronica a chiunque abbia accesso a questo gruppo di risorse. Per espandere il set di utenti, aggiungerli al [gruppo di risorse o a una sottoscrizione](../../azure-monitor/app/resources-roles-access-control.md) (non la risorsa).
* Se si specifica "Indirizzi di posta elettronica aggiuntivi", gli avvisi vengono inviati agli utenti o gruppi corrispondenti (indipendentemente dalla selezione della casella "Invia messaggio di posta elettronica a proprietari..."). 
* Impostare un [indirizzo di webhook](../../azure-monitor/platform/alerts-webhooks.md) se è stata impostata un'app Web che risponde agli avvisi. L'app viene chiamata sia quando l'avviso viene attivato sia quando viene risolto. Si noti però che attualmente i parametri di query non vengono passati come proprietà webhook.
* È possibile disabilitare o abilitare l'avviso: usando i pulsanti nella parte superiore.

*Non è presente il pulsante Aggiungi avviso.*

* Si sta usando un account aziendale? È possibile impostare gli avvisi se si ha l'accesso come proprietario o come collaboratore a questa risorsa dell'applicazione. Esaminare la scheda di controllo di accesso. [Informazioni sul controllo di accesso][roles].

> [!NOTE]
> Nel pannello degli avvisi si nota che è già presente un avviso configurato: [Diagnostica proattiva](../../azure-monitor/app/proactive-failure-diagnostics.md). L'avviso automatico monitora una particolare metrica, la frequenza di errori delle richieste. A meno che non si decida di disabilitare l'avviso proattivo, non è necessario impostare un proprio avviso relativo alla frequenza di errori delle richieste.
> 
> 

## <a name="see-your-alerts"></a>Visualizzare gli avvisi
Si riceve un messaggio di posta elettronica quando lo stato dell'avviso passa da inattivo ad attivo e viceversa. 

Lo stato corrente di ogni avviso viene visualizzato nella scheda regole di avviso.

Nell'elenco a discesa Avvisi è disponibile un riepilogo delle attività recenti:

![Menu a discesa degli avvisi](./media/alerts/010-alert-drop.png)

La cronologia delle modifiche di stato si trova nel Log attività:

![Nella scheda Panoramica, fare clic su impostazioni, log di controllo](./media/alerts/09-alerts.png)

## <a name="how-alerts-work"></a>Funzionamento degli avvisi
* Un avviso può avere tre stati: "Mai attivato", "Attivato" e "Risolto". Lo stato Attivato indica che la condizione specificata aveva valore true al momento dell'ultima valutazione.
* Quando lo stato di un avviso viene modificato, viene generata una notifica. Se la condizione dell'avviso era già impostata su true durante la creazione dell'avviso, è possibile che non si riceva alcuna notifica finché la condizione non viene impostata su false.
* Ogni notifica genera un messaggio di posta elettronica se è stata selezionata la casella dei messaggi di posta elettronica o sono stati specificati indirizzi di posta elettronica. È anche possibile esaminare l'elenco di riepilogo a discesa Notifiche.
* Un avviso viene valutato ogni volta che arriva una metrica, ma non altrimenti.
* La valutazione aggrega la metrica per il periodo precedente e quindi la confronta con la soglia per determinare il nuovo stato.
* Il periodo scelto specifica l'intervallo per il quale vengono aggregate le metriche. Non influisce sulla frequenza con cui viene valutato l'avviso: ciò dipende dalla frequenza di arrivo delle metriche.
* Se per un periodo di tempo non arrivano dati per una determinata metrica, il divario comporta diversi effetti sulla valutazione dell'avviso e sui grafici in Esplora metriche. Se in Esplora metriche non viene visualizzato alcun dato per un periodo più lungo dell'intervallo di campionamento del grafico, il grafico mostra un valore pari a 0. Un avviso basato sulla metrica stessa non viene valutato nuovamente e lo stato dell'avviso rimane invariato. 
  
    Quando infine arrivano i dati, il grafico viene riportato su un valore diverso da zero. L'avviso viene valutato in base ai dati disponibili per il periodo specificato. Se il nuovo punto dati è l'unico disponibile nel periodo, la funzione di aggregazione si basa solo su quel punto dati.
* Un avviso può spesso passare velocemente dallo stato di avviso a quello integro e viceversa, anche se si imposta un periodo prolungato. Questa situazione può verificarsi se il valore della metrica si aggira intorno alla soglia. Non esiste alcuna isteresi nella soglia: la transizione allo stato di avviso si verifica in corrispondenza dello stesso valore della transizione allo stato integro.

## <a name="what-are-good-alerts-to-set"></a>Quali sono gli avvisi corretti da impostare?
Dipende dall'applicazione. Per iniziare, è consigliabile non impostare un numero eccessivo di metriche. Esaminare i grafici delle metriche mentre l'app è in esecuzione per acquisire informazioni sul comportamento normale. Questa procedura consente di trovare un modo per migliorare le prestazioni. Impostare quindi gli avvisi per ricevere le notifiche quando le metriche superano la zona normale. 

Gli avvisi più diffusi includono:

* Le [metriche del browser][client], soprattutto i **tempi di caricamento delle pagine** del browser, sono ottimali per le applicazioni Web. Se la pagina contiene molti script, è consigliabile cercare le **eccezioni del browser**. Per ottenere queste metriche e questi avvisi, è necessario configurare il [monitoraggio delle pagine Web][client].
* Il **tempo di risposta del server** per il lato server delle applicazioni Web. Oltre a impostare gli avvisi, prestare attenzione a questa metrica per vedere se varia in modo sproporzionato in caso di frequenza elevata delle richieste: la variazione può indicare l'esaurimento delle risorse da parte dell'app. 
* **Eccezioni del server** : per visualizzarle, è necessario effettuare alcuni passaggi di [configurazione aggiuntivi](../../azure-monitor/app/asp-net-exceptions.md).

Non dimenticare che la [diagnostica proattiva sulla frequenza errori](../../azure-monitor/app/proactive-failure-diagnostics.md) monitora automaticamente la frequenza con cui l'applicazione risponde alle richieste con codici di errore.

## <a name="how-to-set-an-exception-alert-using-custom-log-search"></a>Come impostare un avviso di eccezione tramite ricerca log personalizzata

In questa sezione verranno esaminate come impostare un avviso di eccezione basati su query. Per questo esempio, si supponga di che volere un avviso quando la frequenza non riuscita è superiore al 10% nelle ultime 24 ore.

1. Passare alla risorsa di Application Insights nel portale di Azure.
2. A sinistra, configurare in fare clic su **avviso**.

    ![A sinistra sotto Configura fare clic su avviso](./media/alerts/1appinsightalert.png)

3. Nella parte superiore della scheda avvisi selezionare **nuova regola di avviso**.

     ![Nella parte superiore della scheda avviso fare clic su nuova regola di avviso](./media/alerts/2createalert.png)

4. La risorsa deve essere selezionata automaticamente. Per impostare una condizione, fare clic su **Aggiungi condizione**.

    ![Fare clic su Aggiungi condizione](./media/alerts/3addcondition.png)

5. Nella scheda Configura del segnale per la logica selezionare **ricerca log personalizzata**

    ![Fare clic su ricerca di log personalizzato](./media/alerts/4customlogsearch.png)

6. Nella scheda Ricerca log personalizzata, immettere la query nella casella "ricerca". In questo esempio si userà la sotto query Kusto.
    ```kusto
    let percentthreshold = 10;
    let period = 24h;
    requests
    | where timestamp >ago(period)
    | summarize requestsCount = sum(itemCount)
    | project requestsCount, exceptionsCount = toscalar(exceptions | where timestamp >ago(period) | summarize sum(itemCount))
    | extend exceptionsRate = toreal(exceptionsCount)/toreal(requestsCount) * 100
    | where exceptionsRate > percentthreshold

    ```

    ![Nella finestra di query di ricerca digitare query](./media/alerts/5searchquery.png)
    
    > [!NOTE]
    > È anche possibile applicare questi passaggi per altri tipi di avvisi basati su query. Altre informazioni sul linguaggio di query Kusto da questo [Kusto introduttiva doc](https://docs.microsoft.com/azure/kusto/concepts/) oppure [foglio informativo sulle query SQL per Kusto](https://docs.microsoft.com/azure/kusto/query/sqlcheatsheet)

7. "Avviso per la logica", scegliere se si basa sul numero di risultati o la misurazione delle metriche. Selezionare la condizione (maggiore di, uguale a, minore di) e una soglia. Mentre si stanno modificando questi valori, è possibile notare le modifiche di frase di anteprima di condizione. In questo esempio viene usato "uguale a".

    ![In Alert logic scegliere tra le opzioni fornite per in base e una condizione, quindi digitare una soglia](./media/alerts/6alertlogic.png)

8. In "Evaluated basata", impostare il periodo e frequenza. Il periodo di qui deve corrispondere al valore che si inseriscono per periodo nella query precedente. Quindi fare clic **su**.

    ![Impostare periodo e frequenza nella parte inferiore e quindi fare clic su Fine](./media/alerts/7evaluate.png)

9. È ora possibile visualizzare la condizione che è stata creata con il costo mensile stimato. Di seguito sotto ["Gruppi di azioni"](../platform/action-groups.md) è possibile creare un nuovo gruppo o selezionarne uno esistente. Se si desidera, è possibile personalizzare le azioni.

    ![Fare clic su di selezionare o creare pulsanti nel gruppo di azione](./media/alerts/8actiongroup.png)

10. Infine aggiungere i dettagli dell'avviso (nome della regola, la descrizione, livello di gravità avviso). Al termine, fare clic su **Crea regola di avviso** nella parte inferiore.

    ![In dettaglio avviso digitare il nome della regola di avviso, scrivere una descrizione e scegliere un livello di gravità](./media/alerts/9alertdetails.png)

## <a name="how-to-unsubscribe-from-classic-alert-e-mail-notifications"></a>Come annullare la sottoscrizione a notifiche di posta elettronica di avviso di classico

In questa sezione si applica a **avvisi classici di disponibilità**, **avvisi delle metriche in Application Insights**e di ottenere **gli avvisi di anomalie errori classico**.

Si ricevono notifiche tramite posta elettronica per questi avvisi classici se applica gli elementi seguenti:

* Indirizzo di posta elettronica è elencato nel campo di destinatari della notifica tramite posta elettronica nelle impostazioni di regola di avviso.

* L'opzione per inviare notifiche tramite posta elettronica agli utenti che contiene determinati ruoli per la sottoscrizione viene attivato e si mantiene un ruolo corrispondente per tale sottoscrizione Azure.

![Schermata di notifica di avviso](./media/alerts/alert-notification.png)

Per controllare meglio la sicurezza e privacy a livello generale, è consigliabile specificare in modo esplicito i destinatari delle notifiche per gli avvisi classici nel **destinatari di posta elettronica di notifica** campo. L'opzione per informare tutti gli utenti che contiene determinati ruoli viene fornito per compatibilità con le versioni precedenti.

Per annullare la sottoscrizione di notifiche tramite posta elettronica generate da una determinata regola di avviso, rimuovere il tuo indirizzo di posta elettronica dal **destinatari di posta elettronica di notifica** campo.

Se l'indirizzo di posta elettronica non è elencato in modo esplicito, è consigliabile disabilitare l'opzione per notificare automaticamente tutti i membri di determinati ruoli e invece elencare tutti i messaggi e-mail di utente che devono ricevere le notifiche per tale regola di avviso tramite posta elettronica di notifica campo di destinatari.

## <a name="who-receives-the-classic-alert-notifications"></a>Chi riceve le notifiche di avviso (classiche)?

Questa sezione si applica solo agli avvisi classici e aiuterà a ottimizzare le notifiche di avviso assicurando che siano ricevute solo dai destinatari desiderati. Per comprendere meglio la differenza tra [avvisi classici](../platform/alerts-classic.overview.md) e i nuovi avvisi esperienza, vedere la [articolo di panoramica avvisi](../platform/alerts-overview.md). Per controllare le notifiche di avviso nella nuova esperienza avvisi, utilizzare [gruppi di azioni](../platform/action-groups.md).

* Consigliamo di usare destinatari specifici per le notifiche di avviso classiche.

* Per gli avvisi nelle metriche Application Insights, incluse le metriche di disponibilità, l'opzione **in blocco/gruppo** della casella di controllo, se abilitata, invia notifiche agli utenti con i ruoli proprietario, collaboratore o lettore nella sottoscrizione. _Tutti_ gli utenti con accesso alla sottoscrizione della risorsa di Application Insights si trovano nell'ambito e riceveranno le notifiche.

> [!NOTE]
> Se attualmente si usa l'opzione **in blocco/gruppo** della casella di controllo e la si disabilita, sarà impossibile annullare le modifiche.

Usare la nuova esperienza di avviso/avvisi quasi in tempo reale se si desidera inviare notifiche agli utenti in base ai ruoli. Con [gruppi di azione](../platform/action-groups.md), è possibile configurare notifiche tramite posta elettronica agli utenti con ruolo proprietario/collaboratore o lettore (non combinati insieme come una singola opzione).

## <a name="automation"></a>Automazione
* [Usare PowerShell per automatizzare la configurazione degli avvisi](../../azure-monitor/app/powershell-alerts.md)
* [Usare i webhook per automatizzare la risposta agli avvisi](../../azure-monitor/platform/alerts-webhooks.md)

## <a name="see-also"></a>Vedere anche 
* [Test Web di disponibilità](../../azure-monitor/app/monitor-web-app-availability.md)
* [Automatizzare la configurazione degli avvisi](../../azure-monitor/app/powershell-alerts.md)
* [Diagnostica proattiva](../../azure-monitor/app/proactive-diagnostics.md) 

<!--Link references-->

[availability]: ../../azure-monitor/app/monitor-web-app-availability.md
[client]: ../../azure-monitor/app/javascript.md
[platforms]: ../../azure-monitor/app/platforms.md
[roles]: ../../azure-monitor/app/resources-roles-access-control.md
[start]: ../../azure-monitor/app/app-insights-overview.md

