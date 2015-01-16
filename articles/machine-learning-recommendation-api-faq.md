<properties title="FAQ for setting up and using the Machine Learning Recommendations API" pageTitle="Domande frequenti sulla configurazione e sull'uso dell'API Recommendations di Machine Learning | Azure" description="API Recommendations Microsoft create con le domande frequenti su Azure Machine Learning" metaKeywords="" services="machine-learning" solutions="" documentationCenter="" authors="jaymathe" manager="paulettm" editor="cgronlun" videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/14/2014" ms.author="jaymathe" /> 

# Domande frequenti sulla configurazione e sull'uso dell'API Recommendations di Machine Learning
Versione 1.0

Sommario

* Che cos'è RECOMMENDATIONS?	
* Quali funzionalità offre RECOMMENDATIONS?	
* Sono previste limitazioni durante il periodo di lancio?	
* Acquisto e fatturazione	
	* Qual è il costo di RECOMMENDATIONS durante il periodo di lancio?	
	* Sono previsti costi per la registrazione e/o l'archiviazione automatica dell'attività utente tramite RECOMMENDATIONS?	
	* È disponibile una versione di valutazione gratuita per RECOMMENDATIONS?	
	* Quando terminerà il periodo di lancio?	
	* Quanto costerà quando sarà disponibile a livello generale?	
	* Quando verrà emessa la fattura per RECOMMENDATIONS?	
	* Come si esegue l'aggiornamento a servizi di livello superiore?	
	* Quando scade la sottoscrizione a RECOMMENDATIONS?	
	* Come si annulla la sottoscrizione a RECOMMENDATIONS?	
* Introduzione a RECOMMENDATIONS	
	* Per quali utenti è consigliata l'API RECOMMENDATIONS?	
	* Quali sono i prerequisiti per la configurazione di RECOMMENDATIONS?	
	* Come si configura per la prima volta RECOMMENDATIONS?	
	* Dove è disponibile la documentazione sulle API?	
	* Quali sono le opzioni disponibili per il caricamento di dati relativi al catalogo e all'utilizzo in RECOMMENDATIONS?	
* Manutenzione e supporto	
	* Quali sono le dimensioni massime consentite per i set di dati?	
	* Dove si può ottenere il supporto tecnico per RECOMMENDATIONS?	
	* Dove si trovano le condizioni per l'utilizzo?	
* Note legali	


## Che cos'è RECOMMENDATIONS?
RECOMMENDATIONS di Machine Learning di Microsoft Azure è un motore di raccomandazioni self-service in esecuzione in Azure, ideale per le organizzazioni e le attività che si basano sulle raccomandazioni per il cross-selling e l'upselling ai clienti. Si tratta di un'implementazione di Collaborating Filtering che usa Matrix Factorization come algoritmo di base. È accessibile agli sviluppatori di applicazioni come API tramite un'interfaccia HTTP RESTful. RECOMMENDATIONS è attualmente disponibile in anteprima pubblica e sarà presto un servizio disponibile a livello generale.

## Quali funzionalità offre RECOMMENDATIONS?
RECOMMENDATIONS accetta come input un elemento o un insieme di elementi e restituisce un elenco di raccomandazioni rilevanti. Ad esempio: un cliente di un rivenditore online fa cli su un prodotto. Il rivenditore online invia il prodotto specifico come input a RECOMMENDATIONS, ottiene un elenco di prodotti e decide quale prodotto verrà visualizzato al cliente. È consigliabile usare RECOMMENDATIONS per ottimizzare il negozio online o per fornire informazioni al reparto vendite o al call center interno.


## Sono previste limitazioni durante il periodo di lancio?

* Numero massimo di modelli per ogni sottoscrizione: 10
* Numero massimo di elementi che possono essere inclusi nel catalogo: 100.000
* La dimensione massima dei dati che possono essere inviati in POST (ad esempio, importazione dei dati di catalogo o dei dati di utilizzo) è di 200 MB.
* Il numero di transazioni al secondo per una build di un modello di raccomandazioni non attivo è di ~2 TPS. Solo le build di modelli di raccomandazioni attivi possono includere fino a 20 TPS

## Acquisto e fatturazione 
Per altre informazioni sulle sottoscrizioni, vedere qui.

###Qual è il costo di RECOMMENDATIONS durante il periodo di lancio?
RECOMMENDATIONS è un servizio basato su sottoscrizione. Gli addebiti sono basati sul volume di transazioni al mese. Durante il periodo di lancio, il servizio sarà gratuito e sarà previsto un limite di 100.000 transazioni al mese.

