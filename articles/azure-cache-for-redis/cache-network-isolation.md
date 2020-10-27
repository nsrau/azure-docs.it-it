---
title: Cache di Azure per le opzioni di isolamento rete Redis
description: In questo articolo si apprenderà come determinare la migliore soluzione di isolamento rete in base alle proprie esigenze. Verranno illustrate le nozioni di base relative al collegamento privato di Azure, all'inserimento di rete virtuale di Azure (VNet) e alle regole del firewall di Azure con i relativi vantaggi e limitazioni.
author: curib
ms.author: cauribeg
ms.service: cache
ms.topic: conceptual
ms.date: 10/15/2020
ms.openlocfilehash: 0fda0b659dd2500e811fac1f53c99a9987276185
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/26/2020
ms.locfileid: "92537474"
---
# <a name="azure-cache-for-redis-network-isolation-options"></a>Cache di Azure per le opzioni di isolamento rete Redis 
In questo articolo si apprenderà come determinare la migliore soluzione di isolamento rete in base alle proprie esigenze. Verranno illustrate le nozioni di base relative al collegamento privato di Azure, all'inserimento di rete virtuale di Azure (VNet) e alle regole del firewall di Azure con i relativi vantaggi e limitazioni.  

## <a name="azure-private-link-public-preview"></a>Collegamento privato di Azure (anteprima pubblica) 
Il collegamento privato di Azure fornisce la connettività privata da una rete virtuale ai servizi di Azure PaaS. Semplifica l'architettura di rete e protegge la connessione tra gli endpoint in Azure eliminando l'esposizione dei dati alla rete Internet pubblica. 

### <a name="advantages"></a>Vantaggi
* Supportato in cache di Azure Basic, standard e Premium per le istanze di Redis. 
* Con il [collegamento privato di Azure](../private-link/private-link-overview.md)è possibile connettersi a un'istanza di cache di Azure dalla rete virtuale tramite un endpoint privato, a cui viene assegnato un indirizzo IP privato in una subnet all'interno della rete virtuale. Con questo, le istanze della cache sono disponibili sia in VNet che in pubblico.  
* Una volta creato un endpoint privato, è possibile limitare l'accesso alla rete pubblica tramite il `publicNetworkAccess` flag. Questo flag è impostato su per `Enabled` impostazione predefinita, in modo da consentire l'accesso sia al collegamento pubblico che al collegamento privato alla cache. Se impostato su `Disabled` , consentirà solo l'accesso ai collegamenti privati. È possibile impostare il valore su `Disabled` con una richiesta patch. Per altre informazioni, vedere [la pagina relativa alla cache di Azure per Redis con collegamento privato di Azure (anteprima)](cache-private-link.md). 
* Tutte le dipendenze della cache esterna non influiranno sulle regole NSG di VNet.

### <a name="limitations"></a>Limitazioni 
* I gruppi di sicurezza di rete (NSG) sono disabilitati per gli endpoint privati. Tuttavia, se sono presenti altre risorse nella subnet, l'imposizione NSG verrà applicata a tali risorse.
* La replica geografica, le regole del firewall, il supporto della console del portale, più endpoint per cache in cluster, la persistenza per il firewall e le cache VNet inserite non sono ancora supportati. 
* Per connettersi a una cache in cluster, `publicNetworkAccess` è necessario impostare su `Disabled` e può essere presente una sola connessione all'endpoint privato.

> [!NOTE]
> Quando si aggiunge un endpoint privato a un'istanza della cache, tutto il traffico Redis verrà spostato nell'endpoint privato a causa del DNS.
> Verificare che le regole del firewall precedenti siano regolate prima.  
>
>

## <a name="azure-virtual-network-injection"></a>Inserimento nella rete virtuale di Azure 
VNet è il blocco predefinito fondamentale per la rete privata in Azure. VNet consente a molte risorse di Azure di comunicare in modo sicuro tra loro, con Internet e con le reti locali. VNet è come una rete tradizionale che può essere operata nella propria data center, ma con i vantaggi dell'infrastruttura di Azure, della scalabilità, della disponibilità e dell'isolamento. 

### <a name="advantages"></a>Vantaggi
* Quando una cache di Azure per l'istanza di redis è configurata con un VNet, non è indirizzabile pubblicamente ed è accessibile solo da macchine virtuali e applicazioni all'interno della VNet.  
* Quando VNet è combinato con criteri NSG limitati, contribuisce a ridurre il rischio di exfiltration di dati. 
* La distribuzione di VNet offre sicurezza e isolamento avanzati per la cache di Azure per Redis, nonché subnet, criteri di controllo degli accessi e altre funzionalità per limitare ulteriormente l'accesso. 
* La replica geografica è supportata. 

### <a name="limitations"></a>Limitazioni
* Le cache VNet inserite sono disponibili solo per le cache Premium di Azure per Redis. 
* Quando si usa una cache VNet inserita, è necessario aprire la VNet per memorizzare nella cache le dipendenze, ad esempio CRL/PKI, AKV, archiviazione di Azure, monitoraggio di Azure e altro ancora.  


## <a name="azure-firewall-rules"></a>Regole del firewall di Azure
Il [firewall di Azure](../firewall/overview.md) è un servizio di sicurezza di rete gestito e basato sul cloud che protegge le risorse VNet di Azure. Si tratta di un firewall completamente con stato come servizio con disponibilità elevata incorporata e scalabilità illimitata del cloud. È possibile creare, applicare e registrare criteri di connettività di applicazione e di rete in modo centralizzato tra le sottoscrizioni e le reti virtuali.  

### <a name="advantages"></a>Vantaggi
* Quando le regole del firewall sono configurate, solo le connessioni client degli intervalli di indirizzi IP specificati possono connettersi alla cache. Le connessioni dai sistemi di monitoraggio di Cache Redis di Azure sono sempre consentite, anche se le regole del firewall sono configurate. Sono consentite anche le regole NSG definite.  

### <a name="limitations"></a>Limitazioni
* Le regole del firewall possono essere usate in combinazione con le cache inserite in VNet, ma non con gli endpoint privati attualmente disponibili. 


## <a name="next-steps"></a>Passaggi successivi
* Informazioni su come configurare una [cache VNet inserita per una cache di Azure Premium per l'istanza di redis](cache-how-to-premium-vnet.md).  
* Informazioni su come configurare [le regole del firewall per tutti i livelli di cache di Azure per Redis](cache-configure.md#firewall). 
* Informazioni su come [configurare gli endpoint privati per tutti i livelli di cache di Azure per Redis](cache-private-link.md).