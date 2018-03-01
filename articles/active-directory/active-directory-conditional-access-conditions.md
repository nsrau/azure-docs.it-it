---
title: Condizioni nell'accesso condizionale di Azure Active Directory | Microsoft Docs
description: Informazioni su come vengono usate le assegnazioni nell'accesso condizionale di Azure Active Directory per attivare un criterio.
services: active-directory
keywords: accesso condizionale alle app, accesso condizionale con Azure AD, accesso sicuro alle risorse aziendali, criteri di accesso condizionale
documentationcenter: 
author: MarkusVi
manager: mtillman
editor: 
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/09/2018
ms.author: markvi
ms.reviewer: calebb
ms.openlocfilehash: 2415a2c2c0143b4abeb8ec1ecab379a204456874
ms.sourcegitcommit: b32d6948033e7f85e3362e13347a664c0aaa04c1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/13/2018
---
# <a name="conditions-in-azure-active-directory-conditional-access"></a>Condizioni nell'accesso condizionale di Azure Active Directory 

Con l'[accesso condizionale di Azure Active Directory (Azure AD)](active-directory-conditional-access-azure-portal.md) è possibile controllare il modo in cui gli utenti autorizzati accedono alle app cloud. In un criterio di accesso condizionale si definisce la risposta ("fare questo") al motivo che ha attivato il criterio ("quando accade questo"). 

![Controllo](./media/active-directory-conditional-access-conditions/10.png)


Nel contesto dell'accesso condizionale:

- "**Quando accade questo**" è la **condizione**. 
- "**Fare questo**" è il **controllo di accesso**.

La combinazione delle condizioni con i controlli di accesso rappresenta un criterio di accesso condizionale.

![Controllo](./media/active-directory-conditional-access-conditions/61.png)

Questo articolo offre una panoramica delle condizioni e di come vengono usate in un criterio di accesso condizionale. 


## <a name="users-and-groups"></a>Utenti e gruppi

La condizione di utenti e gruppi è obbligatoria in un criterio di accesso condizionale. Nei criteri è possibile selezionare **Tutti gli utenti** o selezionare utenti e gruppi specifici.

![Controllo](./media/active-directory-conditional-access-conditions/02.png)

Quando si seleziona:

- **Tutti gli utenti**, il criterio viene applicato a tutti gli utenti nella directory, inclusi gli utenti guest.

- **Seleziona utenti e gruppi**, è possibile scegliere come destinazione insiemi di utenti specifici. Ad esempio, è possibile selezionare un gruppo contenente tutti i membri del reparto Risorse umane, quando è selezionata un'app per le risorse umane come app cloud. 

- Un gruppo può essere un gruppo di qualsiasi tipo in Azure AD, inclusi gruppi di sicurezza e distribuzione dinamici o assegnati.

È anche possibile escludere utenti o gruppi specifici da un criterio. Un caso d'uso comune è rappresentato dagli account del servizio nel caso in cui il criterio applichi l'autenticazione a più fattori. 

La definizione di insiemi di utenti specifici come destinazione è utile per la distribuzione di un nuovo criterio. In un nuovo criterio è necessario definire come destinazione solo l'insieme iniziale di utenti per convalidare il comportamento del criterio. 



## <a name="cloud-apps"></a>App cloud 

Un'app cloud è un servizio o un sito Web. Sono inclusi i siti Web protetti da Azure Application Proxy. Per una descrizione dettagliata delle app cloud supportate, vedere [Assegnazioni di app cloud](active-directory-conditional-access-technical-reference.md#cloud-apps-assignments).    

La condizione delle app cloud è obbligatoria in un criterio di accesso condizionale. Nei criteri è possibile selezionare **Tutte le app cloud** o selezionare app specifiche.

![Controllo](./media/active-directory-conditional-access-conditions/03.png)

È possibile selezionare:

- **Tutte le app cloud** per applicare i criteri di base all'intera organizzazione. Un caso d'uso comune per questa selezione è un criterio che richiede l'autenticazione a più fattori quando viene rilevato il rischio di accesso per qualsiasi app cloud.

- App cloud singole per definire come destinazione servizi specifici in base al criterio. Ad esempio, è possibile richiedere agli utenti di avere un [dispositivo conforme](active-directory-conditional-access-policy-connected-applications.md) per accedere a SharePoint Online. Questo criterio viene applicato anche ad altri servizi quando accedono a contenuto di SharePoint, ad esempio, Microsoft Teams. 

È inoltre possibile escludere app specifiche da un criterio; tuttavia, queste app sono comunque soggette ai criteri applicati ai servizi a cui accedono. 



## <a name="sign-in-risk"></a>Rischio di accesso

Il rischio di accesso è un'indicazione della probabilità (elevata, media o bassa) che un tentativo di accesso non sia stato eseguito dal legittimo proprietario di un account utente. Azure AD calcola il livello di rischio di accesso durante l'accesso di un utente. È possibile usare il livello di rischio di accesso calcolato come condizione nei criteri di accesso condizionale. 

![Condizioni](./media/active-directory-conditional-access-conditions/22.png)

