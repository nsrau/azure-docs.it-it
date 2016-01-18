<properties 
   pageTitle="Prezzi e fatturazione del Bus di servizio | Microsoft Azure"
   description="Panoramica della struttura dei prezzi del Bus di servizio."
   services="service-bus"
   documentationCenter="na"
   authors="sethmanheim"
   manager="timlt"
   editor="tysonn" />
<tags 
   ms.service="service-bus"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="12/28/2015"
   ms.author="sethm" />

# Informazioni sul prezzo e la fatturazione del Bus di servizio

Il Bus di servizio è disponibile nei livelli Basic, Standard, e [Premium](service-bus-premium-messaging.md). È possibile scegliere un livello di servizio per ogni spazio dei nomi del Bus di servizio che si crea e si applica questa selezione del livello in tutte le code, argomenti/sottoscrizioni, inoltri e hub di eventi creati all'interno di tale spazio dei nomi.

>[AZURE.NOTE]Per informazioni dettagliate sui prezzi correnti del bus di servizio, vedere [Domande frequenti sui prezzi del bus di servizio](service-bus-pricing-faq.md).

I Bus di servizio utilizzano i due controlli seguenti per le code e gli argomenti/sottoscrizioni:

1. **Attività di messaggistica**: definite come chiamate di API in coda o argomento o sottoscrizione endpoint del servizio. Questa misurazione sostituirà i messaggi inviati o ricevuti come unità primaria dell'utilizzo fatturabile per le code e argomenti/sottoscrizioni.

2. **Connessioni negoziate**: definite come il numero massimo di connessioni permanenti aperte su code, argomenti/sottoscrizioni o gli hub di eventi durante un periodo di campionamento specificato di un'ora. Questa misurazione verrà applicata solo nel livello Standard, in cui è possibile aprire connessioni aggiuntive (in precedenza, le connessioni sono limitate a 100 per ogni coda, argomento o sottoscrizione) di una tariffa nominale per ogni connessione.

Il livello **Standard** introduce prezzi progressivi per le operazioni eseguite con le code e argomenti/sottoscrizioni, che producono sconti basati sul volume che vanno fino all'80% in più negli elevati livelli di utilizzo. È inoltre disponibile un addebito di base al livello Standard di 10 USD al mese, che consente di eseguire operazioni fino a 12,5 milioni di dollari al mese senza alcun costo aggiuntivo.

Il livello **Premium** fornisce l'isolamento delle risorse a livello di CPU e memoria in modo che ogni carico di lavoro del cliente venga eseguito in isolamento. Questo contenitore di risorse viene chiamato *unità di messaggistica*. Ad ogni spazio dei nomi Premium viene allocata almeno un'unità di messaggistica. È possibile acquistare 1, 2 o 4 unità di messaggistica per ogni spazio dei nomi Premium del bus di servizio. Un singolo carico di lavoro o un'entità può estendersi su più unità di messaggistica e il numero di unità di messaggistica può essere modificato in base alle esigenze, anche se la fatturazione prevede un addebito a frequenza giornaliera o a 24 ore. Ne risultano prestazioni prevedibili e ripetibili per la soluzione basata sul bus di servizio. Non solo le prestazioni sono più prevedibili e disponibili, ma anche più veloci. La messaggistica Premium del bus di servizio di Azure crea il motore di archiviazione introdotto in Hub eventi di Azure. Con la messaggistica Premium, le prestazioni massime sono più rapide rispetto al livello Standard.

Si noti che il costo base standard viene addebitato una sola volta al mese per ogni sottoscrizione di Azure. Ciò significa che dopo aver creato un unico spazio dei nomi del Bus di servizio nei livelli Standard o Premium, si potrà creare tanti nomi di spazio di livello Standard o Premium aggiuntivi quanti se ne desiderano nella stessa sottoscrizione Azure, senza incorrere in costi aggiuntivi di base.

Tutti gli spazi dei nomi del Bus di servizio esistenti creati prima del 1 novembre 2014 sono stati automaticamente inseriti nel livello Standard. In questo modo ci si assicura di continuare ad avere accesso a tutte le funzionalità attualmente disponibili con il Bus di servizio. Successivamente, è possibile utilizzare il [portale di Azure classico][] per effettuare il downgrade al livello di base se lo si desidera.

Nella tabella seguente vengono riepilogate le differenze funzionali tra i livelli di base e quelli Standard/Premium.

