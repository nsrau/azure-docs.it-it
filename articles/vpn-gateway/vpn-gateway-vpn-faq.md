<properties 
   pageTitle="Domande frequenti sul gateway VPN di Rete virtuale | Microsoft Azure"
   description="Domande frequenti sul gateway VPN. Domande frequenti relative alle connessioni cross-premise di Rete virtuale di Microsoft Azure, alle connessioni con configurazioni ibride e ai gateway VPN"
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/10/2016"
   ms.author="cherylmc" />

# Domande frequenti sul gateway VPN

## Connessione a reti virtuali

### È possibile connettere reti virtuali in diverse aree di Azure?
Sì. Non esistono vincoli di area. Una rete virtuale può connettersi a un'altra rete virtuale nella stessa area o in un'area diversa di Azure.

### È possibile connettere reti virtuali in diverse sottoscrizioni?
Sì.

### È possibile connettersi a più siti da una singola rete virtuale?

È possibile connettersi a più siti tramite Windows PowerShell e le API REST di Azure. Vedere la sezione delle domande frequenti relative alla [connettività multisito e tra reti virtuali](#multi-site-and-vnet-to-vnet-connectivity).
## Quali sono le opzioni di connessione cross-premise?

Le seguenti connessioni cross-premise sono supportate:

- [Site-to-Site](vpn-gateway-site-to-site-create.md): connessione VPN tramite IPsec (IKE v1 e IKE v2). Per questo tipo di connessione è necessario un dispositivo VPN o RRAS.

- [Point-to-Site](vpn-gateway-point-to-site-create.md): connessione VPN tramite SSTP (Secure Sockets Tunneling Protocol). Questa connessione non richiede un dispositivo VPN.

- [Da rete virtuale a rete virtuale](virtual-networks-configure-vnet-to-vnet-connection.md): questo tipo di connessione è analogo alla configurazione d sito a sito. La connessione tra reti virtuali è una connessione VPN tramite IPsec (IKE v1 e IKE v2). Non richiede un dispositivo VPN.

- [Multisito](vpn-gateway-multi-site.md): si tratta di una variante di una configurazione da sito a sito che consente di connettere più siti locali a una rete virtuale.

- [ExpressRoute](../expressroute/expressroute-introduction.md) - ExpressRoute è una connessione diretta ad Azure dalla rate WAN, non sulla rete Internet pubblica. Per altre informazioni, vedere [Panoramica tecnica relativa a ExpressRoute](../expressroute/expressroute-introduction.md) e [Domande frequenti su ExpressRoute](../expressroute/expressroute-faqs.md).

Per altre informazioni sulle connessioni cross-premise, vedere [Informazioni sulla connettività cross-premise protetta](vpn-gateway-cross-premises-options.md).

### Qual è la differenza tra una connessione da sito a sito e una connessione da punto a sito?

Le connessioni **Site-to-Site** consentono di connettere qualsiasi computer locale a qualsiasi macchina virtuale o istanza del ruolo all'interno della rete virtuale, a seconda della configurazione di routing. Questa opzione è ottimale per una connessione cross-premise sempre disponibile ed è adatta per le configurazioni ibride. Questo tipo di connessione si basa su un dispositivo VPN IPSec (dispositivo hardware o software) che è necessario distribuire in corrispondenza del perimetro della rete. Per creare questo tipo di connessione, è necessario disporre dell'hardware VPN richiesto e di un indirizzo IP IPv4 accessibile pubblicamente.

Le connessioni **Point-to-Site** consentono di connettere un singolo computer a qualsiasi elemento all'interno della rete virtuale. Questo tipo di connessione usa il client VPN incorporato di Windows. La configurazione da spunto a sito prevede l'installazione di un certificato e di un pacchetto di configurazione client VPN che contiene le impostazioni che consentono al computer di connettersi a qualsiasi macchina virtuale o istanza del ruolo all'interno della rete virtuale. Si tratta di un'ottima opzione quando si desidera connettersi a una rete virtuale ma non ci si trova nella sede locale. È utile anche quando non si ha accesso all'hardware VPN o a un indirizzo IP IPv4 accessibile pubblicamente, entrambi necessari per una connessione da sito a sito.

È possibile configurare la rete virtuale in modo da usare sia la connettività da punto a sito che da sito a sito contemporaneamente, purché la connessione da sito a sito venga creata usando un tipo di VPN basato su route per il gateway. I tipi di VPN basati su route sono detti gateway dinamici nel modello di distribuzione classica.

Per altre informazioni, vedere [Informazioni sulla connettività cross-premise protetta per reti virtuali](vpn-gateway-cross-premises-options.md).

### Che cos'è ExpressRoute?

ExpressRoute permette di creare connessioni private tra i data center Microsoft e l'infrastruttura disponibile localmente o in un ambiente con percorso condiviso. Con ExpressRoute è possibile stabilire connessioni a servizi cloud Microsoft, ad esempio Microsoft Azure e Office 365 in una struttura di partner ExpressRoute con percorso condiviso, oppure connettersi direttamente dalla rete WAN esistente, ad esempio una VPN MPLS fornita da un provider di servizi di rete.

Le connessioni ExpressRoute offrono un livello di sicurezza migliore, maggiore affidabilità, velocità più elevate e latenze più basse rispetto alle connessioni Internet tradizionali. In alcuni casi, l'uso di connessioni ExpressRoute per trasferire i dati tra la rete locale e Azure può offrire vantaggi significativi in termini di costi. Se già stata creata una connessione cross-premise dalla rete locale ad Azure, è possibile eseguire la migrazione a una connessione ExpressRoute, mantenendo intatta la rete virtuale.

Per altre informazioni, vedere le [Domande frequenti su ExpressRoute](../expressroute/expressroute-faqs.md).

## Connessioni Site-to-Site e dispositivi VPN

### Quali sono gli aspetti di cui tenere conto nella scelta di un dispositivo VPN?

È stato convalidato un set di dispositivi VPN da sito a sito standard in collaborazione con fornitori di dispositivi. Per un elenco dei dispositivi VPN sicuramente compatibili, per gli esempi o le istruzioni di configurazione corrispondenti e le relative specifiche, fare clic [qui](vpn-gateway-about-vpn-devices.md). Tutti i dispositivi appartenenti ai gruppi di dispositivi di cui è indicata la compatibilità nota dovrebbero funzionare con Rete virtuale. Per agevolare la configurazione del dispositivo VPN, fare riferimento al collegamento o all'esempio di configurazione del dispositivo corrispondente al gruppo di dispositivi appropriato.

### Quali operazioni è opportuno eseguire se si possiede un dispositivo VPN non incluso nell'elenco di dispositivi con compatibilità nota?

Se un dispositivo non è elencato tra i dispositivi VPN con compatibilità nota e si desidera usarlo per la connessione VPN, è necessario verificare che soddisfi i parametri e le opzioni di configurazione IPsec/IKE elencati [qui](vpn-gateway-about-vpn-devices.md#devices-not-on-the-compatible-list). I dispositivi che soddisfano i requisiti minimi dovrebbero funzionare correttamente con i gateway VPN. Contattare il produttore del dispositivo per assistenza e istruzioni di configurazione.

### Perché il tunnel VPN basato su criteri si arresta quando il traffico è inattivo?

Questo comportamento è previsto per gateway VPN basate su criteri (anche note come routing statico). Quando il traffico attraverso il tunnel è inattivo per più di 5 minuti, il tunnel verrà arrestato. Ma non appena il traffico inizierà a scorrere in entrambe le direzioni, il tunnel verrà ripristinato immediatamente. Se si dispone di un gateway VPN basato su route (noto anche come dinamico), questo comportamento non si verificherà.

### È possibile usare soluzioni software VPN per connettersi ad Azure?

Sono supportati server RRAS (Routing e Accesso remoto) in Windows Server 2012 per la configurazione da sito a sito cross-premise.

Con il gateway dovrebbero funzionare anche altre soluzioni software VPN, purché siano conformi alle implementazioni di IPSec standard del settore. Per istruzioni sulla configurazione e sull'assistenza, contattare il fornitore del software.

## Connessioni Point-to-Site

### Quali sistemi operativi è possibile usare con la connettività da punto a sito?

Sono supportati i sistemi operativi seguenti:

- Windows 7 (solo versione a 64 bit)

- Windows Server 2008 R2

- Windows 8 (solo versione a 64 bit)

- Windows Server 2012

- Windows 10

### Per la connettività da punto a sito è possibile usare qualsiasi client VPN software che supporta SSTP?

No. L'assistenza è limitata solo alle versioni dei sistemi operativi Windows elencati in precedenza.

### Quanti endpoint client VPN è possibile includere nella configurazione da punto sito?

Sono supportati fino a 128 client VPN da poter connettere contemporaneamente a una rete virtuale.

### È possibile usare la CA radice della PKI interna per la connettività da punto a sito?

Sì. In precedenza, era possibile utilizzare solo certificati radice autofirmati. È ancora possibile caricare 20 certificati radice.

### È possibile attraversare proxy e firewall con la funzionalità Da punto a sito

Sì. Viene usato SSTP (Secure Sockets Tunneling Protocol) per effettuare il tunneling tramite firewall. Questo tunnel verrà visualizzato come connessione HTTPs.

### Se si riavvia un computer client configurato per la funzionalità Da punto a sito, la VPN verrà riconnessa automaticamente?

Per impostazione predefinita, tramite il computer client non verrà ristabilita automaticamente la connessione VPN.

### La funzionalità Da punto a sito supporta la riconnessione automatica e il DNS dinamico nei client VPN?

La riconnessione automatica e il DNS dinamico non sono supportati attualmente nelle VPN da punto a sito.

### È possibile la coesistenza di configurazioni da sito a sito e punto a sito per la stessa rete virtuale?

Sì. Entrambe queste soluzioni funzionano se si ha un tipo di VPN basata su route per il gateway. Per il modello di distribuzione classica è necessario un gateway dinamico. Non viene fornito il supporto per i gateway VPN con routing statico da punto a sito o i gateway che usano -VpnType PolicyBased.

### È possibile configurare un client da punto a sito per connettersi contempo a più reti virtuali?

Sì, è possibile. I prefissi IP delle reti virtuali non devono tuttavia essere sovrapposti e gli spazi di indirizzi da punto a sito non devono sovrapporsi tra le reti virtuali.

### Che velocità effettiva è possibile prevedere usando connessioni da sito a sito o da punto a sito?

È difficile mantenere esattamente la velocità effettiva tramite i tunnel VPN. IPSec e SSTP sono protocolli VPN con un elevato livello di crittografia. La velocità effettiva è limitata inoltre dalla latenza e dalla larghezza di banda tra le sedi locali e Internet.

## Gateway

### Cos'è un gateway basato su criteri (con routing statico)?

I gateway basati su criteri implementano VPN basate su criteri. Le VPN basate su criteri crittografano e reindirizzano i pacchetti tramite tunnel IPsec basati su combinazioni di prefissi di indirizzo tra la rete locale e la rete virtuale di Azure. I criteri (o selettore di traffico) vengono in genere definiti come un elenco di accesso nella configurazione VPN.

### Cos'è un gateway basato su route (con routing dinamico)?

I gateway basati su route implementano VPN basate su route. Le VPN basate su route usano "route" nella tabella di inoltro IP o di routing per reindirizzare i pacchetti nelle interfacce tunnel corrispondenti. Le interfacce tunnel consentono quindi di crittografare o decrittografare i pacchetti all'interno e all'esterno dei tunnel. I criteri o selettori di traffico per le VPN basate su route vengono configurati come any-to-any (o caratteri jolly).

### È possibile ottenere l'indirizzo IP del gateway VPN prima di crearlo?

No. È necessario prima creare il gateway per ottenere l'indirizzo IP. L'indirizzo IP verrà modificato se si elimina e si ricrea il gateway VPN.

### Come si ottiene l'autenticazione del tunnel VPN?

Il tunnel VPN di Azure usa chiavi precondivise (PSK) per l'autenticazione. È possibile generare una chiave precondivisa (PSK) quando si crea il tunnel VPN. È possibile modificare la chiave precondivisa generata automaticamente con il cmdlet di PowerShell di impostazione della chiave precondivisa o l'API REST.

### È possibile usare l'API di impostazione della chiave precondivisa per configurare la VPN gateway con routing statico basata su criteri?

Sì, è possibile usare l'API di impostazione della chiave precondivisa e il cmdlet di PowerShell per configurare sia le VPN basate su criteri con routing statico sia le VPN basate su route con routing dinamico di Azure.

### È possibile usare altre opzioni di autenticazione?

Per l'autenticazione possono essere usate solo chiavi precondivise.

### Cos'è la subnet del gateway e perché è necessaria?

È disponibile un servizio gateway che viene eseguito per abilitare la connettività cross-premise.

È necessario creare una subnet del gateway per la rete virtuale configurare un gateway VPN. Tutte le subnet del gateway devono essere denominate GatewaySubnet per il corretto funzionamento. Non assegnare un nome diverso alla subnet del gateway. Non distribuire VM o altri elementi alla subnet del gateway.

Le dimensioni minime della subnet del gateway dipendono interamente dalla configurazione che si vuole creare. Anche se è possibile creare una subnet del gateway pari a/29 per alcune configurazioni, è consigliabile creare una subnet del gateway di /28 o superiore (/ 28, /27, /26 e così via.).

## È possibile distribuire macchine virtuali o istanze del ruolo alla subnet del gateway?

No.

### In che modo è possibile specificare il traffico che può passare attraverso il gateway VPN?

Se si utilizza il portale di Azure classico, aggiungere tutti gli intervalli che si desidera inviare tramite il gateway per la rete virtuale nella pagina Reti in Reti locali.

### È possibile configurare il tunneling forzato?

Sì. Vedere [Configurare il tunneling forzato](vpn-gateway-about-forced-tunneling.md).

### È possibile configurare il server VPN in Azure e usarlo per connettersi alla rete locale?

Sì, è possibile distribuire i gateway o i server VPN in Azure da Azure Marketplace o tramite la creazione dei propri router VPN. Sarà necessario configurare route definite dall'utente nella rete virtuale per garantire che il traffico venga indirizzato correttamente tra le reti locali e le subnet della rete virtuale.

### Perché determinate porte sono aperte nella mia gateway VPN?

Sono necessarie per la comunicazione di infrastruttura di Azure. Sono protette (bloccate) dai certificati di Azure. Senza certificati appropriati, le entità esterne, compresi i clienti di questi gateway, non potranno causare alcun effetto su tali endpoint.

Un gateway VPN è basicamente un dispositivo multihomed con una scheda di rete che utilizza la rete privata del cliente e una scheda di rete rivolta alla rete pubblica. Le entità dell’infrastruttura di Azure non possono utilizzare le reti private del cliente per motivi di conformità, pertanto devono utilizzare endpoint pubblici per la comunicazione dell’infrastruttura. Gli endpoint pubblici vengono analizzati periodicamente dal controllo di sicurezza di Azure.


### Altre informazioni sui tipi di gateway, i requisiti e la velocità effettiva

Per altre informazioni, vedere [Informazioni sui gateway VPN](vpn-gateway-about-vpngateways.md).

## Connettività multisito e tra reti virtuali

### Quale tipo di gateway supporta la connettività multisito e tra reti virtuali?

Solo VPN basate su route (routing dinamico).

### È possibile connettere una rete virtuale con un tipo di VPN RouteBased a un'altra rete virtuale con un tipo di VPN PolicyBased?

No, entrambe le reti virtuali devono usare VPN basate su route con routing dinamico.

### Il traffico tra reti virtuali è sicuro?

Sì, è protetto mediante la crittografia IPsec/IKE.

### Il traffico da rete virtuale a rete virtuale scorre attraverso il backbone di Azure?

Sì.

### A quanti siti locali e reti virtuali può connettersi una rete virtuale?

Max. 10 combinate per i gateway con routing dinamico base e standard, 30 per i gateway VPN con Prestazioni elevate.

### È possibile usare VPN da punto a sito con la rete virtuale con più tunnel VPN?

Sì, è possibile usare VPN da punto a sito (P2S) con i gateway VPN che si connettono a più siti locali e altre reti virtuali.

### È possibile configurare più tunnel tra una rete virtuale e un sito locale usando una VPN multisito?

No, non sono supportati i tunnel ridondanti tra una rete virtuale di Azure e un sito locale.

### Possono esistere spazi di indirizzi sovrapposti tra le reti virtuali connesse e i siti locali?

No, in presenza di spazi di indirizzi sovrapposti, il caricamento del file netcfg o la creazione della rete virtuale hanno esito negativo.

### Si ottiene maggiore larghezza di banda con più VPN da sito a sito per una singola rete virtuale?

No, tutti i tunnel VPN, incluse le VPN da punto a sito, condividono lo stesso gateway VPN di Azure e la larghezza di banda disponibile.

### È possibile usare un gateway VPN di Azure per il transito del traffico tra i siti locali o verso un'altra rete virtuale?

Il traffico in transito tramite gateway VPN di Azure è possibile, ma si basa su spazi di indirizzi definiti in modo statico nel file di configurazione netcfg. Il protocollo BGP non è ancora supportato con le reti virtuali di Azure e i gateway VPN. Senza BGP la definizione manuale degli spazi di indirizzi in transito è soggetta a errori e non è consigliata.

### Azure genera la stessa chiave precondivisa IPsec/IKE per tutte le connessioni VPN per una stessa rete virtuale?

No, per impostazione predefinita vengono generate chiavi precondivise diverse per connessioni VPN diverse. È tuttavia possibile usare la nuova API REST di impostazione della chiave gateway VPN o il cmdlet di PowerShell per impostare il valore di chiave che si preferisce. La chiave DEVE essere una stringa alfanumerica di lunghezza compresa tra 1 e 128 caratteri.

### È previsto un addebito per il traffico tra le reti virtuali?

Per il traffico tra diverse reti virtuali di Azure, in Azure viene addebitato un costo solo per il traffico che passa da un'area di Azure a un'altra. La tariffa è riportata nella pagina [Prezzi del gateway VPN](https://azure.microsoft.com/pricing/details/vpn-gateway/) di Azure .


### È possibile connettere una rete virtuale con VPN IPsec a un circuito ExpressRoute?

Sì, questa operazione è supportata. Per altre informazioni, vedere [Configurare connessioni ExpressRoute e VPN da sito a sito coesistenti](../expressroute/expressroute-howto-coexist-classic.md).

## Connettività cross-premise e macchine virtuali

### Se la macchina virtuale si trova in una rete virtuale e si dispone di una connessione cross-premise, in che modo è possibile connettersi alla macchina virtuale?

Sono disponibili diverse opzioni. Se RDP è abilitato ed è stato creato un endpoint, è possibile connettersi alla macchina virtuale tramite il VIP. In tal caso, specificare il VIP e la porta a cui si desidera connettersi. Sarà necessario configurare la porta sulla macchina virtuale per il traffico. In genere, è possibile passare al portale di Azure classico e salvare le impostazioni per la connessione RDP al computer. Le impostazioni conterranno le informazioni necessarie sulla connessione.

Se si dispone di una rete virtuale per cui è configurata la connettività cross-premise, è possibile connettersi alla macchina virtuale usando il DIP interno o l'indirizzo IP privato. È possibile connettersi alla macchina virtuale anche usando il DIP interno di un'altra macchina virtuale presente nella stessa rete virtuale. Non è possibile usare RDP sulla macchina virtuale tramite DIP se si esegue la connessione da una posizione esterna alla rete virtuale. Se ad esempio è configurata una rete virtuale da punto a sito e non si stabilisce una connessione dal computer, non è possibile connettersi alla macchina virtuale tramite DIP.

### Se la macchina virtuale si trova in una rete virtuale con connettività cross-premise, il traffico dalla macchina virtuale passa tutto attraverso tale connessione?

No. Attraverso il gateway della rete virtuale passerà solo il traffico con un IP di destinazione incluso negli intervalli di indirizzi IP di rete locale specificati per la rete virtuale. Il traffico che dispone di un IP di destinazione nella rete virtuale rimarrà all'interno della rete stessa. Il restante traffico verrà inviato alle reti pubbliche tramite il bilanciamento del carico o, se si usa il tunneling forzato, tramite il gateway VPN di Azure. Per la risoluzione dei problemi, è importante assicurarsi che nella rete locale siano elencati tutti gli intervalli da inviare tramite il gateway. Verificare che gli intervalli di indirizzi nella rete locale non si sovrappongano agli intervalli di indirizzi presenti nella rete virtuale. È opportuno anche verificare che il server DNS in uso risolva il nome nell'indirizzo IP appropriato.


## Domande frequenti su Rete virtuale

Vengono visualizzate informazioni sulla rete virtuale aggiuntive in [Domande frequenti sulla rete virtuale](../virtual-network/virtual-networks-faq.md).

## Passaggi successivi

È possibile visualizzare altre informazioni sui gateway VPN nella [pagina di documentazione del Gateway VPN](https://azure.microsoft.com/documentation/services/vpn-gateway/).

 

<!---HONumber=AcomDC_0316_2016-->