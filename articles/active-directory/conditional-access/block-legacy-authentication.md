---
title: Blocca autenticazione legacy-Azure Active Directory
description: Informazioni su come migliorare il comportamento di sicurezza bloccando l'autenticazione legacy usando Azure AD l'accesso condizionale.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 03/31/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, dawoo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 957aa77e18ea8f910f258d1dc59de0d093b0eab6
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80476640"
---
# <a name="how-to-block-legacy-authentication-to-azure-ad-with-conditional-access"></a>Procedura: bloccare l'autenticazione legacy per Azure AD con accesso condizionale   

Per consentire agli utenti di accedere facilmente alle app cloud, Azure Active Directory (Azure AD) supporta una vasta gamma di protocolli di autenticazione, inclusa l'autenticazione legacy. Tuttavia, i protocolli legacy non supportano multi-factor authentication. La MFA è in molti ambienti un requisito comune contro il furto di identità di indirizzo. 

Alex Weinert, Director of Identity Security di Microsoft, nel suo 12 marzo 2020 post di Blog sui [nuovi strumenti per bloccare l'autenticazione legacy nell'organizzazione enfatizza il](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/new-tools-to-block-legacy-authentication-in-your-organization/ba-p/1225302#) motivo per cui le organizzazioni devono bloccare l'autenticazione legacy e gli strumenti aggiuntivi forniti da Microsoft per completare questa attività:

> Per l'efficacia dell'autenticazione a più fattori, è anche necessario bloccare l'autenticazione legacy. Questo perché i protocolli di autenticazione legacy come POP, SMTP, IMAP e MAPI non possono imporre l'autenticazione a più fattori, rendendoli punti di ingresso preferiti per gli avversari che attaccano l'organizzazione...
> 
>... I numeri di autenticazione legacy da un'analisi del traffico Azure Active Directory (Azure AD) sono Stark:
> 
> - Più del 99% degli attacchi con spray per la password usa protocolli di autenticazione legacy
> - Più del 97% degli attacchi per le credenziali che usano l'autenticazione legacy
> - Azure AD gli account nelle organizzazioni che hanno disabilitato l'esperienza di autenticazione legacy 67% meno compromessi rispetto a quelli in cui è abilitata l'autenticazione legacy
>

Se l'ambiente è pronto a bloccare l'autenticazione legacy per migliorare la protezione del tenant, è possibile raggiungere questo obiettivo con l'accesso condizionale. Questo articolo illustra come configurare i criteri di accesso condizionale che bloccano l'autenticazione legacy per il tenant.

## <a name="prerequisites"></a>Prerequisiti

Questo articolo presuppone che l'utente abbia familiarità con: 

- [Concetti di base](overview.md) di Azure ad l'accesso condizionale 
- [Procedure consigliate](best-practices.md) per la configurazione dei criteri di accesso condizionale nella portale di Azure

## <a name="scenario-description"></a>Descrizione dello scenario

Azure AD supporta diversi dei protocolli di autenticazione e autorizzazione più ampiamente usati. L'autenticazione legacy fa riferimento ai protocolli che usano l'autenticazione di base. In genere, questi protocolli non possono applicare nessun tipo di secondo fattore di autenticazione. Alcuni esempi di app che si basano sull'autenticazione legacy:

- App Microsoft Office meno recenti
- App che usano protocolli di posta elettronica quali POP, IMAP e SMTP

Al giorno d’oggi, l'autenticazione a fattore singolo (ad esempio, nome utente e password) non è più sufficiente. Le password non sono sicure perché sono facili da indovinare e le persone non sanno scegliere password efficaci. Le password sono anche vulnerabili a numerosi attacchi, ad esempio il phishing e lo spray password. Una delle operazioni più semplici da effettuare per proteggersi dalle minacce relative alle password è implementare la MFA. Con la MFA, anche se un utente malintenzionato entra in possesso di una password utente, la sola password non è sufficiente per l’autenticazione e l’accesso ai dati.

Come si può impedire alle app che usano l'autenticazione legacy di accedere alle risorse dei tenant? È consigliabile semplicemente bloccarli con criteri di accesso condizionale. Se necessario, è possibile autorizzare solo determinati utenti e percorsi di rete specifici per l’uso delle app basate sull’autenticazione legacy.

I criteri di accesso condizionale vengono applicati dopo il completamento dell'autenticazione con primo fattore. Pertanto l'accesso condizionale non è da intendersi come una prima misura difensiva da attacchi Denial of Service (DoS), ma può utilizzare segnali da questi eventi, come il livello di rischio di accesso, la posizione della richiesta e così via, per determinare l'accesso.

## <a name="implementation"></a>Implementazione

In questa sezione viene illustrato come configurare un criterio di accesso condizionale per bloccare l'autenticazione legacy. 

### <a name="legacy-authentication-protocols"></a>Protocolli di autenticazione legacy

Le opzioni seguenti sono considerate protocolli di autenticazione legacy

- SMTP autenticato: usato dal POP e dal client IMAP per inviare messaggi di posta elettronica.
- Individuazione automatica: usata dai client Outlook e EAS per trovare e connettersi alle cassette postali in Exchange Online.
- PowerShell per Exchange Online: usato per connettersi a Exchange Online con PowerShell remoto. Se si blocca l'autenticazione di base per Exchange Online PowerShell, è necessario usare il modulo PowerShell di Exchange Online per connettersi. Per istruzioni, vedere [connettersi a Exchange Online PowerShell usando l'autenticazione a più fattori](/powershell/exchange/exchange-online/connect-to-exchange-online-powershell/mfa-connect-to-exchange-online-powershell).
- Servizi Web Exchange (EWS): un'interfaccia di programmazione usata da Outlook, Outlook per Mac e app di terze parti.
- IMAP4: usato dai client di posta elettronica IMAP.
- MAPI su HTTP (MAPI/HTTP): usato da Outlook 2010 e versioni successive.
- Rubrica non in linea (OAB): copia delle raccolte di elenchi di indirizzi scaricate e usate da Outlook.
- Outlook Anywhere (RPC su HTTP): usato da Outlook 2016 e versioni precedenti.
- Servizio Outlook: usato dall'app di posta elettronica e calendario per Windows 10.
- POP3: usato dai client di posta elettronica POP.
- Servizi Web di Reporting: consente di recuperare i dati del report in Exchange Online.
- Altri client: altri protocolli identificati come utilizzo dell'autenticazione legacy.

