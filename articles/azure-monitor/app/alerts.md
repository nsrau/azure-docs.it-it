---
title: Impostare avvisi in Azure Application InsightsSet Alerts in Azure Application Insights
description: Ricevere notifiche su tempi di risposta più lenti, eccezioni e altre prestazioni o modifiche nell'uso delle app Web.
ms.topic: conceptual
ms.date: 01/23/2019
ms.reviewer: lagayhar
ms.subservice: alerts
ms.openlocfilehash: 28fd59556a586b85a6d3caf188d9e02c11d31e3b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80295088"
---
# <a name="set-alerts-in-application-insights"></a>Impostare gli avvisi in Application Insights

[Application Insights di Azure][start] può informare di eventuali cambiamenti nelle prestazioni e nelle metriche di utilizzo dell'app Web in uso. 

Application Insights esegue il monitoraggio dell'app attiva in un'[ampia varietà di piattaforme][platforms] per consentire di diagnosticare i problemi di prestazioni e acquisire informazioni sui modelli di utilizzo.

Esistono diversi tipi di avvisi:

* [**Gli avvisi di metrica**](../../azure-monitor/platform/alerts-metric-overview.md) indicano quando una metrica supera un valore soglia per un determinato periodo, ad esempio i tempi di risposta, i conteggi delle eccezioni, l'utilizzo della CPU o le visualizzazioni di pagina.
* [**Avvisi log**](../../azure-monitor/platform/alerts-unified-log.md) viene utilizzato per descrivere gli avvisi in cui il segnale di avviso è basato su una query Kusto personalizzata.
* [**I test Web**][availability] indicano quando il sito non è disponibile su Internet o rispondono lentamente. [Scopri di più][availability].
* Gli avvisi della [**diagnostica proattiva**](../../azure-monitor/app/proactive-diagnostics.md) vengono configurati automaticamente per informare su schemi di prestazioni insoliti.

## <a name="how-to-set-an-exception-alert-using-custom-log-search"></a>Come impostare un avviso di eccezione utilizzando la ricerca log personalizzataHow to set an exception alert using custom log search

In questa sezione verrà illustrato come impostare un avviso di eccezione basata su query. Per questo esempio, supponiamo di volere un avviso quando la frequenza di errore è maggiore del 10% nelle ultime 24 ore.

1. Passare alla risorsa Informazioni applicazioni nel portale di Azure.Go to your Application Insight resource in the Azure portal.
2. A sinistra, sotto configure clicca su **Avviso**.

    ![A sinistra sotto configura fai clic su Avviso](./media/alerts/1appinsightalert.png)

3. Nella parte superiore della scheda degli avvisi selezionare **Nuova regola di avviso**.

     ![Nella parte superiore della scheda degli avvisi fare clic su nuova regola di avviso](./media/alerts/2createalert.png)

4. La risorsa deve essere selezionata automaticamente. Per impostare una condizione, fare clic su **Aggiungi condizione**.

    ![Fare clic su Aggiungi condizione](./media/alerts/3addcondition.png)

5. Nella scheda Configura logica del segnale selezionare **Ricerca log personalizzata**

    ![Fare clic su Ricerca log personalizzata](./media/alerts/4customlogsearch.png)

