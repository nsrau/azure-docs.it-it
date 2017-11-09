---
title: Monitorare operazioni, eventi e contatori per i gruppi di sicurezza di rete | Documentazione Microsoft
description: Informazioni su come abilitare la registrazione di contatori, eventi e operazioni per i gruppi di sicurezza di rete
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 2e699078-043f-48bd-8aa8-b011a32d98ca
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/31/2017
ms.author: jdial
ms.openlocfilehash: 552f37dd704de25159bc0f0ad34fdae9ed8b73f5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="log-analytics-for-network-security-groups-nsgs"></a>Analisi dei log per i gruppi di sicurezza di rete

È possibile abilitare le seguenti categorie di log di diagnostica per i gruppi di sicurezza di rete:

* **Evento:** contiene voci per cui le regole dei gruppi di sicurezza di rete sono applicate alle macchine virtuali e ai ruoli delle istanze in base all'indirizzo MAC. Lo stato di queste regole viene raccolto ogni 60 secondi.
* **Contenitore di regole:** contiene voci per sapere quante volte ogni regola dei gruppi di sicurezza di rete è stata applicata per rifiutare o consentire il traffico.

> [!NOTE]
> I log di diagnostica sono disponibili solo per i gruppi di sicurezza di rete distribuiti nel modello di distribuzione di Azure Resource Manager. Non è possibile abilitare la registrazione diagnostica per i gruppi di sicurezza di rete distribuiti tramite il modello di distribuzione classico. Per altre informazioni sui due modelli, vedere l'articolo [Comprendere i modelli di distribuzione di Azure](../resource-manager-deployment-model.md).

La registrazione delle attività (precedentemente nota come controllo o registri operativi) è abilitata per impostazione predefinita per i gruppi di sicurezza di rete creati tramite qualsivoglia modello di distribuzione di Azure. Per determinare quali operazioni sono state completate nei NGS nel log attività, cercare le voci che contengono i tipi di risorsa seguenti: 

- Microsoft.ClassicNetwork/networkSecurityGroups 
- Microsoft.ClassicNetwork/networkSecurityGroups/securityRules
- Microsoft.Network/networkSecurityGroups
- Microsoft.Network/networkSecurityGroups/securityRules 

Leggere l'articolo [Panoramica del log attività di Azure](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md) per ulteriori informazioni sui log attività. 

## <a name="enable-diagnostic-logging"></a>Abilitare la registrazione diagnostica

Deve essere abilitata la registrazione diagnostica per *ogni* gruppo di sicurezza di rete da cui si vogliono raccogliere dati. L'articolo [Panoramica dei log di diagnostica di Azure](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md) spiega dove è possibile inviare i log di diagnostica. Se non si dispone di un gruppo di sicurezza di rete esistente, completare i passaggi descritti nell'articolo [Creare un gruppo di sicurezza di rete](virtual-networks-create-nsg-arm-pportal.md) per crearne uno. È possibile abilitare la registrazione diagnostica nei gruppi di sicurezza direte usando uno dei metodi seguenti:

### <a name="azure-portal"></a>Portale di Azure

