---
title: 'Gestione delle applicazioni: procedure consigliate e consigli | Microsoft Docs'
description: Informazioni sulle procedure consigliate e consigli per la gestione delle applicazioni in Azure Active Directory. Informazioni su come usare il provisioning automatico e pubblicare app locali con il proxy di applicazione.
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
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/14/2019
ms.locfileid: "74085291"
---
# <a name="application-management-best-practices"></a>Procedure consigliate per la gestione delle applicazioni
Questo articolo contiene indicazioni e procedure consigliate per la gestione delle applicazioni in Azure Active Directory (Azure AD), l'uso del provisioning automatico e la pubblicazione di app locali con il proxy di applicazione.

## <a name="cloud-app-and-single-sign-on-recommendations"></a>App cloud e consigli Single Sign-On
| Raccomandazione | Commenti |
| --- | --- |
| Controllare la raccolta di applicazioni Azure AD per le app  | Azure AD dispone di una raccolta che contiene migliaia di applicazioni preintegrate abilitate con Enterprise Single Sign-On (SSO). Per indicazioni sulla configurazione specifiche dell'app, vedere l' [elenco delle esercitazioni sulle app Saas](https://azure.microsoft.com/documentation/articles/active-directory-saas-tutorial-list/).  | 
| Usa SSO federato basato su SAML  | Quando un'applicazione la supporta, utilizzare SSO federato basato su SAML con Azure AD anziché SSO basato su password e ADFS.  | 
| Usare SHA-256 per la firma del certificato  | Per impostazione predefinita, Azure AD usa l'algoritmo SHA-256 per firmare la risposta SAML. Usare SHA-256, a meno che l'applicazione non richieda SHA-1 (vedere le [Opzioni di firma del certificato](certificate-signing-options.md) e il [problema di accesso all'applicazione](application-sign-in-problem-application-error.md)).  | 
| Richiedi assegnazione utente  | Per impostazione predefinita, gli utenti possono accedere alle applicazioni aziendali senza essere loro assegnati. Tuttavia, se l'applicazione espone ruoli o se si desidera che l'applicazione venga visualizzata nel pannello di accesso di un utente, richiedere l'assegnazione dell'utente. (Vedere [le linee guida per gli sviluppatori per l'integrazione di applicazioni](developer-guidance-for-integrating-applications.md)).  | 
| Distribuire il pannello di accesso app personali agli utenti | Il [Pannello di accesso](end-user-experiences.md) `https://myapps.microsoft.com` è un portale basato sul Web che offre agli utenti un unico punto di ingresso per le applicazioni basate sul cloud assegnate. Con l'aggiunta di funzionalità aggiuntive, ad esempio la gestione dei gruppi e la reimpostazione della password self-service, gli utenti possono trovarle nel pannello di accesso. Vedere [pianificare una distribuzione del pannello di accesso](access-panel-deployment-plan.md).
| USA assegnazione gruppo  | Se incluso nella sottoscrizione, assegnare i gruppi a un'applicazione in modo da poter delegare la gestione degli accessi in corso al proprietario del gruppo. (Vedere [le linee guida per gli sviluppatori per l'integrazione di applicazioni](developer-guidance-for-integrating-applications.md)).   | 
| Stabilire un processo per la gestione dei certificati | La durata massima di un certificato di firma è di tre anni. Per prevenire o ridurre al minimo le interruzioni dovute alla scadenza di un certificato, usare i ruoli e le liste di distribuzione di posta elettronica per assicurarsi che le notifiche delle modifiche relative ai certificati siano monitorate attentamente. |

## <a name="provisioning-recommendations"></a>Suggerimenti per il provisioning
| Raccomandazione | Commenti |
| --- | --- |
| Usare le esercitazioni per configurare il provisioning con le app Cloud | Vedere l' [elenco delle esercitazioni sulle app Saas](https://azure.microsoft.com/documentation/articles/active-directory-saas-tutorial-list/) per istruzioni dettagliate sulla configurazione del provisioning per l'app della raccolta che si vuole aggiungere. |
| Usare i log di provisioning (anteprima) per monitorare lo stato | I [log di provisioning](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context) forniscono informazioni dettagliate su tutte le azioni eseguite dal servizio di provisioning, incluso lo stato per i singoli utenti. |
| Assegnare un gruppo di distribuzione al messaggio di posta elettronica di notifica del provisioning | Per aumentare la visibilità degli avvisi critici inviati dal servizio di provisioning, assegnare un gruppo di distribuzione all'impostazione dei messaggi di posta elettronica di notifica. |


## <a name="application-proxy-recommendations"></a>Raccomandazioni sul proxy di applicazione
| Raccomandazione | Commenti |
| --- | --- |
| Usare il proxy di applicazione per l'accesso remoto alle risorse interne | Il proxy di applicazione è consigliato per concedere agli utenti remoti l'accesso alle risorse interne, sostituendo la necessità di una VPN o di un proxy inverso. Non è progettato per l'accesso alle risorse dall'interno della rete aziendale perché potrebbe aggiungere latenza.
| Usare domini personalizzati | Configurare domini personalizzati per le applicazioni (vedere [configurare domini personalizzati](application-proxy-configure-custom-domain.md)) in modo che gli URL per gli utenti e tra le applicazioni funzionino da all'interno o all'esterno della rete. Sarà anche possibile controllare la personalizzazione e personalizzare gli URL.  Quando si utilizzano nomi di dominio personalizzati, pianificare l'acquisizione di un certificato pubblico da un'autorità di certificazione non Microsoft attendibile. Applicazione Azure proxy supporta certificati standard, (con[caratteri jolly](application-proxy-wildcard.md)) o San. Vedere la pagina relativa alla [pianificazione del proxy di applicazione](application-proxy-deployment-plan.md). |
| Sincronizzare gli utenti prima di distribuire il proxy di applicazione | Prima di distribuire il proxy di applicazione, sincronizzare le identità utente da una directory locale o crearle direttamente in Azure AD. Sincronizzazione delle identità consente Azure AD di pre-autenticare gli utenti prima di concedere loro l'accesso alle applicazioni pubblicate dal proxy app. Fornisce inoltre le informazioni necessarie sull'identificatore utente per eseguire Single Sign-On (SSO). Vedere la pagina relativa alla [pianificazione del proxy di applicazione](application-proxy-deployment-plan.md). |
| Segui i nostri suggerimenti per la disponibilità elevata e il bilanciamento del carico | Per informazioni sul flusso del traffico tra gli utenti, i connettori del proxy di applicazione e i server app back-end e per ottenere suggerimenti per l'ottimizzazione delle prestazioni e del bilanciamento del carico, vedere [disponibilità elevata e bilanciamento del carico delle applicazioni e dei connettori del proxy di applicazione](application-proxy-high-availability-load-balancing.md). |
| Usare più connettori | Usare due o più connettori proxy di applicazione per aumentare la resilienza, la disponibilità e la scalabilità. vedere [connettori del proxy di applicazione](application-proxy-connectors.md). Creare gruppi di connettori e verificare che ogni gruppo di connettori disponga di almeno due connettori (tre connettori sono ottimali). |
| Individuare i server del connettore vicini ai server applicazioni e assicurarsi che si trovino nello stesso dominio | Per ottimizzare le prestazioni, individuare fisicamente il server connettore vicino ai server applicazioni (vedere [considerazioni sulla topologia di rete](application-proxy-network-topology.md)). Inoltre, i server del connettore e i server delle applicazioni Web devono appartenere allo stesso dominio di Active Directory o devono estendersi ai domini trusting. Questa configurazione è necessaria per SSO con autenticazione integrata di Windows (IWA) e la delega vincolata Kerberos (delega vincolata Kerberos). Se i server si trovano in domini diversi, è necessario usare la delega basata sulle risorse per SSO. vedere [delega vincolata Kerberos per Single Sign-on con il proxy di applicazione](application-proxy-configure-single-sign-on-with-kcd.md). |
| Abilitare gli aggiornamenti automatici per i connettori | Abilitare gli aggiornamenti automatici per i connettori per le funzionalità e le correzioni di bug più recenti. Microsoft fornisce supporto diretto per la versione più recente del connettore e per una versione precedente. Vedere la [cronologia delle versioni di rilascio del proxy di applicazione](application-proxy-release-version-history.md). |
| Ignora il proxy locale | Per semplificare la manutenzione, configurare il connettore per il bypass del proxy locale, in modo che si connetta direttamente ai servizi di Azure. Vedere [connettori proxy applicazione e server proxy](application-proxy-configure-connectors-with-proxy-servers.md). |
| Usare Azure AD proxy di applicazione sul proxy applicazione Web | Usare Azure AD proxy di applicazione per la maggior parte degli scenari locali. Proxy applicazione Web è preferibile solo negli scenari che richiedono un server proxy per AD FS e in cui non è possibile utilizzare domini personalizzati in Azure Active Directory. Vedere [migrazione del proxy di applicazione](application-proxy-migration.md). |
