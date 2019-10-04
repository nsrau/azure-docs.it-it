---
title: Configurare la gestione delle sessioni di autenticazione con Azure Active Directory l'accesso condizionale
description: Personalizzare Azure AD configurazione della sessione di autenticazione, inclusa la frequenza di accesso utente e la persistenza della sessione del browser.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 04/26/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 05c93c9fe2b34ae3b87c44608cc5c5c8947ecc73
ms.sourcegitcommit: a0b37e18b8823025e64427c26fae9fb7a3fe355a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/25/2019
ms.locfileid: "68499844"
---
# <a name="configure-authentication-session-management-with-conditional-access"></a>Configurare la gestione delle sessioni di autenticazione con l'accesso condizionale

Nelle distribuzioni complesse, le organizzazioni potrebbero avere la necessità di limitare le sessioni di autenticazione. Alcuni scenari possono includere:

* Accesso alle risorse da un dispositivo non gestito o condiviso
* Accesso a informazioni riservate da una rete esterna
* Utenti con effetto elevato
* Applicazioni aziendali critiche

I controlli di accesso condizionale consentono di creare criteri destinati a casi d'uso specifici all'interno dell'organizzazione senza influire su tutti gli utenti.

Prima di approfondire i dettagli su come configurare il criterio, esaminiamo la configurazione predefinita.

## <a name="user-sign-in-frequency"></a>Frequenza di accesso utente

Frequenza di accesso consente di definire il periodo di tempo prima che un utente debba accedere di nuovo quando tenta di accedere a una risorsa.

La configurazione predefinita di Azure Active Directory (Azure AD) per la frequenza di accesso dell'utente è una finestra in sequenza di 90 giorni. La richiesta di credenziali da parte degli utenti è spesso un'operazione sensata, ma può essere riattiva: gli utenti che hanno eseguito il training per immettere le proprie credenziali senza pensare possono fornirli involontariamente a una richiesta di credenziali dannose.

Potrebbe sembrare allarmante non chiedere a un utente di eseguire di nuovo l'accesso per 90 giorni, in realtà qualsiasi violazione dei criteri IT revoca la sessione. Alcuni esempi includono (senza limitazioni) una modifica della password, un dispositivo incompliant o la disabilitazione dell'account. È anche possibile revocare in modo esplicito le [sessioni degli utenti usando PowerShell](https://docs.microsoft.com/powershell/module/azuread/revoke-azureaduserallrefreshtoken?view=azureadps-2.0). Il Azure AD configurazione predefinita è "non richiedere agli utenti di fornire le proprie credenziali se il comportamento di sicurezza delle sessioni non è cambiato".

L'impostazione della frequenza di accesso funziona con le app che hanno implementato i protocolli OAUTH2 o OIDC in base agli standard. La maggior parte delle app Microsoft native per Windows, Mac e mobile è conforme all'impostazione.

## <a name="persistence-of-browsing-sessions"></a>Persistenza delle sessioni di esplorazione

Una sessione del browser persistente consente agli utenti di rimanere connessi dopo la chiusura e la riapertura della finestra del browser.

Il Azure AD predefinito per la persistenza della sessione del browser consente agli utenti dei dispositivi personali di scegliere se mantenere la sessione in modo permanente visualizzando un "rimanere connesso?" messaggio di richiesta dopo l'autenticazione riuscita. Se la persistenza del browser è configurata in ad FS usando [le indicazioni fornite nell'articolo ad FS](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/ad-fs-single-sign-on-settings#enable-psso-for-office-365-users-to-access-sharepoint-online
)impostazioni di Single Sign-on, si conformeranno a tale criterio e si renderà persistente anche la sessione di Azure ad. È anche possibile specificare se gli utenti nel tenant visualizzano il "rimanere connessi?" richiedere modificando l'impostazione appropriata nel riquadro branding dell'azienda in portale di Azure seguendo le istruzioni riportate nell'articolo [personalizzare la pagina di accesso Azure ad](../fundamentals/customize-branding.md).

## <a name="configuring-authentication-session-controls"></a>Configurazione di controlli della sessione di autenticazione

