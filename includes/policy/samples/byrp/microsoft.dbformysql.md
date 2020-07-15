---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 07/10/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 6eca6936420c05098329455bcdf84b0c82afb865
ms.sourcegitcommit: 0b2367b4a9171cac4a706ae9f516e108e25db30c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2020
ms.locfileid: "86276677"
---
|Nome<br /><sub>(Portale di Azure)</sub> |Descrizione |Effetto/i |Versione<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[La protezione dei dati BYOK (Bring Your Own Key) deve essere abilitata per i server MySQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F83cef61d-dbd1-4b20-a4fc-5fbc7da10833) |Questo criterio controlla i server MySQL dell'ambiente in cui non è abilitata la protezione dei dati BYOK (Bring Your Own Key). Per altri dettagli, vedere [https://aka.ms/mysqlbyok](https://aka.ms/mysqlbyok). |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_EnableByok_Audit.json) |
|[Il criterio Imponi connessione SSL deve essere abilitato per i server di database MySQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe802a67a-daf5-4436-9ea6-f6d821dd0c5d) |Questo criterio controlla tutti i server MySQL che non impongono la connessione SSL. Database di Azure per il server MySQL supporta la connessione alle applicazioni client tramite Secure Sockets Layer (SSL). L'imposizione di connessioni SSL tra il server di database e le applicazioni client garantisce la protezione da attacchi 'man in the middle' tramite la crittografia del flusso di dati tra il server e l'applicazione. |Audit, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_EnableSSL_Audit.json) |
|[Il backup con ridondanza geografica deve essere abilitato per i database di Azure per MySQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F82339799-d096-41ae-8538-b108becf0970) |Questo criterio controlla tutti i database di Azure per MySQL in cui non è abilitato il backup con ridondanza geografica. |Audit, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForMySQL_Audit.json) |
|[I server MySQL devono usare un endpoint servizio di rete virtuale](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3375856c-3824-4e0e-ae6a-79e011dd4c47) |Questo criterio controlla i server MySQL non configurati per l'uso di un endpoint servizio di rete virtuale. Per altri dettagli, vedere [https://aka.ms/mysqlvnet](https://aka.ms/mysqlvnet). |AuditIfNotExists, Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_VirtualNetworkServiceEndpoint_Audit.json) |
|[L'endpoint privato deve essere abilitato per i server MySQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7595c971-233d-4bcf-bd18-596129188c49) |Questo criterio controlla i server MySQL non configurati per l'uso di un endpoint privato. Per altri dettagli, vedere [https://aka.ms/mysqlprivatelink](https://aka.ms/mysqlprivatelink). |AuditIfNotExists, Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_EnablePrivateEndPoint_Audit.json) |
|[L'accesso alla rete pubblica deve essere disabilitato per i server MySQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd9844e8a-1437-4aeb-a32c-0c992f056095) |Questo criterio controlla i server MySQL dell'ambiente con accesso alla rete pubblica abilitato. Per altri dettagli, vedere [https://go.microsoft.com/fwlink/?linkid=2120014](https://go.microsoft.com/fwlink/?linkid=2120014). |Audit, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_DisablePublicNetworkAccess_Audit.json) |
