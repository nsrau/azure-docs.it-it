---
title: Introduzione alla registrazione dei flussi per i gruppi di sicurezza di rete con Network Watcher | Microsoft Docs
description: "Questa pagina illustra come usare i log dei flussi dei gruppi di sicurezza di rete, una funzionalità di Azure Network Watcher"
services: network-watcher
documentationcenter: na
author: georgewallace
manager: timlt
editor: 
ms.assetid: 47d91341-16f1-45ac-85a5-e5a640f5d59e
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: 318d141d7769d063555c394ada91a2714be77f64
ms.openlocfilehash: 6635079e568634285e0ffeb1c2334d66db3cc633
ms.lasthandoff: 02/22/2017


---

# <a name="introduction-to-flow-logging-for-network-security-groups"></a>Introduzione alla registrazione dei flussi per i gruppi di sicurezza di rete

I log di flusso del gruppo di sicurezza di rete sono una funzionalità di Network Watcher che consente di visualizzare le informazioni sul traffico IP in entrata e in uscita tramite un gruppo di sicurezza di rete. Sono scritti in formato JSON e mostrano i flussi in ingresso e in uscita in base a regole, scheda di rete a cui si applica il flusso, informazioni su 5 tuple relative al flusso (IP di origine/destinazione, porta di origine/destinazione, protocollo), e se il traffico è consentito o meno.

![Panoramica dei log dei flussi][1]

Anche se i log dei flussi specificano come destinazione gruppi di sicurezza di rete, non vengono visualizzati come gli altri log. I log dei flussi vengono archiviati solo in un account di archiviazione e hanno un percorso di registrazione come quello dell'esempio seguente.

```
https://{storageAccountName}.blob.core.windows.net/insights-logs-networksecuritygroupflowevent/resourceId%3D/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/microsoft.network/networksecuritygroups/{nsgName}/{year}/{month}/{day}/PT1H.json
```

Ai log dei flussi si applicano gli stessi criteri di conservazione degli altri log. Il criterio di conservazione dei log può essere impostato su un valore compreso tra 1 giorno e 365 giorni. Se non viene impostato alcun criterio di conservazione, i log vengono conservati per sempre.

## <a name="log-file"></a>File di log

I log dei flussi hanno più proprietà. L'elenco seguente indica le proprietà restituite nel log del flusso del gruppo di sicurezza di rete:

* **time**: ora in cui l'evento è stato registrato.
* **systemId**: ID risorsa del gruppo di sicurezza di rete.
* **category**: categoria dell'evento, che è sempre NetworkSecurityGroupFlowEvent.
* **resourceid**: ID risorsa del gruppo di sicurezza di rete.
* **operationName**: sempre NetworkSecurityGroupFlowEvents.
* **properties**: raccolta di proprietà del flusso.
    * **Version**: numero di versione dello schema di eventi del log dei flussi.
    * **flows**: raccolta di flussi. Questa proprietà ha più voci per regole diverse.
        * **rule**: regola per cui vengono elencati i flussi.
            * **flows**: raccolta di flussi.
                * **mac**: indirizzo MAC della scheda di interfaccia di rete per la VM in cui è stato raccolto il flusso.
                * **flowTuples**: stringa che contiene più proprietà per la tupla del flusso nel formato con valori separati da virgole.
                    * **Time Stamp**: questo valore è il timestamp di quando si è verificato il flusso in formato UNIX EPOCH.
                    * **Source IP**: IP di origine.
                    * **Destination IP**: IP di destinazione.
                    * **Source Port**: porta di origine.
                    * **Destination Port**: porta di destinazione.
                    * **Protocol**: protocollo del flusso. I valori validi sono **T** per TCP e **U** per UDP.
                    * **Traffic Flow**: direzione del flusso del traffico. I valori validi sono **I** per traffico in ingresso e **O** per il traffico in uscita.
                    * **Traffic**: indica se il traffico è stato consentito o negato. I valori validi sono **A** per il traffico consentito e **D** per il traffico negato.


