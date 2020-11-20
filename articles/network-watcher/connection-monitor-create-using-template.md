---
title: Crea monitoraggio connessione (anteprima)-modello ARM
titleSuffix: Azure Network Watcher
description: Informazioni su come creare un monitoraggio della connessione tramite ARMClient.
services: network-watcher
documentationcenter: na
author: vinigam
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/30/2020
ms.author: vinigam
ms.openlocfilehash: e1a0ecd85d07aa4fdf5482c5104c17eb4a068688
ms.sourcegitcommit: 9889a3983b88222c30275fd0cfe60807976fd65b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2020
ms.locfileid: "94984227"
---
# <a name="create-a-connection-monitor-using-the-arm-template"></a>Creare un monitoraggio della connessione usando il modello ARM

Informazioni su come creare un monitoraggio della connessione per monitorare la comunicazione tra le risorse usando ARMClient. Supporta le distribuzioni ibride e cloud di Azure.

> [!IMPORTANT]
> Il monitoraggio connessione è attualmente disponibile in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="before-you-begin"></a>Prima di iniziare 

Nei monitoraggi connessione creati in monitoraggio connessione è possibile aggiungere sia i computer locali sia le VM di Azure come origini. Questi monitoraggi di connessione possono inoltre monitorare la connettività agli endpoint. Gli endpoint possono trovarsi in Azure o in qualsiasi altro URL o indirizzo IP.

Il monitoraggio della connessione include le seguenti entità:

* **Risorsa di monitoraggio della connessione** : una risorsa di Azure specifica dell'area. Tutte le entità seguenti sono proprietà di una risorsa di monitoraggio della connessione.
* **Endpoint** : un'origine o una destinazione che partecipa ai controlli di connettività. Esempi di endpoint includono macchine virtuali di Azure, agenti locali, URL e indirizzi IP.
* **Configurazione di test** : una configurazione specifica del protocollo per un test. In base al protocollo scelto, è possibile definire la porta, le soglie, la frequenza di test e altri parametri.
* **Gruppo di test** : il gruppo che contiene gli endpoint di origine, gli endpoint di destinazione e le configurazioni di test. Un monitoraggio connessione può contenere più di un gruppo di test.
* **Test** : la combinazione di un endpoint di origine, un endpoint di destinazione e una configurazione di test. Un test è il livello più granulare in cui sono disponibili i dati di monitoraggio. I dati di monitoraggio includono la percentuale di controlli non riusciti e il tempo di round trip (RTT).

    ![Diagramma che mostra un monitoraggio della connessione, che definisce la relazione tra i gruppi di test e i test](./media/connection-monitor-2-preview/cm-tg-2.png)

## <a name="steps-to-create-with-sample-arm-template"></a>Passaggi da creare con il modello ARM di esempio

Usare il codice seguente per creare un monitoraggio della connessione tramite ARMClient.

