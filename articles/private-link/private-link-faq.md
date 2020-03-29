---
title: Domande frequenti su Azure Private Link
description: Informazioni su Azure Private Link.Learn about Azure Private Link.
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: conceptual
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: 7870b68ca931123d50e88e846aa066ce53972dbc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75349933"
---
# <a name="azure-private-link-frequently-asked-questions-faq"></a>Domande frequenti su Azure Private Link

## <a name="private-link"></a>Collegamento privato

### <a name="what-is-azure-private-endpoint-and-azure-private-link-service"></a>Che cos'è l'endpoint privato di Azure e il servizio di collegamento privato di Azure?

- **[Endpoint privato di Azure:](private-endpoint-overview.md)** l'endpoint privato di Azure è un'interfaccia di rete che si connette privatamente e in modo sicuro a un servizio basato su Azure Private Link.Azure Private Endpoint : Azure Private Endpoint is a network interface that connects you privately and securely to a service powered by Azure Private Link. È possibile usare gli endpoint privati per connettersi a un servizio di Azure PaaS che supporta Private Link o al proprio servizio di collegamento privato.
- **[Servizio Di Azure Private Link:](private-link-service-overview.md)** il servizio Azure Private Link è un servizio creato da un provider di servizi. Attualmente, un servizio Private Link può essere collegato alla configurazione IP front-end di un servizio di bilanciamento del carico Standard.Currently, a Private Link service can be attached to the frontend IP configuration of a Standard Load Balancer. 

### <a name="how-is-traffic-being-sent-when-using-private-link"></a>Come viene inviato il traffico quando si utilizza Private Link?
Il traffico viene inviato privatamente utilizzando la backbone Microsoft. Non attraversa internet.  
 
### <a name="what-is-the-difference-between-a-service-endpoints-and-a-private-endpoints"></a>Qual è la differenza tra un endpoint di servizio e un endpoint privati?
- Quando si usano endpoint privati, l'accesso alla rete viene concesso a risorse specifiche dietro un determinato servizio che fornisce una segmentazione granulare, inoltre il traffico può raggiungere la risorsa del servizio in locale senza usare endpoint pubblici.
- Un endpoint del servizio rimane un indirizzo IP instradabile pubblicamente.  Un endpoint privato è un indirizzo IP privato nello spazio degli indirizzi della rete virtuale in cui è configurato l'endpoint privato.

### <a name="what-is-the-relationship-between-private-link-service-and-private-endpoint"></a>Qual è la relazione tra il servizio Private Link e l'endpoint privato?
L'endpoint privato fornisce l'accesso a più tipi di risorse di collegamento privato, inclusi i servizi di Azure PaaS e il proprio servizio di collegamento privato. È una relazione uno-a-molti. Un servizio Private Link può ricevere connessioni da più endpoint privati. D'altra parte, un endpoint privato può connettersi solo a un servizio Private Link.    

## <a name="private-endpoint"></a>Endpoint privato 
 
### <a name="can-i-create-multiple-private-endpoints-in-same-vnet-can-they-connect-to-different-services"></a>È possibile creare più endpoint privati nella stessa rete virtuale? Possono connettersi a diversi Servizi? 
Sì. È possibile avere più endpoint privati nella stessa rete virtuale o subnet. Possono connettersi a diversi servizi.  
 
### <a name="do-i-require-a-dedicated-subnet-for-private-endpoints"></a>È necessaria una subnet dedicata per gli endpoint privati? 
No. Non è necessaria una subnet dedicata per gli endpoint privati. È possibile scegliere un IP dell'endpoint privato da qualsiasi subnet della rete virtuale in cui viene distribuito il servizio.  
 
### <a name="can-private-endpoint-connect-to-private-link-service-across-azure-active-directory-tenants"></a>L'endpoint privato può connettersi al servizio Private Link tra i tenant di Azure Active Directory? 
Sì. Gli endpoint privati possono connettersi ai servizi di collegamento privato o a Azure PaaS tra tenant di Active Directory.Private endpoints can connect to Private Link services or Azure PaaS across AD tenants.  
 
