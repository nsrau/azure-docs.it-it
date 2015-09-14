<properties 
   pageTitle="Risoluzione per le macchine virtuali e le istanze del ruolo"
	description="Scenari di risoluzione dei nomi per Azure IaaS, soluzioni ibride, tra diversi servizi cloud, Active Directory e utilizzando il proprio server DNS"
	services="virtual-network"
	documentationCenter="na"
	authors="joaoma"
	manager="jdial"
	editor="tysonn"/>
<tags 
   ms.service="virtual-network"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="infrastructure-services"
	ms.date="08/10/2015"
	ms.author="joaoma"/>

# Risoluzione dei nomi per le macchine virtuali e le istanze del ruolo

A seconda di come si usa Azure per ospitare soluzioni IaaS, PaaS e ibride, potrebbe essere necessario consentire la comunicazione tra VM e istanze del ruolo create. Nonostante questa comunicazione possa avvenire usando indirizzi IP, è molto più semplice usare nomi che possono essere facilmente ricordati e che non cambiano.

Quando le istanze del ruolo e le VM ospitate in Azure devono risolvere nomi di dominio trasformandoli in indirizzi IP interni, possono usare uno di questi due metodi:

- [Risoluzione dei nomi fornita da Azure](#azure-provided-name-resolution)

- [Risoluzione dei nomi usando il server DNS](#name-resolution-using-your-own-dns-server)

Il tipo di risoluzione dei nomi usato dipende dal modo in cui VM e istanze del ruolo devono comunicare.

**Nella tabella seguente vengono illustrate diverse ipotesi e le corrispondenti soluzioni di risoluzione dei nomi:**

| **Scenario** | **Risoluzione dei nomi fornita da Azure:** | **Per altre informazioni, vedere:** |
|--------------|----------------------------------|-------------------------------|
| Risoluzione dei nomi tra istanze del ruolo o macchine virtuali presenti nello stesso servizio cloud | Risoluzione dei nomi fornita da Azure | [Risoluzione dei nomi fornita da Azure](#azure-provided-name-resolution)|
| Risoluzione dei nomi tra macchine virtuali e istanze del ruolo situate nella stessa rete virtuale | Risoluzione dei nomi fornita da Azure (solo per distribuzioni basate su ARM) o Risoluzione dei nomi usando il server DNS | [Risoluzione dei nomi fornita da Azure](#azure-provided-name-resolution), [Risoluzione dei nomi usando il server DNS](#name-resolution-using-your-own-dns-server) e [Requisiti del server DNS](#dns-server-requirements) |
| Risoluzione dei nomi tra macchine virtuali e istanze del ruolo situate in diverse reti virtuali | Risoluzione dei nomi usando il server DNS| [Risoluzione dei nomi usando il server DNS](#name-resolution-using-your-own-dns-server) e [Requisiti del server DNS](#dns-server-requirements)|
| Più sedi locali: Risoluzione dei nomi tra istanze del ruolo o macchine virtuali in computer locali e Azure| Risoluzione dei nomi usando il server DNS| [Risoluzione dei nomi usando il server DNS](#name-resolution-using-your-own-dns-server) e [Requisiti del server DNS](#dns-server-requirements)|
| Ricerca inversa degli indirizzi IP interni| Risoluzione dei nomi usando il server DNS| [Risoluzione dei nomi usando il server DNS](#name-resolution-using-your-own-dns-server) e [Requisiti del server DNS](#dns-server-requirements)|
| Risoluzione dei nomi per i domini non pubblici (ad esempio domini di Active Directory)| Risoluzione dei nomi usando il server DNS| [Risoluzione dei nomi usando il server DNS](#name-resolution-using-your-own-dns-server) e [Requisiti del server DNS](#dns-server-requirements)|
| Risoluzione dei nomi tra istanze del ruolo situate in diversi servizi cloud e non in una rete virtuale| Non applicabile Connettività tra macchine virtuali e istanze del ruolo in servizi cloud diversi non è supportata esternamente a una rete virtuale.| Non applicabile|


## Risoluzione dei nomi fornita da Azure

Oltre alla risoluzione dei nomi DNS pubblici, Azure offre la risoluzione dei nomi interni per VM e istanze del ruolo che si trovano all'interno della stessa rete virtuale o dello stesso servizio cloud. Le VM/istanze in un servizio cloud condividono lo stesso suffisso DNS, quindi è sufficiente il solo nome host. Nelle reti virtuali classiche servizi cloud diversi hanno suffissi DNS diversi, quindi è necessario il nome di dominio completo (FQDN). Nelle reti virtuali basate su ARM il suffisso DNS è comune nella rete virtuale, quindi il nome di dominio completo non è necessario e il nome DNS può essere assegnato alla scheda di interfaccia di rete o alla macchina virtuale. Sebbene la risoluzione dei nomi fornita da Azure non richieda alcuna configurazione, non è la scelta più appropriata per tutti gli scenari di distribuzione, come illustrato nella tabella precedente.

> [AZURE.NOTE]Nel caso dei ruoli web e di lavoro, è possibile accedere anche gli indirizzi IP interni delle istanze del ruolo in base al numero di nome e al numero di istanza del ruolo utilizzando l'API REST di gestione dei servizi Azure. Per altre informazioni, vedere [Riferimento all'API REST di gestione dei servizi](https://msdn.microsoft.com/library/azure/ee460799.aspx).

### Funzionalità e considerazioni

**Funzionalità**

- Facilità di utilizzo: non è necessaria alcuna configurazione per usare la risoluzione dei nomi fornita da Azure.

- La risoluzione dei nomi viene fornita tra istanze del ruolo o VM presenti nello stesso servizio cloud senza necessità di un nome di dominio completo.

- La risoluzione dei nomi viene fornita tra VM nelle reti virtuali basate su ARM senza necessità del nome di dominio completo, mentre le reti classiche richiedono il nome di dominio completo per la risoluzione dei nomi in servizi cloud diversi.

- È possibile creare i nomi host che meglio descrivano le distribuzioni, anziché usare nomi generati automaticamente.

**Considerazioni:**

- La risoluzione dei nomi tra reti virtuali non è disponibile.

- È possibile registrare i nomi host solo per VM e istanze del ruolo che si trovano nei primi 180 servizi cloud aggiunti a una rete virtuale di Azure. Se si dispone di più di 180 servizi cloud, indipendentemente dal numero di macchine virtuali e istanze del ruolo in ogni servizio, è necessario fornire il proprio server DNS per la risoluzione dei nomi.

- La risoluzione dei nomi di più sedi locali non è disponibile.

- Il suffisso DNS creato da Azure non può essere modificato.

- Non è possibile registrare manualmente i propri record.

- WINS e NetBIOS non sono supportati. (Non è possibile elencare le macchine virtuali nel browser di rete in Windows Explorer).

- I nomi host devono essere compatibili con DNS (devono utilizzare solo 0-9, a-z e '-' e non possono iniziare o terminare con un '-'. Vedere RFC 3696 sezione 2).

- Il traffico di query DNS è limitato per ogni macchina virtuale. Se l'applicazione esegue query DNS frequenti su più nomi di destinazione, è possibile che alcune query superino il tempo disponibile. Per evitare questa situazione, è consigliabile abilitare la memorizzazione nella cache del client. Questa opzione è abilitata per impostazione predefinita in Windows, ma potrebbe non essere abilitata in alcune distribuzioni Linux.

## Risoluzione dei nomi usando il server DNS

Se i requisiti di risoluzione dei nomi vanno oltre le funzionalità fornite da Azure, è possibile usare i propri server DNS. Quando si usano i propri server DNS, si è responsabili della gestione dei record DNS.

> [AZURE.NOTE]È consigliabile evitare l'utilizzo di un server DNS esterno, a meno che non sia richiesto dallo scenario di distribuzione.

## Requisiti del server DNS

Se si prevede di usare una risoluzione dei nomi non fornita da Azure, il server DNS specificato deve soddisfare i requisiti seguenti:

- Il server DNS deve accettare la registrazione DNS dinamica tramite il protocollo DDNS (Dynamic DNS) oppure è necessario creare i record richiesti.

- Se si usa il protocollo DNS dinamico, nel server DNS deve essere attivato lo scavenging dei record. In Azure gli indirizzi IP hanno lease DHCP lunghi che possono comportare la rimozione dei record dal server DNS durante lo scavenging.

- Il server DNS deve avere la ricorsione abilitata per consentire la risoluzione dei nomi di dominio.

- Il server DNS deve essere accessibile (sulla porta TCP/UDP 53) per i client che richiedono la risoluzione dei nomi e per i servizi e le macchine virtuali che registreranno i loro nomi.

- Si consiglia inoltre di proteggere il server DNS dagli accessi da internet con i bot di scansione per i DNS open resolver ricorsivi.

- Per prestazioni ottimali, quando si usano VM Azure come server DNS, è necessario disabilitare IPv6 e assegnare un [IP pubblico a livello di istanza](virtual-networks-instance-level-public-ip.mp) a ogni VM server DNS.

## Specificare i server DNS

È possibile specificare più server DNS da utilizzare per le macchine virtuali e le istanze del ruolo. Per ogni query DNS, il client proverà per prima cosa il server DNS preferito e proverà i server alternativi solo nel caso in cui quello preferito non risponda, ovvero non è previsto il bilanciamento del carico tra i diversi server DNS per le query DNS. Per questo motivo, verificare di avere i server DNS elencati nell'ordine corretto per il proprio ambiente.

> [AZURE.NOTE]Se si modificano le impostazioni DNS in un file di configurazione di rete per rete virtuale che è già stato distribuito, è necessario riavviare ogni macchina virtuale affinché le modifiche vengano applicate.

### Impostazione di un server DNS nel portale di gestione

Quando si crea una rete virtuale nel portale di gestione, è possibile specificare l'indirizzo IP e il nome di uno o più server DNS da usare. Una volta creata la rete virtuale, le macchine virtuali e le istanze del ruolo distribuite nella rete virtuale vengono configurate automaticamente con le impostazioni DNS specificate. I server DNS specificati per un determinato servizio cloud (portale di Azure classico) o una scheda di interfaccia rete (distribuzioni basate su ARM) hanno la precedenza su quelli specificati per la rete virtuale. Vedere [Informazioni sulla configurazione di una rete virtuale nel portale di gestione](virtual-networks-settings.md).

### Impostazione di un server DNS usando file di configurazione (portale di Azure classico)

Per le reti virtuali classiche, è possibile specificare le impostazioni DNS usando due file di configurazione diversi: il file di *configurazione della rete* e il file di *configurazione del servizio*.

> [AZURE.NOTE]I server DNS nel file di configurazione del servizio hanno la precedenza sulle impostazioni nel file di configurazione della rete.
 
Il file di configurazione della rete descrive le reti virtuali nella sottoscrizione. Quando si aggiungono istanze del ruolo o VM a un servizio cloud in una rete virtuale, le impostazioni DNS del file di configurazione della rete vengono applicate a ogni istanza del ruolo o VM, a meno che non siano stati specificati server DNS specifici del servizio cloud.

Il file di configurazione del servizio viene creato per ogni servizio cloud aggiunto ad Azure. Quando si aggiungono istanze del ruolo o VM al servizio cloud, le impostazioni DNS del file di configurazione del servizio vengono applicate a ogni istanza del ruolo o VM.



## Passaggi successivi

[Schema di configurazione dei servizi di Azure](https://msdn.microsoft.com/library/azure/ee758710)

[Schema di configurazione di Rete virtuale](https://msdn.microsoft.com/library/azure/jj157100)

[Informazioni sulle impostazioni della rete virtuale nel portale di gestione](virtual-networks-settings.md)

[Configurare una rete virtuale usando un file di configurazione di rete](virtual-networks-using-network-configuration-file.md)

<!---HONumber=September15_HO1-->