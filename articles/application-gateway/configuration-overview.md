---
title: Panoramica della configurazione del gateway applicazione di AzureAzure Application Gateway configuration overview
description: Questo articolo descrive come configurare i componenti del gateway applicazione di AzureThis article describes how to configure the components of Azure Application Gateway
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 03/24/2020
ms.author: absha
ms.openlocfilehash: 89d894a5125a16f95e6ef8a15c2503d48f3a8e55
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/03/2020
ms.locfileid: "80632181"
---
# <a name="application-gateway-configuration-overview"></a>Panoramica della configurazione del gateway applicazione

Il gateway applicazione di Azure è costituito da diversi componenti che è possibile configurare in vari modi per scenari diversi. In questo articolo viene illustrato come configurare ogni componente.

![Diagramma di flusso dei componenti del gateway applicazione](./media/configuration-overview/configuration-overview1.png)

Questa immagine illustra un'applicazione che dispone di tre listener. I primi due sono rispettivamente `http://acme.com/*` `http://fabrikam.com/*`listener multisito per e , . Entrambi ascoltano sulla porta 80. Il terzo è un listener di base con terminazione TLS (Transport Layer Security) end-to-end, precedentemente nota come terminazione SSL (Secure Sockets Layer).


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Prerequisiti

### <a name="azure-virtual-network-and-dedicated-subnet"></a>Rete virtuale di Azure e subnet dedicataAzure virtual network and dedicated subnet

Un gateway applicazione è una distribuzione dedicata nella rete virtuale. All'interno della rete virtuale è necessaria una subnet dedicata per il gateway applicazione. È possibile avere più istanze di una distribuzione di un determinato gateway applicazione in una subnet. È anche possibile distribuire altri gateway applicazione nella subnet. Tuttavia, non è possibile distribuire altre risorse nella subnet del gateway applicazione.

> [!NOTE]
> Non è possibile combinare Standard_v2 e il gateway applicazione standard di Azure nella stessa subnet.

#### <a name="size-of-the-subnet"></a>Dimensioni della subnet

Il gateway applicazione usa un indirizzo IP privato per ogni istanza, più un altro indirizzo IP privato se è configurato un indirizzo IP front-end privato.

Azure riserva inoltre cinque indirizzi IP in ogni subnet per uso interno: i primi quattro e gli ultimi indirizzi IP. Si considerino ad esempio 15 istanze di gateway applicazione senza IP front-end privato. Sono necessari almeno 20 indirizzi IP per questa subnet: cinque per uso interno e 15 per le istanze del gateway applicazione. Quindi, è necessario una dimensione della subnet /27 o superiore.

Si consideri una subnet con 27 istanze del gateway applicazione e un indirizzo IP per un IP front-end privato. In questo caso, sono necessari 33 indirizzi IP: 27 per le istanze del gateway applicazione, uno per il front-end privato e cinque per uso interno. Quindi, è necessario una dimensione di subnet /26 o superiore.

È consigliabile utilizzare una dimensione della subnet di almeno /28. Questa dimensione fornisce 11 indirizzi IP utilizzabili. Se il caricamento dell'applicazione richiede più di 10 istanze del gateway applicazione, prendere in considerazione una dimensione della subnet /27 o /26.If your application load requires more than 10 Application Gateway instances, consider a /27 or /26 subnet size.

#### <a name="network-security-groups-on-the-application-gateway-subnet"></a>Gruppi di sicurezza di rete nella subnet del gateway applicazioneNetwork security groups on the Application Gateway subnet

I gruppi di sicurezza di rete sono supportati nel gateway applicazione.Network security groups (NSGs) are supported on Application Gateway. Ma ci sono alcune restrizioni:

- È necessario consentire il traffico Internet in ingresso sulle porte TCP 65503-65534 per lo SKU del gateway applicazione v1 e le porte TCP 65200-65535 per lo SKU v2 con la subnet di destinazione come **Qualsiasi** e origine come tag di servizio **GatewayManager.** Questo intervallo di porte è necessario per la comunicazione di infrastruttura di Azure. Queste porte sono protette (bloccate) dai certificati di Azure.These ports are protected (locked down) by Azure certificates. Le entità esterne, inclusi i clienti di tali gateway, non possono comunicare su questi endpoint.

- La connettività Internet in uscita non può essere bloccata. Le regole in uscita predefinite nel gruppo di sicurezza di sicurezza di sicurezza di sicurezza consentono la connettività Internet. È consigliabile:

  - Non rimuovere le regole in uscita predefinite.
  - Non creare altre regole in uscita che negano la connettività in uscita.

- Il traffico dal tag **AzureLoadBalancer** deve essere consentito.

#### <a name="allow-application-gateway-access-to-a-few-source-ips"></a>Consentire l'accesso del gateway applicazione ad alcuni indirizzi IP di origineAllow Application Gateway access to a few source IPs

Per questo scenario, utilizzare i gruppi di sicurezza di rete nella subnet del gateway applicazione. Inserire le restrizioni seguenti sulla subnet in questo ordine di priorità:

