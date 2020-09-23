---
title: Panoramica della rete-server flessibile per database di Azure per MySQL
description: Informazioni sulle opzioni di connettività e rete nell'opzione di distribuzione flessibile del server per database di Azure per MySQL
author: rachel-msft
ms.author: raagyema
ms.service: mysql
ms.topic: conceptual
ms.date: 9/21/2020
ms.openlocfilehash: 550f3367fe2e5283aff788b36203e988361590ad
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90937122"
---
# <a name="connectivity-and-networking-concepts-for-azure-database-for-mysql---flexible-server-preview"></a>Connettività e concetti di rete per database di Azure per MySQL-server flessibile (anteprima)

Questo articolo descrive le opzioni di connettività pubblica e privata per il server. Vengono illustrati in dettaglio i concetti di rete per il server flessibile database di Azure per MySQL per creare un server in modo sicuro in Azure.

> [!IMPORTANT]
> Database di Azure per MySQL: il server flessibile è in versione di anteprima.

## <a name="choosing-a-networking-option"></a>Scelta di un'opzione di rete
Sono disponibili due opzioni di rete per il server flessibile del database di Azure per MySQL. Le opzioni sono **Accesso privato (integrazione rete virtuale)** e **Accesso pubblico (indirizzi IP consentiti)** . Al momento della creazione del server, è necessario selezionare un'opzione. 

> [!NOTE]
> L'opzione di rete non può essere modificata dopo la creazione del server. 

* **Accesso privato (integrazione rete virtuale)** : è possibile distribuire il server flessibile nella [rete virtuale di Azure](../../virtual-network/virtual-networks-overview.md). Le reti virtuali di Azure forniscono comunicazioni private e sicure. Le risorse di una rete virtuale possono comunicare tramite indirizzi IP privati.

   Scegliere l'opzione di integrazione rete virtuale per ottenere le funzionalità seguenti:
   * Connettersi al server flessibile da risorse di Azure nella stessa rete virtuale usando indirizzi IP privati
   * Usare VPN o ExpressRoute per connettersi al server flessibile da risorse non di Azure
   * Il server flessibile non ha un endpoint pubblico

* **Accesso pubblico (indirizzi IP consentiti)** : è possibile accedere al server flessibile tramite un endpoint pubblico. L'endpoint pubblico è un indirizzo DNS risolvibile pubblicamente. L'espressione "indirizzi IP consentiti" si riferisce a un intervallo di indirizzi IP a cui si sceglie di concedere l'autorizzazione per l'accesso al server. Queste autorizzazioni si definiscono **regole del firewall**. 

   Scegliere il metodo di accesso pubblico se si desiderano le seguenti funzionalità:
   * Connettersi dalle risorse di Azure che non supportano le reti virtuali
   * Connettersi da risorse esterne a un Azure non connesse tramite VPN o ExpressRoute 
   * Il server flessibile dispone di un endpoint pubblico

