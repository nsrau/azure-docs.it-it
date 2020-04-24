---
title: Aggiornamento dello schema di Azure Analisi del traffico-2020 marzo | Microsoft Docs
description: Esempi di query con nuovi campi nello schema di Analisi del traffico.
services: network-watcher
documentationcenter: na
author: vinigam
manager: agummadi
editor: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/06/2020
ms.author: vinigam
ms.openlocfilehash: 4fe981576e3f6e58b0886d9c0d2eb2915d8b7720
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2020
ms.locfileid: "80396619"
---
# <a name="sample-queries-with-new-fields-in-the-traffic-analytics-schema-august-2019-schema-update"></a>Query di esempio con nuovi campi nello schema di Analisi del traffico (aggiornamento dello schema 2019 agosto)

Lo [schema del log analisi del traffico](https://docs.microsoft.com/azure/network-watcher/traffic-analytics-schema) include i nuovi campi seguenti: **SrcPublicIPs_s**, **DestPublicIPs_s** **NSGRule_s**. I nuovi campi forniscono informazioni sugli IP di origine e di destinazione e semplificano le query.

Nei prossimi mesi i seguenti campi meno recenti saranno deprecati: **VMIP_s**, **Subscription_g**, **Region_s** **,** **NSGRules_s**, **Subnet_s** **, VM_s, NIC_s** **, PublicIPs_s** **FlowCount_d.**

Nei tre esempi seguenti viene illustrato come sostituire i campi obsoleti con quelli nuovi.

## <a name="example-1-vmip_s-subscription_g-region_s-subnet_s-vm_s-nic_s-and-publicips_s-fields"></a>Esempio 1: campi VMIP_s, Subscription_g, Region_s, Subnet_s, VM_s, NIC_s e PublicIPs_s

Non è necessario dedurre i case di origine e di destinazione dal campo **FlowDirection_s** per i flussi AzurePublic e ExternalPublic. Può inoltre non essere appropriato usare il campo **FlowDirection_s** per un'appliance virtuale di rete.

```Old Kusto query
AzureNetworkAnalytics_CL
| where SubType_s == "FlowLog" and FASchemaVersion_s == "1"
| extend isAzureOrExternalPublicFlows = FlowType_s in ("AzurePublic", "ExternalPublic")
| extend SourceAzureVM = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'O', VM_s, "N/A"), VM1_s),
SourceAzureVMIP = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'O', VM_s, "N/A"), SrcIP_s),
SourceAzureVMSubscription = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'O', Subscription_g, "N/A"), Subscription1_g),
SourceAzureRegion = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'O', Region_s, "N/A"), Region1_s),
SourceAzureSubnet = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'O', Subnet_s, "N/A"), Subnet1_s),
SourceAzureNIC = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'O', NIC_s, "N/A"), NIC1_s),
DestAzureVM = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'I', VM_s, "N/A"), VM2_s),
DestAzureVMIP = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'I', VM_s, "N/A"), DestIP_s),
DestAzureVMSubscription = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'I', Subscription_g, "N/A"), Subscription2_g),
DestAzureRegion = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'I', Region_s, "N/A"), Region2_s),
DestAzureSubnet = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'I', Subnet_s, "N/A"), Subnet2_s),
DestAzureNIC = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'I', NIC_s, "N/A"), NIC2_s),
SourcePublicIPsAggregated = iif(isAzureOrExternalPublicFlows and FlowDirection_s == 'I', PublicIPs_s, "N/A"),
DestPublicIPsAggregated = iif(isAzureOrExternalPublicFlows and FlowDirection_s == 'O', PublicIPs_s, "N/A")
```


```New Kusto query
AzureNetworkAnalytics_CL
| where SubType_s == "FlowLog" and FASchemaVersion_s == "2"
| extend SourceAzureVM = iif(isnotempty(VM1_s), VM1_s, "N/A"),
SourceAzureVMIP = iif(isnotempty(SrcIP_s), SrcIP_s, "N/A"),
SourceAzureVMSubscription = iif(isnotempty(Subscription1_g), Subscription1_g, "N/A"),
SourceAzureRegion = iif(isnotempty(Region1_s), Region1_s, "N/A"),
SourceAzureSubnet = iif(isnotempty(Subnet1_s), Subnet1_s, "N/A"),
SourceAzureNIC = iif(isnotempty(NIC1_s), NIC1_s, "N/A"),
DestAzureVM = iif(isnotempty(VM2_s), VM2_s, "N/A"),
DestAzureVMIP = iif(isnotempty(DestIP_s), DestIP_s, "N/A"),
DestAzureVMSubscription = iif(isnotempty(Subscription2_g), Subscription2_g, "N/A"),
DestAzureRegion = iif(isnotempty(Region2_s), Region2_s, "N/A"),
DestAzureSubnet = iif(isnotempty(Subnet2_s), Subnet2_s, "N/A"),
DestAzureNIC = iif(isnotempty(NIC2_s), NIC2_s, "N/A"),
SourcePublicIPsAggregated = iif(isnotempty(SrcPublicIPs_s), SrcPublicIPs_s, "N/A"),
DestPublicIPsAggregated = iif(isnotempty(DestPublicIPs_s), DestPublicIPs_s, "N/A")
```

## <a name="example-2-nsgrules_s-field"></a>Esempio 2: campo NSGRules_s

Il campo precedente usava il formato:

Valore di indice <0) >|<NSG_ RuleName>|<Flow Direction>|<Flow Status>|<FlowCount ProcessedByRule>