Di seguito è riportato un esempio di log dei flussi. Come si può osservare, più record seguono l'elenco di proprietà descritto nella sezione precedente. 

> [!NOTE]
> I valori della proprietà flowTuples sono un elenco delimitato da virgole.
 
```json
{
    "records": 
    [
        
        {
             "time": "2017-02-16T22:00:32.8950000Z",
             "systemId": "2c002c16-72f3-4dc5-b391-3444c3527434",
             "category": "NetworkSecurityGroupFlowEvent",
             "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/FABRIKAMRG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/FABRIAKMVM1-NSG",
             "operationName": "NetworkSecurityGroupFlowEvents",
             "properties": {"Version":1,"flows":[{"rule":"DefaultRule_DenyAllInBound","flows":[{"mac":"000D3AF8801A","flowTuples":["1487282421,42.119.146.95,10.1.0.4,51529,5358,T,I,D"]}]},{"rule":"UserRule_default-allow-rdp","flows":[{"mac":"000D3AF8801A","flowTuples":["1487282370,163.28.66.17,10.1.0.4,61771,3389,T,I,A","1487282393,5.39.218.34,10.1.0.4,58596,3389,T,I,A","1487282393,91.224.160.154,10.1.0.4,61540,3389,T,I,A","1487282423,13.76.89.229,10.1.0.4,53163,3389,T,I,A"]}]}]}
        }
        ,
        {
             "time": "2017-02-16T22:01:32.8960000Z",
             "systemId": "2c002c16-72f3-4dc5-b391-3444c3527434",
             "category": "NetworkSecurityGroupFlowEvent",
             "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/FABRIKAMRG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/FABRIAKMVM1-NSG",
             "operationName": "NetworkSecurityGroupFlowEvents",
             "properties": {"Version":1,"flows":[{"rule":"DefaultRule_DenyAllInBound","flows":[{"mac":"000D3AF8801A","flowTuples":["1487282481,195.78.210.194,10.1.0.4,53,1732,U,I,D"]}]},{"rule":"UserRule_default-allow-rdp","flows":[{"mac":"000D3AF8801A","flowTuples":["1487282435,61.129.251.68,10.1.0.4,57776,3389,T,I,A","1487282454,84.25.174.170,10.1.0.4,59085,3389,T,I,A","1487282477,77.68.9.50,10.1.0.4,65078,3389,T,I,A"]}]}]}
        }
        ,
        {
             "time": "2017-02-16T22:02:32.9040000Z",
             "systemId": "2c002c16-72f3-4dc5-b391-3444c3527434",
             "category": "NetworkSecurityGroupFlowEvent",
             "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/FABRIKAMRG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/FABRIAKMVM1-NSG",
             "operationName": "NetworkSecurityGroupFlowEvents",
             "properties": {"Version":1,"flows":[{"rule":"DefaultRule_DenyAllInBound","flows":[{"mac":"000D3AF8801A","flowTuples":["1487282492,175.182.69.29,10.1.0.4,28918,5358,T,I,D","1487282505,71.6.216.55,10.1.0.4,8080,8080,T,I,D"]}]},{"rule":"UserRule_default-allow-rdp","flows":[{"mac":"000D3AF8801A","flowTuples":["1487282512,91.224.160.154,10.1.0.4,59046,3389,T,I,A"]}]}]}
        }
        ,
        ...
```

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come abilitare i log dei flussi, vedere [Enabling Flow logging](network-watcher-nsg-flow-logging-portal.md) (Abilitazione della registrazione dei flussi).

Per informazioni sulla registrazione dei Gruppi di sicurezza di rete, vedere [Analisi dei log per i gruppi di sicurezza di rete](../virtual-network/virtual-network-nsg-manage-log.md).

Per sapere se il traffico è consentito o negato in una VM, vedere [Verify traffic with IP flow verify](network-watcher-check-ip-flow-verify-portal.md) (Controllare il traffico con la verifica del flusso IP)

<!-- Image references -->
[1]: ./media/network-watcher-nsg-flow-logging-overview/figure1.png