```armclient
$connectionMonitorName = "sampleConnectionMonitor"

$ARM = "https://management.azure.com"

$SUB = "subscriptions/<subscription id 1>;"

$NW = "resourceGroups/NetworkWatcherRG/providers/Microsoft.Network/networkWatchers/NetworkWatcher\_<region>"

$body =

"{

location: '<region>',

properties: {

endpoints: [{

name: 'endpoint_workspace_machine',

type: 'MMAWorkspaceMachine',

resourceId: '/subscriptions/<subscription id>/resourcegroups/<resource group>/providers/Microsoft.OperationalInsights/workspaces/sampleWorkspace',

//Example 1: Choose a machine

address : '<non-Azure machine FQDN>'
}

//Example 2: Select IP from chosen machines

address : '<non-Azure machine FQDN>

"scope": {
      "include": [
            {
                  "address": "<IP belonging to machine chosen above>"  
        }
       ]
      }
   }    
   
name: 'endpoint_workspace_network',

type: 'MMAWorkspaceNetwork',

resourceId: '/subscriptions/<subscription id>/resourcegroups/<resource group>/providers/Microsoft.OperationalInsights/workspaces/sampleWorkspace',

 coverage level : 'high', //Optional
 
 //Include subnets. You can also exclude IPs from subnet to exclude from monitoring
 
 scope: {
      "include": [
            {
                  "address": "<subnet 1 mask>" // Eg: 10.10.1.0/28
            },
            {
                  "address": "<subnet 2 mask>" 
            }
      ],
      "exclude": [
            { 
            "address" : "<ip-from-included-subnets-that-should-be-excluded>"
        }
      ]
     }
},

//Use a Azure VM as an endpoint
{

name: 'endpoint_virtualmachine',

resourceId: '/subscriptions/<subscription id>/resourceGroups/<resource group>/providers/Microsoft.Compute/virtualMachines/<vm-name>'

},

//Use an Azure VNET or Subnet as an endpoint

 {

name: 'endpoint_vnet_subnet',

resourceId: '<resource id of VNET or subnet'
coverage level: 'high' //Optional

//Scope is optional.

  "scope": {
      "include": [
            {
                  "address": "<subnet 1 mask>" // Eg: 10.10.1.0/28 .This subnet should match with any existing subnet in vnet
            }
      ],
    "exclude": [
            {
                  "address": "<ip-from-included-subnets-that-should-be-excluded>" // If used with include, IP should be part of the subnet defined above. Without include, this could be any address within vnet range or any specific subnet range as a whole.
            }
      ]
  }
   },

//Endpoint as a URL
{

name: 'azure portal'

address: '<URL>'

   },

//Endpoint as an IP 
 {

    name: 'ip',

     address: '<IP>'

 }

  ],

  testGroups: [{

    name: 'Connectivity to Azure Portal and Public IP',

    testConfigurations: ['http', 'https', 'tcpEnabled', 'icmpEnabled'],

    sources: ['vm1', 'workspace'],

    destinations: ['azure portal', 'ip']

   },

{

    name: 'Connectivty from Azure VM 1 to Azure VM 2',

   // Choose your protocol
   
    testConfigurations: ['http', 'https', 'tcpDisabled', 'icmpDisabled'],

    sources: ['vm1'],

    destinations: ['vm2'],

    disable: true

   }

  ],

  testConfigurations: [{

    name: 'http',

    testFrequencySec: <frequency>,

    protocol: 'HTTP',

    successThreshold: {

     checksFailedPercent: <threshold for checks failed %>,

     roundTripTimeMs: <threshold for RTT>

    }

   }, {

    name: 'https',

    testFrequencySec: <frequency>,

    protocol: 'HTTP',

    httpConfiguration: {
    
     port: '<port of choice>'
  
    preferHTTPS: true // If port chosen is not 80 or 443
    
    method: 'GET', //Choose GET or POST
    
    path: '/', //Specify path for request
         
    requestHeaders: [
            {
              "name": "Content-Type",
              "value": "appication/json"
            }
          ],
          
    validStatusCodeRanges: [ "102", "200-202", "3xx" ], //Samples
          
    },

    successThreshold: {

     checksFailedPercent: <choose your checks failed threshold>,

     roundTripTimeMs: <choose your RTT threshold>

    }

   }, 
   {

    name: 'tcpEnabled',

    testFrequencySec: <frequency>,

    protocol: 'TCP',

    tcpConfiguration: {

     port: 80

    },

    successThreshold: {

     checksFailedPercent: <choose your checks failed threshold>,

     roundTripTimeMs: <choose your RTT threshold>

    }

   }, {

    name: 'icmpEnabled',

    testFrequencySec: <frequency>,

    protocol: 'ICMP',

    successThreshold: {

     checksFailedPercent: <choose your checks failed threshold>,

     roundTripTimeMs: <choose your RTT threshold>

    }

   }, {

    name: 'icmpDisabled',

    testFrequencySec: <frequency>,

    protocol: 'ICMP',

    icmpConfiguration: {

     disableTraceRoute: true

    },

    successThreshold: {

     checksFailedPercent: <choose your checks failed threshold>,

     roundTripTimeMs: <choose your RTT threshold>

    }

   }, {

    name: 'tcpDisabled',

    testFrequencySec: <frequency>,

    protocol: 'TCP',

    tcpConfiguration: {

     port: 80,

     disableTraceRoute: true

    },

    successThreshold: {

     checksFailedPercent: <choose your checks failed threshold>,

     roundTripTimeMs: <choose your RTT threshold>

    }

   }

  ]

 }

} "
```

Ecco il comando di distribuzione:
```
armclient PUT $ARM/$SUB/$NW/connectionMonitors/$connectionMonitorName/?api-version=2019-07-01 $body -verbose
```

## <a name="description-of-properties"></a>Descrizione delle proprietà

* connectionMonitorName-nome della risorsa di monitoraggio della connessione

* ID sottoscrizione secondaria della sottoscrizione in cui si vuole creare il monitoraggio della connessione

* NW-Network Watcher ID di risorsa in cui verrà creato CM 

* location-area in cui verrà creato il monitoraggio della connessione