### <a name="can-private-endpoint-connect-to-azure-paas-resources-across-azure-regions"></a>L'endpoint privato può connettersi alle risorse di Azure PaaS tra aree di Azure?
Sì. Gli endpoint privati possono connettersi alle risorse di Azure PaaS tra aree di Azure.Private endpoints can connect to Azure PaaS resources across Azure regions.

## <a name="private-link-service"></a>Servizio Collegamento privato
 
### <a name="what-are-the-pre-requisites-for-creating-a-private-link-service"></a>Quali sono i prerequisiti per la creazione di un servizio Private Link? 
I back-end del servizio devono trovarsi in una rete virtuale e dietro un servizio di bilanciamento del carico Standard.Your service backends should be in a Virtual network and behind a Standard Load Balancer.
 
### <a name="how-can-i-scale-my-private-link-service"></a>Come posso scalare il mio servizio Private Link? 
È possibile ridimensionare il servizio Private Link in diversi modi: 
- Aggiungere macchine virtuali back-end al pool dietro il servizio di bilanciamento del carico standardAdd Backend VMs to the pool behind your Standard Load Balancer 
- Aggiungere un indirizzo IP al servizio Private Link. Consentiamo fino a 8 IP per servizio Private Link.  
- Aggiungere un nuovo servizio Private Link a Load Balancer Standard.Add new Private Link service to Standard Load Balancer. Sono consentiti fino a otto servizi Private Link per ogni servizio di bilanciamento del carico.   

### <a name="can-i-connect-my-service-to-multiple-private-endpoints"></a>È possibile connettere il servizio a più endpoint privati?
Sì. Un servizio Private Link può ricevere connessioni da più endpoint privati. Tuttavia, un endpoint privato può connettersi a un solo servizio Private Link.However one Private Endpoint can only connect to one Private Link service.  
 
### <a name="how-should-i-control-the-exposure-of-my-private-link-service"></a>Come devo controllare l'esposizione del mio servizio Private Link?
È possibile controllare l'esposizione utilizzando la configurazione di visibilità sul servizio Private Link. La visibilità supporta tre impostazioni:

- **Nessuno:** solo le sottoscrizioni con accesso RBAC possono individuare il servizio. 
- **Restrittivo:** solo le sottoscrizioni incluse nella whitelist e con accesso RBAC possono individuare il servizio. 
- **Tutti** : tutti possono individuare il servizio. 
 
### <a name="can-i-create-a-private-link-service-with-basic-load-balancer"></a>È possibile creare un servizio Private Link con Basic Load Balancer? 
No. Il servizio Private Link su un servizio di bilanciamento del carico di base non è supportato.
 
### <a name="is-a-dedicated-subnet-required-for-private-link-service"></a>È necessaria una subnet dedicata per il servizio Private Link? 
No. Il servizio Private Link non richiede una subnet dedicata. È possibile scegliere qualsiasi subnet nella rete virtuale in cui viene distribuito il servizio.   

### <a name="i-am-a-service-provider-using-azure-private-link-do-i-need-to-make-sure-all-my-customers-have-unique-ip-space-and-dont-overlap-with-my-ip-space"></a>Sono un provider di servizi che usa Azure Private Link.I are a service provider using Azure Private Link. Devo assicurarmi che tutti i miei clienti dispongano di uno spazio IP unico e non si sovrappongano al mio spazio IP? 
No. Azure Private Link offre questa funzionalità per te. Pertanto, non è necessario disporre di spazio di indirizzi non sovrapposto con lo spazio di indirizzi del cliente. 

##  <a name="next-steps"></a>Passaggi successivi

- Informazioni su [Azure Private Link](private-link-overview.md)
