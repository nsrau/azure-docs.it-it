---
title: Schema di analitica di traffico di Azure | Microsoft Docs
description: Informazioni sullo schema di Analitica di traffico per analizzare i log di flusso gruppo di sicurezza di rete di Azure.
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
ms.openlocfilehash: 491f19abfd87c28ede45e98a24f31fe7e599b18b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "64691424"
---
# <a name="schema-and-data-aggregation-in-traffic-analytics"></a>Aggregazione di schema e i dati in traffico Analitica

Analisi del traffico è una soluzione basata sul cloud che fornisce visibilità delle attività di utenti e applicazioni nelle reti cloud. Analisi del traffico analizza i log dei flussi dei gruppi di sicurezza di rete di Network Watcher per fornire informazioni dettagliate sul flusso del traffico nel cloud di Azure. Con Analisi del traffico è possibile:

- Visualizzare l'attività della rete nelle sottoscrizioni di Azure e identificare le aree sensibili.
- Identificare le minacce alla sicurezza e proteggere la rete con informazioni quali porte aperte, applicazioni che tentano l'accesso a Internet e macchine virtuali (VM) che si connettono a reti non autorizzate.
- Conoscere i modelli di flusso di traffico nelle aree di Azure e in Internet per ottimizzare le prestazioni e la capacità della distribuzione della rete.
- Trovare le configurazioni di rete errate che comportano connessioni non riuscite nella rete.
- Conoscere l'utilizzo della rete in byte, i pacchetti o flussi.

### <a name="data-aggregation"></a>Aggregazione dei dati

