---
title: Che cos'è il servizio di collegamento privato di Azure?
description: Informazioni sul servizio di collegamento privato di Azure.
services: private-link
author: asudbring
ms.service: private-link
ms.topic: conceptual
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: f9bdc180bf0dfd6f35fb18f5c76176a68d8f1644
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/08/2019
ms.locfileid: "74912988"
---
# <a name="what-is-azure-private-link-service"></a>Che cos'è il servizio di collegamento privato di Azure?

Il servizio di collegamento privato di Azure è il riferimento al servizio personalizzato che è basato sul collegamento privato di Azure. Il servizio in esecuzione dietro [Azure Load Balancer standard](../load-balancer/load-balancer-standard-overview.md) può essere abilitato per l'accesso ai collegamenti privati, in modo che i consumer del servizio possano accedervi privatamente dalla propria reti virtuali. I clienti possono creare un endpoint privato all'interno della VNet ed eseguirne il mapping a questo servizio. Questo articolo illustra i concetti correlati al lato del provider di servizi. 

## <a name="workflow"></a>Flusso di lavoro

![Flusso di lavoro del servizio di collegamento privato](media/private-link-service-overview/private-link-service-workflow.png)

### <a name="create-your-private-link-service"></a>Creare il servizio di collegamento privato

- Configurare l'applicazione per l'esecuzione dietro un servizio di bilanciamento del carico standard nella rete virtuale. Se l'applicazione è già stata configurata dietro un servizio di bilanciamento del carico standard, è possibile ignorare questo passaggio.   
- Creare un servizio di collegamento privato che fa riferimento al servizio di bilanciamento del carico sopra riportato. Nel processo di selezione del servizio di bilanciamento del carico scegliere la configurazione IP front-end in cui si vuole ricevere il traffico. Scegliere una subnet per gli indirizzi IP NAT per il servizio di collegamento privato. È consigliabile che nella subnet siano disponibili almeno otto indirizzi IP NAT. Tutto il traffico del consumer sembrerebbe provenire da questo pool di indirizzi IP privati al provider di servizi. Scegliere le proprietà/impostazioni appropriate per il servizio di collegamento privato.    

    > [!NOTE]
    > Il servizio di collegamento privato di Azure è supportato solo in Load Balancer Standard. 
    
### <a name="share-your-service"></a>Condividere il servizio

Dopo aver creato un servizio di collegamento privato, Azure genererà un moniker univoco globale denominato "alias" in base al nome fornito per il servizio. È possibile condividere l'alias o l'URI di risorsa del servizio con i clienti offline. I consumer possono avviare una connessione di collegamento privato utilizzando l'alias o l'URI di risorsa.
 
### <a name="manage-your-connection-requests"></a>Gestire le richieste di connessione

Quando un consumer avvia una connessione, il provider di servizi può accettare o rifiutare la richiesta di connessione. Tutte le richieste di connessione verranno elencate sotto la proprietà **privateendpointconnections** del servizio di collegamento privato.
 
### <a name="delete-your-service"></a>Eliminare il servizio

Se il servizio di collegamento privato non è più in uso, è possibile eliminarlo. Tuttavia, prima di eliminare il servizio, assicurarsi che non esistano connessioni a un endpoint privato associato. È possibile rifiutare tutte le connessioni ed eliminare il servizio.

## <a name="properties"></a>properties

Un servizio di collegamento privato specifica le proprietà seguenti: 

