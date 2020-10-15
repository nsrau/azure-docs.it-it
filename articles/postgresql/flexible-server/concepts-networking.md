---
title: Panoramica della rete-database di Azure per PostgreSQL-server flessibile
description: Informazioni sulle opzioni di connettività e rete nell'opzione di distribuzione flessibile del server per database di Azure per PostgreSQL
author: niklarin
ms.author: nlarin
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/23/2020
ms.openlocfilehash: 38dd103189446e287f3d62d93344ed89a364d238
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91708782"
---
# <a name="networking-overview---azure-database-for-postgresql---flexible-server"></a>Panoramica della rete-database di Azure per PostgreSQL-server flessibile

> [!IMPORTANT]
> Il server flessibile di Database di Azure per PostgreSQL è disponibile in anteprima

Questo articolo descrive i concetti di connettività e rete per database di Azure per PostgreSQL-server flessibili. 

## <a name="choosing-a-networking-option"></a>Scelta di un'opzione di rete
Sono disponibili due opzioni di rete per il database di Azure per PostgreSQL-server flessibile. Le opzioni sono **Accesso privato (integrazione rete virtuale)** e **Accesso pubblico (indirizzi IP consentiti)** . Al momento della creazione del server, è necessario selezionare un'opzione. 

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
* Le connessioni dagli indirizzi IP consentiti devono eseguire l'autenticazione al server PostgreSQL con credenziali valide
* La [crittografia della connessione](#tls-and-ssl) è disponibile per il traffico di rete
* Il server dispone di un nome di dominio completo (FQDN). Per la proprietà hostname nelle stringhe di connessione, è consigliabile utilizzare il nome di dominio completo anziché un indirizzo IP.
* Entrambe le opzioni controllano l'accesso a livello di server, non a livello di database o di tabella. Usare le proprietà dei ruoli di PostgreSQL per controllare l'accesso a database, tabelle e altri oggetti.


## <a name="private-access-vnet-integration"></a>Accesso privato (integrazione rete virtuale)
L'integrazione di accesso privato con rete virtuale (VNET) fornisce comunicazioni private e sicure per il server PostgreSQL flessibile.

### <a name="virtual-network-concepts"></a>Concetti relativi alla rete virtuale
Di seguito sono riportati alcuni concetti che è necessario conoscere quando si usano reti virtuali con server flessibili PostgreSQL.

* **Rete virtuale** : una rete virtuale di Azure (VNet) contiene uno spazio di indirizzi IP privato configurato per l'uso. Per altre informazioni sulle reti virtuali di Azure, vedere [Panoramica di rete virtuale](../../virtual-network/virtual-networks-overview.md) di Azure.

    La rete virtuale deve trovarsi nella stessa area di Azure del server flessibile.


* **Subnet delegata** : una rete virtuale contiene subnet (sottoreti). Le subnet consentono di segmentare la rete virtuale in spazi di indirizzi più piccoli. Le risorse di Azure vengono distribuite in subnet specifiche all'interno di una rete virtuale. 

   Il server flessibile PostgreSQL deve trovarsi in una subnet **delegata** solo per l'uso di server flessibili PostgreSQL. Questa delega indica che solo i server flessibili di database di Azure per PostgreSQL possono usare tale subnet. Gli altri tipi di risorsa di Azure non possono trovarsi nella subnet delegata. Si delega una subnet assegnando la relativa proprietà di delega come Microsoft. DBforPostgreSQL/flexibleServers.

* **Gruppi di sicurezza di rete (NSG)** Le regole di sicurezza nei gruppi di sicurezza di rete consentono di filtrare il tipo di traffico di rete che può fluire in entrata e in uscita dalle subnet della rete virtuale e dalle interfacce di rete. Per ulteriori informazioni, vedere la [Panoramica del gruppo di sicurezza di rete](../../virtual-network/network-security-groups-overview.md) .


### <a name="unsupported-virtual-network-scenarios"></a>Scenari di rete virtuale non supportati
* Endpoint pubblico (o IP pubblico o DNS): un server flessibile distribuito in una rete virtuale non può avere un endpoint pubblico
* Dopo che il server flessibile è stato distribuito in una rete virtuale e in una subnet, non è possibile spostarlo in un'altra rete virtuale o subnet. Non è possibile spostare la rete virtuale in un altro gruppo di risorse o sottoscrizione.
* Non è possibile aumentare le dimensioni della subnet (spazi degli indirizzi) dopo che sono presenti risorse nella subnet
* Il peering di reti virtuali tra aree non è supportato

Informazioni su come creare un server flessibile con accesso privato (VNet Integration) nell' [portale di Azure](how-to-manage-virtual-network-portal.md) o [nell'interfaccia della](how-to-manage-virtual-network-cli.md)riga di comando di Azure.

