---
title: Come bloccare l'autenticazione legacy per Azure Active Directory (Azure AD) con l'accesso condizionale | Microsoft Docs
description: Informazioni su come aumentare la sicurezza mediante il blocco tramite l'accesso condizionale di Azure AD con l'autenticazione legacy.
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
ms.openlocfilehash: 9b2120466652db363206ec20c2303ad56670228c
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/17/2019
ms.locfileid: "67164792"
---
# <a name="how-to-block-legacy-authentication-to-azure-ad-with-conditional-access"></a>Procedura: Autenticazione legacy di blocco con Azure AD con accesso condizionale   

Per consentire agli utenti di accedere facilmente alle app cloud, Azure Active Directory (Azure AD) supporta una vasta gamma di protocolli di autenticazione, inclusa l'autenticazione legacy. Tuttavia, i protocolli legacy non supportano l'autenticazione a più fattori (MFA). La MFA è in molti ambienti un requisito comune contro il furto di identità di indirizzo. 

Se l'ambiente è pronto per l'autenticazione legacy di blocco per migliorare la protezione del tenant, è possibile raggiungere questo obiettivo con accesso condizionale. Questo articolo illustra come configurare i criteri di accesso condizionale che bloccano l'autenticazione legacy per il tenant.

## <a name="prerequisites"></a>Prerequisiti

Questo articolo presuppone che l'utente abbia familiarità con: 

- Il [concetti di base](overview.md) di accesso condizionale di Azure AD 
- Il [procedure consigliate](best-practices.md) per la configurazione dei criteri di accesso condizionale nel portale di Azure

## <a name="scenario-description"></a>Descrizione dello scenario

Azure AD supporta diversi dei protocolli di autenticazione e autorizzazione più ampiamente usati. L'autenticazione legacy fa riferimento ai protocolli che usano l'autenticazione di base. In genere, questi protocolli non possono applicare nessun tipo di secondo fattore di autenticazione. Alcuni esempi di app che si basano sull'autenticazione legacy:

- App Microsoft Office meno recenti

- App che usano protocolli di posta elettronica quali POP, IMAP e SMTP

Al giorno d’oggi, l'autenticazione a fattore singolo (ad esempio, nome utente e password) non è più sufficiente. Le password non sono sicure perché sono facili da indovinare e le persone non sanno scegliere password efficaci. Le password sono anche vulnerabili a numerosi attacchi, ad esempio il phishing e lo spray password. Una delle operazioni più semplici da effettuare per proteggersi dalle minacce relative alle password è implementare la MFA. Con la MFA, anche se un utente malintenzionato entra in possesso di una password utente, la sola password non è sufficiente per l’autenticazione e l’accesso ai dati.

Come si può impedire alle app che usano l'autenticazione legacy di accedere alle risorse dei tenant? Si consiglia di appena bloccarli mediante un criterio di accesso condizionale. Se necessario, è possibile autorizzare solo determinati utenti e percorsi di rete specifici per l’uso delle app basate sull’autenticazione legacy.

Criteri di accesso condizionale vengono applicati dopo il primo-factor authentication è stato completato. Pertanto, l'accesso condizionale non è stato progettato come una prima linea di difesa per gli scenari, ad esempio attacchi di tipo denial of service (DoS), ma possono essere utilizzate segnali provenienti da questi eventi (ad esempio, il livello di rischio di accesso, il percorso della richiesta e così via) per determinare l'accesso.

## <a name="implementation"></a>Implementazione

Questa sezione viene illustrato come configurare un criterio di accesso condizionale per l'autenticazione legacy di blocco. 

### <a name="identify-legacy-authentication-use"></a>Identificare l'utilizzo di autenticazione legacy

Prima è possibile bloccare l'autenticazione legacy nella directory, è necessario innanzi tutto capire se gli utenti hanno le app che usano l'autenticazione legacy e relativo impatto sulla directory globale. Azure AD i log di accesso utilizzabile per capire se si usa l'autenticazione legacy.

