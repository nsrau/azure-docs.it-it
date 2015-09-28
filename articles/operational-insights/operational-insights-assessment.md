<properties
   pageTitle="Ottimizzare l'ambiente con soluzioni di valutazione"
   description="Usare la soluzione di valutazione per valutare i rischi e l'integrità degli ambienti server a intervalli regolari"
   services="operational-insights"
   documentationCenter=""
   authors="bandersmsft"
   manager="jwhit"
   editor="" />
<tags
   ms.service="operational-insights"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/10/2015"
   ms.author="banders" />

# Ottimizzare l'ambiente con soluzioni di valutazione

[AZURE.INCLUDE [operational-insights-note-moms](../../includes/operational-insights-note-moms.md)]

È possibile usare la soluzione Assessment per valutare i rischi e l'integrità degli ambienti server a intervalli regolari e ottenere un elenco con priorità di raccomandazioni specifiche per l'infrastruttura distribuita dei server. Le raccomandazioni vengono classificate in sei aree di interesse che permettono una rapida comprensione del rischio e dell'integrità dell'infrastruttura e consentono di agire rapidamente al fine di ridurre tali rischi e migliorare l'integrità.

Sono attualmente disponibili le soluzioni di valutazione seguenti:

- Active Directory

- SQL Server

Le raccomandazioni fornite si basano sulla conoscenza e l'esperienza acquisite dai tecnici Microsoft sulla base di migliaia di visite dei clienti. Ogni raccomandazione fornisce informazioni aggiuntive sui motivi per cui ogni problema può essere rilevante per l'utente e su come implementare le modifiche suggerite.

Si possono scegliere le aree di interesse più importanti per l'organizzazione e tenere traccia dello stato di avanzamento verso la realizzazione di un ambiente integro ed esente da rischi.

Installare le soluzioni per aggiornare l'agente Operations Manager e il modulo di configurazione di base per Operational Insights e aggiungere il file AdvisorAssessment.exe ai server monitorati. I dati di configurazione vengono letti e quindi inviati al servizio Operational Insights nel cloud per l'elaborazione. Viene applicata la logica ai dati ricevuti, quindi questi ultimi vengono registrati nel servizio cloud. Dopo il completamento delle valutazioni, nel dashboard **Assessment ** per l'infrastruttura nell'ambiente verranno visualizzate le informazioni di riepilogo per l'area di interesse. Grazie alle informazioni visualizzate nel dashboard **Assessment** sarà possibile intraprendere le azioni consigliate per l'infrastruttura dei server.

![immagine del riquadro SQL Assessment](./media/operational-insights-assessment/overview-sql-assess.png)

![Immagine del dashboard SQL Assessment](./media/operational-insights-assessment/gallery-ad-01.png)


## Domande frequenti sulle soluzioni per la valutazione

*Con che frequenza viene eseguita la valutazione?*<br> La valutazione viene eseguita ogni 7 giorni.

*È possibile configurare la frequenza di esecuzione della valutazione?*<br> Attualmente non è possibile.

*Se viene rilevato un altro server dopo l'aggiunta della soluzione per la valutazione, il server verrà valutato?*<br> Sì, a partire dal rilevamento verrà valutato ogni 7 giorni.

*Se un server viene messo fuori servizio, quando sarà rimosso dalla valutazione?*<br> Se un server non invia dati per 3 settimane, verrà rimosso.

*Qual è il nome del processo che esegue la raccolta di dati?*<br> AdvisorAssessment.exe

*Quanto tempo occorre per la raccolta di dati?*<br> La raccolta di dati effettiva sul server richiede circa 1 ora. È possibile che sia necessario più tempo nei server in cui è presente un numero elevato di server di Active Directory oppure di istanze o database SQL.

*Che tipo di dati viene raccolto?*<br> Vengono raccolti i tipi di dati seguenti:

- WMI
- Registro
- Contatori delle prestazioni
- DMV (Dynamic Management View) di SQL.

*È possibile definire l'orario per la raccolta di dati?*<br> Attualmente non è possibile.

*Perché è necessario configurare un account RunAs?*<br> Per un server SQL vengono eseguite alcune SQL. Per permetterne l'esecuzione, è necessario usare un account RunAs con autorizzazioni di tipo VISUALIZZAZIONE STATO DEL SERVER per SQL. Per eseguire query relative a WMI, sono inoltre necessarie credenziali di amministratore locale.

*Perché vengono visualizzate solo le prime 10 raccomandazioni?*<br> Invece di esaminare un lunghissimo elenco completo di attività, è consigliabile concentrare l'attenzione sulle raccomandazioni con priorità maggiore. Dopo la verifica delle raccomandazioni principali, verranno rese disponibili raccomandazioni aggiuntive. Se si preferisce visualizzare l'elenco dettagliato, usare la ricerca di log di Operational Insights per mostrare tutte le raccomandazioni.

*È possibile ignorare una raccomandazione?*<br> Attualmente non è possibile.

## Informazioni sulla classificazione in ordine di priorità delle raccomandazioni

A ogni raccomandazione generata viene assegnato un valore di ponderazione che identifica l'importanza relativa della raccomandazione. Vengono visualizzate solo le dieci raccomandazioni più importanti.

### Come vengono calcolate le ponderazioni

Le ponderazioni sono valori aggregati che si basano su tre fattori chiave:

- La *probabilità* che un problema identificato causi inconvenienti. Una probabilità più elevata equivale a un punteggio complessivamente maggiore per la raccomandazione.

