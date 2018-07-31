---
title: Quali sono le condizioni dell'accesso condizionale di Azure Active Directory? | Microsoft Docs
description: Informazioni su come vengono usate le condizioni nell'accesso condizionale di Azure Active Directory per attivare un criterio.
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
ms.date: 06/13/2018
ms.author: markvi
ms.reviewer: calebb
ms.openlocfilehash: 59ae7e83bc2b76cb679b0baf1f5739f28ec7046c
ms.sourcegitcommit: 156364c3363f651509a17d1d61cf8480aaf72d1a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/25/2018
ms.locfileid: "39248281"
---
# <a name="what-are-conditions-in-azure-active-directory-conditional-access"></a>Quali sono le condizioni dell'accesso condizionale di Azure Active Directory? 

Con l'[accesso condizionale di Azure Active Directory (Azure AD)](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-conditional-access-azure-portal) è possibile controllare il modo in cui gli utenti autorizzati accedono alle app cloud. Nei criteri di accesso condizionale, si definisce la risposta al motivo che ha attivato i criteri. Un esempio di risposta è **Fare questo**. Un esempio di motivo è **Quando accade questo**.

![Motivo e risposta](./media/active-directory-conditional-access-conditions/10.png)


Nel contesto di accesso condizionale, **Quando accade questo** è una **condizione**. **Fare questo** è il **controllo di accesso**. La combinazione delle condizioni e dei controlli di accesso rappresenta un criterio di accesso condizionale.

![Criteri di accesso condizionale](./media/active-directory-conditional-access-conditions/61.png)


Le condizioni che non sono state configurate nei criteri di accesso condizionali non vengono applicate. Alcune condizioni sono [obbligatorie](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-conditional-access-best-practices) per applicare criteri di accesso condizionali in un ambiente. 

Questo articolo offre una panoramica delle condizioni e di come vengono usate in un criterio di accesso condizionale. 

## <a name="users-and-groups"></a>Utenti e gruppi

La condizione di utenti e gruppi è obbligatoria in un criterio di accesso condizionale. Nei criteri è possibile selezionare **Tutti gli utenti** o selezionare utenti e gruppi specifici.

![Utenti e gruppi](./media/active-directory-conditional-access-conditions/111.png)

Selezionando**Tutti gli utenti**, i criteri vengono applicati a tutti gli utenti nella directory, inclusi gli utenti guest.

**Selezionare Utenti e gruppi** consente di impostare le opzioni seguenti:

* **Tutti gli utenti guest** consente di destinare i criteri agli utenti guest B2B. Questa condizione corrisponde a qualsiasi account utente con l'attributo **userType** impostato su **guest**. È possibile usare questa impostazione nei casi in cui è necessario applicare criteri non appena l'account viene creato in un flusso di invito in Azure AD.

* **Ruoli della directory** consente di destinare criteri in base alle assegnazioni di ruolo dell'utente. Questa condizione supporta i ruoli della directory, ad esempio **Amministratore globale** oppure **Amministratore password**.

* **Utenti e gruppi** consente di definire come destinazione insiemi specifici di utenti. Ad esempio, è possibile selezionare un gruppo contenente tutti i membri del reparto Risorse umane, quando è selezionata un'app per le risorse umane come app cloud. Un gruppo può essere un gruppo di qualsiasi tipo in Azure AD, inclusi gruppi di sicurezza e distribuzione dinamici o assegnati.

È anche possibile escludere utenti o gruppi specifici da un criterio. Un caso d'uso comune è rappresentato dagli account del servizio nel caso in cui il criterio applichi l'autenticazione a più fattori (MFA). 

La definizione di insiemi di utenti specifici come destinazione è utile per la distribuzione di un nuovo criterio. In un nuovo criterio è necessario definire come destinazione solo l'insieme iniziale di utenti per convalidare il comportamento del criterio. 



## <a name="cloud-apps"></a>App cloud 

