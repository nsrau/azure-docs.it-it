---
title: Bloccare l'autenticazione legacy - Azure Active Directory
description: Informazioni su come migliorare il comportamento di sicurezza bloccando l'autenticazione legacy con l'accesso condizionale di Azure AD.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 05/13/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, dawoo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5d3df4eee14e5ce2f0638058efde0f80d0e5b051
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87275480"
---
# <a name="how-to-block-legacy-authentication-to-azure-ad-with-conditional-access"></a>Procedura: Bloccare l'autenticazione legacy ad Azure AD con l'accesso condizionale   

Per consentire agli utenti di accedere facilmente alle app cloud, Azure Active Directory (Azure AD) supporta una vasta gamma di protocolli di autenticazione, inclusa l'autenticazione legacy. I protocolli legacy, tuttavia, non supportano l'autenticazione a più fattori (MFA). La MFA è in molti ambienti un requisito comune contro il furto di identità di indirizzo. 

Alex Weinert, Director of Identity Security di Microsoft, nel post di blog del 12 marzo 2020 [Nuovi strumenti per bloccare l'autenticazione legacy in un'organizzazione](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/new-tools-to-block-legacy-authentication-in-your-organization/ba-p/1225302#) evidenzia i motivi per cui le organizzazioni dovrebbero bloccare l'autenticazione legacy nonché gli strumenti aggiuntivi offerti da Microsoft a tale scopo:

> Affinché l'autenticazione MFA sia efficace, è anche necessario bloccare l'autenticazione legacy. I protocolli di autenticazione legacy come POP, SMTP, IMAP e MAPI, infatti, non possono imporre l'autenticazione MFA e costituiscono pertanto i punti di ingresso preferiti dagli antagonisti che attaccano l'organizzazione...
> 
>...I numeri relativi all'autenticazione legacy risultanti da un'analisi del traffico di Azure Active Directory (Azure AD) sono chiari:
> 
> - Oltre il 99% degli attacchi password spraying usa protocolli di autenticazione legacy
> - Oltre il 97% degli attacchi credential stuffing usa l'autenticazione legacy
> - Gli account Azure AD delle organizzazioni che hanno disabilitato l'esperienza di autenticazione legacy sono soggetti al 67% di compromissioni in meno rispetto a quelli per cui l'autenticazione legacy è abilitata
>

Se l'ambiente è pronto per il blocco dell'autenticazione legacy per migliorare la protezione dei tenant, è possibile usare a tale scopo l'accesso condizionale. Questo articolo illustra come configurare i criteri di accesso condizionale che bloccano l'autenticazione legacy per un tenant.

## <a name="prerequisites"></a>Prerequisiti

Questo articolo presuppone che l'utente abbia familiarità con: 

- [Concetti di base](overview.md) dell'accesso condizionale di Azure AD 
- [Procedure consigliate](best-practices.md) per la configurazione dei criteri di accesso condizionale nel portale di Azure

## <a name="scenario-description"></a>Descrizione dello scenario

Azure AD supporta diversi dei protocolli di autenticazione e autorizzazione più ampiamente usati. L'autenticazione legacy fa riferimento ai protocolli che usano l'autenticazione di base. In genere, questi protocolli non possono applicare nessun tipo di secondo fattore di autenticazione. Alcuni esempi di app che si basano sull'autenticazione legacy:

- App Microsoft Office meno recenti
- App che usano protocolli di posta elettronica quali POP, IMAP e SMTP

Al giorno d’oggi, l'autenticazione a fattore singolo (ad esempio, nome utente e password) non è più sufficiente. Le password non sono sicure perché sono facili da indovinare e le persone non sanno scegliere password efficaci. Le password sono anche vulnerabili a numerosi attacchi, ad esempio il phishing e lo spray password. Una delle operazioni più semplici da effettuare per proteggersi dalle minacce relative alle password è implementare la MFA. Con la MFA, anche se un utente malintenzionato entra in possesso di una password utente, la sola password non è sufficiente per l’autenticazione e l’accesso ai dati.

Come si può impedire alle app che usano l'autenticazione legacy di accedere alle risorse dei tenant? È consigliabile bloccarle con criteri di accesso condizionale. Se necessario, è possibile autorizzare solo determinati utenti e percorsi di rete specifici per l’uso delle app basate sull’autenticazione legacy.

I criteri di accesso condizionale vengono applicati dopo il completamento dell'autenticazione con primo fattore. Pertanto l'accesso condizionale non è da intendersi come una prima misura difensiva da attacchi Denial of Service (DoS), ma può utilizzare segnali da questi eventi, come il livello di rischio di accesso, la posizione della richiesta e così via, per determinare l'accesso.

## <a name="implementation"></a>Implementazione

Questa sezione illustra come configurare criteri di accesso condizionale per bloccare l'autenticazione legacy. 

### <a name="legacy-authentication-protocols"></a>Protocolli di autenticazione legacy

Le opzioni seguenti sono considerate protocolli di autenticazione legacy.

- SMTP autenticato: usato dai client POP e IMAP per inviare messaggi di posta elettronica.
- Individuazione automatica: usata dai client Outlook e EAS per trovare le cassette postali in Exchange Online e connettervisi.
- Exchange ActiveSync (EAS): usato per connettersi alle cassette postali in Exchange Online.
- Exchange Online PowerShell: usato per connettersi a Exchange Online con PowerShell remoto. Se si blocca l'autenticazione di base per Exchange Online PowerShell, è necessario usare il modulo Exchange Online PowerShell per connettersi. Per istruzioni, vedere [Connettersi a Exchange Online PowerShell con l'autenticazione a più fattori](/powershell/exchange/exchange-online/connect-to-exchange-online-powershell/mfa-connect-to-exchange-online-powershell).
- Servizi Web Exchange: interfaccia di programmazione usata da Outlook, Outlook per Mac e app di terze parti.
- IMAP4: usato dai client di posta elettronica IMAP.
- MAPI su HTTP (MAPI/HTTP): usato da Outlook 2010 e versioni successive.
- Rubrica offline: copia delle raccolte di elenchi di indirizzi scaricate e usate da Outlook.
- Outlook via Internet (RPC su HTTP): usato da Outlook 2016 e versioni precedenti.
- Servizio Outlook: usato dall'app di posta elettronica e di calendario per Windows 10.
- POP3: usato dai client di posta elettronica POP.
- Servizi Web per la creazione di report: funzionalità usata per recuperare i dati dei report in Exchange Online.
- Altri client: altri protocolli identificati come protocolli che utilizzano l'autenticazione legacy.

