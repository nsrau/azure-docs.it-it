---
title: Controlli di sicurezza per il servizio app Azure
description: Trovare un elenco di controllo dei controlli di sicurezza per la valutazione del servizio app Azure per la propria organizzazione.
author: msmbaldwin
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: 93e5123e5b61c9013177f7f3c908578b68da52d6
ms.sourcegitcommit: 648c8d250106a5fca9076a46581f3105c23d7265
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/27/2020
ms.locfileid: "88962928"
---
# <a name="security-controls-for-azure-app-service"></a>Controlli di sicurezza per il servizio app Azure

Questo articolo descrive i controlli di sicurezza incorporati nel servizio app Azure.

[!INCLUDE [Security controls header](../../includes/security-controls-header.md)]

## <a name="network"></a>Rete

| Controllo di sicurezza | Sì/No | Note | Documentazione
|---|---|--|
| Supporto endpoint di servizio| Sì | Disponibile per il servizio app.| [Restrizioni di accesso al servizio app Azure](app-service-ip-restrictions.md)
| Supporto aggiunta rete virtuale| Sì | Gli ambienti del servizio app sono implementazioni private del servizio App dedicato a un singolo cliente inserito nella rete virtuale di un cliente. | [Introduzione agli ambienti del servizio app](environment/intro.md)
| Supporto isolamento rete e protezione tramite firewall| Sì | Per la variante pubblica multi-tenant del servizio app, i clienti possono configurare gli ACL di rete (restrizioni IP) per bloccare il traffico in ingresso consentito.  Gli ambienti del servizio app vengono distribuiti direttamente nelle reti virtuali e possono quindi essere protetti con gruppi. | [Restrizioni di accesso al servizio app Azure](app-service-ip-restrictions.md)
| Supporto del tunneling forzato| Sì | Gli ambienti del servizio app possono essere distribuiti nella rete virtuale di un cliente in cui è configurato il tunneling forzato. | [Configurare Ambiente del servizio app con il tunneling forzato](environment/forced-tunnel-support.md)

## <a name="monitoring--logging"></a>Monitoraggio e registrazione

| Controllo di sicurezza | Sì/No | Note | Documentazione
|---|---|--|
| Supporto monitoraggio di Azure (analisi dei log, analisi approfondita dell'app e così via)| Sì | Il servizio app si integra con Application Insights per le lingue che supportano Application Insights (completo .NET Framework, .NET Core, Java e Node.JS).  Vedere [monitorare le prestazioni del servizio app Azure](../azure-monitor/app/azure-web-apps.md). Il servizio app invia anche le metriche dell'applicazione in monitoraggio di Azure. | [Monitorare le app in Servizio app di Azure](web-sites-monitor.md)
| Piano di gestione e controllo - Registrazione e controllo| Sì | Tutte le operazioni di gestione eseguite sugli oggetti del servizio app si verificano tramite [Azure Resource Manager](../azure-resource-manager/index.yml). I log cronologici di queste operazioni sono disponibili sia nel portale che tramite l'interfaccia della riga di comando. | [Operazioni del provider di risorse Azure Resource Manager](../role-based-access-control/resource-provider-operations.md#microsoftweb), [AZ monitor Activity-log](/cli/azure/monitor/activity-log)
| Piano dati - Registrazione e controllo | No | Il piano dati per il servizio app è una condivisione file remota che contiene il contenuto del sito Web distribuito del cliente.  Nessun controllo della condivisione file remota. |

## <a name="identity"></a>Identità

| Controllo di sicurezza | Sì/No | Note |  Documentazione
|---|---|--|
| Authentication| Sì | I clienti possono compilare applicazioni nel servizio app che si integrano automaticamente con [Azure Active Directory (Azure ad)](../active-directory/index.yml) e altri provider di identità compatibili con OAuth per l'accesso di gestione alle risorse del servizio app, tutti gli accessi sono controllati da una combinazione di Azure ad principale autenticata e di Azure RBAC. | [Autenticazione e autorizzazione nel servizio app di Azure](overview-authentication-authorization.md)
| Autorizzazione| Sì | Per la gestione dell'accesso alle risorse del servizio app, tutti gli accessi sono controllati da una combinazione di Azure AD entità autenticata e di Azure RBAC.  | [Autenticazione e autorizzazione nel servizio app di Azure](overview-authentication-authorization.md)

## <a name="data-protection"></a>Protezione dei dati

| Controllo di sicurezza | Sì/No | Note | Documentazione
|---|---|--|
| Crittografia lato server dei dati inattivi: chiavi gestite da Microsoft | Sì | Il contenuto del file di sito Web viene archiviato in archiviazione di Azure, che crittografa automaticamente il contenuto inattivo. <br><br>I segreti forniti dal cliente vengono crittografati a riposo. I segreti vengono crittografati a riposo durante l'archiviazione nei database di configurazione del servizio app.<br><br>I dischi collegati localmente possono essere facoltativamente usati come archiviazione temporanea da siti Web (D:\Local e% TMP%). I dischi collegati localmente non sono crittografati. | [Crittografia del servizio di archiviazione di Azure per dati inattivi](../storage/common/storage-service-encryption.md)
| Crittografia lato server dei dati inattivi: chiavi gestite dal cliente (BYOK) | Sì | I clienti possono scegliere di archiviare i segreti dell'applicazione in Key Vault e recuperarli in fase di esecuzione. | [Usare i riferimenti a Key Vault per Servizio app e Funzioni di Azure (anteprima)](app-service-key-vault-references.md)
| Crittografia a livello di colonna (Servizi dati di Azure)| N/D | |
| Crittografia in transito (ad esempio crittografia ExpressRoute, crittografia VNet e crittografia VNet-VNet)| Sì | I clienti possono configurare i siti Web in modo da richiedere e usare HTTPS per il traffico in ingresso.  | [Come creare solo un servizio di app Azure HTTPS](/archive/blogs/benjaminperkins/how-to-make-an-azure-app-service-https-only) (post di Blog)
| Chiamate API crittografate| Sì | Le chiamate di gestione per configurare il servizio app si verificano tramite [Azure Resource Manager](../azure-resource-manager/index.yml) chiamate su HTTPS. |

## <a name="configuration-management"></a>Gestione della configurazione

| Controllo di sicurezza | Sì/No | Note | Documentazione
|---|---|--|
| Supporto gestione della configurazione (controllo delle versioni di configurazione e così via)| Sì | Per le operazioni di gestione, lo stato di una configurazione del servizio app può essere esportato come modello di Azure Resource Manager e con controllo delle versioni nel tempo. Per le operazioni di runtime, i clienti possono gestire più versioni live di un'applicazione usando la funzionalità slot di distribuzione del servizio app. | 

## <a name="next-steps"></a>Passaggi successivi

- Maggiori informazioni sui [controlli di sicurezza incorporati nei servizi di Azure](../security/fundamentals/security-controls.md).