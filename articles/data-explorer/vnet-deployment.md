---
title: Distribuire Esplora dati di Azure nella rete virtuale (anteprima)
description: Informazioni su come distribuire Esplora dati di Azure nella rete virtuale
author: basaba
ms.author: basaba
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 10/31/2019
ms.openlocfilehash: e845b44c51b7611cd3f23f8b33e6576aced2d6ca
ms.sourcegitcommit: f5e4d0466b417fa511b942fd3bd206aeae0055bc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/06/2020
ms.locfileid: "78851459"
---
# <a name="deploy-azure-data-explorer-into-your-virtual-network-preview"></a>Distribuire Esplora dati di Azure nella rete virtuale (anteprima)

Questo articolo illustra le risorse presenti quando si distribuisce un cluster di Esplora dati di Azure in una rete virtuale di Azure personalizzata. Queste informazioni consentiranno di distribuire un cluster in una subnet nella rete virtuale (VNet). Per altre informazioni sulle reti virtuali di Azure, vedere [che cos'è rete virtuale di Azure?](/azure/virtual-network/virtual-networks-overview)

   ![diagramma VNET](media/vnet-deployment/vnet-diagram.png)

Azure Esplora dati supporta la distribuzione di un cluster in una subnet nella rete virtuale (VNet). Questa funzionalità consente di:

* Applicare le regole del [gruppo di sicurezza di rete](/azure/virtual-network/security-overview) (NSG) nel traffico del cluster di Esplora dati di Azure.
* Connettere la rete locale alla subnet del cluster Esplora dati di Azure.
* Proteggere le origini della connessione dati ([Hub eventi](/azure/event-hubs/event-hubs-about) e [griglia di eventi](/azure/event-grid/overview)) con gli endpoint del [servizio](/azure/virtual-network/virtual-network-service-endpoints-overview).

> [!NOTE]
> L'integrazione e la distribuzione della rete virtuale sono in modalità di anteprima. Per abilitare questa funzionalità, aprire un [ticket di supporto](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).

## <a name="access-your-azure-data-explorer-cluster-in-your-vnet"></a>Accedere al cluster di Esplora dati di Azure in VNet

È possibile accedere al cluster di Azure Esplora dati usando i seguenti indirizzi IP per ogni servizio (motore e servizi di gestione dati):

* **IP privato**: usato per accedere al cluster all'interno della VNet.
* **IP pubblico**: usato per accedere al cluster dall'esterno del VNet per la gestione e il monitoraggio e come indirizzo di origine per le connessioni in uscita avviate dal cluster.

Per accedere al servizio vengono creati i seguenti record DNS: 

* viene eseguito il mapping del `ingest-[clustername].[geo-region].kusto.windows.net` (gestione dati) `[clustername].[geo-region].kusto.windows.net` all'indirizzo IP pubblico per ogni servizio. 

* viene eseguito il mapping del `private-ingest-[clustername].[geo-region].kusto.windows.net` (gestione dati) `private-[clustername].[geo-region].kusto.windows.net` all'indirizzo IP privato per ogni servizio.

## <a name="plan-subnet-size-in-your-vnet"></a>Pianificare le dimensioni della subnet nella VNet

Le dimensioni della subnet usata per ospitare un cluster di Esplora dati di Azure non possono essere modificate dopo la distribuzione della subnet. In VNet, Azure Esplora dati usa un indirizzo IP privato per ogni macchina virtuale e due indirizzi IP privati per i bilanciamenti del carico interni (motore e gestione dati). La rete di Azure usa anche cinque indirizzi IP per ogni subnet. Azure Esplora dati effettua il provisioning di due macchine virtuali per il servizio di gestione dati. Il provisioning delle macchine virtuali del servizio motore viene effettuato in base alla capacità di scala della configurazione utente

Il numero totale di indirizzi IP:

| Utilizzo | Numero di indirizzi |
| --- | --- |
| Servizio motore | 1 per istanza |
| Servizio di gestione dati | 2 |
| Bilanciamento del carico interno | 2 |
| Indirizzi riservati di Azure | 5 |
| **Totale** | **#engine_instances + 9** |

> [!IMPORTANT]
> Le dimensioni della subnet devono essere pianificate in anticipo perché non possono essere modificate dopo la distribuzione di Esplora dati di Azure. Quindi, riservare di conseguenza le dimensioni necessarie per la subnet.

## <a name="service-endpoints-for-connecting-to-azure-data-explorer"></a>Endpoint di servizio per la connessione ad Azure Esplora dati

Gli [endpoint di servizio di Azure](/azure/virtual-network/virtual-network-service-endpoints-overview) consentono di proteggere le risorse multi-tenant di Azure nella rete virtuale.
La distribuzione di Azure Esplora dati cluster nella subnet consente di configurare le connessioni dati con [Hub eventi](/azure/event-hubs/event-hubs-about) o [griglia di eventi](/azure/event-grid/overview) , limitando al contempo le risorse sottostanti per la subnet di Azure Esplora dati.

> [!NOTE]
> Quando si usa il programma di installazione di EventGrid con [archiviazione](/azure/storage/common/storage-introduction) e [Hub eventi], l'account di archiviazione usato nella sottoscrizione può essere bloccato con gli endpoint di servizio nella subnet di Azure Esplora dati, consentendo al contempo i servizi della piattaforma Azure trusted nella [configurazione del firewall](/azure/storage/common/storage-network-security), ma l'hub eventi non può abilitare l'endpoint servizio perché non supporta i [servizi della piattaforma Azure](/azure/event-hubs/event-hubs-service-endpoints)attendibili.

## <a name="dependencies-for-vnet-deployment"></a>Dipendenze per la distribuzione di VNet

### <a name="network-security-groups-configuration"></a>Configurazione di gruppi di sicurezza di rete

I [gruppi di sicurezza di rete (NSG)](/azure/virtual-network/security-overview) offrono la possibilità di controllare l'accesso alla rete all'interno di una VNet. È possibile accedere al Esplora dati di Azure con due endpoint: HTTPs (443) e TDS (1433). Per consentire l'accesso a questi endpoint per la gestione, il monitoraggio e il corretto funzionamento del cluster, è necessario configurare le regole di NSG seguenti.

#### <a name="inbound-nsg-configuration"></a>Configurazione di NSG in ingresso

| **Uso**   | **From**   | **To**   | **Protocollo**   |
| --- | --- | --- | --- |
| Gestione  |[Indirizzi di gestione ADX](#azure-data-explorer-management-ip-addresses)/AzureDataExplorerManagement (ServiceTag) | Subnet ADX: 443  | TCP  |
| Monitoraggio dello stato  | [Indirizzi di monitoraggio dell'integrità di ADX](#health-monitoring-addresses)  | Subnet ADX: 443  | TCP  |
| Comunicazione interna ADX  | Subnet ADX: tutte le porte  | Subnet ADX: tutte le porte  | Tutte  |
| Consenti bilanciamento del carico di Azure in ingresso (Probe di integrità)  | AzureLoadBalancer  | Subnet ADX: 80443  | TCP  |

#### <a name="outbound-nsg-configuration"></a>Configurazione NSG in uscita

| **Uso**   | **From**   | **To**   | **Protocollo**   |
| --- | --- | --- | --- |
| Dipendenza da archiviazione di Azure  | Subnet ADX  | Archiviazione: 443  | TCP  |
| Dipendenza da Azure Data Lake  | Subnet ADX  | AzureDataLake: 443  | TCP  |
| Inserimento e monitoraggio del servizio EventHub  | Subnet ADX  | EventHub: 443, 5671  | TCP  |
| Pubblicare le metriche  | Subnet ADX  | AzureMonitor: 443 | TCP  |
| Download della configurazione di monitoraggio di Azure  | Subnet ADX  | [Indirizzi endpoint di configurazione di monitoraggio di Azure](#azure-monitor-configuration-endpoint-addresses): 443 | TCP  |
| Active Directory (se applicabile) | Subnet ADX | AzureActiveDirectory: 443 | TCP |
| Autorità di certificazione | Subnet ADX | Internet: 80 | TCP |
| Comunicazione interna  | Subnet ADX  | Subnet ADX: tutte le porte  | Tutte  |
| Porte usate per `sql\_request` e `http\_request` plug-in  | Subnet ADX  | Internet: personalizzato  | TCP  |

### <a name="relevant-ip-addresses"></a>Indirizzi IP rilevanti

#### <a name="azure-data-explorer-management-ip-addresses"></a>Indirizzi IP di gestione Esplora dati di Azure

| Region | Indirizzi |
| --- | --- |
| Australia centrale | 20.37.26.134 |
| Central2 Australia | 20.39.99.177 |
| Australia orientale | 40.82.217.84 |
| Australia sud-orientale | 20.40.161.39 |
| BrazilSouth | 191.233.25.183 |
| Canada centrale | 40.82.188.208 |
| Canada orientale | 40.80.255.12 |
| India centrale | 40.81.249.251 |
| Stati Uniti centrali | 40.67.188.68 |
| Stati Uniti centrali EUAP | 40.89.56.69 |
| Asia orientale | 20.189.74.103 |
| Stati Uniti orientali | 52.224.146.56 |
| Stati Uniti Orientali 2 | 52.232.230.201 |
| EUAP Uniti orientale | 52.253.226.110 |
| Francia centrale | 40.66.57.91 |
| Francia meridionale | 40.82.236.24 |
| Giappone orientale | 20.43.89.90 |
| Giappone occidentale | 40.81.184.86 |
| Corea centrale | 40.82.156.149 |
| Corea meridionale | 40.80.234.9 |
| Stati Uniti centro-settentrionali | 40.81.45.254 |
| Europa settentrionale | 52.142.91.221 |
| Sudafrica settentrionale | 102.133.129.138 |
| Sudafrica occidentale | 102.133.0.97 |
| Stati Uniti centro-meridionali | 20.45.3.60 |
| Asia sud-orientale | 40.119.203.252 |
| India meridionale | 40.81.72.110 |
| Regno Unito meridionale | 40.81.154.254 |
| Regno Unito occidentale | 40.81.122.39 |
| Stati Uniti centro-occidentali | 52.159.55.120 |
| Europa occidentale | 51.145.176.215 |
| India occidentale | 40.81.88.112 |
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
| Sudafrica occidentale | 104.211.224.189 |
| Stati Uniti centro-meridionali | 23.98.145.105 |
| India meridionale | 23.99.5.162 |
| Asia sud-orientale | 168.63.173.234 |
| Regno Unito meridionale | 23.97.212.5 |
| Regno Unito occidentale | 23.97.212.5 |
| Stati Uniti centro-occidentali | 168.61.212.201 |
| Europa occidentale | 23.97.212.5 |
| India occidentale | 23.99.5.162 |
| Stati Uniti occidentali | 23.99.5.162 |
| Stati Uniti occidentali 2 | 23.99.5.162 |    

#### <a name="azure-monitor-configuration-endpoint-addresses"></a>Indirizzi degli endpoint di configurazione di monitoraggio di Azure

| Region | Indirizzi |
| --- | --- |
| Australia centrale | 52.148.86.165 |
| Australia centrale 2 | 52.148.86.165 |
| Australia orientale | 52.148.86.165 |
| Australia sudorientale | 52.148.86.165 |
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
| Sudafrica occidentale | 13.71.25.187 |
| Stati Uniti centro-meridionali | 13.84.173.99 |
| India meridionale | 13.71.25.187 |
| Asia sudorientale | 52.148.86.165 |
| Regno Unito meridionale | 52.174.4.112 |
| Regno Unito occidentale | 52.169.237.246 |
| Stati Uniti centro-occidentali | 52.161.31.69 |
| Europa occidentale | 52.174.4.112 |
| India occidentale | 13.71.25.187 |
| Stati Uniti occidentali | 40.78.70.148 |
| Stati Uniti occidentali 2 | 52.151.20.103 |

## <a name="expressroute-setup"></a>Installazione di ExpressRoute

Usare ExpressRoute per connettere la rete locale alla rete virtuale di Azure. Una configurazione comune consiste nell'annunciare la route predefinita (0.0.0.0/0) attraverso la sessione di Border Gateway Protocol (BGP). In questo modo si forza il traffico proveniente dalla rete virtuale da inviare alla rete locale del cliente che può eliminare il traffico, causando interruzioni dei flussi in uscita. Per ovviare a questa impostazione predefinita, è possibile configurare [Route definito dall'utente (UDR)](/azure/virtual-network/virtual-networks-udr-overview#user-defined) (0.0.0.0/0) e l'hop successivo sarà *Internet*. Poiché il UDR ha la precedenza su BGP, il traffico sarà destinato a Internet.

## <a name="securing-outbound-traffic-with-firewall"></a>Sicurezza del traffico in uscita con il firewall

Se si vuole proteggere il traffico in uscita usando il [firewall di Azure](/azure/firewall/overview) o qualsiasi appliance virtuale per limitare i nomi di dominio, è necessario che nel firewall siano consentiti i nomi di dominio completi (FQDN) seguenti.

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

È anche necessario definire la [tabella di route](/azure/virtual-network/virtual-networks-udr-overview) nella subnet con gli indirizzi di [gestione](#azure-data-explorer-management-ip-addresses) e gli indirizzi di [monitoraggio dell'integrità](#health-monitoring-addresses) con *Internet* hop successivo per evitare problemi relativi alle route asimmetriche.

Per l'area **Stati Uniti occidentali** , ad esempio, è necessario definire i seguenti UdR:

| Name | Prefisso indirizzo | Hop successivo |
| --- | --- | --- |
| ADX_Management | 13.64.38.225/32 | Internet |
| ADX_Monitoring | 23.99.5.162/32 | Internet |

## <a name="deploy-azure-data-explorer-cluster-into-your-vnet-using-an-azure-resource-manager-template"></a>Distribuire un cluster di Azure Esplora dati in VNet usando un modello di Azure Resource Manager

Per distribuire un cluster di Azure Esplora dati nella rete virtuale, usare il [cluster azure Esplora dati nel](https://azure.microsoft.com/resources/templates/101-kusto-vnet/) modello di Azure Resource Manager di VNet.

Questo modello consente di creare il cluster, la rete virtuale, la subnet, il gruppo di sicurezza di rete e gli indirizzi IP pubblici.

## <a name="troubleshooting"></a>Risoluzione dei problemi

In questa sezione viene illustrato come risolvere i problemi di connettività, funzionamento e creazione di cluster per un cluster distribuito nella [rete virtuale](/azure/virtual-network/virtual-networks-overview).

### <a name="access-issues"></a>Problemi di accesso

Se si riscontra un problema durante l'accesso al cluster tramite l'endpoint Public (cluster.region.kusto.windows.net) o private (private-cluster.region.kusto.windows.net) e si ritiene che sia correlato alla configurazione della rete virtuale, seguire questa procedura per risolvere il problema.

#### <a name="check-tcp-connectivity"></a>Controllare la connettività TCP

Il primo passaggio consiste nel verificare la connettività TCP con il sistema operativo Windows o Linux.

# <a name="windows"></a>[Windows](#tab/windows)

   1. Scaricare [TCping](https://www.elifulkerson.com/projects/tcping.php) nel computer che si connette al cluster.
   2. Effettuare il ping della destinazione dalla macchina di origine usando il comando seguente:

    ```cmd
     C:\> tcping -t yourcluster.kusto.windows.net 443 
    
     ** Pinging continuously.  Press control-c to stop **
    
     Probing 1.2.3.4:443/tcp - Port is open - time=100.00ms
     ```

# <a name="linux"></a>[Linux](#tab/linux)

   1. Installare *netcat* nel computer che si connette al cluster

    ```bash
    $ apt-get install netcat
     ```

   2. Effettuare il ping della destinazione dalla macchina di origine usando il comando seguente:

     ```bash
     $ netcat -z -v yourcluster.kusto.windows.net 443
    
     Connection to yourcluster.kusto.windows.net 443 port [tcp/https] succeeded!
     ```
---

Se il test non riesce, procedere con i passaggi seguenti. Se il test ha esito positivo, il problema non è dovuto a un problema di connettività TCP. Per ulteriori problemi, vedere [problemi operativi](#cluster-creation-and-operations-issues) .

#### <a name="check-the-network-security-group-nsg"></a>Verificare il gruppo di sicurezza di rete (NSG)

   Verificare che il [gruppo di sicurezza di rete](/azure/virtual-network/security-overview) (NSG) collegato alla subnet del cluster disponga di una regola in ingresso che consenta l'accesso dall'IP del computer client per la porta 443.

#### <a name="check-route-table"></a>Controllare la tabella di route

   Se la subnet del cluster dispone del programma di installazione del tunneling forzato sul firewall (subnet con una [tabella di route](/azure/virtual-network/virtual-networks-udr-overview) che contiene la route predefinità 0.0.0.0/0'), assicurarsi che l'indirizzo IP del computer disponga di una route con [tipo hop successivo](/azure/virtual-network/virtual-networks-udr-overview) a virtualnetwork/Internet. Questa operazione è necessaria per evitare problemi di route asimmetrici.

### <a name="ingestion-issues"></a>Problemi di inserimento

Se si verificano problemi di inserimento e si ritiene che siano correlati alla configurazione della rete virtuale, seguire questa procedura.

#### <a name="check-ingestion-health"></a>Controllare l'integrità di inserimento

    Check that the [cluster ingestion metrics](/azure/data-explorer/using-metrics#ingestion-health-and-performance-metrics) indicate a healthy state.

#### <a name="check-security-rules-on-data-source-resources"></a>Controllare le regole di sicurezza per le risorse dell'origine dati

Se la metrica indica che non è stato elaborato alcun evento dall'origine dati (*eventi elaborati* (per gli hub eventi/Internet), verificare che le risorse dell'origine dati (hub eventi o archiviazione) consentano l'accesso dalla subnet del cluster nelle regole del firewall o negli endpoint del servizio.

#### <a name="check-security-rules-configured-on-clusters-subnet"></a>Controllare le regole di sicurezza configurate nella subnet del cluster

Verificare che la subnet del cluster disponga di regole NSG, UDR e firewall configurate correttamente. Verificare inoltre la connettività di rete per tutti gli endpoint dipendenti. 

### <a name="cluster-creation-and-operations-issues"></a>Problemi di creazione e di operazioni del cluster

Se si verificano problemi di creazione o di operazione del cluster e si ritiene che siano correlati alla configurazione della rete virtuale, attenersi alla seguente procedura per risolvere il problema.

#### <a name="diagnose-the-virtual-network-with-the-rest-api"></a>Diagnosticare la rete virtuale con l'API REST

[ARMClient](https://chocolatey.org/packages/ARMClient) viene usato per chiamare l'API REST con PowerShell. 

1. Accedere con ARMClient

   ```powerShell
   armclient login
   ```

1. Richiama operazione di diagnostica

    ```powershell
    $subscriptionId = '<subscription id>'
    $clusterName = '<name of cluster>'
    $resourceGroupName = '<resource group name>'
    $apiversion = '2019-11-09'
    
    armclient post "https://management.azure.com/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Kusto/clusters/$clusterName/diagnoseVirtualNetwork?api-version=$apiversion" -verbose
    ```

1. Controllare la risposta

    ```powershell
    HTTP/1.1 202 Accepted
    ...
    Azure-AsyncOperation: https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Kusto/locations/{location}/operationResults/{operation-id}?api-version=2019-11-09
    ...
    ```

1. Attendi il completamento dell'operazione

    ```powershell
    armclient get https://management.azure.com/subscriptions/$subscriptionId/providers/Microsoft.Kusto/locations/{location}/operationResults/{operation-id}?api-version=2019-11-09
    
    {
      "id": "/subscriptions/{subscription-id}/providers/Microsoft.Kusto/locations/{location}/operationresults/{operation-id}",
      "name": "{operation-name}",
      "status": "[Running/Failed/Completed]",
      "startTime": "{start-time}",
      "endTime": "{end-time}",
      "properties": {...}
    }
    ```
    
   Attendere il *completamento*della proprietà *status* , quindi il campo *Properties (proprietà* ) Mostra:

    ```powershell
    {
      "id": "/subscriptions/{subscription-id}/providers/Microsoft.Kusto/locations/{location}/operationresults/{operation-id}",
      "name": "{operation-name}",
      "status": "Completed",
      "startTime": "{start-time}",
      "endTime": "{end-time}",
      "properties": {
        "Findings": [...]
      }
    }
    ```

Se la proprietà *finds* Mostra un risultato vuoto, significa che tutti i test di rete sono stati superati e che non sono state interrotte connessioni. Se viene visualizzato un errore come segue: la *dipendenza in uscità {dependencyname}: {Port}' potrebbe non essere soddisfatta (in uscita)* , il cluster non può raggiungere gli endpoint di servizio dipendenti. Per risolvere i problemi, procedere con i passaggi seguenti.

#### <a name="check-network-security-group-nsg"></a>Verificare il gruppo di sicurezza di rete (NSG)

Verificare che il [gruppo di sicurezza di rete](/azure/virtual-network/security-overview) sia configurato in modo corretto in base alle istruzioni in [dipendenze per la distribuzione di VNet](/azure/data-explorer/vnet-deployment#dependencies-for-vnet-deployment)

#### <a name="check-route-table"></a>Controllare la tabella di route

Se la subnet del cluster dispone del tunneling forzato impostato su Firewall (subnet con una [tabella di route](/azure/virtual-network/virtual-networks-udr-overview) che contiene la route predefinità 0.0.0.0/0'), verificare che gli indirizzi IP di [gestione](#azure-data-explorer-management-ip-addresses) e il [monitoraggio dell'integrità](#health-monitoring-addresses) abbiano una route con [tipo di hop successivo](/azure/virtual-network/virtual-networks-udr-overview##next-hop-types-across-azure-tools) *Internet*e [prefisso dell'indirizzo di origine](/azure/virtual-network/virtual-networks-udr-overview#how-azure-selects-a-route) su *' Management-IP/32'* e *' Health-Monitoring-IP/32'* Questa operazione è necessaria per evitare problemi di route asimmetrici.

#### <a name="check-firewall-rules"></a>Controllare le regole del firewall

Se si forza il traffico in uscita della subnet del tunnel verso un firewall, assicurarsi che tutte le dipendenze FQDN (ad esempio, *. blob.Core.Windows.NET*) siano consentite nella configurazione del firewall, come descritto in [protezione del traffico in uscita con il firewall](/azure/data-explorer/vnet-deployment#securing-outbound-traffic-with-firewall).