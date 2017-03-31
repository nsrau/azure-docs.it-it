---
title: Prezzi e fatturazione del Bus di servizio | Documentazione Microsoft
description: Panoramica della struttura dei prezzi del Bus di servizio.
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 7c45b112-e911-45ab-9203-a2e5abccd6e0
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/16/2017
ms.author: sethm
translationtype: Human Translation
ms.sourcegitcommit: 182e28e37eb56c547e28524f2a3e13f042238cb4
ms.openlocfilehash: bd042908fec2dcf499dd1cb5230f62ec4be9fdea


---
# <a name="service-bus-pricing-and-billing"></a>Informazioni sul prezzo e la fatturazione del Bus di servizio
Il bus di servizio è disponibile nei livelli Basic, Standard e [Premium](service-bus-premium-messaging.md). È possibile scegliere un livello di servizio per ogni spazio dei nomi del bus di servizio creato e questa selezione del livello si applica a tutte le entità create all'interno dello spazio dei nomi.

> [!NOTE]
> Per informazioni dettagliate sui prezzi correnti del bus di servizio, vedere la [pagina dei prezzi del bus di servizio di Azure](https://azure.microsoft.com/pricing/details/service-bus/) e [Domande frequenti sul bus di servizio](service-bus-faq.md#pricing).
>
>

I bus di servizio usano i due misuratori seguenti per le code e gli argomenti/sottoscrizioni:

1. **Operazioni di messaggistica**: definite come chiamate API sugli endpoint di servizio di code o argomenti/sottoscrizioni. Questo misuratore sostituirà i messaggi inviati o ricevuti come unità primaria dell'utilizzo fatturabile per code e argomenti/sottoscrizioni.
2. **Connessioni negoziate**: definite come numero massimo di connessioni permanenti aperte su code, argomenti o sottoscrizioni durante un periodo di campionamento di un'ora. Questo misuratore verrà applicato solo nel livello Standard, in cui è possibile aprire connessioni aggiuntive (prima le connessioni erano limitate a 100 per ogni coda, argomento o sottoscrizione) per una tariffa nominale per ogni connessione.

Il livello **Standard** introduce prezzi progressivi per le operazioni eseguite con code e argomenti/sottoscrizioni, che generano sconti basati sul volume che arrivano fino all'80% nei livelli di utilizzo più elevati. È inoltre disponibile un addebito di base al livello Standard di 10 USD al mese, che consente di eseguire operazioni fino a 12,5 milioni di dollari al mese senza alcun costo aggiuntivo.

Il livello **Premium** fornisce l'isolamento delle risorse a livello di CPU e memoria in modo che ogni carico di lavoro del cliente venga eseguito in isolamento. Questo contenitore di risorse viene chiamato *unità di messaggistica*. Ad ogni spazio dei nomi Premium viene allocata almeno un'unità di messaggistica. È possibile acquistare 1, 2 o 4 unità di messaggistica per ogni spazio dei nomi Premium del bus di servizio. Un singolo carico di lavoro o un'entità può estendersi su più unità di messaggistica e il numero di unità di messaggistica può essere modificato in base alle esigenze, anche se la fatturazione prevede un addebito a frequenza giornaliera o a 24 ore. Ne risultano prestazioni prevedibili e ripetibili per la soluzione basata sul bus di servizio. Non solo le prestazioni sono più prevedibili e disponibili, ma anche più veloci. La messaggistica Premium del bus di servizio di Azure crea il motore di archiviazione introdotto in Hub eventi di Azure. Con la messaggistica Premium, le prestazioni massime sono più rapide rispetto al livello Standard.

Si noti che il costo di base standard viene addebitato una sola volta al mese per ogni sottoscrizione di Azure. Ciò significa che, dopo aver creato un unico spazio dei nomi del bus di servizio nei livelli Standard o Premium, sarà possibile creare tutti gli spazi dei nomi di livello Standard o Premium aggiuntivi desiderati nella stessa sottoscrizione di Azure, senza incorrere in costi aggiuntivi di base.

Tutti gli spazi dei nomi del Bus di servizio esistenti creati prima del 1 novembre 2014 sono stati automaticamente inseriti nel livello Standard. In questo modo ci si assicura di continuare ad avere accesso a tutte le funzionalità attualmente disponibili con il Bus di servizio. Successivamente è possibile usare il [portale di Azure classico][Azure classic portal] per effettuare il downgrade al livello di base se lo si desidera.

Nella tabella seguente vengono riepilogate le differenze funzionali tra i livelli di base e quelli Standard/Premium.

| Funzionalità | Basic | Standard/Premium |
| --- | --- | --- |
| Code |Sì |Sì |
| Messaggi pianificati |Sì |Sì |
| Argomenti/Sottoscrizioni |No |Sì |
| Inoltri |No |Sì |
| Transazioni |No |Sì |
| Deduplicazione |No |Sì |
| Sessioni |No |Sì |
| Messaggi di grandi dimensioni |No |Sì |
| ForwardTo |No |Sì |
| SendVia |No |Sì |
| Connessioni negoziate (incluse) |100 per ogni spazio dei nomi del bus di servizio |1.000 per ogni sottoscrizione di Azure |
| Connessioni negoziate (importo consentito) |No |Sì (fatturabile) |

## <a name="messaging-operations"></a>Operazioni di messaggistica
Nell'ambito del nuovo modello di determinazione prezzi, la fatturazione per code e argomenti/sottoscrizioni sta cambiando. Queste entità sono in transizione dalla fatturazione per ogni messaggio alla fatturazione per ogni operazione. Per "operazione" si intende qualsiasi chiamata API effettuata da un endpoint di servizio di una coda o di un argomento o sottoscrizione. Sono incluse operazioni di stato della sessione, di invio/ricezione e di gestione.

| Tipo di operazione | Descrizione |
| --- | --- |
| gestione |Creazione, lettura, aggiornamento, eliminazione su code o argomenti/sottoscrizioni. |
| Messaggistica |Invio e ricezione di messaggi con code o argomenti/sottoscrizioni. |
| Stato sessione |Acquisizione o impostazione dello stato della sessione su una coda o un argomento o sottoscrizione. |

I prezzi di seguito sono validi a partire dall’1 novembre 2014:

| Basic | Costi |
| --- | --- |
| Operazioni |0,05 per milioni di operazioni |

| Standard | Costi |
| --- | --- |
| Costo base |$10/mese |
| All’inizio 12,5 milioni di operazioni/mese |Incluso |
| 12,5-100 milioni di operazioni/mese |$0,80 per milioni di operazioni |
| 100 milioni-2.500 milioni di operazioni/mese |$0,50 per ogni milione di operazioni |
| Oltre 2.500 milioni di operazioni/mese |$0,20 per ogni milione di operazioni |

| Premium | Costi |
| --- | --- |
| Giornaliera |$11.13 tariffa fissa per Unità di messaggio |

## <a name="brokered-connections"></a>Connessioni negoziate
Le *connessioni negoziate* sono ideali per i modelli d'uso dei clienti che prevedono un numero elevato di mittenti/destinatari "connessi in modo permanente" a code, argomenti o sottoscrizioni. I mittenti/destinatari costantemente connessi sono quelli che si connettono tramite AMQP o HTTP con un timeout di ricezione diverso da zero, ad esempio, HTTP (tempo di polling). Non generano connessioni negoziate HTTP mittenti e destinatari con un timeout immediato.

Prima le code e gli argomenti/sottoscrizioni avevano un limite previsto di 100 connessioni simultanee per ogni URL. Lo schema di fatturazione corrente rimuove il limite per ogni URL per le code e gli argomenti/sottoscrizioni e implementa quote e misurazioni nelle connessioni negoziate ai livelli di sottoscrizione di Azure e di spazio dei nomi del bus di servizio.

Il livello di base include ed è rigorosamente limitato a 100 connessioni negoziate per ogni spazio dei nomi del Bus di servizio. Le connessioni che superano questo numero verranno rifiutate nel livello di base. Il livello Standard rimuove il limite per ogni spazio dei nomi e calcola l'utilizzo delle connessioni negoziate aggregate nella sottoscrizione di Azure. Nel livello Standard saranno consentite 1.000 connessioni negoziate per ogni sottoscrizione di Azure senza costi (oltre ai costi di base) aggiuntivi. L'uso di più di 1.000 connessioni negoziate totali negli spazi dei nomi del bus di servizio di livello Standard in una sottoscrizione di Azure verrà fatturato in base a una pianificazione progressiva, come illustrato nella tabella seguente.

| Connessioni negoziate (livello Standard) | Costi |
| --- | --- |
| All’inizio 1.000/mese |Incluso con costo base |
| 1.000-100.000/mese |$0,03 per ogni connessione/mese |
| 100.000-500.000/mese |$0,025 per ogni connessione/mese |
| Oltre 500.000/mese |$0.015 per ogni connessione/mese |

> [!NOTE]
> Con il livello di messaggistica Standard (tramite l'addebito di base) sono incluse&1;.000 connessioni negoziate, che possono essere condivise tra tutte le code, gli argomenti e le sottoscrizioni all'interno della sottoscrizione di Azure associata.
>
>

<br />

> [!NOTE]
> La fatturazione si basa sul numero massimo di connessioni simultanee e viene ripartita per ogni ora in base a 744 ore al mese.
>
>

| Livello Premium |
| --- |
| Le connessioni negoziate non vengono addebitate nel livello Premium. |

Per altre informazioni sulle connessioni negoziate, vedere la sezione [Domande frequenti](#faq) più avanti in questo argomento.

## <a name="wcf-relay"></a>Inoltro WCF
Gli inoltri WFC sono disponibili solo negli spazi dei nomi di livello Standard. In caso contrario, le quote di prezzi e la connessione per gli inoltri restano invariate. Ciò significa che gli inoltri continueranno a essere addebitati sul numero di messaggi (non operazioni) e sulle ore di inoltro.

| Prezzi degli inoltri WCF | Costi |
| --- | --- |
| Ore di inoltro WCF |$0,10 per ogni 100 ore di inoltro |
| Messaggi |$0,01 per ogni 10.000 messaggi |

## <a name="faq"></a>Domande frequenti
### <a name="how-is-the-wcf-relay-hours-meter-calculated"></a>Come vengono calcolate le unità di ore di inoltro WCF?
Vedere [questo argomento](service-bus-faq.md).

### <a name="what-are-brokered-connections-and-how-do-i-get-charged-for-them"></a>Quali sono le connessioni negoziate e come viene effettuato l'addebito?
Una connessione negoziata è definita in uno dei seguenti modi:

1. Connessione AMQP da un client a un argomento/sottoscrizione o una coda del bus di servizio.
2. Una chiamata HTTP per ricevere un messaggio da una coda o un argomento del bus di servizio con un valore di timeout della ricezione maggiore di zero.

I costi del Bus di servizio per il numero massimo di connessioni simultanee negoziate che superano la quantità inclusa (1.000 nel livello Standard). I picchi vengono misurati su base oraria, ripartiti dividendo per 744 ore in un mese e sommati nel periodo di fatturazione mensile. La quantità inclusa (1.000 connessioni negoziate al mese) viene applicata alla fine del periodo di fatturazione con la somma dei picchi orari ripartiti.

Ad esempio:

1. Ognuno dei 10.000 dispositivi si connette tramite una singola connessione AMQP e riceve i comandi da un argomento del bus di servizio. I dispositivi inviano eventi di telemetria per un Hub di eventi. Se tutti i dispositivi si connettono per 12 ore al giorno, si applicano i costi di connessione seguenti (oltre a eventuali altri costi per l'argomento del bus di servizio): 10.000 connessioni * 12 ore * 31 giorni/744 = 5.000 connessioni negoziate. Dopo l’indennità mensile di 1.000 connessioni negoziate, ci sarà l'addebito per 4.000 connessioni negoziate, alla tariffa di $0,03 per ogni connessione negoziata, per un totale di $120.
2. 10.000 dispositivi ricevono messaggi da una coda del Bus di servizio tramite HTTP, specificando un timeout diverso da zero. Se tutti i dispositivi si connettono per 12 ore al giorno, si vedranno applicati i costi di connessione seguenti (oltre a eventuali altri costi per l'argomento del bus di servizio): 10.000 connessioni di ricezione HTTP * 12 ore al giorno * 31 giorni/744 = 5.000 connessioni negoziate.

### <a name="do-brokered-connection-charges-apply-to-queues-and-topicssubscriptions"></a>I costi delle connessioni negoziate si applicano a code e argomenti/sottoscrizioni?
Sì. Non sono previsti costi di connessione per l'invio di eventi tramite HTTP, indipendentemente dal numero di sistemi o dispositivi di invio. La ricezione di eventi con HTTP utilizzando un timeout maggiore di zero, talvolta denominato "polling prolungato" genera costi di connessione negoziata. Le connessioni AMQP generano costi di connessione negoziata indipendentemente dal fatto che le connessioni vengano utilizzate per inviare o ricevere. Si noti che sono consentite 100 connessioni negoziate gratuitamente in uno spazio dei nomi Basic. Questo è il numero massimo di connessioni negoziate consentite per la sottoscrizione di Azure. Le prime 1.000 connessioni negoziate tra tutti gli spazi dei nomi Standard in una sottoscrizione di Azure sono incluse senza alcun costo aggiuntivo (oltre ai costi di base). Poiché queste quote sono sufficienti per coprire molti scenari di messaggistica service to service, i costi della connessione negoziata sono in genere rilevanti solo se si prevede di utilizzare il polling prolungato di AMQP o HTTP con un numero elevato di client; ad esempio, per ottenere un flusso di eventi più efficiente o abilitare la comunicazione bidirezionale con molti dispositivi o istanze di applicazione.

## <a name="next-steps"></a>Passaggi successivi
* Per altre informazioni sui prezzi del bus di servizio, vedere la [pagina dei prezzi del bus di servizio](https://azure.microsoft.com/pricing/details/service-bus/).
* Per le risposte alle domande frequenti sui prezzi e sulla fatturazione del bus di servizio, vedere [Domande frequenti sul bus di servizio](service-bus-faq.md#pricing).

[Azure classic portal]: http://manage.windowsazure.com



<!--HONumber=Feb17_HO2-->