- L'*impatto* del problema per l'organizzazione se causa effettivamente un problema. Un impatto più elevato equivale a un punteggio complessivamente maggiore per la raccomandazione.

- Il *lavoro* richiesto per implementare la raccomandazione. Un lavoro richiesto più elevato equivale a un punteggio complessivamente inferiore per la raccomandazione.

La ponderazione per ogni raccomandazione è espressa come percentuale del punteggio totale disponibile per ogni area di interesse. Ad esempio, se una raccomandazione nell'area di interesse relativa a sicurezza e conformità ha un punteggio pari al 5%, l'implementazione della raccomandazione aumenterà del 5% il punteggio complessivo di quell'area.

### Aree di interesse

**Sicurezza e conformità**: proteggere la reputazione dell'organizzazione attivando misure di difesa da minacce e violazioni della sicurezza, applicando i criteri aziendali e rispettando i requisiti di conformità tecnici, legali e normativi.

**Disponibilità e continuità aziendale**: assicurare la disponibilità dei servizi e la redditività dell'azienda garantendo la flessibilità dell'infrastruttura e implementando il corretto livello di protezione aziendale in caso di emergenza

**Prestazioni e scalabilità**: favorire la crescita e l'innovazione dell'organizzazione, garantendo che l'ambiente IT sia in grado di soddisfare gli attuali requisiti di prestazioni e di rispondere rapidamente alle mutevoli esigenze aziendali.

**Aggiornamento, migrazione e distribuzione**: mettere il reparto IT in condizione di agire da promotore principale del cambiamento e dell'innovazione, sfruttando appieno le nuove tecnologie per rendere disponibile maggior valore di business per unità organizzative, risorse e clienti.

**Operazioni e monitoraggio**: ridurre il budget per la manutenzione IT semplificando le operazioni IT e implementando un programma di manutenzione preventiva completo per ottimizzare le prestazioni aziendali.

**Gestione dei cambiamenti e della configurazione**: proteggere le attività operative quotidiane dell'organizzazione e assicurare che i cambiamenti non incidano negativamente sul business, mediante la definizione di procedure di controllo modifiche e il rilevamento e controllo delle configurazioni di sistema.

### È consigliabile mirare a ottenere un punteggio del 100% in tutte le aree di interesse?

Non necessariamente. Le raccomandazioni fornite si basano sulla conoscenza e sull'esperienza acquisite dai tecnici Microsoft attraverso migliaia di visite dei clienti. Non esistono tuttavia due infrastrutture di server uguali e raccomandazioni specifiche possono essere più o meno pertinenti per l'utente. Ad esempio, alcune raccomandazioni sulla sicurezza possono risultare meno pertinenti se le macchine virtuali non sono esposte a Internet. Alcune raccomandazioni sulla disponibilità possono risultare meno pertinenti per i servizi che forniscono creazione di report e raccolta dei dati ad hoc a bassa priorità. I problemi che possono essere importanti per un'azienda collaudata possono esserlo meno per una start-up. È consigliabile identificare quali sono le proprie aree di interesse prioritarie e quindi osservare il cambiamento dei punteggi nel tempo.

Ogni raccomandazione generata include informazioni aggiuntive sui motivi per cui potrebbe essere importante. È consigliabile usare queste informazioni aggiuntive per valutare se l'implementazione della raccomandazione è appropriata, a seconda della natura dei servizi IT e delle esigenze aziendali dell'organizzazione.

## Usare le raccomandazioni relative all'area di interesse della valutazione

Prima di poter usare una soluzione antimalware in Microsoft Azure Operational Insights, è necessario disporre della soluzione installata. Per altre informazioni sull'installazione di soluzioni, vedere [Usare la raccolta di soluzioni per aggiungere o rimuovere soluzioni](operational-insights-setup-workspace.md). Dopo l'installazione, sarà possibile visualizzare il riepilogo delle raccomandazioni usando il riquadro Assessment della pagina Overview in Operational Insights.

È possibile riepilogare le valutazioni relative alla conformità per l'infrastruttura, quindi visualizzare le raccomandazioni nel dettaglio.

![image of SQL Assessment recommendations](./media/operational-insights-assessment/gallery-ad-03.png)



### Per visualizzare le raccomandazioni per un'area di interesse e applicare un'azione correttiva

1. Nella pagina **Overview** fare clic sul riquadro **Assessment** per l'infrastruttura del server.

2. Nella pagina **Assessment** verificare le informazioni di riepilogo in uno dei pannelli dell'area di interesse, quindi fare clic su un'area specifica per visualizzare le raccomandazioni corrispondenti.

3. In una delle pagine relative alle aree di interesse è possibile visualizzare le raccomandazioni relative all'ambiente specifico, classificate in ordine di priorità. Fare clic su una raccomandazione per visualizzare i dettagli relativi al motivo per cui è stata generata e per cui viene visualizzata in **Affected Objects**.

4. Effettuare le azioni correttive suggerite in **Suggested Actions**. Dopo la risoluzione dell'elemento, le valutazioni successive indicheranno che le azioni consigliate sono state effettuate e il punteggio relativo alla conformità aumenterà. Gli elementi corretti vengono visualizzati come **Passed Objects**.

[AZURE.INCLUDE [operational-insights-export](../../includes/operational-insights-export.md)]

<!---HONumber=Sept15_HO3-->