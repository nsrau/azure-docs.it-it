---
title: Risolvere i problemi di connettività di rete-griglia di eventi di Azure | Microsoft Docs
description: Questo articolo fornisce informazioni sulla risoluzione dei problemi di connettività di rete con griglia di eventi di Azure.
author: batrived
ms.topic: article
ms.date: 06/21/2020
ms.author: batrived
ms.openlocfilehash: 5eb40d464fb718f0bd6dffe0d00f6420f4ea4995
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/08/2020
ms.locfileid: "86119005"
---
# <a name="troubleshoot-connectivity-issues---azure-event-grid"></a>Risolvere i problemi di connettività-griglia di eventi di Azure

Esistono diversi motivi per cui le applicazioni client non sono in grado di connettersi a un argomento o dominio di griglia di eventi. I problemi di connettività che si verificano potrebbero essere permanenti o temporanei. Se il problema si verifica sempre (permanente), è consigliabile controllare le impostazioni del firewall dell'organizzazione, le impostazioni del firewall IP, i tag di servizio, gli endpoint privati e altro ancora. Per i problemi temporanei, l'esecuzione di comandi per verificare i pacchetti eliminati e il recupero di tracce di rete possono essere utili per la risoluzione dei problemi.

Questo articolo fornisce suggerimenti per la risoluzione dei problemi di connettività con griglia di eventi di Azure.

## <a name="troubleshoot-permanent-connectivity-issues"></a>Risolvere i problemi di connettività permanenti

Se l'applicazione non è in grado di connettersi alla griglia di eventi, attenersi alla procedura descritta in questa sezione per risolvere il problema.

### <a name="check-if-there-is-a-service-outage"></a>Controllare se è presente un'interruzione del servizio

Verificare l'interruzione del servizio griglia di eventi di Azure nel [sito stato dei servizi di Azure](https://azure.microsoft.com/status/).

### <a name="check-if-the-ports-required-to-communicate-with-event-grid-arent-blocked-by-organizations-firewall"></a>Verificare che le porte necessarie per comunicare con griglia di eventi non siano bloccate dal firewall dell'organizzazione

Verificare che le porte usate per la comunicazione con griglia di eventi di Azure non siano bloccate nel firewall dell'organizzazione. Vedere la tabella seguente per le porte in uscita che è necessario aprire per comunicare con griglia di eventi di Azure.

| Protocollo | Porte |
| -------- | ----- |
| HTTPS    | 443   |

Ecco un comando di esempio che verifica se la porta 443 è bloccata.

```powershell
.\psping.exe -n 25 -i 1 -q {sampletopicname}.{region}-{suffix}.eventgrid.azure.net:443 -nobanner
```

In Linux:

```shell
telnet {sampletopicname}.{region}-{suffix}.eventgrid.azure.net 443
```

### <a name="verify-that-ip-addresses-are-allowed-in-your-corporate-firewall"></a>Verificare che gli indirizzi IP siano consentiti nel firewall aziendale

