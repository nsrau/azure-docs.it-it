---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 10/07/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: c4619610cc8e54f9fbe556e3d12668a680a2e261
ms.sourcegitcommit: b87c7796c66ded500df42f707bdccf468519943c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/08/2020
ms.locfileid: "91859844"
---
|Nome<br /><sub>(Portale di Azure)</sub> |Descrizione |Effetto/i |Versione<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Il backup con ridondanza geografica deve essere abilitato per i database di Azure per MariaDB](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0ec47710-77ff-4a3d-9181-6aa50af424d0) |Il database di Azure per MariaDB consente di scegliere l'opzione di ridondanza per il server di database. Può essere impostata su una risorsa di archiviazione di backup con ridondanza geografica in cui i dati non vengono archiviati solo nell'area in cui è ospitato il server, ma vengono anche replicati in un'area abbinata per fornire l'opzione di ripristino in caso di errore di un'area. La configurazione dell'archiviazione con ridondanza geografica per il backup è consentita solo durante la creazione del server |Audit, Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForMariaDB_Audit.json) |
|[I server MariaDB devono usare un endpoint servizio di rete virtuale](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fdfbd9a64-6114-48de-a47d-90574dc2e489) |Le regole del firewall basate sulla rete virtuale vengono usate per abilitare il traffico da una subnet specifica al database di Azure per MariaDB, garantendo al tempo stesso il traffico rimanente entro il limite di Azure. Questo criterio fornisce un modo per controllare se il database di Azure per MariaDB dispone di un endpoint del servizio di rete virtuale in uso. |AuditIfNotExists, Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MariaDB_VirtualNetworkServiceEndpoint_Audit.json) |
|[L'endpoint privato deve essere abilitato per i server MariaDB](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0a1302fb-a631-4106-9753-f3d494733990) |Le connessioni a endpoint privati applicano una comunicazione sicura abilitando la connettività privata al database di Azure per MariaDB. Configurare una connessione all'endpoint privato per consentire l'accesso al traffico proveniente solo da reti note e impedire l'accesso da tutti gli altri indirizzi IP, incluso in Azure. |AuditIfNotExists, Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MariaDB_EnablePrivateEndPoint_Audit.json) |
|[L'accesso alla rete pubblica deve essere disabilitato per i server MariaDB](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffdccbe47-f3e3-4213-ad5d-ea459b2fa077) |La disabilitazione della proprietà di accesso alla rete pubblica migliora la sicurezza garantendo che il database di Azure per MariaDB possa essere eseguito solo da un endpoint privato. Questa configurazione Disabilita l'accesso da qualsiasi spazio di indirizzi pubblico esterno all'intervallo IP di Azure e nega tutti gli account di accesso che corrispondono alle regole del firewall basate su rete virtuale o IP. |Audit, Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MariaDB_DisablePublicNetworkAccess_Audit.json) |
