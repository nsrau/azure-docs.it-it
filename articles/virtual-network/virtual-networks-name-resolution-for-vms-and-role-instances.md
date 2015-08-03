<properties 
   pageTitle="Risoluzione per le macchine virtuali e le istanze del ruolo"
   description="Scenari di risoluzione dei nomi per Azure IaaS, soluzioni ibride, tra diversi servizi cloud, Active Directory e utilizzando il proprio server DNS"
   services="virtual-network"
   documentationCenter="na"
   authors="joaoma"
   manager="jdial"
   editor="tysonn" />
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="06/30/2015"
   ms.author="joaoma" />

# Risoluzione dei nomi per le macchine virtuali e le istanze del ruolo

A seconda di come si utilizza Azure per ospitare IaaS, PaaS e soluzioni ibride, potrebbe essere necessario consentire alle macchine virtuali e alle istanze del ruolo create di comunicare con altre macchine virtuali e istanze del ruolo. Sebbene sia possibile eseguire questa comunicazione utilizzando indirizzi IP, è molto più semplice utilizzare nomi host che possono essere facilmente ricordati. Tuttavia, questi nomi host devono essere trasformati in indirizzi IP per stabilire la comunicazione.

Quando le istanze del ruolo e le macchine virtuali ospitate in Azure devono trasformare nomi di dominio e nomi host in indirizzi IP interni, possono utilizzare uno di questi due metodi:

- [Risoluzione dei nomi di Azure](azure-provided-name-resolution)

- [Risoluzione dei nomi utilizzando il proprio server DNS](name-resolution-using-your-own-DNS-server)

Il tipo di risoluzione dei nomi utilizzato dipende dal modo in cui le macchine virtuali e le istanze del ruolo hanno bisogno di comunicare all'interno del servizio cloud in questione e con altri servizi cloud.

**Nella tabella seguente vengono illustrate diverse ipotesi e le corrispondenti soluzioni di risoluzione dei nomi:**

