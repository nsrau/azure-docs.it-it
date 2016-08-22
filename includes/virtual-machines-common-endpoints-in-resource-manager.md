Nei modelli di distribuzione classica e Resource Manager, l'approccio agli endpoint di Azure è leggermente diverso. È ora possibile creare filtri di rete che controllano il flusso del traffico in ingresso e in uscita dalle VM e consentono così di creare ambienti di rete complessi al di là di un semplice endpoint come nel modello di distribuzione classica. Questo articolo offre una panoramica dei gruppi di sicurezza di rete, delle differenze rispetto all'uso degli endpoint del modello di distribuzione classica, della creazione di regole di filtro e di scenari di distribuzione di esempio.


## Panoramica delle distribuzioni Resource Manager
Gli endpoint del modello di distribuzione classica sono sostituiti da gruppi di sicurezza di rete e regole dell'elenco di controllo di accesso (ACL). Le azioni rapide per implementare le regole ACL dei gruppi di sicurezza di rete sono le seguenti:

- Creare un gruppo di sicurezza di rete
- Definire le regole ACL del gruppo di sicurezza di rete per consentire o impedire il traffico
- Assegnare il gruppo di sicurezza di rete a un'interfaccia di rete o una subnet di rete virtuale

Se si vuole eseguire anche il port forwarding, è necessario inserire un servizio di bilanciamento del carico a monte della VM e usare regole NAT. Le azioni rapide per implementare un servizio di bilanciamento del carico e regole NAT saranno le seguenti:

- Creare un servizio di bilanciamento del carico
- Creare un pool back-end e aggiungere le VM al pool
- Definire le regole NAT per il port forwarding necessario
- Assegnare le regole NAT alle VM


## Panoramica dei gruppi di sicurezza di rete
I gruppi di sicurezza di rete sono una nuova funzionalità che offre un livello di sicurezza con cui è possibile consentire a porte e subnet specifiche l'accesso alle VM. In genere è sempre presente un gruppo di sicurezza di rete che fornisce questo livello di sicurezza tra le VM e il mondo esterno. I gruppi di sicurezza di rete possono essere applicati a una subnet di rete virtuale o a una specifica interfaccia di rete per una VM. Invece di creare regole ACL di endpoint, si creano le regole ACL dei gruppi di sicurezza di rete. Tali regole ACL garantiscono un controllo notevolmente superiore rispetto alla semplice creazione di un endpoint per il port forwarding di una determinata porta. [Altre informazioni sui gruppi di sicurezza di rete](../articles/virtual-network/virtual-networks-nsg.md).

> [AZURE.TIP] I gruppi di sicurezza di rete possono essere assegnati a più subnet o interfacce di rete. Non esiste un mapping 1:1 ed è quindi possibile creare un gruppo di sicurezza di rete con un set comune di regole ACL e applicarlo a più subnet o interfacce di rete. Il gruppo di sicurezza di rete può anche essere applicato a risorse all'interno della sottoscrizione, con il [controllo degli accessi in base al ruolo](../articles/active-directory/role-based-access-control-what-is.md).


## Panoramica dei servizi di bilanciamento del carico
Nel modello di distribuzione classica, tutte le operazioni Network Address Translation (NAT) e di port forwarding in un servizio cloud vengono eseguite da Azure. Quando si crea un endpoint, si specifica la porta esterna da esporre con la porta interna a cui indirizzare il traffico. I gruppi di sicurezza di rete non eseguono autonomamente le stesse operazioni NAT e di port forwarding.

Per consentire la creazione delle regole NAT per il port forwarding, creare un'istanza di Azure Load Balancer nel gruppo di risorse. Il livello di granularità del servizio di bilanciamento del carico è sufficiente a consentire l'applicazione solo a VM specifiche, se necessario. Le regole NAT di Azure Load Balancer vengono usate insieme alle regole ACL del gruppo di sicurezza di rete per offrire un livello di flessibilità e controllo nettamente superiore rispetto a quello ottenibile con gli endpoint del servizio cloud. [Altre informazioni di panoramica del servizio di bilanciamento del carico](../articles/load-balancer/load-balancer-overview.md).


## Regole ACL dei gruppi di sicurezza di rete
Le regole ACL permettono di definire il traffico consentito in ingresso e in uscita dalla VM in base a porte, intervalli di porte o protocolli specifici. Le regole vengono assegnate a singole macchine virtuali o a una subnet. Lo screenshot seguente contiene un esempio delle regole ACL per un server Web comune:

![Elenco di regole ACL di un gruppo di sicurezza di rete](./media/virtual-machines-common-endpoints-in-resource-manager/example-acl-rules.png)

Le regole ACL vengono applicate in base a una metrica di priorità specificata dall'utente (più il valore è alto, minore è la priorità). Ogni gruppo di sicurezza di rete include tre regole predefinite progettate per gestire il flusso del traffico di rete di Azure, con una regola `DenyAllInbound` esplicita come regola finale. Alle regole ACL predefinite viene assegnata una priorità bassa in modo che non interferiscano con le regole create dall'utente.


## Assegnazione dei gruppi di sicurezza di rete
Un gruppo di sicurezza di rete viene assegnato a una subnet o un'interfaccia di rete. Questo approccio consente di ottenere il livello di granularità necessario quando si applicano le regole ACL solo a una specifica VM oppure di assicurarsi che un set comune di regole ACL venga applicato a tutte le VM che fanno parte di una subnet:

![Applicazione di gruppi di sicurezza di rete a interfacce di rete o subnet](./media/virtual-machines-common-endpoints-in-resource-manager/apply-nsg-to-resources.png)

