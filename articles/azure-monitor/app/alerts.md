---
title: Impostare gli avvisi in Application Insights di Azure | Microsoft Docs
description: Ricevere notifiche su tempi di risposta più lenti, eccezioni e altre prestazioni o modifiche nell'uso delle app Web.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 01/23/2019
ms.reviewer: lagayhar
ms.openlocfilehash: a21e2676d1b03472c58e2f95095a1a59d00b16be
ms.sourcegitcommit: 1bd2207c69a0c45076848a094292735faa012d22
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/21/2019
ms.locfileid: "72678398"
---
# <a name="set-alerts-in-application-insights"></a>Impostare gli avvisi in Application Insights
[Applicazione Azure Insights][start] può avvertire l'utente delle modifiche apportate alle metriche delle prestazioni o dell'utilizzo nell'app Web. 

Application Insights monitora l'app attiva in un' [ampia gamma di piattaforme][platforms] per semplificare la diagnosi dei problemi di prestazioni e comprendere i modelli di utilizzo.

Sono disponibili più tipi di avvisi:

* Gli [**avvisi delle metriche**](../../azure-monitor/platform/alerts-metric-overview.md) indicano quando una metrica supera un valore di soglia per un determinato periodo, ad esempio i tempi di risposta, i conteggi delle eccezioni, l'utilizzo della CPU o le visualizzazioni di pagina.
* Gli [**avvisi del log**](../../azure-monitor/platform/alerts-unified-log.md) vengono usati per descrivere gli avvisi in cui il segnale di avviso si basa su una query kusto personalizzata.
* I [**test Web**][availability] indicano quando il sito non è disponibile in Internet o risponde lentamente. [Altre informazioni][availability].
* Gli avvisi della [**diagnostica proattiva**](../../azure-monitor/app/proactive-diagnostics.md) vengono configurati automaticamente per informare su schemi di prestazioni insoliti.

## <a name="set-a-metric-alert"></a>Impostare un avviso metrica
Aprire la scheda regole di avviso e quindi usare il pulsante Aggiungi.

![Nella scheda regole di avviso scegliere Aggiungi avviso. Impostare l'app come la risorsa da misurare, specificare un nome per l'avviso e scegliere una metrica.](./media/alerts/01-set-metric.png)

