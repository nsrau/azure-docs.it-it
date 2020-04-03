---
title: Distribuire Esplora dati di Azure nella rete virtualeDeploy Azure Data Explorer into your Virtual Network
description: Informazioni su come distribuire Azure Data Explorer nella rete virtuale
author: basaba
ms.author: basaba
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 10/31/2019
ms.openlocfilehash: b0530ddada68cc9d07753a3b8ab30bff642e26dd
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/02/2020
ms.locfileid: "80618654"
---
# <a name="deploy-azure-data-explorer-cluster-into-your-virtual-network"></a>Distribuire il cluster di Azure Data Explorer nella rete virtualeDeploy Azure Data Explorer cluster into your Virtual Network

Questo articolo illustra le risorse presenti quando si distribuisce un cluster di Azure Data Explorer in una rete virtuale di Azure personalizzata. Queste informazioni consentono di distribuire un cluster in una subnet nella rete virtuale (VNet). Per altre informazioni sulle reti virtuali di Azure, vedere Che cos'è la rete virtuale di Azure.For more information on Azure [Virtual Networks, see What is Azure Virtual Network?](/azure/virtual-network/virtual-networks-overview)

   ![diagramma vnet](media/vnet-deployment/vnet-diagram.png)

Azure Data Explorer supporta la distribuzione di un cluster in una subnet nella rete virtuale (VNet). Questa funzionalità consente di:

* Applicare le regole del gruppo di sicurezza di rete (NSG) al traffico del cluster di Azure Data Explorer.Enforce [Network Security Group](/azure/virtual-network/security-overview) (NSG) rules to your Azure Data Explorer cluster traffic.
* Connettere la rete locale alla subnet del cluster Azure Data Explorer.Connect your on-premises network to Azure Data Explorer cluster's subnet.
* Proteggere le origini di connessione dati ([Hub eventi](/azure/event-hubs/event-hubs-about) e Griglia [di eventi](/azure/event-grid/overview)) con gli endpoint del [servizio](/azure/virtual-network/virtual-network-service-endpoints-overview).

## <a name="access-your-azure-data-explorer-cluster-in-your-vnet"></a>Accedere al cluster di Azure Data Explorer nella rete virtualeAccess your Azure Data Explorer cluster in your VNet

È possibile accedere al cluster di Azure Data Explorer usando gli indirizzi IP seguenti per ogni servizio (servizi di gestione di motori e dati):You can access your Azure Data Explorer cluster using the following IP addresses for each service (engine and data management services):

* **IP privato**: Utilizzato per accedere al cluster all'interno della rete virtuale.
* **IP pubblico**: utilizzato per accedere al cluster dall'esterno della rete virtuale per la gestione e il monitoraggio e come indirizzo di origine per le connessioni in uscita avviate dal cluster.

Per accedere al servizio vengono creati i seguenti record DNS: 

* `[clustername].[geo-region].kusto.windows.net`(motore) `ingest-[clustername].[geo-region].kusto.windows.net` (gestione dei dati) sono mappati all'IP pubblico per ogni servizio. 

* `private-[clustername].[geo-region].kusto.windows.net`(motore) `private-ingest-[clustername].[geo-region].kusto.windows.net` (gestione dei dati) sono mappati all'IP privato per ogni servizio.

## <a name="plan-subnet-size-in-your-vnet"></a>Pianificare le dimensioni della subnet nella rete virtualePlan subnet size in your VNet

Le dimensioni della subnet usata per ospitare un cluster di Azure Data Explorer non possono essere modificate dopo la distribuzione della subnet. Nella rete virtuale Azure Data Explorer usa un indirizzo IP privato per ogni macchina virtuale e due indirizzi IP privati per i servizi di bilanciamento del carico interni (gestione di motori e dati). La rete di Azure usa anche cinque indirizzi IP per ogni subnet. Azure Data Explorer esegue il provisioning di due macchine virtuali per il servizio di gestione dei dati. Viene eseguito il provisioning delle macchine virtuali del servizio motore per ogni capacità di scalabilità della configurazione utente.

