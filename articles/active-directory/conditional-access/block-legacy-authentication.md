---
title: Come bloccare l'autenticazione legacy per Azure Active Directory (Azure AD) con accesso condizionale | Microsoft Docs
description: Informazioni su come migliorare il comportamento di sicurezza bloccando l'autenticazione legacy usando Azure AD l'accesso condizionale.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 06/17/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0d227b4cf7090cdc3177c7045d6137f30a13f71b
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/09/2019
ms.locfileid: "68931964"
---
# <a name="how-to-block-legacy-authentication-to-azure-ad-with-conditional-access"></a>Procedura: Blocca l'autenticazione legacy per Azure AD con accesso condizionale   

Per consentire agli utenti di accedere facilmente alle app cloud, Azure Active Directory (Azure AD) supporta una vasta gamma di protocolli di autenticazione, inclusa l'autenticazione legacy. Tuttavia, i protocolli legacy non supportano l'autenticazione a più fattori (MFA). La MFA è in molti ambienti un requisito comune contro il furto di identità di indirizzo. 

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

### <a name="identify-legacy-authentication-use"></a>Identificare l'uso dell'autenticazione legacy

Prima di poter bloccare l'autenticazione legacy nella directory, è prima di tutto necessario comprendere se gli utenti hanno app che usano l'autenticazione legacy e come influiscono sulla directory complessiva. È possibile usare i log di accesso Azure AD per capire se si usa l'autenticazione legacy.

1. Passare al **portale di Azure** > **Azure Active Directory** > **accessi**.
1. Aggiungere la colonna app client se non viene visualizzata facendo clic su **colonne** > **client App**.
1. **Aggiungere filtri** > **app client** > selezionare tutte le opzioni per **altri client** e fare clic su **applica**.

Con il filtro vengono visualizzati solo i tentativi di accesso eseguiti dai protocolli di autenticazione legacy. Se si fa clic su ogni singolo tentativo di accesso, vengono visualizzati altri dettagli. Il campo **app client** nella scheda **informazioni di base** indicherà quale protocollo di autenticazione legacy è stato usato.

Questi log indicheranno quali utenti sono ancora a seconda dell'autenticazione legacy e quali applicazioni usano protocolli legacy per eseguire richieste di autenticazione. Per gli utenti che non sono presenti in questi registri e che non usano l'autenticazione legacy, implementare i criteri di accesso condizionale solo per questi utenti.

### <a name="block-legacy-authentication"></a>Bloccare l'autenticazione legacy 

In un criterio di accesso condizionale è possibile impostare una condizione collegata alle app client usate per accedere alle risorse. La condizione per le app client consente di restringere l'ambito alle app che usano l'autenticazione legacy selezionando **Altri client** per **App per dispositivi mobili e client desktop**.

![Altri client](./media/block-legacy-authentication/01.png)

Per bloccare l'accesso per queste App, è necessario selezionare **Blocca accesso**.

![Blocca accesso](./media/block-legacy-authentication/02.png)

### <a name="select-users-and-cloud-apps"></a>Selezionare utenti e app cloud

Di solito si pensa che, per bloccare l'autenticazione legacy per la propria organizzazione, sia necessario selezionare:

- Tutti gli utenti
- Tutte le app cloud
- Blocca accesso

![Assegnazioni](./media/block-legacy-authentication/03.png)

Azure dispone di una funzionalità di sicurezza che impedisce la creazione di un criterio come questo perché questa configurazione viola le [procedure consigliate](best-practices.md) per i criteri di accesso condizionale.
 
![Configurazione di criteri non supportata](./media/block-legacy-authentication/04.png)

La funzionalità di protezione è necessaria perché *Blocca tutti gli utenti e tutte le app cloud* ha la possibilità di impedire l’accesso ai tenant da parte dell'intera organizzazione. È necessario escludere almeno un utente per soddisfare il requisito minimo della procedura consigliata. È anche possibile escludere un ruolo della directory.

![Configurazione di criteri non supportata](./media/block-legacy-authentication/05.png)

È possibile soddisfare questa funzionalità di protezione escludendo un utente dal criterio. In teoria, è necessario definire alcuni [account amministrativi di accesso di emergenza in Azure AD](../users-groups-roles/directory-emergency-access.md) ed escluderli dal criterio.

## <a name="policy-deployment"></a>Distribuzione dei criteri

Prima di inserire i criteri nell'ambiente di produzione, occuparsi di:
 
- **Account del servizio**: identificare gli account utente utilizzati come account del servizio o da dispositivi, ad esempio i telefoni della sala conferenze. Assicurarsi che per questi account siano impostate password complesse e aggiungerli a un gruppo di esclusione.
- **Report sugli accessi**: esaminare il report sugli accessi e cercare il traffico di **altri client**. Identificare i principali utilizzi e determinare perché si verificano. In genere, il traffico viene generato da client Office meno recenti che non usano l'autenticazione moderna o da alcune app di posta elettronica di terze parti. Creare un piano per abbandonare queste app oppure, se l'impatto è ridotto, avvisare gli utenti che non è più possibile usare queste app.
 
Per altre informazioni, vedere [Come distribuire un nuovo criterio](best-practices.md#how-should-you-deploy-a-new-policy).

## <a name="what-you-should-know"></a>Informazioni utili

Il blocco dell'accesso con **altri client** blocca anche PowerShell per Exchange Online usando l'autenticazione di base.

La configurazione di un criterio per **Altri client** blocca l'intera organizzazione per determinati client come SPConnect. Questo blocco si verifica perché l'autenticazione di client meno recenti avviene in modo imprevisto. Questo problema non si applica alle principali applicazioni di Office, come i client di Office meno recenti.

Perché il criterio abbia effetto, possono essere necessarie fino a 24 ore.

È possibile selezionare tutti i controlli di concessione disponibili per la condizione di **altri client** . Tuttavia, l'esperienza dell'utente finale è sempre lo stesso accesso bloccato.

Se si blocca l'autenticazione legacy usando la condizione di **altri client** , è anche possibile impostare la condizione della piattaforma e del percorso del dispositivo. Ad esempio, se si vuole bloccare solo l'autenticazione legacy per i dispositivi mobili, impostare la condizione **Piattaforma del dispositivo** selezionando:

- Android
- iOS
- Windows Phone

![Configurazione di criteri non supportata](./media/block-legacy-authentication/06.png)

## <a name="next-steps"></a>Passaggi successivi

- Se non si ha ancora familiarità con la configurazione dei criteri di accesso condizionale, vedere richiedere l'autenticazione a più fattori [per app specifiche con Azure Active Directory l'accesso condizionale](app-based-mfa.md) per un esempio.
- Per altre informazioni sul supporto dell'autenticazione moderna, vedere funzionamento dell' [autenticazione moderna per le app client office 2013 e office 2016](https://docs.microsoft.com/office365/enterprise/modern-auth-for-office-2013-and-2016) 
