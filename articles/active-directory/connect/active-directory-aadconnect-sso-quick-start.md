---
title: 'Azure AD Connect: guida introduttiva all''accesso Single Sign-On facile | Documentazione Microsoft'
description: "In questo articolo vengono descritte le attività iniziali dell'accesso Single Sign-On facile di Azure Active Directory."
services: active-directory
keywords: "che cos'è Azure AD Connect, installare Active Directory, componenti richiesti per Azure AD, SSO, Single Sign-On"
documentationcenter: 
author: swkrish
manager: femila
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/04/2017
ms.author: billmath
ms.translationtype: HT
ms.sourcegitcommit: 1dbb1d5aae55a4c926b9d8632b416a740a375684
ms.openlocfilehash: 977108687734a5eb7f7a30419de2a6bdef184d0e
ms.contentlocale: it-it
ms.lasthandoff: 08/07/2017

---

# <a name="azure-active-directory-seamless-single-sign-on-quick-start"></a>Accesso Single Sign-On facile di Azure Active Directory: guida introduttiva

## <a name="how-to-deploy-seamless-sso"></a>Come distribuire l'accesso Single Sign-On facile

La funzionalità Accesso Single Sign-On facile (Accesso SSO facile) di Azure Active Directory consente agli utenti di eseguire l'accesso automaticamente dai desktop di proprietà dell'azienda connessi alla rete aziendale. Consente agli utenti di accedere facilmente alle applicazioni basate su cloud senza la necessità di componenti aggiuntivi in locale.

>[!IMPORTANT]
>La funzionalità Accesso Single Sign-On facile è attualmente in fase di anteprima.

Per distribuire l'accesso SSO facile, è necessario seguire questa procedura:

## <a name="step-1-check-prerequisites"></a>Passaggio 1: Verificare i prerequisiti

Accertarsi di aver soddisfatto i prerequisiti seguenti:

