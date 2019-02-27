---
title: API Graph di Azure Active Directory | Microsoft Docs
description: Panoramica e guida di avvio rapido per l'API Graph di Azure AD che consente l'accesso a livello di codice ad Azure AD tramite endpoint dell'API REST.
services: active-directory
documentationcenter: ''
author: lleonard-msft
manager: mtillman
ms.assetid: 5471ad74-20b3-44df-a2b5-43cde2c0a045
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/24/2018
ms.author: alleonar
ms.reviewer: dkershaw, sureshja
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 567ea09a50edc043f2022a47d08576720550e750
ms.sourcegitcommit: 6cab3c44aaccbcc86ed5a2011761fa52aa5ee5fa
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/20/2019
ms.locfileid: "56448093"
---
# <a name="azure-active-directory-graph-api"></a>API Graph di Azure Active Directory

> [!IMPORTANT]
>
> A partire da febbraio 2019, l'API Graph di Azure Active Directory è deprecata e sostituita dall'API Microsoft Graph.  
>
> Per semplificare la transizione, le versioni precedenti dell'API Graph di Azure AD verranno ritirate prima delle versioni più recenti.  
>
> Per altre informazioni, aggiornamenti e tempistiche, vedere il post [Microsoft Graph o Azure AD Graph](https://dev.office.com/blogs/microsoft-graph-or-azure-ad-graph) del blog su Office Dev Center.
>
> In futuro le applicazioni dovranno usare l'API Microsoft Graph. 



Questo articolo si applica all'API Graph di Azure AD. Per informazioni analoghe relative all'API Microsoft Graph, vedere [Use the Microsoft Graph API](https://docs.microsoft.com/graph/use-the-api) (Usare l'API Microsoft Graph). 

L'API Graph di Azure Active Directory consente l'accesso a livello di codice ad Azure AD tramite endpoint dell'API REST. Le applicazioni possono usare l'API Graph di Azure AD per le operazioni di creazione, lettura, aggiornamento ed eliminazione (CRUD, Create, Read, Update, Delete) su oggetti e dati della directory. Ad esempio, l'API Graph di Azure AD supporta le operazioni comuni seguenti per un oggetto utente:

* Creare un nuovo utente nella directory
* Recuperare le proprietà dettagliate dell'utente, ad esempio i gruppi a cui appartiene
* Aggiornare le proprietà dell'utente, ad esempio indirizzo e numero di telefono, oppure modifica della password
* Verificare l'appartenenza a gruppi di un utente per l'accesso in base al ruolo
* Disabilitare o eliminare completamente l'account di un utente

È inoltre possibile eseguire operazioni simili su altri oggetti, ad esempio gruppi e applicazioni. Per chiamare l'API Graph di Azure AD su una directory, l'applicazione deve essere registrata con Azure AD e deve anche disporre dei diritti di accesso all'API Graph di Azure AD. L'accesso viene in genere ottenuto tramite un flusso di consenso utente o amministratore.

Per iniziare a usare l'API Graph di Azure Active Directory, vedere [Guida introduttiva per l'API Graph di Azure AD](active-directory-graph-api-quickstart.md) oppure la [documentazione interattiva di riferimento per l'API Graph di Azure AD](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog).

## <a name="features"></a>Funzionalità

L'API Graph di Azure AD offre le funzionalità seguenti:

* **Endpoint API REST**: l'API Graph di Azure AD è un servizio RESTful che include endpoint a cui viene eseguito l'accesso tramite richieste HTTP standard. L'API Graph di Azure AD supporta i tipi di contenuto XML o JSON (JavaScript Object Notation) per le richieste e le risposte. Per altre informazioni, vedere [Azure AD Graph REST API reference](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog) (Informazioni di riferimento sull'API REST Graph di Azure AD).
* **Autenticazione con Azure AD**: ogni richiesta all'API Graph di Azure AD deve essere autenticata aggiungendo un token JSON Web (JWT) nell'intestazione dell'autorizzazione della richiesta. Per acquisire il token è necessario inviare una richiesta all'endpoint token di Azure AD e specificare credenziali valide. Per acquisire un token per chiamare l'API Graph, è possibile usare il flusso delle credenziali client OAuth 2.0 o il flusso di concessione del codice di autorizzazione. Per altre informazioni, vedere [OAuth 2.0 in Azure AD](https://msdn.microsoft.com/library/azure/dn645545.aspx).
* **Autorizzazione basata sui ruoli (controllo degli accessi in base al ruolo)**: per il controllo degli accessi in base al ruolo nell'API Graph di Azure AD vengono usati gruppi di sicurezza. Se, ad esempio, si vuole stabilire se un utente può accedere a una risorsa specifica, l'applicazione può chiamare l'operazione di [controllo dell'appartenenza a gruppi (transitiva)](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/functions-and-actions#checkMemberGroups), che restituisce true o false.
* **Query differenziale**: è possibile eseguire una query differenziale per tenere traccia delle modifiche apportate a una directory tra due momenti di tempo senza dover interrogare di frequente l'API Graph di Azure AD. Questo tipo di richiesta restituisce solo le modifiche apportate nel periodo trascorso tra la richiesta di query differenziale precedente e la richiesta corrente. Per altre informazioni, vedere [Query differenziale dell'API Graph di Azure AD](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-differential-query).
* **Estensioni della directory**: è possibile aggiungere proprietà personalizzate agli oggetti della directory senza dover usare un archivio dati esterno. Se, ad esempio, l'applicazione richiede una proprietà ID Skype per ogni utente, è possibile registrare la nuova proprietà nella directory e renderla disponibile per ogni oggetto utente. Per altre informazioni, vedere [Estensioni dello schema della directory dell'API Graph di Azure AD](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-directory-schema-extensions).
* **Protezione tramite ambiti di autorizzazione**: l'API Graph di Azure AD espone gli ambiti di autorizzazione che consentono l'accesso sicuro ai dati di Azure AD tramite OAuth 2.0. Sono supportati diversi tipi di app client, tra cui:
  
  * le interfacce utente a cui viene assegnato l'accesso delegato ai dati tramite l'autorizzazione da parte dell'utente connesso (delegato)
  * le applicazioni di servizio/daemon che operano in background senza un utente connesso e usano il controllo degli accessi in base al ruolo definito dall'applicazione
    
    Entrambe le autorizzazioni basate su accesso delegato e applicazione rappresentano un privilegio esposto dall'API Graph di Azure AD e possono essere richieste dalle applicazioni client tramite le funzionalità per le autorizzazioni di registrazione delle applicazioni [nel portale di Azure](https://portal.azure.com). Gli [ambiti di autorizzazione dell'API Graph di Azure AD](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-permission-scopes) forniscono informazioni su ciò che è disponibile per l'applicazione client.