|Funzionalità|Basic|Standard/Premium|
|---|---|---|
|Hub eventi|Sì|Sì|
|Code|Sì|Sì|
|Messaggi pianificati|Sì|Sì|
|Argomenti/sottoscrizioni|No|Sì|
|Inoltri|No|Sì|
|Transazioni|No|Sì|
|Deduplicazione|No|Sì|
|Sessioni|No|Sì|
|Messaggi di grandi dimensioni|No|Sì|
|ForwardTo|No|Sì|
|SendVia|No|Sì|
|Connessioni negoziate (incluse)|100 per ogni spazio dei nomi del bus di servizio|1\.000 per ogni sottoscrizione di Azure|
|Connessioni negoziate (importo consentito)|No|Sì (fatturabile)|

## Operazioni di messaggistica

Come parte del nuovo modello di determinazione dei prezzi, la fatturazione per le code e argomenti/sottoscrizioni sta cambiando. Queste entità sono in transizione dalla fatturazione per ogni messaggio alla fatturazione per ogni operazione. Un’"operazione" si riferisce a qualsiasi chiamata API effettuata da un endpoint del servizio coda o argomento o sottoscrizione. Sono incluse operazioni di stato della sessione, di invio/ricezione e di gestione.

|Tipo di operazione|Descrizione|
|---|---|
|gestione|Creazione, lettura, aggiornamento, eliminazione (CRUD) su code o argomenti/sottoscrizioni.|
|Messaggistica|L'invio e la ricezione di messaggi con code o argomenti/sottoscrizioni.|
|Stato sessione|Ottenere o impostare lo stato della sessione su una coda o argomento o sottoscrizione.|

I prezzi di seguito sono validi a partire dall’1 novembre 2014:

|Basic|Costi|
|---|---|
|Operazioni|0,05 per milioni di operazioni|

|Standard|Costi|
|---|---|
|Costo base|$10/mese|
|All’inizio 12,5 milioni di operazioni/mese|Incluso|
|12,5-100 milioni di operazioni/mese|$0,80 per milioni di operazioni|
|100 milioni-2.500 milioni di operazioni/mese|$0,50 per ogni milione di operazioni|
|Oltre 2.500 milioni di operazioni/mese|$0,20 per ogni milione di operazioni|

>[AZURE.NOTE]Il livello Premium è attualmente in anteprima e il prezzo seguente riflette uno sconto del 50% in anteprima.

|Premium|Costi|
|---|---|
|Giornaliera|$11.13 tariffa fissa per Unità di messaggio|

## Connessioni negoziate

*Connessioni negoziate* adatta i modelli di utilizzo cliente che coinvolgono un numero elevato di mittenti/destinatari "connessi in modo permanente" relativamente a code, argomenti/sottoscrizioni o hub eventi. I mittenti/destinatari costantemente connessi sono quelli che si connettono tramite AMQP o HTTP con un timeout di ricezione diverso da zero, ad esempio, HTTP (tempo di polling). Non generano connessioni negoziate HTTP mittenti e destinatari con un timeout immediato.

In precedenza, le code e gli argomenti/sottoscrizioni avevano un limite previsto di 100 connessioni simultanee per ciascun URL. Lo schema di fatturazione corrente rimuove il limite per ogni URL per le code e gli argomenti/sottoscrizioni e implementa quote e misurazione sulle connessioni negoziate ai livelli di sottoscrizione di Azure dello spazio dei nomi del Bus di servizio.

Il livello di base include ed è rigorosamente limitato a 100 connessioni negoziate per ogni spazio dei nomi del Bus di servizio. Le connessioni che superano questo numero verranno rifiutate nel livello di base. Il livello Standard rimuove il limite per ogni spazio dei nomi e calcola l'utilizzo di connessione negoziata di aggregazione nella sottoscrizione di Azure. Nel livello Standard, saranno consentite 1.000 connessioni negoziate per ogni sottoscrizione di Azure senza costi (oltre i costi di base) aggiuntivi. Verranno fatturate più di un totale di 1.000 connessioni negoziate attraverso gli spazi dei nomi del Bus di servizio di livello Standard in una sottoscrizione di Azure su una pianificazione progressiva, come illustrato nella tabella seguente.

|Connessioni negoziate (livello Standard)|Costi|
|---|---|
|All’inizio 1.000/mese|Incluso con costo base|
|1\.000-100.000/mese|$0,03 per ogni connessione/mese|
|100\.000-500.000/mese|$0,025 per ogni connessione/mese|
|Oltre 500.000/mese|$0.015 per ogni connessione/mese|