1. Consentire il traffico in ingresso da un intervallo IP o IP di origine con la destinazione come intero intervallo di indirizzi subnet del gateway applicazione e porta di destinazione come porta di accesso in ingresso, ad esempio la porta 80 per l'accesso HTTP.
2. Consentire le richieste in ingresso dall'origine come tag di servizio **GatewayManager** e destinazione come **Porte** qualsiasi e di destinazione come 65503-65534 per lo SKU del gateway applicazione v1 e porte 65200-65535 per lo SKU v2 per la comunicazione dello stato di [integrità back-end](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics). Questo intervallo di porte è necessario per la comunicazione di infrastruttura di Azure. Queste porte sono protette (bloccate) dai certificati di Azure.These ports are protected (locked down) by Azure certificates. Senza i certificati appropriati sul posto, le entità esterne non possono avviare modifiche su tali endpoint.
3. Consentire i probe di Azure Load Balancer in ingresso (tag*AzureLoadBalancer)* e il traffico di rete virtuale in ingresso (tag*VirtualNetwork)* nel gruppo di sicurezza di [rete.](https://docs.microsoft.com/azure/virtual-network/security-overview)
4. Bloccare tutto il traffico in ingresso utilizzando una regola di negazione di tutti.
5. Consentire il traffico in uscita a Internet per tutte le destinazioni.

#### <a name="user-defined-routes-supported-on-the-application-gateway-subnet"></a>Route definite dall'utente supportate nella subnet del gateway applicazione

> [!IMPORTANT]
> L'utilizzo di UDR nella subnet del gateway applicazione potrebbe causare la visualizzazione dello stato di integrità nella [visualizzazione dell'integrità back-end](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics#back-end-health) come **Sconosciuto**. Potrebbe anche causare l'esito negativo della generazione di log e metriche del gateway applicazione. È consigliabile non usare UDR nella subnet del gateway applicazione in modo da poter visualizzare l'integrità, i log e le metriche back-end.

- **v1**

   Per lo SKU v1, le route definite dall'utente (UDR) sono supportate nella subnet del gateway applicazione, purché non modifichino la comunicazione end-to-end richiesta/risposta. Ad esempio, è possibile configurare un UDR nella subnet del gateway applicazione in modo che punti a un'appliance firewall per l'ispezione dei pacchetti. Ma è necessario assicurarsi che il pacchetto può raggiungere la destinazione prevista dopo l'ispezione. In caso contrario, il probe di integrità o il routing del traffico potrebbero essere non corretti. Sono incluse le route apprese o le route 0.0.0.0/0 predefinite propagate da Azure ExpressRoute o dai gateway VPN nella rete virtuale.

- **v2**

   Per lo SKU v2, esistono scenari supportati e non supportati:For the v2 SKU, there are supported and unsupported scenarios:

   **scenari supportati v2**
   > [!WARNING]
   > Una configurazione non corretta della tabella di route potrebbe causare un routing asimmetrico nel gateway applicazione v2. Assicurarsi che tutto il traffico aereo di gestione/controllo venga inviato direttamente a Internet e non tramite un'appliance virtuale. Anche la registrazione e le metriche potrebbero risentirne.


  **Scenario 1:** UDR per disabilitare la propagazione del protocollo BGP (Border Gateway) nella subnet del gateway applicazione

   A volte la route del gateway predefinito (0.0.0.0/0) viene annunciata tramite i gateway ExpressRoute o VPN associati alla rete virtuale del gateway applicazione. Questo interrompe il traffico aereo di gestione, che richiede un percorso diretto a Internet. In tali scenari, un UDR può essere utilizzato per disabilitare la propagazione delle route BGP. 

   Per disabilitare la propagazione delle route BGP, attenersi alla seguente procedura:

   1. Creare una risorsa Tabella di route in Azure.Create a Route Table resource in Azure.
   2. Disabilitare il parametro di **propagazione della route del gateway di rete virtuale.** 
   3. Associare la tabella di route alla subnet appropriata. 

   L'abilitazione dell'UDR per questo scenario non dovrebbe interrompere le impostazioni esistenti.

  **Scenario 2**: UDR per indirizzare 0.0.0.0/0 a Internet

   È possibile creare un UDR per inviare il traffico 0.0.0.0/0 direttamente a Internet.You can create a UDR to send 0.0.0.0/0 traffic directly to the Internet. 

  **Scenario 3:** UDR per il servizio Azure Kubernetes kubenet

  Se si usa kubenet con il servizio Azure Kubernetes (AKS) e il controller di ingresso del gateway applicazione (AGIC), è necessario configurare una tabella di route per consentire il instradamento del traffico inviato ai pod al nodo corretto. Ciò non sarà necessario se si usa Azure CNI. 

   Per impostare la tabella del ciclo di lavorazione in modo da consentire il funzionamento di Kubenet, attenersi alla seguente procedura:

  1. Creare una risorsa Tabella di route in Azure.Create a Route Table resource in Azure. 
  2. Una volta creato, vai alla pagina **Itinerari.** 
  3. Aggiungere un nuovo percorso:
     - Il prefisso dell'indirizzo deve essere l'intervallo IP dei pod che si desidera raggiungere in AKS. 
     - Il tipo di hop successivo deve essere **Virtual Appliance**. 
     - L'indirizzo dell'hop successivo deve essere l'indirizzo IP del nodo che ospita i pod all'interno dell'intervallo IP definito nel campo del prefisso dell'indirizzo. 
    
  **Scenari non supportati v2**

  **Scenario 1:** UDR per appliance virtuali

  Qualsiasi scenario in cui 0.0.0.0/0 deve essere reindirizzato tramite qualsiasi appliance virtuale, una rete virtuale hub/spoke o locale (tunneling forzato) non è supportato per V2.

## <a name="front-end-ip"></a>Front-end IP

È possibile configurare il gateway applicazione in modo che disponga di un indirizzo IP pubblico, di un indirizzo IP privato o di entrambi. Un indirizzo IP pubblico è necessario quando si ospita un back-end a cui i client devono accedere tramite Internet tramite un indirizzo IP virtuale (VIP) con connessione Internet. 

Un indirizzo IP pubblico non è necessario per un endpoint interno non esposto a Internet.A public IP isn't required for an internal endpoint that's not exposed to the internet. È noto come endpoint del *servizio di bilanciamento del carico interno* (ILB) o IP front-end privato. Un gateway applicazione ILB è utile per le applicazioni line-of-business interne che non sono esposte a Internet.An application gateway ILB is useful for internal line-of-business applications that aren's aren't exposed to the internet. È utile anche per i servizi e i livelli in un'applicazione multilivello all'interno di un limite di sicurezza che non sono esposti a Internet ma che richiedono la distribuzione del carico round robin, la persa dalla sessione o la terminazione TLS.

È supportato un solo indirizzo IP pubblico o un indirizzo IP privato. L'IP front-end viene scelto quando si crea il gateway applicazione.

- Per un indirizzo IP pubblico, è possibile creare un nuovo indirizzo IP pubblico o utilizzare un indirizzo IP pubblico esistente nello stesso percorso del gateway applicazione. Per ulteriori informazioni, vedere [indirizzo IP pubblico statico e dinamico](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#static-versus-dynamic-public-ip-address).

- Per un indirizzo IP privato, è possibile specificare un indirizzo IP privato dalla subnet in cui viene creato il gateway applicazione. Se non si specifica uno, un indirizzo IP arbitrario viene selezionato automaticamente dalla subnet. Il tipo di indirizzo IP selezionato (statico o dinamico) non può essere modificato in un secondo momento. Per altre informazioni, vedere Creare un gateway applicazione con un servizio di [bilanciamento del carico interno.](https://docs.microsoft.com/azure/application-gateway/application-gateway-ilb-arm)

Un indirizzo IP front-end è associato a un *listener*, che verifica la presenza di richieste in ingresso nell'IP front-end.

## <a name="listeners"></a>Listener

Un listener è un'entità logica che controlla le richieste di connessione in ingresso utilizzando la porta, il protocollo, l'host e l'indirizzo IP. Quando si configura il listener, è necessario immettere i valori per questi che corrispondono ai valori corrispondenti nella richiesta in ingresso nel gateway.

Quando si crea un gateway applicazione tramite il portale di Azure, si crea anche un listener predefinito scegliendo il protocollo e la porta per il listener. È possibile scegliere se abilitare il supporto HTTP2 nel listener. Dopo aver creato il gateway applicazione, è possibile modificare le impostazioni del listener predefinito (*appGatewayHttpListener*) o creare nuovi listener.

### <a name="listener-type"></a>Tipo di listener

Quando si crea un nuovo listener, si sceglie tra [ *base* e *multisito*](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#types-of-listeners).

- Se si desidera che tutte le richieste (per qualsiasi dominio) vengano accettate e inoltrate ai pool back-end, scegliere Basic. Informazioni su [come creare un gateway applicazione con un listener di base.](https://docs.microsoft.com/azure/application-gateway/quick-create-portal)

- Se si desidera inoltrare le richieste a pool back-end diversi in base all'intestazione *host* o al nome host, scegliere listener multisito, in cui è necessario specificare anche un nome host corrispondente alla richiesta in ingresso. Questo perché il gateway applicazione si basa sulle intestazioni host HTTP 1.1 per ospitare più di un sito Web sullo stesso indirizzo IP pubblico e sulla stessa porta.

#### <a name="order-of-processing-listeners"></a>Ordine di elaborazione dei listener

Per lo SKU v1, le richieste vengono confrontate in base all'ordine delle regole e al tipo di listener. Se una regola con listener di base viene prima nell'ordine, viene elaborata per prima e accetterà qualsiasi richiesta per tale combinazione di porta e IP. Per evitare questo problema, configurare prima le regole con listener multisito ed eseguire il push della regola con il listener di base fino all'ultimo nell'elenco.

Per lo SKU v2, i listener multisito vengono elaborati prima dei listener di base.

### <a name="front-end-ip"></a>Front-end IP

Scegliere l'indirizzo IP front-end che si intende associare a questo listener. Il listener ascolterà le richieste in ingresso su questo IP.

### <a name="front-end-port"></a>Porta front-end

Scegliere la porta front-end. Selezionare una porta esistente o crearne una nuova. Scegliere un valore qualsiasi [dall'intervallo di porte consentito.](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#ports) È possibile utilizzare non solo porte note, ad esempio 80 e 443, ma qualsiasi porta personalizzata consentita adatta. Una porta può essere utilizzata per listener pubblici o di private.

### <a name="protocol"></a>Protocollo

Scegliere HTTP o HTTPS:

- Se si sceglie HTTP, il traffico tra il client e il gateway applicazione non viene crittografato.

- Scegliere HTTPS se si desidera che [la terminazione TLS](features.md#secure-sockets-layer-ssltls-termination) o la [crittografia TLS end-to-end](https://docs.microsoft.com/azure/application-gateway/ssl-overview). Il traffico tra il client e il gateway applicazione è crittografato. E la connessione TLS termina al gateway applicazione. Se si desidera la crittografia TLS end-to-end, è necessario scegliere HTTPS e configurare l'impostazione **HTTP back-end.** In questo modo si garantisce che il traffico venga nuovamente crittografato quando passa dal gateway applicazione al back-end.


Per configurare la terminazione TLS e la crittografia TLS end-to-end, è necessario aggiungere un certificato al listener per consentire al gateway applicazione di derivare una chiave simmetrica. Questo è dettato dalla specifica del protocollo TLS. La chiave simmetrica viene utilizzata per crittografare e decrittografare il traffico inviato al gateway. Il certificato del gateway deve essere in formato PFX (Personal Information Exchange). Questo formato consente di esportare la chiave privata utilizzata dal gateway per crittografare e decrittografare il traffico.

#### <a name="supported-certificates"></a>Certificati supportati

Vedere [i certificati supportati per la terminazione TLS](https://docs.microsoft.com/azure/application-gateway/ssl-overview#certificates-supported-for-ssl-termination).

### <a name="additional-protocol-support"></a>Supporto aggiuntivo del protocollo

#### <a name="http2-support"></a>Supporto HTTP2

Il supporto del protocollo HTTP/2 è disponibile solo per i client che si connettono ai listener del gateway dell'applicazione. La comunicazione con i pool di server back-end è superiore a HTTP/1.1. Per impostazione predefinita, il supporto di HTTP/2 è disabilitato. Il frammento di codice di Azure PowerShell seguente illustra come abilitare questa funzionalità:The following Azure PowerShell code snippet shows how to enable this:

```azurepowershell
$gw = Get-AzApplicationGateway -Name test -ResourceGroupName hm

$gw.EnableHttp2 = $true

Set-AzApplicationGateway -ApplicationGateway $gw
```

#### <a name="websocket-support"></a>Supporto per WebSocket

Il supporto WebSocket è abilitato per impostazione predefinita. Non esiste alcuna impostazione configurabile dall'utente per abilitarla o disabilitarla. È possibile utilizzare WebSockets con entrambi i listener HTTP e HTTPS.

### <a name="custom-error-pages"></a>Pagine di errore personalizzate

È possibile definire un errore personalizzato a livello globale o a livello di listener. Tuttavia, la creazione di pagine di errore personalizzate a livello globale dal portale di Azure non è attualmente supportata. È possibile configurare una pagina di errore personalizzata per un errore 403 del firewall dell'applicazione Web o una pagina di manutenzione 502 a livello di listener. È inoltre necessario specificare un URL BLOB accessibile pubblicamente per il codice di stato di errore specificato. Per altre informazioni, vedere [Create Application Gateway custom error pages](https://docs.microsoft.com/azure/application-gateway/custom-error) (Creare pagine di errore personalizzate del gateway applicazione).

![Codici di errore del gateway applicazione](https://docs.microsoft.com/azure/application-gateway/media/custom-error/ag-error-codes.png)

Per configurare una pagina di errore personalizzata globale, vedere Configurazione di Azure PowerShell .To configure a global custom error page, see [Azure PowerShell configuration](https://docs.microsoft.com/azure/application-gateway/custom-error#azure-powershell-configuration).

### <a name="tls-policy"></a>Criteri TLS

È possibile centralizzare la gestione dei certificati TLS/SSL e ridurre l'overhead di decrittografia per una server farm back-end. La gestione centralizzata TLS consente inoltre di specificare un criterio TLS centrale adatto ai requisiti di sicurezza. È possibile scegliere criteri TLS *predefiniti,* *predefiniti*o *personalizzati.*

Configurare i criteri TLS per controllare le versioni del protocollo TLS. È possibile configurare un gateway applicazione per utilizzare una versione di protocollo minima per gli handshake TLS da TLS1.0, TLS1.1 e TLS1.2.You can configure an application gateway to use a minimum protocol version for TLS handshakes from TLS1.0, TLS1.1 and TLS1.2. Per impostazione predefinita, SSL 2.0 e 3.0 sono disabilitati e non configurabili. Per ulteriori informazioni, vedere [Panoramica dei criteri TLS del gateway applicazione](https://docs.microsoft.com/azure/application-gateway/application-gateway-ssl-policy-overview).

Dopo aver creato un listener, associarlo a una regola di routing delle richieste. Questa regola determina il modo in cui le richieste ricevute nel listener vengono instradate al back-end.

## <a name="request-routing-rules"></a>Richiedere regole di routing

Quando si crea un gateway applicazione tramite il portale di Azure, si crea una regola predefinita (*rule1*). Questa regola associa il listener predefinito (*appGatewayHttpListener*) al pool back-end predefinito (*appGatewayBackendPool*) e alle impostazioni HTTP back-end predefinite (*appGatewayBackendHttpSettings*). Dopo aver creato il gateway, è possibile modificare le impostazioni della regola predefinita o creare nuove regole.

### <a name="rule-type"></a>Tipo regola

Quando si crea una regola, si sceglie tra [ *base* e *basato su percorso*](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#request-routing-rules).

- Scegliere basic se si desidera inoltrare tutte le richieste sul listener associato (ad esempio, *blog<i></i>.contoso.com/\*)* a un singolo pool back-end.
- Scegliere basato su percorso se si desidera instradare le richieste da percorsi URL specifici a pool back-end specifici. Il modello di percorso viene applicato solo al percorso dell'URL, non ai relativi parametri di query.

#### <a name="order-of-processing-rules"></a>Ordine delle regole di elaborazione

Per lo SKU v1, il criterio di ricerca delle richieste in ingresso viene elaborato nell'ordine in cui i percorsi sono elencati nella mappa del percorso URL della regola basata sul percorso. Se una richiesta corrisponde al modello in due o più percorsi nella mappa del percorso, il percorso elencato per primo viene confrontato. E la richiesta viene inoltrata al back-end che è associato a tale percorso.

Per lo SKU v2, una corrispondenza esatta ha una priorità più alta rispetto all'ordine del percorso nella mappa del percorso URL. Se una richiesta corrisponde al modello in due o più percorsi, la richiesta viene inoltrata al back-end associato al percorso che corrisponde esattamente alla richiesta. Se il percorso nella richiesta in ingresso non corrisponde esattamente ad alcun percorso nella mappa, la corrispondenza dei modelli della richiesta viene elaborata nell'elenco degli ordini della mappa del percorso per la regola basata sul percorso.

### <a name="associated-listener"></a>Listener associato

Associare un listener alla regola in modo che la regola di *routing* delle richieste associata al listener venga valutata per determinare il pool back-end a cui instradare la richiesta.

### <a name="associated-back-end-pool"></a>Pool back-end associato

Associare alla regola il pool back-end che contiene le destinazioni back-end che servono le richieste ricevute dal listener.

 - Per una regola di base, è consentito un solo pool back-end. Tutte le richieste sul listener associato vengono inoltrate a tale pool back-end.

 - Per una regola basata sul percorso, aggiungere più pool back-end che corrispondono a ogni percorso URL. Le richieste che corrispondono al percorso URL immesso vengono inoltrate al pool back-end corrispondente. Aggiungere inoltre un pool back-end predefinito. Le richieste che non corrispondono ad alcun percorso URL nella regola vengono inoltrate a tale pool.

### <a name="associated-back-end-http-setting"></a>Impostazione HTTP back-end associata

Aggiungere un'impostazione HTTP back-end per ogni regola. Le richieste vengono instradate dal gateway applicazione alle destinazioni back-end utilizzando il numero di porta, il protocollo e altre informazioni specificate in questa impostazione.

Per una regola di base, è consentita una sola impostazione HTTP back-end. Tutte le richieste sul listener associato vengono inoltrate alle destinazioni back-end corrispondenti utilizzando questa impostazione HTTP.

Per una regola basata sul percorso, aggiungere più impostazioni HTTP back-end che corrispondono a ogni percorso URL. Le richieste che corrispondono al percorso URL in questa impostazione vengono inoltrate alle destinazioni back-end corrispondenti utilizzando le impostazioni HTTP che corrispondono a ogni percorso URL. Aggiungere inoltre un'impostazione HTTP predefinita. Le richieste che non corrispondono ad alcun percorso URL in questa regola vengono inoltrate al pool back-end predefinito utilizzando l'impostazione HTTP predefinita.

### <a name="redirection-setting"></a>Impostazione di reindirizzamento

Se il reindirizzamento è configurato per una regola di base, tutte le richieste sul listener associato vengono reindirizzate alla destinazione. Questo è il reindirizzamento *globale.* Se il reindirizzamento è configurato per una regola basata su percorso, vengono reindirizzate solo le richieste in un'area del sito specifica. Un esempio è un'area carrello della spesa che è indicato da */cart/\**. Si tratta di un reindirizzamento *basato su percorso.*

Per ulteriori informazioni sui reindirizzamenti, vedere Panoramica del [reindirizzamento del gateway applicazione](redirect-overview.md).

#### <a name="redirection-type"></a>Tipo di reindirizzamento

Scegliere il tipo di reindirizzamento richiesto: *Permanent(301)*, *Temporary(307)*, *Found(302)* o *See other(303)*.

#### <a name="redirection-target"></a>Destinazione di reindirizzamento

Scegliere un altro listener o un sito esterno come destinazione del reindirizzamento.

##### <a name="listener"></a>Listener

Scegliere il listener come destinazione del reindirizzamento per reindirizzare il traffico da un listener a un altro nel gateway. Questa impostazione è necessaria quando si desidera abilitare il reindirizzamento da HTTP a HTTPS. Reindirizza il traffico dal listener di origine che controlla le richieste HTTP in ingresso al listener di destinazione che controlla le richieste HTTPS in ingresso. È anche possibile scegliere di includere la stringa di query e il percorso della richiesta originale nella richiesta inoltrata alla destinazione di reindirizzamento.

![Finestra di dialogo Componenti gateway applicazione](./media/configuration-overview/configure-redirection.png)

Per altre informazioni sul reindirizzamento da HTTP a HTTPS, vedere:For more information about HTTP-to-HTTPS redirection, see:
- [Reindirizzamento da HTTP a HTTPS tramite il portale di AzureHTTP-to-HTTPS redirection by using the Azure portal](redirect-http-to-https-portal.md)
- [Reindirizzamento da HTTP a HTTPS tramite PowerShellHTTP-to-HTTPS redirection by using PowerShell](redirect-http-to-https-powershell.md)
- [Reindirizzamento da HTTP a HTTPS tramite l'interfaccia della riga di comando di AzureHTTP-to-HTTPS redirection by using the Azure CLI](redirect-http-to-https-cli.md)

##### <a name="external-site"></a>Sito esterno

Scegliere il sito esterno quando si desidera reindirizzare il traffico sul listener associato a questa regola a un sito esterno. È possibile scegliere di includere la stringa di query dalla richiesta originale nella richiesta inoltrata alla destinazione di reindirizzamento. Non è possibile inoltrare il percorso al sito esterno che era nella richiesta originale.

Per altre informazioni sul reindirizzamento, vedere:For more information about redirection, see:
- [Reindirizzare il traffico a un sito esterno tramite PowerShellRedirect traffic to an external site by using PowerShell](redirect-external-site-powershell.md)
- [Reindirizzare il traffico a un sito esterno utilizzando l'interfaccia della riga di comando](redirect-external-site-cli.md)

#### <a name="rewrite-the-http-header-setting"></a>Riscrivere l'impostazione dell'intestazione HTTP

Questa impostazione consente di aggiungere, rimuovere o aggiornare le intestazioni di richiesta e risposta HTTP mentre i pacchetti di richiesta e risposta si spostano tra il client e i pool back-end. Per altre informazioni, vedere:

 - [Panoramica della riscrittura delle intestazioni HTTP](rewrite-http-headers.md)
 - [Configurare la riscrittura dell'intestazione HTTP](rewrite-http-headers-portal.md)

## <a name="http-settings"></a>Impostazioni HTTP

Il gateway applicazione indirizza il traffico ai server back-end utilizzando la configurazione specificata qui. Dopo aver creato un'impostazione HTTP, è necessario associarla a una o più regole di routing delle richieste.

### <a name="cookie-based-affinity"></a>Affinità basata sui cookie

Il gateway applicazione di Azure usa i cookie gestiti dal gateway per la gestione delle sessioni utente. Quando un utente invia la prima richiesta al gateway applicazione, imposta un cookie di affinità nella risposta con un valore hash che contiene i dettagli della sessione, in modo che le richieste successive che trasportano il cookie di affinità verranno instradate allo stesso server back-end per mantenere la permanerietà. 

Questa funzionalità è utile quando si desidera mantenere una sessione utente sullo stesso server e quando lo stato della sessione viene salvato localmente sul server per una sessione utente. Se l'applicazione non è in grado di gestire l'affinità basata su cookie, non è possibile utilizzare questa funzionalità. Per utilizzarlo, assicurarsi che i client supportino i cookie.

L'aggiornamento [Chromium browser](https://www.chromium.org/Home) [v80](https://chromiumdash.appspot.com/schedule) ha portato un mandato in cui i cookie HTTP senza l'attributo [SameSite](https://tools.ietf.org/id/draft-ietf-httpbis-rfc6265bis-03.html#rfc.section.5.3.7) deve essere considerato come SameSite-Lax. Nel caso di richieste CORS (Cross-Origin Resource Sharing), se il cookie deve essere inviato in un contesto di terze parti, deve utilizzare *SameSite - Nessuno; Attributi protetti* e devono essere inviati solo tramite HTTPS. In caso contrario, in uno scenario solo HTTP, il browser non invia i cookie nel contesto di terze parti. L'obiettivo di questo aggiornamento da Chrome è quello di migliorare la sicurezza e di evitare attacchi Cross-Site Request Forgery (CSRF). 

Per supportare questa modifica, a partire dal 17 febbraio 2020, il gateway applicazione (tutti i tipi SKU) inserirà un altro cookie denominato *ApplicationGatewayAffinityCORS* oltre al cookie *ApplicationGatewayAffinity* esistente. Al cookie *ApplicationGatewayAffinityCORS* sono stati aggiunti altri due attributi (*"SameSite- None; "Secure"*) in modo che la sessione appiccicosa venga mantenuta anche per le richieste tra origini.

Si noti che il nome del cookie di affinità predefinito è *ApplicationGatewayAffinity* ed è possibile modificarlo. Nel caso in cui si sta utilizzando un nome di cookie di affinità personalizzato, viene aggiunto un cookie aggiuntivo con CORS come suffisso. Ad esempio, *CustomCookieNameCORS*.

> [!NOTE]
> Se è impostato l'attributo *SameSite-None,* è obbligatorio che il cookie contenga anche il flag *Secure* e debba essere inviato tramite HTTPS.  Se l'affinità di sessione è necessaria su CORS, è necessario eseguire la migrazione del carico di lavoro a HTTPS. Fare riferimento alla documentazione TLS offload e End-to-End TLS per Gateway applicazione qui - [Panoramica](ssl-overview.md), [Configurare un gateway applicazione con terminazione TLS tramite il portale](create-ssl-portal.md)di Azure , [Configurare TLS end-to-end utilizzando il gateway applicazione con il portale](end-to-end-ssl-portal.md).

### <a name="connection-draining"></a>Esaurimento delle connessioni

Lo svuotamento delle connessioni consente di rimuovere normalmente i membri del pool back-end durante gli aggiornamenti del servizio pianificati. È possibile applicare questa impostazione a tutti i membri di un pool back-end durante la creazione della regola. Garantisce che tutte le istanze di annullamento della registrazione di un pool back-end continuino a mantenere le connessioni esistenti e a soddisfare le richieste in corso per un timeout configurabile e non ricevano nuove richieste o connessioni. L'unica eccezione sono le richieste associate per l'annullamento della registrazione delle istanze a causa dell'affinità di sessione gestita dal gateway e continueranno a essere inoltrate alle istanze di annullamento della registrazione. Lo svuotamento della connessione si applica alle istanze back-end che vengono rimosse in modo esplicito dal pool back-end.

### <a name="protocol"></a>Protocollo

Il gateway applicazione supporta sia HTTP che HTTPS per il routing delle richieste ai server back-end. Se si sceglie HTTP, il traffico verso i server back-end non è crittografato. Se la comunicazione non crittografata non è accettabile, scegliere HTTPS.

Questa impostazione combinata con HTTPS nel listener supporta [TLS end-to-end](ssl-overview.md). Ciò consente di trasmettere in modo sicuro i dati sensibili crittografati al back-end. Ogni server back-end nel pool back-end con TLS end-to-end abilitato deve essere configurato con un certificato per consentire comunicazioni protette.

### <a name="port"></a>Porta

Questa impostazione specifica la porta in cui i server back-end ascoltano il traffico dal gateway applicazione. È possibile configurare porte comprese tra 1 e 65535.

### <a name="request-timeout"></a>Timeout richiesta

Questa impostazione è il numero di secondi che il gateway applicazione attende per ricevere una risposta dal server back-end.

### <a name="override-back-end-path"></a>Ignora percorso back-end

Questa impostazione consente di configurare un percorso di inoltro personalizzato facoltativo da utilizzare quando la richiesta viene inoltrata al back-end. Qualsiasi parte del percorso in ingresso che corrisponde al percorso personalizzato nel campo **percorso back-end** di sostituzione viene copiata nel percorso inoltrato. La tabella seguente mostra il funzionamento di questa funzionalità:

- Quando l'impostazione HTTP è collegata a una regola di routing delle richieste di base:When the HTTP setting is attached to a basic request-routing rule:

  | Richiesta originale  | Ignora percorso back-end | Richiesta inoltrata al back-end |
  | ----------------- | --------------------- | ---------------------------- |
  | /home/            | /override/            | /override/home/              |
  | /home/seconda casa/ | /override/            | /override/casa/seconda casa/   |

- Quando l'impostazione HTTP è collegata a una regola di routing delle richieste basata sul percorso:When the HTTP setting is attached to a path-based request-routing rule:

  | Richiesta originale           | Regola percorso       | Ignora percorso back-end | Richiesta inoltrata al back-end |
  | -------------------------- | --------------- | --------------------- | ---------------------------- |
  | /pathrule/home/            | /pathrule      | /override/            | /override/home/              |
  | /pathrule/casa/seconda casa/ | /pathrule      | /override/            | /override/casa/seconda casa/   |
  | /home/                     | /pathrule      | /override/            | /override/home/              |
  | /home/seconda casa/          | /pathrule      | /override/            | /override/casa/seconda casa/   |
  | /pathrule/home/            | /pathrule/home | /override/            | /override/                   |
  | /pathrule/casa/seconda casa/ | /pathrule/home | /override/            | /override/seconda casa/        |
  | /pathrule/                 | /pathrule/      | /override/            | /override/                   |

### <a name="use-for-app-service"></a>Uso per il servizio app

Si tratta solo di un collegamento dell'interfaccia utente che seleziona le due impostazioni necessarie per il back-end del servizio app di Azure.This is a UI only shortcut that selects the two required settings for the Azure App Service back-end. Abilita **il pick host dall'indirizzo back-end**e crea un nuovo probe personalizzato se non ne hai già uno. Per altre informazioni, vedere la sezione Selezionare il [nome host dall'indirizzo back-end](#pick) di questo articolo. Viene creato un nuovo probe e l'intestazione del probe viene selezionata dall'indirizzo del membro back-end.

### <a name="use-custom-probe"></a>Usa probe personalizzato

Questa impostazione associa un [probe personalizzato](application-gateway-probe-overview.md#custom-health-probe) a un'impostazione HTTP. È possibile associare un solo probe personalizzato a un'impostazione HTTP. Se non si associa in modo esplicito un probe personalizzato, il [probe predefinito](application-gateway-probe-overview.md#default-health-probe-settings) viene utilizzato per monitorare l'integrità del back-end. È consigliabile creare un probe personalizzato per un maggiore controllo sul monitoraggio dello stato dei back-end.

> [!NOTE]
> Il probe personalizzato non monitora l'integrità del pool back-end a meno che l'impostazione HTTP corrispondente non sia associata in modo esplicito a un listener.

### <a name="pick-host-name-from-back-end-address"></a><a id="pick"/></a>Selezionare il nome host dall'indirizzo back-end

Questa funzionalità imposta dinamicamente l'intestazione *host* nella richiesta sul nome host del pool back-end. Utilizza un indirizzo IP o FQDN.

Questa funzionalità è utile quando il nome di dominio del back-end è diverso dal nome DNS del gateway applicazione e il back-end si basa su un'intestazione host specifica per risolvere l'endpoint corretto.

Un caso di esempio è che i servizi multi-tenant sono i servizi back-end. Un servizio app è un servizio multi-tenant che usa uno spazio condiviso con un singolo indirizzo IP. Pertanto, è possibile accedere a un servizio app solo tramite i nomi host configurati nelle impostazioni di dominio personalizzate.

Per impostazione predefinita, il nome di dominio personalizzato è *example.azurewebsites.net*. Per accedere al servizio app usando un gateway applicazione tramite un nome host non registrato in modo esplicito nel servizio app o tramite il nome di dominio completo del gateway applicazione, eseguire l'override del nome host nella richiesta originale al nome host del servizio app. A tale scopo, abilitare l'impostazione di selezionare il **nome host dall'indirizzo back-end.**

Per un dominio personalizzato il cui nome DNS personalizzato esistente è mappato al servizio app, non è necessario abilitare questa impostazione.

> [!NOTE]
> Questa impostazione non è necessaria per l'ambiente del servizio app, che è una distribuzione dedicata.

### <a name="host-name-override"></a>Sostituzione del nome host

Questa funzionalità sostituisce l'intestazione *host* nella richiesta in ingresso nel gateway applicazione con il nome host specificato.

Ad esempio, se *www.contoso.com* viene specificato nell'impostazione **Nome host,** la richiesta originale viene`https://appgw.eastus.cloudapp.azure.com/path1` modificata in ,`https://www.contoso.com/path1` quando la richiesta viene inoltrata al server back-end.

## <a name="back-end-pool"></a>Pool back-end

È possibile puntare un pool back-end a quattro tipi di membri back-end: una macchina virtuale specifica, un set di scalabilità di macchine virtuali, un indirizzo IP/FQDN o un servizio app. 

Dopo aver creato un pool back-end, è necessario associarlo a una o più regole di routing delle richieste. È inoltre necessario configurare i probe di integrità per ogni pool back-end nel gateway applicazione. Quando viene soddisfatta una condizione della regola di routing delle richieste, il gateway applicazione inoltra il traffico ai server integri (come determinato dai probe di integrità) nel pool back-end corrispondente.

## <a name="health-probes"></a>Probe di integrità

Un gateway applicazione esegue il monitoraggio dell'integrità di tutte le risorse nel back-end per impostazione predefinita. È tuttavia consigliabile creare un probe personalizzato per ogni impostazione HTTP back-end per ottenere un maggiore controllo sul monitoraggio dell'integrità. Per informazioni su come configurare un probe personalizzato, vedere [Impostazioni personalizzate](application-gateway-probe-overview.md#custom-health-probe-settings)del probe di integrità .

> [!NOTE]
> Dopo aver creato un probe di integrità personalizzato, è necessario associarlo a un'impostazione HTTP back-end. Un probe personalizzato non monitorerà l'integrità del pool back-end a meno che l'impostazione HTTP corrispondente non sia associata in modo esplicito a un listener tramite una regola.

## <a name="next-steps"></a>Passaggi successivi

Ora che si conoscono i componenti del gateway applicazione, è possibile:Now that you know about Application Gateway components, you can:

- [Creare un gateway applicazione nel portale di AzureCreate an application gateway in the Azure portal](quick-create-portal.md)
- [Creare un gateway applicazione tramite PowerShellCreate an application gateway by using PowerShell](quick-create-powershell.md)
- [Creare un gateway applicazione con l'interfaccia della riga di comando di Azure](quick-create-cli.md)
