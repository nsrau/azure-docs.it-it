---
title: Cenni preliminari su configurazione Gateway applicazione di Azure
description: Questo articolo fornisce informazioni su come configurare i diversi componenti nel Gateway applicazione di Azure
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 03/20/2019
ms.author: absha
ms.openlocfilehash: 61b3a9e066a3ee20effa97f1c6c7a0bd1ae90ac0
ms.sourcegitcommit: 8a59b051b283a72765e7d9ac9dd0586f37018d30
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58285839"
---
# <a name="application-gateway-configuration-overview"></a>Panoramica configurazione del Gateway applicazione

Il gateway applicazione è costituito da diversi componenti che possono essere configurati in modi diversi per portare a termine scenari diversi. Questo articolo illustra come è necessario configurare ogni componente.

![application-gateway-components](./media/configuration-overview/configuration-overview1.png)

L'immagine di esempio precedente illustra una configurazione di un'applicazione con listener di 3. I primi due sono i listener multisito per `http://acme.com/*` e `http://fabrikam.com/*`, rispettivamente. Entrambi sono in ascolto sulla porta 80. Il terzo listener è un listener di base con la terminazione SSL to end. 

## <a name="prerequisites"></a>Prerequisiti

### <a name="azure-virtual-network-and-dedicated-subnet"></a>Rete virtuale di Azure e la subnet dedicata

Il gateway applicazione è una distribuzione dedicata nella rete virtuale. All'interno della rete virtuale, una subnet dedicata è necessaria per il gateway applicazione. Si può avere più istanze di una distribuzione del gateway applicazione specificata in questa subnet. È anche possibile distribuire altri gateway applicazione nella subnet, ma non è possibile distribuire qualsiasi altra risorsa nella subnet del gateway applicazione.  

> [!NOTE]   
> La combinazione dei gateway applicazione Standard_v2 e Standard nella stessa subnet non è supportata.

#### <a name="size-of-the-subnet"></a>Dimensioni della subnet

Il gateway applicazione utilizza un indirizzo IP privato per ogni istanza, oltre a un altro indirizzo IP privato in presenza di una configurazione degli indirizzi IP front-end privati. Inoltre, Azure riserva i primi quattro e l'ultimo indirizzo IP in ogni subnet per uso interno. Ad esempio, se un gateway applicazione è impostato su tre istanze e senza indirizzo IP privato front-end, quindi composta da almeno otto indirizzi IP saranno necessario nella subnet - cinque indirizzi IP per uso interno e tre gli indirizzi IP per le tre istanze del gateway applicazione. Pertanto, in questo caso/29 serve una subnet di dimensioni pari o superiore. Se si dispone di tre istanze e di indirizzi IP per la configurazione IP front-end privato, nove gli indirizzi IP sarà richiesto - tre indirizzi IP per le tre istanze del gateway applicazione, un indirizzo IP privato front-end IP e indirizzo IP cinque indirizzi per uso interno. Pertanto, in questo caso/28 serve una subnet di dimensioni pari o superiore.

Come procedura consigliata, usare almeno/28 le dimensioni della subnet. In questo modo si 11 indirizzi utilizzabili. Se il carico dell'applicazione richiede più di 10 istanze, è necessario considerare/27 o/26 le dimensioni della subnet.

#### <a name="network-security-groups-supported-on-the-application-gateway-subnet"></a>Gruppi di sicurezza di rete supportati nella subnet del Gateway applicazione

Gruppi di sicurezza di rete (Nsg) sono supportati nella subnet del Gateway applicazione con le restrizioni seguenti: 

- Devono essere inserite eccezioni per il traffico in ingresso sulle porte 65503-65534 per lo SKU versione 1 del gateway applicazione e sulle porte 65200-65535 per lo SKU versione 2. Questo intervallo di porte è necessario per la comunicazione di infrastruttura di Azure. Sono protette (bloccate) dai certificati di Azure. Senza certificati appropriati, le entità esterne, compresi i clienti di questi gateway, non saranno in grado di avviare alcuna modifica su tali endpoint.

- La connettività Internet in uscita non può essere bloccata. Le regole in uscita predefinite in sicurezza di rete già consentono la connettività internet. Si consiglia di non rimuovere le regole in uscita predefinite e che non è possibile creare altre regole in uscita che nega la connettività internet in uscita.

- È necessario consentire il traffico dal tag AzureLoadBalancer.