>[AZURE.NOTE]1.000 connessioni negoziate sono incluse con il livello di messaggistica Standard (tramite l'addebito di base) e possono essere condivise tra tutte le code, gli argomenti e le sottoscrizioni e gli hub di eventi all'interno della sottoscrizione Azure associata.

>[AZURE.NOTE]La fatturazione si basa sul numero massimo di connessioni simultanee e viene ripartita per ogni ora in base a 744 ore al mese.

|Livello Premium
|---|
|Le connessioni negoziate non vengono addebitate nel livello Premium.|

Per ulteriori informazioni sulle connessioni negoziate, vedere la sezione [domande frequenti](#FAQ) più avanti in questo argomento.

## Relay

Gli inoltri sono disponibili solo negli spazi dei nomi di livello Standard. In caso contrario, le quote di prezzi e la connessione per gli inoltri restano invariate. Ciò significa che gli inoltri continueranno a essere addebitati sul numero di messaggi (non operazioni) e sulle ore di inoltro.

|Prezzi di inoltro|Costi|
|---|---|
|Ore di inoltro|$0,10 per ogni 100 ore di inoltro|
|Messaggi|$0,01 per ogni 10.000 messaggi|

## Domande frequenti

### Come vengono calcolate le unità di ore di inoltro?

Vedere [questo argomento](service-bus-pricing-faq.md#How-is-the-Relay-Hours-meter-calculated?).

### Quali sono le connessioni negoziate e come viene effettuato l'addebito?

Una connessione negoziata è definita in uno dei seguenti modi:

1. Una connessione AMQP da un client a un argomento o sottoscrizione del Bus di servizio, di accodamento o Hub di eventi.

2. Una chiamata HTTP per ricevere un messaggio da una coda con un valore di timeout di ricezione maggiore di zero o un argomento del Bus di servizio.

I costi del Bus di servizio per il numero massimo di connessioni simultanee negoziate che superano la quantità inclusa (1.000 nel livello Standard). I picchi vengono misurati su base oraria, ripartiti dividendo per 744 ore in un mese e sommati nel periodo di fatturazione mensile. La quantità inclusa (1.000 connessioni negoziate al mese) viene applicata alla fine del periodo di fatturazione con la somma dei picchi orari ripartiti.

Ad esempio:

1. Ognuno dei 10.000 dispositivi si connettono tramite una singola connessione AMQP e ricevono i comandi da un argomento del Bus di servizio. I dispositivi inviano eventi di telemetria per un Hub di eventi. Se tutti i dispositivi di connettono per 12 ore al giorno, si applicano i costi di connessione seguenti (oltre a eventuali altri costi per l’argomento del Bus di Servizio): 10.000 connessioni * 12 ore * 31 giorni / 744 = 5.000 connessioni negoziate. Dopo l’indennità mensile di 1.000 connessioni negoziate, ci sarà l'addebito per 4.000 connessioni negoziate, alla tariffa di $0,03 per ogni connessione negoziata, per un totale di $120.

2. 10\.000 dispositivi ricevono messaggi da una coda del Bus di servizio tramite HTTP, specificando un timeout diverso da zero. Se tutti i dispositivi si connettono per 12 ore al giorno, si vedranno applicati i costi di connessione seguenti (oltre a eventuali altri costi per l’argomento del Bus di Servizio): 10.000 connessioni di ricezione HTTP* 12 ore * 31 giorni / 744 = 5.000 connessioni negoziate.

### I costi della connessione negoziata si applicano a code e argomenti/sottoscrizioni?

Sì. Non sono previsti costi di connessione per l'invio di eventi tramite HTTP, indipendentemente dal numero di sistemi o dispositivi di invio. La ricezione di eventi con HTTP utilizzando un timeout maggiore di zero, talvolta denominato "polling prolungato" genera costi di connessione negoziata. Le connessioni AMQP generano costi di connessione negoziata indipendentemente dal fatto che le connessioni vengano utilizzate per inviare o ricevere. Si noti che sono consentite 100 connessioni negoziate gratuitamente in uno spazio dei nomi Basic. Questo è il numero massimo di connessioni negoziate consentite per la sottoscrizione di Azure. Le prime 1.000 connessioni negoziate tra tutti gli spazi dei nomi Standard in una sottoscrizione di Azure sono incluse senza alcun costo aggiuntivo (oltre i costi di base). Poiché queste quote sono sufficienti per coprire molti scenari di messaggistica service to service, i costi della connessione negoziata sono in genere rilevanti solo se si prevede di utilizzare il polling prolungato di AMQP o HTTP con un numero elevato di client; ad esempio, per ottenere un flusso di eventi più efficiente o abilitare la comunicazione bidirezionale con molti dispositivi o istanze di applicazione.

## Passaggi successivi

Per altre informazioni sui prezzi del bus di servizio, vedere [Domande frequenti sui prezzi del bus di servizio](service-bus-pricing-faq.md).

[portale di Azure classico]: http://manage.windowsazure.com

<!---HONumber=AcomDC_0107_2016-->