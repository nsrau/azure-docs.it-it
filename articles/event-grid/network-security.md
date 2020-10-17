---
title: Sicurezza di rete per le risorse di griglia di eventi di Azure
description: Questo articolo descrive come usare i tag di servizio per l'uscita, le regole del firewall IP per il traffico in ingresso e gli endpoint privati per l'ingresso con griglia di eventi di Azure.
author: VidyaKukke
ms.topic: conceptual
ms.date: 07/07/2020
ms.author: vkukke
ms.openlocfilehash: 84336051fc3d653fbe73f650f2fc2badb2ec58da
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/17/2020
ms.locfileid: "92148930"
---
# <a name="network-security-for-azure-event-grid-resources"></a>Sicurezza di rete per le risorse di griglia di eventi di Azure
Questo articolo descrive come usare le funzionalità di sicurezza seguenti con griglia di eventi di Azure: 

- Tag di servizio per l'uscita
- Regole del firewall IP per il traffico in ingresso (anteprima)
- Endpoint privati per il traffico in ingresso


## <a name="service-tags"></a>Tag di servizio
Un tag del servizio rappresenta un gruppo di prefissi di indirizzi IP di un determinato servizio di Azure. Microsoft gestisce i prefissi di indirizzo inclusi nel tag del servizio e aggiorna automaticamente il tag in base alla modifica degli indirizzi, riducendo la complessità degli aggiornamenti frequenti alle regole di sicurezza di rete. Per altre informazioni sui tag di servizio, vedere [Cenni preliminari sui tag di servizio](../virtual-network/service-tags-overview.md).