1. Configurare il server di Azure AD Connect: se si usa l'[autenticazione pass-through](active-directory-aadconnect-pass-through-authentication.md) come metodo di accesso, non è necessaria alcuna altra azione. Se come metodo di accesso si usa la [sincronizzazione dell'hash delle password](active-directory-aadconnectsync-implement-password-synchronization.md) e se vi è un firewall tra Azure AD Connect e Azure AD, assicurarsi che:
- Si usi la versione 1.1.484.0 o versioni successive di Azure AD Connect.
- Azure AD Connect possa comunicare con gli URL `*.msappproxy.net` e tramite la porta 443. Questo prerequisito è applicabile solo quando si abilita la funzionalità, non per gli accessi utente effettivi.
- Azure AD Connect possa eseguire connessioni IP dirette agli [intervalli IP dei data center di Azure](https://www.microsoft.com/download/details.aspx?id=41653). Anche questo prerequisito è applicabile solo quando si abilita la funzionalità.
2. Sono necessarie le credenziali di amministratore di dominio per ogni foresta di AD che si sincronizza con Azure AD, tramite Azure AD Connect, e per i cui utenti si vuole abilitare la funzionalità Accesso SSO facile.

## <a name="step-2-enable-the-feature"></a>Passaggio 2: Abilitare la funzionalità

La funzionalità Accesso SSO facile può essere abilitata tramite [Azure AD Connect](active-directory-aadconnect.md).

Se si esegue una nuova installazione di Azure AD Connect, scegliere il [percorso di installazione personalizzato](active-directory-aadconnect-get-started-custom.md). Nella pagina "Accesso utente" selezionare l'opzione "Abilita Single Sign-On".

![Azure AD Connect - Accesso utente](./media/active-directory-aadconnect-sso/sso8.png)

Se Azure AD Connect è già installato, scegliere la pagina "Cambia l'accesso utente" in Azure AD Connect e fare clic su "Avanti". Selezionare quindi l'opzione "Abilita Single Sign-On".

![Azure AD Connect - Cambiare l'accesso utente](./media/active-directory-aadconnect-user-signin/changeusersignin.png)

Continuare la procedura guidata fino a quando si arriva alla pagina "Abilita Single Sign-On". Immettere le credenziali di amministratore di dominio per ogni foresta di AD che si sincronizza con Azure AD, tramite Azure AD Connect, e per i cui utenti si vuole abilitare la funzionalità Accesso SSO facile. 

Al termine della procedura guidata, l'accesso SSO facile è abilitato nel tenant.

>[!NOTE]
> Le credenziali di amministratore di dominio non vengono archiviate in Azure AD Connect o in Azure AD, ma vengono usate solo per abilitare la funzionalità.

Seguire queste istruzioni per verificare di aver abilitato correttamente l'accesso Single Sign-On facile:

1. Accedere all'[interfaccia di amministrazione di Azure Active Directory](https://aad.portal.azure.com) con le credenziali di amministratore globale del tenant.
2. Selezionare **Azure Active Directory** nell'opzione di spostamento a sinistra.
3. Selezionare **Azure AD Connect**.
4. Verificare che la funzionalità **Accesso Single Sign-On facile** sia impostata su **Abilitato**.

![Portale di Azure - Pannello Azure AD Connect](./media/active-directory-aadconnect-sso/sso10.png)

## <a name="step-3-roll-out-the-feature"></a>Passaggio 3: Distribuire la funzionalità

Per distribuire la funzionalità agli utenti, è necessario aggiungere due URL di Azure AD, https://autologon.microsoftazuread-sso.com e https://aadg.windows.net.nsatc.net, alle impostazioni dell'area Intranet degli utenti tramite Criteri di gruppo in Active Directory.

>[!NOTE]
> Le istruzioni seguenti valgono solo per Internet Explorer e Google Chrome in Windows, se condivide l'insieme di URL di siti attendibili con Internet Explorer. Vedere la sezione successiva per istruzioni sulla configurazione di Mozilla Firefox e Google Chrome su Mac.

### <a name="why-do-you-need-to-modify-users-intranet-zone-settings"></a>Motivo per cui è necessario modificare le impostazioni della zona Intranet degli utenti

Per impostazione predefinita, il browser calcola automaticamente l'area giusta, Internet o Intranet, dall'URL. Ad esempio l'URL http://contoso/ viene mappato all'area Intranet, mentre l'URL http://intranet.contoso.com/ viene mappato all'area Internet perché l'URL contiene un punto. I browser non inviano i ticket Kerberos a un endpoint del cloud, come i due URL di Azure AD, a meno che l'URL relativo non venga aggiunto all'area Intranet del browser.

### <a name="detailed-steps"></a>Procedura dettagliata

1. Aprire lo strumento Gestione criteri di gruppo.
2. Modificare i Criteri di gruppo applicati ad alcuni utenti o a tutti. In questo esempio viene usato **Criterio dominio predefinito**.
3. Passare a **Configurazione utente\Modelli amministrativi\Componenti di Windows\Internet Explorer\Pannello di controllo Internet\Scheda Sicurezza** e selezionare **Elenco di assegnazione siti ad aree**.
![Single Sign-On](./media/active-directory-aadconnect-sso/sso6.png)  
4. Abilitare i criteri e immettere i valori seguenti (URL di Azure AD in cui vengono inoltrati i ticket Kerberos) e i dati (*1* indica l'area Intranet) nella finestra di dialogo.

        Value: https://autologon.microsoftazuread-sso.com
        Data: 1
        Value: https://aadg.windows.net.nsatc.net
        Data: 1
>[!NOTE]
> Se si vuole impedire ad alcuni utenti di usare l'accesso SSO facile, ad esempio se tali utenti accedono da chioschi multimediali condivisi, impostare i valori precedenti su *4*. Questa azione aggiunge gli URL di Azure AD all'Area con restrizioni e fa sì che l'accesso SSO facile abbia sempre esito negativo.

5. Fare clic su **OK**, quindi nuovamente su **OK**.

![Single sign-on](./media/active-directory-aadconnect-sso/sso7.png)

### <a name="browser-considerations"></a>Considerazioni sui browser

#### <a name="mozilla-firefox"></a>Mozilla Firefox

Mozilla Firefox non esegue automaticamente l'autenticazione Kerberos. Ogni utente deve aggiungere manualmente gli URL di Azure AD alle impostazioni di Firefox usando la procedura seguente:
1. Eseguire Firefox e immettere `about:config` nella barra degli indirizzi. Eliminare tutte le notifiche visualizzate.
2. Cercare la preferenza **network.negotiate-auth.trusted-uris**. Questa preferenza elenca i siti attendibili di Firefox per l'autenticazione Kerberos.
3. Fare clic con il pulsante destro del mouse e selezionare "Modifica".
4. Immettere "https://autologon.microsoftazuread-sso.com, https://aadg.windows.net.nsatc.net" nel campo.
5. Fare clic su "OK" e riaprire il browser.

#### <a name="safari-on-mac-os"></a>Safari su Mac OS

Verificare che il computer che esegue Mac OS sia stato aggiunto a un dominio di AD. [Qui](http://training.apple.com/pdf/Best_Practices_for_Integrating_OS_X_with_Active_Directory.pdf) sono disponibili istruzioni su come eseguire l'operazione.

#### <a name="google-chrome-on-mac-os"></a>Google Chrome su Mac OS

Per Google Chrome su Mac OS e altre piattaforme non Windows, fare riferimento a [questo articolo](https://dev.chromium.org/administrators/policy-list-3#AuthServerWhitelist) per informazioni su come aggiungere gli URL di Azure AD all'elenco degli elementi consentiti per l'autenticazione integrata.

L'uso delle estensioni dei Criteri di gruppo di Active Directory di terze parti per distribuire gli URL di Azure AD in Firefox e Google Chrome su Mac esula dall'ambito di questo articolo.

#### <a name="known-limitations"></a>Limitazioni note

L'accesso SSO facile non funziona in modalità di esplorazione privata in Firefox e nel browser Edge. Non funziona inoltre in Internet Explorer se il browser è in esecuzione in modalità di protezione avanzata.

>[!IMPORTANT]
>Di recente è stato eseguito il rollback del supporto per Microsoft Edge per analizzare i problemi segnalati dai clienti.

## <a name="step-4-test-the-feature"></a>Passaggio 4: Testare la funzionalità

Per testare la funzionalità per un utente specifico, assicurarsi che _tutte_ le condizioni seguenti siano soddisfatte:
  - L'utente esegue l'accesso da un dispositivo aziendale.
  - Il dispositivo è stato aggiunto in precedenza al dominio di Active Directory (AD).
  - Il dispositivo ha una connessione diretta al controller di dominio (DC, Domain Controller), nella rete aziendale cablata o wireless o tramite una connessione di accesso remoto, ad esempio una connessione VPN.
  - La [funzionalità è stata distribuita](##step-3-roll-out-the-feature) all'utente tramite Criteri di gruppo.

Per testare lo scenario in cui l'utente immette solo il nome utente ma non la password:
- Accedere a *https://myapps.microsoft.com/* in una nuova sessione del browser privata.

Per testare lo scenario in cui l'utente non è obbligato a immettere nome utente o password: 
- Accedere a *https://myapps.microsoft.com/contoso.onmicrosoft.com* in una nuova sessione del browser privata. Sostituire "*contoso*" con il nome del tenant.
- In alternativa, accedere a *https://myapps.microsoft.com/contoso.com* in una nuova sessione del browser privata. Sostituire "*contoso.com*" con un dominio verificato, non un dominio federato, nel tenant.

## <a name="step-5-roll-over-keys"></a>Passaggio 5: Rinnovare le chiavi

Nel passaggio 2, Azure AD Connect crea gli account computer, che rappresentano Azure AD, in tutte le foreste di AD in cui è stato abilitato l'accesso SSO facile. [Qui](active-directory-aadconnect-sso-how-it-works.md) sono disponibili informazioni dettagliate a riguardo. Per una maggiore sicurezza, è consigliabile rinnovare spesso le chiavi di decrittografia di Kerberos di questi account computer.

>[!IMPORTANT]
>Non è necessario farlo _subito_ dopo aver abilitato la funzionalità. Rinnovare le chiave di decrittografia di Kerberos almeno ogni 30 giorni.

## <a name="next-steps"></a>Passaggi successivi

- [**Approfondimento tecnico**](active-directory-aadconnect-sso-how-it-works.md): informazioni sul funzionamento di questa funzionalità.
- [**Domande frequenti**](active-directory-aadconnect-sso-faq.md): risposte alle domande più frequenti.
- [**Risoluzione dei problemi**](active-directory-aadconnect-troubleshoot-sso.md): informazioni su come risolvere i problemi comuni relativi a questa funzionalità.
- [**UserVoice**](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect): per l'invio di richieste di nuove funzionalità.

