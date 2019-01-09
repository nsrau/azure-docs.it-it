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
ms.component: conditional-access
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/14/2018
ms.author: markvi
ms.reviewer: calebb
ms.openlocfilehash: 5f3857480bfe6c4d5c4efe759b11761123fa4441
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/02/2019
ms.locfileid: "53973061"
---
# <a name="what-are-conditions-in-azure-active-directory-conditional-access"></a>Quali sono le condizioni dell'accesso condizionale di Azure Active Directory? 

Con l'[accesso condizionale di Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal) è possibile controllare il modo in cui gli utenti accedono alle app cloud. In un criterio di accesso condizionale si definisce la risposta ("Allora fare questo") al motivo per cui viene attivato il criterio ("Quando accade questo"). 

![Motivo e risposta](./media/conditions/10.png)


Nel contesto di accesso condizionale, **Quando accade questo** è una **condizione**. **Fare questo** è il **controllo di accesso**. La combinazione delle condizioni e dei controlli di accesso rappresenta un criterio di accesso condizionale.

![Criteri di accesso condizionale](./media/conditions/61.png)


Le condizioni che non sono state configurate nei criteri di accesso condizionali non vengono applicate. Alcune condizioni sono [obbligatorie](best-practices.md) per applicare criteri di accesso condizionali in un ambiente. 

Questo articolo offre una panoramica delle condizioni e di come vengono usate in un criterio di accesso condizionale. 

## <a name="users-and-groups"></a>Utenti e gruppi

La condizione di utenti e gruppi è obbligatoria in un criterio di accesso condizionale. Nei criteri è possibile selezionare **Tutti gli utenti** o selezionare utenti e gruppi specifici.

![Utenti e gruppi](./media/conditions/111.png)

Selezionando**Tutti gli utenti**, i criteri vengono applicati a tutti gli utenti nella directory, inclusi gli utenti guest.

**Selezionare Utenti e gruppi** consente di impostare le opzioni seguenti:

* **Tutti gli utenti guest** consente di destinare i criteri agli utenti guest B2B. Questa condizione corrisponde a qualsiasi account utente con l'attributo **userType** impostato su **guest**. Usare questa impostazione nei casi in cui è necessario applicare criteri non appena l'account viene creato in un flusso di invito in Azure AD.

* **Ruoli della directory** consente di destinare criteri in base alle assegnazioni di ruolo dell'utente. Questa condizione supporta i ruoli della directory, ad esempio **Amministratore globale** oppure **Amministratore password**.

* **Utenti e gruppi** consente di definire come destinazione insiemi specifici di utenti. Ad esempio, è possibile selezionare un gruppo contenente tutti i membri del reparto Risorse umane, quando è selezionata un'app per le risorse umane come app cloud. Un gruppo può essere un gruppo di qualsiasi tipo in Azure AD, inclusi gruppi di sicurezza e distribuzione dinamici o assegnati.

È anche possibile escludere utenti o gruppi specifici da un criterio. Un caso d'uso comune è rappresentato dagli account del servizio nel caso in cui il criterio applichi l'autenticazione a più fattori (MFA). 

La definizione di insiemi di utenti specifici come destinazione è utile per la distribuzione di un nuovo criterio. In un nuovo criterio è necessario definire come destinazione solo l'insieme iniziale di utenti per convalidare il comportamento del criterio. 



## <a name="cloud-apps"></a>App cloud 

Un'app cloud è un servizio o un sito Web. Anche siti Web protetti da Azure AD Application Proxy sono app cloud. Per una descrizione dettagliata delle app cloud supportate, vedere [Assegnazioni di app cloud](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference#cloud-apps-assignments). 

La condizione delle **app cloud** è obbligatoria in un criterio di accesso condizionale. Nei criteri è possibile selezionare **Tutte le app cloud** o selezionare app specifiche.

![Includere le app cloud](./media/conditions/03.png)

Selezionare:

- **Tutte le app cloud** per applicare i criteri di base all'intera organizzazione. Usare questa selezione per un criterio che richiede l'autenticazione a più fattori quando viene rilevato il rischio di accesso per qualsiasi app cloud. I criteri applicati a **Tutte le app cloud** riguardano l'accesso a tutti i servizi e siti Web. Questa impostazione non è limitata alle app cloud presenti nell'elenco **Seleziona app**. 