* Impostare la risorsa prima delle altre proprietà. **Scegliere la risorsa "(components)"** per impostare avvisi sulle metriche relative a prestazioni e utilizzo.
* Il nome assegnato all'avviso deve essere univoco all'interno del gruppo di risorse (non solo dell'applicazione).
* Prendere nota delle unità in cui viene chiesto di immettere il valore soglia.
* Se si seleziona la casella "Invia messaggio di posta elettronica a proprietari...", vengono inviati avvisi tramite posta elettronica a chiunque abbia accesso a questo gruppo di risorse. Per espandere il set di utenti, aggiungerli al [gruppo di risorse o a una sottoscrizione](../../azure-monitor/app/resources-roles-access-control.md) (non la risorsa).
* Se si specifica "Indirizzi di posta elettronica aggiuntivi", gli avvisi vengono inviati agli utenti o gruppi corrispondenti (indipendentemente dalla selezione della casella "Invia messaggio di posta elettronica a proprietari..."). 
* Impostare un [indirizzo di webhook](../../azure-monitor/platform/alerts-webhooks.md) se è stata impostata un'app Web che risponde agli avvisi. L'app viene chiamata sia quando l'avviso viene attivato sia quando viene risolto. Si noti però che attualmente i parametri di query non vengono passati come proprietà webhook.
* È possibile disabilitare o abilitare l'avviso: vedere i pulsanti nella parte superiore.

*Il pulsante Aggiungi avviso non è visibile.*

* Si sta usando un account aziendale? È possibile impostare gli avvisi se si ha l'accesso come proprietario o come collaboratore a questa risorsa dell'applicazione. Esaminare la scheda controllo di accesso. [informazioni sul controllo di accesso][roles].

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

![Nella scheda Panoramica fare clic su impostazioni, log di controllo](./media/alerts/09-alerts.png)

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

* Le [metriche del browser][client], soprattutto i tempi di caricamento delle **pagine**del browser, sono valide per le applicazioni Web. Se la pagina contiene molti script, è consigliabile cercare le **eccezioni del browser**. Per ottenere queste metriche e avvisi, è necessario configurare il [monitoraggio delle pagine Web][client].
* Il **tempo di risposta del server** per il lato server delle applicazioni Web. Oltre a impostare gli avvisi, prestare attenzione a questa metrica per vedere se varia in modo sproporzionato in caso di frequenza elevata delle richieste: la variazione può indicare l'esaurimento delle risorse da parte dell'app. 
* **Eccezioni del server** : per visualizzarle, è necessario effettuare alcuni passaggi di [configurazione aggiuntivi](../../azure-monitor/app/asp-net-exceptions.md).

Non dimenticare che la [diagnostica proattiva sulla frequenza errori](../../azure-monitor/app/proactive-failure-diagnostics.md) monitora automaticamente la frequenza con cui l'applicazione risponde alle richieste con codici di errore.

## <a name="how-to-set-an-exception-alert-using-custom-log-search"></a>Come impostare un avviso di eccezione usando la ricerca log personalizzata

In questa sezione verrà illustrato come impostare l'avviso di un'eccezione basata su query. Per questo esempio, si desidera un avviso quando la percentuale di errore è superiore al 10% nelle ultime 24 ore.

1. Passare alla risorsa Application Insights nella portale di Azure.
2. A sinistra, in Configura fare clic su **avviso**.

    ![A sinistra in Configura avviso di clic](./media/alerts/1appinsightalert.png)

3. Nella parte superiore della scheda avviso selezionare **nuova regola di avviso**.

     ![Nella parte superiore della scheda avviso fare clic su nuova regola di avviso](./media/alerts/2createalert.png)

4. La risorsa deve essere selezionata automaticamente. Per impostare una condizione, fare clic su **Aggiungi condizione**.

    ![Fare clic su Aggiungi condizione](./media/alerts/3addcondition.png)

5. Nella scheda Configura logica del segnale selezionare **Ricerca log personalizzata**

    ![Fare clic su ricerca log personalizzata](./media/alerts/4customlogsearch.png)

6. Nella scheda Ricerca log personalizzata immettere la query nella casella "query di ricerca". Per questo esempio verrà usata la query kusto seguente.
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

    ![Digitare query nella casella query di ricerca](./media/alerts/5searchquery.png)
    
    > [!NOTE]
    > È anche possibile applicare questi passaggi ad altri tipi di avvisi basati su query. Per altre informazioni sul linguaggio di query kusto, vedere il [documento](https://docs.microsoft.com/azure/kusto/concepts/) introduttivo di Kusto o questo foglio informativo da [SQL a kusto](https://docs.microsoft.com/azure/kusto/query/sqlcheatsheet)

7. In "logica di avviso" scegliere se è basata sul numero di risultati o sulla misurazione della metrica. Quindi selezionare la condizione (maggiore di, uguale a, minore di) e una soglia. Durante la modifica di questi valori, è possibile notare le modifiche della frase di anteprima della condizione. In questo esempio viene usato "uguale a".

    ![In logica avvisi scegliere tra le opzioni fornite per in base alla condizione e, quindi digitare una soglia](./media/alerts/6alertlogic.png)

8. In "valutato in base a" impostare il punto e la frequenza. Il periodo deve corrispondere al valore inserito per il periodo nella query precedente. Quindi fare clic su **fine**.

    ![Impostare periodo e frequenza nella parte inferiore e quindi fare clic su fine.](./media/alerts/7evaluate.png)

9. Viene ora visualizzata la condizione creata con il costo mensile stimato. Sotto ["gruppi di azioni"](../platform/action-groups.md) è possibile creare un nuovo gruppo o selezionarne uno esistente. Se lo si desidera, è possibile personalizzare le azioni.

    ![fare clic sui pulsanti Seleziona o crea in gruppo di azioni](./media/alerts/8actiongroup.png)

10. Aggiungere infine i dettagli dell'avviso (nome della regola di avviso, descrizione, gravità). Al termine, fare clic su **Crea regola di avviso** nella parte inferiore.

    ![In Dettagli avviso digitare il nome della regola di avviso, scrivere una descrizione e selezionare una gravità](./media/alerts/9alertdetails.png)

## <a name="how-to-unsubscribe-from-classic-alert-e-mail-notifications"></a>Come annullare la sottoscrizione di notifiche di posta elettronica di avviso classiche

Questa sezione è valida per gli avvisi di **disponibilità classica**, per gli avvisi della **metrica di Application Insights classici**e per gli **avvisi di anomalie degli errori classici**.

Si ricevono notifiche tramite posta elettronica per questi avvisi classici se si verifica una delle condizioni seguenti:

* L'indirizzo di posta elettronica è elencato nel campo destinatari di messaggi di posta elettronica di notifica nelle impostazioni della regola di avviso.

* L'opzione per l'invio di notifiche tramite posta elettronica agli utenti che contengono determinati ruoli per la sottoscrizione viene attivata e si mantiene un ruolo corrispondente per la sottoscrizione di Azure specifica.

![Screenshot della notifica di avviso](./media/alerts/alert-notification.png)

Per un migliore controllo della sicurezza e della privacy, è in genere consigliabile specificare in modo esplicito i destinatari delle notifiche per gli avvisi classici nel campo **destinatari di posta elettronica di notifica** . Per la compatibilità con le versioni precedenti, è possibile inviare una notifica a tutti gli utenti che dispongono di determinati ruoli.

Per annullare la sottoscrizione delle notifiche tramite posta elettronica generate da una determinata regola di avviso, rimuovere l'indirizzo di posta elettronica dal campo **destinatari di posta elettronica di notifica** .

Se l'indirizzo di posta elettronica non è elencato in modo esplicito, è consigliabile disabilitare l'opzione per notificare automaticamente a tutti i membri di determinati ruoli, elencando invece tutti i messaggi di posta elettronica degli utenti che devono ricevere notifiche per la regola di avviso nel messaggio di posta elettronica di notifica campo destinatari.

## <a name="who-receives-the-classic-alert-notifications"></a>Chi riceve le notifiche di avviso (classiche)?

Questa sezione si applica solo agli avvisi classici e aiuterà a ottimizzare le notifiche di avviso assicurando che siano ricevute solo dai destinatari desiderati. Per ulteriori informazioni sulla differenza tra gli [avvisi classici](../platform/alerts-classic.overview.md) e la nuova esperienza degli avvisi, vedere l' [articolo Panoramica degli avvisi](../platform/alerts-overview.md). Per controllare la notifica di avviso nella nuova esperienza avvisi, usare i [gruppi di azioni](../platform/action-groups.md).

* Consigliamo di usare destinatari specifici per le notifiche di avviso classiche.

* Per gli avvisi nelle metriche Application Insights, incluse le metriche di disponibilità, l'opzione **in blocco/gruppo** della casella di controllo, se abilitata, invia notifiche agli utenti con i ruoli proprietario, collaboratore o lettore nella sottoscrizione. In effetti, _tutti_ gli utenti con accesso alla sottoscrizione della risorsa di Application Insights rientrano nell'ambito e riceveranno le notifiche.

> [!NOTE]
> Se attualmente si usa l'opzione **in blocco/gruppo** della casella di controllo e la si disabilita, sarà impossibile annullare le modifiche.

Usare la nuova esperienza di avviso/avvisi quasi in tempo reale se si desidera inviare notifiche agli utenti in base ai ruoli. Con [gruppi di azioni](../platform/action-groups.md), è possibile configurare notifiche tramite posta elettronica agli utenti con ruolo proprietario, collaboratore o lettore (non combinati insieme come una singola opzione).

## <a name="automation"></a>Automazione
* [Usare PowerShell per automatizzare la configurazione degli avvisi](../../azure-monitor/app/powershell-alerts.md)
* [Usare webhook per automatizzare la risposta agli avvisi](../../azure-monitor/platform/alerts-webhooks.md)

## <a name="see-also"></a>Vedi anche
* [Test Web di disponibilità](../../azure-monitor/app/monitor-web-app-availability.md)
* [Automatizzare la configurazione degli avvisi](../../azure-monitor/app/powershell-alerts.md)
* [Diagnostica proattiva](../../azure-monitor/app/proactive-diagnostics.md) 

<!--Link references-->

[availability]: ../../azure-monitor/app/monitor-web-app-availability.md
[client]: ../../azure-monitor/app/javascript.md
[platforms]: ../../azure-monitor/app/platforms.md
[roles]: ../../azure-monitor/app/resources-roles-access-control.md
[start]: ../../azure-monitor/app/app-insights-overview.md

