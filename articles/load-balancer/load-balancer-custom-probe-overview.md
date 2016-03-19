<properties 
   pageTitle="Probe personalizzati del servizio di bilanciamento del carico e monitoraggio dello stato integrità | Microsoft Azure"
   description="Scoprire come usare probe personalizzati per il servizio di bilanciamento del carico di Azure per monitorare le istanze dietro un servizio di bilanciamento del carico"
   services="load-balancer"
   documentationCenter="na"
   authors="joaoma"
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
   ms.date="01/21/2016"
   ms.author="joaoma" />


# Probe del servizio di bilanciamento del carico 

Il servizio di bilanciamento del carico di Azure consente di monitorare l'integrità delle varie istanze del server con i probe. Se un probe non risponde, il servizio di bilanciamento del carico di Azure interrompe l'invio di nuove connessioni alle istanze non integre. Le connessioni esistenti non vengono colpite mentre le nuove connessioni sono inviate a istanze integre.

Probe personalizzati TCP o HTTP devono essere configurati quando si usano macchine virtuali dietro un servizio di bilanciamento del carico. I ruoli del servizio cloud, ovvero i ruoli di lavoro e i ruoli Web, sono le uniche istanze del server con monitoraggio probe dell'agente guest.
 
## Conoscere il conteggio e il timeout dei probe

Il comportamento del probe dipende dal numero di ricerche con esito positivo o meno necessario per contrassegnare un'istanza come Up o Down. Questo valore viene calcolato come SuccessFailCount = timeout/frequenza. Per il portale, il timeout è impostato sul doppio del valore della frequenza, ovvero timeout = frequenza*2.

La configurazione dei probe di tutte le istanze con bilanciamento del carico per un endpoint, ovvero un set con bilanciamento del carico, deve essere uguale. Ciò significa che non è possibile avere una configurazione di probe diversa, ovvero porta locale, timeout e così via, per ogni istanza del ruolo o macchina virtuale nello stesso servizio ospitato per una combinazione di endpoint in particolare.


>[AZURE.IMPORTANT] Un probe del servizio di bilanciamento del carico usa un indirizzo IP 168.63.129.16. Questo indirizzo IP pubblico viene usato per facilitare un canale di comunicazione alle risorse interne della piattaforma per lo scenario bring-your-own di rete virtuale IP. L'indirizzo IP pubblico virtuale 168.63.129.16 viene usato in tutte le aree e non cambia. È consigliabile consentire l'indirizzo IP in tutti i criteri del firewall locale. Non deve essere considerato un rischio di sicurezza perché solo la piattaforma Azure interna può dare origine a un messaggio da questo indirizzo. In caso contrario, si avrà un comportamento imprevisto in una serie di scenari come la configurazione dello stesso intervallo di indirizzi IP 168.63.129.16 e indirizzi IP duplicati.


## Tipi di probe

### Probe dell'agente guest

Esclusivamente per servizi cloud. Il bilanciamento del carico di Azure utilizza l'agente guest nella macchina virtuale, si mette in ascolto e invia una risposta HTTP 200 OK solo quando l'istanza è nello stato Pronto, ovvero non nello stato Occupato, Riciclo in corso, Arresto e così via.