Le caratteristiche seguenti si applicano se si sceglie di utilizzare l'opzione accesso privato o accesso pubblico:
* Le connessioni dagli indirizzi IP consentiti devono eseguire l'autenticazione al server MySQL con credenziali valide
* La [crittografia della connessione](#tls-and-ssl) è disponibile per il traffico di rete
* Il server dispone di un nome di dominio completo (FQDN). Per la proprietà hostname nelle stringhe di connessione, è consigliabile utilizzare il nome di dominio completo anziché un indirizzo IP.
* Entrambe le opzioni controllano l'accesso a livello di server, non a livello di database o di tabella. Usare le proprietà dei ruoli di MySQL per controllare l'accesso a database, tabelle e altri oggetti.


## <a name="private-access-vnet-integration"></a>Accesso privato (integrazione rete virtuale)
L'integrazione di accesso privato con rete virtuale (VNET) fornisce comunicazioni private e sicure per il server MySQL flessibile.

### <a name="virtual-network-concepts"></a>Concetti relativi alla rete virtuale
Di seguito sono riportati alcuni concetti con cui si ha familiarità con l'uso di reti virtuali con i server MySQL flessibili.

* **Rete virtuale** : una rete virtuale di Azure (VNet) contiene uno spazio di indirizzi IP privato configurato per l'uso. Per altre informazioni sulle reti virtuali di Azure, vedere [Panoramica di rete virtuale](../../virtual-network/virtual-networks-overview.md) di Azure.

    La rete virtuale deve trovarsi nella stessa area di Azure del server flessibile.


* **Subnet delegata** : una rete virtuale contiene subnet (sottoreti). Le subnet consentono di segmentare la rete virtuale in spazi di indirizzi più piccoli. Le risorse di Azure vengono distribuite in subnet specifiche all'interno di una rete virtuale. 

   Il server MySQL flessibile deve trovarsi in una subnet **delegata** solo per l'uso di MySQL flexible server. Con questa delega solo i server flessibili di Database di Azure per MySQL possono usare tale subnet. Gli altri tipi di risorsa di Azure non possono trovarsi nella subnet delegata. Si delega una subnet assegnando la relativa proprietà di delega come Microsoft. DBforMySQL/flexibleServers.


### <a name="unsupported-virtual-network-scenarios"></a>Scenari di rete virtuale non supportati
* Endpoint pubblico (o IP pubblico o DNS): un server flessibile distribuito in una rete virtuale non può avere un endpoint pubblico
* Dopo che il server flessibile è stato distribuito in una rete virtuale e in una subnet, non è possibile spostarlo in un'altra rete virtuale o subnet. Non è possibile spostare la rete virtuale in un altro gruppo di risorse o sottoscrizione.
* Non è possibile aumentare le dimensioni della subnet (spazi degli indirizzi) dopo che sono presenti risorse nella subnet
* Il peering di reti virtuali tra aree non è supportato

Informazioni su come abilitare l'accesso privato (VNET Integration) usando l'interfaccia della riga di comando di [portale di Azure](how-to-manage-virtual-network-portal.md) o [Azure](how-to-manage-virtual-network-cli.md).


## <a name="public-access-allowed-ip-addresses"></a>Accesso pubblico (indirizzi IP consentiti)
Le caratteristiche del metodo di accesso pubblico includono:
* Solo gli indirizzi IP consentiti sono autorizzati ad accedere al server MySQL flessibile. Per impostazione predefinita, non sono consentiti indirizzi IP. È possibile aggiungere gli indirizzi IP durante la creazione del server o successivamente.
* Il server MySQL ha un nome DNS risolvibile pubblicamente
* Il server flessibile non si trova in una delle reti virtuali di Azure
* Il traffico di rete da e verso il server non passa attraverso una rete privata. Il traffico USA i percorsi Internet generali.

### <a name="firewall-rules"></a>Regole del firewall
La concessione dell'autorizzazione a un indirizzo IP è detta regola del firewall. Se un tentativo di connessione proviene da un indirizzo IP non consentito, il client di origine visualizzerà un errore.


### <a name="allowing-all-azure-ip-addresses"></a>Consentire tutti gli indirizzi IP di Azure
Se un indirizzo IP in uscita fisso non è disponibile per il servizio di Azure, è possibile prendere in considerazione l'abilitazione delle connessioni da tutti gli indirizzi IP dei Data Center di Azure.

> [!IMPORTANT]
> L'opzione **Consenti l'accesso pubblico da servizi e risorse di Azure in Azure** consente di configurare il firewall in modo da consentire tutte le connessioni da Azure, incluse le connessioni dalle sottoscrizioni di altri clienti. Quando si seleziona questa opzione, verificare che l'account di accesso e le autorizzazioni utente limitino l'accesso ai soli utenti autorizzati.

Informazioni su come abilitare e gestire l'accesso pubblico (indirizzi IP consentiti) usando il [portale di Azure](how-to-manage-firewall-portal.md) o l'interfaccia della riga di comando di [Azure](how-to-manage-firewall-cli.md).


### <a name="troubleshooting-public-access-issues"></a>Risoluzione dei problemi di accesso pubblico
Quando l'accesso al database Microsoft Azure per il servizio MySQL server non si comporta come previsto, tenere presente quanto segue:

* **Le modifiche all'elenco di consentiti non sono state ancora applicate:** per rendere effettive le modifiche apportate alla configurazione del firewall del database di Azure per server MySQL possono essere necessari fino a cinque minuti.

* **Autenticazione non riuscita:** Se un utente non dispone delle autorizzazioni per il database di Azure per il server MySQL o la password usata non è corretta, la connessione al database di Azure per il server MySQL viene negata. La creazione di un'impostazione del firewall fornisce solo ai client la possibilità di provare a connettersi al server. Ogni client deve comunque fornire le credenziali di sicurezza necessarie.

* **Indirizzo IP client dinamico:** Se si dispone di una connessione Internet con indirizzi IP dinamici e si verificano problemi durante l'accesso al firewall, è possibile provare una delle soluzioni seguenti:

   * Chiedere al provider di servizi Internet (ISP) l'intervallo di indirizzi IP assegnato ai computer client che accedono al database di Azure per il server MySQL, quindi aggiungere l'intervallo di indirizzi IP come regola del firewall.
   * Ottenere indirizzi IP statici per i computer client, quindi aggiungere gli indirizzi IP statici come una regola del firewall.


## <a name="hostname"></a>nomehost
Indipendentemente dall'opzione di rete scelta, è consigliabile utilizzare sempre un nome di dominio completo (FQDN) come nome host per la connessione al server flessibile. Non è garantito che l'indirizzo IP del server rimanga statico. L'utilizzo del nome di dominio completo consente di evitare di apportare modifiche alla stringa di connessione. 

Uno scenario in cui le modifiche IP si verificano se si usa la disponibilità elevata con ridondanza della zona e si verifica un failover tra il database primario e il database secondario. Utilizzando il nome di dominio completo, è possibile ritentare facilmente le connessioni con la stessa stringa di connessione.

Esempio
* Consigliabile `hostname = servername.mysql.database.azure.com`
* Evitare di usare `hostname = 10.0.0.4` (indirizzo privato) o `hostname = 40.2.45.67` (IP pubblico)



## <a name="tls-and-ssl"></a>TLS e SSL
Il server flessibile database di Azure per MySQL supporta la connessione delle applicazioni client al servizio MySQL usando Transport Layer Security (TLS). TLS è un protocollo standard del settore che garantisce connessioni di rete crittografate tra il server di database e le applicazioni client. TLS è un protocollo aggiornato di Secure Sockets Layer (SSL).

Il server flessibile database di Azure per MySQL supporta solo connessioni crittografate con Transport Layer Security (TLS 1,2). Tutte le connessioni in ingresso con TLS 1,0 e TLS 1,1 verranno negate. Non è possibile disabilitare o modificare la versione di TLS per la connessione al server flessibile di database di Azure per MySQL.


## <a name="next-steps"></a>Passaggi successivi
* Informazioni su come abilitare l'accesso privato (integrazione rete virtuale) usando il [portale di Azure](how-to-manage-virtual-network-portal.md) o l'[interfaccia della riga di comando di Azure](how-to-manage-virtual-network-cli.md)
* Informazioni su come abilitare l'accesso pubblico (indirizzi IP consentiti) usando il [portale di Azure](how-to-manage-firewall-portal.md) o l'[interfaccia della riga di comando di Azure](how-to-manage-firewall-cli.md)
* Informazioni su come [usare TLS](how-to-connect-tls-ssl.md)
