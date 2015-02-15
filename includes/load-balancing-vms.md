<properties title="Load Balancing for Azure Infrastructure Services" pageTitle="Bilanciamento del carico per i servizi di infrastruttura di Azure" description="Descrive le funzionalità per eseguire il bilanciamento del carico in Gestione traffico e il bilanciamento del carico." metaKeywords="" services="virtual-machines" solutions="" documentationCenter="" authors="cherylmc" videoId="" scriptId="" manager="adinah" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="" ms.devlang="na" ms.topic="article" ms.date="09/17/2014" ms.author="cherylmc" />

#Bilanciamento del carico per i servizi di infrastruttura di Azure#

Per i servizi di infrastruttura di Azure sono disponibili due livelli di bilanciamento del carico:

- **Livello DNS**:  bilanciamento del carico per il traffico verso servizi cloud o siti Web Azure diversi ubicati in datacenter differenti o verso endpoint esterni. Questa operazione viene eseguita con Gestione traffico e usando il metodo di bilanciamento del carico Round robin.
- **Livello di rete**:  bilanciamento del carico del traffico Internet in ingresso nelle diverse macchine virtuali di un servizio cloud oppure bilanciamento del carico del traffico esistente tra le macchine virtuali di un servizio cloud o di una rete virtuale. Questa operazione viene eseguita con il servizio di bilanciamento del carico di Azure.

##Bilanciamento del carico di Gestione traffico per servizi cloud e siti Web##

Gestione traffico di Azure consente di controllare la distribuzione del traffico utenti verso endpoint quali servizi cloud, siti Web, siti esterni o altri profili di Gestione traffico. Gestione traffico applica un motore dei criteri intelligente alle query DNS (Domain Name System) relative ai nomi di dominio delle risorse Internet. È possibile infatti che i servizi cloud o i siti Web siano in esecuzione in datacenter ubicati in aree geografiche diverse. 

Per configurare endpoint esterni o profili di Gestione traffico come endpoint, è necessario usare REST o Windows PowerShell. 

Per la distribuzione del traffico, Gestione traffico di Azure usa tre metodi di bilanciamento del carico:

- **Failover**:  questo metodo viene usato quando si desidera servirsi di un endpoint principale per tutto il traffico. È opportuno tuttavia prevedere endpoint di backup nel caso in cui l'endpoint principale non sia disponibile.
- **Prestazioni**:  questo metodo viene usato quando gli endpoint sono ubicati in aree geografiche diverse e si desidera che i client richiedenti si servano dell'endpoint "più vicino" in termini di minor latenza.
- **Round robin:** questo metodo viene usato quando si desidera distribuire il carico su un set di servizi cloud eseguiti nello stesso datacenter oppure su servizi cloud o siti Web eseguiti in datacenter diversi.

Per altre informazioni, vedere [Informazioni sui metodi di bilanciamento del carico di Gestione traffico](http://msdn.microsoft.com/it-it/library/azure/dn339010.aspx).

Nella figura seguente viene illustrato un esempio di bilanciamento del carico con metodo Round robin per la distribuzione del traffico tra diversi servizi cloud.

![loadbalancing](./media/load-balancing-vms/TMSummary.png)

Il processo di base è il seguente:

1.	Un client Internet esegue una query relativa a un nome di dominio corrispondente a un servizio Web.
2.	DNS inoltra la richiesta di query a Gestione traffico.
3.	Gestione traffico risponde inviando il nome DNS del servizio cloud presente nell'elenco round robin. Il server DNS del client Internet risolve il nome in un indirizzo IP e lo invia al client Internet.
4.	Il client Internet si connette al servizio cloud scelto.

Per altre informazioni, vedere [Gestione traffico](http://msdn.microsoft.com/it-it/library/azure/hh745750.aspx).

## Bilanciamento del traffico di Azure per macchine virtuali ##

Le macchine virtuali appartenenti allo stesso servizio cloud o alla stessa rete virtuale possono comunicare direttamente tra loro usando i relativi indirizzi IP privati. I computer e i servizi esterni al servizio cloud o alla rete virtuale possono comunicare solo con le macchine virtuali di un servizio cloud o di una rete virtuale con un endpoint configurato. Un endpoint è un mapping di una porta o di un indirizzo IP pubblico alla porta o all'indirizzo IP privato di una macchina virtuale o di un ruolo Web all'interno di un servizio cloud di Azure.

Il servizio di bilanciamento del carico di Azure distribuisce in modo casuale un tipo specifico di traffico in ingresso tra più macchine virtuali o servizi di una configurazione nota come set con carico bilanciato. È ad esempio possibile dividere il carico del traffico delle richieste Web tra più server Web o ruoli Web.

Nella figura seguente è illustrato un endpoint con carico bilanciato per il traffico Web (non crittografato) condiviso tra tre macchine virtuali per la porta TCP 80, pubblica e privata. Queste tre macchine virtuali appartengono a un set con carico bilanciato.

![loadbalancing](./media/load-balancing-vms/LoadBalancing.png)

Per altre informazioni, vedere [Bilanciamento del carico di Azure](http://msdn.microsoft.com/it-it/library/azure/dn655058.aspx). Per i passaggi da eseguire per creare un set con carico bilanciato, vedere [Configurare un set con carico bilanciato](http://msdn.microsoft.com/it-it/library/azure/dn655055.aspx).

Azure è inoltre in grado di bilanciare il carico all'interno di un servizio cloud o una rete virtuale. Questo processo, noto come bilanciamento del carico interno, può essere usato per gli scopi seguenti:

- Bilanciamento del carico tra server appartenenti a livelli diversi di un'applicazione multilivello, ad esempio tra il livello Web e quello di database.
- Bilanciamento del carico per applicazioni line-of-business (LOB) ospitate in Azure senza la necessità di applicazioni software o componenti hardware aggiuntivi per il bilanciamento del carico. 
- Inclusione di server locali nel set di computer il cui traffico viene sottoposto a bilanciamento del carico.

Analogamente al bilanciamento del carico di Azure, il bilanciamento del carico interno viene facilitato con la configurazione di un set con carico bilanciato interno. 

Nella figura seguente viene illustrato un esempio di endpoint con carico bilanciato interno relativo a un'applicazione line-of-business (LOB) condivisa fra tre macchine virtuali appartenenti a una rete virtuale cross-premise. 

![loadbalancing](./media/load-balancing-vms/LOBServers.png)

Per altre informazioni, vedere [Bilanciamento del carico interno](http://msdn.microsoft.com/it-it/library/azure/dn690121.aspx). Per i passaggi da eseguire per creare un set con carico bilanciato, vedere [Configurare un set con carico bilanciato interno](http://msdn.microsoft.com/it-it/library/azure/dn690125.aspx).

<!-- LINKS -->


<!--HONumber=42-->
