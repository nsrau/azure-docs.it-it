---
title: Che cos'è il servizio Azure Private Link?
description: Informazioni sul servizio Azure Private Link.Learn about Azure Private Link service.
services: private-link
author: sumeetmittal
ms.service: private-link
ms.topic: conceptual
ms.date: 09/16/2019
ms.author: sumi
ms.openlocfilehash: 2cc6c577abdb3698ef6aca1f1f04d239f09d119c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79280430"
---
# <a name="what-is-azure-private-link-service"></a>Che cos'è il servizio Azure Private Link?

Il servizio Collegamento privato di Azure è il riferimento a un servizio personalizzato basato su Collegamento privato di Azure. Il servizio in esecuzione dietro [Azure Standard Load Balancer](../load-balancer/load-balancer-standard-overview.md) può essere abilitato per l'accesso Private Link in modo che gli utenti del servizio possano accedervi privatamente dalle proprie reti virtuali. I clienti possono creare un endpoint privato all'interno della rete virtuale e mapparlo a questo servizio. In questo articolo vengono illustrati i concetti relativi al lato provider di servizi. 

## <a name="workflow"></a>Flusso di lavoro

![Flusso di lavoro del servizio di collegamento privatoPrivate Link service workflow](media/private-link-service-overview/private-link-service-workflow.png)

### <a name="create-your-private-link-service"></a>Crea il tuo servizio di collegamento privato

- Configurare l'applicazione per l'esecuzione dietro un servizio di bilanciamento del carico standard nella rete virtuale. Se l'applicazione è già configurata dietro un servizio di bilanciamento del carico standard, è possibile ignorare questo passaggio.   
- Creare un servizio di collegamento privato che fa riferimento al servizio di bilanciamento del carico precedente. Nel processo di selezione del servizio di bilanciamento del carico scegliere la configurazione IP front-end in cui si vuole ricevere il traffico. Scegliere una subnet per gli indirizzi IP NAT per il servizio di collegamento privato. Si consiglia di avere almeno otto indirizzi IP NAT disponibili nella subnet. Tutto il traffico consumer sembrerà provenire da questo pool di indirizzi IP privati al provider di servizi. Scegliere le proprietà/impostazioni appropriate per il servizio di collegamento privato.    

    > [!NOTE]
    > Il servizio di collegamento privato di Azure è supportato solo in Load Balancer standard. 
    
### <a name="share-your-service"></a>Condividi il tuo servizio

Dopo aver creato un servizio di collegamento privato, Azure genererà un moniker denominato univoco a livello globale denominato "alias" in base al nome fornito per il servizio. È possibile condividere l'alias o l'URI della risorsa del servizio con i clienti offline. I consumer possono avviare una connessione Private Link usando l'alias o l'URI della risorsa.
 
### <a name="manage-your-connection-requests"></a>Gestire le richieste di connessione

Dopo che un consumer avvia una connessione, il provider di servizi può accettare o rifiutare la richiesta di connessione. Tutte le richieste di connessione verranno elencate nella proprietà **privateendpointconnections** nel servizio Private Link.
 
### <a name="delete-your-service"></a>Eliminare il servizio

Se il servizio Private Link non è più in uso, è possibile eliminarlo. Tuttavia, prima di eliminare il servizio, assicurarsi che non vi siano connessioni endpoint private associate. È possibile rifiutare tutte le connessioni ed eliminare il servizio.

## <a name="properties"></a>Proprietà

Un servizio Private Link specifica le proprietà seguenti:A Private Link service specifies the following properties: 

