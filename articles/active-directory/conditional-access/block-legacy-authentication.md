---
title: Block legacy authentication - Azure Active Directory
description: Learn how to improve your security posture by blocking legacy authentication using Azure AD Conditional Access.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2a65145fe9752a90e3328c308ce603c8626d8708
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74380869"
---
# <a name="how-to-block-legacy-authentication-to-azure-ad-with-conditional-access"></a>How to: Block legacy authentication to Azure AD with Conditional Access   

Per consentire agli utenti di accedere facilmente alle app cloud, Azure Active Directory (Azure AD) supporta una vasta gamma di protocolli di autenticazione, inclusa l'autenticazione legacy. Tuttavia, i protocolli legacy non supportano l'autenticazione a più fattori (MFA). La MFA è in molti ambienti un requisito comune contro il furto di identità di indirizzo. 

If your environment is ready to block legacy authentication to improve your tenant's protection, you can accomplish this goal with Conditional Access. This article explains how you can configure Conditional Access policies that block legacy authentication for your tenant.

## <a name="prerequisites"></a>Prerequisiti

Questo articolo presuppone che l'utente abbia familiarità con: 

- The [basic concepts](overview.md) of Azure AD Conditional Access 
- The [best practices](best-practices.md) for configuring Conditional Access policies in the Azure portal

## <a name="scenario-description"></a>Descrizione dello scenario

Azure AD supporta diversi dei protocolli di autenticazione e autorizzazione più ampiamente usati. L'autenticazione legacy fa riferimento ai protocolli che usano l'autenticazione di base. In genere, questi protocolli non possono applicare nessun tipo di secondo fattore di autenticazione. Alcuni esempi di app che si basano sull'autenticazione legacy:

- App Microsoft Office meno recenti
- App che usano protocolli di posta elettronica quali POP, IMAP e SMTP

Al giorno d’oggi, l'autenticazione a fattore singolo (ad esempio, nome utente e password) non è più sufficiente. Le password non sono sicure perché sono facili da indovinare e le persone non sanno scegliere password efficaci. Le password sono anche vulnerabili a numerosi attacchi, ad esempio il phishing e lo spray password. Una delle operazioni più semplici da effettuare per proteggersi dalle minacce relative alle password è implementare la MFA. Con la MFA, anche se un utente malintenzionato entra in possesso di una password utente, la sola password non è sufficiente per l’autenticazione e l’accesso ai dati.

Come si può impedire alle app che usano l'autenticazione legacy di accedere alle risorse dei tenant? The recommendation is to just block them with a Conditional Access policy. Se necessario, è possibile autorizzare solo determinati utenti e percorsi di rete specifici per l’uso delle app basate sull’autenticazione legacy.

I criteri di accesso condizionale vengono applicati dopo il completamento dell'autenticazione con primo fattore. Pertanto l'accesso condizionale non è da intendersi come una prima misura difensiva da attacchi Denial of Service (DoS), ma può utilizzare segnali da questi eventi, come il livello di rischio di accesso, la posizione della richiesta e così via, per determinare l'accesso.

## <a name="implementation"></a>Implementazione

This section explains how to configure a Conditional Access policy to block legacy authentication. 

### <a name="identify-legacy-authentication-use"></a>Identify legacy authentication use

Before you can block legacy authentication in your directory, you need to first understand if your users have apps that use legacy authentication and how it affects your overall directory. Azure AD sign-in logs can be used to understand if you’re using legacy authentication.

1. Navigate to the **Azure portal** > **Azure Active Directory** > **Sign-ins**.
1. Add the Client App column if it is not shown by clicking on **Columns** > **Client App**.
1. **Add filters** > **Client App** > select all of the options for **Other clients** and click **Apply**.

Filtering will only show you sign-in attempts that were made by legacy authentication protocols. Clicking on each individual sign-in attempt will show you additional details. The **Client App** field under the **Basic Info** tab will indicate which legacy authentication protocol was used.

These logs will indicate which users are still depending on legacy authentication and which applications are using legacy protocols to make authentication requests. For users that do not appear in these logs and are confirmed to not be using legacy authentication, implement a Conditional Access policy for these users only.

### <a name="block-legacy-authentication"></a>Bloccare l'autenticazione legacy 

In a Conditional Access policy, you can set a condition that is tied to the client apps that are used to access your resources. La condizione per le app client consente di restringere l'ambito alle app che usano l'autenticazione legacy selezionando **Altri client** per **App per dispositivi mobili e client desktop**.

![Altri client](./media/block-legacy-authentication/01.png)

Per bloccare l'accesso per queste App, è necessario selezionare **Blocca accesso**.

![Blocca accesso](./media/block-legacy-authentication/02.png)

### <a name="select-users-and-cloud-apps"></a>Selezionare utenti e app cloud

Di solito si pensa che, per bloccare l'autenticazione legacy per la propria organizzazione, sia necessario selezionare:

- Tutti gli utenti
- Tutte le app cloud
- Blocca accesso

![Assegnazioni](./media/block-legacy-authentication/03.png)

Azure has a safety feature that prevents you from creating a policy like this because this configuration violates the  [best practices](best-practices.md) for Conditional Access policies.
 
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

Blocking access using **Other clients** also blocks Exchange Online PowerShell and Dynamics 365 using basic auth.

La configurazione di un criterio per **Altri client** blocca l'intera organizzazione per determinati client come SPConnect. Questo blocco si verifica perché l'autenticazione di client meno recenti avviene in modo imprevisto. Questo problema non si applica alle principali applicazioni di Office, come i client di Office meno recenti.

Perché il criterio abbia effetto, possono essere necessarie fino a 24 ore.

You can select all available grant controls for the **Other clients** condition; however, the end-user experience is always the same - blocked access.

If you block legacy authentication using the **Other clients** condition, you can also set the device platform and location condition. Ad esempio, se si vuole bloccare solo l'autenticazione legacy per i dispositivi mobili, impostare la condizione **Piattaforma del dispositivo** selezionando:

- Android
- iOS
- Windows Phone

![Configurazione di criteri non supportata](./media/block-legacy-authentication/06.png)

## <a name="next-steps"></a>Passaggi successivi

- If you are not familiar with configuring Conditional Access policies yet, see [require MFA for specific apps with Azure Active Directory Conditional Access](app-based-mfa.md) for an example.
- For more information about modern authentication support, see [How modern authentication works for Office 2013 and Office 2016 client apps](https://docs.microsoft.com/office365/enterprise/modern-auth-for-office-2013-and-2016) 