1. Passare il **portale di Azure** > **Azure Active Directory** > **accessi**.
1. Aggiungere la colonna di App Client se non viene visualizzato facendo clic su **colonne** > **App Client**.
1. Filtrare in base al **App Client** > **altri client** e fare clic su **applica**.

Applicazione di filtri di solo i tentativi di show avere effettuato l'accesso sono state apportate dai protocolli di autenticazione legacy. Facendo clic su ogni tentativo di accesso singoli visualizzerà i dettagli aggiuntivi. Il **App Client** campo sotto il **le informazioni di base** scheda indicherà quale protocollo di autenticazione legacy è stato utilizzato.

Questi log indica che gli utenti dipendono ancora da autenticazione legacy e le applicazioni che usano protocolli legacy per effettuare richieste di autenticazione. Per gli utenti che non vengono visualizzati in questi log e vengano confermati che non venga usata l'autenticazione legacy, implementare criteri di accesso condizionale per solo tali utenti.

### <a name="block-legacy-authentication"></a>Bloccare l'autenticazione legacy 

In un criterio di accesso condizionale, è possibile impostare una condizione che è associata all'App client che consentono di accedere alle risorse. La condizione per le app client consente di restringere l'ambito alle app che usano l'autenticazione legacy selezionando **Altri client** per **App per dispositivi mobili e client desktop**.

![Altri client](./media/block-legacy-authentication/01.png)

Per bloccare l'accesso per queste App, è necessario selezionare **Blocca accesso**.

![Blocca accesso](./media/block-legacy-authentication/02.png)


### <a name="select-users-and-cloud-apps"></a>Selezionare utenti e app cloud

Di solito si pensa che, per bloccare l'autenticazione legacy per la propria organizzazione, sia necessario selezionare:

- Tutti gli utenti

- Tutte le app cloud

- Blocca accesso
 

![Assegnazioni](./media/block-legacy-authentication/03.png)



Azure offre una funzionalità di sicurezza che impedisce la creazione di un criterio simile al seguente, perché questa configurazione viola la [procedure consigliate](best-practices.md) per i criteri di accesso condizionale.
 
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

Blocca l'accesso usando **altri client** blocca anche PowerShell per Exchange Online usando l'autenticazione di base.

La configurazione di un criterio per **Altri client** blocca l'intera organizzazione per determinati client come SPConnect. Questo blocco si verifica perché l'autenticazione di client meno recenti avviene in modo imprevisto. Questo problema non si applica alle principali applicazioni di Office, come i client di Office meno recenti.

Perché il criterio abbia effetto, possono essere necessarie fino a 24 ore.

È possibile selezionare tutti i controlli di concessione disponibili per l'altra condizione dei client; tuttavia, l'esperienza dell’utente finale è sempre la stessa, l'accesso è bloccato.

Se si blocca l'autenticazione legacy usando l'altra condizione dei client, è anche possibile impostare la condizione della piattaforma e del percorso del dispositivo. Ad esempio, se si vuole bloccare solo l'autenticazione legacy per i dispositivi mobili, impostare la condizione **Piattaforma del dispositivo** selezionando:

- Android

- iOS

- Windows Phone

![Configurazione di criteri non supportata](./media/block-legacy-authentication/06.png)




## <a name="next-steps"></a>Passaggi successivi

- Se non ha familiarità con la configurazione dei criteri di accesso condizionale ancora, vedere [richiedere l'autenticazione MFA per App specifiche con Azure Active Directory l'accesso condizionale](app-based-mfa.md) per un esempio.

- Per altre informazioni sul supporto dell'autenticazione moderna, vedere [funzionamento dell'autenticazione moderna per le app client di Office 2013 e Office 2016](https://docs.microsoft.com/office365/enterprise/modern-auth-for-office-2013-and-2016) 
