---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 07/22/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: e7750049ea7c2e504aec325c39b2760e93e3c9dd
ms.sourcegitcommit: 46f8457ccb224eb000799ec81ed5b3ea93a6f06f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87351802"
---
## <a name="azure-security-benchmark"></a>Benchmark di sicurezza di Azure

[Azure Security Benchmark](../../../../articles/security/benchmarks/overview.md) offre consigli sulla protezione delle soluzioni cloud in Azure. Per informazioni sul mapping completo di questo servizio ad Azure Security Benchmark, vedere i [file di mapping di Azure Security Benchmark](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

Per informazioni sul mapping delle definizioni predefinite di Criteri di Azure per tutti i servizi di Azure a questo standard di conformità, vedere [Dettagli dell'iniziativa incorporata di conformità con le normative per Azure Security Benchmark](../../../../articles/governance/policy/samples/azure-security-benchmark.md).

|Dominio |ID controllo |Titolo controllo |Policy<br /><sub>(Portale di Azure)</sub> |Versione del criterio<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|Sicurezza di rete |1.1 |Proteggere le risorse usando i gruppi di sicurezza di rete o il firewall di Azure nella rete virtuale |[Gli intervalli IP autorizzati devono essere definiti nei servizi Kubernetes](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0e246bcf-5f6f-4f87-bc6f-775d4712c7ea) |[1.0.1-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableIpRanges_KubernetesService_Audit.json) |
|Protezione dei dati |4.6 |Usare il controllo degli accessi in base al ruolo di Azure per controllare l'accesso alle risorse |[Nei servizi Kubernetes deve essere usato il controllo degli accessi in base al ruolo](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fac4a19c2-fa67-49b4-8ae5-0b2e78c49457) |[1.0.1-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableRBAC_KubernetesService_Audit.json) |
|Gestione delle vulnerabilità |5.3 |Distribuire una soluzione di gestione delle patch automatizzata per il software di terze parti |[I servizi Kubernetes devono essere aggiornati a una versione di Kubernetes non vulnerabile](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffb893a29-21bb-418c-a157-e99480ec364c) |[1.0.1-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_UpgradeVersion_KubernetesService_Audit.json) |
|Configurazione sicura |7.3 |Garantire la sicurezza delle configurazioni delle risorse di Azure |[[Anteprima]: I criteri di sicurezza pod devono essere definiti nei servizi Kubernetes](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3abeb944-26af-43ee-b83d-32aaf060fb94) |[1.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnablePSP_KubernetesService_Audit.json) |
|Configurazione sicura |7.9 |Implementare il monitoraggio automatizzato della configurazione per i servizi di Azure |[[Anteprima]: I criteri di sicurezza pod devono essere definiti nei servizi Kubernetes](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3abeb944-26af-43ee-b83d-32aaf060fb94) |[1.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnablePSP_KubernetesService_Audit.json) |

## <a name="cis-microsoft-azure-foundations-benchmark"></a>CIS Microsoft Azure Foundations Benchmark

Per informazioni sul mapping delle definizioni predefinite di Criteri di Azure per tutti i servizi di Azure a questo standard di conformità, vedere [Dettagli dell'iniziativa incorporata di conformità con le normative CIS Microsoft Azure Foundations Benchmark 1.1.0](../../../../articles/governance/policy/samples/cis-azure-1-1-0.md).
Per altre informazioni su questo standard di conformità, vedere [CIS Microsoft Azure Foundations Benchmark](https://www.cisecurity.org/benchmark/azure/).

|Dominio |ID controllo |Titolo controllo |Policy<br /><sub>(Portale di Azure)</sub> |Versione del criterio<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|Altre considerazioni sulla sicurezza |8.5 |Abilitare il controllo degli accessi in base al ruolo con il servizio Azure Kubernetes |[Nei servizi Kubernetes deve essere usato il controllo degli accessi in base al ruolo](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fac4a19c2-fa67-49b4-8ae5-0b2e78c49457) |[1.0.1-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableRBAC_KubernetesService_Audit.json) |

## <a name="nist-sp-800-171-r2"></a>NIST SP 800-171 R2

Per informazioni sul mapping delle definizioni predefinite di Criteri di Azure per tutti i servizi di Azure a questo standard di conformità, vedere [Dettagli dell'iniziativa predefinita di conformità alle normative per NIST SP 800-171 R2](../../../../articles/governance/policy/samples/nist-sp-800-171-r2.md).
Per altre informazioni su questo standard di conformità, vedere [NIST SP 800-171 R2](https://csrc.nist.gov/publications/detail/sp/800-171/rev-2/final).

|Dominio |ID controllo |Titolo controllo |Policy<br /><sub>(Portale di Azure)</sub> |Versione del criterio<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|Integrità del sistema e delle informazioni |3.14.1 |Identificare, segnalare e correggere gli errori di sistema in modo tempestivo. |[I servizi Kubernetes devono essere aggiornati a una versione di Kubernetes non vulnerabile](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffb893a29-21bb-418c-a157-e99480ec364c) |[1.0.1-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_UpgradeVersion_KubernetesService_Audit.json) |