|Proprietà |Spiegazione  |
|---------|---------|
|Stato provisioning (provisioningState)  |Proprietà di sola lettura che elenca lo stato di provisioning corrente per il servizio Private Link. Gli stati di provisioning applicabili sono: "Eliminazione; Operazione non riuscita; Riuscito; Aggiornamento". Quando lo stato di provisioning è "Succeeded", il provisioning del servizio Private Link è stato eseguito correttamente.        |
|Alias (alias)     | Alias è una stringa di sola lettura univoca a livello globale per il servizio. Ti aiuta a mascherare i dati dei clienti per il tuo servizio e allo stesso tempo crea un nome facile da condividere per il tuo servizio. Quando si crea un servizio di collegamento privato, Azure genera l'alias per il servizio che è possibile condividere con i clienti. I clienti possono utilizzare questo alias per richiedere una connessione al servizio.          |
|Visibilità (visibilità)     | Visibilità è la proprietà che controlla le impostazioni di esposizione per il servizio Private Link. I provider di servizi possono scegliere di limitare l'esposizione al servizio alle sottoscrizioni con autorizzazioni di controllo degli accessi in base al ruolo, un set limitato di sottoscrizioni o tutte le sottoscrizioni di Azure.Service providers can choose to limit the exposure to their service to subscriptions with role-based access control (RBAC) permissions , a restricted set of subscriptions, or all Azure subscriptions.          |
|Approvazione automatica (approvazione automatica)    |   L'approvazione automatica controlla l'accesso automatico al servizio Private Link. Le sottoscrizioni specificate nell'elenco di approvazione automatica vengono approvate automaticamente quando viene richiesta una connessione dagli endpoint privati in tali sottoscrizioni.          |
|Configurazione IP front-end di Load Balancer (loadBalancerFrontendIpConfigurations)    |    Private Link service is tied to the frontend IP address of a Standard Load Balancer. Tutto il traffico destinato al servizio raggiungerà il frontend della SLB. È possibile configurare le regole SLB per indirizzare il traffico a pool back-end appropriati in cui sono in esecuzione le applicazioni. Le configurazioni IP front-end del servizio di bilanciamento del carico sono diverse dalle configurazioni IP NAT.      |
|Configurazione IP NAT (ipConfigurations)    |    Questa proprietà fa riferimento alla configurazione IP NAT (Network Address Translation) per il servizio Private Link. L'IP NAT può essere scelto da qualsiasi subnet nella rete virtuale di un provider di servizi. Il servizio Private Link esegue NAT-ing sul lato di destinazione sul traffico Private Link. In questo modo si garantisce che non vi sia alcun conflitto IP tra lo spazio di indirizzi di origine (lato consumer) e di destinazione (provider di servizi). Sul lato di destinazione (lato provider di servizi), l'indirizzo IP NAT verrà visualizzato come IP di origine per tutti i pacchetti ricevuti dal servizio e dall'IP di destinazione per tutti i pacchetti inviati dal servizio.       |
|Connessioni endpoint privati (privateEndpointConnections)Private endpoint connections (privateEndpointConnections)     |  Questa proprietà elenca gli endpoint privati che si connettono al servizio Private Link. Più endpoint privati possono connettersi allo stesso servizio Private Link e il provider di servizi può controllare lo stato dei singoli endpoint privati.        |
|Proxy TCP V2 (EnableProxyProtocol)     |  Questa proprietà consente al provider di servizi di utilizzare il proxy tcp v2 per recuperare le informazioni di connessione sul consumer del servizio. Il provider di servizi è responsabile della configurazione delle configurazioni del ricevitore per poter analizzare l'intestazione del protocollo proxy v2.        |
|||


### <a name="details"></a>Dettagli

- È possibile accedere al servizio Private Link dagli endpoint privati approvati nella stessa area. L'endpoint privato può essere raggiunto dalla stessa rete virtuale, dalle reti virtuali con peering regionale, dalle reti virtuali con peering globale e in locale usando connessioni VPN private o ExpressRoute.The private endpoint can be reached from the same virtual network, regionally peered VNets, globally peered VNets and on premises using private VPN or ExpressRoute connections. 
 
- Quando si crea un servizio di collegamento privato, viene creata un'interfaccia di rete per il ciclo di vita della risorsa. Questa interfaccia non è gestibile dal cliente.
 
- The Private Link Service must be deployed in the same region as the virtual network and the Standard Load Balancer.  
 
