---
title: Raccomandazioni sulla sicurezza per il servizio App di Azure
description: Consigli sulla sicurezza per il servizio App di Azure. Implementazione di queste indicazioni della Guida in linea è soddisfare gli obblighi di sicurezza come descritto nel modello di responsabilità condivisa e verrà migliorerà la sicurezza complessiva per le soluzioni di app web.
services: app-service
author: barclayn
manager: barbkess
ms.service: app-service
ms.topic: conceptual
ms.date: 06/17/2019
ms.author: barclayn
ms.custom: security-recommendations
ms.openlocfilehash: 53cd2b1dde1158a1c46f798e57911dad110dc87e
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/10/2019
ms.locfileid: "67718256"
---
# <a name="security-recommendations-for-app-service"></a>Raccomandazioni sulla sicurezza per il servizio App

Questo articolo contiene raccomandazioni sulla sicurezza per il servizio App di Azure. Implementazione di queste indicazioni della Guida in linea è soddisfare gli obblighi di sicurezza come descritto nel modello di responsabilità condivisa e verrà migliorerà la sicurezza complessiva per le soluzioni di App Web. Per altre informazioni su quali Microsoft per soddisfare le responsabilità del provider del servizio, leggere [protezione dell'infrastruttura Azure](../security/azure-security-infrastructure.md).

## <a name="general"></a>Generale

| Recommendation | Commenti |
|-|-|----|
| Rimanere aggiornati | Usare le versioni più recenti delle piattaforme supportate, linguaggi di programmazione, protocolli e Framework. |

## <a name="identity-and-access-management"></a>Gestione delle identità e dell'accesso

| Recommendation | Commenti |
|-|----|
| Disabilitare l'accesso anonimo | A meno che non è necessario supportare le richieste anonime, disabilitare l'accesso anonimo. Per altre informazioni sulle opzioni di autenticazione servizio App di Azure, vedere [autenticazione e autorizzazione nel servizio App di Azure](overview-authentication-authorization.md).|
| Richiedere l'autenticazione | Se possibile, usare il modulo di autenticazione servizio App invece di scrivere codice per gestire l'autenticazione e autorizzazione. Visualizzare [autenticazione e autorizzazione nel servizio App di Azure](overview-authentication-authorization.md). |
| Proteggere le risorse di back-end con l'accesso autenticato | È possibile usare l'identità dell'utente o usare un'identità di applicazione per l'autenticazione a una risorsa back-end. Se si sceglie di usare un'identità applicazione una [identità gestita](overview-managed-identity.md).
| Richiedere l'autenticazione del certificato client | L'autenticazione del certificato client migliora la sicurezza consentendo solo le connessioni dai client che possono eseguire l'autenticazione tramite certificati forniti. |

## <a name="data-protection"></a>Protezione dati

| Recommendation | Commenti |
|-|-|
| Reindirizzare il traffico HTTP a HTTPs | Per impostazione predefinita, i client possono connettersi alle App web usando sia HTTP o HTTPS. È consigliabile il reindirizzamento HTTP a HTTPs perché HTTPS Usa il protocollo SSL/TLS per fornire una connessione protetta, ovvero entrambe crittografate e autenticate. |
| Crittografare le comunicazioni alle risorse di Azure | Quando l'app si connette a risorse di Azure, ad esempio [Database SQL](https://azure.microsoft.com/services/sql-database/) oppure [archiviazione di Azure](/azure/storage/), la connessione rimane in Azure. Poiché la connessione passa attraverso la connessione di rete condivisa in Azure, è consigliabile crittografare sempre tutte le comunicazioni. |
| Richiedono l'ultima versione TLS possibili | Poiché 2018 le nuove app di servizio App di Azure usano TLS 1.2. Le versioni più recenti di TLS includono miglioramenti alla sicurezza rispetto alle precedenti versioni di protocollo. |
| Usare FTPS | Il servizio app supporta FTP e FTPS per la distribuzione dei file. Usare FTPS anziché FTP quando possibile. Quando uno o entrambi questi protocolli non sono in uso, è necessario [disabilitarli](deploy-ftp.md#enforce-ftps). |
| Proteggere i dati dell'applicazione | Non archiviare i segreti dell'applicazione, ad esempio le credenziali del database, i token API o le chiavi private nei file di configurazione o codice. L'approccio comunemente accettato consiste nell'accedere ai segreti come [variabili di ambiente](https://wikipedia.org/wiki/Environment_variable) usando il modello standard nel linguaggio preferito. Nel servizio App di Azure, è possibile definire le variabili di ambiente tramite [le impostazioni dell'app](web-sites-configure.md) e [stringhe di connessione](web-sites-configure.md). Le impostazioni dell'App e le stringhe di connessione vengono archiviate crittografate in Azure. Le impostazioni dell'app vengono decrittografate solo prima che venga introdotto in memoria del processo dell'app all'avvio dell'app. Le chiavi di crittografia vengono sottoposte a rotazione regolarmente. In alternativa, è possibile integrare l'app di servizio App di Azure con [Azure Key Vault](/azure/key-vault/) per la gestione dei segreti avanzate. [Accedendo all'insieme di credenziali delle chiavi con un'identità gestita](../key-vault/tutorial-web-application-keyvault.md), l'app del Servizio app può accedere in modo sicuro ai segreti necessari. |

## <a name="networking"></a>Rete

| Recommendation | Commenti |
|-|-|
| Usare le restrizioni di IP statiche | Servizio App di Azure in Windows è possibile definire un elenco di indirizzi IP consentiti per accedere all'app. Questo elenco può includere singoli indirizzi IP o un intervallo di indirizzi IP definito da una subnet mask. Per altre informazioni, vedere [Restrizioni IP statico del Servizio app di Azure](app-service-ip-restrictions.md).  |
| Usare il piano tariffario isolato | Fatta eccezione per il piano tariffario isolato, tutti i livelli di eseguire le tue App in infrastruttura di rete condivisa nel servizio App di Azure. Il livello di tipo isolato offre isolamento di rete completo tramite l'esecuzione delle App all'interno di un oggetto dedicato [ambiente del servizio App](environment/intro.md). Un ambiente del servizio app viene eseguito nell'istanza di [rete virtuale di Azure](/azure/virtual-network/) dell'utente.|
| Uso di connessioni protette quando l'accesso alle risorse locali | È possibile usare [connessioni ibride](app-service-hybrid-connections.md), [integrazione della rete virtuale](web-sites-integrate-with-vnet.md), o [dell'ambiente del servizio App](environment/intro.md) per connettersi alle risorse locali. |
| Limitare l'esposizione al traffico di rete in ingresso | Gruppi di sicurezza di rete consentono di limitare l'accesso alla rete e controllare il numero di endpoint esposti. Per altre informazioni, vedere [modalità di controllo del traffico in ingresso a un ambiente del servizio App](environment/app-service-app-service-environment-control-inbound-traffic.md). |

## <a name="monitoring"></a>Monitoraggio

| Recommendation | Commenti |
|-|-|
|Livello standard di Azure Usa il Centro sicurezza | [Centro sicurezza di Azure](../security-center/security-center-app-services.md) in modo nativo è integrato con servizio App di Azure. Consente di eseguire valutazioni e fornire raccomandazioni sulla sicurezza. |

## <a name="next-steps"></a>Passaggi successivi

Rivolgersi al provider dell'applicazione per verificare se esistono altri requisiti di sicurezza. Per altre informazioni sullo sviluppo di applicazioni sicure, vedere [documentazione sullo sviluppo sicuro](../security/abstract-develop-secure-apps.md).
