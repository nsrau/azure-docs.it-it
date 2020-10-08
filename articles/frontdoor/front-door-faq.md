---
title: Sportello anteriore di Azure-Domande frequenti
description: Questa pagina fornisce le risposte alle domande frequenti su Azure front door
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/18/2020
ms.author: duau
ms.openlocfilehash: 45f9e7a4e508cffd3593cec7bbcea3dd7882a60c
ms.sourcegitcommit: d2222681e14700bdd65baef97de223fa91c22c55
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/07/2020
ms.locfileid: "91819034"
---
# <a name="frequently-asked-questions-for-azure-front-door"></a>Domande frequenti per Azure front door

Questo articolo risponde alle domande più comuni sulle funzionalità e sulle funzionalità di Azure front door. Se non è presente la risposta a una domanda specifica, è possibile contattare Microsoft tramite i seguenti canali (in ordine di escalation):

1. Sezione dei commenti di questo articolo.
2. [UserVoice di Azure front door](https://feedback.azure.com/forums/217313-networking?category_id=345025).
3. **Supporto tecnico Microsoft:** Per creare una nuova richiesta di supporto, nella portale di Azure della scheda **Guida** selezionare il pulsante Guida e **supporto** e quindi selezionare **nuova richiesta di supporto**.

## <a name="general"></a>Generale

### <a name="what-is-azure-front-door"></a>Che cos'è il servizio Frontdoor di Azure?

Front-end di Azure è un servizio di Application Delivery Network (ADN), che offre diverse funzionalità di bilanciamento del carico di livello 7 per le applicazioni. Offre funzionalità di accelerazione sito dinamico, nonché di bilanciamento del carico globale con failover near real-time. Si tratta di un servizio scalabile e a disponibilità elevata, completamente gestito da Azure.

### <a name="what-features-does-azure-front-door-support"></a>Quali funzionalità sono supportate da Azure front door?

Azure front door supporta accelerazione sito dinamico (DSA), offload TLS/SSL e TLS end-to-end, Web Application Firewall, affinità di sessione basata su cookie, routing basato su percorso URL, certificati gratuiti e più gestione del dominio e altri. Per un elenco completo delle funzionalità supportate, vedere [Panoramica di sportello anteriore di Azure](front-door-overview.md).

### <a name="what-is-the-difference-between-azure-front-door-and-azure-application-gateway"></a>Qual è la differenza tra la porta di ingresso di Azure e il gateway applicazione Azure?

Anche se la porta anteriore e il gateway applicazione sono servizi di bilanciamento del carico di livello 7 (HTTP/HTTPS), la differenza principale è che la porta anteriore è un servizio globale, mentre il gateway applicazione è un servizio a livello di area. Mentre la porta anteriore è in grado di bilanciare il carico tra le diverse unità di scala/cluster/unità di timbri tra le aree, il gateway applicazione consente di bilanciare il carico tra le macchine virtuali/contenitori e così via, all'interno dell'unità di scala.

### <a name="when-should-we-deploy-an-application-gateway-behind-front-door"></a>Quando è necessario distribuire un gateway applicazione dietro la porta anteriore?

Gli scenari principali per cui è consigliabile usare il gateway applicazione dietro la porta anteriore sono:

- Lo sportello anteriore può eseguire il bilanciamento del carico basato sul percorso solo a livello globale, ma se si vuole bilanciare il carico del traffico ancora più all'interno della rete virtuale (VNET), è necessario usare il gateway applicazione.
- Poiché la porta anteriore non funziona a livello di macchina virtuale o contenitore, non è possibile eseguire lo svuotamento della connessione. Tuttavia, il gateway applicazione consente di eseguire lo svuotamento della connessione. 
- Con un gateway applicazione dietro la porta anteriore, è possibile ottenere il 100% di offload TLS/SSL e instradare solo le richieste HTTP all'interno della rete virtuale (VNET).
- La porta anteriore e il gateway applicazione supportano entrambi l'affinità di sessione. Mentre la porta anteriore può indirizzare il traffico successivo da una sessione utente allo stesso cluster o back-end in una determinata area, il gateway applicazione può indirizzare il traffico creare affinità tra al server all'interno del cluster.  

### <a name="can-we-deploy-azure-load-balancer-behind-front-door"></a>È possibile distribuire Azure Load Balancer dietro la porta anteriore?

Per eseguire il routing del traffico a, il front-end di Azure deve avere un indirizzo VIP pubblico o un nome DNS disponibile pubblicamente. La distribuzione di un Azure Load Balancer dietro la porta anteriore è un caso d'uso comune.

### <a name="what-protocols-does-azure-front-door-support"></a>Quali protocolli sono supportati da Azure front door?

Azure front door supporta HTTP, HTTPS e HTTP/2.

### <a name="how-does-azure-front-door-support-http2"></a>In che modo Azure front door supporta HTTP/2?

Il supporto del protocollo HTTP/2 è disponibile solo per i client che si connettono all'ingresso di Azure. La comunicazione ai back-end nel pool back-end è su HTTP/1.1. Il supporto HTTP/2 è abilitato per impostazione predefinita.

### <a name="what-resources-are-supported-today-as-part-of-backend-pool"></a>Quali risorse sono supportate oggi nell'ambito del pool back-end?

I pool back-end possono essere costituiti da archiviazione, app Web, istanze Kubernetes o qualsiasi altro nome host personalizzato con connettività pubblica. Per lo sportello anteriore di Azure è necessario che i backend siano definiti tramite un indirizzo IP pubblico o un nome host DNS risolvibile pubblicamente. I membri dei pool back-end possono trovarsi in zone, aree o anche all'esterno di Azure, purché dispongano di connettività pubblica.

### <a name="what-regions-is-the-service-available-in"></a>In quale aree è disponibile il servizio?

Il front-end di Azure è un servizio globale e non è associato ad alcuna area di Azure specifica. L'unica posizione che è necessario specificare durante la creazione di una porta anteriore è la posizione del gruppo di risorse, che in sostanza specifica la posizione in cui verranno archiviati i metadati per il gruppo di risorse. La risorsa della porta anteriore stessa viene creata come risorsa globale e la configurazione viene distribuita a livello globale in tutti i pop (punto di presenza). 

### <a name="what-are-the-pop-locations-for-azure-front-door"></a>Quali sono i percorsi POP per il front-end di Azure?

Il front-end di Azure presenta lo stesso elenco di località POP (Point of Presence) della rete CDN di Azure di Microsoft. Per l'elenco completo dei pop, vedere le [località pop della rete CDN di Azure di Microsoft](https://docs.microsoft.com/azure/cdn/cdn-pop-locations).

### <a name="is-azure-front-door-a-dedicated-deployment-for-my-application-or-is-it-shared-across-customers"></a>Il front-end di Azure è una distribuzione dedicata per l'applicazione o è condivisa tra i clienti?

Azure front door è un servizio multi-tenant distribuito a livello globale. Quindi, l'infrastruttura per la porta anteriore è condivisa tra tutti i clienti. Tuttavia, creando un profilo di porta anteriore, si definisce la configurazione specifica richiesta per l'applicazione e non vengono apportate modifiche alla porta anteriore che influiscano su altre configurazioni di porte anteriori.

### <a name="is-http-https-redirection-supported"></a>È supportato il reindirizzamento HTTP->HTTPS?

Sì. In realtà, il front-end di Azure supporta il reindirizzamento di host, percorsi e stringhe di query nonché parte del reindirizzamento URL. Altre informazioni sul [Reindirizzamento URL](front-door-url-redirect.md). 

### <a name="in-what-order-are-routing-rules-processed"></a>In quale ordine vengono elaborate le regole di routing?

Le route per la porta anteriore non sono ordinate e viene selezionata una route specifica in base alla migliore corrispondenza. Altre informazioni sul [modo in cui lo sportello anteriore corrisponde alle richieste a una regola di routing](front-door-route-matching.md).

### <a name="how-do-i-lock-down-the-access-to-my-backend-to-only-azure-front-door"></a>Ricerca per categorie bloccare l'accesso al back-end solo alla porta di ingresso di Azure?

Per bloccare l'applicazione in modo che accetti il traffico solo da un sportello anteriore specifico, è necessario configurare gli ACL IP per il back-end e quindi limitare il traffico nel back-end al valore specifico dell'intestazione ' X-Azure-FDID ' inviato dalla porta anteriore. Questi passaggi sono descritti di seguito:

- Configurare ACLing IP per i back-end in modo che accettino il traffico dallo spazio degli indirizzi IP back-end di Azure front door e dai servizi di infrastruttura di Azure. Vedere i dettagli dell'indirizzo IP seguente per ACLing il back-end:
 
    - Vedere la sezione *AzureFrontDoor. backend* negli [intervalli IP di Azure e nei tag del servizio](https://www.microsoft.com/download/details.aspx?id=56519) per l'intervallo di indirizzi IP del back-end IPv4 di front-end oppure è possibile usare anche il tag di servizio *AzureFrontDoor. backend* nei [gruppi di sicurezza di rete](https://docs.microsoft.com/azure/virtual-network/security-overview#security-rules).
    - Lo spazio IP del back-end **IPv6** dello sportello anteriore mentre è incluso nel tag del servizio non è elencato nel file JSON degli intervalli IP di Azure. Se si sta cercando un intervallo di indirizzi IPv6 esplicito, è attualmente limitato a `2a01:111:2050::/44`
    - Servizi di [infrastruttura di base](https://docs.microsoft.com/azure/virtual-network/security-overview#azure-platform-considerations) di Azure tramite indirizzi IP host virtualizzati: `168.63.129.16` e `169.254.169.254`

    > [!WARNING]
    > Lo spazio IP back-end della porta anteriore potrebbe cambiare in un secondo momento, tuttavia, prima che ciò accada, avremmo integrato gli [intervalli IP di Azure e i tag di servizio](https://www.microsoft.com/download/details.aspx?id=56519). È consigliabile sottoscrivere gli [intervalli IP e i tag di servizio di Azure](https://www.microsoft.com/download/details.aspx?id=56519) per eventuali modifiche o aggiornamenti.

-    Eseguire un'operazione GET sulla porta anteriore con la versione API `2020-01-01` o successiva. Nella chiamata API cercare il `frontdoorID` campo. Filtrare in base all'intestazione in ingresso '**X-Azure-FDID**' inviata dalla porta anteriore al back-end con il valore come quello del campo `frontdoorID` . È anche possibile trovare `Front Door ID` il valore nella sezione Panoramica della pagina del portale di porta anteriore. 

- Applicare il filtro regole nel server Web back-end per limitare il traffico in base al valore dell'intestazione ' X-Azure-FDID ' risultante.

  Di seguito è riportato un esempio per [Microsoft Internet Information Services (IIS)](https://www.iis.net/):

    ``` xml
    <?xml version="1.0" encoding="UTF-8"?>
    <configuration>
        <system.webServer>
            <rewrite>
                <rules>
                    <rule name="Filter_X-Azure-FDID" patternSyntax="Wildcard" stopProcessing="true">
                        <match url="*" />
                        <conditions>
                            <add input="{HTTP_X_AZURE_FDID}" pattern="xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" negate="true" />
                        </conditions>
                        <action type="AbortRequest" />
                    </rule>
                </rules>
            </rewrite>
        </system.webServer>
    </configuration>
    ```



### <a name="can-the-anycast-ip-change-over-the-lifetime-of-my-front-door"></a>È possibile modificare l'IP anycast per la durata della mia porta anteriore?

L'IP anycast front-end per la porta anteriore in genere non cambia e può rimanere statico per la durata della porta anteriore. Non sono tuttavia previste **garanzie** per lo stesso. Non eseguire alcuna dipendenza diretta sull'IP.

### <a name="does-azure-front-door-support-static-or-dedicated-ips"></a>Lo sportello anteriore di Azure supporta IP statici o dedicati?

No, il front-end di Azure attualmente non supporta gli IP anycast anycast statici o dedicati. 

### <a name="does-azure-front-door-support-x-forwarded-for-headers"></a>Lo sportello anteriore di Azure supporta le intestazioni x-inoltred-for?

Sì, lo sportello anteriore di Azure supporta le intestazioni x-Inoltred-for, X-Inoltred-host e X-Inoltred-proto. Per X-Inoltred-per se l'intestazione è già presente, l'indirizzo IP del socket client viene aggiunto al connettore. In caso contrario, aggiunge l'intestazione con l'indirizzo IP del socket client come valore. Per X-Inoltred-host e X-Inoltred-proto, il valore viene sottoposto a override.

Altre informazioni sulle [intestazioni HTTP supportate per la porta anteriore](front-door-http-headers-protocol.md).  

### <a name="how-long-does-it-take-to-deploy-an-azure-front-door-does-my-front-door-still-work-when-being-updated"></a>Quanto tempo è necessario per distribuire un front-end di Azure? Lo sportello anteriore continua a funzionare durante l'aggiornamento?

Una nuova creazione di porte anteriori o qualsiasi aggiornamento a una porta anteriore esistente richiede da 3 a 5 minuti per la distribuzione globale. Questo significa che in circa 3-5 minuti la configurazione della porta anteriore verrà distribuita in tutti i nostri pop a livello globale.

Nota: gli aggiornamenti del certificato TLS/SSL personalizzati impiegano circa 30 minuti per essere distribuiti a livello globale.

Eventuali aggiornamenti alle route o ai pool back-end e così via sono senza interruzioni e provocheranno un tempo di inattività pari a zero, se la nuova configurazione è corretta. Gli aggiornamenti dei certificati sono anche atomici e non provocano alcuna interruzione, a meno che non si passi da' AFD Managed ' a' use your own cert ' o viceversa.


## <a name="configuration"></a>Configurazione

### <a name="can-azure-front-door-load-balance-or-route-traffic-within-a-virtual-network"></a>Il servizio di bilanciamento del carico di Azure può instradare il traffico all'interno di una rete virtuale?

Il servizio di Azure front door (AFD) richiede un indirizzo IP pubblico o un nome DNS risolvibile pubblicamente per instradare il traffico. Quindi, la risposta non è direttamente in grado di eseguire il routing all'interno di una rete virtuale, ma l'uso di un gateway applicazione o di un Azure Load Balancer tra risolvono questo scenario.

### <a name="what-are-the-various-timeouts-and-limits-for-azure-front-door"></a>Quali sono i diversi timeout e limiti per lo sportello anteriore di Azure?

Informazioni su tutti i [timeout e i limiti](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#azure-front-door-service-limits)documentati per il front-end di Azure.

### <a name="how-long-does-it-take-for-a-rule-to-take-effect-after-being-added-to-the-front-door-rules-engine"></a>Quanto tempo è necessario per rendere effettive le regole dopo essere state aggiunte al motore delle regole della porta anteriore?

La configurazione del motore regole richiede circa 10-15 minuti per completare un aggiornamento. È possibile fare in modo che la regola abbia effetto non appena l'aggiornamento viene completato. 

## <a name="performance"></a>Prestazioni

### <a name="how-does-azure-front-door-support-high-availability-and-scalability"></a>In che modo Azure front door supporta la disponibilità e la scalabilità elevate?

Il front-end di Azure è una piattaforma multi-tenant distribuita a livello globale con volumi elevati di capacità per soddisfare le esigenze di scalabilità dell'applicazione. Distribuito dal perimetro della rete globale di Microsoft, la porta anteriore fornisce funzionalità di bilanciamento del carico globale che consentono di eseguire il failover dell'intera applicazione o anche di singoli microservizi tra aree o cloud diversi.

## <a name="tls-configuration"></a>Configurazione TLS

### <a name="what-tls-versions-are-supported-by-azure-front-door"></a>Quali versioni di TLS sono supportate da Azure front door?

Tutti i profili di porte anteriori creati dopo il 2019 settembre usano TLS 1,2 come minimo predefinito.

Front door supporta le versioni di TLS 1,0, 1,1 e 1,2. TLS 1,3 non è ancora supportato.

### <a name="what-certificates-are-supported-on-azure-front-door"></a>Quali certificati sono supportati in Azure front door?

Per abilitare il protocollo HTTPS per la distribuzione sicura di contenuto in un dominio personalizzato di una porta anteriore, è possibile scegliere di usare un certificato gestito da Azure front door o usare il proprio certificato.
L'opzione Managed Door (porta anteriore) effettua il provisioning di un certificato TLS/SSL standard tramite DigiCert e viene archiviato nel Key Vault di porte anteriori Se si sceglie di usare il proprio certificato, è possibile caricare un certificato da un'autorità di certificazione supportata e può essere un certificato standard TLS, Extended Validation o anche un certificato con caratteri jolly. I certificati autofirmati non sono supportati. Informazioni [su come abilitare HTTPS per un dominio personalizzato](https://aka.ms/FrontDoorCustomDomainHTTPS).

### <a name="does-front-door-support-autorotation-of-certificates"></a>Lo sportello anteriore supporta la rotazione automatica dei certificati?

Per l'opzione del certificato gestito dalla porta anteriore, i certificati vengono ruotati in base alla porta anteriore. Se si usa un certificato gestito da sportello anteriore e si verifica che la data di scadenza del certificato è inferiore a 60 giorni, archiviare un ticket di supporto.
</br>Per il certificato TLS/SSL personalizzato, la rotazione automatica non è supportata. Analogamente al modo in cui è stato configurato la prima volta per un determinato dominio personalizzato, è necessario puntare davanti alla versione del certificato corretta nell'Key Vault e assicurarsi che l'entità servizio per la porta anteriore abbia ancora accesso al Key Vault. Questa operazione di implementazione del certificato aggiornata dalla porta anteriore è atomica e non provoca alcun effetto di produzione, purché il nome del soggetto o la SAN per il certificato non cambino.

### <a name="what-are-the-current-cipher-suites-supported-by-azure-front-door"></a>Quali sono i pacchetti di crittografia attualmente supportati da Azure front door?

Per TLS 1.2 sono supportati i pacchetti di crittografia seguenti: 

- TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_DHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_DHE_RSA_WITH_AES_128_GCM_SHA256

Quando si usano domini personalizzati con TLS 1.0/1.1 abilitato, sono supportati i pacchetti di crittografia seguenti:

- TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA
- TLS_RSA_WITH_AES_256_GCM_SHA384
- TLS_RSA_WITH_AES_128_GCM_SHA256
- TLS_RSA_WITH_AES_256_CBC_SHA256
- TLS_RSA_WITH_AES_128_CBC_SHA256
- TLS_RSA_WITH_AES_256_CBC_SHA
- TLS_RSA_WITH_AES_128_CBC_SHA
- TLS_DHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_DHE_RSA_WITH_AES_256_GCM_SHA384

### <a name="can-i-configure-tls-policy-to-control-tls-protocol-versions"></a>È possibile configurare I criteri TLS per controllare le versioni del protocollo TLS?

È possibile configurare una versione minima di TLS nella porta anteriore di Azure nelle impostazioni HTTPS del dominio personalizzato tramite portale di Azure o l' [API REST di Azure](https://docs.microsoft.com/rest/api/frontdoorservice/frontdoor/frontdoors/createorupdate#minimumtlsversion). Attualmente, è possibile scegliere tra 1,0 e 1,2.

### <a name="can-i-configure-front-door-to-only-support-specific-cipher-suites"></a>È possibile configurare la porta anteriore per supportare solo pacchetti di crittografia specifici?

No, la configurazione della porta anteriore per specifici pacchetti di crittografia non è supportata. Tuttavia, è possibile ottenere un certificato TLS/SSL personalizzato dall'autorità di certificazione (ad indicare VeriSign, Entrust o DigiCert) e disporre di specifici pacchetti di crittografia contrassegnati per il certificato al momento della generazione. 

### <a name="does-front-door-support-ocsp-stapling"></a>Lo sportello anteriore supporta la graffatura OCSP?

Sì, la graffettatura OCSP è supportata per impostazione predefinita da sportello anteriore e non è necessaria alcuna configurazione.

### <a name="does-azure-front-door-also-support-re-encryption-of-traffic-to-the-backend"></a>Il front-end di Azure supporta nuovamente la crittografia del traffico nel back-end?

Sì, la porta anteriore di Azure supporta l'offload TLS/SSL e il protocollo TLS end-to-end, che consente di crittografare nuovamente il traffico nel back-end. Infatti, poiché le connessioni al back-end avvengono tramite l'indirizzo IP pubblico, è consigliabile configurare la porta anteriore per l'uso di HTTPS come protocollo di trasmissione.

### <a name="does-front-door-support-self-signed-certificates-on-the-backend-for-https-connection"></a>La porta anteriore supporta i certificati autofirmati nel back-end per la connessione HTTPS?

No, i certificati autofirmati non sono supportati nella porta anteriore e la restrizione si applica a entrambi:

1. **Backend**: non è possibile usare certificati autofirmati quando si esegue l'inoltro del traffico come probe di integrità HTTPS o HTTPS o si compila la cache per da origine per le regole di routing con la memorizzazione nella cache abilitata.
2. Front- **end**: non è possibile usare certificati autofirmati quando si usa un certificato TLS/SSL personalizzato per abilitare HTTPS nel dominio personalizzato.

### <a name="why-is-https-traffic-to-my-backend-failing"></a>Perché il traffico HTTPS verso il back-end non riesce?

Per avere correttamente le connessioni HTTPS al back-end, sia per i probe di integrità che per le richieste di invio, potrebbero verificarsi due motivi per cui il traffico HTTPS potrebbe non riuscire:

1. **Mancata corrispondenza del nome del soggetto del certificato**: per le connessioni HTTPS, la porta anteriore prevede che il back-end presenti un certificato da una CA valida con i nomi di soggetto corrispondenti al nome host del back-end. Ad esempio, se il nome host del back-end è impostato su `myapp-centralus.contosonews.net` e il certificato presentato dal back-end durante l'handshake TLS non ha `myapp-centralus.contosonews.net` né `*myapp-centralus*.contosonews.net` nel nome del soggetto, la porta anteriore rifiuterà la connessione e restituirà un errore. 
    1. **Soluzione**: Sebbene non sia consigliabile dal punto di vista della conformità, è possibile risolvere questo errore disabilitando la verifica del nome del soggetto del certificato per la porta anteriore. Questo è presente in impostazioni in portale di Azure e in BackendPoolsSettings nell'API.
2. **Certificato di hosting back-end da autorità di certificazione non valida**: solo i certificati delle [autorità di certificazione valide](/azure/frontdoor/front-door-troubleshoot-allowed-ca) possono essere usati nel back-end con sportello anteriore. I certificati da autorità di certificazione interne o certificati autofirmati non sono consentiti.

### <a name="can-i-use-clientmutual-authentication-with-azure-front-door"></a>È possibile usare l'autenticazione client/reciproca con lo sportello anteriore di Azure?

No. Sebbene il front-end di Azure supporti TLS 1,2, che ha introdotto l'autenticazione client/reciproca in [RFC 5246](https://tools.ietf.org/html/rfc5246), attualmente, la porta anteriore di Azure non supporta l'autenticazione client/reciproca.

## <a name="diagnostics-and-logging"></a>Diagnostica e registrazione

### <a name="what-types-of-metrics-and-logs-are-available-with-azure-front-door"></a>Quali tipi di metriche e log sono disponibili con Azure front door?

Per informazioni sui log e altre funzionalità di diagnostica, vedere [monitoraggio di metriche e log per lo sportello anteriore](front-door-diagnostics.md).

### <a name="what-is-the-retention-policy-on-the-diagnostics-logs"></a>Che cos'è il criterio di conservazione per i log di diagnostica?

I log di diagnostica vengono inseriti nell'account di archiviazione dei clienti, i quali possono impostare i criteri di conservazione in base alle preferenze. I log di diagnostica possono anche essere inviati a un hub eventi o a log di Monitoraggio di Azure. Per altre informazioni, vedere [diagnostica di Azure front door](front-door-diagnostics.md).

### <a name="how-do-i-get-audit-logs-for-azure-front-door"></a>Ricerca per categorie ottenere i log di controllo per Azure front door?

I log di controllo sono disponibili per il front-end di Azure. Nel portale fare clic su **log attività** nel pannello dei menu della porta anteriore per accedere al log di controllo. 

### <a name="can-i-set-alerts-with-azure-front-door"></a>È possibile impostare gli avvisi con lo sportello anteriore di Azure?

Sì, la porta anteriore di Azure supporta gli avvisi. Gli avvisi vengono configurati nelle metriche. 

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [creare una Frontdoor](quickstart-create-front-door.md).
- Informazioni sul [funzionamento di Frontdoor](front-door-routing-architecture.md).