Il comportamento di un gruppo di sicurezza di rete non cambia a seconda che venga assegnato a una subnet o a un'interfaccia di rete. Uno scenario di distribuzione comune prevede l'assegnazione del gruppo di sicurezza di rete a una subnet per garantire la conformità di tutte le VM collegate a tale subnet. [Altre informazioni sull'applicazione di gruppi di sicurezza di rete alle risorse](../virtual-nework/virtual-networks-nsg.md#associating-nsgs).


## Comportamento predefinito dei gruppi di sicurezza di rete
A seconda di come e quando si crea il gruppo di sicurezza di rete, possono essere create regole predefinite per consentire l'accesso RDP sulla porta TCP 3389. Le macchine virtuali Linux consentono l'accesso SSH sulla porta TCP 22. Queste regole ACL automatiche vengono create nelle condizioni seguenti:

- Se si crea una VM Windows con il portale e si accetta l'azione predefinita per la creazione di un gruppo di sicurezza di rete, viene creata una regola ACL per consentire la porta TCP 3389 (RDP).
- Se si crea una VM Linux con il portale e si accetta l'azione predefinita per la creazione di un gruppo di sicurezza di rete, viene creata una regola ACL per consentire la porta TCP 22 (SSH).

In tutte le altre condizioni, queste regole ACL predefinite non vengono create. Non sarà possibile connettersi alla VM senza creare regole ACL appropriate. Ciò includerà le azioni comuni seguenti:

- Creazione di un gruppo di sicurezza di rete con il portale come azione separata rispetto alla creazione della VM.
- Creazione di un gruppo di sicurezza di rete a livello di codice con PowerShell, l'interfaccia della riga di comando di Azure, API REST e così via.
- Creazione di una VM e relativa assegnazione a un gruppo di sicurezza di rete esistente per cui non è già definita la regola ACL appropriata.

In tutti i casi indicati, è necessario creare regole ACL per la VM per consentire le connessioni di gestione remota appropriate.


## Comportamento predefinito di una VM senza un gruppo di sicurezza di rete
È possibile creare una VM senza creare un gruppo di sicurezza di rete. In queste situazioni, è possibile connettersi alla VM con RDP o SSH senza creare regole ACL. Analogamente, se è stato installato un servizio Web sulla porta 80, tale servizio è automaticamente accessibile in remoto. Tutte le porte della VM sono aperte.

> [AZURE.NOTE] Per le connessioni remote, è comunque necessario un indirizzo IP pubblico assegnato a una VM. L'assenza di un gruppo di sicurezza di rete per la subnet o l'interfaccia di rete non espone la VM a tutto il traffico esterno. Quando si crea una VM con il portale, l'azione predefinita prevede la creazione di un nuovo IP pubblico. In tutte le altre forme di creazione di una VM, come PowerShell, l'interfaccia della riga di comando di Azure o un modello di Resource Manager, non viene creato automaticamente un IP pubblico se non esplicitamente richiesto. L'azione predefinita con il portale prevede anche la creazione di un gruppo di sicurezza di rete e non si dovrebbe quindi verificare la situazione di una VM esposta senza filtri di rete.


## Informazioni su servizi di bilanciamento del carico e regole NAT
Nel modello di distribuzione classica, è possibile creare endpoint che eseguono anche il port forwarding. Quando si crea una macchina virtuale nel modello di distribuzione classica, le regole ACL per RDP o SSH vengono create automaticamente. La porta TCP 3389 o la porta TCP 22 rispettivamente non vengono esposte all'esterno. Viene invece esposta una porta TCP di valore elevato con mapping alla porta interna appropriata. È anche possibile creare regole ACL personalizzate in modo analogo, ad esempio esporre un server Web al mondo esterno sulla porta TCP 4280. Lo screenshot seguente del portale classico illustra queste regole ACL e i mapping delle porte:

![Port forwarding con endpoint del modello di distribuzione classica](./media/virtual-machines-common-endpoints-in-resource-manager/classic-endpoints-port-forwarding.png)

Con i gruppi di sicurezza di rete, la funzione di port forwarding è gestita da un servizio di bilanciamento del carico. [Altre informazioni sui servizi di bilanciamento del carico in Azure](../articles/load-balancer/load-balancer-overview.md). Lo screenshot seguente del portale illustra un esempio di un servizio di bilanciamento del carico con una regola NAT per il port forwarding della porta TCP 4222 alla porta TCP 22 interna di una VM:

![Regole NAT del servizio di bilanciamento del carico per il port forwarding](./media/virtual-machines-common-endpoints-in-resource-manager/load-balancer-nat-rules.png)  

> [AZURE.NOTE] Quando si implementa un servizio di bilanciamento del carico, in genere non si assegna un indirizzo IP pubblico alla VM. Al servizio di bilanciamento del carico viene invece assegnato un indirizzo IP pubblico. È comunque necessario creare un gruppo di sicurezza di rete e regole ACL per definire il flusso del traffico in ingresso e in uscita dalla VM. Le regole NAT del servizio di bilanciamento del carico hanno semplicemente lo scopo di definire le porte consentite tramite il servizio di bilanciamento del carico e come vengono distribuite tra le VM back-end. Di conseguenza, è necessario creare una regola NAT per consentire il flusso del traffico attraverso il servizio di bilanciamento del carico e quindi una regola ACL del gruppo di sicurezza di rete per consentire al traffico di raggiungere effettivamente la VM.

<!---HONumber=AcomDC_0810_2016-->