Per altre informazioni su questi protocolli e servizi di autenticazione, vedere [report delle attività di accesso nel portale di Azure Active Directory](../reports-monitoring/concept-sign-ins.md#filter-sign-in-activities).

### <a name="identify-legacy-authentication-use"></a>Identificare l'uso dell'autenticazione legacy

Prima di poter bloccare l'autenticazione legacy nella directory, è prima di tutto necessario comprendere se gli utenti hanno app che usano l'autenticazione legacy e come influiscono sulla directory complessiva. È possibile usare i log di accesso Azure AD per capire se si usa l'autenticazione legacy.

1. Passare al **portale di Azure** > **Azure Active Directory** > **accessi**.
1. Aggiungere la colonna app client se non viene visualizzata facendo clic su **colonne** > **client App**.
1. **Aggiungere filtri** > **app client** > selezionare tutti i protocolli di autenticazione legacy e fare clic su **applica**.

Con il filtro vengono visualizzati solo i tentativi di accesso eseguiti dai protocolli di autenticazione legacy. Se si fa clic su ogni singolo tentativo di accesso, vengono visualizzati altri dettagli. Il campo **app client** nella scheda **informazioni di base** indicherà quale protocollo di autenticazione legacy è stato usato.

Questi log indicheranno quali utenti sono ancora a seconda dell'autenticazione legacy e quali applicazioni usano protocolli legacy per eseguire richieste di autenticazione. Per gli utenti che non sono presenti in questi registri e che non usano l'autenticazione legacy, implementare i criteri di accesso condizionale solo per questi utenti.

### <a name="block-legacy-authentication"></a>Bloccare l'autenticazione legacy 

In un criterio di accesso condizionale è possibile impostare una condizione collegata alle app client usate per accedere alle risorse. La condizione per le app client consente di limitare l'ambito alle app usando l'autenticazione legacy selezionando **client di Exchange ActiveSync** e **altri client** in **app per dispositivi mobili e client desktop**.

![Altri client](./media/block-legacy-authentication/01.png)

Per bloccare l'accesso per queste App, è necessario selezionare **Blocca accesso**.

![Blocca accesso](./media/block-legacy-authentication/02.png)

### <a name="select-users-and-cloud-apps"></a>Selezionare utenti e app cloud

Di solito si pensa che, per bloccare l'autenticazione legacy per la propria organizzazione, sia necessario selezionare:

- tutti gli utenti
- Tutte le app cloud
- Blocca accesso

![Assegnazioni](./media/block-legacy-authentication/03.png)

Azure dispone di una funzionalità di sicurezza che impedisce la creazione di un criterio come questo perché questa configurazione viola le [procedure consigliate](best-practices.md) per i criteri di accesso condizionale.
 
![Configurazione di criteri non supportata](./media/block-legacy-authentication/04.png)

La funzionalità di protezione è necessaria perché *Blocca tutti gli utenti e tutte le app cloud* ha la possibilità di impedire l’accesso ai tenant da parte dell'intera organizzazione. È necessario escludere almeno un utente per soddisfare il requisito minimo della procedura consigliata. È anche possibile escludere un ruolo della directory.

![Configurazione di criteri non supportata](./media/block-legacy-authentication/05.png)

È possibile soddisfare questa funzionalità di protezione escludendo un utente dal criterio. In teoria, è necessario definire alcuni [account amministrativi di accesso di emergenza in Azure AD](../users-groups-roles/directory-emergency-access.md) ed escluderli dal criterio.

L'uso della [modalità solo report](concept-conditional-access-report-only.md) quando si Abilita il criterio per bloccare l'autenticazione legacy consente all'organizzazione di monitorare l'effetto dei criteri.

## <a name="policy-deployment"></a>Distribuzione dei criteri

Prima di inserire i criteri nell'ambiente di produzione, occuparsi di:
 
- **Account del servizio**: identificare gli account utente utilizzati come account del servizio o da dispositivi, ad esempio i telefoni della sala conferenze. Assicurarsi che per questi account siano impostate password complesse e aggiungerli a un gruppo di esclusione.
- **Report sugli accessi**: esaminare il report sugli accessi e cercare il traffico di **altri client**. Identificare i principali utilizzi e determinare perché si verificano. In genere, il traffico viene generato da client Office meno recenti che non usano l'autenticazione moderna o da alcune app di posta elettronica di terze parti. Creare un piano per abbandonare queste app oppure, se l'impatto è ridotto, avvisare gli utenti che non è più possibile usare queste app.
 
Per altre informazioni, vedere [Come distribuire un nuovo criterio](best-practices.md#how-should-you-deploy-a-new-policy).

## <a name="what-you-should-know"></a>Informazioni importanti

Il blocco dell'accesso con **altri client** blocca anche PowerShell per Exchange Online e Dynamics 365 usando l'autenticazione di base.

La configurazione di un criterio per **Altri client** blocca l'intera organizzazione per determinati client come SPConnect. Questo blocco si verifica perché l'autenticazione di client meno recenti avviene in modo imprevisto. Questo problema non si applica alle principali applicazioni di Office, come i client di Office meno recenti.

Perché il criterio abbia effetto, possono essere necessarie fino a 24 ore.

È possibile selezionare tutti i controlli di concessione disponibili per la condizione di **altri client** . Tuttavia, l'esperienza dell'utente finale è sempre lo stesso accesso bloccato.

Se si blocca l'autenticazione legacy usando la condizione di **altri client** , è anche possibile impostare la condizione della piattaforma e del percorso del dispositivo. Ad esempio, se si vuole bloccare solo l'autenticazione legacy per i dispositivi mobili, impostare la condizione **Piattaforma del dispositivo** selezionando:

- Android
- iOS
- Windows Phone

![Configurazione di criteri non supportata](./media/block-legacy-authentication/06.png)

## <a name="next-steps"></a>Passaggi successivi

- [Determinare l'effetto usando la modalità solo report di accesso condizionale](howto-conditional-access-report-only.md)
- Se non si ha ancora familiarità con la configurazione dei criteri di accesso condizionale, vedere richiedere l'autenticazione a più fattori [per app specifiche con Azure Active Directory l'accesso condizionale](app-based-mfa.md) per un esempio.
- Per altre informazioni sul supporto dell'autenticazione moderna, vedere funzionamento dell' [autenticazione moderna per le app client office 2013 e office 2016](/office365/enterprise/modern-auth-for-office-2013-and-2016) 
