<properties 
   pageTitle="Panoramica del bilanciamento del carico di Azure | Microsoft Azure"
   description="Panoramica di funzionalità, architettura e implementazione del bilanciamento del carico di Azure. Informazioni sul funzionamento del bilanciamento del carico e su come usarlo per il cloud"
   services="load-balancer"
   documentationCenter="na"
   authors="joaoma"
   manager="adinah"
   editor="tysonn" />
<tags 
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="07/10/2015"
   ms.author="joaoma" />


# Panoramica del bilanciamento del carico 
Il bilanciamento del carico di Azure offre elevati livelli di disponibilità e prestazioni di rete per le applicazioni. Si tratta di un bilanciamento del carico di livello-4 (TCP, UDP), che distribuisce il traffico in ingresso tra istanze del servizio integre in macchine virtuali o servizi cloud definiti in un set di bilanciamento del carico.
 
Il servizio può essere configurato per:

- Bilanciare il carico del traffico Internet in ingresso nelle macchine virtuali. In questo caso, si parla di [bilanciamento del carico Internet](load-balancer-overview.md).
- Bilanciare il carico del traffico tra macchine virtuali in una rete virtuale, tra macchine virtuali nei servizi cloud o tra computer locali e macchine virtuali in una rete virtuale cross-premise. In questo caso, si parla di [bilanciamento del carico interno](load-balancer-internal-overview.md).
- 	Inoltrare il traffico esterno a una specifica istanza di macchina virtuale


## Funzionalità del bilanciamento del carico

### Bilanciamento del carico di livello 4, distribuzione basata su hash

Il bilanciamento del carico di Azure usa un algoritmo di distribuzione basato su hash. Per impostazione predefinita, usa un hash a 5 tuple (IP di origine, porta di origine, IP di destinazione, porta di destinazione, tipo di protocollo) per eseguire il mapping del traffico ai server disponibili. La persistenza viene mantenuta solo all'interno di una sessione di trasporto. I pacchetti nella stessa sessione TCP o UDP verranno indirizzati alla stessa istanza di IP di data center (DIP) nell'endpoint con carico bilanciato. Quando il client chiude e riapre la connessione o avvia una nuova sessione dallo stesso IP di origine, la porta di origine cambia. Il traffico potrebbe quindi venire indirizzato a un endpoint DIP diverso.


Per altri dettagli, vedere [Modalità di distribuzione del bilanciamento del carico](load-balancer-distribution-mode.md).

![bilanciamento del carico basato su hash](./media/load-balancer-overview/load-balancer-distribution.png)

### Port forwarding

Il bilanciamento del carico di Azure consente di controllare in che modo vengono gestite le comunicazioni in ingresso, ad esempio il traffico avviato da host Internet o macchine virtuali in altri servizi cloud o reti virtuali. Questo controllo è rappresentato da un endpoint, detto anche endpoint di input.

Un endpoint è in ascolto su una porta pubblica e inoltra il traffico a una porta interna. È possibile eseguire il mapping delle stesse porte per un endpoint interno o esterno oppure usare una porta diversa. È ad esempio possibile configurare un server Web per l'ascolto sulla porta 81, mentre l'endpoint pubblico è mappato alla porta 80. La creazione di un endpoint pubblico determina la creazione di un servizio di bilanciamento del carico di Azure.

Per impostazione predefinita, gli endpoint di una macchina virtuale creata con il portale di gestione di Azure vengono utilizzati e configurati per il traffico delle sessioni remote di Windows PowerShell e per il protocollo Remote Desktop Protocol (RDP). Questi endpoint consentono di amministrare la macchina virtuale in remoto tramite Internet.


### Riconfigurazione automatica in base alla scalabilità

Il bilanciamento del carico di Azure si riconfigura immediatamente in base alla scalabilità verticale del numero di istanze, sia che si aumenti il numero di istanze per ruolo Web o di lavoro sia che si inseriscano macchine virtuali aggiuntive nello stesso set con carico bilanciato.


### Monitoraggio del servizio
Il bilanciamento del carico di Azure consente di verificare, tramite probe, l'integrità delle varie istanze del server. Se un probe non risponde, il bilanciamento del carico di Azure interrompe l'invio di una nuova connessione alle istanze non integre. Ciò non influisce sulle connessioni esistenti.

Sono supportati tre tipi di probe:
 
