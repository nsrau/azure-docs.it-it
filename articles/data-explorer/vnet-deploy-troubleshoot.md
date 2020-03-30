---
title: Risolvere i problemi di accesso, inserimento e funzionamento del cluster Di Azure Data Explorer nella rete virtualeTroubleshoot access, ingestion, and operation of your Azure Data Explorer cluster in your virtual network
description: Risolvere i problemi di connettività, inserimento, creazione di cluster e funzionamento del cluster Azure Data Explorer nella rete virtualeTroubleshoot connectivity, ingestion, cluster creation, and operation of your Azure Data Explorer cluster in your virtual network
author: basaba
ms.author: basaba
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 03/24/2020
ms.openlocfilehash: b50b971a3b1980ad35a1a939bdf25f1c9e6ac7ba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80241660"
---
# <a name="troubleshoot-access-ingestion-and-operation-of-your-azure-data-explorer-cluster-in-your-virtual-network"></a>Risolvere i problemi di accesso, inserimento e funzionamento del cluster Di Azure Data Explorer nella rete virtualeTroubleshoot access, ingestion, and operation of your Azure Data Explorer cluster in your virtual network

In questa sezione viene illustrato come risolvere i problemi di connettività, operativi e di creazione di cluster per un cluster distribuito nella [rete virtuale.](/azure/virtual-network/virtual-networks-overview)

## <a name="access-issues"></a>Problemi di accesso

Se si verifica un problema durante l'accesso al cluster utilizzando l'endpoint pubblico (cluster.region.kusto.windows.net) o privato (private-cluster.region.kusto.windows.net) e si sospetta che sia correlato alla configurazione della rete virtuale, eseguire la procedura seguente per risolvere il problema.

### <a name="check-tcp-connectivity"></a>Controllare la connettività TCP

Il primo passaggio include il controllo della connettività TCP tramite Windows o il sistema operativo Linux.

