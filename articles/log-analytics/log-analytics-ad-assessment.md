<properties
	pageTitle="Ottimizzare l'ambiente con la soluzione Active Directory Assessment in Log Analytics | Microsoft Azure"
	description="È possibile usare la soluzione Active Directory Assessment per valutare i rischi e l'integrità degli ambienti server a intervalli regolari."
	services="log-analytics"
	documentationCenter=""
	authors="bandersmsft"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="log-analytics"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/28/2016"
	ms.author="banders"/>

# Ottimizzare l'ambiente con la soluzione Active Directory Assessment in Log Analytics

È possibile usare la soluzione Active Directory Assessment per valutare i rischi e l'integrità degli ambienti server a intervalli regolari. Questo articolo consente di installare e usare la soluzione in modo che si possano intraprendere azioni correttive per problemi potenziali.

La soluzione offre un elenco con priorità di raccomandazioni specifiche per l'infrastruttura distribuita dei server, classificate in quattro aree di interesse che consentono di comprendere rapidamente il rischio e agire in maniera appropriata.

Le raccomandazioni si basano sulla conoscenza e sull'esperienza acquisite dai tecnici Microsoft in base a migliaia di visite dei clienti. Ogni raccomandazione fornisce informazioni aggiuntive sui motivi per cui un problema può essere rilevante per l'utente e su come implementare le modifiche suggerite.

Si possono scegliere le aree di interesse più importanti per l'organizzazione e tenere traccia dello stato di avanzamento verso la realizzazione di un ambiente integro ed esente da rischi.

Dopo aver aggiunto la soluzione e completato una valutazione, nel dashboard **AD Assessment** per l'infrastruttura nell'ambiente verranno visualizzate le informazioni di riepilogo per l'area di interesse. Le sezioni seguenti descrivono come usare le informazioni visualizzate nel dashboard **AD Assessment**, in cui sarà possibile intraprendere le azioni consigliate per l'infrastruttura dei server di Active Directory.

![immagine del riquadro SQL Assessment](./media/log-analytics-ad-assessment/ad-tile.png)

![Immagine del dashboard SQL Assessment](./media/log-analytics-ad-assessment/ad-assessment.png)


## Installazione e configurazione della soluzione
Usare le informazioni seguenti per installare e configurare le soluzioni.

- La soluzione Active Directory Assessment richiede l'installazione di .NET Framework 4 in ogni computer che contiene un agente OMS.
- Aggiungere la soluzione Active Directory Assessment all'area di lavoro OMS usando la procedura che descrive come [aggiungere soluzioni di Log Analytics dalla raccolta soluzioni](log-analytics-add-solutions.md). Non è richiesta alcuna ulteriore configurazione.

    >[AZURE.NOTE] Dopo aver aggiunto la soluzione, il file AdvisorAssessment.exe viene aggiunto al server con agenti. I dati di configurazione vengono letti e quindi inviati al servizio OMS nel cloud per l'elaborazione. Viene applicata la logica ai dati ricevuti, quindi questi ultimi vengono registrati nel servizio cloud.

## Informazioni dettagliate sulla raccolta dei dati di valutazione

La tabella seguente illustra i metodi di raccolta dati per gli agenti, se Operations Manager (SCOM) è obbligatorio e la frequenza con cui i dati vengono raccolti da un agente.

| Piattaforma | Agente diretto | Agente SCOM | Archiviazione di Azure | SCOM obbligatorio? | Dati dell'agente SCOM inviati con il gruppo di gestione | Frequenza della raccolta |
|---|---|---|---|---|---|---|
|Windows|![Sì](./media/log-analytics-ad-assessment/oms-bullet-green.png)|![Sì](./media/log-analytics-ad-assessment/oms-bullet-green.png)|![No](./media/log-analytics-ad-assessment/oms-bullet-red.png)|	![No](./media/log-analytics-ad-assessment/oms-bullet-red.png)|![Sì](./media/log-analytics-ad-assessment/oms-bullet-green.png)|	7 giorni|


## Informazioni sulla classificazione in ordine di priorità delle raccomandazioni

A ogni raccomandazione generata viene assegnato un valore di ponderazione che identifica l'importanza relativa della raccomandazione. Vengono visualizzate solo le dieci raccomandazioni più importanti.

### Come vengono calcolate le ponderazioni

Le ponderazioni sono valori aggregati che si basano su tre fattori chiave:

- La *probabilità* che un problema identificato causi inconvenienti. Una probabilità più elevata equivale a un punteggio complessivamente maggiore per la raccomandazione.