* Endpoint
    * Nome: nome univoco per ogni endpoint
    * resourceId: per gli endpoint di Azure, l'ID risorsa fa riferimento all'ID risorsa Azure Resource Manager per le macchine virtuali. Per gli endpoint non di Azure, l'ID risorsa fa riferimento all'ID di risorsa Azure Resource Manager per l'area di lavoro Log Analytics collegata a agenti non Azure.
    * Address: applicabile solo quando l'ID risorsa non è specificato o se l'ID risorsa è Log Analytics area di lavoro. Se utilizzata con Log Analytics ID risorsa, si riferisce al nome di dominio completo dell'agente che può essere utilizzato per il monitoraggio. Se usato senza ID risorsa, può corrispondere all'URL o all'indirizzo IP di qualsiasi endpoint pubblico.
    * Filter: per gli endpoint non di Azure, usare Filter per selezionare gli agenti dall'area di lavoro Log Analytics che verrà usata per il monitoraggio nella risorsa di monitoraggio della connessione. Se i filtri non sono impostati, tutti gli agenti appartenenti all'area di lavoro Log Analytics possono essere utilizzati per il monitoraggio
        * Tipo: impostare il tipo come "Indirizzo agente"
        * Address: impostare Address come nome di dominio completo dell'agente locale

* Gruppi di test
    * Nome: assegnare un nome al gruppo di test.
    * testConfigurations: configurazioni di test in base alle quali gli endpoint di origine si connettono agli endpoint di destinazione
    * origini: scegliere dagli endpoint creati in precedenza. Per gli endpoint di origine basati su Azure è necessario che sia installata l'estensione di Azure Network Watcher e che gli endpoint di origine non basati su Azure debbano haveAzure Log Analytics agente installato. Per installare un agente per l'origine, vedere [Install Monitoring Agents](./connection-monitor-overview.md#install-monitoring-agents).
    * destinazioni: scegliere tra gli endpoint creati in precedenza. È possibile monitorare la connettività alle macchine virtuali di Azure o a qualsiasi endpoint (un indirizzo IP pubblico, un URL o un nome di dominio completo) specificandone tali elementi come destinazioni. In un singolo gruppo di test è possibile aggiungere macchine virtuali di Azure, URL di Office 365, URL di Dynamics 365 ed endpoint personalizzati.
    * Disable: utilizzare questo campo per disabilitare il monitoraggio per tutte le origini e le destinazioni specificate dal gruppo di test.

* Configurazioni di test
    * Nome: nome della configurazione di test.
    * testFrequencySec: specificare la frequenza con cui le origini effettueranno il ping delle destinazioni sul protocollo e sulla porta specificati. È possibile scegliere 30 secondi, 1 minuto, 5 minuti, 15 minuti o 30 minuti. Le origini proveranno la connettività alle destinazioni in base al valore scelto. Se ad esempio si seleziona 30 secondi, le origini verificheranno la connettività alla destinazione almeno una volta in un periodo di 30 secondi.
    * protocollo: è possibile scegliere TCP, ICMP, HTTP o HTTPS. A seconda del protocollo, è possibile eseguire alcune configurazioni specifiche del protocollo
    
        * preferHTTPS-specificare se usare HTTPS su HTTP, quando la porta usata non è né 80 né 443
        * porta: specificare la porta di destinazione scelta.
        * disableTraceRoute: si applica alle configurazioni di test il cui protocollo è TCP o ICMP. Arresta le origini dall'individuazione della topologia e della RTT hop-by-hop.
        * Method (metodo) applicato alle configurazioni di test il cui protocollo è HTTP. Selezionare il metodo di richiesta HTTP, ovvero GET o POST
        * percorso: specificare i parametri del percorso da accodare all'URL
        * validStatusCodes-scegliere i codici di stato applicabili. Se il codice di risposta non corrisponde a questo elenco, verrà ricevuto un messaggio di diagnostica
        * requestHeaders-specificare le stringhe di intestazione della richiesta personalizzate che verranno passate alla destinazione
        
    * successThreshold: è possibile impostare le soglie nei parametri di rete seguenti:
        * checksFailedPercent: consente di impostare la percentuale di verifiche che possono avere esito negativo quando le origini controllano la connettività alle destinazioni usando i criteri specificati. Per il protocollo TCP o ICMP, la percentuale di controlli non riusciti può essere equivalente alla percentuale di perdita di pacchetti. Per il protocollo HTTP, questo campo rappresenta la percentuale di richieste HTTP che non hanno ricevuto risposta.
        * roundTripTimeMs: consente di impostare RTT in millisecondi per il tempo necessario per la connessione alla destinazione tramite la configurazione di test.

## <a name="scale-limits"></a> Limiti di scalabilità

I monitoraggi connessione presentano i limiti di scalabilità seguenti:

* Numero massimo di monitoraggi di connessione per ogni sottoscrizione per area: 100
* Numero massimo di gruppi di test per connessione: 20
* Numero massimo di origini e destinazioni per monitoraggio connessione: 100
* Numero massimo di configurazioni di test per ogni monitoraggio della connessione: 20 tramite ARMClient

## <a name="next-steps"></a>Passaggi successivi

* Informazioni [su come analizzare i dati di monitoraggio e impostare gli avvisi](./connection-monitor-overview.md#analyze-monitoring-data-and-set-alerts)
* Informazioni [su come diagnosticare i problemi nella rete](./connection-monitor-overview.md#diagnose-issues-in-your-network)