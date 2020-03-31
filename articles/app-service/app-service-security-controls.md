---
title: Controlli di sicurezza
description: Trovare un elenco di controllo dei controlli di sicurezza per la valutazione del servizio app di Azure per l'organizzazione.
author: msmbaldwin
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: 2586821c4c48f809efb5408c3cdae5e42e3b3fcf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74671442"
---
# <a name="security-controls-for-azure-app-service"></a>Controlli di sicurezza per il servizio app di AzureSecurity controls for Azure App Service

Questo articolo documenta i controlli di sicurezza incorporati nel servizio app di Azure.This article documents the security controls built into Azure App Service.

[!INCLUDE [Security controls header](../../includes/security-controls-header.md)]

## <a name="network"></a>Rete

| Controllo di sicurezza | Sì/No | Note | Documentazione
|---|---|--|
| Supporto dell'endpoint di servizioService endpoint support| Sì | Disponibile per il servizio app.| [Restrizioni di accesso al servizio app di AzureAzure App Service Access Restrictions](app-service-ip-restrictions.md)
| Supporto per l'inserimento di vNet| Sì | Gli ambienti del servizio app sono implementazioni private del servizio app dedicate a un singolo cliente inserito nella rete virtuale di un cliente. | [Introduzione agli ambienti del servizio app](environment/intro.md)
| Supporto per l'isolamento della rete e il firewall| Sì | Per la variante pubblica multi-tenant del servizio app, i clienti possono configurare gli ACL di rete (restrizioni IP) per bloccare il traffico in ingresso consentito.  Gli ambienti del servizio app vengono distribuiti direttamente nelle reti virtuali e pertanto possono essere protetti con gruppi di sicurezza di rete. | [Restrizioni di accesso al servizio app di AzureAzure App Service Access Restrictions](app-service-ip-restrictions.md)
| Supporto per il tunneling forzato| Sì | Gli ambienti di servizio app possono essere distribuiti nella rete virtuale di un cliente in cui è configurato il tunneling forzato. | [Configurare Ambiente del servizio app con il tunneling forzato](environment/forced-tunnel-support.md)

## <a name="monitoring--logging"></a>Monitoraggio della registrazione dei &

| Controllo di sicurezza | Sì/No | Note | Documentazione
|---|---|--|
| Supporto per il monitoraggio di Azure (analisi dei log, informazioni dettagliate sulle app e così via)Azure monitoring support (Log analytics, App insights, etc.)| Sì | Il servizio app si integra con Application Insights per i linguaggi che supportano Application Insights (Full .NET Framework, .NET Core, Java e Node.JS).  Vedere [Monitorare le prestazioni del servizio app di Azure.](../azure-monitor/app/azure-web-apps.md) App Service also sends application metrics into Azure Monitor. | [Monitorare le app nel servizio app di AzureMonitor apps in Azure App Service](web-sites-monitor.md)
| Controllo e gestione del controllo e dell'audit degli aerei| Sì | Tutte le operazioni di gestione eseguite sugli oggetti del servizio app vengono eseguite tramite [Azure Resource Manager.](../azure-resource-manager/index.yml) I log cronologici di queste operazioni sono disponibili sia nel portale che tramite l'interfaccia della riga di comando. | [Operazioni del provider](../role-based-access-control/resource-provider-operations.md#microsoftweb)di risorse di Azure Resource Manager , [az monitorare il log attività](/cli/azure/monitor/activity-log)
| Registrazione e controllo dei piani dati | No | Il piano dati per il servizio app è una condivisione file remota contenente il contenuto del sito Web distribuito di un cliente.  Non esiste alcun controllo della condivisione file remota. |

## <a name="identity"></a>Identità

| Controllo di sicurezza | Sì/No | Note |  Documentazione
|---|---|--|
| Authentication| Sì | I clienti possono creare applicazioni nel servizio app che si integrano automaticamente con [Azure Active Directory (Azure AD)](../active-directory/index.yml) e altri provider di identità compatibili con OAuth Per la gestione dell'accesso agli asset del servizio app, tutti gli accessi sono controllati da una combinazione di ruoli dell'entità autenticata di Azure AD e dei ruoli RBAC di Azure Resource Manager di Azure. | [Autenticazione e autorizzazione nel servizio app di Azure](overview-authentication-authorization.md)
| Autorizzazione| Sì | Per l'accesso di gestione agli asset del servizio app, tutti gli accessi sono controllati da una combinazione di ruoli RBAC dell'entità autenticata di Azure AD e di Azure Resource Manager.For management access to App Service assets, all access is controlled by a combination of Azure AD authenticated principal and Azure Resource Manager RBAC roles.  | [Autenticazione e autorizzazione nel servizio app di Azure](overview-authentication-authorization.md)

## <a name="data-protection"></a>Protezione dei dati

| Controllo di sicurezza | Sì/No | Note | Documentazione
|---|---|--|
| Crittografia lato server inattivi: chiavi gestite da MicrosoftServer-side encryption at rest: Microsoft-managed keys | Sì | Il contenuto dei file del sito Web viene archiviato in Archiviazione di Azure, che crittografa automaticamente il contenuto inattivi. <br><br>I segreti forniti dal cliente vengono crittografati a irto. I segreti vengono crittografati inattivi mentre vengono archiviati nei database di configurazione del servizio app.<br><br>I dischi collegati localmente possono essere utilizzati facoltativamente come archiviazione temporanea dai siti Web (D: , local e %TMP%). I dischi collegati localmente non sono crittografati inattivi. | [Crittografia di Archiviazione di Azure per i dati inattiviAzure Storage encryption for data at rest](../storage/common/storage-service-encryption.md)
| Crittografia lato server inattivi: chiavi gestite dal cliente (BYOK)Server-side encryption at rest: customer-managed keys (BYOK) | Sì | I clienti possono scegliere di archiviare i segreti dell'applicazione nell'insieme di credenziali delle chiavi e recuperarli in fase di esecuzione. | [Usare i riferimenti a Key Vault per Servizio app e Funzioni di Azure (anteprima)](app-service-key-vault-references.md)
| Crittografia a livello di colonna (Azure Data Services)Column level encryption (Azure Data Services)| N/D | |
| Crittografia in transito (ad esempio crittografia ExpressRoute, nella crittografia della rete virtuale e crittografia VNet-VNet )| Sì | I clienti possono configurare i siti Web in modo da richiedere e utilizzare HTTPS per il traffico in ingresso.  | [Come creare solo HTTPS per un servizio app di Azure](https://blogs.msdn.microsoft.com/benjaminperkins/2017/11/30/how-to-make-an-azure-app-service-https-only/) (post di blog)How to make an Azure App Service HTTPS only (blog post)
| Chiamate API crittografate| Sì | Le chiamate di gestione per configurare il servizio app avvengono tramite le chiamate di [Azure Resource Manager](../azure-resource-manager/index.yml) tramite HTTPS. |

## <a name="configuration-management"></a>Gestione della configurazione

| Controllo di sicurezza | Sì/No | Note | Documentazione
|---|---|--|
| Supporto per la gestione della configurazione (controllo delle versioni della configurazione e così via)| Sì | Per le operazioni di gestione, lo stato di una configurazione del servizio app può essere esportato come modello di Azure Resource Manager e sottoposto a controllo delle versioni nel tempo. Per le operazioni di runtime, i clienti possono gestire più versioni attive di un'applicazione usando la funzionalità di distribuzione del servizio app. | 

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sui controlli di sicurezza incorporati nei servizi di Azure.Learn more about the [built-in security controls across Azure services](../security/fundamentals/security-controls.md).
