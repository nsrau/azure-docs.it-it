---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 11/17/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 47cded1df30ede858698c6b01c5126279c549af2
ms.sourcegitcommit: c2dd51aeaec24cd18f2e4e77d268de5bcc89e4a7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/18/2020
ms.locfileid: "94746592"
---
|Nome<br /><sub>(Portale di Azure)</sub> |Descrizione |Effetto/i |Versione<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Il backup con ridondanza geografica deve essere abilitato per i database di Azure per MariaDB](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0ec47710-77ff-4a3d-9181-6aa50af424d0) |Il database di Azure per MariaDB consente di scegliere l'opzione di ridondanza per il server di database. Può essere impostato sull'archiviazione di backup con ridondanza geografica in cui i dati non solo vengono archiviati all'interno dell'area in cui è ospitato il server, ma vengono anche replicati in un'area associata per fornire un'opzione di ripristino in caso di errore di un'area. La configurazione dell'archiviazione con ridondanza geografica per il backup è consentita solo durante la creazione del server. |Audit, Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForMariaDB_Audit.json) |
|[I server MariaDB devono usare un endpoint servizio di rete virtuale](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fdfbd9a64-6114-48de-a47d-90574dc2e489) |Le regole del firewall basate su rete virtuale vengono usate per abilitare il traffico da una subnet specifica al database di Azure per MariaDB, garantendo al contempo che il traffico rimanga entro il limite di Azure. Questo criterio consente di controllare se il database di Azure per MariaDB dispone di un endpoint servizio di rete virtuale. |AuditIfNotExists, Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MariaDB_VirtualNetworkServiceEndpoint_Audit.json) |
|[L'endpoint privato deve essere abilitato per i server MariaDB](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0a1302fb-a631-4106-9753-f3d494733990) |Le connessioni endpoint privato impongono una comunicazione sicura tramite l'abilitazione della connettività privata al database di Azure per MariaDB. Configurare una connessione endpoint privato per consentire l'accesso al traffico proveniente solo da reti note e impedire l'accesso da tutti gli altri indirizzi IP, inclusi quelli all'interno di Azure. |AuditIfNotExists, Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MariaDB_EnablePrivateEndPoint_Audit.json) |
|[L'accesso alla rete pubblica deve essere disabilitato per i server MariaDB](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffdccbe47-f3e3-4213-ad5d-ea459b2fa077) |La disabilitazione della proprietà di accesso alla rete pubblica migliora la sicurezza, in quanto garantisce che al database di Azure per MariaDB sia possibile accedere solo da un endpoint privato. Questa configurazione disabilita l'accesso da tutti gli spazi indirizzi pubblici non compresi nell'intervallo di indirizzi IP di Azure e nega tutti gli accessi che corrispondono alle regole del firewall basate su indirizzo IP o rete virtuale. |Audit, Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MariaDB_DisablePublicNetworkAccess_Audit.json) |
