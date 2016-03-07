<properties 
   pageTitle="Risoluzione per le macchine virtuali e le istanze del ruolo"
   description="Scenari di risoluzione dei nomi per Azure IaaS, soluzioni ibride, tra diversi servizi cloud, Active Directory e utilizzando il proprio server DNS"
   services="virtual-network"
   documentationCenter="na"
   authors="GarethBradshawMSFT"
   manager="carmonm"
   editor="tysonn" />
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/20/2016"
   ms.author="joaoma" />

# Risoluzione dei nomi per le macchine virtuali e le istanze del ruolo

A seconda di come si usa Azure per ospitare soluzioni IaaS, PaaS e ibride, potrebbe essere necessario consentire la comunicazione tra VM e istanze del ruolo create. Nonostante questa comunicazione possa avvenire usando indirizzi IP, è molto più semplice usare nomi che possono essere facilmente ricordati e che non cambiano.

Quando le istanze del ruolo e le VM ospitate in Azure devono risolvere nomi di dominio trasformandoli in indirizzi IP interni, possono usare uno di questi due metodi:

- [Risoluzione dei nomi fornita da Azure](#azure-provided-name-resolution)

- [Risoluzione dei nomi usando il server DNS](#name-resolution-using-your-own-dns-server) (che potrebbe inoltrare le query ai server DNS forniti da Azure)

Il tipo di risoluzione dei nomi usato dipende dal modo in cui VM e istanze del ruolo devono comunicare.

**Nella tabella seguente vengono illustrate diverse ipotesi e le corrispondenti soluzioni di risoluzione dei nomi:**

| **Scenario** | **Soluzione** | **Suffisso** |
|--------------|--------------|----------|
| Risoluzione dei nomi tra istanze del ruolo o macchine virtuali situate nello stesso servizio cloud o nella stessa rete virtuale | [Risoluzione dei nomi fornita da Azure](#azure-provided-name-resolution)| nome host o nome di dominio completo |
| Risoluzione dei nomi tra istanze del ruolo o macchine virtuali situate in diverse reti virtuali | Server DNS gestiti dal cliente che inoltrano query tra reti virtuali per la risoluzione da parte di Azure (proxy DNS). Vedere [Risoluzione dei nomi usando il server DNS](#name-resolution-using-your-own-dns-server)| Solo nome di dominio completo |
| Risoluzione dei nomi servizi e computer locali da istanze del ruolo o macchine virtuali in Azure | Server DNS gestiti dal cliente, ad esempio controller di dominio locale, controller di dominio di sola lettura locale o server DNS secondario sincronizzati tramite trasferimenti di zona. Vedere [Risoluzione dei nomi usando il server DNS](#name-resolution-using-your-own-dns-server)|Solo nome di dominio completo |
| Risoluzione di nomi host di Azure da computer locali | Inoltra le query a un server proxy DNS gestito dal cliente nella rete virtuale corrispondente. Il server proxy inoltra le query ad Azure per la risoluzione. Vedere [Risoluzione dei nomi usando il server DNS](#name-resolution-using-your-own-dns-server)| Solo nome di dominio completo |
| DNS inversi per indirizzi IP interni | [Risoluzione dei nomi usando il server DNS](#name-resolution-using-your-own-dns-server) | N/D |
| Risoluzione dei nomi tra macchine virtuali o istanze del ruolo situate in servizi cloud diversi e non in una rete virtuale| Non applicabile Connettività tra macchine virtuali e istanze del ruolo in servizi cloud diversi non è supportata esternamente a una rete virtuale.| N/D |



## Risoluzione dei nomi fornita da Azure

Oltre alla risoluzione dei nomi DNS pubblici, Azure offre la risoluzione dei nomi interni per VM e istanze del ruolo che si trovano all'interno della stessa rete virtuale o dello stesso servizio cloud. Le VM e le istanze in un servizio cloud condividono lo stesso suffisso DNS, quindi il nome host da solo è sufficiente, tuttavia, nelle reti virtuali classiche, servizi cloud diversi hanno suffissi DNS diversi, quindi è necessario il nome di dominio completo per la risoluzione dei nomi tra diversi servizi cloud. Nelle reti virtuali basate su Azure Resource Manager il suffisso DNS è coerente nella rete virtuale, quindi il nome di dominio completo non è necessario e i nomi DNS possono essere assegnati sia alle schede di interfaccia di rete che alle macchine virtuali. Sebbene la risoluzione dei nomi fornita da Azure non richieda alcuna configurazione, non è la scelta più appropriata per tutti gli scenari di distribuzione, come illustrato nella tabella precedente.

> [AZURE.NOTE] Nel caso dei ruoli web e di lavoro, è possibile accedere anche gli indirizzi IP interni delle istanze del ruolo in base al numero di nome e al numero di istanza del ruolo utilizzando l'API REST di gestione dei servizi Azure. Per altre informazioni, vedere [Riferimento all'API REST di gestione dei servizi](https://msdn.microsoft.com/library/azure/ee460799.aspx).

### Funzionalità e considerazioni

**Funzionalità**

- Facilità di utilizzo: non è necessaria alcuna configurazione per usare la risoluzione dei nomi fornita da Azure.

- Il servizio di risoluzione dei nomi fornito da Azure garantisce la disponibilità elevata ed evita così di dover creare e gestire i cluster dei propri server DNS.

- Può essere usato in combinazione con i propri server DNS per risolvere i nomi host locali e i nomi host di Azure.

- La risoluzione dei nomi viene offerta tra istanze del ruolo e VM presenti nello stesso servizio cloud senza necessità di un nome di dominio completo.

- La risoluzione dei nomi viene fornita tra macchine virtuali nelle reti virtuali basate su Gestione risorse di Azure senza la necessità del nome di dominio completo, mentre le reti classiche richiedono il nome di dominio completo per la risoluzione dei nomi in servizi cloud diversi.

- È possibile usare nomi host personalizzati in grado di descrivere in modo più adeguato le distribuzioni, anziché usare nomi generati automaticamente.

**Considerazioni:**

- Il suffisso DNS creato da Azure non può essere modificato.

- Non è possibile registrare manualmente i propri record.

- WINS e NetBIOS non sono supportati. Non è possibile visualizzare le macchine virtuali in Esplora risorse.

- I nomi host devono essere compatibili con DNS (devono utilizzare solo 0-9, a-z e '-' e non possono iniziare o terminare con un '-'. Vedere RFC 3696 sezione 2).

- Il traffico di query DNS è limitato per ogni VM. Questo in genere non comporta alcun impatto sulla maggior parte delle applicazioni. Se viene osservata la limitazione delle richieste, assicurarsi che la memorizzazione nella cache sul lato client sia abilitata. Per altre informazioni, vedere l'articolo che illustra [come usare al meglio la risoluzione dei nomi fornita da Azure](#Getting-the-most-from-Azure-provided-name-resolution).

- Solo le macchine virtuali nei primi 180 servizi cloud sono registrate per ogni rete virtuale classica. Questo non si applica alle reti virtuali basate su Gestione risorse di Azure.


### Come usare al meglio la risoluzione dei nomi fornita da Azure
**Memorizzazione nella cache sul lato client:**

Non tutte le query DNS devono essere inviate attraverso la rete. La memorizzazione nella cache sul lato client consente di ridurre la latenza e migliorare la resilienza ai blip (brevi interruzioni) di rete, tramite la risoluzione di query DNS ricorrenti da una cache locale. I record DNS contengono una durata (TTL) che consente alla cache di memorizzare il record per il periodo di tempo più lungo possibile senza che questo influisca sullo stato di aggiornamento del record. Per questo motivo, la memorizzazione nella cache sul lato client è ideale per la maggior parte delle situazioni.

Il client DNS di Windows predefinito contiene una cache DNS incorporata. Alcune distribuzioni di Linux non includono la memorizzazione nella cache per impostazione predefinita, è quindi consigliabile aggiungerne una a ogni VM Linux (dopo aver verificato che non sia già presente una cache locale).

Sono disponibili molti pacchetti di memorizzazione nella cache DNS diversi, ad esempio dnsmasq. Di seguito è riportata la procedura per installare dnsmasq nei distro più comuni:

- **Ubuntu (usa resolvconf)**:
	- installare il pacchetto dnsmasq ("sudo apt-get install dnsmasq").
- **SUSE (usa netconf)**:
	- installare il pacchetto dnsmasq ("sudo zypper install dnsmasq") 
	- abilitare il servizio dnsmasq ("systemctl enable dnsmasq.service") 
	- avviare il servizio dnsmasq ("systemctl start dnsmasq.service") 
	- modificare "/etc/sysconfig/network/config" e sostituire NETCONFIG\_DNS\_FORWARDER="" con "dnsmasq"
	- aggiornare resolv.conf ("netconfig update") per impostare la cache come resolver DNS locale
- **OpenLogic (usa NetworkManager)**:
	- installare il pacchetto dnsmasq ("sudo yum install dnsmasq")
	- abilitare il servizio dnsmasq ("systemctl enable dnsmasq.service")
	- avviare il servizio dnsmasq ("systemctl start dnsmasq.service")
	- aggiungere "prepend domain-name-servers 127.0.0.1;" in "/etc/dhclient-eth0.conf"
	- riavviare il servizio di rete ("service network restart") per impostare la cache come resolver DNS locale

> [AZURE.NOTE]\: il pacchetto 'dnsmasq' è solo una delle cache DNS disponibili per Linux. Prima di usarla, verificare che sia adatta in base alle esigenze specifiche e assicurarsi che non siano installate altre cache.

**Ripetizione di tentativi sul lato client:**

DNS è principalmente un protocollo UDP. Poiché il protocollo UDP non garantisce il recapito dei messaggi, la logica di ripetizione dei tentativi viene gestita nel protocollo DNS stesso. Ogni client DNS (sistema operativo) può includere una logica di ripetizione dei tentativi diversa, in base alle preferenze degli autori.

 - I sistemi operativi Windows eseguono un nuovo tentativo dopo 1 secondo e ne eseguono un altro dopo 2, 4 e altri 4 secondi. 
 - La configurazione di Linux predefinita esegue il tentativo dopo 5 secondi. È consigliabile modificare questa impostazione in modo da eseguire 5 tentativi a intervalli di 1 secondo.  

Per verificare le impostazioni correnti in una macchina virtuale Linux, aprire il file 'cat /etc/resolv.conf' e osservare la riga 'options', ad esempio:

	options timeout:1 attempts:5

Il file resolv.conf è solitamente generato automaticamente e non deve essere modificato. La procedura specifica per aggiungere la riga 'options' varia a seconda del distro:

- **Ubuntu** (usa resolvconf):
	- aggiungere la riga per le opzioni in '/etc/resolveconf/resolv.conf.d/head' 
	- eseguire 'resolvconf -u' per aggiornare
- **SUSE** (usa netconf):
	- aggiungere 'timeout:1 attempts:5' al parametro NETCONFIG\_DNS\_RESOLVER\_OPTIONS="" in '/etc/sysconfig/network/config' 
	- eseguire 'netconfig update' per aggiornare
- **OpenLogic** (usa NetworkManager):
	- aggiungere 'echo "options timeout:1 attempts:5"' in '/etc/NetworkManager/dispatcher.d/11-dhclient' 
	- eseguire 'service network restart' per aggiornare

## Risoluzione dei nomi usando il server DNS
In alcune situazioni le esigenze in termini di risoluzione dei nomi potrebbero andare oltre le funzionalità fornite da Azure, ad esempio quando si usano i domini di Active Directory o quando è necessaria la risoluzione DNS tra reti virtuali. Per questi scenari specifici, Azure offre la possibilità di usare i propri server DNS.

I server DNS all'interno di una rete virtuale possono inoltrare query DNS ai resolver ricorsivi di Azure per risolvere i nomi host all'interno di quella rete virtuale. Ad esempio, un controller di dominio in esecuzione in Azure può rispondere a query DNS per i relativi domini e inoltrare tutte le altre query ad Azure. Questo consente alle macchine virtuali di visualizzare sia le risorse locali, tramite il controller di dominio, sia i nomi host forniti da Azure, tramite il server di inoltro. L'accesso ai resolver ricorsivi di Azure viene fornito tramite l'indirizzo IP virtuale 168.63.129.16.

L'inoltro DNS consente anche la risoluzione DNS tra reti virtuali e permette alle macchine virtuali locali di risolvere i nomi host forniti da Azure. Per risolvere il nome host di una macchina virtuale, la macchina virtuale del server DNS deve risiedere nella stessa rete virtuale ed essere configurata per inoltrare le query relative ai nomi host ad Azure. Poiché il suffisso DNS è diverso in ogni rete virtuale, è possibile usare le regole di inoltro condizionale per inviare le query DNS alla rete virtuale corretta per la risoluzione. L'immagine seguente mostra due reti virtuali e una rete locale che gestiscono la risoluzione DNS tra reti virtuali con questo metodo:

![DNS tra reti virtuali](./media/virtual-networks-name-resolution-for-vms-and-role-instances/inter-vnet-dns.png)

Quando si usa la risoluzione dei nomi di Azure, il suffisso DNS interno viene fornito a ogni macchina virtuale tramite DHCP. Quando si usa la soluzione di risoluzione dei nomi personalizzata, questo suffisso non viene fornito alle macchine virtuali perché interferisce con altre architetture DNS. Per fare riferimento alle macchine virtuali usando il nome di dominio completo o per configurare il suffisso nelle macchine virtuali, è possibile determinare il suffisso tramite PowerShell o l'API:

-  Per le reti virtuali gestite tramite Azure Resource Manager, il suffisso è disponibile tramite la risorsa [scheda di interfaccia di rete](https://msdn.microsoft.com/library/azure/mt163668.aspx) o il cmdlet [Get-AzureRmNetworkInterface](https://msdn.microsoft.com/library/mt619434.aspx).    
-  Per le distribuzioni classiche, il suffisso è disponibile tramite una chiamata all'[API GetDeployment](https://msdn.microsoft.com/library/azure/ee460804.aspx) o il cmdlet [Get-AzureVM -Debug](https://msdn.microsoft.com/library/azure/dn495236.aspx).


Se l'inoltro delle query ad Azure non soddisfa le esigenze correnti, sarà necessario fornire una soluzione DNS personalizzata. Questa soluzione DNS dovrà:

-  Fornire una soluzione di risoluzione dei nomi host appropriata, ad esempio tramite [DNS dinamico](virtual-networks-name-resolution-ddns.md). Si noti che se si usa il DNS dinamico potrebbe essere necessario disabilitare lo scavenging del record DNS, perché i lease DHCP di Azure sono molto lunghi e potrebbero rimuovere i record DNS anticipatamente. 
-  Fornire una soluzione di risoluzione ricorsiva appropriata per consentire la risoluzione dei nomi di dominio esterni.
-  Essere accessibile (tramite TCP e UDP sulla porta 53) dai client che gestisce e in grado di accedere a Internet.
-  Essere protetta dagli accessi provenienti da Internet per attenuare i rischi rappresentati da agenti esterni.

> [AZURE.NOTE] Per prestazioni ottimali, quando si usano macchine virtuali di Azure come server DNS, è necessario disabilitare IPv6 e assegnare un indirizzo [IP pubblico a livello di istanza](virtual-networks-instance-level-public-ip.mp) a ogni macchina virtuale del server DNS. Se si sceglie di usare Windows Server come server DNS, [questo articolo](http://blogs.technet.com/b/networking/archive/2015/08/19/name-resolution-performance-of-a-recursive-windows-dns-server-2012-r2.aspx) offre un'analisi delle prestazioni e ottimizzazioni aggiuntive.



## Specificare i server DNS

È possibile specificare più server DNS da utilizzare per le macchine virtuali e le istanze del ruolo. Per ogni query DNS, il client proverà per prima cosa il server DNS preferito e proverà i server alternativi solo nel caso in cui quello preferito non risponda, ovvero non è previsto il bilanciamento del carico tra i diversi server DNS per le query DNS. Per questo motivo, verificare di avere i server DNS elencati nell'ordine corretto per il proprio ambiente.

> [AZURE.NOTE] Se si modificano le impostazioni DNS in un file di configurazione di rete per rete virtuale che è già stato distribuito, è necessario riavviare ogni macchina virtuale affinché le modifiche vengano applicate.

### Impostazione di un server DNS nel portale di gestione

Quando si crea una rete virtuale nel portale di gestione, è possibile specificare l'indirizzo IP e il nome di uno o più server DNS da usare. Una volta creata la rete virtuale, le macchine virtuali e le istanze del ruolo distribuite nella rete virtuale vengono configurate automaticamente con le impostazioni DNS specificate. I server DNS specificati per un determinato servizio cloud (portale di Azure classico) o una scheda di interfaccia rete (distribuzioni basate su Azure Resource Manager) hanno la precedenza su quelli specificati per la rete virtuale.

### Impostazione di un server DNS usando file di configurazione (portale di Azure classico)

Per le reti virtuali classiche, è possibile specificare le impostazioni DNS usando due file di configurazione diversi: il file di *configurazione di rete* e il file di *configurazione del servizio*.

Il file di configurazione della rete descrive le reti virtuali della sottoscrizione corrente. Quando si aggiungono istanze del ruolo o VM a un servizio cloud in una rete virtuale, le impostazioni DNS del file di configurazione della rete vengono applicate a ogni istanza del ruolo o VM, a meno che non siano stati specificati server DNS specifici del servizio cloud.

Il file di configurazione del servizio viene creato per ogni servizio cloud aggiunto ad Azure. Quando si aggiungono istanze del ruolo o VM al servizio cloud, le impostazioni DNS del file di configurazione del servizio vengono applicate a ogni istanza del ruolo o VM.

> [AZURE.NOTE] I server DNS nel file di configurazione del servizio hanno la precedenza sulle impostazioni nel file di configurazione della rete.


## Passaggi successivi

[Schema di configurazione dei servizi di Azure](https://msdn.microsoft.com/library/azure/ee758710)

[Schema di configurazione di Rete virtuale](https://msdn.microsoft.com/library/azure/jj157100)

[Configurare una rete virtuale usando un file di configurazione di rete](virtual-networks-using-network-configuration-file.md)

<!---HONumber=AcomDC_0224_2016-->