Per altre informazioni su questi protocolli e servizi di autenticazione, vedere [Report delle attività di accesso nel portale di Azure Active Directory](../reports-monitoring/concept-sign-ins.md#filter-sign-in-activities).

### <a name="identify-legacy-authentication-use"></a>Identificare l'uso dell'autenticazione legacy

Prima di poter bloccare l'autenticazione legacy nella directory, è necessario anzitutto capire se gli utenti usano app che sfruttano l'autenticazione legacy e il modo in cui questa influisce sulla directory complessiva. Per capire se viene usata l'autenticazione legacy è possibile usare i log di accesso di Azure AD.

1. Passare al **portale di Azure** > **Azure Active Directory** > **Accessi**.
1. Se non viene visualizzata, aggiungere la colonna App client facendo clic su **Colonne** > **App client**.
1. **Aggiungi filtri**  >  **App Client** > selezionare tutti i protocolli di autenticazione legacy. Selezionare all'esterno della finestra di dialogo di filtro Blox per applicare le selezioni e chiudere la finestra di dialogo.

Applicando il filtro verranno visualizzati solo i tentativi di accesso eseguiti dai protocolli di autenticazione legacy. Facendo clic su ogni singolo tentativo di accesso vengono visualizzati altri dettagli. Nel campo **App client** nella scheda **Info di base** sarà indicato il protocollo di autenticazione legacy che è stato usato.

Questi log indicano quali utenti ancora dipendono dall'autenticazione legacy e quali applicazioni usano protocolli legacy per eseguire le richieste di autenticazione. Per gli utenti che non sono riportati in questi log e per i quali è confermato che non usano l'autenticazione legacy, implementare criteri di accesso condizionale specifici.

### <a name="block-legacy-authentication"></a>Bloccare l'autenticazione legacy 

Nei criteri di accesso condizionale è possibile impostare una condizione collegata alle app client usate per accedere alle risorse. La condizione relativa alle app client consente di restringere l'ambito alle app che usano l'autenticazione legacy, selezionando **Client Exchange ActiveSync** e **Altri client** in **App per dispositivi mobili e client desktop**.

![Altri client](./media/block-legacy-authentication/01.png)

Per bloccare l'accesso per queste App, è necessario selezionare **Blocca accesso**.

![Blocca accesso](./media/block-legacy-authentication/02.png)

### <a name="select-users-and-cloud-apps"></a>Selezionare utenti e app cloud

Di solito si pensa che, per bloccare l'autenticazione legacy per la propria organizzazione, sia necessario selezionare:

- tutti gli utenti
- Tutte le app cloud
- Blocca accesso

![Assegnazioni](./media/block-legacy-authentication/03.png)

Azure offre una funzionalità di sicurezza che impedisce la creazione di criteri di questo tipo, perché questa configurazione viola le [procedure consigliate](best-practices.md) per i criteri di accesso condizionale.
 
![Configurazione di criteri non supportata](./media/block-legacy-authentication/04.png)

La funzionalità di protezione è necessaria perché *Blocca tutti gli utenti e tutte le app cloud* ha la possibilità di impedire l’accesso ai tenant da parte dell'intera organizzazione. È necessario escludere almeno un utente per soddisfare il requisito minimo della procedura consigliata. È anche possibile escludere un ruolo della directory.

![Configurazione di criteri non supportata](./media/block-legacy-authentication/05.png)

È possibile soddisfare questa funzionalità di protezione escludendo un utente dal criterio. In teoria, è necessario definire alcuni [account amministrativi di accesso di emergenza in Azure AD](../users-groups-roles/directory-emergency-access.md) ed escluderli dal criterio.

Usando la [modalità solo report](concept-conditional-access-report-only.md) quando vengono abilitati i criteri per bloccare l'autenticazione legacy, l'organizzazione potrà monitorare l'impatto dei criteri.

## <a name="policy-deployment"></a>Distribuzione dei criteri

Prima di inserire i criteri nell'ambiente di produzione, occuparsi di:
 
- **Account del servizio**: identificare gli account utente utilizzati come account del servizio o da dispositivi, ad esempio i telefoni della sala conferenze. Assicurarsi che per questi account siano impostate password complesse e aggiungerli a un gruppo di esclusione.
- **Report sugli accessi**: esaminare il report sugli accessi e cercare il traffico di **altri client**. Identificare i principali utilizzi e determinare perché si verificano. In genere, il traffico viene generato da client Office meno recenti che non usano l'autenticazione moderna o da alcune app di posta elettronica di terze parti. Creare un piano per abbandonare queste app oppure, se l'impatto è ridotto, avvisare gli utenti che non è più possibile usare queste app.
 
Per altre informazioni, vedere [Come distribuire un nuovo criterio](best-practices.md#how-should-you-deploy-a-new-policy).

## <a name="what-you-should-know"></a>Informazioni utili

Bloccando l'accesso con **Altri client** vengono bloccati anche Exchange Online PowerShell e Dynamics 365 con autenticazione di base.

La configurazione di un criterio per **Altri client** blocca l'intera organizzazione per determinati client come SPConnect. Questo blocco si verifica perché l'autenticazione di client meno recenti avviene in modo imprevisto. Questo problema non si applica alle principali applicazioni di Office, come i client di Office meno recenti.

Perché il criterio abbia effetto, possono essere necessarie fino a 24 ore.

È possibile selezionare tutti i controlli di concessione disponibili per la condizione **Altri client**, ma l'esperienza dell'utente finale sarà sempre la stessa: l'accesso è bloccato.

Se si blocca l'autenticazione legacy con la condizione **Altri client**, è anche possibile impostare la condizione della piattaforma e del percorso del dispositivo. Ad esempio, se si vuole bloccare solo l'autenticazione legacy per i dispositivi mobili, impostare la condizione **Piattaforma del dispositivo** selezionando:

- Android
- iOS
- Windows Phone

![Configurazione di criteri non supportata](./media/block-legacy-authentication/06.png)

## <a name="next-steps"></a>Passaggi successivi

- [Determinare l'impatto dell'uso della modalità di accesso condizionale solo report](howto-conditional-access-report-only.md)
- Se non si ha ancora familiarità con la configurazione dei criteri di accesso condizionale, per un esempio vedere [Richiedere l'autenticazione MFA per app specifiche con l'accesso condizionale di Azure Active Directory](app-based-mfa.md).
- Per altre informazioni sull'autenticazione moderna, vedere [Funzionamento dell'autenticazione moderna per le app client di Office 2013 e Office 2016](/office365/enterprise/modern-auth-for-office-2013-and-2016). 
- [Come configurare un dispositivo multifunzione o un'applicazione per l'invio di messaggi di posta elettronica tramite Office 365 e Microsoft 365](/exchange/mail-flow-best-practices/how-to-set-up-a-multifunction-device-or-application-to-send-email-using-office-3)