Il numero totale di indirizzi IP:

| Uso | Numero di indirizzi |
| --- | --- |
| Servizio motore | 1 per istanza |
| Servizio di gestione dei dati | 2 |
| Servizi di bilanciamento del carico interni | 2 |
| Indirizzi riservati di AzureAzure reserved addresses | 5 |
| **Totale** | **#engine_instances n. 9** |

> [!IMPORTANT]
> Le dimensioni della subnet devono essere pianificate in anticipo poiché non possono essere modificate dopo la distribuzione di Azure Data Explorer.Subnet size must be planned in advance since it can't be changed after Azure Data Explorer is deployed. Pertanto, riservare le dimensioni della subnet necessarie di conseguenza.

## <a name="service-endpoints-for-connecting-to-azure-data-explorer"></a>Endpoint di servizio per la connessione ad Azure Data ExplorerService endpoints for connecting to Azure Data Explorer

[Gli endpoint](/azure/virtual-network/virtual-network-service-endpoints-overview) del servizio di Azure consentono di proteggere le risorse multi-tenant di Azure nella rete virtuale.
La distribuzione del cluster di Azure Data Explorer nella subnet consente di configurare le connessioni dati con Hub eventi o Griglia di eventi limitando le risorse sottostanti per la subnet di Azure Data Explorer.Deploying Azure Data Explorer cluster into your subnet allows you to setup data connections with [Event Hub](/azure/event-hubs/event-hubs-about) or [Event Grid](/azure/event-grid/overview) while restricting the underlying resources for Azure Data Explorer subnet.

> [!NOTE]
> Quando si usa l'installazione di EventGrid con [Archiviazione](/azure/storage/common/storage-introduction) e [Hub eventi], l'account di archiviazione usato nella sottoscrizione può essere bloccato con gli endpoint del servizio nella subnet di Azure Data Explorer consentendo al contempo servizi di piattaforma Azure attendibili nella configurazione del [firewall,](/azure/storage/common/storage-network-security)ma l'hub eventi non può abilitare l'endpoint del servizio poiché non supporta i servizi di [piattaforma Azure](/azure/event-hubs/event-hubs-service-endpoints)attendibili.

## <a name="dependencies-for-vnet-deployment"></a>Dipendenze per la distribuzione di reti virtuali

### <a name="network-security-groups-configuration"></a>Configurazione dei gruppi di sicurezza di rete

I gruppi di sicurezza di rete (NSG) consentono di controllare l'accesso alla rete all'interno di una rete [virtuale.Network Security Groups (NSG)](/azure/virtual-network/security-overview) provide the ability to control network access within a VNet. È possibile accedere a Azure Data Explorer usando due endpoint: HTTPs (443) e TDS (1433). Le regole del gruppo di sicurezza di rete seguenti devono essere configurate per consentire l'accesso a questi endpoint per la gestione, il monitoraggio e il corretto funzionamento del cluster.

#### <a name="inbound-nsg-configuration"></a>Configurazione del gruppo di sicurezza di rete in ingresso

