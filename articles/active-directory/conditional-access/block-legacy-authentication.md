---
title: Bloccare l'autenticazione legacy - Azure Active DirectoryBlock legacy authentication - Azure Active Directory
description: Informazioni su come migliorare il livello di sicurezza bloccando l'autenticazione legacy tramite l'accesso condizionale di Azure AD.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 03/26/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, dawoo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 76dd07a59a9fa7c0d6231a766ff4090c11f9f5bb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80331917"
---
# <a name="how-to-block-legacy-authentication-to-azure-ad-with-conditional-access"></a>Procedura: Bloccare l'autenticazione legacy in Azure AD con accesso condizionaleHow to: Block legacy authentication to Azure AD with Conditional Access   

Per consentire agli utenti di accedere facilmente alle app cloud, Azure Active Directory (Azure AD) supporta una vasta gamma di protocolli di autenticazione, inclusa l'autenticazione legacy. Tuttavia, i protocolli legacy non supportano l'autenticazione a più fattori (MFA). La MFA è in molti ambienti un requisito comune contro il furto di identità di indirizzo. 

Alex Weinert, Director of Identity Security presso Microsoft, nel suo post di blog del 12 marzo 2020 [New tools to block legacy authentication nella tua organizzazione](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/new-tools-to-block-legacy-authentication-in-your-organization/ba-p/1225302#) sottolinea perché le organizzazioni devono bloccare l'autenticazione legacy e quali strumenti aggiuntivi Microsoft fornisce per eseguire questa attività:

> Affinché l'autenticazione a più fattori sia efficace, è inoltre necessario bloccare l'autenticazione legacy. Questo perché i protocolli di autenticazione legacy come POP, SMTP, IMAP e MAPI non possono applicare l'autenticazione a più fattori, rendendoli punti di ingresso preferiti per gli avversari che attaccano l'organizzazione...
> 
>... I numeri sull'autenticazione legacy da un'analisi del traffico di Azure Active Directory (Azure AD) sono gravi:
> 
> - Più del 99% degli attacchi di spray per le password utilizza protocolli di autenticazione legacy
> - Più del 97% degli attacchi di ripieno delle credenziali utilizza l'autenticazione legacy
> - Account di Azure AD nelle organizzazioni che hanno disabilitato l'autenticazione legacy esperienza 67% meno compromessi rispetto a quelli in cui è abilitata l'autenticazione legacy
>

Se l'ambiente è pronto a bloccare l'autenticazione legacy per migliorare la protezione del tenant, è possibile raggiungere questo obiettivo con l'accesso condizionale. In questo articolo viene illustrato come configurare i criteri di accesso condizionale che bloccano l'autenticazione legacy per il tenant.

## <a name="prerequisites"></a>Prerequisiti

Questo articolo presuppone che l'utente abbia familiarità con: 

- Concetti di base dell'accesso condizionale di Azure ADThe [basic concepts](overview.md) of Azure AD Conditional Access 
- Procedure consigliate per la configurazione dei criteri di accesso condizionale nel portale di AzureThe [best practices](best-practices.md) for configuring Conditional Access policies in the Azure portal

## <a name="scenario-description"></a>Descrizione dello scenario

Azure AD supporta diversi dei protocolli di autenticazione e autorizzazione più ampiamente usati. L'autenticazione legacy fa riferimento ai protocolli che usano l'autenticazione di base. In genere, questi protocolli non possono applicare nessun tipo di secondo fattore di autenticazione. Alcuni esempi di app che si basano sull'autenticazione legacy:

- App Microsoft Office meno recenti
- App che usano protocolli di posta elettronica quali POP, IMAP e SMTP

Al giorno d’oggi, l'autenticazione a fattore singolo (ad esempio, nome utente e password) non è più sufficiente. Le password non sono sicure perché sono facili da indovinare e le persone non sanno scegliere password efficaci. Le password sono anche vulnerabili a numerosi attacchi, ad esempio il phishing e lo spray password. Una delle operazioni più semplici da effettuare per proteggersi dalle minacce relative alle password è implementare la MFA. Con la MFA, anche se un utente malintenzionato entra in possesso di una password utente, la sola password non è sufficiente per l’autenticazione e l’accesso ai dati.

Come si può impedire alle app che usano l'autenticazione legacy di accedere alle risorse dei tenant? Si consiglia di bloccarli con un criterio di accesso condizionale. Se necessario, è possibile autorizzare solo determinati utenti e percorsi di rete specifici per l’uso delle app basate sull’autenticazione legacy.

I criteri di accesso condizionale vengono applicati dopo il completamento dell'autenticazione con primo fattore. Pertanto l'accesso condizionale non è da intendersi come una prima misura difensiva da attacchi Denial of Service (DoS), ma può utilizzare segnali da questi eventi, come il livello di rischio di accesso, la posizione della richiesta e così via, per determinare l'accesso.

## <a name="implementation"></a>Implementazione

In questa sezione viene illustrato come configurare un criterio di accesso condizionale per bloccare l'autenticazione legacy. 

### <a name="legacy-authentication-protocols"></a>Protocolli di autenticazione legacy

Le seguenti opzioni sono considerate protocolli di autenticazione legacy

- SMTP autenticato - Utilizzato dai client POP e IMAP per inviare messaggi di posta elettronica.
- Individuazione automatica - Utilizzato dai client Outlook e EAS per trovare e connettersi alle cassette postali in Exchange Online.
- Exchange Online PowerShell - Utilizzato per connettersi a Exchange Online con PowerShell remoto. Se si blocca l'autenticazione di base per PowerShell di Exchange Online, è necessario utilizzare il modulo PowerShell di Exchange Online per connettersi. Per istruzioni, vedere [Connettersi a Exchange Online PowerShell utilizzando l'autenticazione a più fattori](/powershell/exchange/exchange-online/connect-to-exchange-online-powershell/mfa-connect-to-exchange-online-powershell).
- Servizi Web Exchange (EWS): interfaccia di programmazione utilizzata da Outlook, Outlook per Mac e app di terze parti.
- IMAP4 - Utilizzato dai client di posta elettronica IMAP.
- MAPI su HTTP (MAPI/HTTP) - Utilizzato da Outlook 2010 e versioni successive.
- Rubrica offline -OAB- Una copia delle raccolte di elenchi di indirizzi scaricate e utilizzate da Outlook.
- Outlook via Internet (RPC su HTTP) - Utilizzato da Outlook 2016 e versioni precedenti.
- Servizio Outlook - Utilizzato dall'app Posta e Calendario per Windows 10.
- POP3 - Utilizzato dai client di posta elettronica POP.
- Reporting Web Services: utilizzato per recuperare i dati dei report in Exchange Online.
- Altri client - Altri protocolli identificati come l'utilizzo dell'autenticazione precedente.

