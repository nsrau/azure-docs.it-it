---
title: Introduzione alla registrazione dei flussi per gruppi
titleSuffix: Azure Network Watcher
description: Questo articolo illustra come usare la funzionalità dei log dei flussi dei gruppi di sicurezza di rete di Azure Network Watcher.
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: damendo
ms.openlocfilehash: ed14d3fb1cd3d9d8af37088811ce62b050778a95
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82189804"
---
# <a name="introduction-to-flow-logging-for-network-security-groups"></a>Introduzione alla registrazione dei flussi per i gruppi di sicurezza di rete

## <a name="introduction"></a>Introduzione

I log dei flussi del [gruppo di sicurezza di rete](https://docs.microsoft.com/azure/virtual-network/security-overview#security-rules) (NSG) sono una funzionalità di Azure Network Watcher che consente di registrare informazioni sul flusso del traffico IP tramite un NSG. I dati di flusso vengono inviati agli account di archiviazione di Azure da cui è possibile accedervi e esportarli in qualsiasi strumento di visualizzazione, SIEM o ID a scelta.

![Panoramica dei log di flusso](./media/network-watcher-nsg-flow-logging-overview/homepage.jpg)

## <a name="why-use-flow-logs"></a>Perché usare i log di flusso

È fondamentale monitorare, gestire e conoscere la rete per fare in modo che la sicurezza, la conformità e le prestazioni non vengano compromesse. Per proteggere e ottimizzare l'ambiente, è indispensabile conoscerlo. Spesso è necessario conoscere lo stato corrente della rete, chi si connette a cosa, da dove si connettono, quali porte sono aperte a Internet, il comportamento previsto della rete, il comportamento irregolare della rete e i picchi improvvisi di traffico.

I log dei flussi sono l'origine della verità per tutte le attività di rete nell'ambiente cloud. Che tu sia un avvio imminente che tenti di ottimizzare le risorse o grandi aziende che tentano di rilevare le intrusioni, i log dei flussi sono la scelta migliore. Puoi usarlo per ottimizzare i flussi di rete, monitorare la velocità effettiva, verificare la conformità, rilevare intrusioni e altro ancora.

## <a name="common-use-cases"></a>Casi d'uso comuni

**Monitoraggio della rete**: identificare il traffico sconosciuto o indesiderato. Monitorare i livelli di traffico e l'utilizzo della larghezza di banda Filtrare i log di flusso per IP e porta per comprendere il comportamento dell'applicazione. Esporta i log di flusso negli strumenti di analisi e visualizzazione che preferisci per configurare i dashboard di monitoraggio.

**Monitoraggio e ottimizzazione dell'utilizzo:** Identificare i Talker principali nella rete. Combinare con i dati di GeoIP per identificare il traffico tra aree. Comprendere la crescita del traffico per la previsione della capacità. Usare i dati per rimuovere regole del traffico esplicitamente restrittive.

**Conformità**: usare i dati del flusso per verificare l'isolamento e la conformità della rete con le regole di accesso dell'organizzazione

**Analisi della rete & analisi della sicurezza**: analizzare i flussi di rete da indirizzi IP compromessi e interfacce di rete. Esporta i log dei flussi in qualsiasi strumento SIEM o IDS di tua scelta.

## <a name="how-logging-works"></a>Funzionamento della registrazione

**Proprietà chiave**

- I log di flusso operano al [livello 4](https://en.wikipedia.org/wiki/OSI_model#Layer_4:_Transport_Layer) e registrano tutti i flussi IP in entrata e in uscita da un NSG
- I log vengono raccolti tramite la piattaforma Azure e non influiscono in alcun modo sulle risorse dei clienti o sulle prestazioni di rete.
- I log vengono scritti in formato JSON e mostrano i flussi in ingresso e in uscita in base alla regola NSG.
- Ogni record di log contiene l'interfaccia di rete (NIC) a cui si applica il flusso, informazioni su 5 tuple, il traffico decisionale & (solo versione 2) informazioni sulla velocità effettiva. Per i dettagli completi, vedere il _formato di log_ riportato di seguito.
- I log di flusso hanno una funzionalità di conservazione che consente di eliminare automaticamente i log fino a un anno dopo la loro creazione. **Nota**: la conservazione è disponibile solo se si usano gli account di archiviazione per utilizzo [generico V2 (GPv2)](https://docs.microsoft.com/azure/storage/common/storage-account-overview#types-of-storage-accounts). 

**Concetti principali **

- Le reti definite da software sono organizzate intorno alle reti virtuali (reti virtuali) e alle subnet. La sicurezza di questi reti virtuali e subnet può essere gestita tramite un NSG.
- Un gruppo di sicurezza di rete (NSG) contiene un elenco di _regole di sicurezza_ che consentono o negano il traffico di rete nelle risorse a cui è connessa. Gruppi può essere associato a subnet, singole VM o singole interfacce di rete collegate alle VM (Gestione risorse). Per altre informazioni, vedere [Panoramica dei gruppi di sicurezza di rete](https://docs.microsoft.com/azure/virtual-network/security-overview?toc=%2Fazure%2Fnetwork-watcher%2Ftoc.json).
- Tutti i flussi di traffico nella rete vengono valutati usando le regole nel NSG applicabile.
- Il risultato di queste valutazioni è NSG Flow log. I log di flusso vengono raccolti tramite la piattaforma Azure e non richiedono alcuna modifica alle risorse del cliente.
- I log di flusso NSG vengono scritti negli account di archiviazione da cui è possibile accedervi.
- È possibile esportare, elaborare, analizzare e visualizzare i log di flusso usando strumenti come TA, Splunk, Grafana, Stealthwatch e così via.

## <a name="log-format"></a>Formato di log

I log dei flussi includono le proprietà seguenti:

* **time**: ora in cui l'evento è stato registrato.
* **SystemId** -ID risorsa gruppo di sicurezza di rete.
* **category**: categoria dell'evento. La categoria è sempre **NetworkSecurityGroupFlowEvent**
* **resourceId** : ID risorsa del NSG
* **operationName**: sempre NetworkSecurityGroupFlowEvents.
* **properties**: raccolta di proprietà del flusso.
    * **Version**: numero di versione dello schema di eventi del log dei flussi.
    * **Flows** : raccolta di flussi. Questa proprietà ha più voci per regole diverse.
        * **rule**: regola per cui vengono elencati i flussi.
            * **flows**: raccolta di flussi.
                * **mac**: indirizzo MAC della scheda di interfaccia di rete per la VM in cui è stato raccolto il flusso.
                * **flowTuples**: stringa che contiene più proprietà per la tupla del flusso nel formato con valori separati da virgole.
                    * **Timestamp** : questo valore è il timestamp del momento in cui il flusso si è verificato nel formato Epoch UNIX
                    * **Source IP**: IP di origine.
                    * **Destination IP**: IP di destinazione.
                    * **Source Port**: porta di origine.
                    * **Destination Port**: porta di destinazione.
                    * **Protocol**: protocollo del flusso. I valori validi sono **T** per TCP e **U** per UDP.
                    * **Traffic Flow**: direzione del flusso del traffico. I valori validi sono **I** per traffico in ingresso e **O** per il traffico in uscita.
                    * **Traffic Decision**: indica se il traffico è stato consentito o negato. I valori validi sono **A** per il traffico consentito e **D** per il traffico negato.
                    * **Flow State - Version 2 Only**: acquisisce lo stato del flusso. Gli stati possibili sono **B**: indica la creazione di un flusso. Non vengono fornite statistiche. **C**: indica un flusso in corso. Vengono fornite statistiche a intervalli di 5 minuti. **E**: indica un flusso terminato. Vengono fornite statistiche.
                    * **Packets - Source to destination - Version 2 Only** Numero totale di pacchetti TCP o UDP inviati dall'origine alla destinazione dall'ultimo aggiornamento.
                    * **Bytes sent - Source to destination - Version 2 Only** Numero totale di byte dei pacchetti TCP o UDP inviati dall'origine alla destinazione dall'ultimo aggiornamento. I byte dei pacchetti includono l'intestazione del pacchetto e il payload.
                    * **Packets - Destination to source - Version 2 Only** Numero totale di pacchetti TCP o UDP inviati dalla destinazione all'origine dall'ultimo aggiornamento.
                    * **Bytes sent - Destination to source - Version 2 Only** Numero totale di byte dei pacchetti TCP e UDP inviati dalla destinazione all'origine dall'ultimo aggiornamento. I byte dei pacchetti includono payload e intestazione del pacchetto.


**Log dei flussi di NSG versione 2 (vs versione 1)** 

Nella versione 2 dei log è stato introdotto il concetto di stato del flusso. È possibile configurare la versione del log di flusso ricevuta.

Lo stato del flusso _B_ viene registrato quando viene avviato un flusso. Gli stati del flusso _C_ ed _E_ contrassegnano rispettivamente la continuazione e la fine di un flusso. Gli stati _C_ ed _E_ contengono informazioni sulla larghezza di banda del traffico.

### <a name="sample-log-records"></a>Record di log di esempio

Di seguito è riportato un testo di esempio di log dei flussi. Come si può osservare, più record seguono l'elenco di proprietà descritto nella sezione precedente.

> [!NOTE]
> I valori nella proprietà **della flowtuples* sono un elenco delimitato da virgole.
 
**Esempio di formato di log di flusso del gruppo di sicurezza di rete versione 1**
```json
{
    "records": [
        {
            "time": "2017-02-16T22:00:32.8950000Z",
            "systemId": "2c002c16-72f3-4dc5-b391-3444c3527434",
            "category": "NetworkSecurityGroupFlowEvent",
            "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/FABRIKAMRG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/FABRIAKMVM1-NSG",
            "operationName": "NetworkSecurityGroupFlowEvents",
            "properties": {
                "Version": 1,
                "flows": [
                    {
                        "rule": "DefaultRule_DenyAllInBound",
                        "flows": [
                            {
                                "mac": "000D3AF8801A",
                                "flowTuples": [
                                    "1487282421,42.119.146.95,10.1.0.4,51529,5358,T,I,D"
                                ]
                            }
                        ]
                    },
                    {
                        "rule": "UserRule_default-allow-rdp",
                        "flows": [
                            {
                                "mac": "000D3AF8801A",
                                "flowTuples": [
                                    "1487282370,163.28.66.17,10.1.0.4,61771,3389,T,I,A",
                                    "1487282393,5.39.218.34,10.1.0.4,58596,3389,T,I,A",
                                    "1487282393,91.224.160.154,10.1.0.4,61540,3389,T,I,A",
                                    "1487282423,13.76.89.229,10.1.0.4,53163,3389,T,I,A"
                                ]
                            }
                        ]
                    }
                ]
            }
        },
        {
            "time": "2017-02-16T22:01:32.8960000Z",
            "systemId": "2c002c16-72f3-4dc5-b391-3444c3527434",
            "category": "NetworkSecurityGroupFlowEvent",
            "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/FABRIKAMRG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/FABRIAKMVM1-NSG",
            "operationName": "NetworkSecurityGroupFlowEvents",
            "properties": {
                "Version": 1,
                "flows": [
                    {
                        "rule": "DefaultRule_DenyAllInBound",
                        "flows": [
                            {
                                "mac": "000D3AF8801A",
                                "flowTuples": [
                                    "1487282481,195.78.210.194,10.1.0.4,53,1732,U,I,D"
                                ]
                            }
                        ]
                    },
                    {
                        "rule": "UserRule_default-allow-rdp",
                        "flows": [
                            {
                                "mac": "000D3AF8801A",
                                "flowTuples": [
                                    "1487282435,61.129.251.68,10.1.0.4,57776,3389,T,I,A",
                                    "1487282454,84.25.174.170,10.1.0.4,59085,3389,T,I,A",
                                    "1487282477,77.68.9.50,10.1.0.4,65078,3389,T,I,A"
                                ]
                            }
                        ]
                    }
                ]
            }
        },
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
        
        
```
**Esempio di formato di log di flusso del gruppo di sicurezza di rete versione 2**
```json
 {
    "records": [
        {
            "time": "2018-11-13T12:00:35.3899262Z",
            "systemId": "a0fca5ce-022c-47b1-9735-89943b42f2fa",
            "category": "NetworkSecurityGroupFlowEvent",
            "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/FABRIKAMRG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/FABRIAKMVM1-NSG",
            "operationName": "NetworkSecurityGroupFlowEvents",
            "properties": {
                "Version": 2,
                "flows": [
                    {
                        "rule": "DefaultRule_DenyAllInBound",
                        "flows": [
                            {
                                "mac": "000D3AF87856",
                                "flowTuples": [
                                    "1542110402,94.102.49.190,10.5.16.4,28746,443,U,I,D,B,,,,",
                                    "1542110424,176.119.4.10,10.5.16.4,56509,59336,T,I,D,B,,,,",
                                    "1542110432,167.99.86.8,10.5.16.4,48495,8088,T,I,D,B,,,,"
                                ]
                            }
                        ]
                    },
                    {
                        "rule": "DefaultRule_AllowInternetOutBound",
                        "flows": [
                            {
                                "mac": "000D3AF87856",
                                "flowTuples": [
                                    "1542110377,10.5.16.4,13.67.143.118,59831,443,T,O,A,B,,,,",
                                    "1542110379,10.5.16.4,13.67.143.117,59932,443,T,O,A,E,1,66,1,66",
                                    "1542110379,10.5.16.4,13.67.143.115,44931,443,T,O,A,C,30,16978,24,14008",
                                    "1542110406,10.5.16.4,40.71.12.225,59929,443,T,O,A,E,15,8489,12,7054"
                                ]
                            }
                        ]
                    }
                ]
            }
        },
        {
            "time": "2018-11-13T12:01:35.3918317Z",
            "systemId": "a0fca5ce-022c-47b1-9735-89943b42f2fa",
            "category": "NetworkSecurityGroupFlowEvent",
            "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/FABRIKAMRG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/FABRIAKMVM1-NSG",
            "operationName": "NetworkSecurityGroupFlowEvents",
            "properties": {
                "Version": 2,
                "flows": [
                    {
                        "rule": "DefaultRule_DenyAllInBound",
                        "flows": [
                            {
                                "mac": "000D3AF87856",
                                "flowTuples": [
                                    "1542110437,125.64.94.197,10.5.16.4,59752,18264,T,I,D,B,,,,",
                                    "1542110475,80.211.72.221,10.5.16.4,37433,8088,T,I,D,B,,,,",
                                    "1542110487,46.101.199.124,10.5.16.4,60577,8088,T,I,D,B,,,,",
                                    "1542110490,176.119.4.30,10.5.16.4,57067,52801,T,I,D,B,,,,"
                                ]
                            }
                        ]
                    }
                ]
            }
        }
        
```
**Descrizione della tupla di log**

![Panoramica dei log di flusso](./media/network-watcher-nsg-flow-logging-overview/tuple.png)

**Calcolo della larghezza di banda di esempio**

tuple dei flussi da una conversazione TCP tra 185.170.185.105:35370 e 10.2.0.4:23:

"1493763938,185.170.185.105,10.2.0.4,35370,23,T,I,A,B,,,," "1493695838,185.170.185.105,10.2.0.4,35370,23,T,I,A,C,1021,588096,8005,4610880" "1493696138,185.170.185.105,10.2.0.4,35370,23,T,I,A,E,52,29952,47,27072"

Per gli stati _C_ ed _E_ del flusso, i conteggi di byte e pacchetti sono conteggi aggregati dal momento della registrazione della tupla del flusso precedente. Facendo riferimento alla conversazione di esempio precedente, il numero totale di pacchetti trasferiti è 1021+52+8005+47=9125. Il numero totale di byte trasferiti è 588096+29952+4610880+27072=5256000.


## <a name="enabling-nsg-flow-logs"></a>Abilitazione dei log di flusso NSG

Usare il collegamento pertinente riportato di seguito per le guide sull'abilitazione dei log di flusso.

- [Azure portal](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal)
- [PowerShell](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-powershell)
- [CLI](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-cli)
- [REST](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-rest)
- [Azure Resource Manager](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-azure-resource-manager)

## <a name="updating-parameters"></a>Aggiornamento dei parametri

**Azure portal**

Nella portale di Azure passare alla sezione log di flusso NSG in Network Watcher. Fare quindi clic sul nome del NSG. Verrà impostato il riquadro impostazioni per il log dei flussi. Modificare i parametri desiderati e fare clic su **Salva** per distribuire le modifiche.

**PS/CLI/REST/ARM**

Per aggiornare i parametri tramite gli strumenti da riga di comando, usare lo stesso comando usato per abilitare i log di flusso (sopra), ma con i parametri aggiornati che si desidera modificare.

## <a name="working-with-flow-logs"></a>Uso dei log di flusso

*Leggere ed esportare i log dei flussi*

- [Scaricare &amp; i log dei flussi di visualizzazione dal portale](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal#download-flow-log)
- [Leggere i log di flusso con le funzioni di PowerShell](https://docs.microsoft.com/azure/network-watcher/network-watcher-read-nsg-flow-logs)
- [Esportare i log di flusso NSG in Splunk](https://www.splunk.com/en_us/blog/tips-and-tricks/splunking-microsoft-azure-network-watcher-data.html)

Anche se i log dei flussi specificano come destinazione gruppi di sicurezza di rete, non vengono visualizzati come gli altri log. I log dei flussi vengono archiviati solo in un account di archiviazione e hanno un percorso di registrazione come quello dell'esempio seguente:

```
https://{storageAccountName}.blob.core.windows.net/insights-logs-networksecuritygroupflowevent/resourceId=/SUBSCRIPTIONS/{subscriptionID}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/{nsgName}/y={year}/m={month}/d={day}/h={hour}/m=00/macAddress={macAddress}/PT1H.json
```

*Visualizzare i log dei flussi*

- [Analisi del traffico di Azure](https://docs.microsoft.com/azure/network-watcher/traffic-analytics) è un servizio nativo di Azure per l'elaborazione dei log dei flussi, estrae informazioni dettagliate e Visualizza i log dei flussi. 
- [Tutorial Visualizzare i log dei flussi NSG con Power BI](https://docs.microsoft.com/azure/network-watcher/network-watcher-visualize-nsg-flow-logs-power-bi)
- [Tutorial Visualizzare i log dei flussi NSG con Elastic stack](https://docs.microsoft.com/azure/network-watcher/network-watcher-visualize-nsg-flow-logs-open-source-tools)
- [Tutorial Gestire e analizzare i log di flusso NSG con Grafana](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-grafana)
- [Tutorial Gestire e analizzare i log di flusso NSG con Graylog](https://docs.microsoft.com/azure/network-watcher/network-watcher-analyze-nsg-flow-logs-graylog)


## <a name="nsg-flow-logging-considerations"></a>Considerazioni sulla registrazione del flusso NSG

**Considerazioni sull'account di archiviazione**: 

- Località: l'account di archiviazione usato deve trovarsi nella stessa area del NSG.
- Rotazione automatica delle chiavi: se si modificano/ruotano le chiavi di accesso all'account di archiviazione, i log di flusso NSG smetteranno di funzionare. Per risolvere questo problema, è necessario disabilitare e quindi riabilitare i log dei flussi di NSG.

**Costi**per la registrazione dei flussi: la registrazione del flusso NSG viene addebitata sul volume dei log prodotti. Un volume di traffico elevato può avere come effetto un volume elevato dei log dei flussi, con i conseguenti costi associati. I prezzi dei log dei flussi dei gruppi di sicurezza di rete non includono i costi di archiviazione sottostanti. L'uso della funzionalità relativa ai criteri di conservazione con la registrazione del flusso NSG significa sostenere costi di archiviazione separati per periodi di tempo prolungati. Se non è necessario usare la funzionalità dei criteri di conservazione, è consigliabile impostare questo valore su 0. Per ulteriori informazioni, vedere [Network Watcher prezzi](https://azure.microsoft.com/pricing/details/network-watcher/) e [prezzi di archiviazione di Azure](https://azure.microsoft.com/pricing/details/storage/) .

**Flussi in ingresso registrati da indirizzi IP Internet alle VM senza IP pubblici**: le macchine virtuali che non hanno un indirizzo IP pubblico assegnato tramite un indirizzo IP pubblico associato alla scheda di interfaccia di rete come IP pubblico a livello di istanza o che fanno parte di un pool back-end di bilanciamento del carico di base, usano [SNAT predefiniti](../load-balancer/load-balancer-outbound-connections.md#defaultsnat) e hanno un indirizzo IP assegnato da Azure per semplificare la connettività in uscita. Di conseguenza, è possibile visualizzare le voci del log di flusso per i flussi da indirizzi IP Internet, se il flusso è destinato a una porta nell'intervallo di porte assegnate per SNAT. Sebbene Azure non consenta questi flussi alla macchina virtuale, il tentativo viene registrato e viene visualizzato nel log di flusso NSG di Network Watcher in base alla progettazione. È consigliabile che il traffico Internet in ingresso indesiderato venga bloccato in modo esplicito con NSG.

**Conteggio di byte e pacchetti non corretti per i flussi**senza stato: i [gruppi di sicurezza di rete (gruppi)](https://docs.microsoft.com/azure/virtual-network/security-overview) vengono implementati come [Firewall con stato](https://en.wikipedia.org/wiki/Stateful_firewall?oldformat=true). Tuttavia, molte regole predefinite/interne che controllano il flusso di traffico vengono implementate in modo senza stato. A causa delle limitazioni della piattaforma, i conteggi di byte e pacchetti non vengono registrati per i flussi senza stato, ovvero i flussi di traffico che passano attraverso regole senza stato, vengono registrati solo per i flussi con stato. Di conseguenza, il numero di byte e i pacchetti segnalati nei log di flusso NSG (e Analisi del traffico) potrebbero essere diversi dai flussi effettivi. Questa limitazione è stata pianificata per la correzione entro il 2020 giugno.

## <a name="best-practices"></a>Procedure consigliate

**Abilita su reti virtuali/subnet critiche**: i log di flusso devono essere abilitati in tutte le reti virtuali/subnet critiche nella sottoscrizione come procedura di controllo e sicurezza consigliata. 

**Abilitare la registrazione del flusso NSG in tutti gruppi collegati a una risorsa**: la registrazione dei flussi in Azure è configurata nella risorsa NSG. Un flusso sarà associato a una sola regola di gruppo di sicurezza di rete. Negli scenari in cui vengono usati più gruppi, è consigliabile abilitare i log dei flussi NSG in tutti i gruppi applicati alla subnet o all'interfaccia di rete di una risorsa per assicurarsi che tutto il traffico venga registrato. Per altre informazioni, vedere [come viene valutato il traffico](../virtual-network/security-overview.md#how-traffic-is-evaluated) nei gruppi di sicurezza di rete.

**Provisioning dell'archiviazione**: è necessario eseguire il provisioning dell'archiviazione in sintonia con il volume di log di flusso previsto.

## <a name="troubleshooting-common-issues"></a>Risoluzione dei problemi comuni

**Impossibile abilitare i log dei flussi dei gruppi di sicurezza di rete**

- Il provider di risorse **Microsoft. Insights** non è registrato

Se è stato ricevuto un errore _AuthorizationFailed_ o _GatewayAuthenticationFailed_, potrebbe significare che il provider di risorse Microsoft Insights non è stato abilitato nella sottoscrizione. [Seguire le istruzioni](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal#register-insights-provider) per abilitare il provider Microsoft Insights.

**Sono stati abilitati i log dei flussi dei gruppi di sicurezza di rete ma non vengono visualizzati dati nell'account di archiviazione**

- **Tempo di installazione**

La visualizzazione dei log dei flussi dei gruppo di sicurezza di rete nell'account di archiviazione (se configurato correttamente) può richiedere fino a 5 minuti. Verrà visualizzato PT1H.json, a cui è possibile accedere [come descritto qui](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal#download-flow-log).

- **Assenza di traffico nei gruppi di sicurezza di rete**

Talvolta i log non vengono visualizzati perché le macchine virtuali non sono attive oppure in un gateway applicazione o in altri dispositivi sono presenti filtri upstream che bloccano il traffico verso i gruppi di sicurezza di rete.

**Si desidera automatizzare i log dei flussi dei gruppi di sicurezza di rete**

Il supporto dell'automazione tramite modelli di Azure Resource Manager non è attualmente disponibile per i log dei flussi dei gruppi di sicurezza di rete. Per ulteriori informazioni, leggere l' [annuncio sulle funzionalità](https://azure.microsoft.com/updates/arm-template-support-for-nsg-flow-logs/) .

## <a name="faq"></a>Domande frequenti

**Quali sono i registri dei flussi di NSG?**

Le risorse di rete di Azure possono essere combinate e gestite tramite [gruppi di sicurezza di rete (gruppi)](https://docs.microsoft.com/azure/virtual-network/security-overview). I log di flusso NSG consentono di registrare le informazioni sul flusso di 5 tuple relative a tutto il traffico attraverso gruppi. I log dei flussi non elaborati vengono scritti in un account di archiviazione di Azure da cui possono essere ulteriormente elaborati, analizzati, sottoposti a query o esportati in base alle esigenze.

**L'uso dei log di flusso influisce sulla latenza di rete o sulle prestazioni?**

I dati dei log dei flussi vengono raccolti al di fuori del percorso del traffico di rete e pertanto non influiscono sulla velocità effettiva o sulla latenza di rete. È possibile creare o eliminare i log di flusso senza rischiare di influire sulle prestazioni della rete.

**Ricerca per categorie usare i log di flusso NSG con un account di archiviazione dietro un firewall?**

Per usare un account di archiviazione dietro un firewall, è necessario fornire un'eccezione affinché i servizi Microsoft attendibili accedano all'account di archiviazione:

- Passare all'account di archiviazione digitando il nome dell'account di archiviazione nella ricerca globale nel portale o dalla [pagina account di archiviazione](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.Storage%2FStorageAccounts)
- Nella sezione **Impostazioni** selezionare **firewall e reti virtuali**
- In **Consenti accesso da**selezionare **reti selezionate**. Quindi, in **eccezioni**, seleziona la casella accanto a * * * * Consenti ai servizi Microsoft attendibili di accedere a questo account di archiviazione * * * *
- Se l'opzione è già selezionata, non occorre modificare niente.
- Individuare il NSG di destinazione nella [pagina Panoramica dei log di flusso di NSG](https://ms.portal.azure.com/#blade/Microsoft_Azure_Network/NetworkWatcherMenuBlade/flowLogs) e abilitare i log di flusso di NSG con l'account di archiviazione selezionato.

È possibile controllare i log di archiviazione dopo alcuni minuti. Dovrebbe essere visualizzato un TimeStamp aggiornato o un nuovo file JSON creato.

**Ricerca per categorie usare i log di flusso NSG con un account di archiviazione dietro a un endpoint di servizio?**

I log di flusso NSG sono compatibili con gli endpoint di servizio senza richiedere alcuna configurazione aggiuntiva. Vedere l' [esercitazione sull'abilitazione degli endpoint di servizio](https://docs.microsoft.com/azure/virtual-network/tutorial-restrict-network-access-to-resources#enable-a-service-endpoint) nella rete virtuale.

**Qual è la differenza tra i log dei flussi versioni 1 & 2?**

Log dei flussi versione 2 introduce il concetto di _stato del flusso_ & archivia le informazioni su byte e pacchetti trasmessi. [Altre informazioni](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview#log-file)

## <a name="pricing"></a>Prezzi

I log di flusso NSG vengono addebitati per GB di log raccolti e sono disponibili con un livello gratuito di 5 GB al mese per sottoscrizione. Per i prezzi correnti nella propria area, vedere la [pagina](https://azure.microsoft.com/pricing/details/network-watcher/)relativa ai prezzi di Network Watcher.

L'archiviazione dei log viene addebitata separatamente. per i prezzi pertinenti, vedere la pagina relativa ai prezzi per i [BLOB in blocchi di archiviazione](https://azure.microsoft.com/pricing/details/storage/blobs/)
 
