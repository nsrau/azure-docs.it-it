---
title: Schema di analisi del traffico di Azure | Microsoft Docs
description: Comprendere lo schema di Analisi del traffico per analizzare i log di flusso dei gruppi di sicurezza di rete di Azure.
services: network-watcher
documentationcenter: na
author: vinynigam
manager: agummadi
editor: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/26/2019
ms.author: vinigam
ms.openlocfilehash: efa8a92ca9861c0280237ba07f4304b5c7dbbb88
ms.sourcegitcommit: 6cff17b02b65388ac90ef3757bf04c6d8ed3db03
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/29/2019
ms.locfileid: "68610000"
---
# <a name="schema-and-data-aggregation-in-traffic-analytics"></a>Schema e aggregazione dei dati in Analisi del traffico

Analisi del traffico è una soluzione basata sul cloud che fornisce visibilità delle attività di utenti e applicazioni nelle reti cloud. Analisi del traffico analizza i log dei flussi dei gruppi di sicurezza di rete di Network Watcher per fornire informazioni dettagliate sul flusso del traffico nel cloud di Azure. Con Analisi del traffico è possibile:

- Visualizzare l'attività della rete nelle sottoscrizioni di Azure e identificare le aree sensibili.
- Identificare le minacce alla sicurezza e proteggere la rete con informazioni quali porte aperte, applicazioni che tentano l'accesso a Internet e macchine virtuali (VM) che si connettono a reti non autorizzate.
- Conoscere i modelli di flusso di traffico nelle aree di Azure e in Internet per ottimizzare le prestazioni e la capacità della distribuzione della rete.
- Trovare le configurazioni di rete errate che comportano connessioni non riuscite nella rete.
- Informazioni sull'utilizzo della rete in byte, pacchetti o flussi.

### <a name="data-aggregation"></a>Aggregazione dei dati

