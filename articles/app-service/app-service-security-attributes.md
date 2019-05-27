---
title: Attributi di sicurezza per il servizio App di Azure
description: Un elenco di controllo degli attributi di protezione per la valutazione di servizio App di Azure
services: app-service
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.service: app-service
ms.topic: conceptual
ms.date: 05/08/2019
ms.author: mbaldwin
ms.openlocfilehash: 1d7ab8008e8fbdb5f851f158d14f62bdea803f11
ms.sourcegitcommit: 13cba995d4538e099f7e670ddbe1d8b3a64a36fb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/22/2019
ms.locfileid: "66001694"
---
# <a name="security-attributes-for-azure-app-service"></a>Attributi di sicurezza per il servizio App di Azure

Questo articolo sono descritti gli attributi di sicurezza comuni incorporati nel servizio App di Azure.

[!INCLUDE [Security attributes header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>Prevenzione

| Attributo di sicurezza | Sì/No | Note |
|---|---|--|
| Crittografia dei dati inattivi (ad esempio la crittografia lato server, la crittografia lato server con chiavi gestite dal cliente e altre funzionalità di crittografia) | Sì | Contenuto del file del sito Web verrà archiviato in archiviazione di Azure, che il contenuto quando sono inattivi vengono crittografati automaticamente. Visualizzare [crittografia di archiviazione di Azure per dati inattivi](../storage/common/storage-service-encryption.md).<br><br>I segreti forniti dal cliente vengono crittografati a riposo. I segreti vengono crittografati a riposo mentre archiviati nei database di configurazione del servizio App.<br><br>I dischi collegati localmente, facoltativamente, utilizzabile come risorsa di archiviazione temporanea per siti Web (D:\local e % TMP %). I dischi collegati in locale non vengono crittografati a riposo. |
| Crittografia in transito (ad esempio la crittografia di ExpressRoute, nella crittografia di rete virtuale e la crittografia di rete)| Sì | I clienti possono configurare siti web per richiedere e uso di HTTPS per il traffico in ingresso. Vedere il blog post [come rendere un HTTPS a un servizio App di Azure solo](https://blogs.msdn.microsoft.com/benjaminperkins/2017/11/30/how-to-make-an-azure-app-service-https-only/). |
| Gestione della crittografia chiave (CMK, modalità BYOK e così via)| Sì | I clienti possono scegliere di archiviare i segreti dell'applicazione in Azure Key Vault e recuperarli in fase di esecuzione. Visualizzare [fa riferimento a usare Key Vault per servizio App e funzioni di Azure (anteprima)](app-service-key-vault-references.md).|
| Crittografia a livello di colonna (Azure Data Services)| N/D | |
| Chiamate API crittografate| Sì | Le chiamate di gestione per configurare il servizio App hanno luogo tramite [Azure Resource Manager](../azure-resource-manager/index.yml) chiamate tramite HTTPS. |

## <a name="network-segmentation"></a>Segmentazione di rete

| Attributo di sicurezza | Sì/No | Note |
|---|---|--|
| Supporto endpoint del servizio| Sì | Attualmente disponibile in anteprima per il servizio App. Visualizzare [restrizioni di accesso di servizio App di Azure](app-service-ip-restrictions.md). |
| Supportare l'inserimento di rete virtuale| Sì | Gli ambienti del servizio App sono implementazioni private del servizio App dedicato per un singolo cliente inserito nella rete virtuale del cliente. Visualizzare [introduzione per gli ambienti del servizio App](environment/intro.md). |
| Supporto per l'isolamento della rete e Firewalling| Sì | Per la variazione di multi-tenant pubblica del servizio App, i clienti possono configurare ACL (restrizioni IP) per bloccare il traffico in ingresso consentito di rete.  Visualizzare [restrizioni di accesso di servizio App di Azure](app-service-ip-restrictions.md).  Ambienti del servizio App vengono distribuiti direttamente nelle reti virtuali e pertanto possono essere protetti con Nsg. |
| Supporto di tunneling forzato| Sì | Ambienti del servizio App possono essere distribuiti in una rete virtuale del cliente in cui è configurato il tunneling forzato. I clienti devono seguire le istruzioni disponibili nel [configurare l'ambiente del servizio App con tunneling forzato](environment/forced-tunnel-support.md). |

## <a name="detection"></a>Rilevamento

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Monitoraggio di supporto (analitica di Log, Application insights e così via) di Azure| Sì | Servizio App si integra con Application Insights per le lingue che supportano Application Insights (completa di .NET Framework, .NET Core, Java e Node. js).  Visualizzare [sulle prestazioni di monitoraggio del servizio App Azure](../azure-monitor/app/azure-web-apps.md). Servizio App invia anche le metriche dell'applicazione in Monitoraggio di Azure. Visualizzare [monitorare le app nel servizio App di Azure](web-sites-monitor.md). |

## <a name="identity-and-access-management"></a>Gestione delle identità e dell'accesso

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Authentication| Sì | I clienti possono creare applicazioni nel servizio App che si integrano automaticamente con [Azure Active Directory (Azure AD)](../active-directory/index.md) , nonché altri provider di identità compatibili OAuth; vedere [autenticazione e autorizzazione nel Servizio App di Azure](overview-authentication-authorization.md). Per l'accesso a gestione agli asset di servizio App, tutti gli accessi sono controllato da una combinazione di entità di Azure AD autenticato e i ruoli RBAC di Azure Resource Manager. |
| Authorization| Sì | Per l'accesso a gestione agli asset di servizio App, tutti gli accessi sono controllato da una combinazione di entità di Azure AD autenticato e i ruoli RBAC di Azure Resource Manager.  |


## <a name="audit-trail"></a>Audit trail

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| La registrazione di piano di controllo e la gestione e controllo| Sì | Tutte le operazioni di gestione eseguite sugli oggetti di servizio App hanno luogo tramite [Azure Resource Manager](../azure-resource-manager/index.yml). I log cronologici di queste operazioni sono disponibili nel portale e tramite l'interfaccia CLI; visualizzare [operazioni di provider di risorse di Azure Resource Manager](../role-based-access-control/resource-provider-operations.md#microsoftweb) e [az monitor log attività](/cli/azure/monitor/activity-log). |
| Controllo e registrazione di piano dati | N. | Il piano dati per il servizio App è una condivisione file remota che contiene contenuto sito web distribuito di un cliente.  Non vi è alcun controllo di condivisione file remota. |

## <a name="configuration-management"></a>Gestione della configurazione

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Supporto di Gestione configurazione (controllo delle versioni di configurazione e così via).| Sì | Per le operazioni di gestione, lo stato di una configurazione del servizio App può essere esportato come un modello di Azure Resource Manager e con controllo delle versioni nel corso del tempo. Per le operazioni di runtime, i clienti possono gestire più versioni diverse in tempo reale di un'applicazione usando la funzionalità di slot di distribuzione di servizio App. | 

