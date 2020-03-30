---
title: Schema di analisi del traffico di Azure - Documenti Microsoft
description: Informazioni sullo schema di Analisi del traffico per analizzare i log del flusso del gruppo di sicurezza di rete di Azure.Understand schema of Traffic Analytics to analyze Azure network group flow logs.
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
ms.openlocfilehash: ccfbb92c27e4508595f19c2ea6900730cde609b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74666376"
---
# <a name="schema-and-data-aggregation-in-traffic-analytics"></a>Aggregazione di schema e dati in Analisi del traffico

Analisi del traffico è una soluzione basata sul cloud che fornisce visibilità delle attività di utenti e applicazioni nelle reti cloud. Analisi del traffico analizza i log dei flussi dei gruppi di sicurezza di rete di Network Watcher per fornire informazioni dettagliate sul flusso del traffico nel cloud di Azure. Con Analisi del traffico è possibile:

- Visualizzare l'attività della rete nelle sottoscrizioni di Azure e identificare le aree sensibili.
- Identificare le minacce alla sicurezza e proteggere la rete con informazioni quali porte aperte, applicazioni che tentano l'accesso a Internet e macchine virtuali (VM) che si connettono a reti non autorizzate.
- Conoscere i modelli di flusso di traffico nelle aree di Azure e in Internet per ottimizzare le prestazioni e la capacità della distribuzione della rete.
- Trovare le configurazioni di rete errate che comportano connessioni non riuscite nella rete.
- Conoscere l'utilizzo della rete in byte, pacchetti o flussi.

### <a name="data-aggregation"></a>Aggregazione dei dati