|Proprietà |Spiegazione  |
|---------|---------|
|Stato provisioning (provisioningState)  |Proprietà di sola lettura che elenca lo stato di provisioning corrente per il servizio di collegamento privato. Gli Stati di provisioning applicabili sono: "eliminazione; Fallito Completata Aggiornamento di ". Quando lo stato di provisioning è "succeeded", è stato effettuato il provisioning del servizio di collegamento privato.        |
|Alias (alias)     | Alias è una stringa di sola lettura univoca globale per il servizio. Consente di mascherare i dati del cliente per il servizio e allo stesso tempo di creare un nome di facile condivisione per il servizio. Quando si crea un servizio di collegamento privato, Azure genera l'alias per il servizio che è possibile condividere con i clienti. I clienti possono usare questo alias per richiedere una connessione al servizio.          |
|Visibilità (visibilità)     | Visibility è la proprietà che controlla le impostazioni di esposizione per il servizio di collegamento privato. I provider di servizi possono scegliere di limitare l'esposizione al servizio alle sottoscrizioni con autorizzazioni di controllo degli accessi in base al ruolo (RBAC), un set limitato di sottoscrizioni o tutte le sottoscrizioni di Azure.          |
|Approvazione automatica (autoapprovazione)    |   L'approvazione automatica controlla l'accesso automatico al servizio di collegamento privato. Le sottoscrizioni specificate nell'elenco di approvazione automatica vengono approvate automaticamente quando viene richiesta una connessione da endpoint privati in tali sottoscrizioni.          |
|Configurazione IP Front-End Load Balancer (loadBalancerFrontendIpConfigurations)    |    Il servizio di collegamento privato è associato all'indirizzo IP front-end di un Load Balancer Standard. Tutto il traffico destinato al servizio raggiungerà il front-end del SLB. È possibile configurare le regole di SLB per indirizzare il traffico ai pool back-end appropriati in cui le applicazioni sono in esecuzione. Le configurazioni IP front-end del servizio di bilanciamento del carico sono diverse dalle configurazioni IP NAT.      |
|Configurazione IP NAT (ipConfigurations)    |    Questa proprietà fa riferimento alla configurazione IP NAT (Network Address Translation) per il servizio di collegamento privato. È possibile scegliere l'indirizzo IP NAT da qualsiasi subnet nella rete virtuale di un provider di servizi. Il servizio di collegamento privato esegue la NAT sul lato di destinazione sul traffico dei collegamenti privati. In questo modo si garantisce che non esistano conflitti IP tra l'origine (lato utente) e lo spazio degli indirizzi di destinazione (provider di servizi). Sul lato di destinazione (lato provider di servizi), l'indirizzo IP NAT viene visualizzato come IP di origine per tutti i pacchetti ricevuti dal servizio e dall'IP di destinazione per tutti i pacchetti inviati dal servizio.       |
|Connessioni a endpoint privati (privateEndpointConnections)     |  Questa proprietà elenca gli endpoint privati che si connettono al servizio di collegamento privato. Più endpoint privati possono connettersi allo stesso servizio di collegamento privato e il provider di servizi può controllare lo stato dei singoli endpoint privati.        |
|||


### <a name="details"></a>Dettagli

- È possibile accedere al servizio di collegamento privato dagli endpoint privati approvati nella stessa area. L'endpoint privato può essere raggiunto dalla stessa rete virtuale, reti virtuali con peering a livello di area, reti virtuali con peering globale e in locale usando connessioni VPN private o ExpressRoute. 
 
- Quando si crea un servizio di collegamento privato, viene creata un'interfaccia di rete per il ciclo di vita della risorsa. Questa interfaccia non può essere gestita dal cliente.
 
- Il servizio di collegamento privato deve essere distribuito nella stessa area della rete virtuale e del Load Balancer Standard.  
 
- È possibile accedere a un singolo servizio di collegamento privato da più endpoint privati appartenenti a reti virtuali, sottoscrizioni e/o Active Directory tenant diversi. La connessione viene stabilita tramite un flusso di lavoro di connessione. 
 
