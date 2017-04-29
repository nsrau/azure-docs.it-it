## <a name="virtual-network-basics"></a>Nozioni di base sulla rete virtuale
### <a name="what-is-an-azure-virtual-network-vnet"></a>Che cos'è una rete virtuale di Azure?
È possibile usare le reti virtuali per eseguire il provisioning e gestire reti virtuali private (VPN) in Azure e facoltativamente collegare le reti virtuali con altre reti virtuali in Azure o con l'infrastruttura IT locale per creare soluzioni ibride o cross-premise. Ogni rete virtuale creata ha il proprio blocco CIDR e può essere collegata ad altre reti virtuali e reti locali, purché i blocchi CIDR non entrino in conflitto. Sono disponibili anche i controlli delle impostazioni del server DNS per reti virtuali e della segmentazione della rete virtuale in subnet.

Usare le reti virtuali per:

* Creare una rete virtuale privata dedicata solo cloud
  
    Per una soluzione personalizzata, non è sempre necessaria una configurazione cross-premise. Quando si crea una rete virtuale, i servizi e le macchine virtuali all'interno della rete virtuale possono comunicare direttamente e in modo sicuro tra loro nel cloud. In questo modo il traffico viene mantenuto in modo sicuro all'interno della rete virtuale, ma è ancora possibile configurare connessioni di endpoint per le VM e i servizi che richiedono la comunicazione Internet come parte della soluzione.
* Estendere in modo sicuro il data center
  
    Con le reti virtuali è possibile creare reti virtuali private da sito a sito (S2S) tradizionali per la scalabilità sicura della capacità del data center. Le reti virtuali private S2S usano IPSEC per fornire una connessione sicura tra il gateway VPN della rete virtuale privata aziendale e Azure.
* Abilitare scenari cloud ibridi
  
    Le reti virtuali offrono la flessibilità per supportare una gamma di scenari cloud ibridi. È possibile connettere applicazioni basate sul cloud in modo sicuro a qualsiasi tipo di sistema locale, come mainframe e sistemi Unix.

### <a name="how-do-i-know-if-i-need-a-virtual-network"></a>Come scoprire se è necessaria una rete virtuale?
Per una tabella che consenta di scegliere la migliore opzione di progettazione della rete, vedere [Panoramica sulla rete virtuale](../articles/virtual-network/virtual-networks-overview.md) .

