---
title: Criteri degli endpoint servizio di rete virtuale di Azure | Microsoft Docs
description: Informazioni su come filtrare il traffico di rete virtuale per le risorse dei servizi di Azure usando criteri degli endpoint di servizio
services: virtual-network
documentationcenter: na
author: RDhillon
ms.service: virtual-network
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/21/2020
ms.author: rdhillon
ms.openlocfilehash: 926da07ffaf0c61ca2a7fd02351ef3635ec4d73b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77651296"
---
# <a name="virtual-network-service-endpoint-policies-for-azure-storage"></a>Criteri degli endpoint del servizio di rete virtuale per Archiviazione di AzureVirtual network service endpoint policies for Azure Storage

I criteri degli endpoint del servizio rete virtuale (VNet) consentono di filtrare il traffico di rete virtuale in uscita agli account di Archiviazione di Azure tramite l'endpoint del servizio e consentire l'esfiltrazione dei dati solo in account di Archiviazione di Azure specifici. I criteri endpoint forniscono un controllo di accesso granulare per il traffico di rete virtuale verso Archiviazione di Azure durante la connessione tramite l'endpoint del servizio.

![Protezione del traffico in uscita della rete virtuale agli account di archiviazione di AzureSecuring Virtual network outbound traffic to Azure Storage accounts](./media/virtual-network-service-endpoint-policies-overview/vnet-service-endpoint-policies-overview.png)

Questa funzionalità è in genere disponibile per __Archiviazione di Azure__ in tutte le aree globali di __Azure.__

## <a name="key-benefits"></a>Vantaggi principali

I criteri degli endpoint servizio di rete virtuale offrono i vantaggi seguenti:

