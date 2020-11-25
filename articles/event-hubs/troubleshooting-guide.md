---
title: Risolvere i problemi di connettività-Hub eventi di Azure | Microsoft Docs
description: Questo articolo fornisce informazioni sulla risoluzione dei problemi di connettività con hub eventi di Azure.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 8eddc0e8c598e4553b30759d179fecb6ae880829
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/21/2020
ms.locfileid: "96012681"
---
# <a name="troubleshoot-connectivity-issues---azure-event-hubs"></a>Risolvere i problemi di connettività-Hub eventi di Azure
Esistono diversi motivi per cui le applicazioni client non sono in grado di connettersi a un hub eventi. I problemi di connettività che si verificano potrebbero essere permanenti o temporanei. Se il problema si verifica sempre (permanente), è consigliabile controllare la stringa di connessione, le impostazioni del firewall dell'organizzazione, le impostazioni del firewall IP, le impostazioni di sicurezza di rete (endpoint di servizio, endpoint privati e così via) e altro ancora. Per i problemi temporanei, l'aggiornamento alla versione più recente dell'SDK, l'esecuzione di comandi per controllare i pacchetti eliminati e il recupero di tracce di rete possono essere utili per la risoluzione dei problemi. 

Questo articolo fornisce suggerimenti per la risoluzione dei problemi di connettività con hub eventi di Azure. 

## <a name="troubleshoot-permanent-connectivity-issues"></a>Risolvere i problemi di connettività permanenti
Se l'applicazione non è in grado di connettersi all'hub eventi, attenersi alla procedura descritta in questa sezione per risolvere il problema. 

### <a name="check-if-there-is-a-service-outage"></a>Controllare se è presente un'interruzione del servizio
Verificare l'interruzione del servizio Hub eventi di Azure nel [sito stato dei servizi di Azure](https://azure.microsoft.com/status/).

### <a name="verify-the-connection-string"></a>Verificare la stringa di connessione 
Verificare che la stringa di connessione utilizzata sia corretta. Vedere [ottenere](event-hubs-get-connection-string.md) la stringa di connessione per ottenere la stringa di connessione usando il portale di Azure, l'interfaccia della riga di comando o PowerShell. 