### <a name="how-do-i-get-started"></a>Come iniziare?
Per iniziare, vedere [la documentazione della rete virtuale](https://azure.microsoft.com/documentation/services/virtual-network/) . Questa pagina include collegamenti a passaggi di configurazione comuni, nonché informazioni utili per comprendere ciò che è necessario considerare durante la progettazione della rete virtuale.

### <a name="what-services-can-i-use-with-vnets"></a>Quali servizi è possibile usare con le reti virtuali?
Le reti virtuali possono essere usate con un'ampia gamma di servizi di Azure, come Servizi cloud (PaaS), Macchine virtuali e App Web. Esistono tuttavia alcuni servizi che non sono supportati su una rete virtuale. Controllare il servizio specifico che si vuole usare e verificare che sia compatibile.

### <a name="can-i-use-vnets-without-cross-premises-connectivity"></a>È possibile usare reti virtuali senza connettività cross-premise?
Sì. Si può usare una rete virtuale senza la connettività da sito a sito. Ciò è particolarmente utile se occorre eseguire controller di dominio e farm di SharePoint in Azure.

## <a name="virtual-network-configuration"></a>Configurazione della rete virtuale
### <a name="what-tools-do-i-use-to-create-a-vnet"></a>Quali strumenti si usano per creare una rete virtuale?
Per creare o configurare una rete virtuale, è possibile usare gli strumenti seguenti:

* Portale di Azure (per reti virtuali classiche e di Resource Manager).
* Un file di configurazione di rete (netcfg - solo per reti virtuali classiche). Vedere [Configurazione di una rete virtuale tramite un file di configurazione di rete](../articles/virtual-network/virtual-networks-using-network-configuration-file.md).
* PowerShell (per reti virtuali classiche e di Resource Manager).
* Interfaccia della riga di comando di Azure (per reti virtuali classiche e di Resource Manager).

### <a name="what-address-ranges-can-i-use-in-my-vnets"></a>Quali intervalli di indirizzi è possibile usare nelle reti virtuali?
È possibile usare intervalli di indirizzi IP pubblici e qualsiasi intervallo di indirizzi IP definiti nella [RFC 1918](http://tools.ietf.org/html/rfc1918).

### <a name="can-i-have-public-ip-addresses-in-my-vnets"></a>È possibile avere indirizzi IP pubblici nelle reti virtuali?
Sì. Per altre informazioni sugli intervalli di indirizzi IP pubblici, vedere [Spazio degli indirizzi IP pubblici in una rete virtuale](../articles/virtual-network/virtual-networks-public-ip-within-vnet.md). Tenere presente che gli IP pubblici non saranno accessibili direttamente da Internet.

### <a name="is-there-a-limit-to-the-number-of-subnets-in-my-virtual-network"></a>Esiste un limite al numero di subnet nella rete virtuale?
Non esiste alcun limite al numero di subnet che si usano all'interno di una rete virtuale. Tutte le subnet devono essere completamente contenute nello spazio degli indirizzi della rete virtuale e non devono sovrapporsi tra loro.

### <a name="are-there-any-restrictions-on-using-ip-addresses-within-these-subnets"></a>Esistono restrizioni sull'uso di indirizzi IP all'interno di tali subnet?
Azure riserva alcuni indirizzi IP all'interno di ogni subnet. Il primo e l’ultimo indirizzo IP delle subnet sono riservati per conformità al protocollo, con altri 3 indirizzi usati per i servizi di Azure.

### <a name="how-small-and-how-large-can-vnets-and-subnets-be"></a>Quanto piccole o grandi possono essere le reti virtuali e le subnet?
La subnet più piccola supportata è /29 e la più grande è /8 (usando le definizioni di subnet CIDR).

### <a name="can-i-bring-my-vlans-to-azure-using-vnets"></a>È possibile trasferire le reti VLAN in Azure usando reti virtuali?
No. Le reti virtuali sono sovrapposizioni di livello 3. Azure non supporta alcuna semantica di livello 2.

### <a name="can-i-specify-custom-routing-policies-on-my-vnets-and-subnets"></a>È possibile specificare criteri di routing personalizzati nelle reti virtuali e nelle subnet?
Sì. È possibile usare User Defined Routing (UDR). Per altre informazioni su UDR, vedere [Route e inoltro IP definiti dall'utente](../articles/virtual-network/virtual-networks-udr-overview.md).

### <a name="do-vnets-support-multicast-or-broadcast"></a>Le reti virtuali supportano la distribuzione multicast o broadcast?
No. La distribuzione multicast o broadcast non è supportata.

### <a name="what-protocols-can-i-use-within-vnets"></a>Quali protocolli è possibile usare all'interno delle reti virtuali?
All'interno di reti virtuali è possibile usare i protocolli standard basati su IP. Tuttavia, i pacchetti incapsulati IP in IP, multicast, broadcast e i pacchetti Generic Routing Encapsulation (GRE) sono bloccati all'interno delle reti virtuali. I protocolli standard che funzionano includono:

* TCP
* UDP
* ICMP

### <a name="can-i-ping-my-default-routers-within-a-vnet"></a>È possibile eseguire il ping dei router predefiniti all'interno di una rete virtuale?
No.

### <a name="can-i-use-tracert-to-diagnose-connectivity"></a>È possibile usare tracert per diagnosticare la connettività?
No.

### <a name="can-i-add-subnets-after-the-vnet-is-created"></a>È possibile aggiungere subnet dopo la creazione della rete virtuale?
Sì. Le subnet possono essere aggiunte alle reti virtuali in qualsiasi momento, purché l'indirizzo della subnet non faccia parte di un'altra subnet nella rete virtuale.

### <a name="can-i-modify-the-size-of-my-subnet-after-i-create-it"></a>È possibile modificare le dimensioni della subnet dopo averla creata?
È possibile aggiungere, rimuovere, espandere o compattare una subnet se non sono presenti macchine virtuali o servizi distribuiti al suo interno utilizzando i cmdlet di PowerShell o il file NETCFG. È inoltre possibile aggiungere, rimuovere, espandere o compattare qualsiasi prefisso purché le subnet che contengono macchine virtuali o servizi non siano interessate dalla modifica.

### <a name="can-i-modify-subnets-after-i-created-them"></a>È possibile modificare le subnet dopo averle create?
Sì. È possibile aggiungere, rimuovere e modificare i blocchi CIDR utilizzati da una rete virtuale.

### <a name="can-i-connect-to-the-internet-if-i-am-running-my-services-in-a-vnet"></a>È possibile effettuare la connessione a Internet se si eseguono i servizi in una rete virtuale?
Sì. Tutti i servizi distribuiti all'interno di una rete virtuale possono effettuare la connessione a Internet. A ciascun servizio cloud distribuito in Azure viene assegnato un indirizzo VIP indirizzabile pubblicamente. Per abilitare tali servizi in modo che accettino le connessioni da Internet, sarà necessario definire gli endpoint di input per i ruoli PaaS e gli endpoint per le macchine virtuali.

### <a name="do-vnets-support-ipv6"></a>Le reti virtuali supportano IPv6?
No. Al momento non è possibile usare IPv6 con le reti virtuali.

### <a name="can-a-vnet-span-regions"></a>Una rete virtuale può estendersi a più aree?
No. Una rete virtuale è limitata a una singola area.

### <a name="can-i-connect-a-vnet-to-another-vnet-in-azure"></a>È possibile connettere una rete virtuale a un'altra rete virtuale in Azure?
Sì. È possibile creare reti virtuali per la comunicazione tra reti virtuali utilizzando le API REST o Windows PowerShell. È inoltre possibile connettere le reti virtuali tramite il peering della rete virtuale. Per altre informazioni dettagliate sul peering, consultare [questa pagina.](../articles/virtual-network/virtual-network-peering-overview.md)

## <a name="name-resolution-dns"></a>Risoluzione del nome (DNS)
### <a name="what-are-my-dns-options-for-vnets"></a>Quali sono le opzioni DNS per le reti virtuali?
Usare la tabella delle decisioni nella pagina [Risoluzione dei nomi per le VM e le istanze del ruolo](../articles/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md) come guida per tutte le opzioni DNS disponibili.

### <a name="can-i-specify-dns-servers-for-a-vnet"></a>È possibile specificare i server DNS per una rete virtuale?
Sì. È possibile specificare gli indirizzi IP del server DNS nelle impostazioni della rete virtuale. Questi verranno applicati come server DNS predefiniti per tutte le macchine virtuali nella rete virtuale.

### <a name="how-many-dns-servers-can-i-specify"></a>Quanti server DNS è possibile specificare?
È possibile specificare fino a 12 server DNS.

### <a name="can-i-modify-my-dns-servers-after-i-have-created-the-network"></a>È possibile modificare i server DNS dopo aver creato la rete?
Sì. È possibile modificare l'elenco dei server DNS per la rete virtuale in qualsiasi momento. Se si modifica l'elenco dei server DNS, sarà necessario riavviare tutte le macchine virtuali nella rete virtuale affinché possano selezionare il nuovo server DNS.

### <a name="what-is-azure-provided-dns-and-does-it-work-with-vnets"></a>Qual è il DNS fornito da Azure e funziona con le reti virtuali?
Il DNS fornito da Azure è un servizio DNS multi-tenant offerto da Microsoft. Azure registra tutte le macchine virtuali e le istanze del ruolo nel servizio. Questo servizio fornisce la risoluzione dei nomi dal nome host per le macchine virtuali e le istanze del ruolo contenute all'interno dello stesso servizio cloud e da FQDN per le macchine virtuali e le istanze del ruolo nella stessa rete virtuale.

> [!NOTE]
> Al momento esiste una limitazione ai primi 100 servizi cloud nella rete virtuale per la risoluzione dei nomi cross-tenant utilizzando il DNS fornito da Azure. Se si utilizza il proprio server DNS, questa limitazione non viene applicata.
> 
> 

### <a name="can-i-override-my-dns-settings-on-a-per-vm--service-basis"></a>È possibile ignorare le impostazioni DNS in base alla macchina virtuale/al servizio?
Sì. È possibile impostare i server DNS in base al servizio cloud per ignorare le impostazioni di rete predefinite. Tuttavia, è consigliabile usare quanto più possibile DNS a livello di rete.

### <a name="can-i-bring-my-own-dns-suffix"></a>È possibile trasferire il suffisso DNS personalizzato?
No. Non è possibile specificare un suffisso DNS personalizzato per le reti virtuali.

## <a name="vnets-and-vms"></a>Reti virtuali e macchine virtuali
### <a name="can-i-deploy-vms-to-a-vnet"></a>È possibile distribuire le macchine virtuali su una rete virtuale?
Sì.

### <a name="can-i-deploy-linux-vms-to-a-vnet"></a>È possibile distribuire macchine virtuali di Linux su una rete virtuale?
Sì. È possibile distribuire qualsiasi distributore di Linux supportato da Azure.

### <a name="what-is-the-difference-between-a-public-vip-and-an-internal-ip-address"></a>Qual è la differenza tra un indirizzo VIP pubblico e un indirizzo IP interno?
* Un indirizzo IP interno è un indirizzo IP assegnato a ciascuna macchina virtuale all'interno di una rete virtuale da DHCP. Non è esposto al pubblico. Se è stata creata una rete virtuale, l'indirizzo IP interno viene assegnato dall'intervallo specificato nelle impostazioni della subnet della rete virtuale. Se non si dispone di una rete virtuale, un indirizzo IP interno verrà comunque assegnato. L'indirizzo IP interno rimarrà associato alla macchina virtuale per la relativa durata, a meno che tale macchina virtuale non venga deallocata.
* Un indirizzo VIP pubblico è l'indirizzo IP pubblico assegnato al servizio cloud o al servizio di bilanciamento del carico. Non viene assegnato direttamente alla NIC della macchina virtuale. L’indirizzo VIP rimane associato al servizio cloud a cui è assegnato finché tutte le macchine virtuali in tale servizio cloud non vengano deallocate o eliminate. A quel punto, viene rilasciato.

### <a name="what-ip-address-will-my-vm-receive"></a>Quale indirizzo IP riceverà la macchina virtuale?
* **Indirizzo IP interno** : se si distribuisce una macchina virtuale su una rete virtuale, la macchina virtuale riceve un indirizzo IP interno da un pool di indirizzi IP interni specificato. Le macchine virtuali comunicano all’interno delle reti virtuali utilizzando gli indirizzi IP interni. Sebbene Azure assegni un indirizzo IP interno dinamico, è possibile richiedere un indirizzo statico per la macchina virtuale. Per ulteriori informazioni sugli indirizzi IP interni statici, consultare [Come impostare un indirizzo IP interno statico](../articles/virtual-network/virtual-networks-reserved-private-ip.md).
* **Indirizzo VIP** : la macchina virtuale è inoltre associata a un indirizzo VIP, sebbene un indirizzo VIP non venga mai assegnato direttamente alla macchina virtuale. Un indirizzo VIP è un indirizzo IP pubblico che può essere assegnato al servizio cloud. È possibile riservare un indirizzo VIP per il servizio cloud.
* **Indirizzi ILPIP** : è inoltre possibile configurare un indirizzo IP pubblico a livello di istanza (ILPIP). Gli indirizzi ILPIP vengono associati direttamente alla macchina virtuale, anziché al servizio cloud. Per altre informazioni sugli indirizzi ILPIP, consultare [Panoramica sugli indirizzi IP pubblici a livello di istanza](../articles/virtual-network/virtual-networks-instance-level-public-ip.md).

### <a name="can-i-reserve-an-internal-ip-address-for-a-vm-that-i-will-create-at-a-later-time"></a>È possibile riservare un indirizzo IP interno per una macchina virtuale che verrà creata in un secondo momento?
No. Non è possibile riservare un indirizzo IP interno. Se è disponibile un indirizzo IP interno, quest'ultimo verrà assegnato a una VM o a un'istanza del ruolo dal server DHCP. Tale macchina virtuale potrebbe o non potrebbe essere l’unica a cui si vuole che l'indirizzo IP interno venga assegnato. È possibile, tuttavia, modificare l'indirizzo IP interno di una macchina virtuale già creata con qualsiasi indirizzo IP interno disponibile.

### <a name="do-internal-ip-addresses-change-for-vms-in-a-vnet"></a>Gli indirizzi IP interni vengono modificati per le macchine virtuali in una rete virtuale?
Sì. Gli indirizzi IP interni rimangono associati alla macchina virtuale per la relativa durata, a meno che la macchina virtuale non venga deallocata. Quando una macchina virtuale viene deallocata, l'indirizzo IP interno viene rilasciato a meno che non venga definito un indirizzo IP interno statico per la macchina virtuale. Se la macchina virtuale viene semplicemente arrestata (e non passa allo stato **Arrestato (deallocato)**) l'indirizzo IP rimarrà assegnato alla macchina virtuale.

### <a name="can-i-manually-assign-ip-addresses-to-nics-in-vms"></a>È possibile assegnare manualmente gli indirizzi IP alle NIC nelle macchine virtuali?
No. È necessario modificare le proprietà dell'interfaccia delle macchine virtuali. Eventuali modifiche potrebbero provocare una potenziale perdita di connettività alla macchina virtuale.

### <a name="what-happens-to-my-ip-addresses-if-i-shut-down-a-vm"></a>Cosa accade agli indirizzi IP se si arresta una macchina virtuale?
Niente. Gli indirizzi IP (sia l’indirizzo VIP pubblico sia l’indirizzo IP interno) rimarranno associati al servizio cloud o alla macchina virtuale.

> [!NOTE]
> Se si vuole semplicemente arrestare la macchina virtuale, non usare il portale di gestione per eseguire questa operazione. Attualmente, il pulsante di arresto deallocherà la macchina virtuale.
> 
> 

### <a name="can-i-move-vms-from-one-subnet-to-another-subnet-in-a-vnet-without-re-deploying"></a>È possibile spostare le macchine virtuali da una subnet a un'altra in una rete virtuale senza ripetere la distribuzione?
Sì. Altre informazioni sono disponibili [qui](../articles/virtual-network/virtual-networks-move-vm-role-to-subnet.md).

### <a name="can-i-configure-a-static-mac-address-for-my-vm"></a>È possibile configurare un indirizzo MAC statico per la macchina virtuale?
No. Un indirizzo MAC non può essere configurato in modo statico.

### <a name="will-the-mac-address-remain-the-same-for-my-vm-once-it-has-been-created"></a>L'indirizzo MAC rimarrà invariato per la macchina virtuale dopo averla creata?
Sì, l'indirizzo MAC rimarrà lo stesso per una macchina virtuale anche se questa è stata arrestata (deallocata) e riavviata.

### <a name="can-i-connect-to-the-internet-from-a-vm-in-a-vnet"></a>È possibile eseguire la connessione a Internet da una macchina virtuale in una rete virtuale?
Sì. Tutti i servizi distribuiti all'interno di una rete virtuale possono effettuare la connessione a Internet. Inoltre, ciascun a servizio cloud distribuito in Azure viene assegnato un indirizzo VIP indirizzabile pubblicamente. Per abilitare tali servizi in modo che accettino le connessioni da Internet, è necessario definire gli endpoint di input per i ruoli PaaS e gli endpoint per le macchine virtuali.

## <a name="vnets-and-services"></a>Reti virtuali e servizi
### <a name="what-services-can-i-use-with-vnets"></a>Quali servizi è possibile usare con le reti virtuali?
All’interno delle reti virtuali, è possibile usare solo servizi di calcolo. I servizi di calcolo sono limitati ai servizi cloud (ruoli web e di lavoro) e alle macchine virtuali.

### <a name="can-i-use-web-apps-with-virtual-network"></a>È possibile usare app Web con la rete virtuale?
Sì. È possibile distribuire App Web all'interno di una rete virtuale utilizzando Esplora archivi Azure (Ambiente del servizio app). Oltre a questo, le app Web possono eseguire la connessione e l'accesso alle risorse in una rete virtuale di Azure in modo sicuro se si dispone di point-to-site configurati per la rete virtuale. Per altre informazioni, vedere quanto segue:

* [Creare app Web in un ambiente del servizio app](../articles/app-service-web/app-service-web-how-to-create-a-web-app-in-an-ase.md)
* [Integrazione della rete virtuale di app Web](https://azure.microsoft.com/blog/2014/09/15/azure-websites-virtual-network-integration/)
* [Utilizzo dell’integrazione della rete virtuale e delle connessioni ibride con app Web](https://azure.microsoft.com/blog/2014/10/30/using-vnet-or-hybrid-conn-with-websites/)
* [Integrazione di un'app Web in una rete virtuale di Azure](../articles/app-service-web/web-sites-integrate-with-vnet.md)

### <a name="can-i-deploy-cloud-services-with-web-and-worker-roles-paas-in-a-vnet"></a>È possibile distribuire servizi cloud con ruoli web e di lavoro (PaaS) in una rete virtuale?
Sì. È possibile distribuire servizi PaaS all’interno delle reti virtuali.

### <a name="how-do-i-deploy-paas-roles-to-a-vnet"></a>Come è possibile distribuire ruoli PaaS su una rete virtuale?
È possibile eseguire questa operazione specificando il nome della rete virtuale e i mapping del ruolo/della subnet nella sezione di configurazione della rete della configurazione del servizio. Non è necessario aggiornare i file binari.

### <a name="can-i-move-my-services-in-and-out-of-vnets"></a>È possibile spostare i servizi all’interno e all’esterno delle reti virtuali?
No. Non è possibile spostare i servizi all'interno e all'esterno delle reti virtuali. Sarà necessario eliminare e ridistribuire il servizio per spostarlo in un'altra rete virtuale.

## <a name="vnets-and-security"></a>Reti virtuali e sicurezza
### <a name="what-is-the-security-model-for-vnets"></a>Che cos'è il modello di sicurezza per le reti virtuali?
Le reti virtuali sono completamente isolate l’una dall’altra e gli altri servizi sono ospitati nell'infrastruttura di Azure. Una rete virtuale è un limite di attendibilità.

### <a name="can-i-define-acls-or-nsgs-on-my-vnets"></a>È possibile definire elenchi di controllo di accesso o gruppi di sicurezza di rete sulle reti virtuali?
No. Non è possibile associare gli elenchi di controllo di accesso o i gruppi di sicurezza di rete alle reti virtuali. Tuttavia, è possibile definire elenchi di controllo di accesso sugli endpoint di input per le macchine virtuali distribuite su una rete virtuale e associare i gruppi di sicurezza di rete a subnet o schede di interfaccia di rete.

### <a name="is-there-a-vnet-security-whitepaper"></a>Esiste un white paper sulla sicurezza della rete virtuale?
Sì. È possibile scaricarlo [qui](http://go.microsoft.com/fwlink/?LinkId=386611).

## <a name="apis-schemas-and-tools"></a>API, schemi e strumenti
### <a name="can-i-manage-vnets-from-code"></a>È possibile gestire le reti virtuali dal codice?
Sì. È possibile usare le API REST per gestire le reti virtuali e la connettività cross-premise. Ulteriori informazioni sono disponibili [qui](http://go.microsoft.com/fwlink/?LinkId=296833).

### <a name="is-there-tooling-support-for-vnets"></a>È disponibile il supporto degli strumenti per le reti virtuali?
Sì. È possibile usare gli strumenti di PowerShell e della riga di comando per un'ampia gamma di piattaforme. Ulteriori informazioni sono disponibili [qui](http://go.microsoft.com/fwlink/?LinkId=317721).