## <a name="scenarios"></a>Scenari

Gli scenari di applicazione dell'API Graph di Azure AD sono numerosi. Ecco i più comuni:

* **Applicazione line-of-business (tenant singolo)**: in questo scenario, uno sviluppatore aziendale lavora per un'organizzazione con un abbonamento a Office 365. Lo sviluppatore sta creando un'applicazione Web che interagisce con Azure AD per eseguire diverse attività, ad esempio l'assegnazione di una licenza a un utente. Questa attività richiede l'accesso all'API Graph di Azure AD, in modo che lo sviluppatore possa registrare l'applicazione a tenant singolo in Azure AD e configurare le autorizzazioni di lettura e scrittura per l'API Graph di Azure AD. L'applicazione viene quindi configurata in modo da usare le proprie credenziali o quelle dell'utente che ha eseguito l'accesso per acquisire un token per chiamare l'API Graph di Azure AD.
* **Applicazione software come un servizio (multi-Tenant)**: in questo scenario, un fornitore di software indipendente (ISV) sviluppa un'applicazione Web multi-tenant ospitata che fornisce funzionalità per la gestione degli utenti per altre organizzazioni che usano Azure AD. Queste funzionalità richiedono l'accesso agli oggetti della directory e quindi l'applicazione deve chiamare l'API Graph di Azure AD. Lo sviluppatore registra l'applicazione in Azure AD, la configura per richiedere le autorizzazioni di lettura e scrittura per l'API Graph di Azure AD e quindi abilita l'accesso esterno in modo che altre organizzazioni possano fornire il consenso per l'uso dell'applicazione nella rispettiva directory. Quando un utente in un'altra organizzazione esegue l'autenticazione nell'applicazione per la prima volta, viene visualizzata una finestra di dialogo di consenso con le autorizzazioni richieste dall'applicazione. Se si concede il consenso, l'applicazione riceverà le autorizzazioni richieste per l'API Graph di Azure AD nella directory dell'utente. Per altre informazioni sul framework di consenso, vedere [Panoramica del framework di consenso](consent-framework.md).

## <a name="next-steps"></a>Passaggi successivi

Per iniziare a usare l'API Graph di Azure Active Directory, vedere gli argomenti seguenti:

* [Guida introduttiva per l'API Graph di Azure AD](active-directory-graph-api-quickstart.md)
* [Documentazione di riferimento all'API REST Graph di Azure AD](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog)
