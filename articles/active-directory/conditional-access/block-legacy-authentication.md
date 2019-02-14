---
title: Come bloccare l’autenticazione legacy per Azure Active Directory (Azure AD) con l’accesso condizionale | Microsoft Docs
description: Informazioni su come migliorare il comportamento di sicurezza, bloccando l'autenticazione legacy tramite l'accesso condizionale di Azure AD.
services: active-directory
keywords: accesso condizionale alle app, accesso condizionale con Azure AD, accesso sicuro alle risorse aziendali, criteri di accesso condizionale
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.subservice: conditional-access
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/01/2019
ms.author: markvi
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 644a2279f555af05d711e1455923795b5271e159
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/13/2019
ms.locfileid: "56191623"
---
# <a name="how-to-block-legacy-authentication-to-azure-ad-with-conditional-access"></a>Procedura: Bloccare l’autenticazione legacy per Azure AD con accesso condizionale   

Per consentire agli utenti di accedere facilmente alle app cloud, Azure Active Directory (Azure AD) supporta una vasta gamma di protocolli di autenticazione, inclusa l'autenticazione legacy. Tuttavia, i protocolli legacy non supportano l'autenticazione a più fattori (MFA). La MFA è in molti ambienti un requisito comune contro il furto di identità di indirizzo. 


Se l'ambiente è pronto per bloccare l'autenticazione legacy per migliorare la protezione dei tenant, è possibile farlo con l’accesso condizionale. Questo articolo spiega come configurare i criteri di accesso condizionale che bloccano l'autenticazione legacy per i tenant.



## <a name="prerequisites"></a>Prerequisiti

Questo articolo presuppone che l'utente abbia familiarità con: 

- I [concetti di base](overview.md) dell'accesso condizionale di Azure AD 
- [Procedure consigliate](best-practices.md) per la configurazione dei criteri di accesso condizionale nel portale di Azure



## <a name="scenario-description"></a>Descrizione dello scenario

Azure AD supporta diversi dei protocolli di autenticazione e autorizzazione più ampiamente usati. L'autenticazione legacy fa riferimento ai protocolli che usano l'autenticazione di base. In genere, questi protocolli non possono applicare nessun tipo di secondo fattore di autenticazione. Alcuni esempi di app che si basano sull'autenticazione legacy:

- App Microsoft Office meno recenti

- App che usano protocolli di posta elettronica quali POP, IMAP e SMTP

Al giorno d’oggi, l'autenticazione a fattore singolo (ad esempio, nome utente e password) non è più sufficiente. Le password non sono sicure perché sono facili da indovinare e le persone non sanno scegliere password efficaci. Le password sono anche vulnerabili a numerosi attacchi, ad esempio il phishing e lo spray password. Una delle operazioni più semplici da effettuare per proteggersi dalle minacce relative alle password è implementare la MFA. Con la MFA, anche se un utente malintenzionato entra in possesso di una password utente, la sola password non è sufficiente per l’autenticazione e l’accesso ai dati.

Come si può impedire alle app che usano l'autenticazione legacy di accedere alle risorse dei tenant? Si consiglia di bloccarle con un criterio di accesso condizionale. Se necessario, è possibile autorizzare solo determinati utenti e percorsi di rete specifici per l’uso delle app basate sull’autenticazione legacy.




## <a name="implementation"></a>Implementazione

In questa sezione si spiega come configurare un criterio di accesso condizionale per bloccare l'autenticazione legacy. 

### <a name="block-legacy-authentication"></a>Bloccare l'autenticazione legacy 

In un criterio di accesso condizionale, è possibile impostare una condizione associata alle app client usate per accedere alle risorse. La condizione per le app client consente di restringere l'ambito alle app che usano l'autenticazione legacy selezionando **Altri client** per **App per dispositivi mobili e client desktop**.

![Altri client](./media/block-legacy-authentication/01.png)

Per bloccare l'accesso per queste App, è necessario selezionare **Blocca accesso**.

![Blocca accesso](./media/block-legacy-authentication/02.png)


### <a name="select-users-and-cloud-apps"></a>Selezionare utenti e app cloud

Di solito si pensa che, per bloccare l'autenticazione legacy per la propria organizzazione, sia necessario selezionare:

- Tutti gli utenti

- Tutte le app cloud

- Blocca accesso
 

![Assegnazioni](./media/block-legacy-authentication/03.png)



Azure offre una funzionalità di protezione che impedisce la creazione di un criterio simile, perché questa configurazione viola la [procedure consigliate](best-practices.md) per i criteri di accesso condizionale.
 
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

La configurazione di un criterio per **Altri client** blocca l'intera organizzazione per determinati client come SPConnect. Questo blocco si verifica perché l'autenticazione di client meno recenti avviene in modo imprevisto. Questo problema non si applica alle principali applicazioni di Office, come i client di Office meno recenti.

Perché il criterio abbia effetto, possono essere necessarie fino a 24 ore.

È possibile selezionare tutti i controlli di concessione disponibili per l'altra condizione dei client; tuttavia, l'esperienza dell’utente finale è sempre la stessa, l'accesso è bloccato.

Se si blocca l'autenticazione legacy usando l'altra condizione dei client, è anche possibile impostare la condizione della piattaforma e del percorso del dispositivo. Ad esempio, se si vuole bloccare solo l'autenticazione legacy per i dispositivi mobili, impostare la condizione **Piattaforma del dispositivo** selezionando:

- Android

- iOS

- Windows Phone

![Configurazione di criteri non supportata](./media/block-legacy-authentication/06.png)




## <a name="next-steps"></a>Passaggi successivi

Per informazioni sulla configurazione di criteri di accesso condizionale, in [Richiedere MFA per app specifiche con Azure Active Directory](app-based-mfa.md) è disponibile un esempio.
