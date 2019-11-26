---
title: Domande frequenti sul collegamento privato di Azure
description: Informazioni sul collegamento privato di Azure.
services: private-link
author: asudbring
ms.service: private-link
ms.topic: conceptual
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: 53cb9b91d62c65cefb33451c716e677599306e9c
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74229324"
---
# <a name="azure-private-link-frequently-asked-questions-faq"></a>Domande frequenti sul collegamento privato di Azure

## <a name="private-link"></a>Collegamento privato

### <a name="what-is-azure-private-endpoint-and-azure-private-link-service"></a>Che cos'è l'endpoint privato di Azure e il servizio di collegamento privato di Azure?

- **[Endpoint privato](private-endpoint-overview.md)** di Azure: l'endpoint privato di Azure è un'interfaccia di rete che si connette privatamente e in modo sicuro a un servizio basato su collegamento privato di Azure. È possibile usare endpoint privati per connettersi a un servizio Azure PaaS che supporta il collegamento privato o al servizio di collegamento privato.
- **[Servizio di collegamento privato di Azure](private-link-service-overview.md)** : il servizio di collegamento privato di Azure è un servizio creato da un provider di servizi. Attualmente, un servizio di collegamento privato può essere collegato alla configurazione IP front-end di un Load Balancer Standard. 

### <a name="how-is-traffic-being-sent-when-using-private-link"></a>Come viene inviato il traffico quando si usa un collegamento privato?
Il traffico viene inviato privatamente usando la backbone Microsoft. Non attraversa Internet.  
 
### <a name="what-is-the-difference-between-a-service-endpoints-and-a-private-endpoints"></a>Qual è la differenza tra gli endpoint di servizio e gli endpoint privati?
- Quando si usano endpoint privati, l'accesso alla rete viene concesso a risorse specifiche dietro a un determinato servizio che fornisce segmentazione granulare, anche il traffico può raggiungere la risorsa del servizio da locale senza usare endpoint pubblici.

### <a name="what-is-the-relationship-between-private-link-service-and-private-endpoint"></a>Qual è la relazione tra il servizio di collegamento privato e l'endpoint privato?
L'endpoint privato consente di accedere a più tipi di risorse di collegamento privato, inclusi i servizi PaaS di Azure e il servizio di collegamento privato. Si tratta di una relazione uno-a-molti. Un servizio di collegamento privato può ricevere connessioni da più endpoint privati. D'altra parte, un endpoint privato può connettersi a un solo servizio di collegamento privato.    

## <a name="private-endpoint"></a>Endpoint privato 
 
### <a name="can-i-create-multiple-private-endpoints-in-same-vnet-can-they-connect-to-different-services"></a>È possibile creare più endpoint privati nella stessa VNet? È possibile connettersi a servizi diversi? 
Sì. È possibile avere più endpoint privati nella stessa VNet o nella stessa subnet. Possono connettersi a servizi diversi.  
 
### <a name="do-i-require-a-dedicated-subnet-for-private-endpoints"></a>È necessaria una subnet dedicata per gli endpoint privati? 
No. Non è necessaria una subnet dedicata per gli endpoint privati. È possibile scegliere un indirizzo IP di endpoint privato da qualsiasi subnet dal VNet in cui viene distribuito il servizio.  
 
### <a name="can-private-endpoint-connect-to-private-link-service-across-azure-active-directory-tenants"></a>L'endpoint privato può connettersi al servizio di collegamento privato tra Azure Active Directory tenant? 
Sì. Gli endpoint privati possono connettersi a servizi di collegamento privato o Azure PaaS tra tenant di AD.  
 
### <a name="can-private-endpoint-connect-to-azure-paas-resources-across-azure-regions"></a>L'endpoint privato può connettersi alle risorse PaaS di Azure tra aree di Azure?
Sì. Gli endpoint privati possono connettersi alle risorse PaaS di Azure tra aree di Azure.

## <a name="private-link-service"></a>Servizio Collegamento privato
 
### <a name="what-are-the-pre-requisites-for-creating-a-private-link-service"></a>Quali sono i prerequisiti per la creazione di un servizio di collegamento privato? 
I backend del servizio devono trovarsi in una rete virtuale e dietro un Load Balancer Standard.
 
### <a name="how-can-i-scale-my-private-link-service"></a>Come è possibile ridimensionare il servizio di collegamento privato? 
È possibile ridimensionare il servizio di collegamento privato in diversi modi: 
- Aggiungere macchine virtuali back-end al pool dietro la Load Balancer Standard 
- Aggiungere un indirizzo IP al servizio di collegamento privato. Sono consentiti fino a 8 indirizzi IP per servizio di collegamento privato.  
- Aggiungere un nuovo servizio di collegamento privato per Load Balancer Standard. Sono consentiti fino a otto servizi di collegamento privato per ogni servizio di bilanciamento del carico.   

### <a name="can-i-connect-my-service-to-multiple-private-endpoints"></a>È possibile connettere il servizio a più endpoint privati?
Sì. Un servizio di collegamento privato può ricevere connessioni da più endpoint privati. Un endpoint privato può tuttavia connettersi a un solo servizio di collegamento privato.  
 
### <a name="how-should-i-control-the-exposure-of-my-private-link-service"></a>Come è possibile controllare l'esposizione del servizio di collegamento privato?
È possibile controllare l'esposizione usando la configurazione di visibilità nel servizio di collegamento privato. Visibility supporta tre impostazioni:

- Le sottoscrizioni solo **None** con accesso RBAC possono individuare il servizio. 
- Le sottoscrizioni solo **restrittive** e con accesso RBAC possono individuare il servizio. 
- **Tutti** : tutti gli utenti possono individuare il servizio. 
 
### <a name="can-i-create-a-private-link-service-with-basic-load-balancer"></a>È possibile creare un servizio di collegamento privato con Load Balancer di base? 
No. Il servizio di collegamento privato su un Load Balancer di base non è supportato.
 
### <a name="is-a-dedicated-subnet-required-for-private-link-service"></a>È necessaria una subnet dedicata per il servizio di collegamento privato? 
No. Il servizio di collegamento privato non richiede una subnet dedicata. È possibile scegliere qualsiasi subnet nella VNet in cui viene distribuito il servizio.   

### <a name="i-am-a-service-provider-using-azure-private-link-do-i-need-to-make-sure-all-my-customers-have-unique-ip-space-and-dont-overlap-with-my-ip-space"></a>Sono un provider di servizi che usa il collegamento privato di Azure. È necessario assicurarsi che tutti i miei clienti abbiano uno spazio IP univoco e non si sovrappongano allo spazio IP? 
No. Il collegamento privato di Azure offre questa funzionalità. Di conseguenza, non è necessario disporre di uno spazio di indirizzi non sovrapposto con lo spazio di indirizzi del cliente. 

##  <a name="next-steps"></a>Passaggi successivi

- Informazioni sul [collegamento privato di Azure](private-link-overview.md)