###Sono previsti costi per la registrazione e/o l'archiviazione automatica dell'attività utente tramite RECOMMENDATIONS?
Al momento no.

###È disponibile una versione di valutazione gratuita per RECOMMENDATIONS?
Durante il periodo di lancio, il servizio sarà gratuito e sarà previsto un limite di 100.000 transazioni al mese.

###Quando terminerà il periodo di lancio?
Il periodo di lancio e i rispettivi prezzi rimarranno in vigore per alcuni mesi. Gli utenti che hanno effettuato la sottoscrizione riceveranno notifiche relative a eventuali modifiche allo stato e ai prezzi.

###Quanto costerà quando sarà disponibile a livello generale?
I prezzi per la versione di RECOMMENDATIONS disponibile a livello generale non sono stati ancora definiti. I sottoscrittori riceveranno notifiche in caso di modifiche ai prezzi.

###Quando verrà emessa la fattura per RECOMMENDATIONS?
Una sottoscrizione a pagamento è una sottoscrizione per cui sono previste tariffe mensili. Quando si acquista una sottoscrizione a pagamento, si riceve immediatamente l'addebito per il primo mese di utilizzo. Viene addebitato l'importo indicato accanto all'offerta nella pagina di sottoscrizione, con l'aggiunta di eventuali imposte applicabili. L'addebito mensile viene effettuato ogni mese nello stesso giorno di calendario in cui è stato effettuato l'acquisto originale, fino all'annullamento della sottoscrizione. Per altre informazioni sulle sottoscrizioni, fare clic qui.

###Come si esegue l'aggiornamento a servizi di livello superiore?
Durante il periodo di lancio il servizio potrà effettuare al massimo 100.000 transazioni al mese e non potrà essere aggiornato a un livello superiore.

Se, quando RECOMMENDATIONS sarà disponibile a livello generale, la sottoscrizione non offre una quantità sufficiente di transazioni, sarà possibile effettuare l'aggiornamento a una sottoscrizione con un limite di transazioni superiore.

Quando si aggiorna una sottoscrizione:

* Le transazioni rimanenti nella sottoscrizione precedente non verranno aggiunte alla nuova sottoscrizione. 
* Sarà necessario pagare il prezzo completo per la nuova sottoscrizione, anche se nella sottoscrizione precedente sono presenti transazioni non usate.

Processo per l'aggiornamento di una sottoscrizione

* Accedere a Marketplace.
* Fare clic sulla scheda Dati personali.
* Fare clic sul pulsante relativo all'utilizzo per la sottoscrizione da aggiornare. 
* Nel riquadro a destra sono elencate tutte le sottoscrizioni disponibili e la sottoscrizione specifica è visualizzata come inattiva. Fare clic sul pulsante di opzione per la sottoscrizione a cui si vuole eseguire l'aggiornamento.
* Per eseguire l'aggiornamento, fare clic su OK nella finestra di dialogo. Se non si vuole eseguire l'aggiornamento, fare clic su Annulla.

IMPORTANTE: esaminare attentamente la finestra di dialogo prima di eseguire l'aggiornamento, che prevede implicazioni a livello di fatturazione e utilizzo.