| **Scenario** | **Risoluzione dei nomi di Azure:** | **Per altre informazioni, vedere:** |
|------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Risoluzione dei nomi tra istanze del ruolo o macchine virtuali presenti nello stesso servizio cloud | Risoluzione dei nomi di Azure | [Risoluzione dei nomi di Azure](#azure-provided-name-resolution) |
| Risoluzione dei nomi tra macchine virtuali e istanze del ruolo situate nella stessa rete virtuale | Risoluzione dei nomi fornita da Azure – utilizzando risoluzione FQDNorName tramite il proprio server DNS | - [Risoluzione dei nomi fornita da Azure](#azure-provided-name-resolution) - [Risoluzione dei nomi utilizzando il proprio server DNS](#name-resolution-using-your-own-dns-server) - [Requisiti del server DNS](#dns-server-requirements) |
| Risoluzione dei nomi tra macchine virtuali e istanze del ruolo situate in diverse reti virtuali | Risoluzione dei nomi utilizzando il proprio server DNS | - [Risoluzione dei nomi utilizzando il proprio server DNS](#name-resolution-using-your-own-dns-server) - [Requisiti del server DNS](#dns-server-requirements) |
| Più sedi locali: Risoluzione dei nomi tra istanze del ruolo o macchine virtuali in computer locali e Azure | Risoluzione dei nomi utilizzando il proprio server DNS | - [Risoluzione dei nomi utilizzando il proprio server DNS](#name-resolution-using-your-own-dns-server) - [Requisiti del server DNS](#dns-server-requirements) |
| Ricerca inversa degli indirizzi IP interni | Risoluzione dei nomi utilizzando il proprio server DNS | - [Risoluzione dei nomi utilizzando il proprio server DNS](#name-resolution-using-your-own-dns-server) - [Requisiti del server DNS](#dns-server-requirements) |
| Risoluzione dei nomi per i domini personalizzati (ad esempio domini di Active Directory, domini registrati dall’utente) | Risoluzione dei nomi utilizzando il proprio server DNS | - [Risoluzione dei nomi utilizzando il proprio server DNS](#name-resolution-using-your-own-dns-server) - [Requisiti del server DNS](#dns-server-requirements) |
| Risoluzione dei nomi tra istanze del ruolo situate in diversi servizi cloud e non in una rete virtuale | Non applicabile Connettività tra macchine virtuali e istanze del ruolo in servizi cloud diversi non è supportata esternamente a una rete virtuale. | Non applicabile |

> [AZURE.NOTE]La risoluzione dei nomi tra computer su internet e agli endpoint pubblici viene fornita automaticamente da Azure e non richiede alcuna configurazione.

## Risoluzione dei nomi di Azure

Oltre alla risoluzione per i domini internet pubblici, Azure fornisce la risoluzione dei nomi nome host per macchine virtuali e istanze del ruolo che si trovano nello stesso servizio attraverso i nomi host e tra macchine virtuali e istanze del ruolo in servizi cloud diversi che si trovano nella stessa rete virtuale attraverso FQDN. Sebbene la risoluzione dei nomi fornita da Azure non richieda alcuna configurazione, non è la scelta più appropriata per tutti gli scenari di distribuzione, come illustrato nella tabella precedente.

> [AZURE.NOTE]Nel caso dei ruoli web e di lavoro, è possibile accedere anche gli indirizzi IP interni delle istanze del ruolo in base al numero di nome e al numero di istanza del ruolo utilizzando l'API REST di gestione dei servizi Azure. Per ulteriori informazioni, vedere [riferimento API REST di gestione dei servizi](https://msdn.microsoft.com/library/azure/ee460799.aspx).

### Funzionalità e considerazioni

**Funzionalità**

- Facilità di utilizzo: non è necessaria alcuna configurazione per utilizzare il servizio DNS fornito da Azure.

- La risoluzione dei nomi host è fornita tra istanze del ruolo o macchine virtuali presenti nello stesso servizio cloud.

- Viene fornita la risoluzione dei nomi tra istanze del ruolo e macchine virtuali che si trovano nella stessa rete virtuale, ma in diversi servizi cloud, utilizzando il FQDN dell'istanza del ruolo di destinazione o della macchina virtuale.

- È possibile creare i nomi host che meglio descrivano le distribuzioni, anziché usare nomi generati automaticamente.

- Sono supportate le ricerche DNS standard per risolvere i nomi di dominio pubblico.

**Considerazioni:**

- La risoluzione dei nomi tra reti virtuali non è disponibile.

- È possibile registrare solo i nomi host e i FQDN per macchine virtuali e istanze del ruolo che si trovano nei primi 180 servizi cloud aggiunti a una rete virtuale di Azure. Se si dispone di più di 180 servizi cloud, indipendentemente dal numero di macchine virtuali e istanze del ruolo in ogni servizio, è necessario fornire il proprio server DNS per la risoluzione dei nomi.

- L’utilizzo di più nomi host per la stessa macchina virtuale o istanza del ruolo non è supportato.

- La risoluzione dei nomi di più sedi locali non è disponibile.

- Il suffisso DNS creato da Azure non può essere modificato.

- Non è possibile registrare manualmente i propri record nel DNS fornito da Azure.

- WINS e NetBIOS non sono supportati. (Non è possibile elencare le macchine virtuali nel browser di rete in Windows Explorer).

- I nomi host devono essere compatibili con DNS (devono utilizzare solo 0-9, a-z e '-' e non possono iniziare o terminare con un '-'. Vedere RFC 3696 sezione 2).

- Il traffico di query DNS è limitato per ogni macchina virtuale. Se l'applicazione esegue query DNS frequenti su più nomi di destinazione, è possibile che alcune query superino il tempo disponibile. Per evitarlo, è consigliabile utilizzare la memorizzazione nella cache del client.

## Risoluzione dei nomi utilizzando il proprio server DNS

Se i requisiti di risoluzione dei nomi vanno oltre le funzionalità fornite da Azure, è possibile utilizzare il proprio server DNS. Quando si utilizza il proprio server DNS, si è responsabili della gestione dei record necessari per i servizi cloud.

> [AZURE.NOTE]È consigliabile evitare l'utilizzo di un server DNS esterno, a meno che non sia richiesto dallo scenario di distribuzione.

## Requisiti del server DNS

Se si prevede di utilizzare la risoluzione dei nomi non fornita da Azure, il server DNS specificato deve: accettare la registrazione dinamica DNS tramite DNS dinamico (DDNS).

- Lo scavenging dei record del server DNS deve essere disabilitato. Gli indirizzi IP Azure dispongono di lease lunghi che possono comportare la rimozione dei record nel server DNS durante lo scavenging.

- Il server DNS deve avere la ricorsione abilitata per consentire la risoluzione dei nomi di dominio.

- Il server DNS deve essere accessibile (sulla porta TCP/UDP 53) per i client che richiedono la risoluzione dei nomi e per i servizi e le macchine virtuali che registreranno i loro nomi.

- Si consiglia inoltre di proteggere il server DNS dagli accessi da internet con i bot di scansione per i DNS open resolver ricorsivi..


## Specificare i server DNS

È possibile specificare più server DNS da utilizzare per le macchine virtuali e le istanze del ruolo. I server DNS vengono utilizzati nell'ordine in cui vengono specificati in modalità failover (anziché round robin). Per ogni query DNS, il client proverà per prima cosa il server DNS preferito mentre proverà i server alternativi solo nel caso in cui quello preferito non risponda. Per questo motivo, verificare di avere i server DNS elencati nell'ordine corretto per il proprio ambiente.

> [AZURE.NOTE]Se si modificano le impostazioni DNS in un file di configurazione di rete per rete virtuale che è già stato distribuito, è necessario riavviare ogni macchina virtuale affinché le modifiche vengano applicate.

### Specificare un server DNS tramite il portale di gestione

Quando si crea la rete virtuale tramite il portale di gestione, è possibile specificare l'indirizzo IP e il nome del server DNS (o dei server) che si desidera utilizzare. Una volta creata la rete virtuale, le macchine virtuali e le istanze del ruolo da distribuire nella rete virtuale vengono configurate automaticamente con le impostazioni DNS specificate, a meno che non si specifichi il server (o i server) DNS da utilizzare per la distribuzione. Per ulteriori informazioni sulla configurazione delle impostazioni di rete virtuale di Azure, vedere [Informazioni sulla configurazione di una rete virtuale nel portale di gestione](https://msdn.microsoft.com/library/azure/jj156074.aspx).

> [AZURE.NOTE]È possibile utilizzare solo fino a 9 server DNS.

### Specificare un server DNS utilizzando i file di configurazione

È possibile specificare le impostazioni DNS utilizzando due file di configurazione diverse: il file *configurazione di rete* e il file *configurazione del servizio*.

Il file di configurazione di rete viene creato per ogni rete virtuale che è possibile aggiungere in Azure. Quando si aggiungono istanze del ruolo o macchine virtuali a qualsiasi servizio cloud in una rete virtuale, le impostazioni DNS dal file di configurazione di rete vengono applicate all'istanza del ruolo o alla macchina virtuale, a meno che il file di configurazione del servizio disponga di impostazioni DNS.

Viene creato il file di configurazione del servizio per ogni servizio cloud che si aggiunge ad Azure. Quando si aggiungono istanze del ruolo o macchine virtuali al servizio cloud, le impostazioni DNS dal file di configurazione del servizio vengono applicate all'istanza del ruolo o macchina virtuale.

> [AZURE.NOTE]Le impostazioni nel file di configurazione del servizio hanno la precedenza sulle impostazioni nel file di configurazione di rete. Ad esempio, se una macchina virtuale viene aggiunta a un servizio cloud che fa parte di una rete virtuale e il file di configurazione di rete e il file di configurazione del servizio hanno impostazioni DNS, le impostazioni DNS nel file di configurazione del servizio vengono applicate alla macchina virtuale.


## Vedere anche

[Schema di configurazione di servizi di Azure](https://msdn.microsoft.com/library/azure/ee758710) [Schema di configurazione di rete virtuale](https://msdn.microsoft.com/library/azure/jj157100) [Informazioni sulla configurazione delle impostazioni di rete virtuale nel portale di gestione](https://msdn.microsoft.com/library/azure/jj156074.aspx) [Configurare una rete virtuale utilizzando un file di configurazione di rete](https://msdn.microsoft.com/library/azure/jj156097.aspx) [Attività di configurazione di rete virtuale Azure](https://msdn.microsoft.com/library/azure/jj156206.aspx)

<!---HONumber=July15_HO4-->