## <a name="public-access-allowed-ip-addresses"></a>Accesso pubblico (indirizzi IP consentiti)
Le caratteristiche del metodo di accesso pubblico includono:
* Solo gli indirizzi IP consentiti sono autorizzati ad accedere al server flessibile PostgreSQL. Per impostazione predefinita, non sono consentiti indirizzi IP. È possibile aggiungere gli indirizzi IP durante la creazione del server o successivamente.
* Il server PostgreSQL ha un nome DNS risolvibile pubblicamente
* Il server flessibile non si trova in una delle reti virtuali di Azure
* Il traffico di rete da e verso il server non passa attraverso una rete privata. Il traffico USA i percorsi Internet generali.

### <a name="firewall-rules"></a>Regole del firewall
La concessione dell'autorizzazione a un indirizzo IP è detta regola del firewall. Se un tentativo di connessione proviene da un indirizzo IP non consentito, il client di origine visualizzerà un errore.

Informazioni su come creare un server flessibile con accesso pubblico (indirizzi IP consentiti) nell' [portale di Azure](how-to-manage-firewall-portal.md) o [nell'interfaccia della](how-to-manage-firewall-cli.md)riga di comando di Azure.


### <a name="allowing-all-azure-ip-addresses"></a>Consentire tutti gli indirizzi IP di Azure
Se un indirizzo IP in uscita fisso non è disponibile per il servizio di Azure, è possibile prendere in considerazione l'abilitazione delle connessioni da tutti gli indirizzi IP dei Data Center di Azure.

> [!IMPORTANT]
> L'opzione **Consenti l'accesso pubblico da servizi e risorse di Azure in Azure** consente di configurare il firewall in modo da consentire tutte le connessioni da Azure, incluse le connessioni dalle sottoscrizioni di altri clienti. Quando si seleziona questa opzione, verificare che l'account di accesso e le autorizzazioni utente limitino l'accesso ai soli utenti autorizzati.

### <a name="troubleshooting-public-access-issues"></a>Risoluzione dei problemi di accesso pubblico
Quando si effettua l'accesso al servizio Database di Microsoft Azure per il server PostgreSQL non si comporta come previsto, considerare quanto segue:

* **Le modifiche all'elenco Consentiti non sono state ancora applicate:** per rendere effettive le modifiche apportate al firewall del Database di Azure per il server PostgreSQL possono essere necessari fino a cinque minuti.

* **Autenticazione non riuscita:** Se un utente non dispone delle autorizzazioni per il server di database di Azure per PostgreSQL o la password usata non è corretta, la connessione al database di Azure per il server PostgreSQL viene negata. La creazione di un'impostazione del firewall fornisce solo ai client la possibilità di provare a connettersi al server. Ogni client deve comunque fornire le credenziali di sicurezza necessarie.

* **Indirizzo IP client dinamico:** Se si dispone di una connessione Internet con indirizzi IP dinamici e si verificano problemi durante l'accesso al firewall, è possibile provare una delle soluzioni seguenti:

   * Richiedere al provider di servizi Internet (ISP) l'intervallo di indirizzi IP assegnato ai computer client che accedono al Database di Azure per il server MySQL, quindi aggiungere l'intervallo di indirizzi IP come regola firewall.
   * Ottenere indirizzi IP statici per i computer client, quindi aggiungere gli indirizzi IP statici come una regola del firewall.


## <a name="hostname"></a>nomehost
Indipendentemente dall'opzione di rete scelta, è consigliabile utilizzare sempre un nome di dominio completo (FQDN) come nome host per la connessione al server flessibile. Non è garantito che l'indirizzo IP del server rimanga statico. L'utilizzo del nome di dominio completo consente di evitare di apportare modifiche alla stringa di connessione. 

Esempio
* Consigliabile `hostname = servername.postgres.database.azure.com`
* Laddove possibile, evitare di usare `hostname = 10.0.0.4` (un indirizzo privato) o `hostname = 40.2.45.67` (un indirizzo pubblico)


## <a name="tls-and-ssl"></a>TLS e SSL
Database di Azure per PostgreSQL: il server flessibile supporta la connessione delle applicazioni client al servizio PostgreSQL usando Transport Layer Security (TLS). TLS è un protocollo standard del settore che garantisce connessioni di rete crittografate tra il server di database e le applicazioni client. TLS è un protocollo aggiornato di SSL (Secure Sockets Layer).

Database di Azure per PostgreSQL: il server flessibile supporta solo connessioni crittografate con Transport Layer Security. Tutte le connessioni in ingresso con TLS 1,0 e TLS 1,1 verranno negate. 

## <a name="next-steps"></a>Passaggi successivi
* Informazioni su come creare un server flessibile con **accesso privato (VNet Integration)** nell' [portale di Azure](how-to-manage-virtual-network-portal.md) o [nell'interfaccia della](how-to-manage-virtual-network-cli.md)riga di comando di Azure.
* Informazioni su come creare un server flessibile con **accesso pubblico (indirizzi IP consentiti)** nell' [portale di Azure](how-to-manage-firewall-portal.md) o [nell'interfaccia della](how-to-manage-firewall-cli.md)riga di comando di Azure.
