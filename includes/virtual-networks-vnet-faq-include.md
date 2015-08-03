## Nozioni di base sulla rete virtuale

### Che cos'è una rete virtuale di Azure?

È possibile usare le reti virtuali per eseguire il provisioning e gestire reti virtuali private (VPN) in Azure e facoltativamente collegare le reti virtuali con altre reti virtuali in Azure o con l'infrastruttura IT locale per creare soluzioni ibride o cross-premise. Ogni rete virtuale creata ha il proprio blocco CIDR e può essere collegata ad altre reti virtuali e reti locali, purché i blocchi CIDR non entrino in conflitto. Sono disponibili anche i controlli delle impostazioni del server DNS per reti virtuali e della segmentazione della rete virtuale in subnet.

Usare le reti virtuali per:

- Creare una rete virtuale privata dedicata solo cloud
									
	Per una soluzione personalizzata, non è sempre necessaria una configurazione cross-premise. Quando si crea una rete virtuale, i servizi e le macchine virtuali all'interno della rete virtuale possono comunicare direttamente e in modo sicuro tra loro nel cloud. In questo modo il traffico viene mantenuto in modo sicuro all'interno della rete virtuale, ma è ancora possibile configurare connessioni di endpoint per le VM e i servizi che richiedono la comunicazione Internet come parte della soluzione.

- Estendere in modo sicuro il data center
									
	Con le reti virtuali è possibile creare reti virtuali private da sito a sito (S2S) tradizionali per la scalabilità sicura della capacità del data center. Le reti virtuali private S2S usano IPSEC per fornire una connessione sicura tra il gateway VPN della rete virtuale privata aziendale e Azure.

- Abilitare scenari cloud ibridi
									
	Le reti virtuali offrono la flessibilità per supportare una gamma di scenari cloud ibridi. È possibile connettere applicazioni basate sul cloud in modo sicuro a qualsiasi tipo di sistema locale, come mainframe e sistemi Unix.

### Come scoprire se è necessaria una rete virtuale?

