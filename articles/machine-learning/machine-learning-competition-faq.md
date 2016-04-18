<properties
	pageTitle="Domande frequenti sui concorsi Cortana Intelligence | Microsoft Azure"
	description="Domande frequenti sui concorsi Microsoft Cortana Intelligence."
	services="machine-learning"
	documentationCenter=""
	authors="hning86"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/11/2016"
	ms.author="haining;chlovel;garye"/>

# Domande frequenti sui concorsi Microsoft Cortana Intelligence

**Informazioni sui concorsi Cortana Intelligence**

Microsoft annuncia i concorsi Cortana Intelligence, che riuniscono una community globale di appassionati per risolvere insieme alcuni dei problemi di analisi scientifica dei dati più complessi del mondo. I concorsi Cortana Intelligence consentono agli appassionati di tutto il mondo di competere e compilare modelli di analisi scientifica dei dati estremamente accurati e intelligenti. I concorsi ospitati si basano su set di dati univoci che vengono messi a disposizione del pubblico per la prima volta. I partecipanti possono vincere premi o il riconoscimento nella classifica pubblica dei migliori 10. Fare clic [qui](http://aka.ms/CIComp) per accedere alla home page dei concorsi.

**Con quale frequenza Microsoft lancerà nuovi concorsi?**

I concorsi Microsoft verranno lanciati a intervalli regolari, ogni 8-12 settimane circa.

**Dove è possibile porre domande generali relative all’analisi scientifica dei dati?**

È possibile consultare il [forum di Microsoft Azure Machine Learning](https://social.msdn.microsoft.com/forums/azure/home?forum=MachineLearning).

**Come si partecipa a una competizione?**

Accedere alla home page dei concorsi di Cortana Intelligence Gallery. La pagina include tutti i concorsi attualmente in corso. Nella pagina di iscrizione di ogni concorso sono riportate istruzioni dettagliate, le modalità di partecipazione, i premi e la durata. Fare clic [qui](http://aka.ms/CIComp) per accedere alla home page dei concorsi.

1. Trovare il concorso a cui si vuole partecipare in Cortana Intelligence Gallery, leggere tutte le istruzioni e guardare il video di esercitazione, quindi fare clic sul pulsante "Enter Competition" per copiare l'esperimento iniziale nell'area di lavoro di Azure Machine Learning esistente. Creare prima di tutto un accesso a un'area di lavoro, se non è già disponibile. Eseguire l'esperimento iniziale, osservare le metriche delle prestazioni e usare la propria creatività per migliorare le prestazioni del modello. È probabile che la maggior parte del tempo verrà dedicata a questo passaggio.   

2. Creare un esperimento predittivo con il modello con training a partire dall'esperimento iniziale. Modificare con attenzione gli schemi di input e di output del servizio Web per fare in modo che siano conformi ai requisiti specificati nella documentazione del concorso. Il documento dell'esercitazione contiene in genere istruzioni dettagliate su come fare. Se disponibile, è anche possibile guardare il video dell'esercitazione.

3. Distribuire un servizio Web a partire dall'esperimento predittivo. Testare il servizio Web usando il pulsante Test o il modello di Excel creato automaticamente per verificarne il funzionamento.

4. Inviare il servizio Web come contributo al concorso. Il punteggio pubblico sarà disponibile nella pagina del concorso della raccolta Cortana Intelligence. I migliori potranno entrare nella classifica ufficiale. Dopo aver inviato il contributo al concorso è possibile tornare all'esperimento iniziale copiato, eseguire l'iterazione, aggiornare l'esperimento predittivo, aggiornare il servizio Web e inviare un nuovo contributo.

**È possibile usare strumenti open source per partecipare a questi concorsi?**

I partecipanti al concorso usano Azure Machine Learning Studio, un servizio basato sul cloud all'interno della suite Cortana Intelligence, per sviluppare i modelli di analisi scientifica dei dati e creare i contributi con cui concorrere. ML Studio fornisce un'interfaccia utente grafica per la costruzione di esperimenti di apprendimento automatico e permette anche di usare i propri script R e/o Python per l'esecuzione nativa. Il runtime di Python e R in ML Studio include un set completo di pacchetti R o Python open source e permette anche di importare pacchetti personalizzati come parte dell'esperimento. ML Studio include un servizio Notebook di Jupyter incorporato per l'esplorazione libera dei dati. Naturalmente, è sempre possibile scaricare i set di dati usati nel concorso ed esplorarli con lo strumento preferito all'esterno di ML Studio.

**La competizione è riservata ai data scientist?**

No. Sono invitati a partecipare gli appassionati di analisi scientifica dei dati, gli aspiranti data scientist e chiunque sia interessato alla materia. Sono stati preparati documenti di supporto per consentire a chiunque di partecipare. I destinatari sono:

* Sviluppatori di dati, data scientist e professionisti di analisi e business intelligence: sono responsabili della produzione di dati e contenuti di analisi utilizzati da altri utenti.

* Amministratori dei dati: dispongono di informazioni relative ai dati, al significato dei dati, all'uso previsto e allo scopo.

* Studenti e ricercatori: studiano e acquisiscono competenze relative ai dati attraverso programmi universitari oppure partecipano a corsi aperti online su larga scala (MOOC).


**È possibile iscriversi con i colleghi come team?**

Attualmente la piattaforma del concorso non supporta la partecipazione come team. Ogni contributo è legato a una singola identità utente.

**È necessario pagare per partecipare a un concorso?**

La partecipazione ai concorsi è gratuita. Tuttavia, per partecipare è necessario avere accesso a un'area di lavoro di Azure Machine Learning. Per creare un'area di lavoro gratuita senza una carta di credito è sufficiente eseguire l'accesso con un account Microsoft valido o un account Office 365. I clienti di Azure o di Cortana Intelligence Suite possono creare e usare un'area di lavoro standard nella stessa sottoscrizione di Azure. Per acquistare una sottoscrizione di Azure, è possibile visitare [questa pagina](https://azure.microsoft.com/pricing). All'uso di un'area di lavoro standard per la costruzione degli esperimenti saranno applicate tariffe standard. Per informazioni sui prezzi di Azure Machine Learning, visitare [questa pagina](https://azure.microsoft.com/pricing/details/machine-learning/).

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

**Cosa sono i punteggi pubblici e privati?**

Nella maggior parte dei concorsi si riceve un punteggio pubblico ogni volta che si invia un contributo, in genere entro 10-20 minuti. Al termine del concorso viene invece inviato un punteggio privato che viene usato per la classificazione finale. Ecco cosa accade:

* L'intero set di dati usato per il concorso viene suddiviso in modo casuale con una stratificazione in dati di training e il resto in dati di test. La suddivisione casuale è stratificata per garantire la coerenza delle distribuzioni di etichette dei dati di training e di test.
 
* I dati di training vengono caricati e forniti come parte dell'esperimento iniziale nella configurazione del modulo Reader.

* I dati di test vengono suddivisi ulteriormente in dati di test pubblici e privati, usando la stessa stratificazione.

* I dati di test pubblici vengono usati per il round iniziale di assegnazione dei punteggi. Il risultato è il punteggio pubblico, che viene visualizzato nella cronologia degli invii in seguito all'invio del contributo. Questo punteggio viene calcolato per ogni contributo inviato. Questo punteggio pubblico viene usato per il posizionamento del concorrente nella classifica pubblica.

* I dati di test privati vengono usati per il round finale di assegnazione dei punteggi dopo la conclusione del concorso. Questo è il punteggio privato.

* Per ogni partecipante viene selezionato automaticamente un numero fisso di contributi, tra quelli con i punteggi pubblici più alti, per il round di assegnazione del punteggio privato. Il numero fisso può variare a seconda del concorso. Il contributo con il punteggio privato più alto viene quindi selezionato per la classificazione finale, che determina i vincitori.

**I clienti possono ospitare un concorso sulla piattaforma?**

Le organizzazioni di terze parti possono ospitare concorsi sia pubblici che privati sulla piattaforma, in qualità di partner. Il team dedicato all'accoglienza al concorso si occuperà di illustrare il processo di partecipazione per tali concorsi. Per altre informazioni è possibile scrivere a [compsupport@microsoft.com](mailto:compsupport@microsoft.com).

**Quali limitazioni ci sono per l'invio di contributi?**

In un concorso tipo potrebbe essere imposto un limite al numero di contributi che è possibile inviare un periodo di 24 ore, dalle 00.00.00 alle 23.59.59 UTC, e al numero totale di contributi che è possibile inviare per tutta la durata del concorso. Quando viene superato un limite si riceve il relativo messaggio di errore.

**Cosa accade se si vince un concorso?**

Microsoft verificherà i risultati della classifica privata e contatterà i vincitori. Assicurarsi che l'indirizzo di posta elettronica del profilo utente sia aggiornato.

**Come viene consegnato ai vincitori il denaro del premio?**

I vincitori della competizione devono firmare una dichiarazione di idoneità, autorizzazione e liberatoria. Questo modulo riporta le regole della competizione. I vincitori devono compilare il modulo fiscale W-9 se sono contribuenti degli Stati Uniti o il modulo W-8BEN se non sono contribuenti degli Stati Uniti. Nell'ambito del processo di pagamento dei premi tutti i vincitori verranno contattati all'indirizzo di posta elettronica usato per la registrazione. Per altre informazioni, vedere le [condizioni](http://aka.ms/comptermsandconditions) dei concorsi.

**Cosa accade se più contributi ricevono lo stesso punteggio?**

Per lo spareggio viene presa in considerazione l'ora di invio. I contributi inviati prima hanno la precedenza.

**È possibile partecipare usando l'area di lavoro guest?**

No. Per partecipare, è necessario usare un'area di lavoro standard o gratuita. In un'area di lavoro guest si può aprire l'esperimento iniziale del concorso, ma non è possibile creare un contributo valido per la partecipazione.

**È possibile partecipare usando un'area di lavoro in qualsiasi area di Azure?**

Attualmente la piattaforma del concorso supporta l'invio di contributi soltanto da aree di lavoro nell'area di Azure degli Stati Uniti centro-meridionali. Tutte le aree di lavoro gratuite si trovano negli Stati Uniti centro-meridionali. Se si sceglie di usare un'area di lavoro standard, assicurarsi che si trovi nell'area di Azure degli Stati Uniti centro-meridionali. In caso contrario l'invio avrà esito negativo.

**Le soluzioni e i contributi ai concorsi degli utenti vengono conservati?**

I contributi degli utenti vengono conservati unicamente a scopo di valutazione, per identificare le soluzioni vincenti. Per informazioni specifiche, vedere le [condizioni](http://aka.ms/comptermsandconditions) dei concorsi.

<!---HONumber=AcomDC_0406_2016-->