- L'*impatto* del problema per l'organizzazione se causa effettivamente un problema. Un impatto più elevato equivale a un punteggio complessivamente maggiore per la raccomandazione.

- Il *lavoro* richiesto per implementare la raccomandazione. Un lavoro richiesto più elevato equivale a un punteggio complessivamente inferiore per la raccomandazione.

La ponderazione per ogni raccomandazione è espressa come percentuale del punteggio totale disponibile per ogni area di interesse. Ad esempio, se una raccomandazione nell'area di interesse relativa a sicurezza e conformità ha un punteggio pari al 5%, l'implementazione della raccomandazione aumenterà del 5% il punteggio complessivo di quell'area.

### Aree di interesse

**Sicurezza e conformità**: quest'area di interesse mostra le raccomandazioni per le potenziali minacce e violazioni della sicurezza, i criteri aziendali e i requisiti di conformità tecnici, legali e normativi.

**Disponibilità e continuità aziendale**: quest'area di interesse mostra le raccomandazioni per la disponibilità del servizio, la resilienza dell'infrastruttura e la protezione aziendale.

**Prestazioni e scalabilità**: quest'area di interesse mostra le raccomandazioni utili per la crescita dell'infrastruttura IT aziendale, assicurandosi che l'ambiente IT soddisfi gli attuali requisiti in termini di prestazioni e che possa rispondere alle mutevoli esigenze dell'infrastruttura.

**Aggiornamento, migrazione e distribuzione**: quest'area di interesse mostra raccomandazioni utili per aggiornare, eseguire la migrazione e distribuire Active Directory nell'infrastruttura esistente.

### È consigliabile mirare a ottenere un punteggio del 100% in tutte le aree di interesse?

Non necessariamente. Le raccomandazioni si basano sulla conoscenza e sull'esperienza acquisite dai tecnici Microsoft attraverso migliaia di visite dei clienti. Non esistono tuttavia due infrastrutture di server uguali e raccomandazioni specifiche possono essere più o meno pertinenti per l'utente. Ad esempio, alcune raccomandazioni sulla sicurezza possono risultare meno pertinenti se le macchine virtuali non sono esposte a Internet. Alcune raccomandazioni sulla disponibilità possono risultare meno pertinenti per i servizi che forniscono creazione di report e raccolta dei dati ad hoc a bassa priorità. I problemi che possono essere importanti per un'azienda collaudata possono esserlo meno per una start-up. È consigliabile identificare quali sono le proprie aree di interesse prioritarie e quindi osservare il cambiamento dei punteggi nel tempo.

Ogni raccomandazione include informazioni aggiuntive sui motivi per cui potrebbe essere importante. È consigliabile usare queste informazioni aggiuntive per valutare se l'implementazione della raccomandazione è appropriata, a seconda della natura dei servizi IT e delle esigenze aziendali dell'organizzazione.

## Usare le raccomandazioni relative all'area di interesse della valutazione

Prima di poter usare una soluzione di valutazione in OMS, è necessario averla installata. Per altre informazioni sull'installazione di soluzioni, vedere come [aggiungere soluzioni di Log Analytics dalla raccolta soluzioni](log-analytics-add-solutions.md). Dopo l'installazione, sarà possibile visualizzare il riepilogo delle raccomandazioni usando il riquadro Assessment della pagina Overview in OMS.

Visualizzare il riepilogo delle valutazioni relative alla conformità per l'infrastruttura, quindi visualizzare le raccomandazioni nel dettaglio.


### Per visualizzare le raccomandazioni per un'area di interesse e applicare un'azione correttiva

1. Nella pagina **Overview** fare clic sul riquadro **Assessment** per l'infrastruttura del server.
2. Nella pagina **Assessment** verificare le informazioni di riepilogo in uno dei pannelli dell'area di interesse, quindi fare clic su un'area specifica per visualizzare le raccomandazioni corrispondenti.
3. In una delle pagine relative alle aree di interesse è possibile visualizzare le raccomandazioni relative all'ambiente specifico, classificate in ordine di priorità. Fare clic su una raccomandazione in **Affected Objects** per visualizzare i dettagli relativi al motivo per cui è stata generata. ![immagine delle raccomandazioni di Assessment](./media/log-analytics-ad-assessment/ad-focus.png)
4. È possibile effettuare le azioni correttive suggerite in **Suggested Actions**. Dopo la risoluzione dell'elemento, le valutazioni successive indicheranno che le azioni consigliate sono state effettuate e il punteggio relativo alla conformità aumenterà. Gli elementi corretti vengono visualizzati come **Passed Objects**.

