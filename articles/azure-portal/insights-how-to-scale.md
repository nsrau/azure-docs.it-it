<properties
	pageTitle="Ridimensionare il conteggio delle istanze manualmente o automaticamente| Microsoft Azure"
	description="Informazioni su come scalare i servizi di Azure."
	authors="rboucher"
	manager=""
	editor=""
	services="monitoring-and-diagnostics"
	documentationCenter="monitoring-and-diagnostics"/>  

<tags
	ms.service="monitoring-and-diagnostics"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/08/2015"
	ms.author="robb"/>  

# Scalare il conteggio delle istanze manualmente o automaticamente

Nel [portale di Azure](https://portal.azure.com/) è possibile impostare manualmente il conteggio delle istanze del servizio oppure impostare i parametri per la scalabilità automatica in base alla domanda. Questa impostazione è definita in genere *scalabilità orizzontale* o *riduzione*.

Prima di scalare in base al conteggio delle istanze, occorre tenere presente che la scalabilità è influenzata dal **piano tariffario** oltre che dal conteggio delle istanze. A seconda del piano tariffario possono variare il numero di memorie centrali e la memoria e quindi si avranno prestazioni migliori per lo stesso numero di istanze (ovvero *aumento* o *riduzione*). Questo articolo descrive in particolare la *riduzione* e la *scalabilità orizzontale*.

È possibile ridurre il portale e anche usare l'[API REST](https://msdn.microsoft.com/library/azure/dn931953.aspx) o [.NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Insights/) per regolare la scalabilità manualmente o automaticamente.

## Scalabilità manuale

1. Nel [portale di Azure](https://portal.azure.com/) fare clic su **Sfoglia**, quindi passare alla risorsa da scalare, ad esempio un **piano di servizio App**.

2. Il riquadro **Scalabilità** in **Operazioni** indicherà lo stato della scalabilità: **Disattivato** nel caso in cui il ridimensionamento venga effettuato manualmente, **Attivato** nel caso in cui il ridimensionamento venga effettuato in base a una o più metriche delle prestazioni. ![Riquadro Scalabilità](./media/insights-how-to-scale/Insights_UsageLens.png)

3. Facendo clic sul riquadro, verrà visualizzato il pannello **Scalabilità**. Nella parte superiore del pannello è possibile visualizzare una cronologia delle azioni di scalabilità automatica per il servizio. ![Scale blade](./media/insights-how-to-scale/Insights_ScaleBladeDayZero.png)

>[AZURE.NOTE] In questo grafico verranno visualizzate solo le azioni eseguite con la scalabilità automatica. Se si regola manualmente il conteggio delle istanze, la modifica non verrà applicata nel grafico.

4. È possibile regolare manualmente il numero di **Istanze** con il dispositivo di scorrimento.
5. Fare clic sul comando **Salva** e si passerà quasi immediatamente a quel numero di istanze.

## Scalabilità basata su una metrica preimpostata

Per regolare automaticamente il numero di istanze in base a una metrica, selezionare la metrica desiderata nell'elenco a discesa **Ridimensiona di**. Ad esempio, per un **piano di servizio App** è possibile scalare in base alla **percentuale CPU**.

1. Quando si seleziona una metrica appare un dispositivo di scorrimento e/o alcune caselle di testo in cui immettere il numero di istanze tra cui applicare la scalabilità:

    ![Scale blade with CPU Percentage](./media/insights-how-to-scale/Insights_ScaleBladeCPU.png)

    Con la scalabilità automatica il servizio non sarà mai al di sotto o al di sopra dei limiti impostati, indipendentemente dal carico.

2. Quindi si scegliere l'intervallo di destinazione per la metrica. Ad esempio, se si sceglie **Percentuale CPU**, è possibile impostare un limite per l'utilizzo medio della CPU in tutte le istanze del servizio. Quando l'utilizzo medio della CPU supererà il valore massimo definito, si verificherà una scalabilità orizzontale. Allo stesso modo, quando l'utilizzo medio della CPU scende sotto il valore minimo, si verificherà una riduzione.

3. Fare clic sul comando **Salva**. Scalabilità automatica eseguirà un controllo ogni pochi minuti per assicurarsi che vengano rispettati l'intervallo di istanze e il limite per la metrica. Quando il servizio riceve altro traffico, il numero di istanze aumenterà automaticamente.

## Scalabilità in base ad altre metriche

È possibile scalare in base a metriche diverse da quelle preimpostate che appaiono nell'elenco a discesa **Ridimensiona di** elenco a discesa e persino avere un set di regole di ridimensionamento complesse.

### Aggiunta o modifica di una regola

1. Scegliere **regole per la pianificazione e le prestazioni** nell'elenco a discesa **Ridimensiona di**: ![Regole delle prestazioni](./media/insights-how-to-scale/Insights_PerformanceRules.png)

2. Se prima era attiva la scalabilità automatica, si vedranno esattamente le regole impostate.

3. Per scalare in base a un'altra metrica, fare clic sulla riga **Aggiungi regola**. È anche possibile fare clic su una delle righe esistenti per passare dalla metrica impostata in precedenza alla metrica in base alla quale si vuole scalare. ![Aggiungi regola](./media/insights-how-to-scale/Insights_AddRule.png)

4. Ora è necessario selezionare la metrica in base a cui si desidera scalare. Quando si sceglie una metrica, esistono un paio di aspetti da considerare:
    * La *risorsa* da cui proviene la metrica. In genere, sarà la stessa della risorsa da scalare. Tuttavia, se si desidera scalare in base alla profondità di una coda di archiviazione, la risorsa è la coda in base a cui si desidera scalare.
    * Il *nome della metrica* stesso.
    * L'*aggregazione temporale* della metrica, ovvero il modo in cui i dati vengono combinati per la *durata*.

5. Dopo aver scelto la metrica, si scelgono la soglia per la metrica e l'operatore. Ad esempio, è possibile specificare **Maggiore di** **80%**.

6. Scegliere quindi l'azione che si desidera eseguire. Esistono diversi tipi di azioni:
    * Aumentare o ridurre di: il numero di istanze (**Valore**) definito verrà aggiunto o rimosso
    * Aumentare o ridurre la percentuale: il conteggio delle istanze verrà modificato in base a una percentuale. Ad esempio, è possibile inserire 25 nel campo **Valore** e, se si dispone attualmente di 8 istanze, ne verrebbero aggiunte 2.
    * Aumentare o diminuire fino a: il conteggio delle istanze verrà impostato sul **Valore** definito.

7. Infine è possibile scegliere Disattiva regole dopo: intervallo di attesa applicato alla regola dopo l'azione di scalabilità precedente, prima di una nuova azione di scalabilità.

8. Dopo avere configurato la regola, fare clic su **OK**.

9. Una volta configurate tutte le regole desiderate, assicurarsi di fare clic sul comando **Salva**.

### Ridimensionamento in più passaggi

Gli esempi precedenti sono piuttosto semplici. Se però si vuole agire con maggiore decisione nei confronti di aumenti (o riduzioni), si possono persino aggiungere più regole di scalabilità per la stessa metrica. Ad esempio, è possibile definire due regole di scalabilità per la percentuale CPU:

1. Aumentare di 1 istanza se la percentuale CPU supera il 60%
2. Aumentare di 3 istanze se la percentuale CPU supera l'85%

![Regole multiple di scalabilità](./media/insights-how-to-scale/Insights_MultipleScaleRules.png)

Con questa regola aggiuntiva, se il carico supera l'85% prima che si verifichi un ridimensionamento, si otterranno non una ma due istanze aggiuntive.

## Scalare in base a una pianificazione


Per impostazione predefinita, una regola di scalabilità, una volta creata, viene applicata sempre. È possibile verificarlo quando si fa clic sull'intestazione del profilo:

![Profilo](./media/insights-how-to-scale/Insights_Profile.png)

Tuttavia, è possibile agire con maggiore decisione nei confronti della scalabilità durante il giorno, o durante la settimana, che nel fine settimana. Si può persino arrestare completamente il servizio nelle ore non lavorative.

1. A tale scopo, nel profilo selezionare **ricorrenza** anziché **sempre** e scegliere gli orari in cui si desidera applicare il profilo.

2. Ad esempio, per applicare un profilo durante la settimana, nell'elenco a discesa **Giorni** deselezionare **Sabato** e **Domenica**.

3. Per applicare un profilo durante il giorno, impostare **Ora di inizio** sull'ora del giorno in cui si desidera iniziare. ![Ricorrenza predefinita](./media/insights-how-to-scale/Insights_ProfileRecurrence.png)

4. Fare clic su **OK**.

5. Successivamente, sarà necessario aggiungere il profilo da applicare in altri orari. Fare clic sulla riga **Aggiungi profilo**. ![Non al lavoro](./media/insights-how-to-scale/Insights_ProfileOffWork.png)

6. Assegnare un nome al secondo nuovo profilo, ad esempio **Non al lavoro**.

7. Selezionare quindi nuovamente **ricorrenza** e scegliere l'intervallo di conteggio delle istanze desiderato per questo periodo.

8. Come con il profilo predefinito, in **Giorni** scegliere il numero di giorni da applicare a questo profilo e in **Ora di inizio** un'ora del giorno.

>[AZURE.NOTE] Scalabilità automatica userà le regole dell'ora legale per qualsiasi **fuso orario** selezionato. Tuttavia, con l'ora legale l'offeset UTC visualizzerà l'offset del fuso orario di base, non l'offset UTC dell'ora legale.

9. Fare clic su **OK**.

10. A questo punto, sarà necessario aggiungere le regole da applicare con il secondo profilo. Fare clic su **Aggiungi regola** per costruire la stessa regola applicata con il profilo predefinito. ![Aggiungi regola a Non al lavoro](./media/insights-how-to-scale/Insights_RuleOffWork.png)

11. Assicurarsi di creare una regola sia per la scalabilità orizzontale che per la riduzione, altrimenti durante il profilo il conteggio delle istanze aumenterà (o diminuirà) soltanto.

12. Infine, fare clic su **Salva**.

## Passaggi successivi

* [Monitorare le metriche del servizio](insights-how-to-customize-monitoring.md) per assicurarsi che il servizio sia disponibile e reattivo.
* [Abilitare il monitoraggio e la diagnostica](insights-how-to-use-diagnostics.md) per raccogliere metriche dettagliate e ad alta frequenza sul servizio.
* [Ricevere notifiche di avviso](insights-receive-alert-notifications.md) ogni volta che si verificano eventi operativi o le metriche superano una soglia.
* [Monitorare le prestazioni dell'applicazione](insights-perf-analytics.md) se si desidera comprendere esattamente il comportamento del codice nel cloud.
* [Visualizzare eventi e registri di controllo](insights-debugging-with-events.md) per informazioni su tutti gli eventi che si sono verificati nel servizio.
* [Monitorare la disponibilità e i tempi di risposta di qualsiasi pagina Web](../application-insights/app-insights-monitor-web-app-availability.md) con Application Insights per definire se la pagina è inattiva.

<!---HONumber=AcomDC_0810_2016-->