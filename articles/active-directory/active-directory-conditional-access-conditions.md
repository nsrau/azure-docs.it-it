---
title: Condizioni nell'accesso condizionale di Azure Active Directory | Microsoft Docs
description: Informazioni su come vengono usate le assegnazioni nell'accesso condizionale di Azure Active Directory per attivare un criterio.
services: active-directory
keywords: accesso condizionale alle app, accesso condizionale con Azure AD, accesso sicuro alle risorse aziendali, criteri di accesso condizionale
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.component: protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/01/2018
ms.author: markvi
ms.reviewer: calebb
ms.openlocfilehash: 5f0ff092a7535448d48642e972d1d36652f1b83f
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/05/2018
ms.locfileid: "34735142"
---
# <a name="conditions-in-azure-active-directory-conditional-access"></a>Condizioni nell'accesso condizionale di Azure Active Directory 

Con l'[accesso condizionale di Azure Active Directory (Azure AD)](active-directory-conditional-access-azure-portal.md) è possibile controllare il modo in cui gli utenti autorizzati accedono alle app cloud. Nei criteri di accesso condizionale si definisce la risposta ("fare questo") al motivo che ha attivato i criteri ("quando accade questo"). 

![Controllo](./media/active-directory-conditional-access-conditions/10.png)


Nel contesto dell'accesso condizionale:

- "**Quando accade questo**" è la **condizione**. 
- "**Fare questo**" è il **controllo di accesso**.

La combinazione delle condizioni con i controlli di accesso rappresenta un criterio di accesso condizionale.

![Controllo](./media/active-directory-conditional-access-conditions/61.png)


