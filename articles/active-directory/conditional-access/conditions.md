---
title: Quali sono le condizioni in Active Directory accesso condizionale di Azure? | Microsoft Docs
description: Informazioni su come le condizioni vengono utilizzate in Active Directory accesso condizionale di Azure per attivare un criterio.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 05/17/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8e6c18b7ffca83c8e0fe9576ec275f89b6db7b4f
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2019
ms.locfileid: "67509009"
---
# <a name="what-are-conditions-in-azure-active-directory-conditional-access"></a>Quali sono le condizioni in Active Directory accesso condizionale di Azure?

È possibile controllare l'accesso alle App cloud mediante [accesso condizionale di Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal). In un criterio di accesso condizionale si definisce la risposta ("quindi fare questo") per il motivo per attivare i criteri ("quando accade questo").

![Motivo e risposta](./media/conditions/10.png)

Nel contesto di accesso condizionale **in questo caso** viene chiamato un **condizione**. **Fare questo** è il **controllo di accesso**. La combinazione delle proprie condizioni e i controlli di accesso rappresenta un criterio di accesso condizionale.

![Criteri di accesso condizionale](./media/conditions/61.png)

Non vengono applicate le condizioni che nei criteri di accesso condizionale non è stato configurato. Alcune condizioni vengono [obbligatori](best-practices.md) per applicare un criterio di accesso condizionale per un ambiente.

Questo articolo viene fornita una panoramica delle condizioni e sul loro uso in un criterio di accesso condizionale. 

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

## <a name="cloud-apps-and-actions"></a>Le app cloud e le azioni

Un'app cloud è un sito Web, servizio o endpoint protetti da Azure AD Application Proxy. Per una descrizione dettagliata delle app cloud supportate, vedere [Assegnazioni di app cloud](technical-reference.md#cloud-apps-assignments). Il **Cloud apps o azioni** condizione è obbligatoria in un criterio di accesso condizionale. Nei criteri, è possibile selezionare **tutte le app cloud** oppure specificare le app con **selezionare le app**.

Le organizzazioni possono scegliere tra le opzioni seguenti:

* **Tutte le app cloud** quando si applicano i criteri di base si applicano all'intera organizzazione. Utilizzare questa selezione per i criteri che richiedono l'autenticazione a più fattori quando viene rilevato rischio di accesso per qualsiasi app cloud. Applicata un criterio a tutte le app cloud si applica per l'accesso a tutti i siti Web e servizi. Questa impostazione non è limitata alle App cloud che vengono visualizzati nell'elenco di App selezionare.
* **Seleziona app** per definire i servizi specifici in base ai propri criteri. Ad esempio, è possibile richiedere agli utenti di avere un dispositivo conforme per accedere a SharePoint Online. Questo criterio viene applicato anche ad altri servizi quando accedono a contenuto di SharePoint. Un esempio è Microsoft Teams.

> [!NOTE]
> È possibile escludere App specifiche da un criterio. Tuttavia, queste app sono comunque soggette ai criteri applicati ai servizi a cui accedono.

**Le azioni utente** sono attività che possono essere eseguite da un utente. L'unica azione attualmente supportata è **registrare le informazioni di sicurezza (anteprima)** , che consente di criteri di accesso condizionale imporrà quando gli utenti che sono abilitati per la registrazione combinata tentano di registrare la sicurezza informazioni. Altre informazioni sono reperibili nell'articolo [combinato di abilitare la registrazione di informazioni di sicurezza (anteprima)](../authentication/howto-registration-mfa-sspr-combined.md).

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

Un caso d'uso comune per questa condizione sono criteri che limitano l'accesso alle app cloud per i [dispositivi gestiti](require-managed-devices.md). Per altri scenari, tra cui la condizione della piattaforma del dispositivo, vedere [accesso condizionale basato su app di Azure Active Directory](app-based-conditional-access.md).

## <a name="device-state"></a>Stato del dispositivo

La condizione di stato dispositivo esclude ibridi che aggiunti ad Azure AD i dispositivi e i dispositivi contrassegnati come conformi da criteri di accesso condizionale. 

![Configurazione dello stato del dispositivo](./media/conditions/112.png)

Tale condizione è utile quando i criteri devono essere applicati solo al dispositivo non gestito per fornire ulteriore sicurezza della sessione. Ad esempio, applicare il controllo di sessione di Microsoft Cloud App Security solo quando un dispositivo non è gestito. 

## <a name="locations"></a>Località

Utilizzando le posizioni, è possibile definire le condizioni in base a dove è stata tentata una connessione. 

![Configurazione delle posizioni](./media/conditions/25.png)

Casi d'uso comuni per questa condizione sono criteri che hanno le seguenti protezioni:

- Richiedere l'autenticazione a più fattori per utenti che accedono a un servizio quando sono connessi alla rete aziendale.  
- Bloccano l'accesso per gli utenti che accedono a un servizio da specifici paesi o aree geografiche. 

Per altre informazioni, vedere [qual è la condizione della posizione in Active Directory accesso condizionale di Azure?](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-locations).

## <a name="client-apps"></a>App client

Per impostazione predefinita, si applica un criterio di accesso condizionale per le app seguenti:

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

È possibile avere come destinazione solo questa impostazione per utenti e gruppi. Non supporta guest e ruoli. Se una condizione guest o il ruolo è configurata, tutti gli utenti sono bloccati perché l'accesso condizionale non è possibile determinare se applicare i criteri per l'utente o No.

Per altre informazioni, vedere:

- [Configurare SharePoint Online ed Exchange Online per Azure Active Directory l'accesso condizionale](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-no-modern-authentication).
- [Accesso condizionale basato su app di Azure Active Directory](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access). 

## <a name="next-steps"></a>Passaggi successivi

- Per scoprire come configurare un criterio di accesso condizionale, vedere [Guida introduttiva: Richiedere l'autenticazione MFA per App specifiche con Azure Active Directory l'accesso condizionale](app-based-mfa.md).
- Per configurare i criteri di accesso condizionale per l'ambiente, vedere la [procedure consigliate per l'accesso condizionale in Azure Active Directory](best-practices.md). 
