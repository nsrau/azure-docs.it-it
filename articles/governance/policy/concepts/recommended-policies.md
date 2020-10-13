---
title: Criteri consigliati per i servizi di Azure
description: Viene descritto come trovare e applicare i criteri consigliati per i servizi di Azure, ad esempio macchine virtuali di Azure.
ms.date: 09/02/2020
ms.topic: conceptual
ms.customer: generated
ms.openlocfilehash: 5c297bcedd29fa8d9a2712850a57b66630cb52d9
ms.sourcegitcommit: a2d8acc1b0bf4fba90bfed9241b299dc35753ee6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/12/2020
ms.locfileid: "91951520"
---
# <a name="recommended-policies-for-azure-services"></a>Criteri consigliati per i servizi di Azure

I clienti che non hanno familiarità con i criteri di Azure spesso ricercano le definizioni dei criteri comuni per gestire e amministrare le proprie risorse. I criteri **consigliati** di criteri di Azure forniscono un elenco mirato di definizioni di criteri comuni da usare. L'esperienza dei **criteri consigliati** per le risorse supportate è incorporata nell'esperienza del portale per tale risorsa.

Per altri criteri di Azure predefiniti, vedere [definizioni predefinite di criteri di Azure](../samples/built-in-policies.md).

## <a name="azure-virtual-machines"></a>Macchine virtuali di Azure

I **criteri consigliati** per le [macchine virtuali di Azure](../../../virtual-machines/index.yml) sono disponibili nella pagina **Panoramica** per le macchine virtuali e nella scheda **funzionalità** . Nella scheda _criteri di Azure_ selezionare il testo "non configurato" o "# assegnato" per aprire un riquadro laterale con i criteri consigliati. Qualsiasi definizione di criteri già assegnata a un ambito di cui la macchina virtuale è membro è disabilitata. Selezionare i criteri consigliati da applicare alla macchina virtuale e selezionare **Assegna criteri** per creare un'assegnazione per ognuno di essi.

Quando un'organizzazione raggiunge la maturità con l' [organizzazione delle risorse e della gerarchia delle risorse](/azure/cloud-adoption-framework/ready/azure-best-practices/organize-subscriptions), è consigliabile eseguire la transizione di queste assegnazioni dei criteri da una per risorsa a livello di sottoscrizione o di [gruppo di gestione](../../management-groups/index.yml) .

### <a name="azure-virtual-machines-recommended-policies"></a>Criteri consigliati per le macchine virtuali di Azure

|Nome<br /><sub>(Portale di Azure)</sub> |Descrizione |Effetto/i |Versione<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controlla macchine virtuali in cui non è configurato il ripristino di emergenza](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0015ea4d-51ff-4ce3-8d8c-f3f8f0179a56) |Controlla le macchine virtuali in cui non è configurato il ripristino di emergenza. Per altre informazioni sul ripristino di emergenza, vedere [https://aka.ms/asr-doc](https://aka.ms/asr-doc). |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/RecoveryServices_DisasterRecovery_Audit.json) |
|[Controlla macchine virtuali che non usano dischi gestiti](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F06a78e20-9358-41c9-923c-fb736d382a4d) |Questo criterio controlla le macchine virtuali che non usano dischi gestiti |controllo |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/VMRequireManagedDisk_Audit.json) |
|[La soluzione Backup di Azure deve essere abilitata per le macchine virtuali](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F013e242c-8828-4970-87b3-ab247555486d) |Questo criterio consente di controllare se il servizio Backup di Azure è abilitato per tutte le macchine virtuali. Backup di Azure è una soluzione di backup con un clic economica che semplifica il ripristino dei dati ed è più facile da abilitare rispetto ad altri servizi di backup nel cloud. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/VirtualMachines_EnableAzureBackup_Audit.json) |

## <a name="next-steps"></a>Passaggi successivi

- Vedere gli esempi in [Esempi di Criteri di Azure](../samples/index.md).
- Leggere [Informazioni sugli effetti di Criteri](./effects.md).
- Informazioni su come [correggere le risorse non conformi](../how-to/remediate-resources.md).