| **Utilizzare**   | **Da**   | **A**   | **Protocollo**   |
| --- | --- | --- | --- |
| Gestione  |[Indirizzi di gestione ADX](#azure-data-explorer-management-ip-addresses)/AzureDataExplorerManagement(ServiceTag)ADX management addresses /AzureDataExplorerManagement(ServiceTag) | Subnet ADX:443  | TCP  |
| Monitoraggio dell’integrità  | [Indirizzi di monitoraggio dello stato di ADX](#health-monitoring-addresses)  | Subnet ADX:443  | TCP  |
| Comunicazione interna ADX  | Subnet ADX: tutte le porte  | Subnet ADX:Tutte le porte  | Tutti  |
| Consentire il servizio di bilanciamento del carico di Azure in ingresso (probe di integrità)Allow Azure load balancer inbound (health probe)  | AzureLoadBalancer  | Subnet ADX:80,443  | TCP  |

#### <a name="outbound-nsg-configuration"></a>Configurazione del gruppo di sicurezza di rete in uscitaOutbound NSG configuration

| **Utilizzare**   | **Da**   | **A**   | **Protocollo**   |
| --- | --- | --- | --- |
| Dipendenza da Archiviazione di Azure  | Subnet ADX  | Stoccaggio:443  | TCP  |
| Dipendenza da Azure Data LakeDependency on Azure Data Lake  | Subnet ADX  | AzureDataLake:443AzureDataLake:443  | TCP  |
| Inserimento di EventHub e monitoraggio dei servizi  | Subnet ADX  | EventHub:443,5671  | TCP  |
| Pubblicare metriche  | Subnet ADX  | Monitor di Azure:443 | TCP  |
| Download della configurazione di Azure MonitorAzure Monitor configuration download  | Subnet ADX  | [Indirizzi dell'endpoint](#azure-monitor-configuration-endpoint-addresses)di configurazione di Monitoraggio di Azure:443Azure Monitor configuration endpoint addresses :443 | TCP  |
| Active Directory (se applicabile) | Subnet ADX | AzureActiveDirectory:443 | TCP |
| Autorità di certificazione | Subnet ADX | Internet:80 | TCP |
| Comunicazione interna  | Subnet ADX  | Subnet ADX:Tutte le porte  | Tutti  |
| Porte utilizzate per `sql\_request` `http\_request` e plugin  | Subnet ADX  | Internet:Personalizzato  | TCP  |

### <a name="relevant-ip-addresses"></a>Indirizzi IP pertinenti

#### <a name="azure-data-explorer-management-ip-addresses"></a>Indirizzi IP di gestione di Azure Data ExplorerAzure Data Explorer management IP addresses

| Region | Indirizzi |
| --- | --- |
| Australia centrale | 20.37.26.134 |
| Australia Centrale2 | 20.39.99.177 |
| Australia orientale | 40.82.217.84 |
| Australia sud-orientale | 20.40.161.39 |
| BrasileSud | 191.233.25.183 |
| Canada centrale | 40.82.188.208 |
| Canada orientale | 40.80.255.12 |
| India centrale | 40.81.249.251, 104.211.98.159 |
| Stati Uniti centrali | 40.67.188.68 |
| Stati Uniti centrali EUAP | 40.89.56.69 |
| Asia orientale | 20.189.74.103 |
| Stati Uniti orientali | 52.224.146.56 |
| Stati Uniti Orientali 2 | 52.232.230.201 |
| East US2 EUAP | 52.253.226.110 |
| Francia centrale | 40.66.57.91 |
| Francia meridionale | 40.82.236.24 |
| Giappone orientale | 20.43.89.90 |
| Giappone occidentale | 40.81.184.86 |
| Corea centrale | 40.82.156.149 |
| Corea meridionale | 40.80.234.9 |
| Stati Uniti centro-settentrionali | 40.81.45.254 |
| Europa settentrionale | 52.142.91.221 |
| Sudafrica settentrionale | 102.133.129.138 |
| Sudafrica Ovest | 102.133.0.97 |
| Stati Uniti centro-meridionali | 20.45.3.60 |
| Asia sud-orientale | 40.119.203.252 |
| India meridionale | 40.81.72.110, 104.211.224.189 |
| Regno Unito meridionale | 40.81.154.254 |
| Regno Unito occidentale | 40.81.122.39 |
| Stati Uniti centro-occidentali | 52.159.55.120 |
| Europa occidentale | 51.145.176.215 |
| India occidentale | 40.81.88.112, 104.211.160.120 |
| Stati Uniti occidentali | 13.64.38.225 |
| Stati Uniti occidentali 2 | 40.90.219.23 |

#### <a name="health-monitoring-addresses"></a>Indirizzi di monitoraggio dello stato

| Region | Indirizzi |
| --- | --- |
| Australia centrale | 191.239.64.128 |
| Australia centrale 2 | 191.239.64.128 |
| Australia orientale | 191.239.64.128 |
| Australia sud-orientale | 191.239.160.47 |
| Brasile meridionale | 23.98.145.105 |
| Canada centrale | 168.61.212.201 |
| Canada orientale | 168.61.212.201 |
| India centrale | 23.99.5.162 |
| Stati Uniti centrali | 168.61.212.201 |
| Stati Uniti centrali EUAP | 168.61.212.201 |
| Asia orientale | 168.63.212.33 |
| Stati Uniti orientali | 137.116.81.189 |
| Stati Uniti orientali 2 | 137.116.81.189 |
| Stati Uniti orientali 2 EUAP | 137.116.81.189 |
| Francia centrale | 23.97.212.5 |
| Francia meridionale | 23.97.212.5 |
| Giappone orientale | 138.91.19.129 |
| Giappone occidentale | 138.91.19.129 |
| Corea centrale | 138.91.19.129 |
| Corea meridionale | 138.91.19.129 |
| Stati Uniti centro-settentrionali | 23.96.212.108 |
| Europa settentrionale | 191.235.212.69 
| Sudafrica settentrionale | 104.211.224.189 |
| Sudafrica Ovest | 104.211.224.189 |
| Stati Uniti centro-meridionali | 23.98.145.105 |
| India meridionale | 23.99.5.162 |
| Asia sud-orientale | 168.63.173.234 |
| Regno Unito meridionale | 23.97.212.5 |
| Regno Unito occidentale | 23.97.212.5 |
| Stati Uniti centro-occidentali | 168.61.212.201 |
| Europa occidentale | 23.97.212.5 |
| India occidentale | 23.99.5.162 |
| Stati Uniti occidentali | 23.99.5.162 |
| Stati Uniti occidentali 2 | 23.99.5.162, 104.210.32.14 |    

#### <a name="azure-monitor-configuration-endpoint-addresses"></a>Indirizzi degli endpoint di configurazione di Monitoraggio di AzureAzure Monitor configuration endpoint addresses

| Region | Indirizzi |
| --- | --- |
| Australia centrale | 52.148.86.165 |
| Australia centrale 2 | 52.148.86.165 |
| Australia orientale | 52.148.86.165 |
| Australia Sud-est | 52.148.86.165 |
| Brasile meridionale | 13.68.89.19 |
| Canada centrale | 13.90.43.231 |
| Canada orientale | 13.90.43.231 |
| India centrale | 13.71.25.187 |
| Stati Uniti centrali | 52.173.95.68 |
| Stati Uniti centrali EUAP | 13.90.43.231 |
| Asia orientale | 13.75.117.221 |
| Stati Uniti orientali | 13.90.43.231 |
| Stati Uniti orientali 2 | 13.68.89.19 |    
| Stati Uniti orientali 2 EUAP | 13.68.89.19 |
| Francia centrale | 52.174.4.112 |
| Francia meridionale | 52.174.4.112 |
| Giappone orientale | 13.75.117.221 |
| Giappone occidentale | 13.75.117.221 |
| Corea centrale | 13.75.117.221 |
| Corea meridionale | 13.75.117.221 |
| Stati Uniti centro-settentrionali | 52.162.240.236 |
| Europa settentrionale | 52.169.237.246 |
| Sudafrica settentrionale | 13.71.25.187 |
| Sudafrica Ovest | 13.71.25.187 |
| Stati Uniti centro-meridionali | 13.84.173.99 |
| India meridionale | 13.71.25.187 |
| Asia sud-orientale | 52.148.86.165 |
| Regno Unito meridionale | 52.174.4.112 |
| Regno Unito occidentale | 52.169.237.246 |
| Stati Uniti centro-occidentali | 52.161.31.69 |
| Europa occidentale | 52.174.4.112 |
| India occidentale | 13.71.25.187 |
| Stati Uniti occidentali | 40.78.70.148 |
| Stati Uniti occidentali 2 | 52.151.20.103 |

## <a name="expressroute-setup"></a>Configurazione di ExpressRouteExpressRoute setup

Usare ExpressRoute per connettersi alla rete locale alla rete virtuale di Azure.Use ExpressRoute to connect on premises network to the Azure Virtual Network. Un'impostazione comune consiste nell'annunciare la route predefinita (0.0.0.0/0) tramite la sessione BGP (Border Gateway Protocol). In questo modo, il traffico proveniente dalla rete virtuale viene inoltrato alla rete premessa del cliente che potrebbe causare l'interruzione del traffico, causando l'interruzione dei flussi in uscita. Per ovviare a questa impostazione predefinita, è possibile configurare [La route definita dall'utente (UDR, User Defined Route)](/azure/virtual-network/virtual-networks-udr-overview#user-defined) (0.0.0.0/0) e l'hop successivo sarà *Internet*. Poiché l'UDR ha la precedenza su BGP, il traffico sarà destinato a Internet.

## <a name="securing-outbound-traffic-with-firewall"></a>Protezione del traffico in uscita con il firewall

Se si vuole proteggere il traffico in uscita usando Firewall di [Azure](/azure/firewall/overview) o qualsiasi appliance virtuale per limitare i nomi di dominio, nel firewall devono essere consentiti i seguenti nomi di dominio completi (FQDN) nel firewall.

```
prod.warmpath.msftcloudes.com:443
production.diagnostics.monitoring.core.windows.net:443
graph.windows.net:443
*.update.microsoft.com:443
shavamanifestcdnprod1.azureedge.net:443
login.live.com:443
wdcp.microsoft.com:443
login.microsoftonline.com:443
azureprofilerfrontdoor.cloudapp.net:443
*.core.windows.net:443
*.servicebus.windows.net:443
shoebox2.metrics.nsatc.net:443
prod-dsts.dsts.core.windows.net:443
ocsp.msocsp.com:80
*.windowsupdate.com:80
ocsp.digicert.com:80
go.microsoft.com:80
dmd.metaservices.microsoft.com:80
www.msftconnecttest.com:80
crl.microsoft.com:80
www.microsoft.com:80
adl.windows.com:80
crl3.digicert.com:80
```

È inoltre necessario definire la [tabella](/azure/virtual-network/virtual-networks-udr-overview) di route nella subnet con [gli indirizzi](#azure-data-explorer-management-ip-addresses) di gestione e gli indirizzi di monitoraggio [dell'integrità](#health-monitoring-addresses) con *L'hop* successivo Internet per evitare problemi di route asimmetriche.

Ad esempio, per l'area **Stati Uniti occidentali,** è necessario definire i seguenti UDR:

| Nome | Prefisso indirizzo | Hop successivo |
| --- | --- | --- |
| ADX_Management | 13.64.38.225/32 | Internet |
| ADX_Monitoring | 23.99.5.162/32 | Internet |

## <a name="deploy-azure-data-explorer-cluster-into-your-vnet-using-an-azure-resource-manager-template"></a>Distribuire il cluster di Azure Data Explorer nella rete virtuale usando un modello di Azure Resource ManagerDeploy Azure Data Explorer cluster into your VNet using an Azure Resource Manager template

Per distribuire il cluster di Azure Data Explorer nella rete virtuale, usare il cluster Deploy Azure Data Explorer nella rete virtuale di Azure Resource Manager.To deploy Azure Data Explorer cluster into your virtual network, use the [Deploy Azure Data Explorer cluster into your VNet](https://azure.microsoft.com/resources/templates/101-kusto-vnet/) Azure Resource Manager template.

Questo modello crea il cluster, la rete virtuale, la subnet, il gruppo di sicurezza di rete e gli indirizzi IP pubblici.