- È possibile creare più servizi di collegamento privato nella stessa Load Balancer Standard usando diverse configurazioni IP front-end. Sono previsti limiti al numero di servizi di collegamento privato che è possibile creare per ogni Load Balancer Standard e per sottoscrizione. Per informazioni dettagliate, vedere [limiti di Azure](https://docs.microsoft.com/azure/azure-subscription-service-limits#networking-limits).
 
- Il servizio di collegamento privato può avere più di una configurazione IP NAT collegata. La scelta di più configurazioni IP NAT può aiutare i provider di servizi a eseguire la scalabilità. Attualmente, i provider di servizi possono assegnare fino a otto indirizzi IP NAT per ogni servizio di collegamento privato. Con ogni indirizzo IP NAT è possibile assegnare più porte per le connessioni TCP e quindi aumentare il numero di porte. Dopo aver aggiunto più indirizzi IP NAT a un servizio di collegamento privato, non è possibile eliminare gli indirizzi IP NAT. Questa operazione viene eseguita per garantire che le connessioni attive non siano interessate durante l'eliminazione degli indirizzi IP NAT.


## <a name="alias"></a>Alias

**Alias** è un nome univoco globale per il servizio. Consente di mascherare i dati del cliente per il servizio e allo stesso tempo di creare un nome di facile condivisione per il servizio. Quando si crea un servizio di collegamento privato, Azure genera un alias per il servizio che è possibile condividere con i clienti. I clienti possono usare questo alias per richiedere una connessione al servizio.

L'alias è costituito da tre parti: *Prefix*. *GUID*. *Suffisso*

- Il prefisso è il nome del servizio. È possibile scegliere il proprio prefisso. Dopo aver creato "alias", non è possibile modificarlo, quindi selezionare il prefisso in modo appropriato.  
- Il GUID verrà fornito dalla piattaforma. Questo consente di rendere il nome univoco a livello globale. 
- Il suffisso viene accodato da Azure: *Region*. Azure. privatelinkservice 

Alias completo: *prefisso*. {GUID}. *Region*. Azure. privatelinkservice  

## <a name="control-service-exposure"></a>Controllare l'esposizione del servizio

Il servizio di collegamento privato offre le opzioni per controllare l'esposizione del servizio tramite l'impostazione "visibilità". È possibile rendere il servizio privato per l'uso da reti virtuali diversi di cui si è proprietari (solo autorizzazioni RBAC), limitare l'esposizione a un set limitato di sottoscrizioni attendibili o renderlo pubblico in modo che tutte le sottoscrizioni di Azure possano richiedere connessioni sul collegamento privato servizio. Le impostazioni di visibilità decidono se un consumer può connettersi al servizio o meno. 

## <a name="control-service-access"></a>Controllare l'accesso al servizio

I consumer con esposizione (controllata dall'impostazione di visibilità) al servizio di collegamento privato possono creare un endpoint privato nella reti virtuali e richiedere una connessione al servizio di collegamento privato. La connessione all'endpoint privato verrà creata in uno stato "in sospeso" nell'oggetto servizio di collegamento privato. Il provider di servizi è responsabile dell'azione della richiesta di connessione. È possibile approvare la connessione, rifiutare la connessione o eliminare la connessione. Solo le connessioni approvate possono inviare il traffico al servizio di collegamento privato.

L'azione di approvazione delle connessioni può essere automatizzata tramite la proprietà di approvazione automatica nel servizio di collegamento privato. L'approvazione automatica è una possibilità per i provider di servizi di preapprovare un set di sottoscrizioni per l'accesso automatizzato al servizio. I clienti dovranno condividere le sottoscrizioni offline per i provider di servizi da aggiungere all'elenco di approvazione automatica. L'approvazione automatica è un subset della matrice di visibilità. Visibility controlla le impostazioni di esposizione mentre l'approvazione automatica controlla le impostazioni di approvazione per il servizio. Se un cliente richiede una connessione da una sottoscrizione nell'elenco di approvazione automatica, la connessione viene approvata automaticamente e viene stabilita la connessione. I provider di servizi non devono più approvare manualmente la richiesta. D'altra parte, se un cliente richiede una connessione da una sottoscrizione nell'array di visibilità e non nell'array di approvazione automatica, la richiesta raggiungerà il provider di servizi, ma il provider di servizi deve approvare manualmente le connessioni.

## <a name="limitations"></a>Limitazioni

Di seguito sono riportate le limitazioni note relative all'utilizzo del servizio di collegamento privato:
- Supportato solo in Load Balancer Standard 
- Supporta solo traffico IPv4
- Supporta solo il traffico TCP
- L'esperienza di creazione e gestione da portale di Azure non è supportata
- Le informazioni di connessione dei client che usano il protocollo proxy non sono disponibili per il provider di servizi

## <a name="next-steps"></a>Passaggi successivi
- [Creare un servizio di collegamento privato utilizzando Azure PowerShell](create-private-link-service-powershell.md)
- [Creare un servizio di collegamento privato usando l'interfaccia della riga di comando](create-private-link-service-cli.md)