1. Tutti i log di flusso in un gruppo di sicurezza di rete tra "FlowIntervalStartTime_t" e "FlowIntervalEndTime_t" vengono acquisiti a intervalli di un minuto nell'account di archiviazione come BLOB prima di essere elaborati da Analisi del traffico.
2. L'intervallo di elaborazione predefinito di Analisi del traffico è 60 minuti. Ciò significa che ogni 60 min analisi del traffico seleziona i BLOB dall'archiviazione per l'aggregazione. Se l'intervallo di elaborazione scelto è 10 min, Analisi del traffico selezionerà i BLOB dall'account di archiviazione dopo ogni 10 min.
3. I flussi che hanno lo stesso IP di origine, IP di destinazione, porta di destinazione, nome del gruppo di sicurezza di rete, regola del gruppo di sicurezza di rete, direzione del flusso e protocollo del livello di trasporto (NOTa: la porta di origine è esclusa per l'aggregazione) vengono inseriti in un singolo flusso da Analisi del traffico
4. Questo singolo record è decorato (Dettagli nella sezione seguente) e ingerito in Log Analytics da Analisi del traffico.Questo processo può richiedere fino a 1 ora al massimo.
5. FlowStartTime_t campo indica la prima occorrenza di tale flusso aggregato (stessa tupla) nell'intervallo di elaborazione del log di flusso tra "FlowIntervalStartTime_t" e "FlowIntervalEndTime_t".
6. Per qualsiasi risorsa in TA, i flussi indicati nell'interfaccia utente sono flussi totali visualizzati dal gruppo di sicurezza di rete, ma in Log Analytics l'utente vedrà solo il record singolo ridotto. Per visualizzare tutti i flussi, usare il campo blob_id, a cui è possibile fare riferimento da Archiviazione.To see all the flows, use the blob_id field, which can be referenced from Storage. Il numero totale di flussi per tale record corrisponderà ai singoli flussi visualizzati nel BLOB.

La query seguente consente di esaminare tutti i log di flusso dall'ambiente locale negli ultimi 30 giorni.
```
AzureNetworkAnalytics_CL
| where SubType_s == "FlowLog" and FlowStartTime_t >= ago(30d) and FlowType_s == "ExternalPublic"
| project Subnet_s  
```
Per visualizzare il percorso del BLOB per i flussi nella query sopra menzionata, usare la query seguente:To view the blob path for the flows in the above mentioned query, use the query below:

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

La query precedente crea un URL per accedere direttamente al BLOB. L'URL con segnaposto è riportato di seguito:

```
https://{saName}@insights-logs-networksecuritygroupflowevent/resoureId=/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroup}/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/{nsgName}/y={year}/m={month}/d={day}/h={hour}/m=00/macAddress={macAddress}/PT1H.json

```

### <a name="fields-used-in-traffic-analytics-schema"></a>Campi usati nello schema di Analisi del traffico
  > [!IMPORTANT]
  > Lo schema di analisi del traffico è stato aggiornato il 22 agosto 2019. Il nuovo schema fornisce gli indirizzi IP di origine e di destinazione separatamente la rimozione della necessità di analizzare il campo FlowDirection rendendo le query più semplici. </br>
  > FASchemaVersion_s aggiornato da 1 a 2. </br>
  > Campi deprecati: VMIP_s, Subscription_s, Region_s, NSGRules_s, Subnet_s, VM_s, NIC_s, PublicIPs_s FlowCount_d </br>
  > Nuovi campi: SrcPublicIPs_s, DestPublicIPs_s NSGRule_s </br>
  > I campi deprecati saranno disponibili fino al 22 novembre 2019.

Analisi del traffico si basa su Log Analytics, pertanto puoi eseguire query personalizzate sui dati decorati da Analisi del traffico e impostare avvisi sullo stesso.

Di seguito sono elencati i campi dello schema e i presenti

| Campo | Format | Commenti |
|:---   |:---    |:---  |
| TableName | AzureNetworkAnalytics_CL | Tabella per i dati di Analisi del traffico
| SubType_s | FlowLog (FlowLog) | Sottotipo per i log di flusso. Utilizzare solo "FlowLog", altri valori di SubType_s sono per il funzionamento interno del prodotto |
| FASchemaVersion_s |   2   | Versione dello schema. Non riflette la versione del registro di flusso del gruppo di sicurezza di database |
| TimeProcessed_t   | Data e ora in UTC  | Ora in cui analisi del traffico ha elaborato i log del flusso non elaborato dall'account di archiviazioneTime at the Traffic Analytics processed the raw flow logs from the storage account |
| FlowIntervalStartTime_t | Data e ora in UTC |  Ora iniziale dell'intervallo di elaborazione del log di flusso. Questo è il tempo da cui viene misurato l'intervallo di flusso |
| FlowIntervalEndTime_t | Data e ora in UTC | Ora di fine dell'intervallo di elaborazione del log di flusso |
| FlowStartTime_t | Data e ora in UTC |  Prima occorrenza del flusso (che verrà aggregato) nell'intervallo di elaborazione del log di flusso tra "FlowIntervalStartTime_t" e "FlowIntervalEndTime_t". Questo flusso viene aggregato in base alla logica di aggregazioneThis flow gets aggregated based on aggregation logic |
| FlowEndTime_t | Data e ora in UTC | Ultima occorrenza del flusso (che verrà aggregato) nell'intervallo di elaborazione del log di flusso tra "FlowIntervalStartTime_t" e "FlowIntervalEndTime_t". In termini di log di flusso v2, questo campo contiene l'ora in cui è stato avviato l'ultimo flusso con la stessa tupla a quattro (contrassegnato come "B" nel record di flusso non elaborato) |
| FlowType_s |  IntraVNet (IntraVNet) <br> InterVNet <br> S2S (S2S) <br> P2S (P2S) <br> - AzurePublic <br> - EsternoPubblico <br> - MaliciousFlow <br> - Privato sconosciuto <br> - Sconosciuto | Definizione nelle note sotto la tabella |
| SrcIP_s | Indirizzo IP di origine | Sarà vuoto in caso di flussi AzurePublic ed ExternalPublic |
| DestIP_s | Indirizzo IP di destinazione | Sarà vuoto in caso di flussi AzurePublic ed ExternalPublic |
| VMIP_s | IP della macchina virtuale | Utilizzato per i flussi AzurePublic ed ExternalPublicUsed for AzurePublic and ExternalPublic flows |
| PublicIP_s | Indirizzi IP pubblici | Utilizzato per i flussi AzurePublic ed ExternalPublicUsed for AzurePublic and ExternalPublic flows |
| DestPort_d | Porta di destinazione | Porta in cui il traffico è in ingresso |
| L4Protocol_s  | T (in questo modo) <br> N. di controllo U  | Protocollo di trasporto. T - TCP <br> U - UDP (UDP) |
| L7Protocol_s  | Nome protocollo | Derivato dalla porta di destinazione |
| FlowDirection_s | I - In entrata<br> O - In uscita | Direzione del flusso in ingresso/uscita dal gruppo di sicurezza di sicurezza di sicurezza di gruppo in base al log di flusso |
| FlowStatus_s  | La regola NSG è consentita dalla regola NSG <br> D - Negato dalla regola NSG  | Stato del flusso consentito/nbloccato dal gruppo di sicurezza di sicurezza di sicurezza di gruppo in base al log di flusso |
| NSGList_s | \<SUBSCRIPTIONID \/> NSG_NAME \/>RESOURCEGROUP_NAME><<<<<< | Gruppo di sicurezza di rete (NSG) associato al flussoNetwork Security Group (NSG) associated with the flow |
| NSGRules_s | \<Il valore di \| \<indice \| \<0)>NSG_RULENAME>direzione del flusso>\| \<stato del flusso>\| \<> FlowCount ProcessedByRule |  Regola del gruppo di sicurezza di sicurezza di sicurezza di gruppo che ha consentito o negato questo flusso |
| NSGRule_s | NSG_RULENAME |  Regola del gruppo di sicurezza di sicurezza di sicurezza di gruppo che ha consentito o negato questo flusso |
| NSGRuleType_s | - Definito dall'utente - Predefinito |   Il tipo di regola del gruppo di sicurezza di sicurezza di sicurezza di base utilizzato dal flusso |
| MACAddress_s | Indirizzo MAC | Indirizzo MAC della scheda NIC in cui è stato acquisito il flusso |
| Subscription_s | La sottoscrizione della rete virtuale di Azure/interfaccia di rete/macchina virtuale viene popolata in questo campoSubscription of the Azure virtual network/network interface/virtual machine is populated in this field | Applicabile solo per i tipi di flusso FlowType: S2S, P2S, AzurePublic, ExternalPublic, MaliciousFlow e UnknownPrivate (tipi di flusso in cui solo un lato è azure)Applicable only for FlowType - S2S, P2S, AzurePublic, ExternalPublic, MaliciousFlow, and UnknownPrivate flow types (flow types where only one side is azure) |
| Subscription1_s | ID sottoscrizione | ID sottoscrizione della rete virtuale/interfaccia di rete/macchina virtuale a cui appartiene l'IP di origine nel flusso |
| Subscription2_s | ID sottoscrizione | ID sottoscrizione della rete virtuale/interfaccia di rete/macchina virtuale a cui appartiene l'IP di destinazione nel flusso |
| Region_s | Area di Azure di rete virtuale/interfaccia di rete/macchina virtuale a cui appartiene l'indirizzo IP nel flussoAzure region of virtual network/network interface/virtual machine to which the IP in the flow belongs to | Applicabile solo per i tipi di flusso FlowType: S2S, P2S, AzurePublic, ExternalPublic, MaliciousFlow e UnknownPrivate (tipi di flusso in cui solo un lato è azure)Applicable only for FlowType - S2S, P2S, AzurePublic, ExternalPublic, MaliciousFlow, and UnknownPrivate flow types (flow types where only one side is azure) |
| Region1_s | Area di Azure | Area di Azure di rete virtuale/interfaccia di rete/macchina virtuale a cui appartiene l'IP di origine nel flussoAzure region of virtual network/network interface/virtual machine to which the source IP in the flow belongs to |
| Region2_s | Area di Azure | Area di Azure della rete virtuale a cui appartiene l'IP di destinazione nel flussoAzure region of virtual network to which the destination IP in the flow belongs to |
| NIC_s | \<> \/ \<resourcegroup_Name>NomeInterfacciarete |  Nic associata alla macchina virtuale che invia o riceve il traffico |
| NIC1_s | <>\<resourcegroup_Name/> nomeinterfacciarete rete | NIC associata all'IP di origine nel flusso |
| NIC2_s | <>\<resourcegroup_Name/> nomeinterfacciarete rete | NIC associata all'IP di destinazione nel flusso |
| VM_s | <resourcegroup_Name \/ \<>nome>interfaccia di rete resourcegroup_Name | Macchina virtuale associata all'interfaccia di rete NIC_s |
| VM1_s | > resourcegroup_Name\<</NomeComputervirtuale> | Macchina virtuale associata all'IP di origine nel flussoVirtual Machine associated with the source IP in the flow |
| VM2_s | > resourcegroup_Name\<</NomeComputervirtuale> | Macchina virtuale associata all'IP di destinazione nel flussoVirtual Machine associated with the destination IP in the flow |
| Subnet_s | ResourceGroup_Name <>/VNET_Name\<>/<Subnet>Name | Subnet associata al NIC_s |
| Subnet1_s | ResourceGroup_Name <>/VNET_Name\<>/<Subnet>Name | Subnet associata all'IP di origine nel flusso |
| Subnet2_s | ResourceGroup_Name <>/VNET_Name\<>/<Subnet>Name    | Subnet associata all'IP di destinazione nel flusso |
| ApplicationGateway1_s | \<> SubscriptionID/ResourceGroupName\<\<>/ApplicationGatewayName> | Gateway applicazione associato all'INDIRIZZO IP di origine nel flussoApplication gateway associated with the Source IP in the flow |
| ApplicationGateway2_s | \<> SubscriptionID/ResourceGroupName\<\<>/ApplicationGatewayName> | Gateway applicazione associato all'IP di destinazione nel flusso |
| LoadBalancer1_s | \<> SubscriptionID/\<> NomeGruppoRisorse/\<NomeServizioServizio>diproprietà | Servizio di bilanciamento del carico associato all'IP di origine nel flussoLoad balancer associated with the Source IP in the flow |
| LoadBalancer2_s | \<> SubscriptionID/\<> NomeGruppoRisorse/\<NomeServizioServizio>diproprietà | Servizio di bilanciamento del carico associato all'IP di destinazione nel flussoLoad balancer associated with the Destination IP in the flow |
| LocalNetworkGateway1_s | \<> SubscriptionID/>\<NomeGruppoRisorse/NomeReteCache\<>Locale | Gateway di rete locale associato all'IP di origine nel flusso |
| LocalNetworkGateway2_s | \<> SubscriptionID/>\<NomeGruppoRisorse/NomeReteCache\<>Locale | Gateway di rete locale associato all'IP di destinazione nel flusso |
| ConnectionType_s | I valori possibili sono VNetPeering, VpnGateway ed ExpressRoute |    Tipo di connessione |
| ConnectionName_s | \<> SubscriptionID/ResourceGroupName\<\<>/ ConnectionName> | Nome connessione. Per Flowtype P2S, questo verrà <gateway name>formattato come _<VPN Client IP> |
| ConnectingVNets_s | Elenco di nomi di rete virtuale separati da spazi | In caso di topologia hub e spoke, le reti virtuali hub verranno popolate qui |
| Country_s | Codice paese a due lettere (ISO 3166-1 alpha-2) | Popolato per il tipo di flusso ExternalPublic. Tutti gli indirizzi IP in PublicIPs_s campo condivideranno lo stesso codice paese |
| AzureRegion_s | Posizioni dell'area di AzureAzure region locations | Popolato per il tipo di flusso AzurePublic.Populated for flow type AzurePublic. Tutti gli indirizzi IP in PublicIPs_s campo condivideranno l'area di AzureAll IP addresses in a field will share the Azure region |
| AllowedInFlows_d | | Numero di flussi in ingresso consentiti. Rappresenta il numero di flussi che hanno condiviso lo stesso in ingresso a quattro tuple verso l'interfaccia di rete in cui è stato acquisito il flusso |
| DeniedInFlows_d |  | Numero di flussi in ingresso negati. (In ingresso all'interfaccia di rete in cui è stato acquisito il flusso) |
| AllowedOutFlows_d | | Numero di flussi in uscita consentiti (in uscita verso l'interfaccia di rete in cui è stato acquisito il flusso) |
| DeniedOutFlows_d  | | Numero di flussi in uscita negati (in uscita verso l'interfaccia di rete in cui è stato acquisito il flusso) |
| FlowCount_d | Operazione deprecata. Flussi totali che corrispondono alla stessa tupla con quattro. In caso di tipi di flusso ExternalPublic e AzurePublic, count includerà anche i flussi da vari indirizzi PublicIP.
| InboundPackets_d | Pacchetti ricevuti come acquisiti nell'interfaccia di rete in cui è stata applicata la regola del gruppo di sicurezza di rete | Viene popolato solo per la versione 2 dello schema del log di flusso del gruppo di sicurezza di databaseThis is populated only for the Version 2 of NSG flow log schema |
| OutboundPackets_d  | Pacchetti inviati come acquisiti nell'interfaccia di rete in cui è stata applicata la regola del gruppo di sicurezza di rete | Viene popolato solo per la versione 2 dello schema del log di flusso del gruppo di sicurezza di databaseThis is populated only for the Version 2 of NSG flow log schema |
| InboundBytes_d |  Byte ricevuti come acquisiti nell'interfaccia di rete in cui è stata applicata la regola del gruppo di sicurezza di rete | Viene popolato solo per la versione 2 dello schema del log di flusso del gruppo di sicurezza di databaseThis is populated only for the Version 2 of NSG flow log schema |
| OutboundBytes_d | Byte inviati come acquisiti nell'interfaccia di rete in cui è stata applicata la regola del gruppo di sicurezza di rete | Viene popolato solo per la versione 2 dello schema del log di flusso del gruppo di sicurezza di databaseThis is populated only for the Version 2 of NSG flow log schema |
| CompletedFlows_d  |  | Viene popolato con un valore diverso da zero solo per la versione 2 dello schema del log di flusso del gruppo di sicurezza di databaseThis is populated with un-zero value only for the Version 2 of NSG flow log schema |
| PublicIPs_s | > INBOUND_BYTES \| \<>\| \< \| \< \| \< \| \< \|>>OUTBOUND_PACKETS FLOW_ENDED_COUNT>FLOW_STARTED_COUNT>PUBLIC_IP <INBOUND_PACKETS INBOUND_PACKETS INBOUND_PACKETS>>>>>>>>>>>>>>>>>>>\<> | Voci separate da barre |
| SrcPublicIPs_s | > INBOUND_BYTES \| \<>\| \< \| \< \| \< \| \< \|>INBOUND_PACKETS>OUTBOUND_PACKETS>FLOW_ENDED_COUNT>>SOURCE_PUBLIC_IP <FLOW_STARTED_COUNT FLOW_STARTED_COUNT FLOW_STARTED_COUNT FLOW_STARTED_COUNT FLOW_STARTED_COUNT FLOW_STARTED_COUNT FLOW_STARTED_COUNT FLOW_STARTED_COUNT FLOW_STARTED_COUNT FLOW_STARTED_COUNT FLOW_STARTED_COUNT FLOW_STARTED_COUNT FLOW_STARTED_COUNT FLOW_STARTED_COUNT FLOW_STARTED_COUNT FLOW_STARTED_COUNT \<FLOW_STARTED_COUNT | Voci separate da barre |
| DestPublicIPs_s | <DESTINATION_PUBLIC_IP \| \<>\| \<<\| \<>\| \<FLOW_STARTED_COUNT \| \<>\| \<OUTBOUND_BYTES INBOUND_PACKETS>OUTBOUND_PACKETS>FLOW_ENDED_COUNT>>>>>INBOUND_BYTES INBOUND_BYTES INBOUND_BYTES INBOUND_BYTES INBOUND_BYTES INBOUND_BYTES INBOUND_BYTES INBOUND_BYTES INBOUND_BYTES INBOUND_BYTES INBOUND_BYTES INBOUND_BYTES INBOUND_BYTES INBOUND_BYTES INBOUND_BYTES INBOUND_BYTES INBOUND_BYTES INBOUND_BYTES | Voci separate da barre |

### <a name="notes"></a>Note

1. In caso di flussi AzurePublic ed ExternalPublic, l'IP della macchina virtuale di Azure di proprietà del cliente viene popolato nel campo VMIP_s, mentre gli indirizzi IP pubblici vengono popolati nel campo PublicIPs_s. Per questi due tipi di flusso, è necessario usare VMIP_s e PublicIPs_s anziché i campi di SrcIP_s e DestIP_s. Per gli indirizzi AzurePublic e ExternalPublicIP, viene aggregato ulteriormente, in modo che il numero di record ingeriti nell'area di lavoro di analisi dei log dei clienti sia minimo. (Questo campo sarà deprecato a breve e dovremmo usare SrcIP_ e DestIP_s a seconda che la macchina virtuale azure sia l'origine o la destinazione nel flusso)
1. Dettagli per i tipi di flusso: in base agli indirizzi IP coinvolti nel flusso, i flussi vengono classificati nei tipi di flusso seguenti:Details for flow types: Based on the IP addresses involved in the flow, we categorize the flows in to the following flow types:
1. IntraVNet: entrambi gli indirizzi IP nel flusso risiedono nella stessa rete virtuale di Azure.IntraVNet – Both the IP addresses in the flow reside in the same Azure Virtual Network.
1. InterVNet: gli indirizzi IP nel flusso risiedono nelle due diverse reti virtuali di Azure.InterVNet - IP addresses in the flow reside in the two different Azure Virtual Networks.
1. S2S – (Sito a sito) Uno degli indirizzi IP appartiene alla rete virtuale di Azure mentre l'altro indirizzo IP appartiene alla rete del cliente (sito) connessa alla rete virtuale di Azure tramite gateway VPN o Express Route.
1. P2S - (Point To Site) Uno degli indirizzi IP appartiene alla rete virtuale di Azure mentre l'altro indirizzo IP appartiene alla rete del cliente (sito) connessa alla rete virtuale di Azure tramite gateway VPN.
1. AzurePublic: uno degli indirizzi IP appartiene alla rete virtuale di Azure mentre l'altro indirizzo IP appartiene agli indirizzi IP pubblici interni di Azure di proprietà di Microsoft.AzurePublic - One of the IP addresses belongs to Azure Virtual Network while the other IP address belongs to Azure Internal Public IP addresses owned by Microsoft. Gli indirizzi IP pubblici di proprietà del cliente non faranno parte di questo tipo di flusso. Ad esempio, qualsiasi macchina virtuale di proprietà del cliente che invia il traffico a un servizio di Azure (endpoint di archiviazione) verrebbe classificata in questo tipo di flusso.
1. ExternalPublic - Uno degli indirizzi IP appartiene alla rete virtuale di Azure mentre l'altro indirizzo IP è un indirizzo IP pubblico che non è in Azure, non viene segnalato come dannoso nei feed ASC che Analisi del traffico utilizza per l'intervallo di elaborazione tra " FlowIntervalStartTime_t" e "FlowIntervalEndTime_t".
1. MaliciousFlow - Uno degli indirizzi IP appartiene alla rete virtuale di Azure mentre l'altro indirizzo IP è un indirizzo IP pubblico che non è in Azure e viene segnalato come dannoso nei feed ASC che Analisi del traffico utilizza per l'intervallo di elaborazione tra " FlowIntervalStartTime_t" e "FlowIntervalEndTime_t".
1. UnknownPrivate: uno degli indirizzi IP appartiene alla rete virtuale di Azure mentre l'altro indirizzo IP appartiene all'intervallo IP privato definito in RFC 1918 e non può essere mappato da Analisi traffico a un sito di proprietà del cliente o a una rete virtuale di Azure.
1. Sconosciuto: impossibile eseguire il mapping di uno degli indirizzi IP nei flussi con la topologia del cliente in Azure e in locale (sito).
1. Ad alcuni nomi di \_campo \_viene aggiunto s o d. Questi NON indicano l'origine e la destinazione, ma indicano rispettivamente la stringa dei tipi di dati e il decimale.

### <a name="next-steps"></a>Passaggi successivi
Per ottenere risposte alle domande frequenti, vedere [Domande frequenti sull'analisi del traffico](traffic-analytics-faq.md) Per informazioni dettagliate sulle funzionalità, vedere la documentazione relativa all'analisi del [traffico](traffic-analytics.md)
