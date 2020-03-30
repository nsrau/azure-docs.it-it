---
title: 'Gestione delle applicazioni: procedure consigliate e consigli Documenti Microsoft'
description: Informazioni sulle procedure consigliate e sui consigli per la gestione delle applicazioni in Azure Active Directory.Learn best practices and recommendations for managing applications in Azure Active Directory. Informazioni sull'uso del provisioning automatico e della pubblicazione di app locali con il proxy di applicazione.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/13/2019
ms.subservice: app-mgmt
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6534efb6fcd07ee3b9f3979cabf2feb77496a8b8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74085291"
---
# <a name="application-management-best-practices"></a>Procedure consigliate per la gestione delle applicazioni
Questo articolo contiene consigli e procedure consigliate per la gestione delle applicazioni in Azure Active Directory (Azure AD), l'uso del provisioning automatico e la pubblicazione di app locali con il proxy di applicazione.

## <a name="cloud-app-and-single-sign-on-recommendations"></a>Consigli per l'app cloud e l'accesso Single Sign-On
| Recommendation | Commenti |
| --- | --- |
| Controllare la raccolta di applicazioni di Azure AD per le appCheck the Azure AD application gallery for apps  | Azure AD include una raccolta che contiene migliaia di applicazioni preintegrate abilitate con Enterprise Single Sign-On (SSO). Per indicazioni sull'installazione specifiche [dell'app, vedere l'esercitazione sull'elenco delle app SaaS](https://azure.microsoft.com/documentation/articles/active-directory-saas-tutorial-list/).  | 
| Utilizzare SSO federato basato su SAML  | Quando un'applicazione lo supporta, usare SSO federato basato su SAML con Azure AD anziché SSO e ADFS basati su password.  | 
| Usare SHA-256 per la firma del certificatoUse SHA-256 for certificate signing  | Azure AD usa l'algoritmo SHA-256 per impostazione predefinita per firmare la risposta SAML. Utilizzare SHA-256 a meno che l'applicazione non richieda SHA-1 (vedere Opzioni di [firma dei certificati](certificate-signing-options.md) e problema di accesso [dell'applicazione).](application-sign-in-problem-application-error.md)  | 
| Richiedi assegnazione utente  | Per impostazione predefinita, gli utenti possono accedere alle applicazioni aziendali senza essere loro assegnati. Tuttavia, se l'applicazione espone ruoli o se si desidera che l'applicazione venga visualizzata nel pannello di accesso di un utente, richiedere l'assegnazione dell'utente. (Vedere [Linee guida per gli sviluppatori per l'integrazione di applicazioni](developer-guidance-for-integrating-applications.md).  | 
| Distribuire il pannello di accesso App personali agli utenti | Il [pannello](end-user-experiences.md) `https://myapps.microsoft.com` di accesso in è un portale basato sul Web che fornisce agli utenti un unico punto di ingresso per le applicazioni basate su cloud assegnate. Man mano che vengono aggiunte funzionalità aggiuntive come la gestione dei gruppi e la reimpostazione della password self-service, gli utenti possono trovarle nel pannello di accesso. Consultate Pianificare la distribuzione di un pannello di [accesso.](access-panel-deployment-plan.md)
| Utilizzare l'assegnazione di gruppo  | Se incluso nella sottoscrizione, assegnare gruppi a un'applicazione in modo da poter delegare la gestione degli accessi in corso al proprietario del gruppo. (Vedere [Linee guida per gli sviluppatori per l'integrazione di applicazioni](developer-guidance-for-integrating-applications.md).   | 
| Stabilire un processo per la gestione dei certificati | La durata massima di un certificato di firma è di tre anni. Per evitare o ridurre al minimo l'interruzione dovuta alla scadenza di un certificato, utilizzare i ruoli e le liste di distribuzione di posta elettronica per assicurarsi che le notifiche di modifica relative ai certificati siano attentamente monitorate. |

## <a name="provisioning-recommendations"></a>Consigli per il provisioning
| Recommendation | Commenti |
| --- | --- |
| Usare le esercitazioni per configurare il provisioning con le app cloudUse tutorials to set up provisioning with cloud apps | Controlla le [esercitazioni su List of SaaS](https://azure.microsoft.com/documentation/articles/active-directory-saas-tutorial-list/) per istruzioni dettagliate sulla configurazione del provisioning per l'app della raccolta che vuoi aggiungere. |
| Usare i registri di provisioning (anteprima) per monitorare lo statoUse provisioning logs (preview) to monitor status | I log di [provisioning](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context) forniscono dettagli su tutte le azioni eseguite dal servizio di provisioning, incluso lo stato per i singoli utenti. |
| Assegnare un gruppo di distribuzione all'e-mail di notifica di provisioningAssign a distribution group to the provisioning notification email | Per aumentare la visibilità degli avvisi critici inviati dal servizio di provisioning, assegnare un gruppo di distribuzione all'impostazione E-mail di notifica. |


## <a name="application-proxy-recommendations"></a>Consigli per il proxy di applicazione
| Recommendation | Commenti |
| --- | --- |
| Utilizzare il proxy di applicazione per l'accesso remoto alle risorse interneUse Application Proxy for remote access to internal resources | Il proxy di applicazione è consigliato per consentire agli utenti remoti di accedere alle risorse interne, sostituendo la necessità di un proxy VPN o inverso. Non è destinato all'accesso alle risorse dall'interno della rete aziendale perché potrebbe aggiungere latenza.
| Utilizzare domini personalizzati | Configurare domini personalizzati per le applicazioni (vedere [Configurare domini personalizzati](application-proxy-configure-custom-domain.md)) in modo che gli URL per gli utenti e tra le applicazioni funzionino dall'interno o dall'esterno della rete. Potrai anche controllare il tuo branding e personalizzare i tuoi URL.  Quando si usano nomi di dominio personalizzati, pianificare l'acquisizione di un certificato pubblico da un'autorità di certificazione attendibile non Microsoft.When using custom domain names, plan to acquire a public certificate from a non-Microsoft trusted certificate authority. Il proxy di applicazione di Azure supporta certificati standard ([caratteri jolly](application-proxy-wildcard.md)) o basati su SAN. (Vedere Pianificazione del proxy di [applicazione](application-proxy-deployment-plan.md). |
| Sincronizzare gli utenti prima di distribuire il proxy di applicazione | Prima di distribuire il proxy di applicazione, sincronizzare le identità utente da una directory locale o crearle direttamente in Azure AD. La sincronizzazione delle identità consente ad Azure AD di preautenticare gli utenti prima di concedere loro l'accesso alle applicazioni pubblicate da App Proxy.Identity synchronization allows Azure AD to pre-authenticate users before granting them access to App Proxy published applications. Fornisce inoltre le informazioni necessarie sull'identificatore utente per eseguire l'accesso Single Sign-On (SSO). (Vedere Pianificazione del proxy di [applicazione](application-proxy-deployment-plan.md). |
| Segui i nostri suggerimenti per la disponibilità elevata e il bilanciamento del carico | Per informazioni sul flusso di traffico tra utenti, connettori proxy di applicazione e server applicazioni back-end e per ottenere suggerimenti per l'ottimizzazione delle prestazioni e del bilanciamento del carico, vedere [Disponibilità elevata e bilanciamento del carico dei connettori e](application-proxy-high-availability-load-balancing.md)delle applicazioni del proxy di applicazione. |
| Utilizzare più connettori | Utilizzare due o più connettori proxy di applicazione per una maggiore resilienza, disponibilità e scalabilità (vedere [Connettori proxy applicazione](application-proxy-connectors.md)). Creare gruppi di connettori e verificare che ogni gruppo di connettori disponga di almeno due connettori (tre connettori è ottimale). |
| Individuare i server dei connettori vicini ai server applicazioni e assicurarsi che si trovano nello stesso dominio | Per ottimizzare le prestazioni, individuare fisicamente il server del connettore vicino ai server applicazioni (vedere [Considerazioni sulla topologia di](application-proxy-network-topology.md)rete ). Inoltre, il server del connettore e i server delle applicazioni Web devono appartenere allo stesso dominio di Active Directory oppure devono estendersi ai domini di attendibilità. Questa configurazione è necessaria per SSO con autenticazione integrata di Windows (IWA) e delega vincolata Kerberos (KCD). Se i server si trovano in domini diversi, è necessario utilizzare la delega basata sulle risorse per SSO (vedere [KCD per l'accesso Single Sign-On con il proxy di applicazione](application-proxy-configure-single-sign-on-with-kcd.md)). |
| Abilitare gli aggiornamenti automatici per i connettoriEnable auto-updates for connectors | Abilitare gli aggiornamenti automatici per i connettori per le funzionalità e le correzioni di bug più recenti. Microsoft fornisce supporto diretto per l'ultima versione del connettore e una versione precedente. Vedere [Cronologia versioni del proxy di applicazione](application-proxy-release-version-history.md). |
| Ignorare il proxy locale | Per semplificare la manutenzione, configurare il connettore in modo che ignori il proxy locale in modo che si connetta direttamente ai servizi di Azure.For easier maintenance, configure the connector to bypass your on-premises proxy so it directly connects to the Azure services. Vedere [Connettori proxy di applicazione e server proxy](application-proxy-configure-connectors-with-proxy-servers.md). |
| Usare il proxy di applicazione di Azure AD rispetto al proxy dell'applicazione WebUse Azure AD Application Proxy over Web Application Proxy | Usare il proxy di applicazione di Azure AD per la maggior parte degli scenari locali. Proxy applicazione Web è preferito solo in scenari che richiedono un server proxy per ADFS e in cui non è possibile usare domini personalizzati in Azure Active Directory. (Vedere [Migrazione del proxy](application-proxy-migration.md)di applicazione . |