# <a name="windows"></a>[Windows](#tab/windows)

   1. Scaricare [TCping](https://www.elifulkerson.com/projects/tcping.php) nel computer che si connette al cluster.
   2. Eseguire il ping della destinazione dal computer di origine utilizzando il comando seguente:

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

   2. Eseguire il ping della destinazione dal computer di origine utilizzando il comando seguente:

     ```bash
     $ netcat -z -v yourcluster.kusto.windows.net 443
    
     Connection to yourcluster.kusto.windows.net 443 port [tcp/https] succeeded!
     ```
---

Se il test non ha esito positivo, procedere come segue. Se il test ha esito positivo, il problema non è dovuto a un problema di connettività TCP. Passare a [problemi operativi](#cluster-creation-and-operations-issues) per risolvere ulteriormente.

### <a name="check-the-network-security-group-nsg"></a>Controllare il gruppo di sicurezza di rete (NSG)

   Verificare che il gruppo di sicurezza di [rete](/azure/virtual-network/security-overview) (NSG) collegato alla subnet del cluster disponga di una regola in ingresso che consente l'accesso dall'IP del computer client per la porta 443.

### <a name="check-route-table"></a>Controllare la tabella del percorso

   Se la subnet del cluster dispone di configurazione del tunneling forzato al firewall (subnet con una tabella di [route](/azure/virtual-network/virtual-networks-udr-overview) che contiene la route predefinita '0.0.0.0/0'), assicurarsi che l'indirizzo IP del computer abbia una route con tipo di [hop successivo](/azure/virtual-network/virtual-networks-udr-overview) a VirtualNetwork/Internet. Questo percorso è necessario per evitare problemi di percorso asimmetrico.

## <a name="ingestion-issues"></a>Problemi di ingestione

Se si verificano problemi di inserimento e si sospetta che siano correlati alla configurazione della rete virtuale, eseguire la procedura seguente.

### <a name="check-ingestion-health"></a>Controllare l'integrità dell'inserimento

Verificare che le metriche di [inserimento](/azure/data-explorer/using-metrics#ingestion-health-and-performance-metrics) del cluster indichino uno stato integro.

### <a name="check-security-rules-on-data-source-resources"></a>Controllare le regole di sicurezza nelle risorse dell'origine datiCheck security rules on data source resources

Se le metriche indicano che non sono stati elaborati eventi dall'origine dati (metrica*Eventi elaborati* per hub event/IoT), assicurarsi che le risorse dell'origine dati (Hub eventi o Archiviazione) consentano l'accesso dalla subnet del cluster nelle regole del firewall o negli endpoint del servizio.

### <a name="check-security-rules-configured-on-clusters-subnet"></a>Controllare le regole di sicurezza configurate nella subnet del clusterCheck security rules configured on cluster's subnet

Assicurarsi che la subnet del cluster disponga di NSG, UDR e che le regole del firewall siano configurate correttamente. Inoltre, testare la connettività di rete per tutti gli endpoint dipendenti. 

## <a name="cluster-creation-and-operations-issues"></a>Problemi relativi alla creazione e alle operazioni dei cluster

Se si verificano problemi di creazione o funzionamento del cluster e si sospetta che sia correlato alla configurazione della rete virtuale, attenersi alla seguente procedura per risolvere il problema.

### <a name="diagnose-the-virtual-network-with-the-rest-api"></a>Diagnosticare la rete virtuale con l'API RESTDiagnose the virtual network with the REST API

ARMClient viene usato per chiamare l'API REST tramite PowerShell.The [ARMClient](https://chocolatey.org/packages/ARMClient) is used to call the REST API using PowerShell. 

1. Accedere con ARMClient

   ```powerShell
   armclient login
   ```

1. Richiamare l'operazione di diagnosiInvoke diagnose operation

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

1. Attendere il completamento dell'operazione

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
    
   Attendere che la proprietà *status* sia *Completata*, quindi il campo *delle proprietà* dovrebbe mostrare:

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

Se la proprietà *Findings* mostra un risultato vuoto, significa che tutti i test di rete sono stati superati e nessuna connessione viene interrotta. Se viene visualizzato l'errore seguente, la *dipendenza in uscita ''dependencyName':'port' potrebbe non essere soddisfatta (in uscita)*, il cluster non può raggiungere gli endpoint del servizio dipendenti. Procedere con i passaggi seguenti.

### <a name="check-network-security-group-nsg"></a>Controllare il gruppo di sicurezza di rete (NSG)Check Network Security Group (NSG)

Assicurarsi che il gruppo di sicurezza di [rete](/azure/virtual-network/security-overview) sia configurato correttamente in base alle istruzioni in [Dipendenze per](/azure/data-explorer/vnet-deployment#dependencies-for-vnet-deployment) la distribuzione della rete virtuale

### <a name="check-route-table"></a>Controllare la tabella del percorso

Se nella subnet del cluster è impostato il tunneling forzato su firewall (subnet con una [tabella](/azure/virtual-network/virtual-networks-udr-overview) di route contenente la route predefinita '0.0.0.0/0') assicurarsi che gli [indirizzi IP](vnet-deployment.md#azure-data-explorer-management-ip-addresses)di gestione ) e gli indirizzi IP di monitoraggio [dell'integrità](vnet-deployment.md#health-monitoring-addresses) dispongano di una route con tipo di [hop successivo](/azure/virtual-network/virtual-networks-udr-overview##next-hop-types-across-azure-tools) *Internet*e un prefisso dell'indirizzo [di origine](/azure/virtual-network/virtual-networks-udr-overview#how-azure-selects-a-route) a *'management-ip/32'* e *'health-monitoring-ip/32'.* Questa route è necessaria per evitare problemi di route asimmetriche.

### <a name="check-firewall-rules"></a>Controllare le regole del firewall

Se si forza il tunneling del traffico in uscita della subnet verso un firewall, assicurarsi che tutte le dipendenze FQDN (ad esempio, *.blob.core.windows.net*) siano consentite nella configurazione del firewall come descritto in [protezione del traffico in uscita con il firewall](/azure/data-explorer/vnet-deployment#securing-outbound-traffic-with-firewall).