##### <a name="whitelist-application-gateway-access-to-a-few-source-ips"></a>Accesso al Gateway di applicazione di elenco elementi consentiti da alcuni IP di origine

Questo scenario è possibile usando gruppi di sicurezza di rete nella subnet del gateway applicazione. Nella subnet nell'ordine di priorità elencato, è necessario inserire le restrizioni seguenti:

1. Consentire il traffico in ingresso dall'intervallo di IP/IP di origine.
2. Consentire le richieste in ingresso da tutte le origini alle porte 65503-65534 per la [comunicazione integrità back-end](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics). Questo intervallo di porte è necessario per la comunicazione di infrastruttura di Azure. Sono protette (bloccate) dai certificati di Azure. Senza certificati appropriati, le entità esterne, compresi i clienti di questi gateway, non saranno in grado di avviare alcuna modifica su tali endpoint.
3. Consentire probe di bilanciamento del carico di Azure in ingresso (tag AzureLoadBalancer) e il traffico di rete virtuale in ingresso (tag VirtualNetwork) nei [gruppi di sicurezza di rete](https://docs.microsoft.com/azure/virtual-network/security-overview).
4. Bloccare tutto il traffico in ingresso con una regola Nega tutto.
5. Consentire il traffico in uscita a Internet per tutte le destinazioni.

#### <a name="user-defined-routes-supported-on-the-application-gateway-subnet"></a>Route definite dall'utente supportate nella subnet del Gateway applicazione

In caso di SKU v1, route definite dall'utente (Udr) sono supportate nella subnet del gateway applicazione, purché non modificano la comunicazione end-to-end richiesta/risposta. Ad esempio, è possibile configurare una route UDR nella subnet del gateway applicazione in modo che punti a un'appliance firewall per l'ispezione dei pacchetti, ma è necessario assicurarsi che il pacchetto possa raggiungere l'ispezione dei post relativa alla destinazione prevista. In caso contrario, potrebbe tradursi in probe di integrità o comportamento di instradamento del traffico non corretti. Sono incluse route o route 0.0.0.0/0 predefinite propagate dal gateway VPN o ExpressRoute nella rete virtuale.

In caso di v2 SKU, route definite dall'utente nella subnet del gateway applicazione non sono supportati. Per altre informazioni, vedere [Gateway applicazione con scalabilità automatica e ridondanza della zona (anteprima pubblica)](https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant#known-issues-and-limitations).

> [!NOTE]
> L'uso di route definite dall'utente nella subnet del gateway applicazione comporta lo stato di integrità nel [visualizzazione dell'integrità back-end](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics#back-end-health) da visualizzare come **sconosciuto** e comporterà inoltre verificato della generazione di log del gateway applicazione e metriche. Si consiglia che non utilizzare route definite dall'utente nella subnet del gateway applicazione sia in grado di visualizzare l'integrità back-end, log e metriche.

## <a name="frontend-ip"></a>IP front-end

È possibile configurare il gateway applicazione per disporre di un indirizzo IP pubblico o un indirizzo IP privato oppure entrambi. Indirizzo IP pubblico è obbligatorio quando si ospita un back-end che deve essere accessibile dai client su internet tramite un indirizzo VIP con connessione Internet. Indirizzo IP pubblico non è necessaria per un endpoint interno non esposto a Internet, noto anche come un endpoint di servizio di bilanciamento del carico interno. Configurare il gateway con un ILB è utile per le applicazioni line-of-business interne non esposte a Internet. È utile anche per servizi e livelli in un'applicazione a più livelli posti entro un limite di sicurezza non esposto a Internet, ma che richiedono la distribuzione del carico round robin, la persistenza delle sessioni o la terminazione Secure Sockets Layer (SSL).

È supportato un solo indirizzo IP pubblico o un indirizzo IP privato. Si sceglie l'indirizzo IP front-end durante la creazione del Gateway applicazione. 

- In caso di un indirizzo IP pubblico, è possibile scegliere di creare un nuovo indirizzo IP pubblico oppure usare un IP pubblico esistente nella stessa posizione del gateway applicazione. Se si crea un nuovo indirizzo IP pubblico, il tipo di indirizzo IP selezionato (statico o dinamico) non può essere modificato in un secondo momento. Per altre informazioni, vedere [porte statiche e indirizzo IP pubblico dinamico](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#static-vs-dynamic-public-ip) 

- In caso di un indirizzo IP privato, è possibile scegliere di specificare un indirizzo IP privato dalla subnet in cui viene creato il Gateway applicazione. Se non specificato in modo esplicito, un indirizzo IP arbitrario verrà selezionato automaticamente dalla subnet. Per altre informazioni, vedere [creare un gateway applicazione con un endpoint di servizio di bilanciamento del carico interno.](https://docs.microsoft.com/azure/application-gateway/application-gateway-ilb-arm)

È associato a un indirizzo IP Frontend *listener* che controlla le richieste in ingresso in Frontend IP.

## <a name="listeners"></a>Listener

Un listener è un'entità logica che controlla le richieste di connessione in ingresso, usando la porta, protocollo, host e indirizzo IP. Pertanto, quando si configura il listener che è necessario immettere tali valori della porta, protocollo, host e indirizzi IP che sono uguali a quelli i valori corrispondenti nella richiesta in ingresso nel gateway. Quando si crea un gateway applicazione usando il portale di Azure, viene creato anche un listener predefinito scegliendo il protocollo e porta del listener. È inoltre possibile scegliere se si desidera abilitare il supporto di HTTP2 nel listener. Dopo aver creato il Gateway applicazione, è possibile modificare l'impostazione di questo listener predefinito (*appGatewayHttpListener*/*appGatewayHttpsListener*) e/o creazione di nuovi listener.

### <a name="listener-type"></a>Tipo di listener

È possibile scegliere tra [listener di base o multisito](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#types-of-listeners) durante la creazione di un nuovo listener. 

- Se si ospita un singolo sito dietro un gateway applicazione, scegliere listener di base. Scopri [come creare un gateway applicazione con listener di base](https://docs.microsoft.com/azure/application-gateway/quick-create-portal).

- Se si sta configurando più di un'applicazione web o più sottodomini dello stesso dominio padre nella stessa istanza del gateway applicazione, quindi scegliere listener multisito. Per il listener multisito, sarà inoltre necessario immettere un nome host. Questo avviene perché il Gateway applicazione si basa su intestazioni host HTTP 1.1 per ospitare più siti Web sul stesso indirizzo IP pubblico e sulla porta.

#### <a name="order-of-processing-listeners"></a>Ordine di elaborazione listener

In caso di SKU v1, i listener vengono elaborati nell'ordine in cui sono visibili. Per tale motivo, se un listener di base corrisponde a una richiesta in ingresso, questa viene elaborata per prima. Di conseguenza, i listener multisito devono essere configurati prima di un listener di base per verificare che il traffico viene indirizzato al back-end corretto.

In caso di SKU v2, vengono elaborati i listener multisito prima i listener di base.

### <a name="frontend-ip"></a>IP front-end

Scegliere l'indirizzo IP front-end che si desidera associare a questo listener. Rimarrà in ascolto il listener per la richiesta in ingresso in questo indirizzo IP.

### <a name="frontend-port"></a>Porta front-end

Scegliere la porta front-end. È possibile scegliere tra le porte esistenti o crearne uno nuovo. È possibile scegliere qualsiasi valore compreso il [intervallo di porte consentito](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#ports). Questo consente non soltanto Usa porte note, ad esempio 80 e 443 ma ciascuno può porta personalizzata appropriata per l'uso. Una porta può essere usata per listener con connessione pubblico o privato listener affiancate.

### <a name="protocol"></a>Protocollo

È necessario scegliere tra i protocolli HTTP e HTTPS. 

- Se si sceglie HTTP, il traffico tra il client e il gateway applicazione scorrerà non crittografati.

- Selezionare HTTPS se si è interessati [terminazione Secure Sockets Layer (SSL)](https://docs.microsoft.com/azure/application-gateway/overview#secure-sockets-layer-ssl-terminationl) oppure [crittografia SSL end-to-end](https://docs.microsoft.com/azure/application-gateway/ssl-overview). Se si sceglie di HTTPS, il traffico tra il client e il gateway applicazione verrà crittografato e verrà terminata la connessione SSL nel gateway applicazione.  Se si desidera crittografia SSL end-to-end, sarà inoltre necessario scegliere il protocollo HTTPS durante la configurazione *impostazione back-end HTTP*. Ciò garantisce che il traffico viene nuovamente crittografato quando viene trasmesso dal back-end del Gateway applicazione.

  Per configurare la terminazione Secure Sockets Layer (SSL) e crittografia SSL end-to-end, è necessario un certificato da aggiungere al listener in modo da consentire al Gateway applicazione derivare una chiave simmetrica in base alla specifica del protocollo SSL. La chiave simmetrica viene quindi usata per crittografare e decrittografare il traffico inviato al gateway. Il certificato del gateway deve essere in formato PFX (Personal Information Exchange). Questo formato di file consente l'esportazione della chiave privata necessaria al gateway applicazione per eseguire la crittografia e la decrittografia del traffico. 

#### <a name="supported-certificates"></a>Certificati supportati

Visualizzare [certificati supportati per la terminazione SSL](https://docs.microsoft.com/azure/application-gateway/ssl-overview#certificates-supported-for-ssl-termination).

### <a name="additional-protocol-support"></a>Supporto dei protocolli aggiuntivi

#### <a name="http2-support"></a>Supporto di HTTP2

Il supporto del protocollo HTTP/2 è disponibile per i client che si connettono solo a listener del gateway applicazione. La comunicazione con i pool di server back-end avviene tramite HTTP/1.1. Per impostazione predefinita, il supporto di HTTP/2 è disabilitato. Il frammento di codice di esempio seguente, di Azure PowerShell, illustra come abilitarlo:

```azurepowershell
$gw = Get-AzureRmApplicationGateway -Name test -ResourceGroupName hm

$gw.EnableHttp2 = $true

Set-AzureRmApplicationGateway -ApplicationGateway $gw
```

#### <a name="websocket-support"></a>Supporto di WebSocket

Supporto di WebSocket è abilitato per impostazione predefinita. Non esistono impostazioni configurabili dall'utente per abilitare o disabilitare in modo selettivo il supporto di WebSocket. È possibile usare i WebSocket con listener HTTP e HTTPS. 

### <a name="custom-error-page"></a>Pagina errori personalizzati

Pagine errori personalizzati possono essere definite a livello globale, nonché il livello di listener, tuttavia, la creazione di pagine di errore personalizzate a livello globale dal portale di Azure non è attualmente supportata. È possibile configurare una pagina di manutenzione 502 o una pagina di errore personalizzato per un errore 403 di Web Application firewall a livello di listener. È anche necessario specificare un URL blob accessibile pubblicamente per il codice di stato di errore specificato. Per altre informazioni, vedere [pagina di errore personalizzato crea](https://docs.microsoft.com/azure/application-gateway/custom-error).

![Codici di errore del gateway applicazione](https://docs.microsoft.com/azure/application-gateway/media/custom-error/ag-error-codes.png)

Per configurare una pagina di errore personalizzata globale, usare [Azure PowerShell per la configurazione](https://docs.microsoft.com/azure/application-gateway/custom-error#azure-powershell-configuration) 

### <a name="ssl-policy"></a>Criteri SSL

È possibile centralizzare la gestione dei certificati SSL e ridurre il sovraccarico di crittografia e decrittografia da una farm di server back-end. Questa gestione centralizzata dei certificati SSL permette anche di specificare criteri SSL centrali adatti ai requisiti di sicurezza dell'organizzazione.  È possibile scegliere tra l'impostazione predefinita, predefiniti e i criteri SSL personalizzati. 

È possibile configurare criteri SSL per gestire le versioni del protocollo SSL. È possibile configurare i gateway applicazione per rifiutare TLS1.0, TLS1.1 e TLS1.2. SSL 2.0 e 3.0 sono già disabilitati per impostazione predefinita e non sono configurabili. Per altre informazioni, vedere [Cenni preliminari sui criteri SSL per il Gateway applicazione](https://docs.microsoft.com/azure/application-gateway/application-gateway-ssl-policy-overview).

Dopo aver creato un listener, è associarlo a una regola di routing di richiesta che determina come deve essere indirizzato al back-end la richiesta ricevuta nel listener.

## <a name="request-routing-rule"></a>Regola richiesta routing

Quando si crea il gateway applicazione usando il portale di Azure, si crea una regola predefinita (*rule1*), che associa il listener predefinito (*appGatewayHttpListener*) con il pool di back-end predefinito (*appGatewayBackendPool*) e le impostazioni di back-end HTTP predefinito (*appGatewayBackendHttpSettings*). Dopo aver creato il gateway applicazione, è possibile modificare l'impostazione di questa regola predefinita e/o creare nuove regole.

### <a name="rule-type"></a>Tipo regola

È possibile scegliere tra [regola di base o basato su percorsi](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#request-routing-rule) durante la creazione di una nuova regola. 

- Se si desidera inoltrare tutte le richieste sul listener associati (ad esempio: blog.contoso.com/*) a un pool di back-end singolo, selezionare listener di base. 
- Scegliere come listener basato su percorso se si vuole indirizzare le richieste con percorso dell'URL specifico per i pool back-end specifico. Il modello di percorso viene applicato solo al percorso dell'URL, non ai relativi parametri di query.


#### <a name="order-of-processing-rules"></a>Ordine delle regole di elaborazione

In caso di SKU v1, corrispondenza del modello di richiesta in ingresso viene elaborata nell'ordine in cui sono elencati i percorsi nella mappa del percorso URL della regola basata sul percorso. Per questo motivo, se una richiesta corrisponde al modello in due o più percorsi nella mappa del percorso URL, quindi il percorso incluso nell'elenco prima di tutto si troverà una corrispondenza e la richiesta verrà inoltrata al back-end associato a tale percorso.

In caso di SKU v2, una corrispondenza esatta contiene priorità sull'ordine in cui sono elencati i percorsi nella mappa del percorso URL. Per tale motivo, se una richiesta corrisponde al modello in due o più percorsi, quindi la richiesta verrà inoltrata al back-end associato a tale percorso che corrisponde esattamente alla richiesta. Se il percorso nella richiesta in ingresso corrisponde esattamente a qualsiasi percorso nella mappa del percorso URL, quindi di corrispondenza del modello di richiesta in ingresso viene elaborata nell'ordine in cui sono elencati i percorsi nella mappa del percorso URL della regola basata sul percorso.

### <a name="associated-listener"></a>Listener associati

È necessario associare un listener per la regola in modo che il *regola richiesta routing* associati con il *listener* viene valutata per determinare il *pool back-end* a cui il richiesta deve essere instradato.

### <a name="associated-backend-pool"></a>Back-end associati Pool

Associare il pool back-end che contiene le destinazioni di back-end che soddisferà le richieste ricevute dal listener. In caso di una regola di base, un solo pool di back-end è consentito perché tutte le richieste sul listener associati verranno inoltrate a questo pool back-end. In caso di una regola basata sul percorso, aggiungere più pool di back-end corrispondente a ciascun percorso URL. Le richieste che corrispondono al percorso URL immesso in questo caso, verranno inoltrati al pool back-end corrispondente. Inoltre, aggiungere un pool di back-end predefinito poiché le richieste che non corrispondono ad alcun percorso dell'URL immesso in questa regola verranno inoltrate a esso.

### <a name="associated-backend-http-setting"></a>Impostazione HTTP back-end associati

Aggiungere un'impostazione HTTP back-end per ogni regola. Verranno indirizzate le richieste dal Gateway applicazione per le destinazioni di back-end usando il numero di porta, protocollo e altre impostazioni specificate in questa impostazione. In caso di una regola di base, è consentita una sola impostazione di back-end HTTP perché tutte le richieste sul listener associati verranno inoltrate alle destinazioni di back-end corrispondenti con questa impostazione HTTP. In caso di una regola basata sul percorso, aggiungere più impostazioni di back-end HTTP corrispondente a ciascun percorso URL. Verranno inoltrate le richieste che corrispondono al percorso URL immesso in questo caso, usando le impostazioni HTTP corrispondente a ciascun percorso URL nelle destinazioni dei back-end corrispondente. Inoltre, aggiungere un'impostazione HTTP predefinita poiché le richieste che non corrispondono ad alcun percorso dell'URL immesso in questa regola verranno inoltrate al pool back-end predefinito usando l'impostazione HTTP predefinito.

### <a name="redirection-setting"></a>Impostazione di reindirizzamento

Se il reindirizzamento è configurato per una regola di base, tutte le richieste sul listener associati verranno reindirizzate alla destinazione di reindirizzamento, abilitando in questo modo il reindirizzamento globale. Se il reindirizzamento è configurato per una regola basata sul percorso, le richieste solo in un'area specifica del sito, ad esempio un carrello area indicata da /Cart/ *, verrà reindirizzato alla destinazione di reindirizzamento, abilitando in questo modo il reindirizzamento basato sul percorso. 

Per informazioni sulla funzionalità di reindirizzamento, vedere [Panoramica del reindirizzamento](https://docs.microsoft.com/azure/application-gateway/redirect-overview).

- #### <a name="redirection-type"></a>Tipo di reindirizzamento

  Scegliere il tipo di reindirizzamento richiesto da: Other(303) Permanent(301), Temporary(307), Found(302) o vedere.

- #### <a name="redirection-target"></a>Destinazione di reindirizzamento

  È possibile scegliere tra un sito esterno o un altro listener come destinazione di reindirizzamento. 

  - ##### <a name="listener"></a>Listener

    Listener scegliendo come destinazione di reindirizzamento consente il reindirizzamento da un listener a un altro listener nel gateway. Questa impostazione è necessaria quando si desidera abilitare HTTP per il reindirizzamento HTTPS, ad esempio, il traffico di reindirizzamento dal listener di origine di controllo per le richieste HTTP in ingresso al listener di destinazione di controllo per le richieste HTTPS in ingresso. È anche possibile scegliere la stringa di query e il percorso nella richiesta originale da includere nella richiesta inoltrata alla destinazione di reindirizzamento.![application-gateway-components](./media/configuration-overview/configure-redirection.png)

    Per altre informazioni sul protocollo HTTP per il reindirizzamento HTTPS, vedere [reindirizzamento da HTTP a HTTP tramite portale](https://docs.microsoft.com/azure/application-gateway/redirect-http-to-https-portal), [reindirizzamento da HTTP a HTTP tramite PowerShell](https://docs.microsoft.com/azure/application-gateway/redirect-http-to-https-powershell), [reindirizzamento da HTTP a HTTP tramite CLI](https://docs.microsoft.com/azure/application-gateway/redirect-http-to-https-cli)

  - ##### <a name="external-site"></a>Sito esterno

    Quando si desidera reindirizzare il traffico nel listener associato alla regola pertanto essere reindirizzati a un sito esterno, scegliere sito esterno. È possibile scegliere la stringa di query nella richiesta originale da includere nella richiesta inoltrata alla destinazione di reindirizzamento. Non è possibile inoltrare il percorso nella richiesta originale al sito esterno.

    Per altre informazioni sul reindirizzamento al sito esterno, vedere [reindirizzare il traffico al sito esterno usando PowerShell](https://docs.microsoft.com/azure/application-gateway/redirect-external-site-powershell) e [https://docs.microsoft.com/azure/application-gateway/redirect-external-site-cli](https://docs.microsoft.com/azure/application-gateway/redirect-external-site-cli)

#### <a name="rewrite-http-header-setting"></a>Riscrivere l'impostazione dell'intestazione HTTP

Questa funzionalità consente di aggiungere, rimuovere o aggiornare le intestazioni di richiesta e risposta HTTP durante la richiesta e spostare pacchetti di risposta tra il pool back-end e client.    È possibile configurare questa funzionalità solo tramite PowerShell. Non è ancora disponibile il supporto di portale e della riga di comando. Per altre informazioni, vedere [intestazioni HTTP riscrivere](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers) Cenni preliminari e [riscrittura dell'intestazione HTTP configurare](https://docs.microsoft.com/azure/application-gateway/add-http-header-rewrite-rule-powershell#specify-your-http-header-rewrite-rule-configuration).

## <a name="http-settings"></a>Impostazioni HTTP

Il gateway applicazione instrada il traffico ai server di back-end tramite la configurazione specificata in questo componente. Dopo aver creato un'impostazione HTTP, è necessario associarlo a uno o più regole di routing di richieste.

### <a name="cookie-based-affinity"></a>Affinità basata su cookie

Questa funzionalità è utile quando si desidera mantenere una sessione utente nello stesso server. Usando cookie gestiti dal gateway, il gateway applicazione può indirizzare il traffico successivo proveniente da una sessione utente allo stesso server per l'elaborazione. Questo è importante nei casi in cui lo stato della sessione viene salvato in locale sul server per una sessione utente. Se l'applicazione non è possibile gestire l'affinità basata su cookie, quindi non sarà in grado di usare questa funzionalità. Per usare l'affinità di sessione basata su cookie, è necessario assicurarsi che il client deve supportare i cookie. 

### <a name="connection-draining"></a>Esaurimento delle connessioni

Lo svuotamento delle connessioni aiuta a rimuovere in modo controllato i membri del pool back-end durante gli aggiornamenti pianificati del servizio. Questa impostazione può essere applicata a tutti i membri di un pool di back-end durante la creazione della regola. Una volta abilitato, il gateway applicazione assicura che tutte le istanze di annullare la registrazione di un pool di back-end non ricevono tutte le nuove richieste, consentendo le richieste esistenti per il completamento entro un limite di tempo configurato. Questo vale sia per le istanze back-end che vengono rimosse dal pool back-end in modo esplicito mediante una chiamata API, sia per le istanze back-end che vengono segnalate come non integre, come determinato dai probe di integrità.

### <a name="protocol"></a>Protocollo

Il gateway applicazione supporta i protocolli HTTP e HTTPS per il routing delle richieste ai server back-end. Se si sceglie il protocollo HTTP, quindi il traffico transita non crittografato verso i server back-end. In questi casi in cui comunicazioni non crittografate verso i server back-end non sono un'opzione accettabile, è consigliabile scegliere il protocollo HTTPS. Questa impostazione combinato con sceglie il protocollo HTTPS nel listener di consente di abilitare [SSL end-to-end](https://docs.microsoft.com/azure/application-gateway/ssl-overview). Che consente di trasmettere in modo sicuro dati sensibili nel back-end crittografato. Per una comunicazione protetta, ogni server back-end nel pool di back-end con SSL end-to-end abilitato deve essere configurato con un certificato.

### <a name="port"></a>Porta

Questa è la porta in ascolto il server back-end per il traffico proveniente dal Gateway applicazione. È possibile configurare le porte compreso tra 1 e 65535.

### <a name="request-timeout"></a>Timeout richiesta

Il numero di secondi di attesa del gateway applicazione per ricevere risposta dal pool di back-end prima di restituire un errore "Timeout della connessione".

### <a name="override-backend-path"></a>Override del percorso back-end

Questa impostazione consente di configurare un percorso di inoltro personalizzato facoltativo da utilizzare per la richiesta viene inoltrata al back-end. Verranno copiati qualsiasi parte del percorso in ingresso che corrisponde al percorso personalizzato specificato nel **sostituire il percorso di back-end** campo percorso inoltrato. Vedere la tabella seguente per comprendere come funziona la funzionalità.

- Quando l'impostazione HTTP è associato a una regola di routing di base richiesta:

  | Richiesta originale  | Override del percorso back-end | Richiesta inoltrata al back-end |
  | ----------------- | --------------------- | ---------------------------- |
  | /Home/            | /override/            | / sostituzione/home /              |
  | / home/secondhome / | /override/            | / sostituzione/home/secondhome /   |

- Quando l'impostazione HTTP è associato a una regola di routing basato sul percorso richiesta:

  | Richiesta originale           | Regola di percorso       | Override del percorso back-end | Richiesta inoltrata al back-end |
  | -------------------------- | --------------- | --------------------- | ---------------------------- |
  | /pathrule/home /            | /pathrule*      | /override/            | / sostituzione/home /              |
  | regola di percorso/home/secondhome / | /pathrule*      | /override/            | / sostituzione/home/secondhome /   |
  | /Home/                     | /pathrule*      | /override/            | / sostituzione/home /              |
  | / home/secondhome /          | /pathrule*      | /override/            | / sostituzione/home/secondhome /   |
  | /pathrule/home /            | regola di percorso/home page * | /override/            | /override/                   |
  | regola di percorso/home/secondhome / | regola di percorso/home page * | /override/            | / sostituzione/secondhome /        |

### <a name="use-for-app-service"></a>Usa per il servizio app

Si tratta di un collegamento dell'interfaccia utente che consente di selezionare le due impostazioni necessarie per back-end del servizio App: consente di selezionare il nome host di indirizzi back-end e crea un nuovo probe personalizzato. Il motivo per cui viene eseguita prima descritto nella sezione relativa al **scegliere il nome host di indirizzi back-end** impostazione. Viene creato un nuovo probe in cui l'intestazione di probe anche viene prelevato dall'indirizzo del membro di back-end.

### <a name="use-custom-probe"></a>Usa probe personalizzato

Questa impostazione viene utilizzata per associare un [probe personalizzato](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview#custom-health-probe) con questa impostazione HTTP. È possibile associare solo un probe personalizzato a un'impostazione HTTP. Se si non associa in modo esplicito un probe personalizzato, quindi [probe predefinito](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview#default-health-probe-settings) verrà usato per monitorare l'integrità del back-end. È consigliabile creare un probe personalizzato per un controllo più granulare sul monitoraggio dell'integrità del back-end.

> [!NOTE]   
> Probe personalizzato non verrà avviato il monitoraggio dell'integrità del pool back-end a meno che l'impostazione HTTP corrispondente sia associato in modo esplicito a un listener.

### <a name="pick-host-name-from-backend-address"></a>Nome host di selezione dall'indirizzo back-end

Questa funzionalità imposta in modo dinamico il *host* intestazione nella richiesta per il nome host del pool back-end usando un indirizzo IP o nome di dominio completo (FQDN). Ciò è utile negli scenari in cui il nome di dominio del back-end è diverso dal nome DNS del gateway applicazione e il back-end si basa su un'intestazione host specifica estensione SNI o per risolvere l'endpoint corretto, ad esempio nel caso dei servizi multi-tenant come il back-end. Poiché il servizio App è un servizio multi-tenant con un'area condivisa con un singolo indirizzo IP, un servizio App sono accessibili solo con i nomi host configurato nelle impostazioni di dominio personalizzato. Per impostazione predefinita, è il nome di dominio personalizzato *example.azurewebsites.net*. Pertanto, se si desidera accedere al servizio App usando il gateway applicazione con entrambi un nome host registrati in servizio App non esplicito o con nome di dominio completo del gateway applicazione, è necessario eseguire l'override del nome host nella richiesta originale al nome host del servizio App, abilitazione **scegliere il nome host di indirizzi back-end** impostazione.

Se si possiede un dominio personalizzato e aver mappato il nome DNS personalizzato esistente per il servizio App, non è necessario abilitare questa impostazione.

> [!NOTE]   
> Poiché l'ambiente del servizio App è una distribuzione dedicata, questa impostazione non è necessaria per ambiente del servizio App (ASE). 

### <a name="host-name-override"></a>Override del nome host

Questa funzionalità sostituisce la *host* intestazione nella richiesta in ingresso nel gateway applicazione con il nome host specificato qui. Ad esempio, se www\.contoso.com è specificato come i **nome Host** impostazione, la richiesta originale https://appgw.eastus.cloudapp.net/path1 verrà modificata in https://www.contoso.com/path1 quando la richiesta viene inoltrata al server back-end. 

## <a name="backend-pool"></a>Pool back-end

Un pool back-end può essere puntato a quattro tipi di membri di back-end: un servizio app, set di scalabilità di macchine virtuali, un indirizzo IP/FQDN o una macchina virtuale specifica. Ogni pool back-end può puntare a più membri dello stesso tipo. Verso i membri di tipi diversi nello stesso pool back-end non è supportato. 

Dopo aver creato un pool back-end, è necessario associarlo a uno o più regole di routing richiesta. È anche necessario configurare il probe di integrità per ogni pool back-end nel gateway applicazione. Quando viene soddisfatta una condizione della regola di routing di richiesta, il gateway applicazione inoltra il traffico ai server Integro (come determinato dal probe di integrità) nel pool di back-end corrispondente.

## <a name="health-probes"></a>Probe di integrità

Anche se il gateway applicazione monitora l'integrità di tutte le risorse nel relativo back-end per impostazione predefinita, si consiglia vivamente che creare un probe personalizzato per ogni impostazione HTTP back-end in modo da avere un controllo più granulare sul monitoraggio dell'integrità. Per informazioni su come configurare il probe di integrità personalizzati, vedere [impostazioni del probe di integrità personalizzato](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview#custom-health-probe-settings).

> [!NOTE]   
> Dopo aver creato un probe di integrità personalizzati, è necessario associarlo a un'impostazione di back-end HTTP. Probe personalizzato non verrà avviato il monitoraggio dell'integrità del pool back-end a meno che l'impostazione HTTP corrispondente sia associato in modo esplicito a un listener.

## <a name="next-steps"></a>Passaggi successivi

Dopo aver acquisito familiarità con i componenti di Gateway applicazione, è possibile:

- [Creare un Gateway applicazione nel portale di Azure](quick-create-portal.md)
- [Creare un Gateway applicazione usando PowerShell](quick-create-powershell.md)
- [Creare un Gateway applicazione con Azure CLI](quick-create-cli.md)