- È possibile accedere a un singolo servizio di collegamenti privati da più endpoint privati appartenenti a reti virtuali, sottoscrizioni e/o tenant di Active Directory diversi. La connessione viene stabilita tramite un flusso di lavoro di connessione. 
 
- È possibile creare più servizi Private Link nello stesso load balancer Standard usando configurazioni IP front-end diverse. Esistono limiti al numero di servizi Private Link che è possibile creare per ogni Load Balancer Standard e per sottoscrizione. Per informazioni dettagliate, vedere Limiti di [Azure](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#networking-limits).
 
- Il servizio Private Link può avere più di una configurazione NAT IP collegata ad esso. La scelta di più configurazioni IP NAT può aiutare i provider di servizi a scalare. Oggi, i provider di servizi possono assegnare fino a otto indirizzi IP NAT per ogni servizio Private Link. Con ogni indirizzo IP NAT, è possibile assegnare più porte per le connessioni TCP e quindi scalare orizzontalmente. Dopo aver aggiunto più indirizzi IP NAT a un servizio Private Link, non è possibile eliminare gli indirizzi IP NAT. Questa operazione viene eseguita per garantire che le connessioni attive non siano interessate durante l'eliminazione degli indirizzi IP NAT.


## <a name="alias"></a>Alias

**Alias** è un nome univoco globale per il servizio. Ti aiuta a mascherare i dati dei clienti per il tuo servizio e allo stesso tempo crea un nome facile da condividere per il tuo servizio. Quando si crea un servizio di collegamento privato, Azure genera un alias per il servizio che è possibile condividere con i clienti. I clienti possono utilizzare questo alias per richiedere una connessione al servizio.

L'alias è composto da tre parti: *Prefisso*. *GUID*. *Suffisso*

- Prefisso è il nome del servizio. È possibile scegliere il proprio prefisso. Dopo aver creato "Alias", non è possibile modificarlo, quindi seleziona il prefisso in modo appropriato.  
- GUID verrà fornito dalla piattaforma. Ciò consente di rendere il nome globalmente univoco. 
- Suffisso aggiunto da Azure: area .azure.privatelinkserviceSuffix is appended by Azure: *region*.azure.privatelinkservice 

Alias completo: *Prefisso*. "GUID". *regione*.azure.privatelinkservice  

## <a name="control-service-exposure"></a>Controllare l'esposizione del servizio

Il servizio Private Link offre opzioni per controllare l'esposizione del servizio tramite l'impostazione "Visibilità". È possibile rendere privato il servizio per l'utilizzo da diverse reti virtuali di cui si è proprietari (solo autorizzazioni RBAC), limitare l'esposizione a un set limitato di sottoscrizioni attendibili o renderlo pubblico in modo che tutte le sottoscrizioni di Azure possano richiedere connessioni nel collegamento privato Servizio. Le impostazioni di visibilità decidono se un consumer può connettersi al servizio o meno. 

## <a name="control-service-access"></a>Controllare l'accesso al servizio

I consumer che hanno un'esposizione (controllata dall'impostazione di visibilità) al servizio Private Link possono creare un endpoint privato nelle reti virtuali e richiedere una connessione al servizio Private Link. La connessione all'endpoint privato verrà creata in uno stato "In sospeso" nell'oggetto servizio Private Link. Il provider di servizi è responsabile dell'azione sulla richiesta di connessione. È possibile approvare la connessione, rifiutarla o eliminarla. Solo le connessioni approvate possono inviare traffico al servizio Private Link.

L'azione di approvazione delle connessioni può essere automatizzata utilizzando la proprietà di approvazione automatica nel servizio Private Link. L'approvazione automatica è una capacità per i fornitori di servizi di preapprovare un set di sottoscrizioni per l'accesso automatico al servizio. I clienti dovranno condividere le proprie sottoscrizioni offline per consentire ai provider di servizi di aggiungerli all'elenco di approvazione automatica. L'approvazione automatica è un sottoinsieme della matrice di visibilità. La visibilità controlla le impostazioni di esposizione, mentre l'approvazione automatica controlla le impostazioni di approvazione per il servizio. Se un cliente richiede una connessione da una sottoscrizione nell'elenco di approvazione automatica, la connessione viene approvata automaticamente e la connessione viene stabilita. I fornitori di servizi non devono più approvare manualmente la richiesta. D'altra parte, se un cliente richiede una connessione da una sottoscrizione nell'array di visibilità e non nell'array di approvazione automatica, la richiesta raggiungerà il provider di servizi, ma il provider di servizi deve approvare manualmente le connessioni.

