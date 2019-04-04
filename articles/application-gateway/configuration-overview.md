---
title: Cenni preliminari su configurazione Gateway applicazione di Azure
description: Questo articolo descrive come configurare i componenti del Gateway applicazione di Azure
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 03/20/2019
ms.author: absha
ms.openlocfilehash: 40c5444a54f4e483a9dcacb958c18f66da45019a
ms.sourcegitcommit: 9f4eb5a3758f8a1a6a58c33c2806fa2986f702cb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/03/2019
ms.locfileid: "58906124"
---
# <a name="application-gateway-configuration-overview"></a>Panoramica configurazione del Gateway applicazione

Gateway applicazione di Azure è costituito da diversi componenti che è possibile configurare in vari modi per scenari diversi. Questo articolo illustra come configurare ogni componente.

![Diagramma di flusso i componenti di Gateway applicazione](./media/configuration-overview/configuration-overview1.png)

Questa immagine illustra un'applicazione che dispone di tre listener. Le prime due sono i listener multisito per `http://acme.com/*` e `http://fabrikam.com/*`, rispettivamente. Sia in ascolto sulla porta 80. Il terzo è un listener di base con la terminazione Secure Sockets Layer (SSL) end-to-end.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Prerequisiti

### <a name="azure-virtual-network-and-dedicated-subnet"></a>Rete virtuale di Azure e la subnet dedicata

Un gateway applicazione è una distribuzione dedicata nella rete virtuale. All'interno della rete virtuale, una subnet dedicata è necessaria per il gateway applicazione. Si può avere più istanze di una distribuzione del gateway applicazione specificata in una subnet. È anche possibile distribuire altri gateway applicazione nella subnet. Ma non è possibile distribuire qualsiasi altra risorsa nella subnet del gateway applicazione.

> [!NOTE]
> È possibile combinare Standard_v2 e Gateway applicazione di Azure Standard nella stessa subnet.

#### <a name="size-of-the-subnet"></a>Dimensioni della subnet

Il Gateway applicazione usa 1 indirizzo IP privato per ogni istanza, più un altro indirizzo IP privato se è configurato un indirizzo IP di front-end privato.

Azure riserva inoltre i 5 indirizzi IP in ogni subnet per uso interno: l'indirizzo IP ultimo e i primi 4 indirizzi. Si consideri ad esempio 15 istanze del gateway applicazione con nessun indirizzo IP privato front-end. Sono necessari almeno 20 indirizzi IP per questa subnet: 5 per uso interno e 15 per le istanze del gateway applicazione. È quindi necessario/27 subnet dimensioni pari o superiori.

Prendere in considerazione una subnet con 27 istanze del gateway applicazione e un indirizzo IP per un indirizzo IP front-end privato. In questo caso, è necessario 33 gli indirizzi IP: 27 per le istanze del gateway applicazione, 1 per il front-end privato e 5 per uso interno. È quindi necessario un /26 subnet dimensioni pari o superiori.

È consigliabile usare una subnet con dimensioni di almeno/28. Queste dimensioni offre 11 indirizzi IP utilizzabili. Se il carico dell'applicazione richiede più di 10 indirizzi IP, prendere in considerazione/27 o/26 le dimensioni della subnet.

#### <a name="network-security-groups-on-the-application-gateway-subnet"></a>Gruppi di sicurezza di rete nella subnet del Gateway applicazione

Gruppi di sicurezza di rete (Nsg) sono supportati dal Gateway applicazione. Ma esistono diverse restrizioni:

- È necessario includere le eccezioni per il traffico in ingresso alle porte 65503-65534 per lo SKU di Gateway applicazione v1 e porte 65200-65535 per lo SKU v2. Questo intervallo di porte è necessario per la comunicazione di infrastruttura di Azure. Queste porte sono protette (bloccate) dai certificati di Azure. Le entità esterne, compresi i clienti di questi gateway, non possono avviare le modifiche su tali endpoint senza certificati appropriati posto.

- La connettività Internet in uscita non può essere bloccata. Le regole in uscita predefinite in sicurezza di rete consentono la connettività internet. È consigliabile:

  - Non rimuovere le regole in uscita predefinito.
  - Non creare altre regole in uscita che nega la connettività internet in uscita.

- Il traffico dal **AzureLoadBalancer** tag deve essere consentito.

