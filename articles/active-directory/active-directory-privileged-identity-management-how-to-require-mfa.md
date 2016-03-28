<properties
   pageTitle="Azure AD Privileged Identity Management: Come richiedere l'autenticazione a più fattori"
   description="Informazioni su come richiedere l'autenticazione a più fattori (MFA, Multi-Factor Authentication) per identità con privilegi con l'estensione Azure Active Directory Privileged Identity Management."
   services="active-directory"
   documentationCenter=""
   authors="kgremban"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="03/08/2016"
   ms.author="kgremban"/>

# Azure Privileged Identity Management: Come richiedere l'MFA

È consigliabile richiedere l'autenticazione a più fattori per tutti gli amministratori.

## Richiedere l'MFA in Azure AD Privileged Identity Management

Quando si accede come amministratore PIM, si riceveranno avvisi che suggeriscono che gli account con privilegi devono richiedere l'autenticazione a più fattori (MFA, Multi-Factor Authentication). Fare clic sull'avviso di sicurezza nel dashboard PIM e verrà aperto un nuovo pannello con un elenco degli account amministratore che devono richiedere l'MFA. È possibile richiedere il servizio MFA selezionando più ruoli e quindi facendo clic sul pulsante **Correggi** oppure è possibile fare clic sui puntini di sospensione accanto ai singoli ruoli e quindi fare clic sul pulsante **Correggi**.

È anche possibile modificare il requisito di MFA per un ruolo specifico facendo clic sul ruolo nella sezione Ruoli del dashboard e abilitando il servizio MFA per tale ruolo facendo clic su **Impostazioni** nel pannello del ruolo e quindi selezionando l'opzione **Abilita** in Multi-Factor Authentication.

## Modalità di convalida della MFA da parte di Azure AD PIM

> [AZURE.IMPORTANT] Poiché gli account Microsoft, ad esempio @outlook.com, @live.com oppure @hotmail.com, non sono attualmente supportati per la registrazione per Azure MFA, non saranno consentiti come amministratori temporanei per ruoli con privilegi elevati. Se gli utenti devono continuare a gestire i carichi di lavoro usando un account Microsoft, convertirli in amministratori permanenti per il momento.

Sono disponibili due opzioni per la convalida di MFA quando un utente attiva un ruolo.

Il modo più semplice consiste nel basarsi su Azure MFA per gli utenti che attivano un ruolo con privilegi. A questo scopo, verificare prima di tutto che gli utenti abbiano una licenza, se necessario, e che abbiano effettuato la registrazione ad Azure MFA. Per altre operazioni su questa procedura, vedere [Introduzione ad Azure Multi-Factor Authentication sul cloud](../multi-factor-authentication/multi-factor-authentication-get-started-cloud.md#assigning-an-azure-mfa-azure-ad-premium-or-enterprise-mobility-license-to-users). Si noti che è consigliato, ma non obbligatorio, configurare Azure AD per l'imposizione di MFA per questi utenti all'accesso. Le verifiche di MFA verranno infatti effettuate direttamente da Azure AD PIM.

In alternativa, se gli utenti eseguono l'autenticazione in locale, è possibile assegnare al provider di identità la responsabilità di MFA. Ad esempio, se AD Federation Services è stato configurato per richiedere l'autenticazione basata su smart card prima dell'accesso ad Azure AD, l'articolo relativo alla [Protezione delle risorse cloud con Azure Multi-Factor Authentication e AD FS](../multi-factor-authentication/multi-factor-authentication-get-started-adfs-cloud.md) include istruzioni per la configurazione di AD FS per l'invio di attestazioni ad Azure AD. Quando un utente prova ad attivare un ruolo, Azure AD PIM accetta il fatto che la Multi-Factor Authentication sia già stata convalidata per l'utente alla ricezione delle attestazioni appropriate.


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Passaggi successivi
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!---HONumber=AcomDC_0316_2016-->