## Ignorare le raccomandazioni

Per ignorare delle raccomandazioni è possibile creare un file di testo che OMS userà per impedirne la visualizzazione nei risultati della valutazione.

### Per identificare le raccomandazioni che verranno ignorate

1.	Accedere all'area di lavoro e aprire Ricerca log. Usare la query seguente per elencare le raccomandazioni non riuscite per i computer nell'ambiente.

    ```
    Type=ADAssessmentRecommendation RecommendationResult=Failed | select  Computer, RecommendationId, Recommendation | sort  Computer
    ```

    Di seguito è riportata una schermata che mostra la query di Ricerca log: ![raccomandazioni non riuscite](./media/log-analytics-ad-assessment/ad-failed-recommendations.png)

2.	Scegliere le raccomandazioni che si vogliono ignorare. Nella procedura successiva verranno usati i valori per ID raccomandazione.


### Per creare e usare un file di testo IgnoreRecommendations.txt

1.	Creare un file denominato IgnoreRecommendations.txt.
2.	Incollare o digitare ciascun ID raccomandazione per ogni raccomandazione che Log Analytics dovrà ignorare in una riga separata e quindi salvare e chiudere il file.
3.	Inserire il file nella cartella seguente in ogni computer in cui si vuole che OMS ignori le raccomandazioni.
    - Nei computer con Microsoft Monitoring Agent (connesso direttamente o attraverso Operations Manager) - *SystemDrive*: \\Programmi\\Microsoft Monitoring Agent\\Agent
    - Nel server di gestione Operations Manager - *SystemDrive*: \\Programmi\\Microsoft System Center 2012 R2\\Operations Manager\\Server

### Per verificare che le raccomandazioni vengano ignorate

Dopo la successiva esecuzione della valutazione pianificata, per impostazione predefinita ogni 7 giorni, le raccomandazioni specificate sono contrassegnate come *ignorate* e non verranno visualizzate nel dashboard di valutazione.

1. È possibile usare le query di Ricerca log seguenti per elencare tutte le raccomandazioni ignorate.

    ```
    Type=ADAssessmentRecommendation RecommendationResult=Ignored | select  Computer, RecommendationId, Recommendation | sort  Computer
    ```

2.	Se in seguito si decide che si vogliono vedere le raccomandazioni ignorate, rimuovere eventuali file IgnoreRecommendations.txt oppure è possibile rimuovere gli ID raccomandazione dagli stessi.

## Domande frequenti sulle soluzioni AD Assessment

*Con quale frequenza viene eseguita la valutazione?*
- La valutazione viene eseguita ogni 7 giorni.

*È possibile configurare la frequenza di esecuzione della valutazione?*
- Attualmente non è possibile.

*Se viene rilevato un altro server dopo l'aggiunta della soluzione per la valutazione, il server verrà valutato?*
- Sì, a partire dal rilevamento verrà valutato ogni 7 giorni.

*Se un server viene rimosso, quando sarà rimosso dalla valutazione?*
- Se un server non invia dati per 3 settimane, verrà rimosso.

*Qual è il nome del processo che esegue la raccolta di dati?*
- AdvisorAssessment.exe

*Quanto tempo occorre per la raccolta di dati?*
- La raccolta di dati effettiva sul server richiede circa 1 ora. Potrebbe essere necessario più tempo nei server in cui è presente un numero elevato di server di Active Directory.

*Quali tipi di dati vengono raccolti?*
- Vengono raccolti i tipi di dati seguenti:
    - WMI
    - Registro
    - Contatori delle prestazioni

*È possibile definire l'orario per la raccolta di dati?*
- Attualmente non è possibile.

*Perché vengono visualizzate solo le prime 10 raccomandazioni?*
- Invece di esaminare un lunghissimo elenco completo di attività, è consigliabile concentrare l'attenzione sulle raccomandazioni con priorità maggiore. Dopo la verifica delle raccomandazioni principali, verranno rese disponibili raccomandazioni aggiuntive. Se si preferisce visualizzare l'elenco dettagliato, usare Ricerca log per mostrare tutte le raccomandazioni.

*È possibile ignorare una raccomandazione?*
- Sì. Vedere la sezione [Ignorare le raccomandazioni](#ignore-recommendations) sopra riportata.


## Passaggi successivi

- Per visualizzare dati dettagliati e raccomandazioni di AD Assessment usare le [Ricerche log in Log Analytics](log-analytics-log-searches.md).

<!---HONumber=AcomDC_0504_2016-->