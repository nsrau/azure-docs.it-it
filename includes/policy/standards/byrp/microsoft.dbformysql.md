---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 07/22/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: cf56786656d830d2fea4c9e916f350fc392f5cf0
ms.sourcegitcommit: 3246e278d094f0ae435c2393ebf278914ec7b97b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/02/2020
ms.locfileid: "89380300"
---
## <a name="azure-security-benchmark"></a>Benchmark di sicurezza di Azure

[Azure Security Benchmark](../../../../articles/security/benchmarks/overview.md) offre consigli sulla protezione delle soluzioni cloud in Azure. Per informazioni sul mapping completo di questo servizio ad Azure Security Benchmark, vedere i [file di mapping di Azure Security Benchmark](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

Per informazioni sul mapping delle definizioni predefinite di Criteri di Azure per tutti i servizi di Azure a questo standard di conformità, vedere [Dettagli dell'iniziativa incorporata di conformità con le normative per Azure Security Benchmark](../../../../articles/governance/policy/samples/azure-security-benchmark.md).

|Dominio |ID controllo |Titolo controllo |Policy<br /><sub>(Portale di Azure)</sub> |Versione del criterio<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|Sicurezza di rete |1.1 |Proteggere le risorse usando i gruppi di sicurezza di rete o il firewall di Azure nella rete virtuale |[L'endpoint privato deve essere abilitato per i server MySQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7595c971-233d-4bcf-bd18-596129188c49) |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_EnablePrivateEndPoint_Audit.json) |
|Protezione dei dati |4.4 |Crittografare tutte le informazioni riservate in transito |[Il criterio Imponi connessione SSL deve essere abilitato per i server di database MySQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe802a67a-daf5-4436-9ea6-f6d821dd0c5d) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_EnableSSL_Audit.json) |
|Ripristino dei dati |9.1 |Garantire l'esecuzione regolare di backup automatizzati |[Il backup con ridondanza geografica deve essere abilitato per i database di Azure per MySQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F82339799-d096-41ae-8538-b108becf0970) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForMySQL_Audit.json) |
|Ripristino dei dati |9.2 |Eseguire backup completi del sistema e il backup di tutte le chiavi gestite dal cliente |[Il backup con ridondanza geografica deve essere abilitato per i database di Azure per MySQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F82339799-d096-41ae-8538-b108becf0970) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForMySQL_Audit.json) |

## <a name="cis-microsoft-azure-foundations-benchmark"></a>CIS Microsoft Azure Foundations Benchmark

Per informazioni sul mapping delle definizioni predefinite di Criteri di Azure per tutti i servizi di Azure a questo standard di conformità, vedere [Dettagli dell'iniziativa incorporata di conformità con le normative CIS Microsoft Azure Foundations Benchmark 1.1.0](../../../../articles/governance/policy/samples/cis-azure-1-1-0.md).
Per altre informazioni su questo standard di conformità, vedere [CIS Microsoft Azure Foundations Benchmark](https://www.cisecurity.org/benchmark/azure/).

|Dominio |ID controllo |Titolo controllo |Policy<br /><sub>(Portale di Azure)</sub> |Versione del criterio<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|Servizi di database |4.11 |Assicurarsi che il criterio "Imponi connessione SSL" sia abilitato per il server di database MySQL |[Il criterio Imponi connessione SSL deve essere abilitato per i server di database MySQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe802a67a-daf5-4436-9ea6-f6d821dd0c5d) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_EnableSSL_Audit.json) |

