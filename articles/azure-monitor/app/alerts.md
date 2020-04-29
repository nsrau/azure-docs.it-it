---
title: Impostare gli avvisi in applicazione Azure Insights
description: Ricevere notifiche su tempi di risposta più lenti, eccezioni e altre prestazioni o modifiche nell'uso delle app Web.
ms.topic: conceptual
ms.date: 01/23/2019
ms.reviewer: lagayhar
ms.subservice: alerts
ms.openlocfilehash: 28fd59556a586b85a6d3caf188d9e02c11d31e3b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80295088"
---
# <a name="set-alerts-in-application-insights"></a>Impostare gli avvisi in Application Insights

[Application Insights di Azure][start] può informare di eventuali cambiamenti nelle prestazioni e nelle metriche di utilizzo dell'app Web in uso. 

Application Insights esegue il monitoraggio dell'app attiva in un'[ampia varietà di piattaforme][platforms] per consentire di diagnosticare i problemi di prestazioni e acquisire informazioni sui modelli di utilizzo.

Sono disponibili più tipi di avvisi:

* Gli [**avvisi delle metriche**](../../azure-monitor/platform/alerts-metric-overview.md) indicano quando una metrica supera un valore di soglia per un determinato periodo, ad esempio i tempi di risposta, i conteggi delle eccezioni, l'utilizzo della CPU o le visualizzazioni di pagina.
* Gli [**avvisi del log**](../../azure-monitor/platform/alerts-unified-log.md) vengono usati per descrivere gli avvisi in cui il segnale di avviso si basa su una query kusto personalizzata.
* I [**test Web**][availability] indicano quando il sito non è disponibile in Internet o risponde lentamente. [Altre informazioni][availability]
* Gli avvisi della [**diagnostica proattiva**](../../azure-monitor/app/proactive-diagnostics.md) vengono configurati automaticamente per informare su schemi di prestazioni insoliti.

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

Se l'indirizzo di posta elettronica non è elencato in modo esplicito, è consigliabile disabilitare l'opzione per notificare automaticamente a tutti i membri di determinati ruoli, elencando invece tutti i messaggi di posta elettronica degli utenti che devono ricevere le notifiche per la regola di avviso nel campo destinatari di messaggi di posta elettronica di notifica.

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