Un'app cloud è un servizio o un sito Web. Anche siti Web protetti da Azure AD Application Proxy sono app cloud. Per una descrizione dettagliata delle app cloud supportate, vedere [Assegnazioni di app cloud](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-conditional-access-technical-reference#cloud-apps-assignments). 

La condizione delle **app cloud** è obbligatoria in un criterio di accesso condizionale. Nei criteri è possibile selezionare **Tutte le app cloud** o selezionare app specifiche.

![Includere le app cloud](./media/active-directory-conditional-access-conditions/03.png)

- Selezionare **Tutte le app cloud** per applicare i criteri di base all'intera organizzazione. Usare questa selezione per un criterio che richiede l'autenticazione a più fattori quando viene rilevato il rischio di accesso per qualsiasi app cloud. I criteri applicati a **Tutte le app cloud** riguardano l'accesso a tutti i servizi e siti Web. Questa impostazione non è limitata alle app cloud presenti nell'elenco **Seleziona app**. 

- Selezionare app cloud singole per definire come destinazione servizi specifici in base al criterio. Ad esempio, è possibile richiedere agli utenti di avere un [dispositivo conforme](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-conditional-access-mam#app-based-or-compliant-device-policy-for-exchange-online-and-sharepoint-online) per accedere a SharePoint Online. Questo criterio viene applicato anche ad altri servizi quando accedono a contenuto di SharePoint. Un esempio è Microsoft Teams. 

È possibile escludere App specifiche da un criterio. Tuttavia, queste app sono comunque soggette ai criteri applicati ai servizi a cui accedono. 



## <a name="sign-in-risk"></a>Rischio di accesso

Il rischio di accesso è un'indicazione della probabilità (elevata, media o bassa) che un tentativo di accesso non sia stato eseguito dal legittimo proprietario di un account utente. Azure AD calcola il livello di rischio di accesso durante l'accesso di un utente. È possibile usare il livello di rischio di accesso calcolato come condizione nei criteri di accesso condizionale. 

![Livelli di rischio di accesso](./media/active-directory-conditional-access-conditions/22.png)

Per usare questa condizione, è necessario che sia abilitato [Azure Active Directory Identity Protection](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-identityprotection-enable).
 
Casi d'uso comuni per questa condizione sono criteri che hanno le seguenti protezioni: 

- Blocco degli utenti con un rischio di accesso elevato. Questa protezione impedisce agli utenti potenzialmente non legittimi di accedere alle app cloud. 
- Richiesta di autenticazione a più fattori per gli utenti con un rischio di accesso medio. Tramite l'applicazione dell'autenticazione a più fattori, si aumenta la certezza che l'accesso venga eseguito dal legittimo proprietario di un account.

Per altre informazioni, vedere [Accessi a rischio](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-reporting-security-risky-sign-ins).  

## <a name="device-platforms"></a>Piattaforme del dispositivo

La piattaforma del dispositivo è caratterizzata dal sistema operativo in esecuzione sul dispositivo. Azure AD identifica la piattaforma usando le informazioni offerte dal dispositivo, ad esempio l'agente utente. Informazione non verificata. È consigliabile che tutte le piattaforme abbiano criteri applicati a essi. I criteri devono bloccare l'accesso, richiedere la conformità ai criteri di Microsoft Intune o richiedere che il dispositivo sia aggiunto a un dominio. L'impostazione predefinita è l'applicazione di un criterio a tutte le piattaforme del dispositivo. 


![Configurazione delle piattaforme del dispositivo](./media/active-directory-conditional-access-conditions/24.png)

Per un elenco delle piattaforme del dispositivo supportate, vedere [Condizione per le piattaforme del dispositivo](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-conditional-access-technical-reference#device-platform-condition).


Un caso d'uso comune per questa condizione sono criteri che limitano l'accesso alle app cloud per i [dispositivi gestiti](active-directory-conditional-access-policy-connected-applications.md#managed-devices). Per altri scenari, tra cui la condizione della piattaforma del dispositivo, vedere [Accesso condizionale basato su app di Azure Active Directory](active-directory-conditional-access-mam.md).



## <a name="device-state"></a>Stato del dispositivo

La condizione dello stato del dispositivo consente all'aggiunta all'identità ibrida di Azure AD e ai dispositivi contrassegnati come conformi di essere esclusi dai criteri di accesso condizionali. Tale condizione è utile quando i criteri devono essere applicati solo al dispositivo non gestito per fornire ulteriore sicurezza della sessione. Ad esempio, applicare il controllo di sessione di Microsoft Cloud App Security solo quando un dispositivo non è gestito. 


![Configurazione dello stato del dispositivo](./media/active-directory-conditional-access-conditions/112.png)

Se si desidera bloccare l'accesso ai dispositivi non gestiti, implementare [l'accesso condizionale basato sul dispositivo](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-conditional-access-mam#app-based-or-compliant-device-policy-for-exchange-online-and-sharepoint-online).


## <a name="locations"></a>Località

Utilizzando le posizioni, è possibile definire le condizioni in base a dove è stata tentata una connessione. 

![Configurazione delle posizioni](./media/active-directory-conditional-access-conditions/25.png)

Casi d'uso comuni per questa condizione sono criteri che hanno le seguenti protezioni:

- Richiesta dell'autenticazione a più fattori per gli utenti che accedono a un servizio quando non sono connessi alla rete aziendale.  

- Bloccano l'accesso per gli utenti che accedono a un servizio da specifici paesi o aree geografiche. 

Per altre informazioni, vedere [Qual è la condizione della posizione nell'accesso condizionale di Azure Active Directory?](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-conditional-access-locations)


## <a name="client-apps"></a>App client

La condizione delle app client consente di applicare un criterio a diversi tipi di applicazioni. Esempi sono siti Web, servizi, app per dispositivi mobili e applicazioni desktop. 



Un'applicazione viene classificata come:

- Un servizio o un sito Web se usa i protocolli SSO Web, SAML, WS-Fed o OpenID Connect per un client riservato.

- Un'app per dispositivi mobili o un'applicazione desktop, se usa l'app per dispositivi mobili OpenID Connect per un client nativo.

Per un elenco delle app client che è possibile usare nei criteri di accesso condizionale, vedere [Condizione per le app client](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-conditional-access-technical-reference#client-apps-condition) nella documentazione tecnica sull'accesso condizionale di Azure Active Directory.

Casi d'uso comuni per questa condizione sono criteri che hanno le seguenti protezioni: 

- Richiedono un [dispositivo conforme](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-conditional-access-mam#app-based-or-compliant-device-policy-for-exchange-online-and-sharepoint-online) per applicazioni desktop e app per dispositivi mobili che scaricano grandi quantità di dati nel dispositivo. Allo stesso tempo, consentono l'accesso al browser da qualsiasi dispositivo.

- Bloccano l'accesso da applicazioni Web, ma lo consentono dalle applicazioni desktop e per dispositivi mobili.

È possibile applicare questa condizione ai protocolli di autenticazione SSO Web e moderni. È anche possibile applicarlo alle applicazioni di posta elettronica che usano Microsoft Exchange ActiveSync. Esempi sono le app di posta elettronica native presenti nella maggior parte degli Smartphone. Attualmente, le app client che usano protocolli legacy devono essere protette tramite il Servizio federativo Azure AD.

È possibile scegliere questa condizione per le app client solo se Microsoft Office 365 Exchange Online è l'unica app cloud selezionata.

![App cloud](./media/active-directory-conditional-access-conditions/32.png)

La selezione di **Exchange ActiveSync** come condizione per le app client è supportata solo se in un criterio non sono presenti altre condizioni configurate. Tuttavia, è possibile limitare l'ambito di questa condizione in modo da applicarla solo alle piattaforme supportate.

 
![Applicazione dei criteri solo alle piattaforme supportate](./media/active-directory-conditional-access-conditions/33.png)

L'applicazione di questa condizione solo alle piattaforme supportate equivale all'applicazione a tutte le piattaforme di dispositivi per una [condizione per le piattaforme di dispositivi](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-conditional-access-mam#app-based-or-compliant-device-policy-for-exchange-online-and-sharepoint-online).

![Configurazione delle piattaforme del dispositivo](./media/active-directory-conditional-access-conditions/34.png)


 Per altre informazioni, vedere questi articoli:

- [Configurare SharePoint Online ed Exchange Online per l'accesso condizionale di Azure Active Directory](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-conditional-access-no-modern-authentication).
 
- [Accesso condizionale basato su app di Azure Active Directory](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-conditional-access-mam). 


### <a name="legacy-authentication"></a>Autenticazione legacy  

L'accesso condizionale ora si applica ai client di Microsoft Office meno recenti che non supportano l'autenticazione moderna. Si applica anche ai client che usano protocolli di posta elettronica quali SMTP, POP e IMAP. Tramite l'autenticazione legacy, è possibile configurare i criteri, ad esempio **bloccare l'accesso da altri client**.


![Configurazione delle app client](./media/active-directory-conditional-access-conditions/160.png)  


#### <a name="known-issues"></a>Problemi noti

- La configurazione di un criterio per **Altri client** blocca l'intera organizzazione per determinati client come SPConnect. Questo blocco si verifica perché l'autenticazione di client meno recenti avviene in modo imprevisto. Questo problema non si applica alle principali applicazioni di Office, come i client di Office meno recenti. 

- Perché il criterio abbia effetto, possono essere necessarie fino a 24 ore. 


#### <a name="frequently-asked-questions"></a>Domande frequenti

**D:** Questa autenticazione blocca Servizi Web di Microsoft Exchange?

Dipende dal protocollo di autenticazione usato da Servizi Web di Exchange. Se l'applicazione Servizi Web di Exchange usa l'autenticazione moderna, viene gestita dall'app client **App per dispositivi mobili e client desktop**. L'autenticazione di base è coperta dall'app client **Altri client**.


**D:** Quali controlli è possibile usare per **Altri client**?

Per **Altri client** è possibile configurare qualsiasi controllo. Tuttavia, l'esperienza utente finale bloccherà l'accesso per tutti i casi. L'app **Altri client** non supporta controlli come MFA, il dispositivo conforme e l'aggiunta a un dominio. 
 
**D:** Quali condizioni è possibile usare per **Altri client**?

Per **Altri client** è possibile configurare qualsiasi condizione.

**D:** Exchange ActiveSync supporta tutte le condizioni e tutti i controlli?

No. Nell'elenco seguente viene riepilogato quello che Exchange ActiveSync può supportare: 

- Exchange ActiveSync supporta come destinazione solo utenti e gruppi. Non supporta guest e ruoli. Se è configurata una condizione per guest o ruolo, tutti gli utenti vengono bloccati. Exchange ActiveSync blocca tutti gli utenti perché non è possibile determinare se è necessario applicare i criteri per l'utente.

- Exchange ActiveSync funziona solo con Microsoft Exchange Online come app cloud. 

- Exchange ActiveSync non supporta nessuna condizione, tranne l'app client stessa. 

- È possibile configurare Exchange ActiveSync con qualsiasi controllo. Tutti i controlli, ad eccezione di conformità del dispositivo, causano un blocco.

**D:** I criteri si applicheranno a tutte le app client per impostazione predefinita d'ora in poi?

No. Non è prevista alcuna modifica al comportamento predefinito dei criteri. I criteri continueranno ad applicarsi ad applicazioni browser e per dispositivi mobili e a client desktop per impostazione predefinita.



## <a name="next-steps"></a>Passaggi successivi

- Per informazioni su come configurare criteri di accesso condizionale, vedere [Guida introduttiva: Richiedere MFA per app specifiche con l'accesso condizionale di Azure Active Directory](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-conditional-access-app-based-mfa).

- Per configurare i criteri di accesso condizionale per l'ambiente in uso, vedere [Procedure consigliate per l'accesso condizionale in Azure Active Directory](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-conditional-access-best-practices). 

