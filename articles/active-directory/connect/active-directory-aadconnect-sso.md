---
title: 'Azure AD Connect: Seamless Single Sign-On | Documentazione Microsoft'
description: Questo argomento descrive Seamless Single Sign-On di Azure Active Directory (Azure AD) e in che modo consente di fornire il vero single sign-on agli utenti dei desktop aziendali all&quot;interno della rete aziendale.
services: active-directory
keywords: "che cos&quot;è Azure AD Connect, installare Active Directory, componenti richiesti per Azure AD, SSO, Single Sign-On"
documentationcenter: 
author: swkrish
manager: femila
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/08/2017
ms.author: billmath
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 2601850f99188445cf63a6a4f185bdc4ebb92c29
ms.contentlocale: it-it
ms.lasthandoff: 05/10/2017

---

# <a name="azure-active-directory-seamless-single-sign-on"></a>Seamless Single Sign-On di Azure Active Directory

## <a name="what-is-azure-active-directory-seamless-single-sign-on"></a>Cos'è Seamless Single Sign-On di Azure Active Directory?

Seamless Single Sign-On (Seamless SSO) di Azure Active Directory fornisce il vero punto di accesso singolo agli utenti che eseguono l'accesso dal proprio PC desktop aziendale connesso alla rete aziendale. Quando la funzionalità è abilitata, gli utenti non dovranno digitare la password per eseguire l'accesso ad Azure AD e nella maggior parte dei casi non dovranno neppure digitare il loro nome utente. Questa funzionalità consente agli utenti di accedere facilmente ai servizi basati su cloud senza la necessità di componenti aggiuntivi in locale.

La funzionalità Seamless SSO può essere abilitata tramite Azure AD Connect e può essere combinata con la [sincronizzazione delle password](active-directory-aadconnectsync-implement-password-synchronization.md) o l'[autenticazione pass-through](active-directory-aadconnect-pass-through-authentication.md).

>[!NOTE]
>Questa funzionalità NON è applicabile ad Active Directory Federation Services (ADFS) che dispone già di tale funzione.

Perché questa funzionalità sia operativa per un utente specifico, devono essere soddisfatte le condizioni seguenti:

- L'utente esegue l'accesso da un desktop aziendale.
- Il desktop è stato aggiunto in precedenza al dominio di Active Directory (AD).
- Il desktop ha una connessione diretta al controller di dominio (DC), nella rete aziendale cablata o wireless o tramite una connessione di accesso remoto, ad esempio una connessione VPN.
- Gli endpoint di servizio sono stati inclusi nell'area Intranet del browser.

Se una delle condizioni precedenti non è soddisfatta, all'utente verrà richiesto di immettere il nome utente e la password come prima.

![Seamless Single Sign-On](./media/active-directory-aadconnect-sso/sso1.png)

Seamless SSO offre anche le altre funzionalità seguenti:

