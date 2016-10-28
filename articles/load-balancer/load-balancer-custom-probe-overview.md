<properties
  pageTitle="Probe personalizzati del servizio di bilanciamento del carico e monitoraggio dello stato integrità | Microsoft Azure"
  description="Informazioni su come usare probe personalizzati per il servizio di bilanciamento del carico di Azure per monitorare le relative istanze"
  services="load-balancer"
  documentationCenter="na"
  authors="sdwheeler"
  manager="carmonm"
  editor=""
  tags="azure-resource-manager"
/> 
<tags
  ms.service="load-balancer"
  ms.devlang="na"
  ms.topic="article"
  ms.tgt_pltfrm="na"
  ms.workload="infrastructure-services"
  ms.date="08/25/2016"
  ms.author="sewhee" /> 

# Probe del servizio di bilanciamento del carico

Il servizio di bilanciamento del carico di Azure consente di monitorare l'integrità delle istanze del server tramite probe. Se un probe non risponde, il servizio di bilanciamento del carico interrompe l'invio di nuove connessioni all'istanza non integra. Le connessioni esistenti non sono interessate, mentre quelle nuove vengono inviate alle istanze integre.

I ruoli del servizio cloud, ovvero i ruoli di lavoro e i ruoli Web, usano un agente guest per il monitoraggio probe. I probe TCP o HTTP personalizzati devono essere configurati quando si usano macchine virtuali monitorate tramite il servizio di bilanciamento del carico.

## Informazioni su conteggio e timeout dei probe

Il comportamento dei probe dipende dagli elementi seguenti:

- Numero di probe riusciti che consentono di etichettare un'istanza come attiva.
- Numero di probe non riusciti che fanno sì che un'istanza sia etichettata come inattiva.

Il timeout diviso per il valore di frequenza probe è uguale al parametro SuccessFailCount che determina se un'istanza si presume sia attiva o inattiva. Nel portale di Azure il timeout è impostato sul doppio del valore della frequenza.

La configurazione dei probe deve essere la stessa in tutte le istanze con bilanciamento del carico per un endpoint, ovvero un set con bilanciamento del carico. Ciò significa che non è possibile avere una configurazione dei probe diversa per ogni istanza del ruolo o macchina virtuale nello stesso servizio ospitato per una combinazione di endpoint particolare. Ad esempio, ogni istanza deve avere porte locali e i timeout identici.


>[AZURE.IMPORTANT] Un probe del servizio di bilanciamento del carico usa l'indirizzo IP 168.63.129.16. Questo indirizzo IP pubblico facilita la comunicazione con le risorse interne della piattaforma per lo scenario Rete virtuale di Azure che prevede l'uso di un IP personale ("bring your own IP"). L'indirizzo IP pubblico virtuale 168.63.129.16 viene usato in tutte le aree e non cambia. È consigliabile consentire questo indirizzo IP in tutti i criteri firewall locali. Non deve essere considerato come un rischio per la sicurezza, perché solo la piattaforma Azure interna può generare un messaggio da questo indirizzo. In caso contrario, si avrà un comportamento imprevisto in diversi scenari, come la configurazione dello stesso intervallo di indirizzi IP 168.63.129.16 e la presenza di indirizzi IP duplicati.

## Informazioni sui tipi di probe

### Probe dell'agente guest

Questo probe è disponibile solo per i servizi cloud di Azure. Il servizio di bilanciamento del carico di Azure utilizza l'agente guest nella macchina virtuale e quindi rimane in ascolto e invia una risposta HTTP 200 OK solo quando l'istanza è nello stato Pronto, ovvero non nello stato Occupato, Riciclo in corso o Arresto.