Per altre informazioni su questi protocolli e servizi di autenticazione, vedere Report attività di accesso nel portale di Azure Active Directory.For more information about these authentication protocols and services, see [Sign-in activity reports in the Azure Active Directory portal](../reports-monitoring/concept-sign-ins.md#filter-sign-in-activities).

### <a name="identify-legacy-authentication-use"></a>Identificare l'utilizzo dell'autenticazione legacy

Prima di poter bloccare l'autenticazione legacy nella directory, devi prima capire se gli utenti dispongono di app che usano l'autenticazione legacy e come influisce sulla directory complessiva. I log di accesso di Azure AD possono essere usati per capire se si usa l'autenticazione legacy.

1. Passare al **portale** > di Azure Ad Azure**Access-ins****Azure Active Directory** > di Azure .
1. Aggiungere la colonna App client se non viene visualizzata facendo clic su **Colonne** > **Client App**.
1. **Aggiungere filtri** > **App client** > selezionare tutti i protocolli di autenticazione legacy e fare clic su **Applica**.

Il filtro mostrerà solo i tentativi di accesso effettuati dai protocolli di autenticazione legacy. Cliccando su ogni singolo tentativo di accesso ti mostrerà ulteriori dettagli. Il campo **App client** nella scheda Informazioni **di base** indicherà quale protocollo di autenticazione legacy è stato utilizzato.

Questi registri indicheranno quali utenti dipendono ancora dall'autenticazione legacy e quali applicazioni utilizzano protocolli legacy per effettuare richieste di autenticazione. Per gli utenti che non vengono visualizzati in questi log e vengono confermati di non utilizzare l'autenticazione legacy, implementare un criterio di accesso condizionale solo per questi utenti.

### <a name="block-legacy-authentication"></a>Bloccare l'autenticazione legacy 

In un criterio di accesso condizionale è possibile impostare una condizione legata alle app client usate per accedere alle risorse. La condizione per le app client consente di restringere l'ambito alle app che usano l'autenticazione legacy selezionando **Altri client** per **App per dispositivi mobili e client desktop**.

![Altri client](./media/block-legacy-authentication/01.png)

Per bloccare l'accesso per queste App, è necessario selezionare **Blocca accesso**.

![Blocca accesso](./media/block-legacy-authentication/02.png)

### <a name="select-users-and-cloud-apps"></a>Selezionare utenti e app cloud

Di solito si pensa che, per bloccare l'autenticazione legacy per la propria organizzazione, sia necessario selezionare:

- tutti gli utenti
- Tutte le app cloud
- Blocca accesso

![Assegnazioni](./media/block-legacy-authentication/03.png)

Azure dispone di una funzionalità di sicurezza che impedisce di creare criteri di questo tipo perché questa configurazione viola [le procedure consigliate](best-practices.md) per i criteri di accesso condizionale.
 
![Configurazione di criteri non supportata](./media/block-legacy-authentication/04.png)

La funzionalità di protezione è necessaria perché *Blocca tutti gli utenti e tutte le app cloud* ha la possibilità di impedire l’accesso ai tenant da parte dell'intera organizzazione. È necessario escludere almeno un utente per soddisfare il requisito minimo della procedura consigliata. È anche possibile escludere un ruolo della directory.

![Configurazione di criteri non supportata](./media/block-legacy-authentication/05.png)

È possibile soddisfare questa funzionalità di protezione escludendo un utente dal criterio. In teoria, è necessario definire alcuni [account amministrativi di accesso di emergenza in Azure AD](../users-groups-roles/directory-emergency-access.md) ed escluderli dal criterio.

L'utilizzo della [modalità solo report](concept-conditional-access-report-only.md) quando si abilita il criterio per bloccare l'autenticazione legacy offre all'organizzazione l'opportunità di monitorare l'impatto del criterio.

## <a name="policy-deployment"></a>Distribuzione dei criteri

Prima di inserire i criteri nell'ambiente di produzione, occuparsi di:
 
- **Account del servizio**: identificare gli account utente utilizzati come account del servizio o da dispositivi, ad esempio i telefoni della sala conferenze. Assicurarsi che per questi account siano impostate password complesse e aggiungerli a un gruppo di esclusione.
- **Report sugli accessi**: esaminare il report sugli accessi e cercare il traffico di **altri client**. Identificare i principali utilizzi e determinare perché si verificano. In genere, il traffico viene generato da client Office meno recenti che non usano l'autenticazione moderna o da alcune app di posta elettronica di terze parti. Creare un piano per abbandonare queste app oppure, se l'impatto è ridotto, avvisare gli utenti che non è più possibile usare queste app.
 
Per altre informazioni, vedere [Come distribuire un nuovo criterio](best-practices.md#how-should-you-deploy-a-new-policy).

## <a name="what-you-should-know"></a>Informazioni utili

Il blocco dell'accesso tramite **Altri client** blocca anche Exchange Online PowerShell e Dynamics 365 utilizzando l'autenticazione di base.

La configurazione di un criterio per **Altri client** blocca l'intera organizzazione per determinati client come SPConnect. Questo blocco si verifica perché l'autenticazione di client meno recenti avviene in modo imprevisto. Questo problema non si applica alle principali applicazioni di Office, come i client di Office meno recenti.

Perché il criterio abbia effetto, possono essere necessarie fino a 24 ore.

È possibile selezionare tutti i controlli di concessione disponibili per la condizione **Altri client;** tuttavia, l'esperienza dell'utente finale è sempre la stessa - accesso bloccato.

Se si blocca l'autenticazione legacy usando la condizione **Altri client,** è anche possibile impostare la piattaforma del dispositivo e la condizione di posizione. Ad esempio, se si vuole bloccare solo l'autenticazione legacy per i dispositivi mobili, impostare la condizione **Piattaforma del dispositivo** selezionando:

- Android
- iOS
- Windows Phone

![Configurazione di criteri non supportata](./media/block-legacy-authentication/06.png)

## <a name="next-steps"></a>Passaggi successivi

- [Determinare l'impatto utilizzando la modalità solo report di accesso condizionaleDetermine impact using Conditional Access report-only](howto-conditional-access-report-only.md)
- Se non si ha ancora familiarità con la configurazione dei criteri di accesso condizionale, vedere [Richiedere l'autenticazione a più fattori per app specifiche con l'accesso condizionale](app-based-mfa.md) di Azure Active Directory per un esempio.
- Per altre informazioni sul supporto dell'autenticazione moderna, vedere [Funzionamento dell'autenticazione moderna per le app client di Office 2013 e Office 2016](/office365/enterprise/modern-auth-for-office-2013-and-2016) 
