---
title: Attributi di sicurezza per il servizio app Azure
description: Elenco di controllo degli attributi di sicurezza per la valutazione del servizio app Azure
services: app-service
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.service: app-service
ms.topic: conceptual
ms.date: 05/08/2019
ms.author: mbaldwin
ms.openlocfilehash: 299262610c027529749840720f46d6dc97a07b21
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/24/2019
ms.locfileid: "68442260"
---
# <a name="security-attributes-for-azure-app-service"></a>Attributi di sicurezza per il servizio app Azure

Questo articolo descrive gli attributi di sicurezza incorporati nel servizio app Azure.

[!INCLUDE [Security attributes header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>Prevenzione

| Attributo di sicurezza | Sì/No | Note |
|---|---|--|
| Crittografia dei servizi inattivi, ad esempio la crittografia lato server, la crittografia lato server con chiavi gestite dal cliente e altre funzionalità di crittografia | Sì | Il contenuto del file di sito Web viene archiviato in archiviazione di Azure, che crittografa automaticamente il contenuto inattivo. Vedere [crittografia di archiviazione di Azure per dati](../storage/common/storage-service-encryption.md)inattivi.<br><br>I segreti forniti dal cliente vengono crittografati a riposo. I segreti vengono crittografati a riposo durante l'archiviazione nei database di configurazione del servizio app.<br><br>I dischi collegati localmente possono essere facoltativamente usati come archiviazione temporanea da siti Web (D:\Local e% TMP%). I dischi collegati localmente non sono crittografati. |
| Crittografia in transito (ad esempio crittografia ExpressRoute, crittografia VNet e crittografia VNet-VNet)| Yes | I clienti possono configurare i siti Web in modo da richiedere e usare HTTPS per il traffico in ingresso. Vedere il post [di Blog come creare solo un servizio app Azure HTTPS](https://blogs.msdn.microsoft.com/benjaminperkins/2017/11/30/how-to-make-an-azure-app-service-https-only/). |
| Gestione della chiave di crittografia (CMK, BYOK e così via)| Sì | I clienti possono scegliere di archiviare i segreti dell'applicazione in Key Vault e recuperarli in fase di esecuzione. Vedere [usare i riferimenti Key Vault per il servizio app e funzioni di Azure (anteprima)](app-service-key-vault-references.md).|
| Crittografia a livello di colonna (servizi dati di Azure)| N/D | |
| Chiamate API crittografate| Sì | Le chiamate di gestione per configurare il servizio app si verificano tramite [Azure Resource Manager](../azure-resource-manager/index.yml) chiamate su HTTPS. |

## <a name="network-segmentation"></a>Segmentazione di rete

| Attributo di sicurezza | Sì/No | Note |
|---|---|--|
| Supporto per endpoint di servizio| Yes | Attualmente disponibile in anteprima per il servizio app. Vedere [restrizioni di accesso al servizio app Azure](app-service-ip-restrictions.md). |
| Supporto di VNet Injection| Sì | Gli ambienti del servizio app sono implementazioni private del servizio App dedicato a un singolo cliente inserito nella rete virtuale di un cliente. Vedere [Introduzione agli ambienti del servizio app](environment/intro.md). |
| Isolamento rete e supporto del firewall| Sì | Per la variante pubblica multi-tenant del servizio app, i clienti possono configurare gli ACL di rete (restrizioni IP) per bloccare il traffico in ingresso consentito.  Vedere [restrizioni di accesso al servizio app Azure](app-service-ip-restrictions.md).  Gli ambienti del servizio app vengono distribuiti direttamente nelle reti virtuali e possono quindi essere protetti con gruppi. |
| Supporto del tunneling forzato| Yes | Gli ambienti del servizio app possono essere distribuiti nella rete virtuale di un cliente in cui è configurato il tunneling forzato. I clienti devono seguire le istruzioni riportate in [configurare il ambiente del servizio app con](environment/forced-tunnel-support.md)il tunneling forzato. |

## <a name="detection"></a>Rilevamento

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Supporto di monitoraggio di Azure (log Analytics, Application Insights e così via)| Sì | Il servizio app si integra con Application Insights per le lingue che supportano Application Insights (Full .NET Framework, .NET Core, Java e node. JS).  Vedere [monitorare le prestazioni del servizio app Azure](../azure-monitor/app/azure-web-apps.md). Il servizio app invia anche le metriche dell'applicazione in monitoraggio di Azure. Vedere [monitorare le app nel servizio app Azure](web-sites-monitor.md). |

## <a name="identity-and-access-management"></a>Gestione delle identità e dell'accesso

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Authentication| Yes | I clienti possono compilare applicazioni nel servizio app che si integrano automaticamente con [Azure Active Directory (Azure ad)](../active-directory/index.yml) e altri provider di identità compatibili con OAuth; vedere [autenticazione e autorizzazione nel servizio app Azure](overview-authentication-authorization.md). Per la gestione dell'accesso alle risorse del servizio app, tutti gli accessi sono controllati da una combinazione di Azure AD entità autenticata e Azure Resource Manager ruoli RBAC. |
| Authorization| Sì | Per la gestione dell'accesso alle risorse del servizio app, tutti gli accessi sono controllati da una combinazione di Azure AD entità autenticata e Azure Resource Manager ruoli RBAC.  |


## <a name="audit-trail"></a>Audit trail

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Registrazione e controllo del piano di gestione e controllo| Yes | Tutte le operazioni di gestione eseguite sugli oggetti del servizio app si verificano tramite [Azure Resource Manager](../azure-resource-manager/index.yml). I log cronologici di queste operazioni sono disponibili sia nel portale che tramite l'interfaccia della riga di comando. vedere [Azure Resource Manager operazioni del provider di risorse](../role-based-access-control/resource-provider-operations.md#microsoftweb) e [AZ monitor Activity-log](/cli/azure/monitor/activity-log). |
| Registrazione e controllo del piano dati | No | Il piano dati per il servizio app è una condivisione file remota che contiene il contenuto del sito Web distribuito del cliente.  Nessun controllo della condivisione file remota. |

## <a name="configuration-management"></a>Gestione della configurazione

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Supporto per la gestione della configurazione (controllo delle versioni della configurazione e così via)| Sì | Per le operazioni di gestione, lo stato di una configurazione del servizio app può essere esportato come modello di Azure Resource Manager e con controllo delle versioni nel tempo. Per le operazioni di runtime, i clienti possono gestire più versioni live di un'applicazione usando la funzionalità slot di distribuzione del servizio app. | 