Per altre informazioni sulle sottoscrizioni, fare clic [qui](http://msdn.microsoft.com/it-it/library/gg312164.aspx).

###Quando scade la sottoscrizione a RECOMMENDATIONS?
La sottoscrizione avrà termine quando la si annulla. Per annullare le sottoscrizioni, vedere le istruzioni seguenti.

###Come si annulla la sottoscrizione a RECOMMENDATIONS?
Per annullare la sottoscrizione, eseguire la procedura seguente. Se la sottoscrizione attuale è una sottoscrizione a pagamento, la sottoscrizione rimarrà attiva fino al termine del periodo di fatturazione corrente. Se è necessario un annullamento con effetto immediato, contattare Microsoft facendo clic [qui](https://support.microsoft.com/oas/default.aspx?gprid=17024&st=1&wfxredirect=1&sd=gn).

Non sono previsti rimborsi in caso di annullamento prima della fine di un periodo di fatturazione o per transazioni non usate in un periodo di fatturazione.

* Accedere a Marketplace.
* Fare clic sulla scheda Dati personali.
* Individuare la sottoscrizione da annullare.
* Fare clic su Annulla a destra del nome e dello stato del set di dati. È possibile usare questa sottoscrizione fino al termine del periodo di fatturazione corrente o fino al raggiungimento del limite di transazioni, a seconda di quale evento si verifica per primo.

Per annullare immediatamente la sottoscrizione e acquistare una nuova sottoscrizione, fare clic [qui](https://support.microsoft.com/oas/default.aspx?gprid=17024&st=1&wfxredirect=1&sd=gn) per inviare un ticket di supporto.


## Introduzione a RECOMMENDATIONS

###Per quali utenti è consigliata l'API RECOMMENDATIONS? 
RECOMMENDATIONS di Machine Learning di Microsoft Azure è ideale per le organizzazioni e le attività che si basano sulle raccomandazioni per il cross-selling e l'upselling ai clienti. Se si ha un sito Web pubblico, una forza vendite, una forza vendite interna o un call center e se si offre un catalogo che include più di qualche dozzina di prodotti o servizi, l'uso di RECOMMENDATIONS potrebbe offrire molti vantaggi. L'uso di RECOMMENDATIONS è abbastanza semplice. La versione attuale basata su API richiede competenze di base a livello di programmazione. Se è necessario supporto, rivolgersi al fornitore che ha sviluppato il sito Web. Se l'organizzazione include un reparto IT o è presente uno sviluppatore interno, dovrebbero essere in grado di rendere operativo RECOMMENDATIONS. 

###Quali sono i prerequisiti per la configurazione di RECOMMENDATIONS?
RECOMMENDATIONS richiede la disponibilità di un log di scelte utente correlate al catalogo. Se tale log non è disponibile ma si usa un sito Web pubblico, sarà possibile usare RECOMMENDATIONS per raccogliere automaticamente informazioni sulle attività degli utenti. RECOMMENDATIONS richiede anche un catalogo di prodotti o servizi. Se il catalogo non è disponibile, RECOMMENDATIONS potrà usare i dati di utilizzo effettivi dei clienti per creare un catalogo. Un catalogo "implicito" non includerà elementi non "segnalati" come parte delle transazioni degli utenti.

###Come si configura per la prima volta RECOMMENDATIONS?
Dopo la sottoscrizione a RECOMMENDATIONS, è consigliabile usare la documentazione sulle API disponibile  [qui](https://onedrive.live.com/view.aspx?cid=8536718B52F71725&resid=8536718B52F71725!118&app=Word&authkey=!AOLb7aR6D0cVqMQ) per configurare il servizio.

###Dove è disponibile la documentazione sulle API? 
La documentazione sulle API è disponibile [qui](https://onedrive.live.com/view.aspx?cid=8536718B52F71725&resid=8536718B52F71725!118&app=Word&authkey=!AOLb7aR6D0cVqMQ).

###Quali sono le opzioni disponibili per il caricamento di dati relativi al catalogo e all'utilizzo in RECOMMENDATIONS?
Sono disponibili due opzioni per il caricamento dei dati relativi al catalogo e all'utilizzo: è possibile esportare questi dati dal sistema CRM o da altri log e caricarli in RECOMMENDATIONS oppure aggiungere tag al sito Web, in modo da tenere traccia delle attività degli utenti. Nel secondo caso, i dati verranno archiviati in Azure.

##Manutenzione e supporto

###Quali sono le dimensioni massime consentite per i set di dati?
Ogni set di dati può includere al massimo 100.000 elementi del catalogo e fino a 2048 MB di dati di utilizzo.
Una sottoscrizione può anche includere un massimo di 10 set di dati (modelli).

###Dove si può ottenere il supporto tecnico per RECOMMENDATIONS?
Il supporto tecnico è disponibile [qui](https://social.msdn.microsoft.com/forums/azure/en-US/home?forum=MachineLearning).

###Dove si trovano le condizioni per l'utilizzo?

Le condizioni per l'utilizzo sono disponibili [qui](https://datamarket.azure.com/dataset/amla/recommendations#terms).

#Note legali
Questo documento viene fornito "così com'è". Le informazioni e le indicazioni riportate nel presente documento, inclusi URL e altri riferimenti a siti Web Internet, sono soggette a modifica senza preavviso. 
Alcuni esempi usati in questo documento vengono forniti a scopo puramente illustrativo e sono fittizi. Nessuna associazione reale o connessione è intenzionale o può essere desunta. 
Il presente documento non fornisce all'utente alcun diritto legale rispetto a qualsiasi proprietà intellettuale in qualsiasi prodotto Microsoft. È possibile copiare e usare il presente documento per scopi interni e di riferimento. 
(c) 2014 Microsoft. Tutti i diritti sono riservati. 