È possibile usare i tag del servizio per definire i controlli di accesso alla rete nei [gruppi di sicurezza di rete](../virtual-network/network-security-groups-overview.md#security-rules) o in [Firewall di Azure](../firewall/service-tags.md). Usare i tag del servizio anziché indirizzi IP specifici quando si creano regole di sicurezza. Specificando il nome del tag di servizio (ad esempio, **AzureEventGrid**) nel *source*   campo di origine o di *destinazione*appropriato   di una regola, è possibile consentire o negare il traffico per il servizio corrispondente.

| Tag di servizio | Scopo | È possibile usarlo in ingresso o in uscita? | Può essere regionale? | È possibile usarlo con Firewall di Azure? |
| --- | -------- |:---:|:---:|:---:|
| AzureEventGrid | Griglia di eventi di Azure. | Entrambe | No | No |


## <a name="ip-firewall"></a>Firewall IP 
Griglia di eventi di Azure supporta i controlli di accesso basati su IP per la pubblicazione in argomenti e domini. Con i controlli basati su IP, è possibile limitare i Publisher a un argomento o un dominio solo a un set di computer e servizi cloud approvati. Questa funzionalità è complementare ai [meccanismi di autenticazione](security-authentication.md) supportati da griglia di eventi.

Per impostazione predefinita, l'argomento e il dominio sono accessibili da Internet, purché la richiesta venga fornita con autenticazione e autorizzazione valide. Con il firewall IP, è possibile limitarlo ulteriormente a un set di indirizzi IP o intervalli di indirizzi IP nella notazione [CIDR (Inter-Domain routing senza classi)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) . Gli autori che hanno origine da altri indirizzi IP verranno rifiutati e riceveranno una risposta 403 (accesso negato).

Per istruzioni dettagliate su come configurare il firewall IP per argomenti e domini, vedere Configurare il [firewall IP](configure-firewall.md).

## <a name="private-endpoints"></a>Endpoint privati
È possibile usare [endpoint privati](../private-link/private-endpoint-overview.md) per consentire l'ingresso di eventi direttamente dalla rete virtuale agli argomenti e ai domini in modo sicuro tramite un [collegamento privato](../private-link/private-link-overview.md) senza passare attraverso la rete Internet pubblica. Un endpoint privato è un'interfaccia di rete speciale per un servizio di Azure nella VNet. Quando si crea un endpoint privato per l'argomento o il dominio, fornisce connettività sicura tra i client nella VNet e la risorsa di griglia di eventi. All'endpoint privato viene assegnato un indirizzo IP dall'intervallo di indirizzi IP della VNet. La connessione tra l'endpoint privato e il servizio griglia di eventi usa un collegamento privato protetto.

![Diagramma dell'architettura](./media/network-security/architecture-diagram.png)

L'uso di endpoint privati per la risorsa griglia di eventi consente di:

- Proteggere l'accesso all'argomento o al dominio da un VNet su una rete backbone Microsoft anziché da Internet pubblico.
- Connettersi in modo sicuro da reti locali che si connettono al VNet tramite VPN o delle expressroute con peering privato.

Quando si crea un endpoint privato per un argomento o un dominio nel VNet, viene inviata una richiesta di consenso per l'approvazione al proprietario della risorsa. Se l'utente che richiede la creazione dell'endpoint privato è anche un proprietario della risorsa, questa richiesta di consenso viene approvata automaticamente. In caso contrario, la connessione è in stato di **attesa** fino all'approvazione. Le applicazioni in VNet possono connettersi senza interruzioni al servizio griglia di eventi tramite l'endpoint privato, usando le stesse stringhe di connessione e i meccanismi di autorizzazione usati in altro modo. I proprietari delle risorse possono gestire le richieste di consenso e gli endpoint privati, tramite la scheda **endpoint privati** per la risorsa nel portale di Azure.

### <a name="connect-to-private-endpoints"></a>Connettersi agli endpoint privati
I Publisher in una VNet che usa l'endpoint privato devono usare la stessa stringa di connessione per l'argomento o il dominio come client che si connettono all'endpoint pubblico. La risoluzione DNS instrada automaticamente le connessioni da VNet all'argomento o al dominio tramite un collegamento privato. Griglia di eventi crea una [zona DNS privata](../dns/private-dns-overview.md) collegata a VNet con l'aggiornamento necessario per gli endpoint privati, per impostazione predefinita. Tuttavia, se si usa il proprio server DNS, potrebbe essere necessario apportare altre modifiche alla configurazione DNS.

### <a name="dns-changes-for-private-endpoints"></a>Modifiche DNS per gli endpoint privati
Quando si crea un endpoint privato, il record CNAME DNS per la risorsa viene aggiornato a un alias in un sottodominio con il prefisso `privatelink` . Per impostazione predefinita, viene creata una zona DNS privata che corrisponde al sottodominio del collegamento privato. 

Quando si risolve l'argomento o l'URL dell'endpoint del dominio dall'esterno del VNet con l'endpoint privato, viene risolto nell'endpoint pubblico del servizio. I record di risorse DNS per ' topica ', quando risolti dall' **esterno del VNet** che ospita l'endpoint privato, saranno:

| Nome                                          | Type      | valore                                         |
| --------------------------------------------- | ----------| --------------------------------------------- |  
| `topicA.westus.eventgrid.azure.net`             | CNAME     | `topicA.westus.privatelink.eventgrid.azure.net` |
| `topicA.westus.privatelink.eventgrid.azure.net` | CNAME     | \<Azure traffic manager profile\>

È possibile negare o controllare l'accesso per un client esterno a VNet tramite l'endpoint pubblico usando il [firewall IP](#ip-firewall). 

Quando viene risolto da VNet che ospita l'endpoint privato, l'argomento o l'URL dell'endpoint di dominio viene risolto nell'indirizzo IP dell'endpoint privato. I record di risorse DNS per l'argomento ' topica ', quando risolti dall' **interno di VNet** che ospita l'endpoint privato, saranno:

| Nome                                          | Type      | valore                                         |
| --------------------------------------------- | ----------| --------------------------------------------- |  
| `topicA.westus.eventgrid.azure.net`             | CNAME     | `topicA.westus.privatelink.eventgrid.azure.net` |
| `topicA.westus.privatelink.eventgrid.azure.net` | A         | 10.0.0.5

Questo approccio consente di accedere all'argomento o al dominio usando la stessa stringa di connessione per i client in VNet che ospitano gli endpoint privati e i client esterni al VNet.

Se si usa un server DNS personalizzato nella rete, i client possono risolvere l'FQDN per l'argomento o l'endpoint di dominio nell'indirizzo IP dell'endpoint privato. Configurare il server DNS per delegare il sottodominio di collegamento privato alla zona DNS privata per la VNet o configurare i record A per `topicOrDomainName.regionName.privatelink.eventgrid.azure.net` con l'indirizzo IP dell'endpoint privato.

Il nome della zona DNS consigliata è `privatelink.eventgrid.azure.net` .

### <a name="private-endpoints-and-publishing"></a>Endpoint privati e pubblicazione

Nella tabella seguente vengono descritti i vari Stati della connessione all'endpoint privato e gli effetti sulla pubblicazione:

| Stato connessione   |  Pubblicazione completata (sì/no) |
| ------------------ | -------------------------------|
| Approved           | Sì                            |
| Rifiutato           | No                             |
| Pending            | No                             |
| Disconnesso       | No                             |

Per la corretta pubblicazione, lo stato di connessione dell'endpoint privato deve essere **approvato**. Se una connessione viene rifiutata, non può essere approvata utilizzando la portale di Azure. L'unica possibilità consiste nell'eliminare la connessione e crearne una nuova.

## <a name="pricing-and-quotas"></a>Prezzi e quote
Gli **endpoint privati** sono disponibili sia nei livelli Basic che Premium di griglia di eventi. Griglia di eventi consente di creare fino a 64 connessioni all'endpoint privato per ogni argomento o dominio. 

La funzionalità **firewall IP** è disponibile nei livelli Basic e Premium di griglia di eventi. È possibile creare fino a 16 regole del firewall IP per argomento o dominio.

## <a name="next-steps"></a>Passaggi successivi
È possibile configurare il firewall IP per la risorsa di griglia di eventi per limitare l'accesso tramite la rete Internet pubblica solo da un set di indirizzi IP o intervalli di indirizzi IP selezionati. Per istruzioni dettagliate, vedere [configurare il firewall IP](configure-firewall.md).

È possibile configurare endpoint privati per limitare l'accesso solo da reti virtuali selezionate. Per istruzioni dettagliate, vedere [configurare endpoint privati](configure-private-endpoints.md).

Per risolvere i problemi di connettività di rete, vedere [risolvere i problemi di connettività di rete](troubleshoot-network-connectivity.md)