---
title: Suggerimenti per la sicurezza
description: Implementare i consigli di sicurezza per soddisfare gli obblighi di sicurezza come indicato nel nostro modello di responsabilità condivisa. Migliora la sicurezza della tua app.
author: msmbaldwin
manager: barbkess
ms.topic: conceptual
ms.date: 06/17/2019
ms.author: mbaldwin
ms.custom: security-recommendations
ms.openlocfilehash: 50e2666aa533a5111055a095c612b58bfe6f9db4
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/02/2020
ms.locfileid: "80546708"
---
# <a name="security-recommendations-for-app-service"></a>Consigli sulla sicurezza per il servizio appSecurity recommendations for App Service

Questo articolo contiene consigli sulla sicurezza per il servizio app di Azure.This article contains security recommendations for Azure App Service. L'implementazione di questi consigli consente di adempiere agli obblighi di sicurezza come descritto nel modello di responsabilità condivisa e migliorerà la sicurezza complessiva per le soluzioni Web App. Per altre informazioni sulle operazioni eseguite da Microsoft per adempiere alle responsabilità del provider di servizi, leggere [Sicurezza dell'infrastruttura](../security/fundamentals/infrastructure.md)di Azure .For more information on what Microsoft does to fulfill service provider responsibilities, read Azure infrastructure security .

## <a name="general"></a>Generale

| Recommendation | Commenti |
|-|-|----|
| Aggiornamenti | Utilizzare le versioni più recenti di piattaforme supportate, linguaggi di programmazione, protocolli e framework. |

## <a name="identity-and-access-management"></a>Gestione delle identità e dell'accesso