## <a name="getting-connection-information-using-tcp-proxy-v2"></a>Recupero delle informazioni di connessione tramite il proxy TCP v2Getting connection Information using TCP Proxy v2

Quando si utilizza il servizio di collegamento privato, l'indirizzo IP di origine dei pacchetti provenienti dall'endpoint privato è l'indirizzo di rete convertito (NAT) sul lato provider di servizi che utilizza l'IP NAT allocato dalla rete virtuale del provider. Di conseguenza, le applicazioni ricevono l'indirizzo IP NAT allocato anziché l'indirizzo IP di origine effettivo dei consumer del servizio. Se l'applicazione necessita di un indirizzo IP di origine effettivo dal lato consumer, è possibile abilitare il protocollo Proxy nel servizio e recuperare le informazioni dall'intestazione del protocollo proxy. Oltre all'indirizzo IP di origine, l'intestazione del protocollo proxy contiene anche il LinkID dell'endpoint privato. Combinazione di indirizzo IP di origine e LinkID può aiutare i fornitori di servizi a identificare in modo univoco i loro consumer. Per ulteriori informazioni sul protocollo proxy, visitare [il sito Web all'indirizzo](https://www.haproxy.org/download/1.8/doc/proxy-protocol.txt). 

Queste informazioni vengono codificate utilizzando un vettore TLV (Type-Length-Value) personalizzato come segue:

Dettagli TLV personalizzati:

|Campo |Lunghezza (ottetti)  |Descrizione  |
|---------|---------|----------|
|Type  |1        |PP2_TYPE_AZURE (0xEE)|
|Length  |2      |Lunghezza del valore|
|valore  |1     |PP2_SUBTYPE_AZURE_PRIVATEENDPOINT_LINKID (0x01)|
|  |4        |UINT32 (4 byte) che rappresenta il LINKID dell'endpoint privato. Codificato in formato little endian.|

 > [!NOTE]
 > Il provider di servizi è responsabile dell'utilizzo del servizio sottostante il servizio di bilanciamento del carico standard configurato per analizzare l'intestazione del protocollo proxy in base alle [specifiche](https://www.haproxy.org/download/1.8/doc/proxy-protocol.txt) quando il protocollo proxy è abilitato nel servizio di collegamento privato. La richiesta avrà esito negativo se l'impostazione del protocollo proxy è abilitata nel servizio di collegamento privato ma il servizio del provider di servizi non è configurato per analizzare l'intestazione. Analogamente, la richiesta avrà esito negativo se il servizio del provider di servizi prevede un'intestazione di protocollo proxy mentre l'impostazione non è abilitata nel servizio di collegamento privato. Una volta abilitata l'impostazione del protocollo proxy, l'intestazione del protocollo proxy verrà inclusa anche nei probe di integrità HTTP/TCP dall'host alle macchine virtuali back-end, anche se non saranno presenti informazioni sul client nell'intestazione. 

## <a name="limitations"></a>Limitazioni

Di seguito sono riportate le limitazioni note quando si utilizza il servizio Private Link:
- Supportato solo in Load Balancer Standard 
- Supporta solo il traffico IPv4
- Supporta solo il traffico TCP

## <a name="next-steps"></a>Passaggi successivi
- [Creare un servizio di collegamento privato usando Azure PowerShellCreate a private link service using Azure PowerShell](create-private-link-service-powershell.md)
- [Creare un servizio di collegamento privato usando l'interfaccia della riga di comando di AzureCreate a private link service using Azure CLI](create-private-link-service-cli.md)