- __Sicurezza migliorata per il traffico della rete virtuale verso Archiviazione di AzureImproved security for your Virtual Network traffic to Azure Storage__

  [I tag del servizio di Azure per](https://aka.ms/servicetags) i gruppi di sicurezza di rete consentono di limitare il traffico in uscita della rete virtuale a aree di Archiviazione di Azure specifiche. Tuttavia, ciò consente il traffico a qualsiasi account all'interno dell'area di archiviazione di Azure selezionata.
  
  I criteri endpoint consentono di specificare gli account di Archiviazione di Azure a cui è consentito l'accesso in uscita alla rete virtuale e limita l'accesso a tutti gli altri account di archiviazione. In questo modo il controllo di sicurezza molto più granulare per proteggere l'esfiltrazione dei dati dalla rete virtuale.

- __Criteri scalabili a disponibilità elevata per filtrare il traffico dei servizi di Azure__

   I criteri degli endpoint forniscono una soluzione a disponibilità elevata e con scalabilità orizzontale per filtrare il traffico dei servizi di Azure dalle reti virtuali sugli endpoint di servizio. Non è richiesto alcun sovraccarico aggiuntivo per la gestione di appliance di rete centrali per il traffico nelle reti virtuali.

## <a name="json-object-for-service-endpoint-policies"></a>Oggetto JSON per i criteri degli endpoint del servizio
Diamo una rapida occhiata all'oggetto Criteri endpoint del servizio.

```json
"serviceEndpointPolicyDefinitions": [
    {
            "description": null,
            "name": "MySEP-Definition",
            "resourceGroup": "MySEPDeployment",
            "service": "Microsoft.Storage",
            "serviceResources": [ 
                    "/subscriptions/subscriptionID/resourceGroups/MySEPDeployment/providers/Microsoft.Storage/storageAccounts/mystgacc"
            ],
            "type": "Microsoft.Network/serviceEndpointPolicies/serviceEndpointPolicyDefinitions"
    }
]
```

## <a name="configuration"></a>Configurazione

-   È possibile configurare i criteri endpoint per limitare il traffico di rete virtuale ad account di Archiviazione di Azure specifici.
-   I criteri degli endpoint sono configurati in una subnet di una rete virtuale. Gli endpoint di servizio per Archiviazione di Azure devono essere abilitati nella subnet per applicare i criteri.
-   I criteri endpoint consentono di aggiungere account di Archiviazione di Azure specifici per consentire l'elenco, usando il formato resourceID.Endpoint policy allows you to add specific Azure Storage accounts to allow list, using the resourceID format. È possibile limitare l'accesso
    - tutti gli account di archiviazione in una sottoscrizioneall storage accounts in a subscription<br>
      `E.g. /subscriptions/subscriptionId`

    - tutti gli account di archiviazione in un gruppo di risorse<br>
      `E.g. subscriptions/subscriptionId/resourceGroups/resourceGroupName`
     
    - un singolo account di archiviazione elencando l'ID risorsa di Azure Resource Manager corrispondente. Ciò riguarda il traffico verso BLOB, tabelle, code, file e Azure Data Lake Storage Gen2. <br>
    `E.g. /subscriptions/subscriptionId/resourceGroups/resourceGroupName/providers/Microsoft.Storage/storageAccounts/storageAccountName`
-   Per impostazione predefinita, se nessun criterio è collegato a una subnet con endpoint, è possibile accedere a tutti gli account di archiviazione nel servizio. Dopo la configurazione dei criteri in tale subnet, dalle istanze di calcolo in tale subnet è possibile accedere solo alle risorse specificate nei criteri. L'accesso a tutti gli altri account di archiviazione verrà negato.
-   Quando si applicano i criteri dell'endpoint del servizio in una subnet, *l'ambito dell'endpoint* del servizio di archiviazione di Azure viene aggiornato da regionale a **globale.** Ciò significa che tutto il traffico verso Archiviazione di Azure è protetto tramite l'endpoint del servizio in seguito. Anche i criteri degli endpoint del servizio sono applicabili a livello globale, pertanto agli account di archiviazione non consentiti in modo esplicito verrà negato l'accesso. 
-   È possibile applicare più criteri a una subnet. Quando alla subnet sono associati più criteri, sarà consentito il traffico di rete virtuale verso le risorse specificate in uno di questi criteri. L'accesso a tutte le altre risorse dei servizi, non specificate in alcuno dei criteri, viene negato.

    > [!NOTE]  
    > I criteri degli endpoint del servizio sono **criteri consentiti,** pertanto, oltre alle risorse specificate, tutte le altre risorse sono limitate. Assicurarsi che tutte le dipendenze delle risorse del servizio per le applicazioni siano identificate ed elencate nei criteri.

- Nei criteri degli endpoint è possibile specificare solo gli account di archiviazione che usano il modello Azure Resource Manager. Gli account di archiviazione di Azure classici non supporteranno i criteri degli endpoint del servizio di Azure.Your classic Azure Storage accounts will not support Azure Service Endpoint Policies.
- L'accesso secondario RA-GRS (con ridondanza geografica e accesso in lettura) viene consentito automaticamente se è specificato l'account principale.
- Gli account di archiviazione possono trovarsi in uno stesso o in un diverso tenant di Azure Active Directory o sottoscrizione rispetto alla rete virtuale.

## <a name="scenarios"></a>Scenari

- **Reti virtuali con peering, connesse o multiple**: per filtrare il traffico nelle reti virtuali con peering, i criteri degli endpoint devono essere applicati individualmente a tali reti virtuali.
- **Filtraggio del traffico Internet con Network Appliances o Firewall di Azure:** filtra il traffico del servizio di Azure con criteri, supera gli endpoint del servizio e filtra il resto del traffico Internet o di Azure tramite appliance o Firewall di Azure.Filtering Internet traffic with Network Appliances or Azure Firewall : Filter Azure service traffic with policies, over service endpoints, and filter rest of the Internet or Azure traffic via appliances or Azure Firewall.
- **Filtraggio del traffico nei servizi**di Azure distribuiti nelle reti virtuali: al momento i criteri degli endpoint del servizio di Azure non sono supportati per i servizi di Azure gestiti distribuiti nella rete virtuale. 
- **Filtro del traffico dall'ambiente locale ai servizi di Azure**: i criteri degli endpoint di servizio si applicano solo al traffico dalle subnet associate ai criteri. Per consentire l'accesso a risorse dei servizi di Azure specifiche dall'ambiente locale, il traffico deve essere filtrato tramite firewall o appliance virtuali di rete.

## <a name="logging-and-troubleshooting"></a>Registrazione e risoluzione dei problemi
Non sono disponibili funzionalità di registrazione centralizzata per i criteri degli endpoint di servizio. Per i log di diagnostica del servizio, vedere [Registrazione degli endpoint di servizio](virtual-network-service-endpoints-overview.md#logging-and-troubleshooting).

### <a name="troubleshooting-scenarios"></a>Scenari di risoluzione dei problemi
- Accesso negato agli account di archiviazione che lavoravano in anteprima (non nell'area con associazione geografica)
  - Con l'aggiornamento di Archiviazione di Azure per usare i tag del servizio globale, l'ambito dell'endpoint del servizio e quindi i criteri dell'endpoint del servizio sono ora globali. Pertanto, qualsiasi traffico verso Archiviazione di Azure viene crittografato negli endpoint del servizio e solo gli account di archiviazione elencati in modo esplicito nei criteri sono consentiti per l'accesso.
  - Consenti esplicitamente l'elenco di tutti gli account di archiviazione necessari per ripristinare l'accesso.  
  - Contattare il supporto tecnico di Azure.
- Accesso negato per gli account specificati nei criteri degli endpoint
  - L'accesso potrebbe essere bloccato da gruppi di sicurezza di rete o filtro del firewall
  - Se la rimozione e la nuova applicazione dei criteri comporta la perdita di connettività:
    - Verificare se il servizio di Azure è configurato per consentire l'accesso dalla rete virtuale tramite endpoint o se il criterio predefinito per la risorsa è impostato su *Consenti tutto.*
    - Verificare che la diagnostica del servizio mostri il traffico sugli endpoint.
    - Controllare se i log di flusso del gruppo di sicurezza di rete mostrano l'accesso e controllare che i log di archiviazione mostrino l'accesso, come previsto, sugli endpoint di servizio.
    - Contattare il supporto tecnico di Azure.
- Accesso negato per gli account non specificati nei criteri degli endpoint di servizio
  - Verificare se Archiviazione di Azure è configurata per consentire l'accesso dalla rete virtuale tramite endpoint o se i criteri predefiniti per la risorsa sono impostati su *Consenti tutto.*
  - Verificare che gli account non siano **account di archiviazione classici** con criteri degli endpoint del servizio nella subnet.
- Un servizio di Azure gestito ha smesso di funzionare dopo l'applicazione di un criterio endpoint del servizio sulla subnetA managed Azure Service stopped to working after aPplying a Service Endpoint Policy over the subnet
  - I servizi gestiti non sono attualmente supportati con i criteri degli endpoint del servizio. *Guarda questo spazio per gli aggiornamenti*.

## <a name="provisioning"></a>Provisioning

I criteri degli endpoint di servizio possono essere configurati nelle subnet da un utente con accesso in scrittura a una rete virtuale. Leggere altre informazioni sui [ruoli predefiniti](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) di Azure e sull'assegnazione di autorizzazioni specifiche ai [ruoli personalizzati](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Le reti virtuali e gli account di archiviazione di Azure possono trovarsi nella stessa sottoscrizione o in sottoscrizioni diverse oppure nei tenant di Azure Active Directory.Virtual networks and Azure Storage accounts can be in the same or different subscriptions, or Azure Active Directory tenants.

## <a name="limitations"></a>Limitazioni

- È possibile distribuire criteri degli endpoint di servizio solo nelle reti virtuali distribuite con il modello di distribuzione Azure Resource Manager.
- Le reti virtuali devono trovarsi nella stessa area dei criteri degli endpoint di servizio.
- È possibile applicare criteri degli endpoint di servizio in una subnet solo se gli endpoint di servizio sono configurati per i servizi di Azure specificati nei criteri.
- Non è possibile usare i criteri degli endpoint di servizio per il traffico dalla rete locale ai servizi di Azure.
- I servizi gestiti di Azure non supportano attualmente i criteri endpoint. Sono inclusi i servizi gestiti distribuiti nelle subnet condivise (ad esempio *Azure HDInsight, Azure Batch, Azure ADDS, Azure APplication Gateway, gateway VPN*di Azure, Firewall di Azure) o nelle subnet dedicate (ad esempio Azure App Service *Environment, Azure Redis Cache, Azure API Management, Azure SQL MI, servizi gestiti classici*).

 > [!WARNING]
 > I servizi di Azure distribuiti nella rete virtuale, ad esempio Azure HDInsight, accedono ad altri servizi di Azure, ad esempio Archiviazione di Azure, per i requisiti di infrastruttura. Limitando i criteri degli endpoint a risorse specifiche si potrebbe interrompere l'accesso a queste risorse di infrastruttura per i servizi di Azure distribuiti nella rete virtuale.

- Gli account di archiviazione della versione classica non sono supportati nei criteri degli endpoint. Per impostazione predefinita, i criteri negano l'accesso a tutti gli account di archiviazione della versione classica. Se l'applicazione deve accedere ad Azure Resource Manager e agli account di archiviazione della versione classica, i criteri degli endpoint non devono essere usati per il traffico.

## <a name="pricing-and-limits"></a>Prezzi e limiti

Non sono previsti costi aggiuntivi per l'uso dei criteri degli endpoint di servizio. Viene applicato il modello di determinazione dei prezzi corrente per i servizi di Azure, ad esempio Archiviazione di Azure, per gli endpoint di servizio.

Per i criteri degli endpoint di servizio vengono applicati i limiti seguenti: 

 |Risorsa | Limite predefinito |
 |---------|---------------|
 |ServiceEndpointPoliciesPerSubscription |500 |
 |ServiceEndpintPoliciesPerSubnet|100 |
 |ServiceResourcesPerServiceEndpointPolicyDefinition|200 |

## <a name="next-steps"></a>Passaggi successivi

- Leggere [come configurare i criteri degli endpoint servizio di rete virtuale](virtual-network-service-endpoint-policies-portal.md)
- Altre informazioni sugli endpoint del servizio di rete virtualeLearn more about [Virtual network Service Endpoints](virtual-network-service-endpoints-overview.md)