L'accesso condizionale è una funzionalità di Azure AD Premium e richiede una licenza Premium. Per altre informazioni sull'accesso condizionale, vedere [che cos'è l'accesso condizionale in Azure Active Directory?](overview.md#license-requirements)

> [!WARNING]
> Se si usa la funzionalità di [durata dei token](../develop/active-directory-configurable-token-lifetimes.md) configurabile attualmente disponibile in anteprima pubblica, si noti che non è supportata la creazione di due criteri diversi per la stessa combinazione di utenti o app: uno con questa funzionalità e un altro con un token configurabile funzionalità Lifetime. Microsoft prevede di ritirare la funzionalità di durata dei token configurabile il 1 ° novembre e sostituirla con la funzionalità di gestione della sessione di autenticazione dell'accesso condizionale.  

### <a name="policy-1-sign-in-frequency-control"></a>Criterio 1: Controllo della frequenza di accesso

1. Crea nuovo criterio
1. Scegliere tutte le condizioni necessarie per l'ambiente del cliente, incluse le app cloud di destinazione.

   > [!NOTE]
   > È consigliabile impostare la frequenza di richiesta di autenticazione uguale per le app chiave Microsoft Office come Exchange Online e SharePoint Online per ottimizzare l'esperienza utente.

1. Passare alla**sessione** **controlli** > di accesso e fare clic su **frequenza di** accesso
1. Immettere il valore richiesto di giorni e ore nella prima casella di testo
1. Selezionare un valore di **ore** o **giorni** dall'elenco a discesa
1. Salvare i criteri

![Criteri di accesso condizionale configurati per la frequenza di accesso](media/howto-conditional-access-session-lifetime/conditional-access-policy-session-sign-in-frequency.png)

In Azure AD i dispositivi Windows registrati, l'accesso al dispositivo viene considerato una richiesta. Se ad esempio è stata configurata la frequenza di accesso a 24 ore per le app di Office, gli utenti in Azure AD dispositivi Windows registrati soddisferanno i criteri di frequenza di accesso effettuando l'accesso al dispositivo e non verrà più visualizzata alcuna richiesta all'apertura delle app di Office.

Se è stata configurata una frequenza di accesso diversa per diverse app Web in esecuzione nella stessa sessione del browser, i criteri più severi verranno applicati a entrambe le app perché tutte le app in esecuzione nella stessa sessione del browser condividono un solo token di sessione.

### <a name="policy-2-persistent-browser-session"></a>Criterio 2: Sessione del browser persistente

1. Crea nuovo criterio
1. Scegliere tutte le condizioni necessarie.

   > [!NOTE]
   > Si noti che questo controllo richiede di scegliere "tutte le app Cloud" come condizione. La persistenza della sessione del browser è controllata dal token della sessione di autenticazione. Tutte le schede in una sessione del browser condividono un solo token di sessione e pertanto devono condividere lo stato di persistenza.

1. Passare alla**sessione** **controlli** > di accesso e fare clic su **sessione del browser permanente**
1. Selezionare un valore dall'elenco a discesa
1. Salva i criteri

![Criteri di accesso condizionale configurati per il browser permanente](media/howto-conditional-access-session-lifetime/conditional-access-policy-session-persistent-browser.png)

> [!NOTE]
> La configurazione della sessione del browser persistente in Azure AD accesso condizionale sovrascriverà il "rimanere connesso?" impostazione nel riquadro Personalizzazione società nel portale di Azure per lo stesso utente se sono stati configurati entrambi i criteri.

## <a name="validation"></a>Convalida

Usare lo strumento simulazione per simulare un account di accesso dell'utente all'applicazione di destinazione e altre condizioni in base alla configurazione dei criteri. I controlli di gestione della sessione di autenticazione vengono visualizzati nel risultato dello strumento.

![Risultati dell'accesso condizionale What If strumento](media/howto-conditional-access-session-lifetime/conditional-access-what-if-tool-result.png)

## <a name="policy-deployment"></a>Distribuzione dei criteri

La procedura consigliata è testare i criteri prima di distribuirli nell'ambiente di produzione per assicurarsi che funzionino nel modo previsto. L'approccio ideale è usare un tenant di test per verificare se il nuovo criterio funziona nel modo previsto. Per ulteriori informazioni, vedere l'articolo [procedure consigliate per l'accesso condizionale in Azure Active Directory](best-practices.md).

## <a name="next-steps"></a>Passaggi successivi

* Per informazioni su come configurare i criteri di accesso condizionale, vedere l'articolo richiedere l'autenticazione a più fattori [per app specifiche con Azure Active Directory l'accesso condizionale](app-based-mfa.md).
* Se si è pronti per configurare i criteri di accesso condizionale per l'ambiente in uso, vedere l'articolo [procedure consigliate per l'accesso condizionale in Azure Active Directory](best-practices.md).