6. Nella scheda ricerca log personalizzata, immettere la query nella casella "Query di ricerca". Per questo esempio, useremo la query Kusto seguente.
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

    ![Digitare query nella casella di ricerca](./media/alerts/5searchquery.png)
    
    > [!NOTE]
    > È inoltre possibile applicare questi passaggi ad altri tipi di avvisi basati su query. Puoi saperne di più sulla lingua di query Kusto da questo [kusto getting started doc](https://docs.microsoft.com/azure/kusto/concepts/) o questo [foglio trucco sql a Kusto](https://docs.microsoft.com/azure/kusto/query/sqlcheatsheet)

7. In "Logica di avviso", scegli se basarsi sul numero di risultati o sulla misurazione metrica. Quindi selezionare la condizione (maggiore di, uguale a, minore di) e una soglia. Durante la modifica di questi valori, è possibile notare le modifiche alla frase di anteprima della condizione. In questo esempio stiamo usando "uguale a".

    ![In Logica di avviso scegliere tra le opzioni fornite in base a e condizione, quindi digitare una soglia](./media/alerts/6alertlogic.png)

8. In "Valutato in base a", impostare il periodo e la frequenza. Il punto qui deve corrispondere al valore che abbiamo inserito per periodo nella query precedente. Quindi fare clic su **Fatto**.

    ![Impostare il periodo e la frequenza in basso e quindi fare clic su Fatto](./media/alerts/7evaluate.png)

9. Ora vediamo la condizione che abbiamo creato con il costo mensile stimato. Di seguito sotto ["Gruppi di azioni"](../platform/action-groups.md) puoi creare un nuovo gruppo o selezionarne uno esistente. Se lo si desidera, è possibile personalizzare le azioni.

    ![fare clic sui pulsanti seleziona o crea in Gruppo di azioni](./media/alerts/8actiongroup.png)

10. Infine aggiungere i dettagli dell'avviso (nome della regola di avviso, descrizione, gravità). Al termine, fare clic su **Crea regola** di avviso nella parte inferiore.

    ![In Dettagli avviso digitare il nome della regola di avviso, scrivere una descrizione e selezionare un livello di gravità](./media/alerts/9alertdetails.png)

## <a name="how-to-unsubscribe-from-classic-alert-e-mail-notifications"></a>Come annullare l'iscrizione alle notifiche e-mail di avviso classiche

Questa sezione si applica agli avvisi di **disponibilità classica,** agli **avvisi di metrica classica**di Application Insights e agli avvisi di anomalie di errore **classici.**

Si ricevono notifiche tramite posta elettronica per questi avvisi classici se si verifica una delle condizioni seguenti:

* L'indirizzo di posta elettronica è elencato nel campo Destinatari di posta elettronica di notifica nelle impostazioni della regola di avviso.

* Viene attivata l'opzione per inviare notifiche tramite posta elettronica agli utenti che detengono determinati ruoli per la sottoscrizione e si ha un ruolo per la sottoscrizione di Azure specifica.

![Schermata di notifica degli avvisi](./media/alerts/alert-notification.png)

Per controllare meglio la sicurezza e la privacy, in genere è consigliabile specificare in modo esplicito i destinatari delle notifiche per gli avvisi classici nel campo **Destinatari e-mail** di notifica. L'opzione per notificare a tutti gli utenti che detengono determinati ruoli viene fornita per la compatibilità con le versioni precedenti.

Per annullare la sottoscrizione alle notifiche e-mail generate da una determinata regola di avviso, rimuovere l'indirizzo di posta elettronica dal campo Destinatari di posta elettronica di **notifica.**

Se il tuo indirizzo e-mail non è elencato in modo esplicito, ti consigliamo di disattivare l'opzione per notificare automaticamente a tutti i membri determinati ruoli e elencare invece tutti i messaggi di posta elettronica degli utenti che devono ricevere notifiche per quella regola di avviso nell'e-mail di notifica campo destinatari.

## <a name="who-receives-the-classic-alert-notifications"></a>Chi riceve le notifiche di avviso (classiche)?

Questa sezione si applica solo agli avvisi classici e aiuterà a ottimizzare le notifiche di avviso assicurando che siano ricevute solo dai destinatari desiderati. Per ulteriori informazioni sulla differenza tra [gli avvisi classici](../platform/alerts-classic.overview.md) e la nuova esperienza di avviso, vedere l'articolo panoramica degli [avvisi.](../platform/alerts-overview.md) Per controllare la notifica degli avvisi nella nuova esperienza di avviso, utilizzare i [gruppi di azioni](../platform/action-groups.md).

* Consigliamo di usare destinatari specifici per le notifiche di avviso classiche.

* Per gli avvisi nelle metriche Application Insights, incluse le metriche di disponibilità, l'opzione **in blocco/gruppo** della casella di controllo, se abilitata, invia notifiche agli utenti con i ruoli proprietario, collaboratore o lettore nella sottoscrizione. In effetti, _tutti_ gli utenti con accesso alla sottoscrizione della risorsa di Application Insights rientrano nell'ambito e riceveranno le notifiche.

> [!NOTE]
> Se attualmente si usa l'opzione **in blocco/gruppo** della casella di controllo e la si disabilita, sarà impossibile annullare le modifiche.

Usare la nuova esperienza di avviso/avvisi quasi in tempo reale se si desidera inviare notifiche agli utenti in base ai ruoli. Con [gruppi di azioni](../platform/action-groups.md), è possibile configurare notifiche tramite posta elettronica agli utenti con ruolo proprietario, collaboratore o lettore (non combinati insieme come una singola opzione).

## <a name="automation"></a>Automazione
* [Usare PowerShell per automatizzare la configurazione degli avvisi](../../azure-monitor/app/powershell-alerts.md)
* [Usare webhook per automatizzare la risposta agli avvisi](../../azure-monitor/platform/alerts-webhooks.md)

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

