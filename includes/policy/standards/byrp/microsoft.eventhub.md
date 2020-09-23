---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 09/16/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 006a2ba41a5d9337291e09f3a7b93c875a1c261a
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90973201"
---
## <a name="azure-security-benchmark"></a>Benchmark di sicurezza di Azure

[Azure Security Benchmark](../../../../articles/security/benchmarks/overview.md) offre consigli sulla protezione delle soluzioni cloud in Azure. Per informazioni sul mapping completo di questo servizio ad Azure Security Benchmark, vedere i [file di mapping di Azure Security Benchmark](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

Per informazioni sul mapping delle definizioni predefinite di Criteri di Azure per tutti i servizi di Azure a questo standard di conformità, vedere [Dettagli dell'iniziativa incorporata di conformità con le normative per Azure Security Benchmark](../../../../articles/governance/policy/samples/azure-security-benchmark.md).

|Dominio |ID controllo |Titolo controllo |Policy<br /><sub>(Portale di Azure)</sub> |Versione del criterio<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|Sicurezza di rete |1.1 |Proteggere le risorse usando i gruppi di sicurezza di rete o il firewall di Azure nella rete virtuale |[Gli hub eventi devono usare un endpoint servizio di rete virtuale](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd63edb4a-c612-454d-b47d-191a724fcbf0) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_EventHub_AuditIfNotExists.json) |
|Registrazione e monitoraggio |2.3 |Abilitare la registrazione di controllo per le risorse di Azure |[È consigliabile abilitare i log di diagnostica in Hub eventi](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F83a214f7-d01a-484b-91a9-ed54470c9a6a) |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Event%20Hub/EventHub_AuditDiagnosticLog_Audit.json) |

## <a name="hipaa-hitrust-92"></a>HIPAA HITRUST 9.2

Per informazioni sul mapping delle definizioni predefinite di Criteri di Azure per tutti i servizi di Azure a questo standard di conformità, vedere [Dettagli dell'iniziativa predefinita di conformità alle normative per HIPAA HITRUST 9.2](../../../../articles/governance/policy/samples/hipaa-hitrust-9-2.md).
Per altre informazioni su questo standard di conformità, vedere [HIPAA HITRUST 9.2](https://www.hhs.gov/hipaa/for-professionals/security/laws-regulations/index.html).

|Dominio |ID controllo |Titolo controllo |Policy<br /><sub>(Portale di Azure)</sub> |Versione del criterio<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|Separazione nelle reti |0805.01m1Organizational.12 - 01.m |I gateway di sicurezza dell'organizzazione, ad esempio i firewall, impongono i criteri di sicurezza e sono configurati per filtrare il traffico tra domini, bloccare l'accesso non autorizzato e vengono usati per mantenere la separazione tra segmenti di rete cablati interni, wireless interni ed esterni, ad esempio Internet, incluse le reti perimetrali, e impongono i criteri di controllo di accesso per ogni dominio. |[Gli hub eventi devono usare un endpoint servizio di rete virtuale](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd63edb4a-c612-454d-b47d-191a724fcbf0) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_EventHub_AuditIfNotExists.json) |
|Separazione nelle reti |0806.01m2Organizational.12356 - 01.m |La rete dell'organizzazione è segmentata a livello logico e fisico con un perimetro di sicurezza definito e un set graduale di controlli, con le sottoreti per i componenti di sistema accessibili pubblicamente separate a livello logico dalla rete interna, in base ai requisiti aziendali; il traffico viene controllato in base alle funzionalità necessarie e alla classificazione dei dati/sistemi secondo la valutazione dei rischi e i rispettivi requisiti di sicurezza. |[Gli hub eventi devono usare un endpoint servizio di rete virtuale](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd63edb4a-c612-454d-b47d-191a724fcbf0) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_EventHub_AuditIfNotExists.json) |
|Separazione nelle reti |0894.01m2Organizational.7 - 01.m |Le reti vengono separate dalle reti a livello di produzione quando si esegue la migrazione di server fisici, applicazioni o dati a server virtualizzati. |[Gli hub eventi devono usare un endpoint servizio di rete virtuale](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd63edb4a-c612-454d-b47d-191a724fcbf0) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_EventHub_AuditIfNotExists.json) |
|Registrazione di controllo |1207.09aa2System.4 - 09.aa |I record di controllo vengono conservati per 90 giorni e quelli meno recenti vengono archiviati per un anno. |[È consigliabile abilitare i log di diagnostica in Hub eventi](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F83a214f7-d01a-484b-91a9-ed54470c9a6a) |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Event%20Hub/EventHub_AuditDiagnosticLog_Audit.json) |
|Controlli di rete |0863.09m2Organizational.910 - 09.m |L'organizzazione crea una configurazione del firewall che limita le connessioni tra le reti non attendibili e i componenti di sistema nell'ambiente delle informazioni riservate; tutte le modifiche apportate alla configurazione del firewall vengono aggiornate nel diagramma di rete. |[Gli hub eventi devono usare un endpoint servizio di rete virtuale](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd63edb4a-c612-454d-b47d-191a724fcbf0) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_EventHub_AuditIfNotExists.json) |