Per usare li portale per abilitare la registrazione, accedere al [portale](https://portal.azure.com). Fare clic su **Altri servizi**, quindi digitare *gruppi di sicurezza di rete*. Selezionare il gruppo di sicurezza di rete per cui si vuole abilitare l'accesso. Seguire le istruzioni per le risorse non di calcolo nell'articolo [Abilitare i log di diagnostica nel portale](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md#how-to-enable-collection-of-resource-diagnostic-logs). Selezionare **NetworkSecurityGroupEvent**, **NetworkSecurityGroupRuleCounter** o entrambe le categorie di log.

### <a name="powershell"></a>PowerShell

Per abilitare la registrazione con PowerShell, seguire le istruzioni nell'articolo [Abilitare i log di diagnostica tramite PowerShell](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md#how-to-enable-collection-of-resource-diagnostic-logs). Valutare le seguenti informazioni prima di immettere un comando dall'articolo:

- È possibile determinare il valore da usare per il parametro `-ResourceId` sostituendo in base alle necessità il [testo] seguente e quindi immettendo il comando `Get-AzureRmNetworkSecurityGroup -Name [nsg-name] -ResourceGroupName [resource-group-name]`. L'output dell'ID dal comando è simile a */subscriptions/[ID sottoscrizione]/resourceGroups/[Gruppo di risorse]/providers/Microsoft.Network/networkSecurityGroups/[Nome gruppo di sicurezza di rete]*.
- Se si desidera solamente raccogliere i dati dalla categoria di log, aggiungere `-Categories [category]` alla fine del comando nell'articolo, dove la categoria è *NetworkSecurityGroupEvent* o *NetworkSecurityGroupRuleCounter*. Se non si usa il parametro `-Categories`, la raccolta dei dati viene abilitata per entrambe le categorie di log.

### <a name="azure-command-line-interface-cli"></a>interfaccia della riga di comando di Azure (CLI)

Per abilitare la registrazione con l'interfaccia della riga di comando, seguire le istruzioni nell'articolo [Abilitare i log di diagnostica tramite l'interfaccia della riga di comando](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md#how-to-enable-collection-of-resource-diagnostic-logs). Valutare le seguenti informazioni prima di immettere un comando dall'articolo:

- È possibile determinare il valore da usare per il parametro `-ResourceId` sostituendo in base alle necessità il [testo] seguente e quindi immettendo il comando `azure network nsg show [resource-group-name] [nsg-name]`. L'output dell'ID dal comando è simile a */subscriptions/[ID sottoscrizione]/resourceGroups/[Gruppo di risorse]/providers/Microsoft.Network/networkSecurityGroups/[Nome gruppo di sicurezza di rete]*.
- Se si desidera solamente raccogliere i dati dalla categoria di log, aggiungere `-Categories [category]` alla fine del comando nell'articolo, dove la categoria è *NetworkSecurityGroupEvent* o *NetworkSecurityGroupRuleCounter*. Se non si usa il parametro `-Categories`, la raccolta dei dati viene abilitata per entrambe le categorie di log.

## <a name="logged-data"></a>Dati registrati

Vengono scritti dati in formato JSON per entrambi i log. I dati specifici scritti per ciascun tipo di log sono elencati nelle sezioni seguenti:

### <a name="event-log"></a>Registro eventi
Questo registro contiene informazioni su quali regole del gruppo di sicurezza di rete vengono applicate alle macchine virtuali e alle istanze del ruolo del servizio cloud, in base all'indirizzo MAC. I dati nell'esempio seguente vengono registrati per ogni evento:

```json
{
    "time": "[DATE-TIME]",
    "systemId": "007d0441-5d6b-41f6-8bfd-930db640ec03",
    "category": "NetworkSecurityGroupEvent",
    "resourceId": "/SUBSCRIPTIONS/[SUBSCRIPTION-ID]/RESOURCEGROUPS/[RESOURCE-GROUP-NAME]/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/[NSG-NAME]",
    "operationName": "NetworkSecurityGroupEvents",
    "properties": {
        "vnetResourceGuid":"{5E8AEC16-C728-441F-B0CA-B791E1DBC2F4}",
        "subnetPrefix":"192.168.1.0/24",
        "macAddress":"00-0D-3A-92-6A-7C",
        "primaryIPv4Address":"192.168.1.4",
        "ruleName":"UserRule_default-allow-rdp",
        "direction":"In",
        "priority":1000,
        "type":"allow",
        "conditions":{
            "protocols":"6",
            "destinationPortRange":"3389-3389",
            "sourcePortRange":"0-65535",
            "sourceIP":"0.0.0.0/0",
            "destinationIP":"0.0.0.0/0"
            }
        }
}
```

### <a name="rule-counter-log"></a>Log contatore regole

Questo log contiene informazioni su ogni regola applicata alle risorse. I dati nell'esempio seguente vengono registrati ogni volta che viene applicata una regola:

```json
{
    "time": "[DATE-TIME]",
    "systemId": "007d0441-5d6b-41f6-8bfd-930db640ec03",
    "category": "NetworkSecurityGroupRuleCounter",
    "resourceId": "/SUBSCRIPTIONS/[SUBSCRIPTION ID]/RESOURCEGROUPS/[RESOURCE-GROUP-NAME]TESTRG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/[NSG-NAME]",
    "operationName": "NetworkSecurityGroupCounters",
    "properties": {
        "vnetResourceGuid":"{5E8AEC16-C728-441F-B0CA-791E1DBC2F4}",
        "subnetPrefix":"192.168.1.0/24",
        "macAddress":"00-0D-3A-92-6A-7C",
        "primaryIPv4Address":"192.168.1.4",
        "ruleName":"UserRule_default-allow-rdp",
        "direction":"In",
        "type":"allow",
        "matchedConnections":125
        }
}
```

## <a name="view-and-analyze-logs"></a>Visualizzare e analizzare i log

Leggere l'articolo [Panoramica del log attività di Azure](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md) per ulteriori informazioni su come visualizzare i dati del log attività. Leggere l'articolo [Panoramica dei log di diagnostica di Azure](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md) per ulteriori informazioni su come visualizzare i dati dei log di diagnostica. Se si inviano dati di diagnostica a Log Analytics, è possibile usare la soluzione di gestione [Analisi di gruppo di sicurezza di rete di Azure](../log-analytics/log-analytics-azure-networking-analytics.md) (anteprima) per approfondimenti avanzati. 