Per altre informazioni, vedere l'articolo relativo alla [configurazione di file di definizione del servizio (file csdef) per i probe di integrità](https://msdn.microsoft.com/library/azure/jj151530.asp) oppure l'articolo [Introduzione alla creazione del servizio di bilanciamento del carico per Internet per i servizi cloud](load-balancer-get-started-internet-classic-cloud.md#check-load-balancer-health-status-for-cloud-services).

### Perché un probe dell'agente guest contrassegna un'istanza come non integra

Se l'agente guest non risponde con un messaggio HTTP 200 OK, il servizio di bilanciamento del carico contrassegna l'istanza che non risponde e arresta l'invio di traffico a tale istanza. Il servizio di bilanciamento del carico continua a effettuare il ping dell'istanza. Se l'agente guest risponde con un messaggio HTTP 200, il servizio di bilanciamento del carico continua a inviare il traffico a tale istanza.

Quando si usa un ruolo Web, il codice del sito Web in genere viene eseguito in w3wp.exe, che non è monitorato dall'infrastruttura di Azure o dall'agente guest. Gli errori in w3wp.exe, ad esempio le risposte HTTP 500, non vengono quindi segnalati all'agente guest e il servizio di bilanciamento del carico non esclude l'istanza dalla rotazione.

### Probe HTTP personalizzato

Il probe HTTP personalizzato del servizio di bilanciamento del carico esegue l'override del probe dell'agente guest predefinito e consente di creare una logica personalizzata per determinare l'integrità dell'istanza del ruolo. Per impostazione predefinita, il servizio di bilanciamento del carico esegue regolarmente probe sull'endpoint ogni 15 secondi. L'istanza viene considerata in rotazione dal servizio di bilanciamento del carico nel caso di risposta con un messaggio HTTP 200 entro il periodo di timeout, ovvero 31 secondi per impostazione predefinita.

Questa impostazione può essere utile se si vuole implementare la logica personalizzata per rimuovere le istanze dalla rotazione del servizio di bilanciamento del carico. Ad esempio, è possibile decidere di rimuovere un'istanza se presenta oltre il 90% di utilizzo della CPU e restituisce uno stato diverso da 200. Nel caso di ruoli Web che usano w3wp.exe si ottiene anche il monitoraggio automatico del sito Web, perché gli errori nel codice del sito Web restituiranno uno stato diverso da 200 al probe del servizio di bilanciamento del carico.

>[AZURE.NOTE] Il probe HTTP personalizzato supporta solo percorsi relativi e il protocollo HTTP. HTTPS non è supportato.

### Perché un probe HTTP personalizzato contrassegna un'istanza come non integra

- L'applicazione HTTP restituisce un codice di risposta HTTP diverso da 200, ad esempio 403, 404 o 500. Si tratta di un acknowledgment positivo per indicare che l'istanza dell'applicazione deve essere esclusa immediatamente dal servizio.

. Il server HTTP non invia alcuna risposta dopo il periodo di timeout. A seconda del valore di timeout impostato, questo può significare che più richieste di probe non riceveranno risposta prima che il probe venga contrassegnato come non in esecuzione, vale a dire prima dell'invio di probe SuccessFailCount.
- 	Il server chiude la connessione tramite l'invio di TCP Reset.

### Probe TCP personalizzato

I probe TCP avviano una connessione tramite l'esecuzione di un handshake a tre livelli con la porta definita.

### Perché un probe TCP personalizzato contrassegna un'istanza come non integra

- Il server TCP non invia alcuna risposta dopo il periodo di timeout. Il probe viene contrassegnato come non in esecuzione in base alla configurazione del numero di richieste di probe non riuscite che possono rimanere senza risposta prima che il probe sia contrassegnato come non in esecuzione.
- Il probe riceve un TCP Reset dall'istanza del ruolo.

Per altre informazioni sulla configurazione di un probe di integrità HTTP o di un probe TCP, vedere [Introduzione alla creazione di un servizio di bilanciamento del carico per Internet in Gestione risorse con PowerShell](load-balancer-get-started-internet-arm-ps.md#create-lb-rules-nat-rules-a-probe-and-a-load-balancer).

## Aggiungere di nuovo le istanze integre alla rotazione del servizio di bilanciamento del carico

I probe TCP e HTTP sono considerati integri e contrassegnano come integra l'istanza del ruolo quando:

- Il servizio di bilanciamento del carico ottiene un probe positivo al primo avvio della macchina virtuale.
- Il numero di SuccessFailCount, descritto in precedenza, definisce il valore dei probe riusciti necessario per contrassegnare l'istanza del ruolo come integra. Se un'istanza del ruolo è stata rimossa, il numero di probe successivi riusciti deve essere uguale o maggiore del valore di SuccessFailCount per contrassegnare l'istanza del ruolo come in esecuzione.

>[AZURE.NOTE] Se l'integrità di un'istanza del ruolo varia, il servizio di bilanciamento del carico rimane in attesa più a lungo prima di ripristinarne lo stato di integrità. Questa operazione viene eseguita tramite i criteri per la protezione dell'utente e dell'infrastruttura.

## Usare Analisi dei log per il servizio di bilanciamento del carico

È possibile usare [Analisi dei log per il servizio di bilanciamento del carico](load-balancer-monitor-log.md) per verificare lo stato di integrità e il conteggio dei probe. Per fornire statistiche dello stato di integrità del servizio di bilanciamento del carico, è possibile usare la registrazione con Power BI o con Operational Insights.

<!---HONumber=AcomDC_0921_2016-->