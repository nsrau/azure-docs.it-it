---
title: Controlli di sicurezza per il servizio app Azure
description: Elenco di controllo dei controlli di sicurezza per la valutazione del servizio app Azure
services: app-service
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.service: app-service
ms.topic: conceptual
ms.date: 05/08/2019
ms.author: mbaldwin
ms.openlocfilehash: 8fd4d19fa2c95107990fc7942e673ca5ad719df3
ms.sourcegitcommit: 86d49daccdab383331fc4072b2b761876b73510e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70743791"
---
# <a name="security-controls-for-azure-app-service"></a>Controlli di sicurezza per il servizio app Azure

Questo articolo descrive i controlli di sicurezza incorporati nel servizio app Azure.

[!INCLUDE [Security attributes header](../../includes/security-attributes-header.md)]

## <a name="network"></a>Rete

| Controllo di sicurezza | Sì/No | Note | Documentazione
|---|---|--|
| Supporto per endpoint di servizio| Sì | Attualmente disponibile in anteprima per il servizio app.| [Restrizioni di accesso al servizio app Azure](app-service-ip-restrictions.md)
| Supporto di VNet Injection| Sì | Gli ambienti del servizio app sono implementazioni private del servizio App dedicato a un singolo cliente inserito nella rete virtuale di un cliente. | [Introduzione agli ambienti del servizio app](environment/intro.md)
| Isolamento rete e supporto del firewall| Sì | Per la variante pubblica multi-tenant del servizio app, i clienti possono configurare gli ACL di rete (restrizioni IP) per bloccare il traffico in ingresso consentito.  Gli ambienti del servizio app vengono distribuiti direttamente nelle reti virtuali e possono quindi essere protetti con gruppi. | [Restrizioni di accesso al servizio app Azure](app-service-ip-restrictions.md)
| Supporto del tunneling forzato| Yes | Gli ambienti del servizio app possono essere distribuiti nella rete virtuale di un cliente in cui è configurato il tunneling forzato. | [Configurare Ambiente del servizio app con il tunneling forzato](environment/forced-tunnel-support.md)

## <a name="monitoring--logging"></a>Monitoraggio & registrazione

| Controllo di sicurezza | Sì/No | Note | Documentazione
|---|---|--|
| Supporto di monitoraggio di Azure (log Analytics, Application Insights e così via)| Yes | Il servizio app si integra con Application Insights per le lingue che supportano Application Insights (Full .NET Framework, .NET Core, Java e node. JS).  Vedere [monitorare le prestazioni del servizio app Azure](../azure-monitor/app/azure-web-apps.md). Il servizio app invia anche le metriche dell'applicazione in monitoraggio di Azure. | [Monitorare le app in Servizio app di Azure](web-sites-monitor.md)
| Registrazione e controllo del piano di gestione e controllo| Sì | Tutte le operazioni di gestione eseguite sugli oggetti del servizio app si verificano tramite [Azure Resource Manager](../azure-resource-manager/index.yml). I log cronologici di queste operazioni sono disponibili sia nel portale che tramite l'interfaccia della riga di comando. | [Operazioni del provider di risorse Azure Resource Manager](../role-based-access-control/resource-provider-operations.md#microsoftweb), [AZ monitor Activity-log](/cli/azure/monitor/activity-log)
| Registrazione e controllo del piano dati | No | Il piano dati per il servizio app è una condivisione file remota che contiene il contenuto del sito Web distribuito del cliente.  Nessun controllo della condivisione file remota. |

## <a name="identity"></a>identità

| Controllo di sicurezza | Sì/No | Note |  Documentazione
|---|---|--|
| Authentication| Sì | I clienti possono compilare applicazioni nel servizio app che si integrano automaticamente con [Azure Active Directory (Azure ad)](../active-directory/index.yml) e altri provider di identità compatibili con OAuth per l'accesso di gestione alle risorse del servizio app, tutti gli accessi sono controllati da un combinazione di Azure AD entità autenticata e Azure Resource Manager ruoli RBAC. | [Autenticazione e autorizzazione nel servizio app di Azure](overview-authentication-authorization.md)
| Authorization| Yes | Per la gestione dell'accesso alle risorse del servizio app, tutti gli accessi sono controllati da una combinazione di Azure AD entità autenticata e Azure Resource Manager ruoli RBAC.  | [Autenticazione e autorizzazione nel servizio app di Azure](overview-authentication-authorization.md)

## <a name="data-protection"></a>Protezione dati

| Controllo di sicurezza | Sì/No | Note | Documentazione
|---|---|--|
| Crittografia lato server: Chiavi gestite da Microsoft | Sì | Il contenuto del file di sito Web viene archiviato in archiviazione di Azure, che crittografa automaticamente il contenuto inattivo. <br><br>I segreti forniti dal cliente vengono crittografati a riposo. I segreti vengono crittografati a riposo durante l'archiviazione nei database di configurazione del servizio app.<br><br>I dischi collegati localmente possono essere facoltativamente usati come archiviazione temporanea da siti Web (D:\Local e% TMP%). I dischi collegati localmente non sono crittografati. | [Crittografia di archiviazione di Azure per dati inattivi](../storage/common/storage-service-encryption.md)
| Crittografia lato server: chiavi gestite dal cliente (BYOK) | Yes | I clienti possono scegliere di archiviare i segreti dell'applicazione in Key Vault e recuperarli in fase di esecuzione. | [Usare i riferimenti Key Vault per il servizio app e funzioni di Azure (anteprima)](app-service-key-vault-references.md)
| Crittografia a livello di colonna (servizi dati di Azure)| N/D | |
| Crittografia in transito (ad esempio crittografia ExpressRoute, crittografia VNet e crittografia VNet-VNet)| Sì | I clienti possono configurare i siti Web in modo da richiedere e usare HTTPS per il traffico in ingresso.  | [Come rendere solo HTTPS un servizio di app Azure](https://blogs.msdn.microsoft.com/benjaminperkins/2017/11/30/how-to-make-an-azure-app-service-https-only/) (post di Blog)
| Chiamate API crittografate| Sì | Le chiamate di gestione per configurare il servizio app si verificano tramite [Azure Resource Manager](../azure-resource-manager/index.yml) chiamate su HTTPS. |

## <a name="configuration-management"></a>Gestione della configurazione

| Controllo di sicurezza | Sì/No | Note | Documentazione
|---|---|--|
| Supporto per la gestione della configurazione (controllo delle versioni della configurazione e così via)| Yes | Per le operazioni di gestione, lo stato di una configurazione del servizio app può essere esportato come modello di Azure Resource Manager e con controllo delle versioni nel tempo. Per le operazioni di runtime, i clienti possono gestire più versioni live di un'applicazione usando la funzionalità slot di distribuzione del servizio app. | 

