---
title: Cos'è la pagina Verifica aggiuntiva? - Azure AD
description: Come ottenere la pagina Verifica di sicurezza aggiuntiva per la verifica a due fattori.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: end-user-help
ms.date: 01/15/2020
ms.author: curtand
ms.openlocfilehash: 47220bc721092005c7e4d65a00eb933cd5c49a8c
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83746757"
---
# <a name="what-is-the-additional-verification-page"></a>Cos'è la pagina Verifica aggiuntiva?

Si riceve una e-mail da un referente IT o dal proprio capo in cui si afferma che l'organizzazione ha aggiunto altre verifiche di sicurezza all'account. Cosa significa? Significa che l'organizzazione sta prendendo altri provvedimenti per accertare l'identità dell'utente al momento dell'accesso. Questa verifica aggiuntiva, nota anche come verifica a due fattori, viene eseguita con una combinazione di nome utente, password e dispositivo mobile o telefono.

Si tratta di un metodo più sicuro rispetto alla sola password perché si basa su due forme di autenticazione, ovvero un elemento noto e un elemento che l'utente ha con sé. L'elemento noto è la password. L'elemento che l'utente normalmente ha con sé è un telefono o un dispositivo. Con la verifica a due fattori si impedisce ai pirati informatici di fingere di essere un utente autorizzato, perché anche se possiedono la password dell'utente, è improbabile che abbiano anche il suo dispositivo.

>[!Important]
>Questo articolo è destinato agli utenti che provano a usare la verifica a due fattori con un account aziendale o dell'istituto di istruzione, ad esempio alain@contoso.com. Se si è un amministratore e si cercano informazioni su come attivare la verifica a due fattori per i dipendenti o altri utenti, vedere la [documentazione sull'autenticazione di Azure Active Directory](https://docs.microsoft.com/azure/active-directory/authentication/).

## <a name="who-decides-if-you-use-this-feature"></a>Chi decide se usare questa funzionalità?

A seconda del tipo di account, l'organizzazione potrebbe decidere che è necessario usare la verifica a due fattori oppure si potrebbe deciderlo autonomamente.

- **Account aziendale o dell'istituto di istruzione.** Se si usa un account aziendale o dell'istituto di istruzione, ad esempio alain@contoso.com, spetta all'organizzazione scegliere se usare la verifica a due fattori, insieme ai metodi di verifica specifici. Poiché l'organizzazione ha deciso che è necessario usare questa funzionalità, non è possibile disattivarla individualmente.

- **Account Microsoft personale.** È possibile scegliere di impostare la verifica a due fattori per gli account Microsoft personali, ad esempio alain@outlook.com. Se si riscontrano problemi con la verifica a due fattori e l'account Microsoft personale, vedere [Attivazione o disattivazione della verifica a due fattori per l'account Microsoft](https://support.microsoft.com/help/4028586/microsoft-account-turning-two-step-verification-on-or-off). Poiché si ha la libertà di scegliere se usare questa funzionalità, è possibile attivarla o disattivarla ogni volta che si desidera.

    >[!Note]
    >Se si riscontrano problemi con la verifica a due fattori e con uno degli account Microsoft personali (ad esempio, danielle@outlook.com), è possibile provare i suggerimenti riportati in [Come usare la verifica in due passaggi con l'account Microsoft](https://support.microsoft.com/help/12408/microsoft-account-how-to-use-two-step-verification).

## <a name="access-the-additional-security-verification-page"></a>Accedere alla pagina Verifica aggiuntiva di sicurezza

Dopo che l'organizzazione ha attivato e configurato la verifica a due fattori, all'utente viene chiesto di specificare altre informazioni per garantire la protezione dell'account.

![Prompt per la richiesta di altre informazioni](media/multi-factor-authentication-verification-methods/multi-factor-authentication-initial-prompt.png)

### <a name="to-access-the-additional-security-verification-page"></a>Per accedere alla pagina Verifica aggiuntiva di sicurezza

1. Selezionare **Avanti** nel prompt **Sono necessarie altre informazioni**.

    Viene visualizzata la pagina **Verifica aggiuntiva di protezione**.

2. Nella pagina **Verifica aggiuntiva di protezione** è necessario definire il metodo di verifica a due fattori da usare per verificare la propria identità dopo aver eseguito l'accesso all'account aziendale o dell'istituto di istruzione. È possibile selezionare:

    | Metodo di contatto | Descrizione |
    | --- | --- |
    | App per dispositivi mobili | <ul><li>**Ricevi notifiche per la verifica.** Questa opzione invia una notifica all'app di autenticazione sul tablet o lo smartphone. Visualizzare la notifica e, se del caso, selezionare **Autentica** nell'app. L'azienda o dell'istituto di istruzione potrebbe richiedere di immettere un PIN prima eseguire l'autenticazione.</li><li>**Usa codice di verifica.** In questa modalità, l'app di autenticazione genera un nuovo codice di verifica ogni 30 secondi. Immettere il codice di verifica più recente nella schermata di accesso.<br>L'app Microsoft Authenticator è disponibile per [Android](https://go.microsoft.com/fwlink/?linkid=866594) e [iOS](https://go.microsoft.com/fwlink/?linkid=866594).</li></ul> |
    | Telefono per l'autenticazione | <ul><li>**Telefonata** invia una chiamata vocale automatizzata al numero di telefono specificato. Per l'autenticazione, rispondere alla chiamata e premere il tasto cancelletto (#) sul tastierino telefonico.</li><li>**SMS** invia un SMS contenente un codice di verifica. Seguendo la richiesta indicata nel testo, rispondere all'SMS o immettere il codice di verifica fornito nell'interfaccia di accesso.</li></ul> |
    | Telefono ufficio | Invia una chiamata vocale automatizzata al numero di telefono fornito. Per l'autenticazione, rispondere alla chiamata e premere il tasto cancelletto (#) sul tastierino telefonico. |

## <a name="next-steps"></a>Passaggi successivi

Dopo aver eseguito l'accesso alla pagina **Verifica di sicurezza aggiuntiva**, è necessario selezionare e configurare il metodo di verifica a due fattori:

- [Configurare il dispositivo mobile come metodo di verifica](multi-factor-authentication-setup-phone-number.md)

- [Configurare il telefono dell'ufficio come metodo di verifica](multi-factor-authentication-setup-office-phone.md)

- [Configurare l'app Microsoft Authenticator come metodo di verifica](multi-factor-authentication-setup-auth-app.md)

## <a name="related-resources"></a>Risorse correlate

- [Gestire le impostazioni del metodo di verifica a due fattori](multi-factor-authentication-end-user-manage-settings.md)

- [Gestire le password delle app](multi-factor-authentication-end-user-app-passwords.md)

- [Accedere usando la verifica a due fattori](multi-factor-authentication-end-user-signin.md)

- [Risolvere i problemi relativi alla verifica a due fattori](multi-factor-authentication-end-user-troubleshoot.md) 