Per altre informazioni, vedere l'articolo sulla [configurazione di file di definizione del servizio (csdef) per i probe di integrità](https://msdn.microsoft.com/library/azure/jj151530.asp) o l'[Introduzione alla creazione del servizio di bilanciamento del carico Internet per i servizi cloud](load-balancer-get-started-internet-classic-cloud.md#check-load-balancer-health-status-for-cloud-services).
 
### Perché un probe dell'agente guest contrassegna un'istanza come non integra.

Se l'agente guest non risponde con HTTP 200 OK, il bilanciamento del carico di Azure contrassegna l'istanza che non risponde e interrompe l'invio di traffico a tale istanza. Il bilanciamento del carico di Azure continua a effettuare il ping dell'istanza e, se l'agente guest risponde con HTTP 200, il bilanciamento del carico di Azure invia di nuovo il traffico a tale istanza.

Quando si usa un ruolo Web, il codice del sito Web viene in genere eseguito in w3wp.exe, che non è monitorato dall'agente guest o dall'infrastruttura di Azure, pertanto gli errori in w3wp.exe, ad esempio le risposte HTTP 500, non vengono segnalati all'agente guest e il servizio di bilanciamento del carico non sa di dover escludere l'istanza dalla rotazione.


### Probe HTTP personalizzato

Il probe del servizio di bilanciamento del carico HTTP personalizzato esegue l'override del probe dell'agente guest predefinito e consente di creare una logica personalizzata per determinare l'integrità dell'istanza del ruolo. Il servizio di bilanciamento del carico esegue il probe dell'endpoint ogni 15 secondi per impostazione predefinita e l'istanza viene considerata nella rotazione del servizio di bilanciamento del carico se risponde con HTTP 200 entro il periodo di timeout, ovvero 31 secondi per impostazione predefinita. Ciò è utile per implementare una logica personalizzata e rimuovere le istanze dalla rotazione del servizio di bilanciamento del carico, ad esempio restituendo uno stato diverso da 200 se l'istanza supera il 90% della CPU. Per i ruoli Web che usano w3wp.exe, ciò significa anche che si ottiene il monitoraggio automatico del sito Web in quanto gli errori nel codice del sito Web restituiranno uno stato diverso da 200 al probe del servizio di bilanciamento del carico.

>[AZURE.NOTE] Il probe personalizzato HTTP supporta solo il protocollo HTTP e i relativi percorsi. HTTPS non è supportato.


### Perché un probe personalizzato HTTP contrassegna un'istanza come non integra. 

- L'applicazione HTTP restituisce un codice di risposta HTTP diverso da 200, vale a dire 403, 404, 500 e così via. Si tratta di un acknowledgment positivo che l'istanza dell'applicazione deve essere esclusa immediatamente dal servizio.

-  Quando il server HTTP non risponde affatto dopo il periodo di timeout. Si noti che in base al valore di timeout impostato, è possibile che più richieste di probe non ricevano risposta prima di aver contrassegnato il probe come down, ovvero quando vengono inviati probe SuccessFailCount.
- 	Quando il server chiude la connessione tramite un ripristino di TCP.

### Probe TCP personalizzato

I probe TCP avviano una connessione alla porta definita tramite l'esecuzione di un handshake a tre vie.

### Perché un probe personalizzato TCP contrassegna un'istanza come non integra.

- Quando il server TCP non risponde affatto dopo il periodo di timeout. Ciò dipenderà dal numero di richieste di probe non riuscite, configurate per non ricevere risposta prima di aver contrassegnato il probe come down.
- 	Si riceve un ripristino di TCP dall'istanza del ruolo.

Vedere l'articolo sulla [creazione di un servizio di bilanciamento del carico Internet per resource manager](load-balancer-get-started-internet-arm-ps.md#create-lb-rules-nat-rules-a-probe-and-a-load-balancer) per sapere come configurare un probe di integrità HTTP o un probe TCP.

## Aggiunta di istanze integre nel servizio di bilanciamento del carico

I probe TCP e HTTP sono considerati integri e contrassegnano come integra l'istanza del ruolo quando:

- Si avvia per la prima volta la VM e il servizio di bilanciamento del carico ottiene un probe positivo.
- Il numero SuccessFailCount di cui sopra indica il valore dei probe con esito positivo necessario per contrassegnare l'istanza del ruolo come integra. Se un'istanza del ruolo è stata rimossa, è necessario avere probe SuccessFailCount consecutivamente con esito positivo per contrassegnare l'istanza del ruolo come UP.

>[AZURE.NOTE] Se l'integrità di un'istanza del ruolo è variabile, il servizio di bilanciamento del carico di Azure rimane in attesa più a lungo prima di rimettere l'istanza del ruolo nello stato di integrità. Questa operazione viene eseguita tramite i criteri per la protezione dell'utente e dell'infrastruttura.

## Analisi dei log per il servizio di bilanciamento del carico

È possibile usare l'[analisi dei log per il servizio di bilanciamento del carico](load-balancer-monitor-log.md) per verificare lo stato integrità e il conteggio dei probe. È possibile usare la registrazione con Power BI o Operational Insights per fornire statistiche dello stato integrità del servizio di bilanciamento del carico.
 

<!---HONumber=AcomDC_0302_2016-->