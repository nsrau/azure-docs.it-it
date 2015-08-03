<properties 
   pageTitle="Domande frequenti sul gateway VPN di Rete virtuale | Microsoft Azure"
   description="Domande frequenti sul gateway VPN. Domande frequenti relative alle connessioni cross-premise di Rete virtuale di Microsoft Azure, alle connessioni con configurazioni ibride e ai gateway VPN"
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="jdial"
   editor="tysonn" />
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="07/16/2015"
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

Reti virtuali supporta le seguenti connessioni cross-premise:

- [Site-to-Site](vpn-gateway-site-to-site-create.md): connessione VPN tramite IPsec (IKE v1 e IKE v2). Per questo tipo di connessione è necessario un dispositivo VPN o RRAS.

- [Point-to-Site](vpn-gateway-point-to-site-create.md): connessione VPN tramite SSTP (Secure Sockets Tunneling Protocol). Questa connessione non richiede un dispositivo VPN.

- [Tra reti virtuali](../virtual-networks-vnet-to-vnet-connection.md): connessione analoga alla configurazione Site-to-Site. La connessione tra reti virtuali è una connessione VPN tramite IPsec (IKE v1 e IKE v2). Non richiede un dispositivo VPN.

- [Multisito](http://go.microsoft.com/fwlink/?LinkID=615106): variante di una configurazione Site-to-Site che consente di connettere più siti locali a un singolo gateway di rete virtuale.

- [ExpressRoute](../expressroute-overview.md): connessione diretta ad Azure dalla rate WAN, non sulla rete Internet pubblica. Per altre informazioni, vedere [Panoramica tecnica relativa a ExpressRoute](../expressroute-overview.md) e [Domande frequenti su ExpressRoute](../expressroute-faqs.md).

### Qual è la differenza tra una connessione Site-to-Site e una Point-to-Site?

Le connessioni **Site-to-Site** consentono di connettere qualsiasi computer locale a qualsiasi macchina virtuale o istanza del ruolo all'interno della rete virtuale, a seconda della configurazione di routing. Questa opzione è ottimale per una connessione cross-premise sempre disponibile ed è adatta per le configurazioni ibride. Questo tipo di connessione si basa su un dispositivo VPN IPSec (dispositivo hardware o software) che è necessario distribuire in corrispondenza del perimetro della rete. Per creare questo tipo di connessione, è necessario disporre dell'hardware VPN richiesto e di un indirizzo IP IPv4 accessibile pubblicamente.

Le connessioni **Point-to-Site** consentono di connettere un singolo computer a qualsiasi elemento all'interno della rete virtuale. Questo tipo di connessione usa il client VPN incorporato di Windows. La configurazione Point-to-Site prevede l'installazione di un certificato e di un pacchetto di configurazione client VPN che contiene le impostazioni che consentono al computer di connettersi a qualsiasi macchina virtuale o istanza del ruolo all'interno della rete virtuale. Si tratta di un'ottima opzione quando si desidera connettersi a una rete virtuale ma non ci si trova nella sede locale. È consigliabile anche quando non si dispone dell'hardware VPN o dell'indirizzo IP IPv4 accessibile pubblicamente, entrambi necessari per una connessione Site-to-Site.

Nota: è possibile configurare la rete virtuale in modo da usare sia la connettività Point-to-Site sia la connettività Site-to-Site contemporaneamente, purché la connessione Site-to-Site venga creata usando un gateway dinamico. Per altre informazioni, vedere [Informazioni sulla connettività cross-premise protetta della rete virtuale](http://go.microsoft.com/fwlink/p/?LinkID=532884&clcid=0x409).

### Che cos'è ExpressRoute?

ExpressRoute permette di creare connessioni private tra i data center Microsoft e l'infrastruttura disponibile localmente o in un ambiente con percorso condiviso. Con ExpressRoute è possibile stabilire connessioni a servizi cloud Microsoft, ad esempio Microsoft Azure e Office 365 in una struttura di partner ExpressRoute con percorso condiviso, oppure connettersi direttamente dalla rete WAN esistente, ad esempio una VPN MPLS fornita da un provider di servizi di rete.

Le connessioni ExpressRoute offrono un livello di sicurezza migliore, maggiore affidabilità, velocità più elevate e latenze più basse rispetto alle connessioni Internet tradizionali. In alcuni casi, l'uso di connessioni ExpressRoute per trasferire i dati tra la rete locale e Azure può offrire vantaggi significativi in termini di costi. Se già stata creata una connessione cross-premise dalla rete locale ad Azure, è possibile eseguire la migrazione a una connessione ExpressRoute, mantenendo intatta la rete virtuale.

Per altre informazioni, vedere le [Domande frequenti su ExpressRoute](../expressroute-faqs.md).

## Dispositivi VPN e connessioni Site-to-Site

### Quali sono gli aspetti di cui tenere conto nella scelta di un dispositivo VPN?

È stato approvato un set di dispositivi VPN Site-to-Site standard in partnership con fornitori di dispositivi. Per un elenco dei dispositivi VPN sicuramente compatibili, per gli esempi o le istruzioni di configurazione corrispondenti e le relative specifiche, fare clic [qui](http://go.microsoft.com/fwlink/p/?LinkID=248098). Tutti i dispositivi appartenenti ai gruppi di dispositivi di cui è indicata la compatibilità nota dovrebbero funzionare con Rete virtuale. Per agevolare la configurazione del dispositivo VPN, fare riferimento al collegamento o all'esempio di configurazione del dispositivo corrispondente al gruppo di dispositivi appropriato.

### Quali operazioni è opportuno eseguire se si possiede un dispositivo VPN non incluso nell'elenco di dispositivi con compatibilità nota?

Se un dispositivo non è elencato tra i dispositivi VPN con compatibilità nota e si desidera usarlo per la connessione VPN, è necessario verificare che soddisfi i parametri e le opzioni di configurazione IPsec/IKE elencati [qui](http://go.microsoft.com/fwlink/p/?LinkID=615099). I dispositivi che soddisfano i requisiti minimi dovrebbero funzionare correttamente con Rete virtuale. Contattare il produttore del dispositivo per assistenza e istruzioni di configurazione.

### È possibile usare soluzioni software VPN per connettersi ad Azure?

Sono supportati server RRAS (Routing e Accesso remoto) in Windows Server 2012 per la configurazione Site-to-Site cross-premise.

Con il gateway dovrebbero funzionare anche altre soluzioni software VPN, purché siano conformi alle implementazioni di IPSec standard del settore. Per istruzioni sulla configurazione e sull'assistenza, contattare il fornitore del software.

## Connessioni Point-to-Site

### Quali sistemi operativi è possibile usare con la connettività Point-to-Site?

Sono supportati i sistemi operativi seguenti:

- Windows 7 (solo versione a 64 bit)

- Windows Server 2008 R2

- Windows 8 (solo versione a 64 bit)

- Windows Server 2012

### Per la connettività Point-to-Site è possibile usare qualsiasi software VPN client che supporti SSTP?

No. L'assistenza è limitata solo alle versioni dei sistemi operativi Windows elencati in precedenza.

### Di quanti endpoint client VPN è possibile disporre nella configurazione Point-to-Site?

Sono supportati fino a 128 client VPN da poter connettere contemporaneamente a una rete virtuale.

### È possibile usare la CA radice della PKI interna per la connettività Point-to-Site?

Attualmente sono supportati solo i certificati radice autofirmati.

### È possibile attraversare proxy e firewall con la funzionalità Point-to-Site?

Sì. Viene usato SSTP (Secure Sockets Tunneling Protocol) per effettuare il tunneling tramite firewall. Questo tunnel verrà visualizzato come connessione HTTPs.

### Se si riavvia un computer client configurato per la funzionalità Point-to-Site, la VPN verrà riconnessa automaticamente?

Per impostazione predefinita, tramite il computer client non verrà ristabilita automaticamente la connessione VPN.

### La funzionalità Point-to-Site supporta la riconnessione automatica e il DNS dinamico nei client VPN?

La riconnessione automatica e il DNS dinamico non sono supportati attualmente nelle VPN Point-to-Site.

### È possibile la coesistenza di configurazioni Site-to-Site e Point-to-Site per la stessa rete virtuale?

Sì. Funzionano entrambe le soluzioni se si dispone di un gateway VPN con routing dinamico per la rete virtuale. Non sono supportate configurazioni Point-to-Site in gateway VPN con routing statico.

### È possibile configurare un client Point-to-Site per connettersi contemporaneamente a più reti virtuali?

Sì, è possibile. I prefissi IP delle reti virtuali non devono tuttavia essere sovrapposti e gli spazi di indirizzi Point-to-Site non devono sovrapporsi tra le reti virtuali.

### Che velocità effettiva è possibile prevedere tramite le connessioni Site-to-Site o Point-to-Site?

È difficile mantenere esattamente la velocità effettiva tramite i tunnel VPN. IPSec e SSTP sono protocolli VPN con un elevato livello di crittografia. La velocità effettiva è limitata inoltre dalla latenza e dalla larghezza di banda tra le sedi locali e Internet.

## Gateway

### Cos'è un gateway con routing statico?

I gateway con routing statico implementano VPN basate su criteri. Le VPN basate su criteri crittografano e reindirizzano i pacchetti tramite tunnel IPsec basati sulle combinazioni di prefissi di indirizzo tra la rete locale e la rete virtuale di Azure. I criteri (o selettore di traffico) vengono in genere definiti come un elenco di accesso nella configurazione VPN.

### Cos'è un gateway con routing dinamico?

I gateway con routing dinamico implementano VPN basate su route. Le VPN basate su route usano "route" nella tabella di inoltro IP o di routing per reindirizzare i pacchetti nelle interfacce tunnel corrispondenti. Le interfacce tunnel consentono quindi di crittografare o decrittografare i pacchetti all'interno e all'esterno dei tunnel. I criteri o selettori di traffico per le VPN basate su route vengono configurati come any-to-any (o caratteri jolly).

### È possibile ottenere l'indirizzo IP del gateway VPN prima di crearlo?

No. È necessario prima creare il gateway per ottenere l'indirizzo IP. L'indirizzo IP verrà modificato se si elimina e si ricrea il gateway VPN.

### Come si ottiene l'autenticazione del tunnel VPN?

Il tunnel VPN di Azure usa chiavi precondivise (PSK) per l'autenticazione. È possibile generare una chiave precondivisa (PSK) quando si crea il tunnel VPN. È possibile modificare la chiave precondivisa generata automaticamente con il cmdlet di PowerShell di impostazione della chiave precondivisa o l'API REST.

### È possibile usare l'API di impostazione della chiave precondivisa per configurare la VPN gateway con routing statico?

Sì, è possibile usare l'API di impostazione della chiave precondivisa e il cmdlet di PowerShell per configurare sia la VPN con routing statico sia le VPN con routing dinamico di Azure.

### È possibile usare altre opzioni di autenticazione?

Per l'autenticazione possono essere usate solo chiavi precondivise.

### Cos'è la subnet del gateway e perché è necessaria?

È disponibile un servizio gateway che viene eseguito per abilitare la connettività cross-premise. Sono necessari 2 indirizzi IP dal dominio di routing per abilitare il routing tra le sedi locali e il cloud. All'utente viene richiesto di specificare almeno una subnet /29 da cui sia possibile selezionare gli indirizzi IP per la configurazione delle route.

Si noti che non è necessario distribuire le macchine virtuali o le istanze del ruolo nella subnet del gateway.

### In che modo è possibile specificare il traffico che può passare attraverso il gateway VPN?

Aggiungere tutti gli intervalli che si desidera inviare tramite il gateway per la rete virtuale nella pagina Reti in Reti locali.

### È possibile configurare il tunneling forzato?

Sì. Vedere [Informazioni sul tunneling forzato](vpn-gateway-about-forced-tunneling.md).

### È possibile configurare il server VPN in Azure e usarlo per connettersi alla rete locale?

Sì, è possibile distribuire i gateway o i server VPN in Azure da Azure Marketplace o tramite la creazione dei propri router VPN. Sarà necessario configurare le route definite dall'utente nella rete virtuale per garantire che il traffico venga indirizzato correttamente tra le reti locali e le subnet della rete virtuale.

### Altre informazioni sui tipi di gateway, i requisiti e la velocità effettiva

Per altre informazioni, vedere[sul gateway VPN](vpn-gateway-about-vpngateways.md).

## Connettività multisito e tra reti virtuali

### Quale tipo di gateway supporta la connettività multisito e tra reti virtuali?

Solo le VPN con routing dinamico.

### È possibile connettere una rete virtuale con VPN con routing dinamico a un'altra rete virtuale con VPN con routing statico?

No, entrambe le reti virtuali devono usare VPN con routing dinamico.

### Il traffico tra reti virtuali è sicuro?

Sì, è protetto mediante la crittografia IPsec/IKE.

### A quanti siti locali e reti virtuali può connettersi una rete virtuale?

Max. 10 combinate per i gateway con routing dinamico base e standard, 30 per i gateway VPN con Prestazioni elevate.

### È possibile usare VPN Point-to-Site con la rete virtuale con più tunnel VPN?

Sì, è possibile usare VPN Point-to-Site (P2S) con i gateway VPN che si connettono a più siti locali e altre reti virtuali.

### È possibile configurare più tunnel tra una rete virtuale e un sito locale usando una VPN multisito?

No, non sono supportati i tunnel ridondanti tra una rete virtuale di Azure e un sito locale.

### Possono esistere spazi di indirizzi sovrapposti tra le reti virtuali connesse e i siti locali?

No, in presenza di spazi di indirizzi sovrapposti, il caricamento di NETCFG o la creazione della rete virtuale ha esito negativo.

### Si ottiene maggiore larghezza di banda con più VPN Site-to-Site per una singola rete virtuale?

No, tutti i tunnel VPN, incluse le VPN Point-to-Site, condividono lo stesso gateway VPN di Azure e la larghezza di banda disponibile.

### È possibile usare un gateway VPN di Azure per il transito del traffico tra i siti locali o verso un'altra rete virtuale?

Il traffico in transito tramite gateway VPN di Azure è possibile, ma si basa su spazi di indirizzi definiti in modo statico nel file di configurazione NETCFG. Il protocollo BGP non è ancora supportato con le reti virtuali di Azure e i gateway VPN. Senza BGP, la definizione manuale degli spazi di indirizzi in transito in NETCFG è soggetta a errori e non è consigliata.

### Azure genera la stessa chiave precondivisa IPsec/IKE per tutte le connessioni VPN per una stessa rete virtuale?

No, per impostazione predefinita vengono generate chiavi precondivise diverse per connessioni VPN diverse. È tuttavia possibile usare la nuova API REST di impostazione della chiave gateway VPN o il cmdlet di PowerShell per impostare il valore di chiave che si preferisce. La chiave DEVE essere una stringa alfanumerica di lunghezza compresa tra 1 e 128 caratteri.

### È previsto un addebito per il traffico tra le reti virtuali?

Per il traffico tra diverse reti virtuali di Azure, in Azure viene addebitato un costo solo per il traffico che passa da un'area di Azure a un'altra. La tariffa è riportata nella pagina [Prezzi](https://azure.microsoft.com/pricing/details/vpn-gateway/) del gateway VPN di Azure .

### È possibile connettere una rete virtuale con VPN IPsec a un circuito ExpressRoute?

No, questa operazione non è supportata.

## Connettività e macchine virtuali

### Se la macchina virtuale si trova in una rete virtuale e si dispone di una connessione cross-premise, in che modo è possibile connettersi alla macchina virtuale?

Sono disponibili diverse opzioni. Se RDP è abilitato ed è stato creato un endpoint, è possibile connettersi alla macchina virtuale tramite il VIP. In tal caso, specificare il VIP e la porta a cui si desidera connettersi. Sarà necessario configurare la porta sulla macchina virtuale per il traffico. In genere, è possibile salvare le impostazioni per la connessione RDP al computer nel portale di gestione. Le impostazioni conterranno le informazioni necessarie sulla connessione.

Se si dispone di una rete virtuale per cui è configurata la connettività cross-premise, è possibile connettersi alla macchina virtuale usando il DIP interno o l'indirizzo IP privato. È possibile connettersi alla macchina virtuale anche usando il DIP interno di un'altra macchina virtuale presente nella stessa rete virtuale. Non è possibile usare RDP sulla macchina virtuale tramite DIP se si esegue la connessione da una posizione esterna alla rete virtuale. Se ad esempio è configurata una rete virtuale Point-to-Site e non si stabilisce una connessione dal computer, non è possibile connettersi alla macchina virtuale tramite DIP.

### Se la macchina virtuale si trova in una rete virtuale con connettività cross-premise, il traffico dalla macchina virtuale passa tutto attraverso tale connessione?

No. Attraverso il gateway della rete virtuale passerà solo il traffico con un IP di destinazione incluso negli intervalli di indirizzi IP di rete locale specificati per la rete virtuale. Il traffico che dispone di un IP di destinazione nella rete virtuale rimarrà all'interno della rete stessa. Il restante traffico verrà inviato alle reti pubbliche tramite il bilanciamento del carico o, se si usa il tunneling forzato, tramite il gateway VPN di Azure. Per la risoluzione dei problemi, è importante assicurarsi che nella rete locale siano elencati tutti gli intervalli da inviare tramite il gateway. Verificare che gli intervalli di indirizzi nella rete locale non si sovrappongano agli intervalli di indirizzi presenti nella rete virtuale. È opportuno anche verificare che il server DNS in uso risolva il nome nell'indirizzo IP appropriato.

## Passaggi successivi

Per dettagli aggiuntivi, vedere altre domande frequenti sulla rete:

- [Domande frequenti su Rete virtuale](http://go.microsoft.com/fwlink/p/?LinkId=615155)

- [Domande frequenti su ExpressRoute](../expressroute-faqs.md)

 

<!---HONumber=July15_HO4-->