##### <a name="whitelist-application-gateway-access-to-a-few-source-ips"></a>Accesso al Gateway di applicazione di elenco elementi consentiti da alcuni IP di origine

Per questo scenario, usare gli Nsg nella subnet del Gateway applicazione. Inserire le restrizioni seguenti sulla subnet in questo ordine di priorità:

1. Consentire il traffico in ingresso da un intervallo di IP/indirizzi IP di origine.
2. Consentire le richieste in ingresso da tutte le origini alle porte 65503-65534 per [comunicazione integrità back-end](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics). Questo intervallo di porte è necessario per la comunicazione di infrastruttura di Azure. Queste porte sono protette (bloccate) dai certificati di Azure. Senza certificati appropriati posto, le entità esterne non possono avviare le modifiche su tali endpoint.
3. Consentire probe di bilanciamento del carico di Azure in ingresso (*AzureLoadBalancer* tag) e il traffico di rete virtuale in ingresso (*VirtualNetwork* tag) sul [gruppo di sicurezza di rete](https://docs.microsoft.com/azure/virtual-network/security-overview).
4. Blocca tutto il traffico in ingresso con una regola di negazione completa.
5. Consentire il traffico in uscita a Internet per tutte le destinazioni.

#### <a name="user-defined-routes-supported-on-the-application-gateway-subnet"></a>Route definite dall'utente supportate nella subnet del Gateway applicazione

Per lo SKU v1, route definite dall'utente (Udr) sono supportate nella subnet del Gateway applicazione, purché non modificano la comunicazione end-to-end richiesta/risposta. Ad esempio, è possibile configurare una route UDR nella subnet del Gateway applicazione in modo che punti a un'appliance firewall per l'ispezione dei pacchetti. Ma è necessario assicurarsi che il pacchetto possa raggiungere la destinazione prevista dopo l'ispezione. In caso contrario potrebbe probe di integrità non corretto o il comportamento di routing del traffico. Sono inclusi route o route 0.0.0.0/0 predefinite che vengono propagate tramite Azure ExpressRoute o VPN gateway nella rete virtuale.

Per lo SKU v2, route definite dall'utente non sono supportati nella subnet del Gateway applicazione. Per altre informazioni, vedere [la scalabilità automatica e la ridondanza della zona per il Gateway applicazione](https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant#known-issues-and-limitations).

> [!NOTE]
> L'utilizzo di route definite dall'utente nella subnet del Gateway applicazione determina lo stato di integrità nel [visualizzazione dell'integrità back-end](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics#back-end-health) venga visualizzato come "Sconosciuto". Determina anche la generazione di log del Gateway applicazione e le metriche di esito negativo. È consigliabile non usare route definite dall'utente nella subnet del Gateway applicazione in modo che sia possibile visualizzare l'integrità back-end, log e metriche.

## <a name="front-end-ip"></a>IP front-end

È possibile configurare il gateway applicazione per avere un indirizzo IP pubblico, un indirizzo IP privato o entrambi. Quando si ospita un back-end che devono accedere i client su internet tramite un con connessione internet (VIP, virtual IP), è necessario un indirizzo IP pubblico. 

Un indirizzo IP pubblico non è necessario per un endpoint interno non esposto a internet. Che è noto come un *bilanciamento del carico interno* endpoint (ILB). Un gateway applicazione bilanciamento del carico interno è utile per applicazioni line-of-business interne non sono esposti a internet. È anche utile per i servizi e i livelli in un'applicazione a più livelli all'interno di un limite di sicurezza che non sono esposti a internet ma che richiede round-robin caricano distribuzione, persistenza delle sessioni o la terminazione SSL.

È supportato solo 1 indirizzo IP pubblico o 1 indirizzo IP privato. Quando si crea il gateway applicazione scegliere l'indirizzo IP front-end.

- Per un indirizzo IP pubblico, è possibile creare un nuovo indirizzo IP pubblico o usare un indirizzo IP pubblico esistente nella stessa posizione del gateway applicazione. Se si crea un nuovo indirizzo IP pubblico, il tipo di indirizzo IP selezionato (dinamico o statico) non può essere modificato in un secondo momento. Per altre informazioni, vedere [statico e indirizzo IP pubblico dinamico](https://docs.microsoft.com/en-us/azure/application-gateway/application-gateway-components#static-vs-dynamic-public-ip-address).

- Per un indirizzo IP privato, è possibile specificare un indirizzo IP privato dalla subnet in cui viene creato il gateway applicazione. Se non si specifica uno, un indirizzo IP arbitrario viene selezionato automaticamente dalla subnet. Per altre informazioni, vedere [creare un gateway applicazione con un servizio di bilanciamento del carico interno](https://docs.microsoft.com/azure/application-gateway/application-gateway-ilb-arm).

Un indirizzo IP front-end è associato a un *listener*, che consente di verificare le richieste in ingresso sull'indirizzo IP front-end.

## <a name="listeners"></a>Listener

Un listener è un'entità logica che controlla le richieste di connessione in ingresso usando la porta, protocollo, host e indirizzo IP. Quando si configura il listener, è necessario immettere i valori per questi corrispondenti ai valori corrispondenti nella richiesta in ingresso nel gateway.

Quando si crea un gateway applicazione usando il portale di Azure, viene creato anche un listener predefinito scegliendo il protocollo e porta del listener. È possibile scegliere se abilitare il supporto di HTTP2 nel listener. Dopo aver creato il gateway applicazione, è possibile modificare le impostazioni del listener predefinito (*appGatewayHttpListener*/*appGatewayHttpsListener*) o creazione di nuovi listener.

### <a name="listener-type"></a>Tipo di listener

Quando si crea un nuovo listener, si sceglie tra [ *base* e *multisito*](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#types-of-listeners).

- Se si ospita un singolo sito dietro un gateway applicazione, scegliere base. Scopri [come creare un gateway applicazione con un listener di base](https://docs.microsoft.com/azure/application-gateway/quick-create-portal).

- Se si sta configurando più di un'applicazione web o più sottodomini dello stesso dominio padre nella stessa istanza del gateway applicazione, scegliere listener multisito. Per un listener multisito, è anche necessario immettere un nome host. Questo avviene perché il Gateway applicazione si basa su intestazioni host HTTP 1.1 per ospitare più siti Web sul stesso indirizzo IP pubblico e sulla porta.

#### <a name="order-of-processing-listeners"></a>Ordine di elaborazione listener

Per lo SKU di versione 1, i listener vengono elaborati nell'ordine in cui sono elencati. Se un listener di base corrisponde a una richiesta in ingresso, il listener elabora prima che la richiesta. Quindi, configurare i listener multisito prima i listener di base per assicurarsi che il traffico viene indirizzato al back-end corretti.

Per lo SKU di versione 2, vengono elaborati i listener multisito prima i listener di base.

### <a name="front-end-ip"></a>IP front-end

Scegliere l'indirizzo IP front-end che si intende associare a questo listener. Il listener verrà ascolta le richieste in ingresso in questo indirizzo IP.

### <a name="front-end-port"></a>Porta front-end

Scegliere la porta front-end. Selezionare una porta esistente o crearne uno nuovo. Scegliere qualsiasi valore compreso il [intervallo di porte consentito](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#ports). È possibile utilizzare non solo porte note, ad esempio 80 e 443, ma qualsiasi porta consentita personalizzato adatto. Una porta utilizzabile per listener rivolte al pubblico o privato rivolte al listener.

### <a name="protocol"></a>Protocollo

Scegliere HTTP o HTTPS:

- Se si sceglie HTTP, il traffico tra client e il gateway applicazione non crittografato.

- Se si vuole scegliere HTTPS [terminazione SSL](https://docs.microsoft.com/azure/application-gateway/overview#secure-sockets-layer-ssl-terminationl) oppure [crittografia SSL end-to-end](https://docs.microsoft.com/azure/application-gateway/ssl-overview). Il traffico tra il client e il gateway applicazione è crittografato. E consente di terminare la connessione SSL nel gateway applicazione. Se si desidera che la crittografia SSL end-to-end, è necessario selezionare HTTPS e configurare il **back-end HTTP** impostazione. Ciò garantisce che il traffico viene nuovamente crittografato quando viene trasmesso dal back-end del gateway applicazione.

Per configurare la terminazione SSL e la crittografia SSL end-to-end, è necessario aggiungere un certificato per il listener per consentire al gateway applicazione derivare una chiave simmetrica. Ciò dipende dalla specifica del protocollo SSL. La chiave simmetrica viene usata per crittografare e decrittografare il traffico che viene inviato al gateway. Il certificato del gateway deve essere nel formato di scambio di informazioni personali (PFX). Questo formato consente di esportare la chiave privata che il gateway Usa per crittografare e decrittografare il traffico.

#### <a name="supported-certificates"></a>Certificati supportati

Visualizzare [certificati supportati per la terminazione SSL](https://docs.microsoft.com/azure/application-gateway/ssl-overview#certificates-supported-for-ssl-termination).

### <a name="additional-protocol-support"></a>Supporto dei protocolli aggiuntivi

#### <a name="http2-support"></a>Supporto di HTTP2

Supporto del protocollo HTTP/2 è disponibile per i client in grado di connettersi solo listener del gateway applicazione. La comunicazione ai pool di server back-end è su HTTP/1.1. Per impostazione predefinita, il supporto di HTTP/2 è disabilitato. Il frammento di codice di Azure PowerShell seguente viene illustrato come abilitare questa opzione:

```azurepowershell
$gw = Get-AzApplicationGateway -Name test -ResourceGroupName hm

$gw.EnableHttp2 = $true

Set-AzApplicationGateway -ApplicationGateway $gw
```

#### <a name="websocket-support"></a>Supporto per WebSocket

Supporto di WebSocket è abilitato per impostazione predefinita. È disponibile alcuna impostazione configurabile dall'utente per abilitarla o disabilitarla. È possibile usare i WebSocket con listener HTTP e HTTPS.

### <a name="custom-error-pages"></a>Pagine di errore personalizzate

È possibile definire errore personalizzata a livello globale o di listener. Ma la creazione di pagine di errore personalizzate a livello globale dal portale di Azure non è attualmente supportata. È possibile configurare una pagina di errore personalizzato per un errore del firewall applicazione 403 web o una pagina di 502 manutenzione a livello di listener. È anche necessario specificare un URL blob accessibile pubblicamente per il codice di stato di errore specificato. Per altre informazioni, vedere [Create Application Gateway custom error pages](https://docs.microsoft.com/azure/application-gateway/custom-error) (Creare pagine di errore personalizzate del gateway applicazione).

![Codici di errore del gateway applicazione](https://docs.microsoft.com/azure/application-gateway/media/custom-error/ag-error-codes.png)

Per configurare una pagina di errore personalizzata globale, vedere [configurazione di Azure PowerShell](https://docs.microsoft.com/azure/application-gateway/custom-error#azure-powershell-configuration).

### <a name="ssl-policy"></a>Criteri SSL

È possibile centralizzare la gestione dei certificati SSL e ridurre la crittografia di decrittografia sovraccarico per una farm di server back-end. Gestione centralizzata di SSL consente anche di specificare criteri SSL centrali adatti ai requisiti di sicurezza. È possibile scegliere *predefinite*, *predefiniti*, o *personalizzato* criteri SSL.

Configurare criteri SSL controllo versioni del protocollo SSL. È possibile configurare un gateway applicazione per rifiutare TLS1.0, TLS1.1 e TLS1.2. Per impostazione predefinita, SSL 2.0 e 3.0 sono disabilitati e non sono configurabili. Per altre informazioni, vedere [Cenni preliminari sui criteri SSL per il Gateway applicazione](https://docs.microsoft.com/azure/application-gateway/application-gateway-ssl-policy-overview).

Dopo aver creato un listener, è associarlo a una regola di routing delle richieste. Tale regola consente di determinare come le richieste ricevute nel listener vengono indirizzate al back-end.

## <a name="request-routing-rules"></a>Richiedere le regole di routing

Quando si crea un gateway applicazione usando il portale di Azure, si crea una regola predefinita (*rule1*). Questa regola associa il listener predefinito (*appGatewayHttpListener*) con il pool di back-end predefinito (*appGatewayBackendPool*) e le impostazioni HTTP back-end predefinito ( *appGatewayBackendHttpSettings*). Dopo aver creato il gateway, è possibile modificare le impostazioni della regola predefinita o creare nuove regole.

### <a name="rule-type"></a>Tipo regola

Quando si crea una regola, si sceglie tra [ *base* e *basata sul percorso*](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#request-routing-rule).

- Scegli il tipo basic se si desidera inoltrare tutte le richieste sul listener associati (ad esempio, *blog<i></i>.contoso.com/\*)* a un singolo pool di back-end.
- Scegliere basata sul percorso se si vuole indirizzare le richieste dai percorsi URL specifici ai pool back-end specifico. Il modello di percorso viene applicato solo al percorso dell'URL, non ai relativi parametri di query.

#### <a name="order-of-processing-rules"></a>Ordine delle regole di elaborazione

Per lo SKU v1, criteri di ricerca delle richieste in ingresso viene elaborata in ordine sono elencati i percorsi nella mappa del percorso URL della regola basata sul percorso. Se una richiesta corrisponde al modello in due o più percorsi nella mappa del percorso, il percorso in cui è elencato prima soddisfano la corrispondenza. E la richiesta viene inoltrata al back-end associata tale percorso.

Per lo SKU v2, una corrispondenza esatta è prioritario rispetto a quelli dell'ordine di percorso nella mappa del percorso URL. Se una richiesta corrisponde al modello in due o più percorsi, la richiesta viene inoltrata al back-end associata con il percorso che corrisponde esattamente alla richiesta. Se il percorso nella richiesta in ingresso non corrisponde esattamente a qualsiasi percorso nella mappa, criteri di ricerca della richiesta viene elaborata nell'elenco degli ordini percorso mappa per la regola basata sul percorso.

### <a name="associated-listener"></a>Listener associati

Associare un listener per la regola in modo che il *regola richiesta routing* che è associato il listener viene valutato per stabilire i pool back-end per instradare la richiesta per.

### <a name="associated-back-end-pool"></a>Pool back-end associato

Associare il pool back-end che contiene le destinazioni di back-end di elaborare le richieste che riceve il listener alla regola.

 - Per una regola di base, è consentito un solo pool di back-end. Tutte le richieste sul listener associati vengono inoltrate al pool back-end.

 - Per una regola basata sul percorso, aggiungere più pool di back-end che corrispondono a ogni percorso dell'URL. Le richieste che corrispondono al percorso URL immesso vengono inoltrate al pool back-end corrispondente. Inoltre, aggiungere un pool di back-end predefinito. Le richieste che non corrispondono a qualsiasi percorso dell'URL nella regola vengono inoltrate a tale pool.

### <a name="associated-back-end-http-setting"></a>Impostazione HTTP back-end associata

Aggiungere un'impostazione HTTP back-end per ogni regola. Le richieste vengono indirizzate dagli obiettivi di back-end del gateway applicazione usando il numero di porta, protocollo e altre informazioni che viene specificate in questa impostazione.

Per una regola di base, è consentita una sola impostazione HTTP back-end. Tutte le richieste sul listener associati vengono inoltrate alle destinazioni di back-end corrispondenti con questa impostazione HTTP.

Aggiungere un'impostazione HTTP back-end per ogni regola. Le richieste vengono indirizzate dagli obiettivi di back-end del gateway applicazione usando il numero di porta, protocollo e ad altre informazioni che viene specificato in questa impostazione.

Per una regola di base, è consentita una sola impostazione HTTP back-end. Tutte le richieste sul listener associati vengono inoltrate alle destinazioni di back-end corrispondenti con questa impostazione HTTP.

Per una regola basata sul percorso, aggiungere più impostazioni HTTP back-end che corrispondono a ogni percorso URL. Le richieste che corrispondono al percorso URL in questa impostazione vengono inoltrate alle destinazioni di back-end corrispondente utilizzando le impostazioni HTTP che corrispondono a ogni percorso dell'URL. Inoltre, aggiungere un'impostazione HTTP predefinito. Le richieste che non corrispondono a qualsiasi percorso dell'URL nella regola vengono inoltrate al pool back-end predefinito usando l'impostazione HTTP predefinito.

### <a name="redirection-setting"></a>Impostazione di reindirizzamento

Se il reindirizzamento è configurato per una regola di base, le richieste sul listener associati vengono reindirizzate alla destinazione. Si tratta *globale* il reindirizzamento. Se il reindirizzamento è configurato per una regola basata sul percorso, vengono reindirizzate solo le richieste in un'area specifica del sito. Un esempio è un'area del carrello acquisti che è identificata da */cart/\**. Si tratta *basata sul percorso* il reindirizzamento.

Per altre informazioni sul reindirizzamento, vedere [Panoramica del reindirizzamento nel Gateway applicazione](https://docs.microsoft.com/azure/application-gateway/redirect-overview).

#### <a name="redirection-type"></a>Tipo di reindirizzamento

Scegliere il tipo di reindirizzamento obbligato: *Permanent(301)*, *Temporary(307)*, *Found(302)*, oppure *vedere other(303)*.

#### <a name="redirection-target"></a>Destinazione di reindirizzamento

Scegliere un altro listener o un sito esterno come destinazione del reindirizzamento.

##### <a name="listener"></a>Listener

Scegliere listener come destinazione di reindirizzamento per reindirizzare il traffico da un listener a un altro nel gateway. Questa impostazione è necessaria quando si vuole abilitare il reindirizzamento HTTP-a-HTTPS. Reindirizza il traffico dal listener di origine che consente di rilevare le richieste HTTP in ingresso al listener di destinazione che controlla le richieste HTTPS in ingresso. È anche possibile scegliere di includere la stringa di query e il percorso dalla richiesta originale nella richiesta che viene inoltrata alla destinazione di reindirizzamento.

![Finestra di dialogo componenti Gateway applicazione](./media/configuration-overview/configure-redirection.png)

Per altre informazioni sul reindirizzamento HTTP-a-HTTPS, vedere:
- [Reindirizzamento HTTP-a-HTTP tramite il portale di Azure](https://docs.microsoft.com/azure/application-gateway/redirect-http-to-https-portal)
- [Reindirizzamento HTTP-a-HTTP tramite PowerShell](https://docs.microsoft.com/azure/application-gateway/redirect-http-to-https-powershell)
- [Reindirizzamento HTTP per HTTP tramite la CLI di Azure](https://docs.microsoft.com/azure/application-gateway/redirect-http-to-https-cli)

##### <a name="external-site"></a>Sito esterno

Quando si desidera reindirizzare il traffico nel listener che è associata a questa regola a un sito esterno, scegliere sito esterno. È possibile scegliere di includere la stringa di query dalla richiesta originale nella richiesta che viene inoltrata alla destinazione di reindirizzamento. Non è possibile inoltrare il percorso per il sito esterno che è stato nella richiesta originale.

Per altre informazioni sul reindirizzamento, vedere:
- [Reindirizzare il traffico a un sito esterno usando PowerShell](https://docs.microsoft.com/azure/application-gateway/redirect-external-site-powershell)
- [Reindirizzare il traffico a un sito esterno tramite l'interfaccia della riga di comando](https://docs.microsoft.com/azure/application-gateway/redirect-external-site-cli)

#### <a name="rewrite-the-http-header-setting"></a>Riscrivere l'impostazione dell'intestazione HTTP

Questa impostazione aggiunge, rimuove o aggiorna le intestazioni di richiesta e risposta HTTP durante la richiesta e spostano pacchetti di risposta tra il client e il pool back-end. È possibile configurare solo questa operazione tramite PowerShell. Portale di Azure e il supporto dell'interfaccia della riga non sono ancora disponibili. Per altre informazioni, vedere:

 - [Riscrivere Cenni preliminari sulle intestazioni HTTP](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers)
 - [Configurare riscrittura dell'intestazione HTTP](https://docs.microsoft.com/azure/application-gateway/add-http-header-rewrite-rule-powershell#specify-your-http-header-rewrite-rule-configuration)

## <a name="http-settings"></a>Impostazioni HTTP

Il gateway applicazione instrada il traffico verso i server back-end usando la configurazione specificati qui. Dopo aver creato un'impostazione HTTP, è necessario associarlo a una o più regole di routing delle richieste.

### <a name="cookie-based-affinity"></a>Affinità basata sui cookie

Questa funzionalità è utile quando si desidera mantenere una sessione utente nello stesso server. I cookie gestito dal gateway consentono l'applicazione gateway diretto il traffico successivo proveniente da una sessione utente nello stesso server per l'elaborazione. Questo è importante quando lo stato della sessione viene salvato in locale nel server per una sessione utente. Se l'applicazione non è possibile gestire l'affinità basata su cookie, è possibile utilizzare questa funzionalità. Per usarlo, assicurarsi che i client supportino i cookie.

### <a name="connection-draining"></a>Esaurimento delle connessioni

Lo svuotamento della connessione consente di rimuovere correttamente i membri del pool back-end durante gli aggiornamenti pianificati del servizio. È possibile applicare questa impostazione per tutti i membri di un pool back-end durante la creazione della regola. Assicura che tutte le istanze di annullare la registrazione di un pool back-end non ricevano le nuove richieste. Nel frattempo, le richieste esistenti possono completare entro un limite di tempo configurato. Lo svuotamento della connessione si applica alle istanze di back-end che vengono rimossi dal pool di back-end in modo esplicito da una chiamata API. Si applica anche alle istanze di back-end che vengono segnalate come *integro* per lo stato di integrità probe.

### <a name="protocol"></a>Protocollo

Il Gateway applicazione supporta HTTP e HTTPS per il routing delle richieste per i server back-end. Se si sceglie HTTP, il traffico verso i server back-end non crittografato. Se la comunicazione non crittografata non è accettabile, scegliere HTTPS.

Questa impostazione è combinato con HTTPS in supporta il listener [SSL end-to-end](https://docs.microsoft.com/azure/application-gateway/ssl-overview). In questo modo è possibile trasmettere in modo sicuro dati sensibili crittografati al back-end. Ogni server back-end nel pool di back-end con SSL end-to-end abilitato deve essere configurato con un certificato per consentire la comunicazione sicura.

### <a name="port"></a>Porta

Questa impostazione specifica la porta in cui i server back-end in ascolto per il traffico dal gateway applicazione. È possibile configurare le porte compreso tra 1 e 65535.

### <a name="request-timeout"></a>Timeout richiesta

Questa impostazione è il numero di secondi che il gateway applicazione è in attesa di ricevere una risposta dal pool di back-end prima che venga restituito un messaggio di errore "timeout della connessione".

### <a name="override-back-end-path"></a>Sostituire il percorso di back-end

Questa impostazione consente di configurare un percorso di inoltro personalizzato facoltativo da utilizzare per la richiesta viene inoltrata al back-end. Qualsiasi parte del percorso in ingresso che corrisponde al percorso personalizzato nel **sostituire il percorso di back-end** campo viene copiato nel percorso inoltrati. La tabella seguente illustra il funzionamento di questa funzionalità:

- Quando l'impostazione HTTP è associato a una regola di routing delle richieste di base:

  | Richiesta originale  | Sostituire il percorso di back-end | Richiesta inoltrata al back-end |
  | ----------------- | --------------------- | ---------------------------- |
  | /Home/            | /override/            | / sostituzione/home /              |
  | / home/secondhome / | /override/            | / sostituzione/home/secondhome /   |

- Quando l'impostazione HTTP è associato a una regola di routing delle richieste basata sul percorso:

  | Richiesta originale           | Regola di percorso       | Sostituire il percorso di back-end | Richiesta inoltrata al back-end |
  | -------------------------- | --------------- | --------------------- | ---------------------------- |
  | /pathrule/home /            | /pathrule*      | /override/            | / sostituzione/home /              |
  | regola di percorso/home/secondhome / | /pathrule*      | /override/            | / sostituzione/home/secondhome /   |
  | /Home/                     | /pathrule*      | /override/            | / sostituzione/home /              |
  | / home/secondhome /          | /pathrule*      | /override/            | / sostituzione/home/secondhome /   |
  | /pathrule/home /            | regola di percorso/home page * | /override/            | /override/                   |
  | regola di percorso/home/secondhome / | regola di percorso/home page * | /override/            | / sostituzione/secondhome /        |

### <a name="use-for-app-service"></a>Utilizzo per il servizio app

Si tratta di un collegamento dell'interfaccia utente che consente di selezionare le due impostazioni necessarie per il back-end di servizio App di Azure. Consente **scegliere il nome host di indirizzi back-end**, e crea un nuovo probe personalizzato. (Per altre informazioni, vedere la [Pick nome host di indirizzi back-end](#pick) l'impostazione di questo articolo.) Viene creato un nuovo probe e l'intestazione di probe viene prelevato dall'indirizzo del membro back-end.

### <a name="use-custom-probe"></a>Usa probe personalizzato

Questa impostazione consente di associare un [probe personalizzato](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview#custom-health-probe) con un'impostazione HTTP. È possibile associare solo un probe personalizzato a un'impostazione HTTP. Se non è possibile associare in modo esplicito un probe personalizzato, il [probe predefinito](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview#default-health-probe-settings) viene usato per monitorare l'integrità del back-end. È consigliabile creare un probe personalizzato per un maggiore controllo sul monitoraggio dell'integrità del back-end.

> [!NOTE]
> Il probe personalizzato non monitora l'integrità del pool back-end a meno che l'impostazione HTTP corrispondente sia associato in modo esplicito a un listener.

### <a id="pick"/></a>Selezionare il nome host di indirizzi back-end

Questa funzionalità imposta in modo dinamico il *host* intestazione nella richiesta per il nome host del pool back-end. Usa un indirizzo IP o FQDN.

Questa funzionalità è utile quando il nome di dominio del back-end è diverso dal nome DNS del gateway applicazione e il back-end si basa su un'estensione di indicazione nome Server (SNI) per risolvere l'endpoint corretto o l'intestazione host specifica.

Un caso di esempio è servizi multi-tenant come back-end. Un servizio app è un servizio multi-tenant che usa un'area condivisa con un singolo indirizzo IP. Pertanto, un servizio app è accessibile solo tramite i nomi host configurati nelle impostazioni del dominio personalizzato.

Per impostazione predefinita, è il nome di dominio personalizzato *example.azurewebsites.<i> </i>net*. Per accedere al servizio app usando un gateway applicazione tramite un nome host registrato in modo non esplicito nel servizio app o nome di dominio completo del gateway applicazione, si esegue l'override del nome host nella richiesta originale al nome host del servizio app. A tale scopo, abilitare la **scegliere il nome host di indirizzi back-end** impostazione.

Per un dominio personalizzato viene eseguito il mapping il cui nome DNS personalizzato esistente per il servizio app, non è necessario abilitare questa impostazione.

> [!NOTE]
> Questa impostazione non è necessaria per ambiente del servizio App di PowerApps, che è una distribuzione dedicata.

### <a name="host-name-override"></a>Override del nome host

Questa funzionalità sostituisce la *host* intestazione nella richiesta in ingresso nel gateway applicazione con il nome host specificato.

Ad esempio, se *www.contoso<i></i>. com* specificato nella **nome Host** impostazione, la richiesta originale *https:/<i></i>/appgw.eastus.cloudapp.net/path1* viene modificato in *https:/<i></i>/www.contoso.com/path1* quando la richiesta viene inoltrata al server back-end.

## <a name="back-end-pool"></a>Pool back-end

È possibile puntare a un pool back-end a quattro tipi di membri di back-end: una macchina virtuale specifica, un set di scalabilità di macchine virtuali, un indirizzo IP/FQDN o un servizio app. Ogni pool back-end può puntare a più membri dello stesso tipo. Che punta ai membri di tipi diversi nello stesso pool back-end non è supportata.

Dopo aver creato un pool back-end, è necessario associarlo a una o più regole di routing delle richieste. È inoltre necessario configurare i probe di integrità per ogni pool back-end nel gateway applicazione. Quando viene soddisfatta una condizione di regola di routing delle richieste, il gateway applicazione inoltra il traffico ai server Integro (come determinato dal probe di integrità) nel pool di back-end corrispondente.

## <a name="health-probes"></a>Probe di integrità

Per impostazione predefinita, un gateway applicazione monitora l'integrità di tutte le risorse nel relativo back-end. Ma è consigliabile creare un probe personalizzato per ogni impostazione HTTP back-end ottenere maggiore controllo sul monitoraggio dell'integrità. Per informazioni su come configurare un probe personalizzato, vedere [impostazioni del probe di integrità personalizzato](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview#custom-health-probe-settings).

> [!NOTE]
> Dopo aver creato un probe di integrità personalizzati, è necessario associarlo a un'impostazione HTTP back-end. Un probe personalizzato non monitorerà l'integrità del pool back-end a meno che l'impostazione HTTP corrispondente sia associato in modo esplicito a un listener.

## <a name="next-steps"></a>Passaggi successivi

Ora che sai sui componenti di Gateway applicazione, è possibile:

- [Creare un gateway applicazione nel portale di Azure](quick-create-portal.md)
- [Creare un gateway applicazione usando PowerShell](quick-create-powershell.md)
- [Creare un gateway applicazione con l'interfaccia della riga di comando di Azure](quick-create-cli.md)