| Recommendation | Commenti |
|-|----|
| Disabilitare l'accesso anonimo | A meno che non sia necessario supportare le richieste anonime, disabilitare l'accesso anonimo. Per altre informazioni sulle opzioni di autenticazione del servizio app di Azure, vedere Autenticazione e autorizzazione nel servizio app di Azure.For more information on Azure App Service authentication options, see [Authentication and authorization in Azure App Service.](overview-authentication-authorization.md)|
| Richiedi autenticazione | Quando possibile, usare il modulo di autenticazione del servizio app anziché scrivere codice per gestire l'autenticazione e l'autorizzazione. Vedere [Autenticazione e autorizzazione nel](overview-authentication-authorization.md)servizio app di Azure.See Authentication and authorization in Azure App Service . |
| Proteggere le risorse back-end con l'accesso autenticatoProtect back-end resources with authenticated access | È possibile usare l'identità dell'utente o un'identità dell'applicazione per eseguire l'autenticazione a una risorsa back-end. Quando si sceglie di utilizzare un'identità dell'applicazione, utilizzare [un'identità gestita.](overview-managed-identity.md)
| Richiedere l'autenticazione del certificato clientRequire client certificate authentication | L'autenticazione dei certificati client migliora la sicurezza consentendo solo le connessioni dai client che possono eseguire l'autenticazione utilizzando i certificati forniti. |

## <a name="data-protection"></a>Protezione dei dati

| Recommendation | Commenti |
|-|-|
| Reindirizzare HTTP a HTTP | Per impostazione predefinita, i client possono connettersi alle app Web usando sia HTTP che HTTPS. È consigliabile reindirizzare HTTP a HTTP a HTTP perché HTTPS utilizza il protocollo SSL/TLS per fornire una connessione sicura, crittografata e autenticata. |
| Crittografare le comunicazioni con le risorse di AzureEncrypt communication to Azure resources | Quando l'app si connette alle risorse di Azure, ad esempio Database SQL o [Archiviazione di Azure,](/azure/storage/)la connessione rimane in Azure.When your app connects to Azure resources, such as [SQL Database](https://azure.microsoft.com/services/sql-database/) or Azure Storage , the connection stays in Azure. Poiché la connessione passa attraverso la rete condivisa in Azure, è sempre necessario crittografare tutte le comunicazioni. |
| Richiedere l'ultima versione tls possibile | Dal 2018 le nuove app del servizio app di Azure usano TLS 1.2. Le versioni più recenti di TLS includono miglioramenti della sicurezza rispetto alle versioni precedenti del protocollo. |
| Utilizzare FTPS | Il servizio app supporta FTP e FTPS per la distribuzione dei file. Utilizzare FTPS anziché FTP quando possibile. Quando uno o entrambi questi protocolli non sono in uso, è necessario [disabilitarli](deploy-ftp.md#enforce-ftps). |
| Proteggere i dati dell'applicazione | Non archiviare segreti dell'applicazione, ad esempio credenziali di database, token API o chiavi private nel codice o nei file di configurazione. L'approccio comunemente accettato consiste nell'accedere ai segreti come [variabili di ambiente](https://wikipedia.org/wiki/Environment_variable) usando il modello standard nel linguaggio preferito. Nel servizio app di Azure è possibile definire variabili di ambiente tramite [le impostazioni dell'app](web-sites-configure.md) e le stringhe di [connessione.](web-sites-configure.md) Le impostazioni dell'app e le stringhe di connessione vengono archiviate crittografate in Azure.App settings and connection strings are stored encrypted in Azure. Le impostazioni dell'app vengono decrittografate solo prima di essere inserite nella memoria del processo dell'app all'avvio dell'app. Le chiavi di crittografia vengono sottoposte a rotazione regolarmente. In alternativa, è possibile integrare l'app del servizio app di Azure con [l'insieme](/azure/key-vault/) di credenziali delle chiavi di Azure per la gestione avanzata dei segreti. [Accedendo all'insieme di credenziali delle chiavi con un'identità gestita](../key-vault/tutorial-web-application-keyvault.md), l'app del Servizio app può accedere in modo sicuro ai segreti necessari. |

## <a name="networking"></a>Rete

| Recommendation | Commenti |
|-|-|
| Utilizzare restrizioni IP statiche | Servizio app di Azure in Windows consente di definire un elenco di indirizzi IP autorizzati ad accedere all'app. Questo elenco può includere singoli indirizzi IP o un intervallo di indirizzi IP definito da una subnet mask. Per altre informazioni, vedere [Restrizioni IP statico del Servizio app di Azure](app-service-ip-restrictions.md).  |
| Usare il piano tariffario isolatoUse the isolated pricing tier | Ad eccezione del piano tariffario isolato, tutti i livelli eseguono le app nell'infrastruttura di rete condivisa nel servizio app di Azure.Except for the isolated pricing tier, all tiers run your apps on the shared network infrastructure in Azure App Service. Il livello isolato offre l'isolamento completo della rete eseguendo le app all'interno di un ambiente del [servizio app](environment/intro.md)dedicato. Un ambiente del servizio app viene eseguito nell'istanza di [rete virtuale di Azure](/azure/virtual-network/) dell'utente.|
| Usare connessioni protette quando si accede alle risorse localiUse secure connections when accessing on-premises resources | È possibile utilizzare [connessioni ibride,](app-service-hybrid-connections.md) [integrazione](web-sites-integrate-with-vnet.md)di rete virtuale o ambiente del [servizio](environment/intro.md) app per connettersi alle risorse locali. |
| Limitare l'esposizione al traffico di rete in ingresso | I gruppi di sicurezza di rete consentono di limitare l'accesso alla rete e di controllare il numero di endpoint esposti.Network security groups allow you to restrict network access and control the number of exposed endpoints. Per altre informazioni, vedere Come controllare il [traffico in ingresso in un ambiente](environment/app-service-app-service-environment-control-inbound-traffic.md)del servizio app . |

## <a name="monitoring"></a>Monitoraggio

| Recommendation | Commenti |
|-|-|
|Usare il livello standard del Centro sicurezza di AzureUse Azure Security Center standard tier | [Centro sicurezza di Azure](../security-center/security-center-app-services.md) è integrato in modo nativo con il servizio app di Azure.Azure Security Center is natively integrated with Azure App Service. Può eseguire valutazioni e fornire raccomandazioni sulla sicurezza. |

## <a name="next-steps"></a>Passaggi successivi

Rivolgersi al provider dell'applicazione per verificare se sono presenti ulteriori requisiti di sicurezza. Per ulteriori informazioni sullo sviluppo di applicazioni protette, vedere [Documentazione sullo sviluppo protetto](../security/fundamentals/abstract-develop-secure-apps.md).