I dati non vengono più aggregati in un gruppo di sicurezza di rete (NSG). Nello schema aggiornato **NSGList_s** contiene un solo NSG. **NSGRules** contiene anche una sola regola. È stata rimossa la formattazione complicata qui e in altri campi, come illustrato nell'esempio.

```Old Kusto query
AzureNetworkAnalytics_CL
| where SubType_s == "FlowLog" and FASchemaVersion_s == "1"
| extend NSGRuleComponents = split(NSGRules_s, "|")
| extend NSGName = NSGList_s // remains same
| extend NSGRuleName = NSGRuleComponents[1],
         FlowDirection = NSGRuleComponents[2],
         FlowStatus = NSGRuleComponents[3],
         FlowCountProcessedByRule = NSGRuleComponents[4]
| project NSGName, NSGRuleName, FlowDirection, FlowStatus, FlowCountProcessedByRule
```

```New Kusto query
AzureNetworkAnalytics_CL
| where SubType_s == "FlowLog" and FASchemaVersion_s == "2"
| extend NSGRuleComponents = split(NSGRules_s, "|")
| project NSGName = NSGList_s,
NSGRuleName = NSGRule_s ,
FlowDirection = FlowDirection_s,
FlowStatus = FlowStatus_s,
FlowCountProcessedByRule = AllowedInFlows_d + DeniedInFlows_d + AllowedOutFlows_d + DeniedOutFlows_d
```

## <a name="example-3-flowcount_d-field"></a>Esempio 3: campo FlowCount_d

Poiché i dati non vengono consociati attraverso la NSG, il **FlowCount_d** è semplicemente:

**AllowedInFlows_d** + **DeniedInFlows_d**DeniedInFlows_d + **AllowedOutFlows_d**AllowedOutFlows_d + **DeniedOutFlows_d**

Solo uno dei quattro campi sarà diverso da zero. Gli altri tre campi sono pari a zero. I campi vengono popolati per indicare lo stato e il conteggio nella scheda di interfaccia di rete in cui è stato acquisito il flusso.

Per illustrare queste condizioni:

- Se il flusso è consentito, verrà popolato uno dei campi con prefisso "Allowed".
- Se il flusso è stato negato, verrà popolato uno dei campi con prefisso "Denied".
- Se il flusso è in ingresso, uno dei campi suffissi "InFlows_d" verrà popolato.
- Se il flusso è in uscita, viene popolato uno dei campi con suffisso "OutFlows_d".

A seconda delle condizioni, si sa quale sarà il popolamento di uno dei quattro campi.

## <a name="next-steps"></a>Passaggi successivi

- Per le risposte alle domande frequenti, vedere [Traffic Analytics FAQ](traffic-analytics-faq.md) (Domande frequenti su Analisi del traffico).
- Per informazioni dettagliate sulle funzionalità, vedere [analisi del traffico documentazione](traffic-analytics.md).
