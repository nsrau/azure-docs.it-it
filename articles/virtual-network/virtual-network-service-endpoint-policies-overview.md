---
title: Criteri degli endpoint servizio di rete virtuale di Azure | Microsoft Docs
description: Informazioni su come filtrare il traffico di rete virtuale per le risorse dei servizi di Azure usando criteri degli endpoint di servizio
services: virtual-network
documentationcenter: na
author: sumeetmittal
ms.service: virtual-network
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/18/2018
ms.author: sumeet.mittal
ms.openlocfilehash: b39f365c8b66f7cab074a20bc574803e12f93422
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61033901"
---
# <a name="virtual-network-service-endpoint-policies-preview"></a>Criteri degli endpoint servizio di rete virtuale (anteprima)

I criteri degli endpoint di servizio di rete virtuale permettono di filtrare il traffico di rete virtuale per i servizi di Azure, consentendo l'accesso solo a risorse specifiche dei servizi di Azure sugli endpoint di servizio. I criteri degli endpoint forniscono il controllo granulare dell'accesso per il traffico di rete virtuale per i servizi di Azure.

Questa funzionalità è disponibile in __anteprima__ per i servizi e le aree di Azure seguenti:

__Archiviazione di Azure__: WestCentralUS, WestUS2, NorthCentralUS, SouthCentralUS, CentralUS, EastUS2.

Per le notifiche più aggiornate per l'anteprima, vedere la pagina [Aggiornamenti di Azure](https://azure.microsoft.com/updates/?product=virtual-network).

> [!NOTE]  
> Durante l'anteprima, i criteri degli endpoint servizio di rete virtuale potrebbero non offrire lo stesso livello di disponibilità e affidabilità delle funzionalità presenti nella versione con disponibilità generale. Per altre informazioni, vedere [Condizioni Supplementari di Microsoft Azure le Anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="key-benefits"></a>Vantaggi principali

I criteri degli endpoint servizio di rete virtuale offrono i vantaggi seguenti:

- __Maggiore sicurezza per il traffico di rete virtuale per i servizi di Azure__

  I [tag dei servizi di Azure per i gruppi di sicurezza di rete](https://aka.ms/servicetags) consentono di limitare il traffico in uscita dalla rete virtuale a servizi di Azure specifici. Tuttavia, in questo modo si consente il traffico verso qualsiasi risorsa di tale servizio di Azure. 
  
  Con i criteri degli endpoint, è ora possibile limitare l'accesso in uscita dalla rete virtuale solo a risorse di Azure specifiche. Ciò offre un controllo della sicurezza molto più granulare, per la protezione dei dati a cui viene eseguito l'accesso in una rete virtuale. 

- __Criteri scalabili a disponibilità elevata per filtrare il traffico dei servizi di Azure__

   I criteri degli endpoint forniscono una soluzione a disponibilità elevata e con scalabilità orizzontale per filtrare il traffico dei servizi di Azure dalle reti virtuali sugli endpoint di servizio. Non è richiesto alcun sovraccarico aggiuntivo per la gestione di appliance di rete centrali per il traffico nelle reti virtuali.

## <a name="configuration"></a>Configurazione

- È possibile configurare i criteri degli endpoint per limitare il traffico di rete virtuale per risorse dei servizi di Azure specifiche. Per la versione di anteprima, sono supportati i criteri degli endpoint per Archiviazione di Azure. 
- I criteri degli endpoint sono configurati in una subnet di una rete virtuale. Per applicare i criteri, gli endpoint di servizio devono essere abilitati nella subnet, per tutti i servizi di Azure specificati nei criteri.
- I criteri degli endpoint consentono di autorizzare risorse dei servizi di Azure specifiche, usando il formato resourceID. È possibile limitare l'accesso a tutte le risorse di una sottoscrizione o a un gruppo di risorse. È anche possibile limitare l'accesso a risorse specifiche. 
- Per impostazione predefinita, se a una subnet non solo collegati criteri con gli endpoint, è possibile accedere a tutte le risorse nel servizio. Dopo la configurazione dei criteri in tale subnet, dalle istanze di calcolo in tale subnet è possibile accedere solo alle risorse specificate nei criteri. L'accesso a tutte le altre risorse, per il servizio specifico, viene negato. 
- È possibile filtrare il traffico per le risorse dei servizi di Azure nelle aree in cui è configurato l'endpoint di servizio. L'accesso alle risorse dei servizi nelle altre aree viene consentito per impostazione predefinita. 

  > [!NOTE]  
  > Per bloccare completamente l'accesso in uscita dalla rete virtuale alle risorse di Azure nelle aree degli endpoint di servizio, è anche necessario configurare regole dei gruppi di sicurezza di rete per consentire il traffico solo per le aree degli endpoint di servizio, tramite [tag del servizio](security-overview.md#service-tags).

- È possibile applicare più criteri a una subnet. Quando alla subnet sono associati più criteri per lo stesso servizio, viene consentito il traffico di rete virtuale per le risorse specificate in ognuno dei criteri. L'accesso a tutte le altre risorse dei servizi, non specificate in alcuno dei criteri, viene negato. 

  > [!NOTE]  
  > I criteri consentono l'accesso solo alle risorse dei servizi specificate da una rete virtuale. Tutto il restante traffico verso il servizio viene negato automaticamente, quando si aggiungono risorse specifiche ai criteri. Assicurarsi che tutte le dipendenze delle risorse dei servizi per le applicazioni possano essere identificate e specificate nei criteri.

  > [!WARNING]  
  > I servizi di Azure distribuiti nella rete virtuale, ad esempio Azure HDInsight, accedono ad altri servizi di Azure, ad esempio Archiviazione di Azure, per i requisiti di infrastruttura. Limitando i criteri degli endpoint a risorse specifiche si potrebbe interrompere l'accesso a queste risorse di infrastruttura per i servizi distribuiti nella rete virtuale. Per i servizi specifici, vedere [Limitazioni](#limitations). Durante l'anteprima, i criteri degli endpoint di servizio non sono supportati per i servizi di Azure gestiti distribuiti nella rete virtuale.

- Per Archiviazione di Azure: 
  -  È possibile limitare l'accesso specificando i valori *resourceId* di Azure Resource Manager dell'account di archiviazione. Ciò riguarda il traffico verso BLOB, tabelle, code, file e Azure Data Lake Storage Gen2.

     Gli account di archiviazione di Azure possono ad esempio essere specificati nella definizione dei criteri degli endpoint come illustrato di seguito:
    
     Per consentire un account di archiviazione specifico:         
     `subscriptions/subscriptionId/resourceGroups/resourceGroup/providers/Microsoft.Storage/storageAccounts/storageAccountName`
      
     Per consentire tutti gli account in una sottoscrizione e un gruppo di risorse: `/subscriptions/subscriptionId/resourceGroups/resourceGroup`
     
     Per consentire tutti gli account in una sottoscrizione: `/subscriptions/subscriptionId`
    
- Nei criteri degli endpoint è possibile specificare solo gli account di archiviazione che usano il modello Azure Resource Manager.  

  > [!NOTE]  
  > L'accesso agli account di archiviazione della versione classica è bloccato con i criteri degli endpoint.

- La località primaria per l'account specificato deve essere nelle aree geografiche associate dell'endpoint di servizio per la subnet. 

  > [!NOTE]  
  > I criteri consentono di specificare risorse dei servizi di altre aree. L'accesso di rete virtuale per i servizi di Azure viene filtrato solo per le aree geografiche associate. Se i gruppi di sicurezza di rete non sono limitati alle aree geografiche associate per Archiviazione di Azure, la rete virtuale può accedere a tutti gli account di archiviazione all'esterno delle aree geografiche associate.

- L'accesso secondario RA-GRS (con ridondanza geografica e accesso in lettura) viene consentito automaticamente se è specificato l'account principale. 
- Gli account di archiviazione possono trovarsi in uno stesso o in un diverso tenant di Azure Active Directory o sottoscrizione rispetto alla rete virtuale. 

## <a name="limitations"></a>Limitazioni

- È possibile distribuire criteri degli endpoint di servizio solo nelle reti virtuali distribuite con il modello di distribuzione Azure Resource Manager.
- Le reti virtuali devono trovarsi nella stessa area dei criteri degli endpoint di servizio.
- È possibile applicare criteri degli endpoint di servizio in una subnet solo se gli endpoint di servizio sono configurati per i servizi di Azure specificati nei criteri.
- Non è possibile usare i criteri degli endpoint di servizio per il traffico dalla rete locale ai servizi di Azure.
- I criteri degli endpoint non devono essere applicati alle subnet con servizi di Azure gestiti, che dipendono da altri servizi di Azure per i requisiti di infrastruttura. 

  > [!WARNING]  
  > I servizi di Azure distribuiti nella rete virtuale, ad esempio Azure HDInsight, accedono ad altri servizi di Azure, ad esempio Archiviazione di Azure, per i requisiti di infrastruttura. Limitando i criteri degli endpoint a risorse specifiche si potrebbe interrompere l'accesso a queste risorse di infrastruttura per i servizi di Azure distribuiti nella rete virtuale.
  
  - Alcuni servizi di Azure possono essere distribuiti in subnet con altre istanze di calcolo. Assicurarsi che i criteri degli endpoint non vengano applicati alla subnet se nella subnet sono distribuiti i servizi gestiti elencati di seguito.
   
    - HDInsight di Azure
    - Azure Batch (Azure Resource Manager)
    - Azure Active Directory Domain Services (Azure Resource Manager)
    - Gateway applicazione di Azure (Azure Resource Manager)
    - Gateway VPN di Azure (Azure Resource Manager)
    - Firewall di Azure

  - Alcuni servizi di Azure vengono distribuiti in subnet dedicate. I criteri degli endpoint sono bloccati in tutti i servizi di questo tipo, elencati di seguito, durante la fase di anteprima. 

     - Ambiente del servizio app di Azure
     - Cache Redis di Azure
     - Gestione API di Azure
     - Istanza gestita di SQL di Azure
     - Servizi di dominio Azure Active Directory
     - Gateway applicazione di Azure (versione classica)
     - Gateway VPN di Azure (versione classica)

- Archiviazione di Azure. Gli account di archiviazione della versione classica non sono supportati nei criteri degli endpoint. Per impostazione predefinita, i criteri negano l'accesso a tutti gli account di archiviazione della versione classica. Se l'applicazione deve accedere ad Azure Resource Manager e agli account di archiviazione della versione classica, i criteri degli endpoint non devono essere usati per il traffico. 

## <a name="nsgs-with-service-endpoint-policies"></a>Gruppi di sicurezza di rete con criteri degli endpoint di servizio
- Per impostazione predefinita, i gruppi di sicurezza di rete consentono il traffico Internet in uscita, incluso il traffico di rete virtuale per i servizi di Azure.
- Per negare tutto il traffico Internet in uscita e consentire solo il traffico per risorse dei servizi di Azure specifiche: 

  Passaggio 1: configurare i gruppi di sicurezza di rete per consentire il traffico in uscita solo per i servizi di Azure nelle aree degli endpoint, usando *tag dei servizi di Azure*. Per altre informazioni, vedere [Tag dei servizi per i gruppi di sicurezza di rete](https://aka.ms/servicetags)
      
  Le regole del gruppo di sicurezza di rete che limitano l'accesso solo alle aree degli endpoint hanno ad esempio un aspetto simile al seguente:

  ```
  Allow AzureStorage.WestUS2,
  Allow AzureStorage.WestCentralUS,
  Deny all
  ```

  Passaggio 2: applicare i criteri degli endpoint di servizio con accesso solo a risorse dei servizi di Azure specifiche.

  > [!WARNING]  
  > Se il gruppo di sicurezza di rete non è configurato per limitare l'accesso della rete virtuale ai servizi di Azure solo alle aree degli endpoint, è possibile accedere alle risorse dei servizi in altre aree, anche se vengono applicati i criteri degli endpoint di servizio.

## <a name="scenarios"></a>Scenari

- **Reti virtuali multiple, connesse o con peering**: per filtrare il traffico nelle reti virtuali con peering, i criteri degli endpoint devono essere applicati individualmente a tali reti virtuali.
- **Filtro del traffico Internet con appliance di rete o Firewall di Azure**: filtrare il traffico dei servizi di Azure con i criteri sugli endpoint e filtrare il resto del traffico Internet o di Azure tramite appliance o Firewall di Azure. 
- **Filtro del traffico nei servizi di Azure distribuiti nelle reti virtuali**: durante l'anteprima, i criteri degli endpoint di servizio non sono supportati per i servizi di Azure gestiti distribuiti nella rete virtuale. 
 Per i servizi specifici, vedere [Limitazioni](#limitations).
- **Filtro del traffico dall'ambiente locale ai servizi di Azure**: i criteri degli endpoint di servizio si applicano solo al traffico dalle subnet associate ai criteri. Per consentire l'accesso a risorse dei servizi di Azure specifiche dall'ambiente locale, il traffico deve essere filtrato tramite firewall o appliance virtuali di rete.

## <a name="logging-and-troubleshooting"></a>Registrazione e risoluzione dei problemi
Non sono disponibili funzionalità di registrazione centralizzata per i criteri degli endpoint di servizio. Per i log di diagnostica del servizio, vedere [Registrazione degli endpoint di servizio](virtual-network-service-endpoints-overview.md#logging-and-troubleshooting).

### <a name="troubleshooting-scenarios"></a>Scenari di risoluzione dei problemi
- Accesso consentito agli account di archiviazione non specificati nei criteri degli endpoint
  - I gruppi di sicurezza di rete potrebbero consentire l'accesso a Internet o agli account di archiviazione di Azure in altre aree.
  - I gruppi di sicurezza di rete devono essere configurati per negare tutto il traffico Internet in uscita e consentire solo il traffico per aree di Archiviazione di Azure specifiche. Per informazioni dettagliate, vedere la sezione relativa ai gruppi di sicurezza di rete.
- Accesso negato per gli account specificati nei criteri degli endpoint
  - L'accesso potrebbe essere bloccato da gruppi di sicurezza di rete o filtro del firewall
  - Se la rimozione e la nuova applicazione dei criteri comporta la perdita di connettività:
    - Verificare se il servizio di Azure è configurato per consentire l'accesso dalla rete virtuale, tramite gli endpoint, o verificare che i criteri predefiniti per la risorsa siano impostati su *Consenti tutto*.
      > [!NOTE]      
      > Non è necessario che le risorse dei servizi siano associate alle reti virtuali per ottenere l'accesso tramite i criteri degli endpoint. Tuttavia, come procedura consigliata per la sicurezza, è consigliabile che le risorse dei servizi siano associate alle reti attendibili, ad esempio le reti virtuali di Azure, tramite gli endpoint di servizio e in locale tramite un firewall IP.
  
    - Verificare che la diagnostica del servizio mostri il traffico sugli endpoint.
    - Controllare se i log di flusso del gruppo di sicurezza di rete mostrano l'accesso e controllare che i log di archiviazione mostrino l'accesso, come previsto, sugli endpoint di servizio.
    - Contattare il supporto tecnico di Azure.
- Accesso negato per gli account non specificati nei criteri degli endpoint di servizio
  - L'accesso potrebbe essere bloccato da gruppi di sicurezza di rete o filtro del firewall. Verificare che il tag del servizio *Archiviazione di Azure* sia consentito per le aree degli endpoint. Per le restrizioni dei criteri, vedere [Limitazioni](#limitations).
  Ad esempio, agli account di archiviazione della versione classica viene negato l'accesso se vengono applicati i criteri.
  - Verificare se il servizio di Azure è configurato per consentire l'accesso dalla rete virtuale, tramite gli endpoint, o verificare se i criteri predefiniti per la risorsa sono impostati su *Consenti tutto*.

## <a name="provisioning"></a>Provisioning

I criteri degli endpoint di servizio possono essere configurati nelle subnet da un utente con accesso in scrittura a una rete virtuale. Leggere altre informazioni sui [ruoli predefiniti](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) di Azure e sull'assegnazione di autorizzazioni specifiche ai [ruoli personalizzati](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Le reti virtuali e le risorse dei servizi di Azure possono trovarsi in uno stesso o in un diverso tenant di Azure Active Directory o sottoscrizione. 

## <a name="pricing-and-limits"></a>Prezzi e limiti

Non sono previsti costi aggiuntivi per l'uso dei criteri degli endpoint di servizio. Viene applicato il modello di determinazione dei prezzi corrente per i servizi di Azure, ad esempio Archiviazione di Azure, per gli endpoint di servizio.

Per i criteri degli endpoint di servizio vengono applicati i limiti seguenti: 

 |Risorsa | Limite predefinito |
 |---------|---------------|
 |ServiceEndpointPoliciesPerSubscription |500 |
 |ServiceEndpintPoliciesPerSubnet|100 |
 |ServiceResourcesPerServiceEndpointPolicyDefinition|200 |

## <a name="next-steps"></a>Fasi successive

- Leggere [come configurare i criteri degli endpoint servizio di rete virtuale](virtual-network-service-endpoint-policies-portal.md)
- Leggere altre informazioni sugli [endpoint servizio di rete virtuale](virtual-network-service-endpoints-overview.md)