- Probe dell'agente guest (solo in VM PaaS). Il bilanciamento del carico di Azure utilizza l'agente guest nella macchina virtuale, si mette in ascolto e invia una risposta HTTP 200 OK solo quando l'istanza è nello stato Pronto, ovvero non nello stato Occupato, Riciclo in corso, Arresto e così via. Se l'agente guest non risponde con HTTP 200 OK, il bilanciamento del carico di Azure contrassegna l'istanza che non risponde e interrompe l'invio di traffico a tale istanza. Il bilanciamento del carico di Azure continua a effettuare il ping dell'istanza e, se l'agente guest risponde con HTTP 200, il bilanciamento del carico di Azure invia di nuovo il traffico a tale istanza. Quando si usa un ruolo Web, il codice del sito Web viene in genere eseguito in w3wp.exe, che non è monitorato dall'agente guest o dall'infrastruttura di Azure, pertanto gli errori in w3wp.exe (ad esempio le risposte HTTP 500) non vengono segnalati all'agente guest e il bilanciamento del carico non sa di dover escludere l'istanza dalla rotazione.

- Probe HTTP personalizzati. Il probe di bilanciamento del carico personalizzato esegue l'override del probe dell'agente guest predefinito e consente di creare una logica personalizzata per determinare l'integrità dell'istanza del ruolo. Il bilanciamento del carico esegue regolarmente il probe dell'endpoint (per impostazione predefinita ogni 15 secondi) e l'istanza viene considerata nella rotazione se risponde con ACK TCP o HTTP 200 entro il periodo di timeout (31 secondi per impostazione predefinita). Ciò può risultare utile per implementare una logica personalizzata per la rimozione di istanze dalla rotazione di bilanciamento del carico, ad esempio restituendo uno stato diverso da 200 se l'istanza presenta oltre il 90% di uso della CPU. Per i ruoli Web che usano w3wp.exe, ciò significa anche che si ottiene il monitoraggio automatico del sito Web, in quanto gli errori nel codice del sito Web restituiranno uno stato diverso da 200 al probe del bilanciamento del carico.

- Probe TCP personalizzati. I probe TCP si basano sulla corretta attivazione di una sessione TCP in una porta probe definita.

Per altre informazioni, vedere [Probe di integrità del bilanciamento del carico](https://msdn.microsoft.com/library/azure/jj151530.aspx).

### Source NAT (SNAT)


Tutto il traffico in uscita proveniente dal servizio e destinato a Internet viene sottoposto a un processo SNAT (Source NAT) usando lo stesso indirizzo VIP del traffico in ingresso. Questo processo offre tre importanti vantaggi:

- Consente di eseguire in modo semplice l'aggiornamento e il ripristino di emergenza dei servizi, in quanto l'indirizzo VIP può essere mappato in modo dinamico a un'altra istanza del servizio

- Semplifica la gestione dell'elenco di controllo di accesso (ACL), in quanto l'elenco può essere espresso in termini di VIP e pertanto resta invariato anche in caso di aumento o riduzione delle istanze dei servizi o di ridistribuzione dei servizi

La configurazione di bilanciamento del carico di Azure supporta il processo NAT di tipo full cone per UDP. Con questo tipo di processo, la porta consente le connessioni in ingresso da qualsiasi host esterno (in risposta a una richiesta in uscita).

![SNAT](./media/load-balancer-overview/load-balancer-snat.png)


>[AZURE.NOTE]Si noti che per ogni nuova connessione in uscita avviata da una macchina virtuale, viene allocata anche una porta in uscita dal bilanciamento del carico di Azure. L'host esterno vedrà il traffico in arrivo come porta allocata VIP. Se gli scenari richiedono un numero elevato di connessioni in uscita, è consigliabile che le macchine virtuali usino indirizzi IP pubblici a livello di istanza, in modo che sia disponibile un IP in uscita dedicato per il processo SNAT (Source Network Address Translation). Ciò consente di ridurre il rischio di esaurimento delle porte.
>
>Il numero massimo di porte che possono essere usate dall'indirizzo VIP o ILPIP è 64.000. Si tratta di una limitazione standard TCP.


**Supporto per più IP con carico bilanciato per le macchine virtuali**

A un set di macchine virtuali è possibile assegnare più di un indirizzo IP pubblico con carico bilanciato. Ciò consente di ospitare più siti Web SSL e/o più listener del gruppo di disponibilità SQL AlwaysOn nello stesso set di macchine virtuali. Per altre informazioni, vedere [Indirizzi VIP multipli per un servizio cloud](load-balancer-multivip.md)

**Distribuzioni basate su modelli con Gestione risorse di Azure (anteprima pubblica)** Gestione risorse di Azure è il nuovo framework di gestione per i servizi di Azure. È ora possibile gestire il bilanciamento del carico di Azure con API e strumenti basati su Gestione risorse di Azure. Per altre informazioni su Gestione risorse di Azure, vedere [Semplificazione di scenari IaaS con Gestione risorse di Azure](http://azure.microsoft.com/blog/2015/04/29/iaas-just-got-easier-again/).


## Passaggi successivi

[Panoramica del bilanciamento del carico Internet](load-balancer-internet-overview.md)

[Panoramica del bilanciamento del carico interno](load-balancer-internal-overview.md)

[Introduzione - Bilanciamento del carico Internet](load-balancer-internet-getstarted.md)
 

<!---HONumber=July15_HO4-->