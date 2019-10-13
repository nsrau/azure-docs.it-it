---
title: Gateway dati locale per Azure Analysis Services | Microsoft Docs
description: Un gateway locale è necessario se il server Analysis Services di Azure si connette a origini dati locali.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 8d9df32070ff252dff791650788888d1d9a6ce84
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/13/2019
ms.locfileid: "72294946"
---
# <a name="connecting-to-on-premises-data-sources-with-on-premises-data-gateway"></a>Connessione a origini dati locali con gateway dati locale

Il gateway dati locale garantisce il trasferimento sicuro dei dati tra le origini dati locali e i server Azure Analysis Services nel cloud. Oltre a lavorare con più server Azure Analysis Services nella stessa area, la versione più recente del gateway funziona anche con app per la logica di Azure, Power BI, PowerApps e Microsoft Flow. È possibile associare più servizi nella stessa sottoscrizione e nella stessa area con un singolo gateway. Sebbene il gateway installato sia lo stesso in tutti i servizi, Azure Analysis Services e le app per la logica hanno alcuni passaggi aggiuntivi.

Per Azure Analysis Services, il primo processo di configurazione con il gateway è un processo in quattro parti:

- **Scaricare ed eseguire il programma di installazione**: questo passaggio consente di installare un servizio gateway in un computer dell'organizzazione. È anche possibile accedere ad Azure usando un account in Azure AD del [tenant](/previous-versions/azure/azure-services/jj573650(v=azure.100)#what-is-an-azure-ad-tenant). Gli account Azure B2B (guest) non sono supportati.

- **Registrare il gateway**: in questo passaggio vengono specificati un nome e una chiave di ripristino per il gateway, si seleziona un'area e si registra il gateway con il servizio cloud Gateway. La risorsa gateway può essere registrata in qualsiasi area, ma è consigliabile registrarla nella stessa area dei server Analysis Services. 

- **Creare una risorsa per il gateway in Azure**: in questo passaggio, si crea una risorsa per il gateway nella sottoscrizione di Azure.

- **Connettere i server per la risorsa per il gateway**: dopo aver creato una risorsa per il gateway nella sottoscrizione, è possibile iniziare a connettere i server. È possibile connettere più server e altre risorse purché si trovino nella stessa sottoscrizione e nella stessa area.

## <a name="how-it-works"> </a>Funzionamento
Il gateway installato su un computer dell'organizzazione viene eseguito come servizio Windows, **Gateway dati locale**. Il servizio locale è registrato con il servizio Cloud Gateway tramite il bus di servizio di Azure. Si crea quindi una risorsa gateway dati locale per la sottoscrizione di Azure. I server di Azure Analysis Services vengono quindi connessi alla risorsa del gateway di Azure. Quando i modelli nel server devono connettersi ai dati locali di origine per le query o l'elaborazione, un flusso di dati e query attraversa la risorsa del gateway, il bus di servizio di Azure, il servizio gateway dati locale e le origini dati. 

![Funzionamento](./media/analysis-services-gateway/aas-gateway-how-it-works.png)

Query e flusso di dati:

1. Il servizio cloud crea una query con le credenziali crittografate per l'origine dati locale. La query viene inviata a una coda per l'elaborazione da parte del gateway.
2. Il servizio cloud del gateway analizza la query e inserisce la richiesta nel [bus di servizio di Azure](https://azure.microsoft.com/documentation/services/service-bus/).
3. Il gateway dati locale esegue il polling del bus di servizio per le richieste in sospeso.
4. Il gateway riceve la query, decrittografa le credenziali e si connette alle origini dati usando tali credenziali.
5. Invia quindi la query all'origine dati per l'esecuzione.
6. I risultati vengono inviati dall'origine dati al gateway e quindi al servizio cloud e al server.

## <a name="installing"></a>Installazione

Quando si installa per un ambiente di Azure Analysis Services, è importante attenersi alla procedura descritta in [installare e configurare il gateway dati locale per Azure Analysis Services](analysis-services-gateway-install.md). Questo articolo è specifico per Azure Analysis Services. Include passaggi aggiuntivi necessari per configurare una risorsa gateway dati locale in Azure e connettere il server Azure Analysis Services alla risorsa.

## <a name="ports-and-communication-settings"></a>Porte e impostazioni di comunicazione

Il gateway crea una connessione in uscita al bus di servizio di Azure. La comunicazione avviene sulle porte in uscita: TCP 443 (impostazione predefinita), 5671, 5672, 9350 attraverso 9354.  Non sono richieste porte in ingresso.

Potrebbe essere necessario inserire nell'elenco elementi consentiti gli indirizzi IP per l'area dati nel firewall. È possibile scaricare l'[elenco degli indirizzi IP dei data center di Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653). L'elenco viene aggiornato ogni settimana. Gli indirizzi IP inclusi nell'elenco degli indirizzi IP dei data center di Azure sono nella notazione CIDR. Per altre informazioni, vedere [Classless Inter-Domain Routing](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing).

Di seguito sono riportati i nomi di dominio completi usati dal gateway.

| Nomi di dominio | Porte in uscita | Descrizione |
| --- | --- | --- |
| *.powerbi.com |80 |HTTP usato per scaricare il programma di installazione. |
| *.powerbi.com |443 |HTTPS |
| *. analysis.windows.net |443 |HTTPS |
| *. login.windows.net, login.live.com, aadcdn.msauth.net |443 |HTTPS |
| *.servicebus.windows.net |5671-5672 |Advanced Message Queuing Protocol (AMQP) |
| *.servicebus.windows.net |443, 9350-9354 |Listener in Inoltro del Bus di servizio su TCP (richiede 443 per l'acquisizione del token di Controllo di accesso) |
| *.frontend.clouddatahub.net |443 |HTTPS |
| *.core.windows.net |443 |HTTPS |
| login.microsoftonline.com |443 |HTTPS |
| *.msftncsi.com |443 |Usato per testare la connettività a Internet se il gateway non è raggiungibile dal servizio Power BI. |
| *.microsoftonline-p.com |443 |Usato per l'autenticazione, a seconda della configurazione. |
| dc.services.visualstudio.com  |443 |Usato da AppInsights per raccogliere i dati di telemetria. |

### <a name="force-https"></a>Forzare la comunicazione HTTPS con il bus di servizio di Azure

È possibile forzare il gateway a comunicare con il bus di servizio di Azure usando HTTPS anziché TCP diretto. Questa operazione, tuttavia, può ridurre le prestazioni in misura significativa. È necessario modificare il file *Microsoft.PowerBI.DataMovement.Pipeline.GatewayCore.dll.config* modificando il valore da `AutoDetect` in `Https`. Questo file si trova solitamente in *C:\Programmi\On-premises data gateway*.

```
<setting name="ServiceBusSystemConnectivityModeString" serializeAs="String">
    <value>Https</value>
</setting>
```

## <a name="next-steps"></a>Passaggi successivi 

Gli articoli seguenti sono inclusi nel contenuto generale del gateway dati locale applicabile a tutti i servizi supportati dal gateway:

* [Domande frequenti sul gateway dati locale](https://docs.microsoft.com/data-integration/gateway/service-gateway-onprem-faq)   
* [Usare l'app gateway dati locale](https://docs.microsoft.com/data-integration/gateway/service-gateway-app)   
* [Amministrazione a livello di tenant](https://docs.microsoft.com/data-integration/gateway/service-gateway-tenant-level-admin)
* [Configurare le impostazioni proxy](https://docs.microsoft.com/data-integration/gateway/service-gateway-proxy)   
* [Modificare le impostazioni di comunicazione](https://docs.microsoft.com/data-integration/gateway/service-gateway-communication)   
* [Configurare i file di log](https://docs.microsoft.com/data-integration/gateway/service-gateway-log-files)   
* [Risolvere i problemi](https://docs.microsoft.com/data-integration/gateway/service-gateway-tshoot)
* [Monitorare e ottimizzare le prestazioni del gateway](https://docs.microsoft.com/data-integration/gateway/service-gateway-performance)