- Se una richiesta di accesso ad Azure AD include il parametro `domain_hint` o `login_hint` (avviato da un'applicazione nel tenant), Seamless SSO sfrutterà tale parametro e l'utente eviterà di l'immissione del nome utente e della password.
- Seamless SSO supporta il nome utente che corrisponde al nome utente predefinito locale, in genere "userPrincipalName", o un altro attributo configurato in Azure AD Connect, definito "ID alternativo".
- Seamless SSO è una funzionalità opportunistica, il che significa che, se per qualche motivo ha esito negativo, l'esperienza di accesso dell'utente ritorna al comportamento normale, ovvero l'utente dovrà immettere la propria password nella pagina di accesso.

## <a name="whats-available-during-preview"></a>Cos'è disponibile durante l'anteprima?

>[!NOTE]
>Seamless SSO di Azure AD è attualmente in fase di anteprima. È una funzionalità gratuita e non serve alcuna delle edizioni a pagamento di Azure AD per utilizzarla.

La funzionalità Seamless Single Sign-On è supportata tramite i client basati su Web browser e i client di Office che prevedono l'[autenticazione moderna](https://aka.ms/modernauthga) nei PC desktop che supportano l'autenticazione Kerberos, ad esempio i PC desktop basati su Windows. La matrice seguente fornisce i dettagli dei client basati su browser su vari sistemi operativi.

| SO\Browser |Internet Explorer|Google Chrome|Mozilla Firefox|Edge
| --- | --- |--- | --- | --- |
|Windows 10|Sì|Sì|Sì\*|No
|Windows 8.1|Sì|Sì|Sì\*|N/D 
|Windows 8|Sì|Sì|Sì\*|N/D 
|Windows 7|Sì|Sì|Sì\*|N/D
|Mac OS X|N/D|Sì\*|Sì\*|N/D

\*Richiede configurazione aggiuntiva.

>[!NOTE]
>Per Windows 10, si consiglia di usare l'[aggiunta ad Azure AD](../active-directory-azureadjoin-overview.md) per ottenere risultati ottimali con Azure AD.

## <a name="how-does-azure-ad-seamless-sso-work"></a>Come opera la funzionalità Seamless SSO di Azure AD?

È possibile abilitare la funzionalità Seamless SSO in Azure AD Connect come mostrato [di seguito](#how-to-enable-azure-ad-seamless-sso?). Una volta abilitata, viene creato un account computer denominato AZUREADSSOACCT in Active Directory (AD) locale e la chiave di decrittografia di Kerberos è condivisa in modo sicuro con Azure AD. Inoltre, vengono creati due nomi di entità servizio (SPN) Kerberos per rappresentare due URL di servizio utilizzati durante l'accesso ad Azure AD.

>[!NOTE]
> È necessario creare l'account computer e gli SPN Kerberos in ogni foresta Active Directory che si sincronizza con Azure AD (tramite Azure AD Connect) e per i cui utenti si desidera abilitare la funzionalità Seamless SSO. Se la foresta Active Directory dispone di unità organizzative (OU) per gli account computer, dopo aver abilitato la funzionalità Seamless SSO, spostare l'account computer AZUREADSSOACCT in una OU per assicurarsi che non venga eliminato e che sia gestito nello stesso modo degli altri account computer.

Una volta completata questa configurazione, l'accesso ad Azure AD funziona esattamente come qualsiasi altro accesso che usa l'autenticazione integrata di Windows (IWA). La funzionalità Seamless SSO opera nel modo seguente:

Si supponga che l'utente tenti di accedere a una risorsa basata su cloud che è protetta da Azure AD, ad esempio SharePoint Online. SharePoint Online reindirizza il browser dell'utente ad Azure AD per l'accesso.

Se la richiesta di accesso ad Azure AD include il parametro `domain_hint` (identifica il tenant di Azure AD, ad esempio contoso.onmicrosoft.com) o il parametro `login_hint` (identifica il nome utente dell'utente, ad esempio user@contoso.onmicrosoft.com o user@contoso.com), si verificano i passaggi seguenti da 1 a 5.

Se uno di questi due parametri non è incluso nella richiesta, all'utente viene chiesto di fornire il proprio nome utente nella pagina di accesso ad Azure AD. I passaggi da 1 a 5 si verificano solo dopo l'utente esce dal campo del nome utente mediante tabulazione o fa clic sul pulsante "Continua".

1. Azure AD richiede al client, tramite una risposta di tipo 401 Non autorizzato, di fornire un ticket Kerberos.
2. Il client richiede un ticket ad Active Directory per Azure AD (rappresentato dall'account computer configurato in precedenza).
3. Active Directory individua l'account computer e restituisce un ticket Kerberos al client, crittografato con il segreto dell'account computer. Il ticket include l'identità dell'utente attualmente connesso al desktop.
4. Il client invia il ticket Kerberos acquisito da Active Directory ad Azure AD.
5. Azure AD decrittografa il ticket Kerberos utilizzando la chiave già condivisa, In caso di esito positivo, Azure AD restituisce un token o chiede all'utente di eseguire prove aggiuntive, ad esempio l'autenticazione a più fattori come richiesto dalla risorsa.

Seamless SSO è una funzionalità opportunistica, il che significa che, se per qualche motivo ha esito negativo, l'esperienza di accesso dell'utente ritorna al comportamento normale, ovvero l'utente dovrà immettere la propria password nella pagina di accesso.

Il processo è illustrato anche nel diagramma riportato di seguito:

![Seamless Single Sign-On](./media/active-directory-aadconnect-sso/sso2.png)

## <a name="how-to-enable-azure-ad-seamless-sso"></a>Come abilitare la funzionalità Seamless SSO di Azure AD?

### <a name="pre-requisites"></a>Prerequisiti

Se si abilita la funzionalità Seamless Single Sign-On con l'autenticazione pass-through, non sono necessari pre-requisiti aggiuntivi oltre a quelli richiesti per la funzione di autenticazione pass-through.

Se si abilita la funzionalità Seamless Single Sign-On con sincronizzazione password e se è presente un firewall tra Azure AD Connect e Azure AD, assicurarsi che:

- Il server Azure AD Connect possa comunicare con gli URL `*.msappproxy.net`.
- Azure AD Connect (versioni 1.1.484.0 o successive) può eseguire richieste HTTPS ad Azure AD sulla porta 443. Si usa solo per abilitare la funzionalità e non per l'accesso effettivo degli utenti.
- Azure AD Connect possa anche eseguire connessioni IP dirette agli [intervalli IP dei data center di Azure](https://www.microsoft.com/download/details.aspx?id=41653). Anche in questo caso, viene usato solo per abilitare la funzionalità.

>[!NOTE]
> Le versioni precedenti di Azure AD Connect (inferiori alla 1.1.484.0) devono poter comunicare con Azure AD sulla porta 9090.

### <a name="enabling-the-azure-ad-seamless-sso-feature"></a>Abilitazione della funzionalità Seamless SSO di Azure AD

La funzionalità Seamless SSO di Azure AD può essere abilitata tramite Azure AD Connect.

Se si esegue una nuova installazione di Azure AD Connect, scegliere il [percorso di installazione personalizzato](active-directory-aadconnect-get-started-custom.md). Nella pagina "Accesso utente" selezionare l'opzione "Abilita Single Sign-On".

![Azure AD Connect - Accesso utente](./media/active-directory-aadconnect-sso/sso8.png)

Se si ha già un'installazione di Azure AD Connect, eseguire la configurazione usando il percorso di [installazione rapida](active-directory-aadconnect-get-started-express.md) o di [installazione personalizzata](active-directory-aadconnect-get-started-custom.md), scegliere la pagina "Cambia l'accesso utente" in Azure AD Connect e fare clic su "Avanti". Selezionare quindi l'opzione "Abilita Single Sign-On".

![Azure AD Connect - Cambiare l'accesso utente](./media/active-directory-aadconnect-user-signin/changeusersignin.png)

Continuare la procedura guidata fino a quando si arriva alla pagina "Abilita Single Sign-On". È necessario fornire le credenziali di amministratore di dominio per ogni foresta AD che si sincronizza con Azure AD (tramite Azure AD Connect) e per i cui utenti si desidera abilitare la funzionalità Seamless SSO. Si noti che le credenziali di amministratore di dominio non sono archiviate in Azure AD Connect o in Azure AD, ma sono utilizzate solo per creare l'account computer e configurare gli SPN Kerberos come descritto in precedenza.

Al termine della procedura guidata, Seamless SSO è abilitato nel tenant. Si noti che è comunque necessario completare i passaggi della sezione successiva prima che gli utenti possono trarre vantaggio da questa funzionalità.

## <a name="rolling-the-feature-out-to-your-users"></a>Distribuzione della funzionalità agli utenti

Per distribuire la funzionalità Seamless SSO agli utenti, è necessario aggiungere due URL di Azure AD (https://autologon.microsoftazuread-sso.com e https://aadg.windows.net.nsatc.net) alle impostazioni dell'area Intranet degli utenti tramite Criteri di gruppo in Active Directory. Si noti che questo vale solo per Internet Explorer e Google Chrome (se condivide lo stesso insieme di URL di siti attendibili di Internet Explorer). È necessario eseguire una configurazione separata per Mozilla Firefox.

### <a name="why-do-you-need-this"></a>Perché è necessario eseguire questa operazione?

Per impostazione predefinita, i browser inviano i ticket Kerberos a un endpoint del cloud solo se il relativo URL è definito come parte dell'area Intranet del browser. Il browser calcola automaticamente l'area giusta (Internet o Intranet) dall'URL. Ad esempio l'URL http://contoso/ verrà mappato all'area Intranet, mentre l'URL http://intranet.contoso.com/ verrà mappato all'area Internet (perché l'URL contiene un punto).

Poiché gli URL di Azure AD usati per la funzionalità Seamless SSO contengono un punto, devono essere aggiunti esplicitamente all'area Intranet di ogni browser. In questo modo il browser invia automaticamente i ticket Kerberos dell'utente attualmente connesso ad Azure AD. Anche se è possibile farlo manualmente su ogni PC desktop, il modo più semplice per aggiungere gli URL richiesti all'area Intranet per tutti gli utenti consiste nel creare un criterio di gruppo in Active Directory.

### <a name="detailed-steps"></a>Procedura dettagliata

1. Aprire lo strumento Gestione criteri di gruppo.
2. Modificare i criteri di gruppo applicati a tutti gli utenti, ad esempio il **Criterio dominio predefinito**.
3. Passare a **Configurazione utente\Modelli amministrativi\Componenti di Windows\Internet Explorer\Pannello di controllo Internet\Scheda Sicurezza** e selezionare **Elenco di assegnazione siti ad aree**.
![Single Sign-On](./media/active-directory-aadconnect-sso/sso6.png)  
4. Abilitare i criteri e immettere i valori/dati seguenti nella finestra di dialogo. Questi sono gli URL di Azure AD in cui vengono inviati i ticket Kerberos.

        Value: https://autologon.microsoftazuread-sso.com
        Data: 1
        Value: https://aadg.windows.net.nsatc.net
        Data: 1
5. Fare clic su **OK**, quindi nuovamente su **OK**.

L'aspetto dovrebbe risultare simile al seguente:

![Single sign-on](./media/active-directory-aadconnect-sso/sso7.png)

>[!NOTE]
>Per impostazione predefinita, Chrome usa lo stesso insieme di URL di siti attendibili di Internet Explorer. Se per Chrome sono state configurate impostazioni diverse, sarà necessario aggiornare le impostazioni separatamente.

## <a name="disabling-azure-ad-seamless-sso"></a>Disabilitazione della funzionalità Seamless SSO di Azure AD

La funzionalità Seamless SSO di Azure AD può essere disabilitata tramite Azure AD Connect.

Eseguire Azure AD Connect, scegliere la pagina "Cambia l'accesso utente" e fare clic su "Avanti". Deselezionare quindi l'opzione "Abilita Single Sign-On". Continuare la procedura guidata. Al termine della procedura guidata, Seamless SSO è disabilitato nel tenant. Verrà tuttavia visualizzato un messaggio con il testo seguente:

"L'accesso Single Sign-On è ora disabilitato, ma è necessario eseguire altri passaggi manuali per completare la pulizia. Altre informazioni"

Questa è la procedura manuale da eseguire:

- Ottenere l'elenco di foreste di AD in cui è stato abilitato Seamless SSO.
  - In PowerShell chiamare `New-AzureADSSOAuthenticationContext`. Dovrebbe essere visualizzata una finestra popup per l'immissione delle credenziali dell'amministratore del tenant di Azure AD.
  - Chiamare `Get-AzureADSSOStatus`. Verrà visualizzato l'elenco di foreste di AD, ovvero l'elenco "Domini", in cui è stata abilitata questa funzionalità.
- Eliminare manualmente l'account AZUREADSSOACCT del computer da ogni foresta di AD elencata in precedenza.

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni su come risolvere i problemi comuni della funzionalità Seamless SSO di Azure AD, vedere la [guida alla risoluzione dei problemi](active-directory-aadconnect-troubleshoot-sso.md).

## <a name="feedback"></a>Commenti e suggerimenti

I commenti degli utenti sono importanti. Usare la sezione relativa ai commenti seguente in caso di domande. Per richieste sulla nuova funzionalità, vedere il [forum UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect).