Vedere [Panoramica sulla rete virtuale](http://go.microsoft.com/fwlink/?LinkId=296649) per visualizzare una tabella che consentirà di scegliere la migliore opzione di progettazione della rete.

### Come iniziare?

Per iniziare, vedere [la documentazione della rete virtuale](http://azure.microsoft.com/documentation/services/virtual-network/). Questa pagina include collegamenti a passaggi di configurazione comuni, nonché informazioni utili per comprendere ciò che è necessario considerare durante la progettazione della rete virtuale.

### Quali servizi è possibile usare con le reti virtuali?

Le reti virtuali possono essere usate con un'ampia gamma di servizi di Azure, come Servizi cloud (PaaS), Macchine virtuali e App Web. Esistono tuttavia alcuni servizi che non sono supportati su una rete virtuale. Controllare il servizio specifico che si vuole usare e verificare che sia compatibile.

### È possibile usare reti virtuali senza connettività cross-premise?

Sì. Si può usare una rete virtuale senza la connettività da sito a sito. Ciò è particolarmente utile se occorre eseguire controller di dominio e farm di SharePoint in Azure.

## Configurazione della rete virtuale

### Quali strumenti si usano per creare una rete virtuale?

Per creare o configurare una rete virtuale, è possibile usare gli strumenti seguenti:

- È possibile usare il portale di gestione. Vedere [Come gestire le proprietà della rete virtuale](virtual-networks-settings.md).

- È possibile usare un file di configurazione di rete (netcfg). Vedere [Configurazione di una rete virtuale tramite un file di configurazione di rete](virtual-networks-using-network-configuration-file.md).

### Quali intervalli di indirizzi è possibile usare nelle reti virtuali?

È possibile usare intervalli di indirizzi IP pubblici e qualsiasi intervallo di indirizzi IP definiti nella [RFC 1918](http://tools.ietf.org/html/rfc1918).

### È possibile avere indirizzi IP pubblici nelle reti virtuali?

Sì. Per altre informazioni sugli intervalli di indirizzi IP pubblici, vedere [Spazio degli indirizzi IP pubblici in una rete virtuale](virtual-networks-public-ip-within-vnet.md). Tenere presente che gli IP pubblici non saranno accessibili direttamente da Internet.

### Esiste un limite al numero di subnet nella rete virtuale?

Non esiste alcun limite al numero di subnet che si usano all'interno di una rete virtuale. Tutte le subnet devono essere completamente contenute nello spazio degli indirizzi della rete virtuale e non devono sovrapporsi tra loro.

### Esistono restrizioni sull'uso di indirizzi IP all'interno di tali subnet?

Azure riserva alcuni indirizzi IP all'interno di ogni subnet. Il primo e l’ultimo indirizzo IP delle subnet sono riservati per conformità al protocollo, con altri due indirizzi usati per i servizi di Azure.

### Quanto piccole o grandi possono essere le reti virtuali e le subnet?

La subnet più piccola supportata è /29 e la più grande è /8 (usando le definizioni di subnet CIDR).

### È possibile trasferire le reti VLAN in Azure usando reti virtuali?

No, le reti virtuali sono sovrapposizioni di livello 3. Azure non supporta alcuna semantica di livello 2.

### È possibile specificare criteri di routing personalizzati nelle reti virtuali e nelle subnet?

Sì. È possibile usare User Defined Routing (UDR). Per altre informazioni su UDR, vedere [Route e inoltro IP definiti dall'utente](virtual-networks-udr-overview.md).

### Le reti virtuali supportano la distribuzione multicast o broadcast?

No, la distribuzione multicast o broadcast non è supportata.

### Quali protocolli è possibile usare all'interno delle reti virtuali?

All'interno di reti virtuali è possibile usare i protocolli standard basati su IP. Tuttavia, i pacchetti incapsulati IP in IP, multicast, broadcast e i pacchetti Generic Routing Encapsulation (GRE) sono bloccati all'interno delle reti virtuali. I protocolli standard che funzionano includono:

- TCP
- UDP
- ICMP

### È possibile eseguire il ping dei router predefiniti all'interno di una rete virtuale?

No.

### È possibile usare tracert per diagnosticare la connettività?

No.

### È possibile aggiungere subnet dopo la creazione della rete virtuale?

Sì. Le subnet possono essere aggiunte alle reti virtuali in qualsiasi momento, purché l'indirizzo della subnet non faccia parte di un'altra subnet nella rete virtuale.

### È possibile modificare le dimensioni della rete virtuale dopo averla creata?

È possibile aggiungere, rimuovere, espandere o compattare una subnet se non sono presenti macchine virtuali o servizi distribuiti al suo interno utilizzando i cmdlet di PowerShell o il file NETCFG. È inoltre possibile aggiungere, rimuovere, espandere o compattare qualsiasi prefisso purché le subnet che contengono macchine virtuali o servizi non siano interessate dalla modifica.

### È possibile modificare le subnet dopo averle create?

Sì. È possibile aggiungere, rimuovere e modificare i blocchi CIDR utilizzati da una rete virtuale.

### È possibile effettuare la connessione a Internet se si eseguono i servizi in una rete virtuale?

Sì. Tutti i servizi distribuiti all'interno di una rete virtuale possono effettuare la connessione a Internet. A ciascun servizio cloud distribuito in Azure viene assegnato un indirizzo VIP indirizzabile pubblicamente. Per abilitare tali servizi in modo che accettino le connessioni da Internet, sarà necessario definire gli endpoint di input per i ruoli PaaS e gli endpoint per le macchine virtuali.

### Le reti virtuali supportano IPv6?

No, al momento non è possibile usare IPv6 con le reti virtuali.

### Una rete virtuale può estendersi a più aree?

No, una rete virtuale è limitata a una singola area.

### È possibile connettere una rete virtuale a un'altra rete virtuale in Azure?

Sì. È possibile creare reti virtuali per la comunicazione tra reti virtuali utilizzando le API REST o Windows PowerShell. Vedere [Configurazione di una connessione tra reti virtuali](virtual-networks-configure-vnet-to-vnet-connection.md).

## Risoluzione del nome (DSN)

### Quali sono le opzioni DNS per le reti virtuali?

Vedere la tabella delle decisioni nella pagina [Risoluzione dei nomi per le macchine virtuali e le istanze del ruolo](https://msdn.microsoft.com/library/azure/jj156088.aspx) come guida per tutte le opzioni DNS disponibili.

### È possibile specificare i server DNS per una rete virtuale?

Sì. È possibile specificare gli indirizzi IP del server DNS nelle impostazioni della rete virtuale. Questi verranno applicati come server DNS predefiniti per tutte le macchine virtuali nella rete virtuale.

### Quanti server DNS è possibile specificare?

È possibile specificare fino a 12 server DNS.

### È possibile modificare i server DNS dopo aver creato la rete?

Sì. È possibile modificare l'elenco dei server DNS per la rete virtuale in qualsiasi momento. Se si modifica l'elenco dei server DNS, sarà necessario riavviare tutte le macchine virtuali nella rete virtuale affinché possano selezionare il nuovo server DNS.


### Qual è il DNS fornito da Azure e funziona con le reti virtuali?

Il DNS fornito da Azure è un servizio DNS multi-tenant offerto da Microsoft. Azure registra tutte le macchine virtuali e le istanze del ruolo nel servizio. Questo servizio fornisce la risoluzione dei nomi dal nome host per le macchine virtuali e le istanze del ruolo contenute all'interno dello stesso servizio cloud e da FQDN per le macchine virtuali e le istanze del ruolo nella stessa rete virtuale.

> [AZURE.NOTE]Al momento esiste una limitazione ai primi 100 servizi cloud nella rete virtuale per la risoluzione dei nomi cross-tenant utilizzando il DNS fornito da Azure. Se si utilizza il proprio server DNS, questa limitazione non viene applicata.

### È possibile ignorare le impostazioni DNS in base alla macchina virtuale/al servizio?

Sì. È possibile impostare i server DNS in base al servizio cloud per ignorare le impostazioni di rete predefinite. Tuttavia, è consigliabile usare quanto più possibile DNS a livello di rete.

### È possibile trasferire il suffisso DNS personalizzato?

No, non è possibile specificare un suffisso DNS personalizzato per le reti virtuali.

## Reti virtuali e macchine virtuali

### È possibile distribuire le macchine virtuali su una rete virtuale?

Sì.

### È possibile distribuire macchine virtuali di Linux su una rete virtuale?

Sì. È possibile distribuire qualsiasi distributore di Linux supportato da Azure.

### Qual è la differenza tra un indirizzo VIP pubblico e un indirizzo IP interno?

- Un indirizzo IP interno è un indirizzo IP assegnato a ciascuna macchina virtuale all'interno di una rete virtuale da DHCP. Non è esposto al pubblico. Se è stata creata una rete virtuale, l'indirizzo IP interno viene assegnato dall'intervallo specificato nelle impostazioni della subnet della rete virtuale. Se non si dispone di una rete virtuale, un indirizzo IP interno verrà comunque assegnato. L'indirizzo IP interno rimarrà associato alla macchina virtuale per la relativa durata, a meno che tale macchina virtuale non venga deallocata.

- Un indirizzo VIP pubblico è l'indirizzo IP pubblico assegnato al servizio cloud o al servizio di bilanciamento del carico. Non viene assegnato direttamente alla NIC della macchina virtuale. L’indirizzo VIP rimane associato al servizio cloud a cui è assegnato finché tutte le macchine virtuali in tale servizio cloud non vengano deallocate o eliminate. A quel punto, viene rilasciato.

### Quale indirizzo IP riceverà la macchina virtuale?

- **Indirizzo IP interno**: se si distribuisce una macchina virtuale su una rete virtuale, la macchina virtuale riceve un indirizzo IP interno da un pool di indirizzi IP interni specificato. Le macchine virtuali comunicano all’interno delle reti virtuali utilizzando gli indirizzi IP interni. Sebbene Azure assegni un indirizzo IP interno dinamico, è possibile richiedere un indirizzo statico per la macchina virtuale. Per altre informazioni sugli indirizzi IP interni statici, consultare [Come impostare un indirizzo IP interno statico](virtual-networks-reserved-private-ip.md).

- **Indirizzo VIP**: la macchina virtuale è inoltre associata a un indirizzo VIP, sebbene un indirizzo VIP non venga mai assegnato direttamente alla macchina virtuale. Un indirizzo VIP è un indirizzo IP pubblico che può essere assegnato al servizio cloud. È possibile riservare un indirizzo VIP per il servizio cloud. Vedere [IP pubblico riservato](virtual-networks-reserved-public-ip.md).

- **Indirizzi ILPIP**: è inoltre possibile configurare un indirizzo IP pubblico a livello di istanza (ILPIP). Gli indirizzi ILPIP vengono associati direttamente alla macchina virtuale, anziché al servizio cloud. Per altre informazioni sugli indirizzi ILPIP, consultare [Panoramica sugli indirizzi IP pubblici a livello di istanza](virtual-networks-instance-level-public-ip.md).

### È possibile riservare un indirizzo IP interno per una macchina virtuale che verrà creata in un secondo momento?

No, non è possibile riservare un indirizzo IP interno. Se è disponibile un indirizzo IP interno verrà assegnato a una macchina virtuale o a un'istanza del ruolo dal server DHCP. Tale macchina virtuale potrebbe o non potrebbe essere l’unica a cui si vuole che l'indirizzo IP interno venga assegnato. È possibile, tuttavia, modificare l'indirizzo IP interno di una macchina virtuale già creata con qualsiasi indirizzo IP interno disponibile.

### Gli indirizzi IP interni vengono modificati per le macchine virtuali in una rete virtuale?

Sì. Gli indirizzi IP interni rimangono associati alla macchina virtuale per la relativa durata, a meno che la macchina virtuale non venga deallocata. Quando una macchina virtuale viene deallocata, l'indirizzo IP interno viene rilasciato a meno che non venga definito un indirizzo IP interno statico per la macchina virtuale. Se la macchina virtuale viene semplicemente arrestata (e non passa allo stato **Arrestato (deallocato)**) l'indirizzo IP rimarrà assegnato alla macchina virtuale.

### È possibile assegnare manualmente gli indirizzi IP alle NIC nelle macchine virtuali?

No, non è necessario modificare le proprietà dell'interfaccia delle macchine virtuali. Eventuali modifiche potrebbero provocare una potenziale perdita di connettività alla macchina virtuale.

### Cosa accade agli indirizzi IP se si arresta una macchina virtuale?

Niente. Gli indirizzi IP (sia l’indirizzo VIP pubblico sia l’indirizzo IP interno) rimarranno associati al servizio cloud o alla macchina virtuale.

> [AZURE.NOTE]Se si vuole semplicemente arrestare la macchina virtuale, non usare il portale di gestione per eseguire questa operazione. Attualmente, il pulsante di arresto deallocherà la macchina virtuale.

### È possibile spostare le macchine virtuali da una subnet a un'altra in una rete virtuale senza ripetere la distribuzione?

Sì. Altre informazioni sono disponibili [qui](virtual-networks-move-vm-role-to-subnet.md).

### È possibile configurare un indirizzo MAC statico per la macchina virtuale?

No, un indirizzo MAC non può essere configurato in modo statico.

### L'indirizzo MAC rimarrà invariato per la macchina virtuale dopo averla creata?

No, l’indirizzo MAC della macchina virtuale può cambiare per diversi motivi. Se la macchina virtuale passa allo stato Arrestato (deallocato), se si modificano le dimensioni della macchina virtuale oppure se è disponibile la correzione del servizio o la manutenzione pianificata del server host, l'indirizzo MAC non viene conservato.

### È possibile eseguire la connessione a Internet da una macchina virtuale in una rete virtuale?

Sì. Tutti i servizi distribuiti all'interno di una rete virtuale possono effettuare la connessione a Internet. Inoltre, ciascun a servizio cloud distribuito in Azure viene assegnato un indirizzo VIP indirizzabile pubblicamente. Per abilitare tali servizi in modo che accettino le connessioni da Internet, è necessario definire gli endpoint di input per i ruoli PaaS e gli endpoint per le macchine virtuali.

## Reti virtuali e servizi

### Quali servizi è possibile usare con le reti virtuali?

All’interno delle reti virtuali, è possibile usare solo servizi di calcolo. I servizi di calcolo sono limitati ai servizi cloud (ruoli web e di lavoro) e alle macchine virtuali.

### È possibile usare app Web con la rete virtuale?

No, un'app Web di Azure non può essere distribuita in una rete virtuale. Tuttavia, le app Web possono eseguire la connessione e l'accesso alle risorse in una rete virtuale di Azure in modo sicuro se si dispone di point-to-site configurati per la rete virtuale. Per altre informazioni, vedere quanto segue:

- [Integrazione della rete virtuale di app Web](http://azure.microsoft.com/blog/2014/09/15/azure-websites-virtual-network-integration/)

- [Utilizzo dell’integrazione della rete virtuale e delle connessioni ibride con app Web](http://azure.microsoft.com/blog/2014/10/30/using-vnet-or-hybrid-conn-with-websites/)

- [Integrazione di un'app Web in una rete virtuale di Azure](web-sites-integrate-with-vnet.md)


### È possibile distribuire servizi cloud con ruoli web e di lavoro (PaaS) in una rete virtuale?

Sì. È possibile distribuire servizi PaaS all’interno delle reti virtuali.

### Come è possibile distribuire ruoli PaaS su una rete virtuale?

È possibile eseguire questa operazione specificando il nome della rete virtuale e i mapping del ruolo/della subnet nella sezione di configurazione della rete della configurazione del servizio. Non è necessario aggiornare i file binari.

### È possibile spostare i servizi all’interno e all’esterno delle reti virtuali?

No, non è possibile spostare i servizi all’interno e all’esterno delle reti virtuali. Sarà necessario eliminare e ridistribuire il servizio per spostarlo in un'altra rete virtuale.

## Reti virtuali e sicurezza

### Che cos'è il modello di sicurezza per le reti virtuali?

Le reti virtuali sono completamente isolate l’una dall’altra e gli altri servizi sono ospitati nell'infrastruttura di Azure. Una rete virtuale è un limite di attendibilità.

### È possibile definire ACL o NSG sulle reti virtuali?

No, non è possibile associare ACL o NSG alle reti virtuali. Tuttavia, è possibile definire ACL sugli endpoint di input per le macchine virtuali che sono state distribuite su una rete virtuale e associare NSG a subnet o NIC.

### Esiste un white paper sulla sicurezza della rete virtuale?

Sì. È possibile scaricarlo [qui](http://go.microsoft.com/fwlink/?LinkId=386611).

## API, schemi e strumenti

### È possibile gestire le reti virtuali dal codice?

Sì. È possibile usare le API REST per gestire le reti virtuali e la connettività cross-premise. Ulteriori informazioni sono disponibili [qui](http://go.microsoft.com/fwlink/?LinkId=296833).

### È disponibile il supporto degli strumenti per le reti virtuali?

Sì. È possibile usare gli strumenti di PowerShell e della riga di comando per un'ampia gamma di piattaforme. Ulteriori informazioni sono disponibili [qui](http://go.microsoft.com/fwlink/?LinkId=317721).

<!---HONumber=July15_HO4-->