- **Seleziona app** per definire i servizi specifici in base ai propri criteri. Ad esempio, è possibile richiedere agli utenti di avere un [dispositivo conforme](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-mam#app-based-or-compliant-device-policy-for-exchange-online-and-sharepoint-online) per accedere a SharePoint Online. Questo criterio viene applicato anche ad altri servizi quando accedono a contenuto di SharePoint. Un esempio è Microsoft Teams. 

È possibile escludere App specifiche da un criterio. Tuttavia, queste app sono comunque soggette ai criteri applicati ai servizi a cui accedono. 



## <a name="sign-in-risk"></a>Rischio di accesso

Il rischio di accesso è un'indicazione della probabilità (alta, media o bassa) che un accesso non sia stato eseguito dal legittimo proprietario di un account utente. Azure AD calcola il livello di rischio di accesso durante l'accesso di un utente. È possibile usare il livello di rischio di accesso calcolato come condizione nei criteri di accesso condizionale.

![Livelli di rischio di accesso](./media/conditions/22.png)

Per usare questa condizione, è necessario che sia abilitato [Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection-enable).
 
Casi d'uso comuni per questa condizione sono criteri che hanno le seguenti protezioni: 

- Blocco degli utenti con un rischio di accesso elevato. Questa protezione impedisce agli utenti potenzialmente non legittimi di accedere alle app cloud. 
- Richiesta di autenticazione a più fattori per gli utenti con un rischio di accesso medio. Tramite l'applicazione dell'autenticazione a più fattori, si aumenta la certezza che l'accesso venga eseguito dal legittimo proprietario di un account.

Per altre informazioni, vedere [bloccare l'accesso quando viene rilevato un rischio nella sessione](app-sign-in-risk.md).  

## <a name="device-platforms"></a>Piattaforme del dispositivo

La piattaforma del dispositivo è caratterizzata dal sistema operativo in esecuzione sul dispositivo. Azure AD identifica la piattaforma usando le informazioni offerte dal dispositivo, ad esempio l'agente utente. Informazione non verificata. È consigliabile che tutte le piattaforme abbiano criteri applicati a essi. I criteri devono bloccare l'accesso, richiedere la conformità ai criteri di Microsoft Intune o richiedere che il dispositivo sia aggiunto a un dominio. L'impostazione predefinita è l'applicazione di un criterio a tutte le piattaforme del dispositivo. 


![Configurazione delle piattaforme del dispositivo](./media/conditions/24.png)

Per un elenco delle piattaforme del dispositivo supportate, vedere [Condizione per le piattaforme del dispositivo](technical-reference.md#device-platform-condition).


Un caso d'uso comune per questa condizione sono criteri che limitano l'accesso alle app cloud per i [dispositivi gestiti](require-managed-devices.md). Per altri scenari, tra cui la condizione della piattaforma del dispositivo, vedere [Accesso condizionale basato su app di Azure Active Directory](app-based-conditional-access.md).



## <a name="device-state"></a>Stato del dispositivo

La condizione dello stato del dispositivo consente all'aggiunta all'identità ibrida di Azure AD e ai dispositivi contrassegnati come conformi di essere esclusi dai criteri di accesso condizionali. 


![Configurazione dello stato del dispositivo](./media/conditions/112.png)

Tale condizione è utile quando i criteri devono essere applicati solo al dispositivo non gestito per fornire ulteriore sicurezza della sessione. Ad esempio, applicare il controllo di sessione di Microsoft Cloud App Security solo quando un dispositivo non è gestito. 

## <a name="locations"></a>Località

Utilizzando le posizioni, è possibile definire le condizioni in base a dove è stata tentata una connessione. 

![Configurazione delle posizioni](./media/conditions/25.png)

Casi d'uso comuni per questa condizione sono criteri che hanno le seguenti protezioni:

- Richiesta dell'autenticazione a più fattori per gli utenti che accedono a un servizio quando non sono connessi alla rete aziendale.  

- Bloccano l'accesso per gli utenti che accedono a un servizio da specifici paesi o aree geografiche. 

Per altre informazioni, vedere [Qual è la condizione della posizione nell'accesso condizionale di Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-locations)


## <a name="client-apps"></a>App client

Per impostazione predefinita, si applica un criterio di accesso condizionale alle app seguenti:

- **[App del browser](technical-reference.md#supported-browsers)**  -  Le app del browser includono siti Web che usano SAML, WS-Federation oppure i protocolli OpenID Connect SSO web. Questo vale anche per qualsiasi sito web o servizio web che sia stato registrato come un client riservato OAuth. Ad esempio, il sito web Office 365 SharePoint. 

- **[App per dispositivi mobili e desktop tramite l'autenticazione moderna](technical-reference.md#supported-mobile-applications-and-desktop-clients)** - Queste app includono le app desktop di Office e le app per telefoni. 


Inoltre, è possibile assegnare un criterio per le app client specifiche che non usano l'autenticazione moderna, ad esempio:

- **[Client Exchange ActiveSync](conditions.md#exchange-activesync-clients)** - Quando un criterio blocca con Exchange ActiveSync, gli utenti interessati ricevono un messaggio di quarantena singolo con le informazioni sul motivo del blocco. Se necessario, il messaggio di posta elettronica include istruzioni per la registrazione del dispositivo con Intune.

- **[Altri client](block-legacy-authentication.md)** - Queste app includono i client che usano l'autenticazione di base con protocolli di posta elettronica come IMAP, MAPI, POP, SMTP e le app Office meno recenti che non usano l'autenticazione moderna. Per altre informazioni, vedere [Funzionamento dell'autenticazione moderna per le app client di Office 2013 e Office 2016](https://docs.microsoft.com/office365/enterprise/modern-auth-for-office-2013-and-2016).

![App client](./media/conditions/41.png)

Casi d'uso comuni per questa condizione sono criteri che hanno i seguenti requisiti:

- **[Richiedono un dispositivo gestito](require-managed-devices.md)** per applicazioni desktop e app per dispositivi mobili che scaricano dati in un dispositivo. Allo stesso tempo, consentono l'accesso al browser da qualsiasi dispositivo. Questo scenario impedisce il salvataggio e la sincronizzazione di documenti in un dispositivo non gestito. Con questo metodo, è possibile ridurre la probabilità di perdita dei dati se il dispositivo viene smarrito o rubato.

- **[Richiedono un dispositivo gestito](require-managed-devices.md)** per le app tramite ActiveSync per accedere a Exchange Online.

- **[Bloccano l'autenticazione legacy](block-legacy-authentication.md)** per Azure AD (altri client)

- Bloccano l'accesso da applicazioni Web, ma lo consentono dalle applicazioni desktop e per dispositivi mobili.



### <a name="exchange-activesync-clients"></a>Client Exchange ActiveSync

È possibile selezionare **client Exchange ActiveSync** solo se:


- Microsoft Office 365 Exchange Online è l'unica app cloud selezionata.

    ![App cloud](./media/conditions/32.png)

- Non si dispone di altre condizioni configurate in un criterio. Tuttavia, è possibile limitare l'ambito di questa condizione in modo da applicarla solo alle [piattaforme supportate](technical-reference.md#device-platform-condition).
 
    ![Applicazione dei criteri solo alle piattaforme supportate](./media/conditions/33.png)


Quando l'accesso è bloccato perché viene richiesto un [dispositivo gestito](require-managed-devices.md), gli utenti interessati ottengono un singolo messaggio di posta elettronica che li guida all'uso di Intune. 

Se è necessaria un'app approvata, gli utenti interessati ottengono linee guida per installare e usare il client di Outlook per dispositivi mobili.

In altri casi, ad esempio, se è obbligatoria l'autenticazione a più fattori, gli utenti interessati vengono bloccati, perché i client che usano l'autenticazione di base non supportano l'autenticazione a più fattori.

È possibile avere come destinazione solo questa impostazione per utenti e gruppi. Non supporta guest e ruoli. Se si configura una condizione guest o di ruolo, vengono bloccati tutti gli utenti poiché l'accesso condizionale non può determinare se il criterio debba essere applicato o meno all'utente.


 Per altre informazioni, vedere:

- [Configurare SharePoint Online ed Exchange Online per l'accesso condizionale di Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-no-modern-authentication).
 
- [Accesso condizionale basato su app di Azure Active Directory](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access). 



## <a name="next-steps"></a>Passaggi successivi

- Per scoprire come configurare un criterio di accesso condizionale, vedere [Avvio rapido: Richiedere l'autenticazione a più fattori per app specifiche con l'accesso condizionale di Azure Active Directory](app-based-mfa.md).

- Per configurare i criteri di accesso condizionale per l'ambiente in uso, vedere [Procedure consigliate per l'accesso condizionale in Azure Active Directory](best-practices.md). 