1. Tutti i log di flusso in un NSG tra "FlowIntervalStartTime_t" e "FlowIntervalEndTime_t" vengono acquisiti a intervalli di un minuto nell'account di archiviazione come BLOB prima di essere elaborati da Analisi del traffico. 
2. L'intervallo di elaborazione predefinito del Analisi del traffico è 60 minuti. Ciò significa che ogni 60 minuti Analisi del traffico seleziona i BLOB dall'archiviazione per l'aggregazione. Se l'intervallo di elaborazione scelto è 10 minuti, Analisi del traffico selezionerà i BLOB dall'account di archiviazione dopo ogni 10 minuti.
3. Flussi con lo stesso IP di origine, IP di destinazione, porta di destinazione, nome NSG, regola NSG, direzione del flusso e protocollo di livello trasporto (TCP o UDP) (Nota: La porta di origine è esclusa per l'aggregazione) viene suddivisa in un singolo flusso per Analisi del traffico
4. Questo singolo record è decorato (dettagli nella sezione riportata di seguito) e inserito in Log Analytics da Analisi del traffico. questo processo può richiedere fino a un massimo di 1 ora.
5. Il campo FlowStartTime_t indica la prima occorrenza di un flusso di questo tipo aggregato (stessa tupla con quattro elementi) nell'intervallo di elaborazione del log di flusso tra "FlowIntervalStartTime_t" e "FlowIntervalEndTime_t".
6. Per qualsiasi risorsa in TA, i flussi indicati nell'interfaccia utente sono flussi totali visualizzati da NSG, ma in Log Analytics utente vedrà solo il singolo record ridotto. Per visualizzare tutti i flussi, usare il campo blob_id, a cui è possibile fare riferimento dalla risorsa di archiviazione. Il conteggio totale dei flussi per il record corrisponderà ai singoli flussi visualizzati nel BLOB.

La query seguente consente di esaminare tutti i log di flusso dall'ambiente locale negli ultimi 30 giorni.
```
AzureNetworkAnalytics_CL
| where SubType_s == "FlowLog" and FlowStartTime_t >= ago(30d) and FlowType_s == "ExternalPublic"
| project Subnet_s  
```
Per visualizzare il percorso BLOB per i flussi nella query indicata in precedenza, usare la query seguente:

```
let TableWithBlobId =
(AzureNetworkAnalytics_CL
   | where SubType_s == "Topology" and ResourceType == "NetworkSecurityGroup" and DiscoveryRegion_s == Region_s and IsFlowEnabled_b
   | extend binTime = bin(TimeProcessed_t, 6h),
            nsgId = strcat(Subscription_g, "/", Name_s),
            saNameSplit = split(FlowLogStorageAccount_s, "/")
   | extend saName = iif(arraylength(saNameSplit) == 3, saNameSplit[2], '')
   | distinct nsgId, saName, binTime)
| join kind = rightouter (
   AzureNetworkAnalytics_CL
   | where SubType_s == "FlowLog"  
   | extend binTime = bin(FlowEndTime_t, 6h)
) on binTime, $left.nsgId == $right.NSGList_s  
| extend blobTime = format_datetime(todatetime(FlowIntervalStartTime_t), "yyyy MM dd hh")
| extend nsgComponents = split(toupper(NSGList_s), "/"), dateTimeComponents = split(blobTime, " ")
| extend BlobPath = strcat("https://", saName,
                        "@insights-logs-networksecuritygroupflowevent/resoureId=/SUBSCRIPTIONS/", nsgComponents[0],
                        "/RESOURCEGROUPS/", nsgComponents[1],
                        "/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/", nsgComponents[2],
                        "/y=", dateTimeComponents[0], "/m=", dateTimeComponents[1], "/d=", dateTimeComponents[2], "/h=", dateTimeComponents[3],
                        "/m=00/macAddress=", replace(@"-", "", MACAddress_s),
                        "/PT1H.json")
| project-away nsgId, saName, binTime, blobTime, nsgComponents, dateTimeComponents;

TableWithBlobId
| where SubType_s == "FlowLog" and FlowStartTime_t >= ago(30d) and FlowType_s == "ExternalPublic"
| project Subnet_s , BlobPath
```

La query precedente crea un URL per accedere direttamente al BLOB. L'URL con i posti di posizionamento è il seguente:

```
https://{saName}@insights-logs-networksecuritygroupflowevent/resoureId=/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroup}/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/{nsgName}/y={year}/m={month}/d={day}/h={hour}/m=00/macAddress={macAddress}/PT1H.json

```

### <a name="fields-used-in-traffic-analytics-schema"></a>Campi utilizzati nello schema Analisi del traffico

Analisi del traffico si basa su Log Analytics, è quindi possibile eseguire query personalizzate sui dati decorati da Analisi del traffico e impostare avvisi sullo stesso.

Di seguito sono elencati i campi dello schema e gli elementi che indicano

| Campo | Formato | Commenti |
|:---   |:---    |:---  |
| TableName | AzureNetworkAnalytics_CL | Tabella per i dati Analisi del traffico
| SubType_s | FlowLog | Sottotipo per i log dei flussi. Utilizzare solo "FlowLog", altri valori di SubType_s sono per i meccanismi interni del prodotto |
| FASchemaVersion_s |   1   | Versione dello schema. Non riflette la versione del log del flusso di NSG |
| TimeProcessed_t   | Data e ora in formato UTC  | Ora in cui il Analisi del traffico ha elaborato i log di flusso non elaborati dall'account di archiviazione |
| FlowIntervalStartTime_t | Data e ora in formato UTC |  Ora di inizio dell'intervallo di elaborazione del log di flusso. Tempo da cui viene misurato l'intervallo di flusso |
| FlowIntervalEndTime_t | Data e ora in formato UTC | Ora di fine dell'intervallo di elaborazione del log di flusso |
| FlowStartTime_t | Data e ora in formato UTC |  Prima occorrenza del flusso (che viene aggregata) nell'intervallo di elaborazione del log di flusso tra "FlowIntervalStartTime_t" e "FlowIntervalEndTime_t". Questo flusso viene aggregato in base alla logica di aggregazione |
| FlowEndTime_t | Data e ora in formato UTC | Ultima occorrenza del flusso (che viene aggregata) nell'intervallo di elaborazione del log di flusso tra "FlowIntervalStartTime_t" e "FlowIntervalEndTime_t". In termini di log di flusso V2, questo campo contiene l'ora in cui è stato avviato l'ultimo flusso con le stesse quattro Tuple (contrassegnato come "B" nel record di flusso non elaborato) |
| FlowType_s |  * IntraVNet <br> * Tra reti virtuali <br> * S2S <br> * P2S <br> * AzurePublic <br> * ExternalPublic <br> * MaliciousFlow <br> * Privato sconosciuto <br> * Sconosciuto | Definizione nelle note sotto la tabella |
| SrcIP_s | Indirizzo IP origine | Il campo sarà vuoto in caso di flussi AzurePublic e ExternalPublic |
| DestIP_s | Indirizzo IP di destinazione | Il campo sarà vuoto in caso di flussi AzurePublic e ExternalPublic |
| VMIP_s | IP della macchina virtuale | Usato per flussi AzurePublic e ExternalPublic |
| PublicIP_s | Indirizzi IP pubblici | Usato per flussi AzurePublic e ExternalPublic |
| DestPort_d | Porta di destinazione | Porta in cui il traffico è in ingresso |
| L4Protocol_s  | * T <br> * U  | Protocollo di trasporto. T = TCP <br> U = UDP |
| L7Protocol_s  | Nome protocollo | Derivato dalla porta di destinazione |
| FlowDirection_s | * I = in ingresso<br> * O = in uscita | Direzione del flusso in/out di NSG come per log di flusso |
| FlowStatus_s  | * A = consentito dalla regola NSG <br> * D = negato dalla regola NSG  | Stato del flusso consentito/nblocked da NSG in base al log di flusso |
| NSGList_s | \<SUBSCRIPTIONID>\/<RESOURCEGROUP_NAME>\/<NSG_NAME> | Gruppo di sicurezza di rete (NSG) associato al flusso |
| NSGRules_s | \<Valore di indice 0) > < NSG_RULENAME\<> direzione del\<flusso > stato\<del flusso > FlowCount ProcessedByRule > |  Regola NSG che ha consentito o negato questo flusso |
| NSGRuleType_s | * Definito dall'utente * predefinito |   Tipo di regola NSG usata dal flusso |
| MACAddress_s | Indirizzo MAC | Indirizzo MAC della scheda di interfaccia di rete in cui è stato acquisito il flusso |
| Subscription_s | La sottoscrizione della rete virtuale o dell'interfaccia di rete/macchina virtuale di Azure viene popolata in questo campo | Applicabile solo per i tipi di flusso FlowType = S2S, P2S, AzurePublic, ExternalPublic, MaliciousFlow e UnknownPrivate (tipi di flusso in cui solo un lato è Azure) |
| Subscription1_s | ID sottoscrizione | ID sottoscrizione della rete virtuale/interfaccia di rete/macchina virtuale a cui appartiene l'indirizzo IP di origine nel flusso |
| Subscription2_s | ID sottoscrizione | ID sottoscrizione della rete virtuale/interfaccia di rete/macchina virtuale a cui appartiene l'indirizzo IP di destinazione |
| Region_s | Area di Azure della rete virtuale/interfaccia di rete/macchina virtuale a cui appartiene l'indirizzo IP nel flusso | Applicabile solo per i tipi di flusso FlowType = S2S, P2S, AzurePublic, ExternalPublic, MaliciousFlow e UnknownPrivate (tipi di flusso in cui solo un lato è Azure) |
| Region1_s | Area di Azure | Area di Azure della rete virtuale/interfaccia di rete/macchina virtuale a cui appartiene l'indirizzo IP di origine nel flusso |
| Region2_s | Area di Azure | Area di Azure della rete virtuale a cui appartiene l'indirizzo IP di destinazione |
| NIC_s | \<resourcegroup_Name >\/\<NetworkInterfaceName > |  SCHEDA di interfaccia di rete associata alla macchina virtuale che invia o riceve il traffico |
| NIC1_s | < resourcegroup_Name >/\<NetworkInterfaceName > | SCHEDA di interfaccia di rete associata all'indirizzo IP di origine nel flusso |
| NIC2_s | < resourcegroup_Name >/\<NetworkInterfaceName > | SCHEDA di interfaccia di rete associata all'indirizzo IP di destinazione nel flusso |
| VM_s | < resourcegroup_Name >\/\<NetworkInterfaceName > | Macchina virtuale associata all'interfaccia di rete NIC_s |
| VM1_s | < resourcegroup_Name >/\<VirtualMachineName > | Macchina virtuale associata all'indirizzo IP di origine nel flusso |
| VM2_s | < resourcegroup_Name >/\<VirtualMachineName > | Macchina virtuale associata all'indirizzo IP di destinazione nel flusso |
| Subnet_s | < ResourceGroup_Name >/< VNET_Name >/\<subnetName > | Subnet associata a NIC_s |
| Subnet1_s | < ResourceGroup_Name >/< VNET_Name >/\<subnetName > | Subnet associata all'indirizzo IP di origine nel flusso |
| Subnet2_s | < ResourceGroup_Name >/< VNET_Name >/\<subnetName >    | Subnet associata all'indirizzo IP di destinazione nel flusso |
| ApplicationGateway1_s | \<SubscriptionID>/\<ResourceGroupName>/\<ApplicationGatewayName> | Gateway applicazione associato all'indirizzo IP di origine nel flusso |
| ApplicationGateway2_s | \<SubscriptionID>/\<ResourceGroupName>/\<ApplicationGatewayName> | Gateway applicazione associato all'indirizzo IP di destinazione nel flusso |
| LoadBalancer1_s | \<SubscriptionId >/\<ResourceGroupName >/\<LoadBalancerName > | Bilanciamento del carico associato all'indirizzo IP di origine nel flusso |
| LoadBalancer2_s | \<SubscriptionId >/\<ResourceGroupName >/\<LoadBalancerName > | Bilanciamento del carico associato all'indirizzo IP di destinazione nel flusso |
| LocalNetworkGateway1_s | \<SubscriptionId >/\<ResourceGroupName >/\<LocalNetworkGatewayName > | Gateway di rete locale associato all'indirizzo IP di origine nel flusso |
| LocalNetworkGateway2_s | \<SubscriptionId >/\<ResourceGroupName >/\<LocalNetworkGatewayName > | Gateway di rete locale associato all'indirizzo IP di destinazione nel flusso |
| ConnectionType_s | I valori possibili sono VNetPeering, VpnGateway e ExpressRoute |    Tipo di connessione |
| ConnectionName_s | \<SubscriptionId >/\<ResourceGroupName >/\<ConnectionName > | Connection Name (Nome connessione) |
| ConnectingVNets_s | Elenco separato da spazi dei nomi delle reti virtuali | Nel caso della topologia hub-spoke, le reti virtuali dell'hub verranno popolate qui |
| Country_s | Codice paese a due lettere (ISO 3166-1 Alpha-2) | Popolato per il tipo di flusso ExternalPublic. Tutti gli indirizzi IP nel campo PublicIPs_s condividono lo stesso codice paese |
| AzureRegion_s | Località dell'area di Azure | Popolato per il tipo di flusso AzurePublic. Tutti gli indirizzi IP nel campo PublicIPs_s condividono l'area di Azure |
| AllowedInFlows_d | | Conteggio dei flussi in ingresso consentiti. Rappresenta il numero di flussi che hanno condiviso le stesse quattro tuple in ingresso nell'interfaccia di rete in cui è stato acquisito il flusso |
| DeniedInFlows_d |  | Conteggio dei flussi in ingresso che sono stati negati. (In ingresso per l'interfaccia di rete in cui è stato acquisito il flusso) |
| AllowedOutFlows_d | | Conteggio dei flussi in uscita consentiti (in uscita dall'interfaccia di rete in cui è stato acquisito il flusso) |
| DeniedOutFlows_d  | | Conteggio dei flussi in uscita negati (in uscita dall'interfaccia di rete in cui è stato acquisito il flusso) |
| FlowCount_d | Operazione deprecata. Flussi totali che corrispondono alla stessa tupla con quattro elementi. Nel caso dei tipi di flusso ExternalPublic e AzurePublic, il conteggio includerà anche i flussi da diversi indirizzi IP pubblico.
| InboundPackets_d | Pacchetti ricevuti come acquisiti nell'interfaccia di rete in cui è stata applicata la regola NSG | Questa operazione viene popolata solo per la versione 2 dello schema del log del flusso di NSG |
| OutboundPackets_d  | Pacchetti inviati come acquisiti nell'interfaccia di rete in cui è stata applicata la regola NSG | Questa operazione viene popolata solo per la versione 2 dello schema del log del flusso di NSG |
| InboundBytes_d |  Byte ricevuti come acquisiti nell'interfaccia di rete in cui è stata applicata la regola NSG | Questa operazione viene popolata solo per la versione 2 dello schema del log del flusso di NSG |
| OutboundBytes_d | Byte inviati come acquisiti nell'interfaccia di rete in cui è stata applicata la regola NSG | Questa operazione viene popolata solo per la versione 2 dello schema del log del flusso di NSG |
| CompletedFlows_d  |  | Viene popolato con un valore diverso da zero solo per la versione 2 dello schema del log del flusso NSG |
| PublicIPs_s | <PUBLIC_IP>\|\<FLOW_STARTED_COUNT>\|\<FLOW_ENDED_COUNT>\|\<OUTBOUND_PACKETS>\|\<INBOUND_PACKETS>\|\<OUTBOUND_BYTES>\|\<INBOUND_BYTES> | Voci separate da barre |

### <a name="notes"></a>Note

1. Nel caso dei flussi AzurePublic e ExternalPublic, l'indirizzo IP della macchina virtuale di Azure di proprietà del cliente viene popolato nel campo VMIP_s, mentre gli indirizzi IP pubblici vengono popolati nel campo PublicIPs_s. Per questi due tipi di flusso, è consigliabile usare VMIP_s e PublicIPs_s anziché i campi SrcIP_s e DestIP_s. Per gli indirizzi AzurePublic e ExternalPublicIP, si aggregano ulteriormente, in modo che il numero di record inseriti nell'area di lavoro di log Analytics del cliente sia minimo. Questo campo verrà presto deprecato ed è consigliabile usare SrcIP_ e DestIP_s a seconda che la macchina virtuale di Azure sia l'origine o la destinazione nel flusso.
1. Dettagli per i tipi di flusso: In base agli indirizzi IP del flusso, i flussi vengono suddivisi in categorie nei tipi di flusso seguenti:
1. IntraVNet: entrambi gli indirizzi IP nel flusso si trovano nella stessa rete virtuale di Azure.
1. Tra reti virtuali-gli indirizzi IP nel flusso si trovano nelle due diverse reti virtuali di Azure.
1. S2S-(da sito a sito) uno degli indirizzi IP appartiene alla rete virtuale di Azure mentre l'altro indirizzo IP appartiene alla rete del cliente (sito) connesso alla rete virtuale di Azure tramite il gateway VPN o Express route.
1. P2S-(da punto a sito) uno degli indirizzi IP appartiene alla rete virtuale di Azure mentre l'altro indirizzo IP appartiene alla rete del cliente (sito) connesso alla rete virtuale di Azure tramite il gateway VPN.
1. AzurePublic: uno degli indirizzi IP appartiene alla rete virtuale di Azure mentre l'altro indirizzo IP appartiene a indirizzi IP pubblici interni di Azure di proprietà di Microsoft. Gli indirizzi IP pubblici di proprietà del cliente non faranno parte di questo tipo di flusso. Ad esempio, tutte le macchine virtuali di proprietà del cliente che inviano traffico a un servizio di Azure (endpoint di archiviazione) verranno categorizzate in questo tipo di flusso.
1. ExternalPublic: uno degli indirizzi IP appartiene alla rete virtuale di Azure, mentre l'altro è un indirizzo IP pubblico che non si trova in Azure, non viene segnalato come dannoso nei feed ASC che Analisi del traffico utilizza per l'intervallo di elaborazione tra " FlowIntervalStartTime_t "e" FlowIntervalEndTime_t ".
1. MaliciousFlow: uno degli indirizzi IP appartiene alla rete virtuale di Azure, mentre l'altro è un indirizzo IP pubblico che non si trova in Azure e viene segnalato come dannoso nei feed ASC che Analisi del traffico utilizza per l'intervallo di elaborazione tra " FlowIntervalStartTime_t "e" FlowIntervalEndTime_t ".
1. UnknownPrivate: uno degli indirizzi IP appartiene alla rete virtuale di Azure, mentre l'altro indirizzo IP appartiene a un intervallo di indirizzi IP privati come definito nella specifica RFC 1918 e non può essere mappato da Analisi del traffico a un sito di proprietà del cliente o a una rete virtuale di Azure.
1. Sconosciuto: non è possibile eseguire il mapping di uno degli indirizzi IP nei flussi con la topologia Customer in Azure e in locale (sito).
1. Alcuni nomi di campo vengono aggiunti con _S o _D. Questi non indicano l'origine e la destinazione.

### <a name="next-steps"></a>Fasi successive
Per ottenere risposte alle domande frequenti, vedere domande [frequenti su analisi del traffico](traffic-analytics-faq.md) per visualizzare i dettagli sulle funzionalità, vedere la [documentazione di analisi del traffico](traffic-analytics.md)
