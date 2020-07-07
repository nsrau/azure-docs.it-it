---
title: Cos'è la pagina Verifica aggiuntiva? - Azure AD
description: Come accedere alla pagina Verifica aggiuntiva di sicurezza per la verifica a due fattori
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: end-user-help
ms.date: 05/28/2020
ms.author: curtand
ms.openlocfilehash: fc95e988b3f89402967cdbedd06c4b945a99f99a
ms.sourcegitcommit: 309cf6876d906425a0d6f72deceb9ecd231d387c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/01/2020
ms.locfileid: "84266254"
---
# <a name="what-is-the-additional-verification-page"></a>Cos'è la pagina Verifica aggiuntiva?

L'organizzazione adotta altri provvedimenti per accertare l'identità dell'utente al momento dell'accesso. Questa verifica di sicurezza aggiuntiva è nota anche come verifica a due fattore. È costituita da una combinazione di nome utente, password e dispositivo mobile o telefono. Se si vuole solo disattivare la verifica a due fattori per un account Microsoft, ad esempio alain@outlook.com, usare le istruzioni riportate in [Attivazione o disattivazione della verifica a due fattori per l'account Microsoft](https://support.microsoft.com/help/4028586/microsoft-account-turning-two-step-verification-on-or-off).

<center>

![Immagine concettuale dei metodi di autenticazione](../authentication/media/concept-mfa-howitworks/methods.png)</center>

La verifica a due fattori è un metodo più sicuro rispetto alla sola password perché si basa su due forme di autenticazione:

- Qualcosa che si sa, come la password.
- Qualcosa che si ha, come un telefono o un altro dispositivo mobile.

La verifica a due fattori consente di impedire a utenti malintenzionati di fingere di essere un utente autorizzato. Anche se conoscono la password, è improbabile che abbiano anche il dispositivo.

>[!Important]
>Se si è un amministratore e si cercano informazioni su come attivare la verifica a due fattori per i dipendenti o altri utenti, vedere la [documentazione sull'autenticazione di Azure Active Directory](https://docs.microsoft.com/azure/active-directory/authentication/). Questo articolo è destinato agli utenti che provano a usare la verifica a due fattori con un account aziendale o dell'istituto di istruzione, ad esempio alain@contoso.com.

## <a name="who-decides-if-you-use-this-feature"></a>Chi decide se usare questa funzionalità?

Dipende dal tipo di account che si usa:

- **Account aziendale o dell'istituto di istruzione.** Se si usa un account aziendale o dell'istituto di istruzione, ad esempio alain@contoso.com, spetta all'organizzazione scegliere se usare o meno la verifica a due fattori, insieme ai metodi di verifica specifici. Se l'organizzazione decide che è necessario usare questa funzionalità, non è possibile disattivarla individualmente.

- **Account Microsoft personale.** Se si ha un account Microsoft personale, ad esempio alain@outlook.com, si può decidere autonomamente se configurare la verifica a due fattori. È possibile attivarla o disattivarla ogni volta che si vuole, seguendo le semplici istruzioni riportate in [Attivazione e disattivazione della verifica a due fattori per l'account Microsoft](https://support.microsoft.com/help/4028586/microsoft-account-turning-two-step-verification-on-or-off).

    >[!Note]
    >Se si riscontrano altri problemi con la verifica a due fattori e con uno degli account Microsoft personali, è possibile provare a seguire i suggerimenti riportati in [Come usare la verifica in due passaggi con l'account Microsoft](https://support.microsoft.com/help/12408/microsoft-account-how-to-use-two-step-verification).

## <a name="open-the-additional-security-verification-page"></a>Aprire la pagina Verifica aggiuntiva di sicurezza

Dopo che l'organizzazione ha attivato e configurato la verifica a due fattori, ogni volta che si accede si riceve la richiesta di fornire altre informazioni per preservare la sicurezza dell'account.

![Prompt per la richiesta di altre informazioni](media/multi-factor-authentication-verification-methods/multi-factor-authentication-initial-prompt.png)

### <a name="to-access-the-additional-security-verification-page"></a>Per accedere alla pagina Verifica aggiuntiva di sicurezza

1. Selezionare **Avanti** nel prompt **Sono necessarie altre informazioni**.

    Viene visualizzata la pagina **Verifica aggiuntiva di protezione**.

2. Nella pagina **Verifica aggiuntiva di sicurezza** selezionare il metodo di verifica a due fattori per confermare di essere chi si dice di essere quando si accede all'account aziendale o dell'istituto di istruzione. È possibile selezionare:

    | Metodo di contatto | Descrizione |
    | --- | --- |
    | App per dispositivi mobili | <ul><li>**Ricevi notifiche per la verifica.** Questa opzione invia una notifica all'app di autenticazione sul tablet o lo smartphone. Visualizzare la notifica e, se del caso, selezionare **Autentica** nell'app. L'azienda o dell'istituto di istruzione potrebbe richiedere di immettere un PIN prima eseguire l'autenticazione.</li><li>**Usa codice di verifica.** In questa modalità l'app genera un codice di verifica che si aggiorna ogni 30 secondi. Immettere il codice di verifica più recente nella schermata di accesso.<br>L'app Microsoft Authenticator è disponibile per [Android](https://go.microsoft.com/fwlink/?linkid=866594) e [iOS](https://go.microsoft.com/fwlink/?linkid=866594).</li></ul> |
    | Telefono per l'autenticazione | <ul><li>**Telefonata** invia una chiamata vocale automatizzata al numero di telefono specificato. Per l'autenticazione, rispondere alla chiamata e premere il tasto cancelletto (#) sul tastierino telefonico.</li><li>**SMS** invia un SMS contenente un codice di verifica. Seguendo la richiesta indicata nel testo, rispondere all'SMS o immettere il codice di verifica fornito nell'interfaccia di accesso.</li></ul> |
    | Telefono ufficio | Invia una chiamata vocale automatizzata al numero di telefono fornito. Per l'autenticazione, rispondere alla chiamata e premere il tasto cancelletto (#) sul tastierino telefonico. |

## <a name="next-steps"></a>Passaggi successivi

Dopo aver selezionato il metodo di verifica a due fattori nella pagina **Verifica aggiuntiva di sicurezza**, è necessario configurarlo:

- [Configurare il dispositivo mobile come metodo di verifica](multi-factor-authentication-setup-phone-number.md)

- [Configurare il telefono dell'ufficio come metodo di verifica](multi-factor-authentication-setup-office-phone.md)

- [Configurare l'app Microsoft Authenticator come metodo di verifica](multi-factor-authentication-setup-auth-app.md)

## <a name="related-resources"></a>Risorse correlate

- [Accedere usando la verifica a due fattori](multi-factor-authentication-end-user-signin.md)

- [Risolvere i problemi relativi alla verifica a due fattori](multi-factor-authentication-end-user-troubleshoot.md)