Per i client Kafka, verificare che i file producer.config o consumer.config siano configurati correttamente. Per altre informazioni, vedere [inviare e ricevere messaggi con Kafka in hub eventi](event-hubs-quickstart-kafka-enabled-event-hubs.md#send-and-receive-messages-with-kafka-in-event-hubs).

[!INCLUDE [event-hubs-connectivity](../../includes/event-hubs-connectivity.md)]

### <a name="verify-that-azureeventgrid-service-tag-is-allowed-in-your-network-security-groups"></a>Verificare che il tag del servizio AzureEventGrid sia consentito nei gruppi di sicurezza di rete
Se l'applicazione è in esecuzione all'interno di una subnet ed è presente un gruppo di sicurezza di rete associato, verificare se è consentito il servizio Internet in uscita o se è consentito il tag del servizio AzureEventGrid. Vedere [tag del servizio di rete virtuale](../virtual-network/service-tags-overview.md) e cercare `EventHub` .

### <a name="check-if-the-application-needs-to-be-running-in-a-specific-subnet-of-a-vnet"></a>Controllare se l'applicazione deve essere in esecuzione in una subnet specifica di un VNET
Verificare che l'applicazione sia in esecuzione in una subnet di rete virtuale che abbia accesso allo spazio dei nomi. In caso contrario, eseguire l'applicazione nella subnet che ha accesso allo spazio dei nomi o aggiungere l'indirizzo IP del computer in cui è in esecuzione l'applicazione al [firewall IP](event-hubs-ip-filtering.md). 

Quando si crea un endpoint del servizio rete virtuale per uno spazio dei nomi di hub eventi, lo spazio dei nomi accetta il traffico solo dalla subnet associata all'endpoint del servizio. Si è verificata un'eccezione a questo comportamento. È possibile aggiungere indirizzi IP specifici nel firewall IP per abilitare l'accesso all'endpoint pubblico dell'hub eventi. Per altre informazioni, vedere [endpoint del servizio di rete](event-hubs-service-endpoints.md).

### <a name="check-the-ip-firewall-settings-for-your-namespace"></a>Controllare le impostazioni del firewall IP per lo spazio dei nomi
Verificare che l'indirizzo IP pubblico del computer in cui è in esecuzione l'applicazione non sia bloccato dal firewall IP.  

Per impostazione predefinita, gli spazi dei nomi di Hub eventi sono accessibili da Internet, purché la richiesta sia accompagnata da un'autenticazione e da un'autorizzazione valide. Con un firewall per gli indirizzi IP, è possibile limitare ulteriormente l'accesso a un set di indirizzi IPv4 o a intervalli di indirizzi IPv4 in notazione [CIDR (Classless Inter-Domain Routing)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing).

Le regole del firewall IP vengono applicate a livello dello spazio dei nomi di Hub eventi. Vengono pertanto applicate a tutte le connessioni provenienti dai client con qualsiasi protocollo supportato. Qualsiasi tentativo di connessione proveniente da un indirizzo IP che non corrisponde a una regola di indirizzi IP consentiti nello spazio dei nomi di Hub eventi viene rifiutato come non autorizzato. Nella risposta non viene fatto riferimento alla regola IP. Le regole del filtro IP vengono applicate in ordine e la prima regola corrispondente all'indirizzo IP determina l'azione di accettazione o rifiuto.

Per altre informazioni, vedere [configurare le regole del firewall IP per uno spazio dei nomi di hub eventi di Azure](event-hubs-ip-filtering.md). Per verificare se sono presenti problemi di filtro IP, rete virtuale o catena di certificati, vedere risolvere i problemi [relativi alla rete](#troubleshoot-network-related-issues).

### <a name="check-if-the-namespace-can-be-accessed-using-only-a-private-endpoint"></a>Controllare se è possibile accedere allo spazio dei nomi usando solo un endpoint privato
Se lo spazio dei nomi di hub eventi è configurato per essere accessibile solo tramite endpoint privato, verificare che l'applicazione client acceda allo spazio dei nomi sull'endpoint privato. 

Il [servizio di collegamento privato di Azure](../private-link/private-link-overview.md) consente di accedere a hub eventi di Azure tramite un **endpoint privato** nella rete virtuale. Un endpoint privato è un'interfaccia di rete che connette privatamente e in modo sicuro a un servizio basato su Collegamento privato di Azure. L'endpoint privato usa un indirizzo IP privato della rete virtuale, portando il servizio nella rete virtuale. Tutto il traffico verso il servizio può essere instradato tramite l'endpoint privato, quindi non sono necessari gateway, dispositivi NAT, ExpressRoute o connessioni VPN oppure indirizzi IP pubblici. Il traffico tra la rete virtuale e il servizio attraversa la rete backbone Microsoft, impedendone l'esposizione alla rete Internet pubblica. È possibile connettersi a un'istanza di una risorsa di Azure, garantendo il massimo livello di granularità nel controllo di accesso.

Per altre informazioni, vedere [configurare endpoint privati](private-link-service.md). Vedere la sezione **verificare che la connessione all'endpoint privato funzioni** per verificare che venga usato un endpoint privato. 

### <a name="troubleshoot-network-related-issues"></a>Risolvere i problemi relativi alla rete
Per risolvere i problemi relativi alla rete con hub eventi, attenersi alla procedura seguente: 

Passare a o [wget](https://www.gnu.org/software/wget/) `https://<yournamespacename>.servicebus.windows.net/` . Consente di controllare se sono presenti problemi di filtro IP, rete virtuale o catena di certificati (più comuni quando si usa Java SDK).

Esempio di **messaggio riuscito**:

```xml
<feed xmlns="http://www.w3.org/2005/Atom"><title type="text">Publicly Listed Services</title><subtitle type="text">This is the list of publicly-listed services currently available.</subtitle><id>uuid:27fcd1e2-3a99-44b1-8f1e-3e92b52f0171;id=30</id><updated>2019-12-27T13:11:47Z</updated><generator>Service Bus 1.1</generator></feed>
```

Esempio di **messaggio di errore di errore**:

```json
<Error>
    <Code>400</Code>
    <Detail>
        Bad Request. To know more visit https://aka.ms/sbResourceMgrExceptions. . TrackingId:b786d4d1-cbaf-47a8-a3d1-be689cda2a98_G22, SystemTracker:NoSystemTracker, Timestamp:2019-12-27T13:12:40
    </Detail>
</Error>
```

## <a name="troubleshoot-transient-connectivity-issues"></a>Risolvere i problemi di connettività temporanei
Se si verificano problemi di connettività intermittenti, vedere le sezioni seguenti per suggerimenti sulla risoluzione dei problemi. 

### <a name="use-the-latest-version-of-the-client-sdk"></a>Usare la versione più recente dell'SDK client
Alcuni dei problemi di connettività temporanei potrebbero essere stati corretti nelle versioni successive dell'SDK rispetto a quello che si sta usando. Assicurarsi di usare la versione più recente degli SDK client nelle applicazioni. Gli SDK sono costantemente migliorati con funzionalità nuove o aggiornate e correzioni di bug, quindi è sempre consigliabile eseguire test con il pacchetto più recente. Controllare le note sulla versione per i problemi corretti e le funzionalità aggiunte o aggiornate. 

Per informazioni sugli SDK client, vedere l'articolo [Hub eventi di Azure-SDK client](sdks.md) . 

### <a name="run-the-command-to-check-dropped-packets"></a>Eseguire il comando per controllare i pacchetti eliminati
Quando si verificano problemi di connettività intermittenti, eseguire il comando seguente per verificare se sono presenti pacchetti eliminati. Questo comando tenterà di stabilire 25 connessioni TCP diverse ogni secondo con il servizio. Quindi, è possibile controllare il numero di riuscite/non riuscite e vedere anche latenza di connessione TCP. È possibile scaricare lo `psping` strumento da [qui](/sysinternals/downloads/psping).

```shell
.\psping.exe -n 25 -i 1 -q <yournamespacename>.servicebus.windows.net:5671 -nobanner     
```
È possibile utilizzare comandi equivalenti se si utilizzano altri strumenti, ad esempio `tnc` , `ping` e così via. 

Ottenere una traccia di rete se i passaggi precedenti non sono utili e analizzarli tramite strumenti come [Wireshark](https://www.wireshark.org/). Se necessario, contattare [supporto tecnico Microsoft](https://support.microsoft.com/) . 

### <a name="service-upgradesrestarts"></a>Aggiornamenti/riavvii del servizio
Potrebbero verificarsi problemi di connettività temporanei a causa degli aggiornamenti del servizio back-end e dei riavvii. Quando si verificano, è possibile che vengano visualizzati i sintomi seguenti: 

- Potrebbe essere presente un calo nei messaggi/richieste in arrivo.
- Il file di log può contenere messaggi di errore.
- È possibile che le applicazioni siano disconnesse dal servizio per alcuni secondi.
- Le richieste possono essere temporaneamente limitate.

Se il codice dell'applicazione usa l'SDK, i criteri di ripetizione dei tentativi sono già incorporati e attivi. L'applicazione si riconnetterà senza conseguenze significative per l'applicazione o il flusso di lavoro. Il rilevamento di questi errori temporanei, il backup e quindi la ripetizione della chiamata assicurerà che il codice sia resiliente a questi problemi temporanei.

## <a name="next-steps"></a>Passaggi successivi
Vedere gli articoli seguenti:

* [Risolvere i problemi di autenticazione e autorizzazione](troubleshoot-authentication-authorization.md)