Le condizioni che non sono state configurate nei criteri di accesso condizionali non vengono applicate. Alcune condizioni sono [obbligatorie](active-directory-conditional-access-best-practices.md#whats-required-to-make-a-policy-work) per applicare criteri di accesso condizionali in un ambiente. 

Questo articolo offre una panoramica delle condizioni e di come vengono usate in un criterio di accesso condizionale. 

## <a name="users-and-groups"></a>Utenti e gruppi

La condizione di utenti e gruppi è obbligatoria in un criterio di accesso condizionale. Nei criteri è possibile selezionare **Tutti gli utenti** o selezionare utenti e gruppi specifici.

![Controllo](./media/active-directory-conditional-access-conditions/111.png)

Quando si seleziona:

- **Tutti gli utenti** consente di applicare i criteri a tutti gli utenti nella directory, inclusi gli utenti guest.

- **Selezionare Utenti e gruppi** consente di impostare le opzioni seguenti:

    - **Tutti gli utenti guest** consente di destinare i criteri agli utenti guest B2B. Questa condizione corrisponde a qualsiasi account utente con l'attributo *userType* impostato su *guest*. È possibile usare questa impostazione nei casi in cui è necessario applicare dei criteri non appena l'account viene creato in un flusso di invito in Azure AD.

    - **Ruoli della directory** consente di destinare dei criteri in base alle assegnazioni di ruolo dell'utente. Questa condizione supporta i ruoli della directory, ad esempio *Amministratore globale* oppure *Amministratore password*.

    - **Utenti e gruppi** consente di scegliere come destinazione insiemi di utenti specifici. Ad esempio, è possibile selezionare un gruppo contenente tutti i membri del reparto Risorse umane, quando è selezionata un'app per le risorse umane come app cloud.

Un gruppo può essere un gruppo di qualsiasi tipo in Azure AD, inclusi gruppi di sicurezza e distribuzione dinamici o assegnati

È anche possibile escludere utenti o gruppi specifici da un criterio. Un caso d'uso comune è rappresentato dagli account del servizio nel caso in cui il criterio applichi l'autenticazione a più fattori (MFA). 

La definizione di insiemi di utenti specifici come destinazione è utile per la distribuzione di un nuovo criterio. In un nuovo criterio è necessario definire come destinazione solo l'insieme iniziale di utenti per convalidare il comportamento del criterio. 



## <a name="cloud-apps"></a>App cloud 

Un'app cloud è un servizio o un sito Web. Sono inclusi i siti Web protetti da Azure Application Proxy. Per una descrizione dettagliata delle app cloud supportate, vedere [Assegnazioni di app cloud](active-directory-conditional-access-technical-reference.md#cloud-apps-assignments).    

La condizione delle app cloud è obbligatoria in un criterio di accesso condizionale. Nei criteri è possibile selezionare **Tutte le app cloud** o selezionare app specifiche.

![Controllo](./media/active-directory-conditional-access-conditions/03.png)

È possibile selezionare:

- **Tutte le app cloud** per applicare i criteri di base all'intera organizzazione. Un caso d'uso comune per questa selezione è un criterio che richiede l'autenticazione a più fattori quando viene rilevato il rischio di accesso per qualsiasi app cloud. I criteri applicati a **Tutte le app cloud** riguardano l'accesso a tutti i servizi e il sito Web. Questa impostazione non è limitata alle app cloud presenti nell'elenco **Seleziona app cloud**.

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


Un caso d'uso comune per questa condizione sono criteri che limitano l'accesso alle app cloud per i [dispositivi gestiti](active-directory-conditional-access-policy-connected-applications.md#managed-devices). Per altri scenari, tra cui la condizione della piattaforma del dispositivo, vedere [Accesso condizionale basato su app di Azure Active Directory](active-directory-conditional-access-mam.md).



## <a name="device-state"></a>Stato del dispositivo

La condizione dello stato del dispositivo consente all'aggiunta all'identità ibrida di Azure AD e ai dispositivi contrassegnati come conformi di essere esclusi dai criteri di accesso condizionali. È utile quando i criteri devono essere applicati solo al dispositivo non gestito per fornire ulteriore sicurezza della sessione. Ad esempio, applicare il controllo di sessione di Microsoft Cloud App Security solo quando un dispositivo non è gestito. 


![Condizioni](./media/active-directory-conditional-access-conditions/112.png)

Se si desidera bloccare l'accesso ai dispositivi non gestiti, è necessario implementare [l'accesso condizionale basato sul dispositivo](active-directory-conditional-access-policy-connected-applications.md).


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



Un'applicazione viene classificata come:

- Un servizio o un sito Web se usa i protocolli SSO Web, SAML, WS-Fed o OpenID Connect per un client riservato.

- Un'app per dispositivi mobili o un'applicazione desktop, se usa l'app per dispositivi mobili OpenID Connect per un client nativo.

Per un elenco completo delle app client che è possibile usare nei criteri di accesso condizionale, vedere [Condizione per le app client](active-directory-conditional-access-technical-reference.md#client-apps-condition) nella documentazione tecnica sull'accesso condizionale di Azure Active Directory.

Casi d'uso comuni per questa condizione sono criteri che:

- Richiedono un [dispositivo conforme](active-directory-conditional-access-policy-connected-applications.md) per applicazioni desktop e app per dispositivi mobili che scaricano grandi quantità di dati nel dispositivo e allo stesso tempo consentono l'accesso al browser da qualsiasi dispositivo.

- Bloccano l'accesso da applicazioni Web, ma lo consentono dalle applicazioni desktop e per dispositivi mobili.

Oltre a usare i protocolli di autenticazione moderni e SSO Web, è possibile applicare questa condizione alle applicazioni di posta elettronica che usano Exchange ActiveSync, ad esempio le app di posta elettronica native della maggior parte degli smartphone. Attualmente, le app client che usano protocolli legacy devono essere protette con AD FS.

È possibile scegliere questa condizione solo se **Office 365 Exchange Online** è l'unica app cloud selezionata.

![App cloud](./media/active-directory-conditional-access-conditions/32.png)

La selezione di **Exchange ActiveSync** come condizione per le app client è supportata solo se in un criterio non sono presenti altre condizioni configurate. Tuttavia, è possibile limitare l'ambito di questa condizione in modo da applicarla solo alle piattaforme supportate.

 
![Piattaforme supportate](./media/active-directory-conditional-access-conditions/33.png)

L'applicazione di questa condizione solo alle piattaforme supportate equivale all'applicazione a tutte le piattaforme di dispositivi per una [condizione per le piattaforme di dispositivi](active-directory-conditional-access-conditions.md#device-platforms).

![Piattaforme supportate](./media/active-directory-conditional-access-conditions/34.png)


 Per altre informazioni, vedere:

- [Configurare SharePoint Online ed Exchange Online per l'accesso condizionale di Azure Active Directory](active-directory-conditional-access-no-modern-authentication.md)
 
- [Accesso condizionale basato su app di Azure Active Directory](active-directory-conditional-access-mam.md) 


### <a name="legacy-authentication"></a>Autenticazione legacy  

L'accesso condizionale si applica ora ai client di Office meno recenti che non supportano l'autenticazione moderna, nonché ai client che usano protocolli di posta come POP, IMAP, SMTP e così via. In questo modo, è possibile configurare criteri come il **blocco dell'accesso da altri client**.


![Autenticazione legacy](./media/active-directory-conditional-access-conditions/160.png)
 



#### <a name="known-issues"></a>Problemi noti

- La configurazione di un criterio per **Altri client** blocca l'intera organizzazione per determinati client come SPConnect. Questo è dovuto al fatto che i client meno recenti si autenticano in modi imprevisti. Questo problema non si applica alle principali applicazioni di Office, come i client di Office meno recenti. 

- Perché il criterio abbia effetto, possono essere necessarie fino a 24 ore. 


#### <a name="frequently-asked-questions"></a>Domande frequenti

**Servizi Web Exchange viene bloccato?**

Dipende dal protocollo di autenticazione usato da Servizi Web Exchange. Se l'applicazione Servizi Web Exchange usa l'autenticazione moderna, viene gestita dall'app client "App per dispositivi mobili e client desktop". Se l'applicazione Servizi Web Exchange usa l'autenticazione di base, viene gestita dall'app client "Altri client".


**Quali controlli è possibile usare per Altri client?**

Per "Altri client" è possibile configurare qualsiasi controllo. Tuttavia, l'esperienza utente finale bloccherà l'accesso per tutti i casi. L'app "Altri client" non supporta controlli come MFA, il dispositivo conforme, l'aggiunta a un dominio e così via. 
 
**Quali condizioni è possibile usare per Altri client?**

Per "Altri client" è possibile configurare qualsiasi condizione.

**Exchange ActiveSync supporta tutte le condizioni e tutti i controlli?**

di serie Ecco un riepilogo del supporto di Exchange ActiveSync (EAS):

- EAS supporta solo utenti e gruppi come destinazione. Non supporta guest e ruoli. Se si configura la condizione guest/ruolo, vengono bloccati tutti gli utenti, perché non è possibile determinare se il criterio debba essere applicato o meno all'utente.

- EAS funziona solo con Exchange come app cloud. 

- EAS non supporta alcuna condizione eccetto l'app client stessa.

- EAS può essere configurato con qualsiasi controllo (tutti eccetto la conformità del dispositivo causeranno un blocco).

**I criteri si applicheranno a tutte le app client per impostazione predefinita d'ora in poi?**

di serie Non è prevista alcuna modifica al comportamento predefinito dei criteri. I criteri continueranno ad applicarsi ad applicazioni browser e per dispositivi mobili e a client desktop per impostazione predefinita.



## <a name="next-steps"></a>Passaggi successivi

- Per informazioni su come configurare criteri di accesso condizionale, vedere [Introduzione all'accesso condizionale in Azure Active Directory](active-directory-conditional-access-azure-portal-get-started.md).

- Se si è pronti per configurare i criteri di accesso condizionale per l'ambiente in uso, vedere il [Procedure consigliate per l'accesso condizionale in Azure Active Directory](active-directory-conditional-access-best-practices.md). 