Per usare questa condizione, è necessario che sia abilitato [Azure Active Directory Identity Protection](active-directory-identityprotection.md).
 
Casi d'uso comuni per questa condizione sono criteri che:

- Bloccano gli utenti con un elevato rischio di accesso per impedire a utenti potenzialmente non legittimi di accedere alle app cloud. 
- Richiedono l'autenticazione a più fattori per gli utenti con un rischio di accesso medio. Tramite l'applicazione dell'autenticazione a più fattori, si aumenta la certezza che l'accesso venga eseguito dal legittimo proprietario di un account.

Per altre informazioni, vedere [Accessi a rischio](active-directory-identityprotection.md#risky-sign-ins).  

## <a name="device-platforms"></a>Piattaforme del dispositivo

Le piattaforme del dispositivo sono caratterizzate dal sistema operativo in esecuzione sul dispositivo. Azure AD identifica la piattaforma usando le informazioni offerte dal dispositivo, ad esempio l'agente utente. Poiché queste informazioni non sono state verificate, è consigliabile applicare un criterio a tutte le piattaforme, bloccando l'accesso, richiedendo la conformità ai criteri Intune o richiedendo l'aggiunta a un dominio per il dispositivo. L'impostazione predefinita è l'applicazione di un criterio a tutte le piattaforme del dispositivo. 


![Condizioni](./media/active-directory-conditional-access-conditions/24.png)

Per un elenco completo delle piattaforme del dispositivo supportate, vedere [Condizione per le piattaforme del dispositivo](active-directory-conditional-access-technical-reference.md#device-platform-condition).


Un caso d'uso comune per questa condizione è un criterio che limita l'accesso alle app cloud per i [dispositivi attendibili](active-directory-conditional-access-policy-connected-applications.md#trusted-devices). Per altri scenari, tra cui la condizione della piattaforma del dispositivo, vedere [Accesso condizionale basato su app di Azure Active Directory](active-directory-conditional-access-mam.md).


## <a name="locations"></a>Località

Le località consentono di definire le condizioni che dipendono dal punto in cui è stato avviato un tentativo di connessione. 
     
![Condizioni](./media/active-directory-conditional-access-conditions/25.png)

Casi d'uso comuni per questa condizione sono criteri che:

- Richiedono l'autenticazione a più fattori per gli utenti che accedono a un servizio quando non sono connessi alla rete aziendale.  

- Bloccano l'accesso per gli utenti che accedono a un servizio da specifici paesi o aree geografiche. 

Per altre informazioni, vedere [Condizioni della posizione nell'accesso condizionale di Azure Active Directory](active-directory-conditional-access-locations.md).


## <a name="client-apps"></a>App client

La condizione delle app client consente di applicare un criterio a diversi tipi di applicazioni, ad esempio:

- Servizi e siti Web
- App per dispositivi mobili e applicazioni desktop 

![Condizioni](./media/active-directory-conditional-access-conditions/04.png)

Un'applicazione viene classificata come:

- Un servizio o un sito Web se usa i protocolli SSO Web, SAML, WS-Fed o OpenID Connect per un client riservato.

- Un'app per dispositivi mobili o un'applicazione desktop, se usa l'app per dispositivi mobili OpenID Connect per un client nativo.

Per un elenco completo delle app client che è possibile usare nel criterio di accesso condizionale, vedere [Documentazione tecnica sull'accesso condizionale di Azure Active Directory](active-directory-conditional-access-technical-reference.md#client-apps-condition).

Casi d'uso comuni per questa condizione sono criteri che:

- Richiedono un [dispositivo conforme](active-directory-conditional-access-policy-connected-applications.md) per applicazioni desktop e app per dispositivi mobili che scaricano grandi quantità di dati nel dispositivo e allo stesso tempo consentono l'accesso al browser da qualsiasi dispositivo.

- Bloccano l'accesso da applicazioni Web, ma lo consentono dalle applicazioni desktop e per dispositivi mobili.

Oltre a usare i protocolli di autenticazione moderni e SSO Web, è possibile applicare questa condizione alle applicazioni di posta elettronica che usano Exchange ActiveSync, ad esempio le app di posta elettronica native della maggior parte degli smartphone. Attualmente, le app client che usano protocolli legacy devono essere protette con AD FS.

 Per altre informazioni, vedere:

- [Configurare SharePoint Online ed Exchange Online per l'accesso condizionale di Azure Active Directory](active-directory-conditional-access-no-modern-authentication.md)
 
- [Accesso condizionale basato su app di Azure Active Directory](active-directory-conditional-access-mam.md) 








## <a name="next-steps"></a>Passaggi successivi

- Per informazioni su come configurare criteri di accesso condizionale, vedere [Introduzione all'accesso condizionale in Azure Active Directory](active-directory-conditional-access-azure-portal-get-started.md).

- Se si è pronti per configurare i criteri di accesso condizionale per l'ambiente in uso, vedere il [Procedure consigliate per l'accesso condizionale in Azure Active Directory](active-directory-conditional-access-best-practices.md). 