Quando si lavora con Azure, a volte è necessario consentire URL o intervalli di indirizzi IP specifici nel firewall o nel proxy aziendale per accedere a tutti i servizi di Azure in uso o che si sta tentando di usare. Verificare che il traffico sia consentito sugli indirizzi IP usati da griglia di eventi. Per gli indirizzi IP usati da griglia di eventi di Azure: vedere [intervalli IP e tag di servizio di Azure-cloud pubblico](https://www.microsoft.com/download/details.aspx?id=56519) e [tag del servizio-AzureEventGrid](network-security.md#service-tags).

> [!NOTE]
> È possibile aggiungere nuovi indirizzi IP al tag del servizio AzureEventGrid, anche se non è usuale. Quindi, è opportuno eseguire un controllo settimanale sui tag del servizio.

### <a name="verify-that-azureeventgrid-service-tag-is-allowed-in-your-network-security-groups"></a>Verificare che il tag del servizio AzureEventGrid sia consentito nei gruppi di sicurezza di rete

Se l'applicazione è in esecuzione all'interno di una subnet e se è presente un gruppo di sicurezza di rete associato, verificare che sia consentito Internet in uscita o che sia consentito il tag del servizio AzureEventGrid. Vedere i [tag di servizio](../virtual-network/service-tags-overview.md)

### <a name="check-the-ip-firewall-settings-for-your-topicdomain"></a>Controllare le impostazioni del firewall IP per l'argomento o il dominio

Verificare che l'indirizzo IP pubblico del computer in cui è in esecuzione l'applicazione non sia bloccato dall'argomento EventGrid/firewall IP del dominio.

Per impostazione predefinita, gli argomenti/domini della griglia di eventi sono accessibili da Internet purché la richiesta venga fornita con autenticazione e autorizzazione valide. Con un firewall per gli indirizzi IP, è possibile limitare ulteriormente l'accesso a un set di indirizzi IPv4 o a intervalli di indirizzi IPv4 in notazione [CIDR (Classless Inter-Domain Routing)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing).

Le regole del firewall IP vengono applicate a livello di argomento/dominio di griglia di eventi. Vengono pertanto applicate a tutte le connessioni provenienti dai client con qualsiasi protocollo supportato. Qualsiasi tentativo di connessione da un indirizzo IP che non corrisponde a una regola IP consentita nell'argomento o dominio di griglia di eventi viene rifiutato come non consentito. Nella risposta non viene fatto riferimento alla regola IP.

Per altre informazioni, vedere [configurare le regole del firewall IP per un argomento/dominio di griglia di eventi di Azure](configure-firewall.md).

#### <a name="find-the-ip-addresses-blocked-by-ip-firewall"></a>Trovare gli indirizzi IP bloccati dal firewall IP

Abilitare i log di diagnostica per l'argomento o il dominio di griglia di eventi [abilitare i log di diagnostica](enable-diagnostic-logs-topic.md#enable-diagnostic-logs-for-a-custom-topic). Viene visualizzato l'indirizzo IP per la connessione negata.

```json
{
  "time": "2019-11-01T00:17:13.4389048Z",
  "resourceId": "/SUBSCRIPTIONS/SAMPLE-SUBSCTIPTION-ID/RESOURCEGROUPS/SAMPLE-RESOURCEGROUP-NAME/PROVIDERS/MICROSOFT.EVENTGRID/TOPICS/SAMPLE-TOPIC-NAME",
  "category": "PublishFailures",
  "operationName": "Post",
  "message": "inputEventsCount=null, requestUri=https://SAMPLE-TOPIC-NAME.region-suffix.eventgrid.azure.net/api/events, publisherInfo=PublisherInfo(category=User, inputSchema=EventGridEvent, armResourceId=/SUBSCRIPTIONS/SAMPLE-SUBSCTIPTION-ID/RESOURCEGROUPS/SAMPLE-RESOURCEGROUP-NAME/PROVIDERS/MICROSOFT.EVENTGRID/TOPICS/SAMPLE-TOPIC-NAME), httpStatusCode=Forbidden, errorType=ClientIPRejected, errorMessage=Publishing to SAMPLE-TOPIC-NAME.{region}-{suffix}.EVENTGRID.AZURE.NET by client {clientIp} is rejected due to IpAddress filtering rules."
}
```

### <a name="check-if-the-eventgrid-topicdomain-can-be-accessed-using-only-a-private-endpoint"></a>Controllare se è possibile accedere all'argomento o al dominio EventGrid usando solo un endpoint privato

Se l'argomento o il dominio di griglia di eventi è configurato per essere accessibile solo tramite endpoint privato, verificare che l'applicazione client acceda all'argomento o al dominio sull'endpoint privato. Per confermare questo problema, controllare se l'applicazione client è in esecuzione all'interno di una subnet ed esiste un endpoint privato per l'argomento o il dominio di griglia di eventi in tale subnet.

Il [servizio di collegamento privato di Azure](../private-link/private-link-overview.md) consente di accedere a griglia di eventi di Azure tramite un **endpoint privato** nella rete virtuale. Un endpoint privato è un'interfaccia di rete che connette privatamente e in modo sicuro a un servizio basato su Collegamento privato di Azure. L'endpoint privato usa un indirizzo IP privato della rete virtuale, introducendo efficacemente il servizio nella rete virtuale. Tutto il traffico verso il servizio può essere instradato tramite l'endpoint privato, quindi non sono necessari gateway, dispositivi NAT, ExpressRoute o connessioni VPN oppure indirizzi IP pubblici. Il traffico tra la rete virtuale e il servizio attraversa la rete backbone Microsoft, impedendone l'esposizione alla rete Internet pubblica. È possibile connettersi a un'istanza di una risorsa di Azure, garantendo il massimo livello di granularità nel controllo di accesso.

Per altre informazioni, vedere [configurare endpoint privati](configure-private-endpoints.md).

## <a name="troubleshoot-transient-connectivity-issues"></a>Risolvere i problemi di connettività temporanei

Se si verificano problemi di connettività intermittenti, vedere le sezioni seguenti per suggerimenti sulla risoluzione dei problemi.

### <a name="run-the-command-to-check-dropped-packets"></a>Eseguire il comando per controllare i pacchetti eliminati

Quando si verificano problemi di connettività intermittenti, eseguire il comando seguente per verificare se sono presenti pacchetti eliminati. Questo comando tenterà di stabilire 25 connessioni TCP diverse ogni secondo con il servizio. Quindi, è possibile controllare il numero di riuscite/non riuscite e vedere anche latenza di connessione TCP. È possibile scaricare lo `psping` strumento da [qui](/sysinternals/downloads/psping).

```shell
.\psping.exe -n 25 -i 1 -q {sampletopicname}.{region}-{suffix}.eventgrid.azure.net:443 -nobanner
```

È possibile utilizzare comandi equivalenti se si utilizzano altri strumenti, ad esempio `tcpping` [tcpping.exe](https://www.elifulkerson.com/projects/tcping.php).

Ottenere una traccia di rete se i passaggi precedenti non sono utili e analizzarli tramite strumenti come [Wireshark](https://www.wireshark.org/). Se necessario, contattare [supporto tecnico Microsoft](https://support.microsoft.com/) .

### <a name="service-upgradesrestarts"></a>Aggiornamenti/riavvii del servizio

Potrebbero verificarsi problemi di connettività temporanei a causa degli aggiornamenti del servizio back-end e dei riavvii. Quando si verificano, è possibile che vengano visualizzati i sintomi seguenti:

- Potrebbe essere presente un calo nei messaggi/richieste in arrivo.
- Il file di log può contenere messaggi di errore.
- È possibile che le applicazioni siano disconnesse dal servizio per alcuni secondi.
- Le richieste possono essere temporaneamente limitate.

Il rilevamento di questi errori temporanei, il backup e quindi la ripetizione della chiamata assicurerà che il codice sia resiliente a questi problemi temporanei.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, pubblicare il problema nel [Forum Stack Overflow](https://stackoverflow.com/questions/tagged/azure-eventgrid) oppure aprire un [ticket di supporto](https://azure.microsoft.com/support/options/).
