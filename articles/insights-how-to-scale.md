<properties title="How to scale a website" pageTitle="How to scale a website" description="Learn how to scale your hosting plan in Azure." authors="stepsic"  />

<tags ms.service="application-insights" ms.workload="tbd" ms.tgt_pltfrm="ibiza" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="stepsic" />

# Come scalare un sito Web

Nel portale di gestione di Azure è possibile impostare manualmente il conteggio delle istanze del piano di hosting Web oppure impostare i parametri per la scalabilità automatica. Prima di configurare il ridimensionamento del piano di hosting Web, è necessario tenere presente che la scalabilità è influenzata dalle dimensioni delle istanze. Istanze di dimensioni superiori sono dotate di più core e memoria quindi consentiranno di ottenere prestazioni migliori per lo stesso numero di istanze.

La scala influisce sul piano di hosting Web nel suo complesso. Quando si crea un sito Web è possibile creare un piano di hosting Web nuovo o usarne uno esistente. Quando si dispone di un piano di hosting Web, tutti i siti condivideranno le stesse istanze, quindi si ridimensioneranno insieme.

## Ridimensionamento di un piano di hosting Web

1.  Nel [portale di gestione di Azure][portale di gestione di Azure] fare clic su **Sfoglia**, quindi su **Siti Web** e quindi fare clic sul nome del sito Web per aprire il pannello.
2.  La parte **Scala** della finestra **Operazioni** nel pannello del sito Web riporterà lo stato del piano di hosting Web: **Disattivato** nel caso in cui il ridimensionamento venga effettuato manualmente, **Prestazioni** se viene effettuato in base a una o più metriche delle prestazioni e **Pianificazione** se sono stati abilitati più profili di scalabilità automatica.  
    ![Scale part][Scale part]
3.  Facendo clic sulla parte verrà visualizzato il pannello **Scale**. Nella parte superiore del pannello è possibile visualizzare una cronologia delle azioni di scalabilità automatica per il piano di hosting Web.

    ![Scale blade][Scale blade]

4.  Il dispositivo di scorrimento **Instance** consente di regolare manualmente il numero di macchine virtuali che eseguono il piano di hosting Web.
5.  Se si desidera regolare automaticamente il numero di istanze in base al carico, selezionare **Autoscale Mode** e quindi **Performance**. Al momento, nell'anteprima del portale di gestione di Azure non è possibile selezionare **Pianificazione**.  
    ![Scale blade with CPU Percentage][Scale blade with CPU Percentage]
6.  Dopo aver selezionato Performance, si notano due modifiche:
    - **Instance Range** consente ora di scegliere un conteggio massimo e minimo di istanze. Autoscale manterrà i valori sempre entro questo intervallo, a prescindere dal carico.
    -   Nella sezione **Target Metrics** è possibile definire le metriche delle prestazioni.

7.  La sezione **CPU Percentage** consente di impostare un limite per l'uso medio della CPU in tutte le istanze del piano di hosting Web. Quando l'uso medio della CPU supererà il valore massimo definito, si verificherà un aumento.

Con la scalabilità automatica abilitata, nella parte del pannello del sito Web verrà visualizzato **Prestazioni** e nel grafico sarà possibile visualizzare la cronologia della scalabilità.

![Scale blade with CPU Percentage][1]

Si noti che nel portale di gestione di Azure non è possibile modificare il numero di istanze di un piano di hosting Web condiviso.

## Ridimensionamento avanzato

Nel portale di gestione di Azure è ora possibile effettuare ridimensionamenti in base a metriche diverse dalla percentuale CPU e persino avere un set di regole di ridimensionamento complesse.

### Ridimensionamento basato su altre metriche delle prestazioni

Oltre alla CPU, è possibile effettuare ridimensionamenti basati su:

-   Utilizzo medio della memoria - Se la percentuale media di memoria utilizzata nelle istanze sale o scende oltre una soglia specificata, vengono aggiunte o rimosse istanze.
-   Profondità coda HTTP o Profondità coda del disco - Se il numero di messaggi nella coda di richieste HTTP o nella coda del disco sale o scende oltre una soglia specificata, vengono aggiunte o rimosse istanze.

È possibile effettuare il ridimensionamento in base a un'altra metrica in due modi diversi. Se si intende scalare in base a una sola metrica, selezionare il simbolo \> accanto al dispositivo di scorrimento **CPU Percentage**. Verrà visualizzato il pannello Metric Details.

![Punto di ingresso per il ridimensionamento basato sulle metriche][Punto di ingresso per il ridimensionamento basato sulle metriche]

Per scalare in base a più di una metrica alla volta, è possibile fare clic su **Add Metric** nella barra dei comandi:

![Aggiungi metriche][Aggiungi metriche]

Il pannello Metric Detail contiene tutti i controlli necessari per configurare il profilo di scalabilità ottimale. Nella parte superiore, scegliere la nuova metrica in base alla quale effettuare il ridimensionamento.

### Ridimensionamento in più passaggi

Sotto il grafico relativo alla metrica sono visualizzate due sezioni: **Scale up rules** e **Scale down rules**. Se **una qualsiasi** delle regole relative all'aumento viene rispettata, il servizio procederà all'aumento. Al contrario, se **tutte** le regole relative alla riduzione vengono rispettate, il servizio procederà alla riduzione.

Per ogni regola è possibile scegliere:

-   Condition - Maggiore di o Minore di
-   Threshold - Numero che la metrica deve superare per attivare l'azione
-   Over Past - Numero di minuti su cui viene calcolata la media per la metrica
-   Scale up or down by - Dimensioni dell'azione di scalabilità
-   Cool down - Intervallo di attesa applicato alla regola dopo l'azione di scalabilità precedente, prima di una nuova azione di scalabilità

![Regole multiple di scalabilità][Regole multiple di scalabilità]

Le regole multiple di scalabilità consentono di agire con maggiore decisione nei confronti di aumenti (o riduzioni) basati su modifiche delle prestazioni. Ad esempio, è possibile definire due regole di scalabilità:

1.  Aumentare di 1 istanza se la percentuale CPU supera il 60%
2.  Aumentare di 3 istanze se la percentuale CPU supera l'85%

Con questa regola aggiuntiva, se il carico supera l'85% prima che si verifichi un ridimensionamento, si otterranno non una ma due istanze aggiuntive.

  [portale di gestione di Azure]: https://portal.azure.com/
  [Scale part]: ./media/insights-how-to-scale/Insights_ScalePartOff.png
  [Scale blade]: ./media/insights-how-to-scale/Insights_ScaleBladeDayZero.png
  [Scale blade with CPU Percentage]: ./media/insights-how-to-scale/Insights_ScaleBladeCPU.png
  [1]: ./media/insights-how-to-scale/Insights_ScalePartBladeOn.png
  [Punto di ingresso per il ridimensionamento basato sulle metriche]: ./media/insights-how-to-scale/Insights_ScaleMetricChevron.png
  [Aggiungi metriche]: ./media/insights-how-to-scale/Insights_AddMetric.png
  [Regole multiple di scalabilità]: ./media/insights-how-to-scale/Insights_MultipleScaleRules.png