1. Tutti i log dei flussi in un gruppo di sicurezza tra "FlowIntervalStartTime_t" e "FlowIntervalEndTime_t" vengono acquisiti a intervalli di un minuto nell'account di archiviazione come BLOB prima di essere elaborato dal traffico Analitica. 
2. Intervallo di elaborazione di Analitica traffico predefinito è 60 minuti. Ciò significa che ogni 60 minuti che Analitica traffico sceglie i BLOB di archiviazione per l'aggregazione.
3. I flussi che hanno lo stesso indirizzo IP di origine, IP di destinazione, porta di destinazione, nome NSG, regola NSG, direzione del flusso e trasporto layer protocol (TCP o UDP) (Nota: Porta di origine viene escluso per l'aggregazione) viene eseguito in un singolo flusso per il traffico Analitica
4. Questo record singolo è decorati (dettagli nella sezione seguente) e inseriti in Log Analitica dal traffico Analytics.This processo può richiedere fino a 1 ora massima.
5. Campo FlowStartTime_t indica la prima occorrenza di questo tipo un aggregato del flusso (stesso quattro tuple) nel log di flusso tra "FlowIntervalStartTime_t" e "FlowIntervalEndTime_t" intervallo di elaborazione. 
6. Per qualsiasi risorsa nel trust Anchor, i flussi indicati nell'interfaccia utente sono flussi totali visualizzati per il gruppo di sicurezza, ma nel Log Anlaytics utente visualizzerà solo il record singolo, ridotto. Per visualizzare tutti i flussi, usare il campo blob_id, che è possibile fare riferimento da un archivio. Il flusso totale Conteggio per che record corrispondenti ai criteri i singoli flussi visibili nel blob.


### <a name="fields-used-in-traffic-analytics-schema"></a>Campi usati nello schema di Analitica di traffico

Il traffico Analitica è basato su Log Analitica, query personalizzate che può essere eseguito sui dati contrassegnate dal traffico Analitica e impostare avvisi per lo stesso.

Di seguito sono riportati i campi nello schema di e quali indicare.

| Campo | Format | Commenti | 
|:---   |:---    |:---  |
| TableName | AzureNetworkAnalytics_CL | Tabella per i dati di traffico Anlaytics
| SubType_s | FlowLog | Sottotipo per i log dei flussi |
| FASchemaVersion_s |   1   | Versione di schema. Non riflette la versione del Log di flusso NSG |
| TimeProcessed_t   | Data e ora in formato UTC  | Ora in cui il traffico di Analitica elaborato i log dei flussi non elaborati dall'account di archiviazione |
| FlowIntervalStartTime_t | Data e ora in formato UTC |  Ora di inizio dell'intervallo di elaborazione del log di flusso. Si tratta di tempo da cui viene misurato l'intervallo di flusso |
| FlowIntervalEndTime_t | Data e ora in formato UTC | Ora dell'intervallo di elaborazione del log di flusso di fine |
| FlowStartTime_t | Data e ora in formato UTC |  Prima occorrenza del flusso (che verrà ottenere aggregato) nell'intervallo di elaborazione del log di flusso tra "FlowIntervalStartTime_t" e "FlowIntervalEndTime_t". Questo flusso Ottiene aggregato in base alla logica di aggregazione |
| FlowEndTime_t | Data e ora in formato UTC | Ultima occorrenza del flusso (che verrà ottenere aggregato) nell'intervallo di elaborazione del log di flusso tra "FlowIntervalStartTime_t" e "FlowIntervalEndTime_t". In termini di v2 di log di flusso, questo campo contiene l'ora di inizio (contrassegnato come "B" nel record di flusso non elaborati) l'ultimo flusso con la stessa tupla quattro elementi |
| FlowType_s |  * IntraVNet <br> * Tra reti virtuali <br> * S2S <br> * P2S <br> * AzurePublic <br> * ExternalPublic <br> * MaliciousFlow <br> * Privato sconosciuto <br> * Sconosciuto | Definizione nelle note per la tabella seguente |
| SrcIP_s | Indirizzo IP di origine | Verrà lasciato vuoto in caso di AzurePublic ed ExternalPublic flussi |
| DestIP_s | Indirizzo IP di destinazione | Verrà lasciato vuoto in caso di AzurePublic ed ExternalPublic flussi |
| VMIP_s | Indirizzo IP della VM | Utilizzato per i flussi AzurePublic ed ExternalPublic |
| PublicIP_s | Indirizzi IP pubblici | Utilizzato per i flussi AzurePublic ed ExternalPublic |
| DestPort_d | Porta di destinazione | Porta in corrispondenza del quale il traffico è in arrivo | 
| L4Protocol_s  | * T <br> * U  | Protocollo di trasporto. T = TCP <br> U = UDP | 
| L7Protocol_s  | Nome del protocollo | Derivate dalla porta di destinazione |
| FlowDirection_s | * Si = in ingresso<br> * O = in uscita | Direzione del flusso in memoria e dalla sicurezza di rete in base ai log di flusso | 
| FlowStatus_s  | * A = consentito dalla regola NSG <br> * 1!d = negati dalla regola di sicurezza di rete  | Stato del flusso consentiti/nblocked dalla sicurezza di rete in base ai log di flusso |
| NSGList_s | \<SUBSCRIPTIONID>\/<RESOURCEGROUP_NAME>\/<NSG_NAME> | Gruppo sicurezza di rete (NSG) associati al flusso |
| NSGRules_s | \<Indice valore 0) >< NSG_RULENAME >\<direzione di flusso >\<lo stato del flusso >\<FlowCount ProcessedByRule > |  Regola di sicurezza di rete consentito o negato questo flusso |
| NSGRuleType_s | * Definito dall'utente * predefinito |   Il tipo di regola di sicurezza di rete utilizzato dal flusso |
| MACAddress_s | Indirizzo MAC | Indirizzo MAC dell'interfaccia di rete in cui è stato acquisito il flusso |
| Subscription_s | Sottoscrizione della rete virtuale di Azure o l'interfaccia di rete / macchina virtuale viene popolata in questo campo | Applicabile solo per FlowType = S2S, P2S, AzurePublic, ExternalPublic, MaliciousFlow e UnknownPrivate tipi di flusso (tipi di flusso in cui un solo lato è azure) |
| Subscription1_s | ID sottoscrizione | ID sottoscrizione della rete virtuale / di interfaccia di rete / macchina virtuale a cui appartiene l'indirizzo IP di origine nel flusso |
| Subscription2_s | ID sottoscrizione | ID sottoscrizione della rete virtuale / di interfaccia di rete / macchina virtuale a cui appartiene l'indirizzo IP di destinazione nel flusso |
| Region_s | Area di Azure della rete virtuale / di interfaccia di rete / macchina virtuale a cui appartiene l'indirizzo IP nel flusso | Applicabile solo per FlowType = S2S, P2S, AzurePublic, ExternalPublic, MaliciousFlow e UnknownPrivate tipi di flusso (tipi di flusso in cui un solo lato è azure) |
| Region1_s | Area di Azure | Area di Azure della rete virtuale / di interfaccia di rete / macchina virtuale a cui appartiene l'indirizzo IP di origine nel flusso |
| Region2_s | Area di Azure | Area della rete virtuale a cui appartiene l'indirizzo IP di destinazione nel flusso di Azure |
| NIC_s | \<resourcegroup_Name>\/\<NetworkInterfaceName> |  Interfaccia di rete associato alla macchina virtuale invia o riceve il traffico |
| NIC1_s | < resourcegroup_Name > /\<NetworkInterfaceName > | Interfaccia di rete associato con l'indirizzo IP di origine nel flusso |
| NIC2_s | < resourcegroup_Name > /\<NetworkInterfaceName > | Interfaccia di rete associato con l'indirizzo IP di destinazione nel flusso |
| VM_s | <resourcegroup_Name>\/\<NetworkInterfaceName> | Associata con l'interfaccia di rete NIC_s della macchina virtuale |
| VM1_s | < resourcegroup_Name > /\<VirtualMachineName > | Associata con l'indirizzo IP di origine nel flusso della macchina virtuale |
| VM2_s | < resourcegroup_Name > /\<VirtualMachineName > | Associata con l'indirizzo IP di destinazione nel flusso della macchina virtuale |
| Subnet_s | < ResourceGroup_Name > / < VNET_Name > /\<SubnetName > | Subnet associata la NIC_s |
| Subnet1_s | < ResourceGroup_Name > / < VNET_Name > /\<SubnetName > | Subnet associate con l'indirizzo IP di origine nel flusso |
| Subnet2_s | < ResourceGroup_Name > / < VNET_Name > /\<SubnetName >    | Subnet associate con l'indirizzo IP di destinazione nel flusso |
| ApplicationGateway1_s | \<SubscriptionID>/\<ResourceGroupName>/\<ApplicationGatewayName> | Gateway applicazione associato con l'indirizzo IP di origine nel flusso | 
| ApplicationGateway2_s | \<SubscriptionID>/\<ResourceGroupName>/\<ApplicationGatewayName> | Gateway applicazione associato all'indirizzo IP di destinazione nel flusso |
| LoadBalancer1_s | \<SubscriptionID>/\<ResourceGroupName>/\<LoadBalancerName> | Servizio di bilanciamento del carico associato con l'indirizzo IP di origine nel flusso |
| LoadBalancer2_s | \<SubscriptionID>/\<ResourceGroupName>/\<LoadBalancerName> | Servizio di bilanciamento del carico associato con l'indirizzo IP di destinazione nel flusso |
| LocalNetworkGateway1_s | \<SubscriptionID>/\<ResourceGroupName>/\<LocalNetworkGatewayName> | Gateway di rete locale associato con l'indirizzo IP di origine nel flusso |
| LocalNetworkGateway2_s | \<SubscriptionID>/\<ResourceGroupName>/\<LocalNetworkGatewayName> | Gateway di rete locale associato con l'indirizzo IP di destinazione nel flusso |
| ConnectionType_s | I valori possibili sono VNetPeering, gateway VPN ed ExpressRoute |    Tipo di connessione |
| ConnectionName_s | \<SubscriptionID>/\<ResourceGroupName>/\<ConnectionName> | Connection Name (Nome connessione) |
| ConnectingVNets_s | Elenco separato da spazi dei nomi di rete virtuale | Topologia hub- spoke, in caso di reti virtuali dell'hub verranno popolate qui |
| Country_s | Codice paese (ISO 3166-1 alfa-2) due lettere | Popolato per il tipo di flusso ExternalPublic. Tutti gli indirizzi IP nel campo PublicIPs_s condivideranno lo stesso codice paese |
| AzureRegion_s | Percorsi di area di Azure | Popolato per il tipo di flusso AzurePublic. Tutti gli indirizzi IP nel campo PublicIPs_s condivideranno l'area di Azure |
| AllowedInFlows_d | | Numero di flussi in ingresso che erano consentite. Rappresenta il numero di flussi che condivisa la stessa tupla quattro elementi in ingresso all'interfaccia netweork in corrispondenza del quale è stato acquisito il flusso | 
| DeniedInFlows_d |  | Numero di flussi in ingresso che sono state negate. (In ingresso all'interfaccia di rete in cui è stato acquisito il flusso) |
| AllowedOutFlows_d | | Numero di flussi in uscita che sono state consentite (in uscita all'interfaccia di rete in cui è stato acquisito il flusso) |
| DeniedOutFlows_d  | | Numero di flussi in uscita che sono stati negati (in uscita all'interfaccia di rete in cui è stato acquisito il flusso) |
| FlowCount_d | Operazione deprecata. Totale flussi che soddisfano le stessa tupla quattro elementi. In caso di tipi di flusso ExternalPublic e AzurePublic, conteggio includerà i flussi da anche diversi indirizzi IP pubblico.
| InboundPackets_d | Pacchetti ricevuti come acquisita all'interfaccia di rete in cui è stata applicata la regola NSG | Questo campo viene popolato solo per lo schema del log versione 2 del NSG flow |
| OutboundPackets_d  | Pacchetti inviati al livello di interfaccia di rete in cui è stata applicata la regola NSG dell'acquisizione | Questo campo viene popolato solo per lo schema del log versione 2 del NSG flow |
| InboundBytes_d |  Byte ricevuti al livello di interfaccia di rete in cui è stata applicata la regola NSG dell'acquisizione | Questo campo viene popolato solo per lo schema del log versione 2 del NSG flow |
| OutboundBytes_d | Byte inviati al livello di interfaccia di rete in cui è stata applicata la regola NSG dell'acquisizione | Questo campo viene popolato solo per lo schema del log versione 2 del NSG flow |
| CompletedFlows_d  |  | Questo campo viene popolato con valore diverso da zero solo per lo schema del log versione 2 del NSG flow |
| PublicIPs_s | <PUBLIC_IP>\|\<FLOW_STARTED_COUNT>\|\<FLOW_ENDED_COUNT>\|\<OUTBOUND_PACKETS>\|\<INBOUND_PACKETS>\|\<OUTBOUND_BYTES>\|\<INBOUND_BYTES> | Le voci separate da barre |
    
### <a name="notes"></a>Note
    
1. In caso di flussi AzurePublic ed ExternalPublic, il cliente proprietario che IP della macchina virtuale di Azure viene popolato nel campo VMIP_s, mentre gli indirizzi IP pubblici in corso il popolamento del campo PublicIPs_s. Per questi tipi di due flusso, dovremmo usiamo VMIP_s e PublicIPs_s anziché SrcIP_s e DestIP_s campi. Per gli indirizzi di AzurePublic ed ExternalPublicIP Aggreghiamo ulteriormente, in modo che il numero di record inseriti all'area di lavoro dei clienti log analitica è minimo. (Questo campo sarà deprecato a breve e sarebbe necessario usare SrcIP_ e DestIP_s a seconda che una VM di azure sia l'origine o destinazione del flusso di) 
1. Dettagli per i tipi di flusso: È basata sugli indirizzi IP coinvolte nel flusso, classificare i flussi per i tipi di flusso seguenti: 
1. IntraVNet: entrambi gli indirizzi IP nel flusso si trovano nella stessa rete virtuale di Azure. 
1. Tra reti virtuali - indirizzi IP nel flusso si trovano nelle reti virtuali di Azure diverse due. 
1. – S2S (da sito a sito) uno degli indirizzi IP appartiene a rete virtuale di Azure mentre l'altro indirizzo IP appartiene alla rete del cliente (Site) connessa alla rete virtuale di Azure tramite gateway VPN o Expressroute. 
1. P2S - (da punto a sito) appartiene uno degli indirizzi IP per rete virtuale di Azure mentre l'altro indirizzo IP appartiene alla rete del cliente (sito) connesse alla rete virtuale di Azure tramite gateway VPN.
1. AzurePublic - una degli indirizzi IP appartenente alla rete virtuale di Azure mentre l'altro indirizzo IP appartiene agli indirizzi IP pubblico interno di Azure di proprietà di Microsoft. Il cliente è proprietario gli indirizzi IP pubblici non far parte di questo tipo di flusso. Ad esempio, tutti i clienti di proprietà della macchina virtuale l'invio di traffico a un servizio di Azure (endpoint di archiviazione) verrà suddivisi in questo tipo di flusso. 
1. ExternalPublic - appartiene uno degli indirizzi IP per rete virtuale di Azure mentre l'altro indirizzo IP è un indirizzo IP pubblico che non sia in Azure, non è segnalato come dannoso nei feed Centro sicurezza di AZURE che utilizza il traffico Analitica per l'intervallo di elaborazione tra " FlowIntervalStartTime_t"e"FlowIntervalEndTime_t". 
1. MaliciousFlow - uno degli indirizzi IP appartenenti a rete virtuale di azure mentre l'altro indirizzo IP è un indirizzo IP pubblico che non si trova in Azure e viene indicato come dannoso nei feed Centro sicurezza di AZURE che utilizza il traffico Analitica per l'intervallo di elaborazione tra" FlowIntervalStartTime_t"e"FlowIntervalEndTime_t". 
1. UnknownPrivate - uno degli indirizzi IP appartenenti a rete virtuale di Azure mentre l'altro indirizzo IP appartiene a un intervallo di IP privati come definito in RFC 1918 e non può essere mappato dal traffico Analitica a un sito o rete virtuale di Azure di proprietà del cliente.
1. Sconosciuto: non è possibile eseguire il mapping tra l'indirizzo IP indirizzi per i flussi con la topologia dei clienti in Azure così come in locale (sito).
1. Alcuni nomi di campo vengono aggiunti con s o d. Questi comporta l'origine e destinazione.

### <a name="next-steps"></a>Fasi successive
Per ottenere le risposte alle domande più frequenti, vedere [analitica domande frequenti di traffico](traffic-analytics-faq.md) per visualizzare informazioni dettagliate sulle funzionalità, vedere [documentazione analitica del traffico](traffic-analytics.md)
    


    


