<properties
	pageTitle="Anteprima di Azure Active Directory B2C: configurazione di token, sessione e accesso Single Sign-On | Microsoft Azure"
	description="Configurazione di token, sessione e accesso Single Sign-On in Azure Active Directory B2C"
	services="active-directory-b2c"
	documentationCenter=""
	authors="swkrish"
	manager="msmbaldwin"
	editor="bryanla"/>

<tags
	ms.service="active-directory-b2c"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/16/2016"
	ms.author="swkrish"/>

# Anteprima di Azure Active Directory B2C: configurazione di token, sessione e accesso Single Sign-On

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

Questa funzionalità offre un controllo dettagliato, in base ai [singoli criteri](active-directory-b2c-reference-policies.md), per gli elementi seguenti:
 
1. Durate dei token di sicurezza emessi da Azure Active Directory (Azure AD) B2C.
2. Durate delle sessioni delle applicazioni Web gestite da Azure AD B2C.
3. Comportamento dell'accesso Single Sign-On (SSO) in più app e criteri nel tenant di B2C.

È possibile usare questa funzionalità nel tenant di B2C come indicato di seguito:

1. Seguire questa procedura per [passare al pannello delle funzionalità B2C](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade) nel portale di Azure.
2. Fare clic su **Criteri di accesso**. *Nota: è possibile usare questa funzionalità su qualsiasi tipo di criterio, non solo per i **Criteri di accesso***.
3. Fare clic su un criterio per aprirlo. Ad esempio, fare clic su **B2C\_1\_SiIn**.
4. Fare clic su **Modifica** nella parte superiore del pannello.
5. Fare clic su **Configurazione di token, sessione e accesso Single Sign-On**.
6. Apportare le modifiche necessarie. Per informazioni sulle proprietà disponibili, vedere le sezioni successive.
7. Fare clic su **OK**.
8. Fare clic su **Salva** nella parte superiore del pannello.

![Schermata della configurazione di token, sessione e accesso Single Sign-On](./media/active-directory-b2c-token-session-sso/token-session-sso.png)

## Configurazione delle durate dei token

Azure AD B2C supporta il [protocollo di autorizzazione OAuth 2.0](active-directory-b2c-reference-protocols.md) per abilitare l'accesso sicuro alle risorse protette. Per implementare questo supporto, Azure AD B2C emette diversi [token di sicurezza](active-directory-b2c-reference-tokens.md). Queste sono le proprietà che è possibile usare per gestire le durate dei token di sicurezza emessi da Azure AD B2C:

- **Durate dei token di accesso e ID (minuti)**: durata del token di connessione OAuth 2.0 usato per ottenere l'accesso a una risorsa protetta. Azure AD B2C emette attualmente solo token ID. Questo valore verrà applicato anche ai token di accesso, quando verrà aggiunto il supporto per questo tipo di token.
   - Impostazione predefinita: 60 minuti.
   - Valore minimo (inclusivo): 15 minuti.
   - Valore massimo (inclusivo): 1440 minuti.
- **Durata del token di aggiornamento (giorni)**: periodo di tempo massimo prima che sia possibile usare un token di aggiornamento per acquisire un nuovo token di accesso o ID e, facoltativamente, un nuovo token di aggiornamento, se all'applicazione è stato concesso l'ambito `offline_access`.
   - Impostazione predefinita: 14 giorni.
   - Valore minimo (inclusivo): 1 giorno.
   - Valore massimo (inclusivo): 90 giorni.
- **Durata della finestra temporale scorrevole del token di aggiornamento (giorni)**: allo scadere di questo periodo, l'utente dovrà ripetere l'autenticazione, indipendentemente dal periodo di validità del token di accesso più recente acquisito dall'applicazione. Questo valore può essere specificato solo se l'opzione è impostata su **Con vincoli**. Deve essere superiore o uguale al valore **Durata del token di aggiornamento (giorni)**. Se l'opzione è impostata su **Senza vincoli**, non sarà possibile specificare un valore specifico.
   - Impostazione predefinita: 90 giorni.
   - Valore minimo (inclusivo): 1 giorno.
   - Valore massimo (inclusivo): 365 giorni.

Ecco un paio di casi di utilizzo in cui è possibile abilitare l'uso di queste proprietà:

- Consentire a un utente di mantenere la connessione a un dispositivo mobile per un periodo illimitato, purché l'utente sia continuamente attivo sull'applicazione. È possibile ottenere questo risultato impostando l'opzione **Durata della finestra temporale scorrevole del token di aggiornamento (giorni)** su **Senza vincoli** nei criteri di accesso.
- Soddisfare i requisiti di sicurezza e conformità del settore, impostando durate appropriate per i token di accesso.

## Configurazione della sessione

Azure AD B2C supporta il [protocollo di autenticazione OpenID Connect](active-directory-b2c-reference-oidc.md) per abilitare l'accesso sicuro alle applicazioni Web. Ecco le proprietà che è possibile usare per gestire le sessioni delle applicazioni Web:

- **Durata della sessione dell'app Web (minuti)**: durata del cookie della sessione di Azure AD B2C archiviato nel browser dell'utente dopo un'autenticazione corretta.
   - Impostazione predefinita: 1440 minuti.
   - Valore minimo (inclusivo): 15 minuti.
   - Valore massimo (inclusivo): 1440 minuti.
- **Timeout della sessione dell'app Web**: se questa opzione è impostata su **Assoluto**, l'utente dovrà ripetere l'autenticazione dopo la scadenza del periodo di tempo specificato da **Durata della sessione dell'app Web (minuti)**. Se l'opzione è impostata su **Continuo** (impostazione predefinita), l'utente rimane connesso, purché sia continuamente attivo nell'applicazione Web.

Ecco un paio di casi di utilizzo in cui è possibile abilitare l'uso di queste proprietà:

- Soddisfare i requisiti di sicurezza e conformità del settore, impostando durate appropriate per la sessione dell'applicazione Web.
- Imporre la ripetizione dell'autenticazione dopo un periodo di tempo specifico durante l'interazione di un utente con una parte a sicurezza elevata dell'applicazione Web. 

## Configurazione dell'accesso Single Sign-On

Se il tenant di B2C include più applicazioni e criteri, è possibile gestire le interazioni degli utenti tra di essi usando la proprietà **Configurazione dell'accesso Single Sign-On**. È possibile impostare la proprietà su uno dei valori seguenti:

- **Tenant**: impostazione predefinita. L'uso di questa impostazione consente a più applicazioni e criteri del tenant di B2C di condividere la stessa sessione utente. Ad esempio, quando un utente accede a un'applicazione, Contoso Shopping, può accedere contemporaneamente anche a un'altra applicazione, Contoso Pharmacy, senza alcun problema.
- **Applicazione**: consente di mantenere una sessione utente esclusivamente per un'applicazione, indipendentemente dalle altre applicazioni. Ad esempio, un utente può accedere a Contoso Pharmacy, usando le stesse credenziali, anche se ha già eseguito l'accesso a Contoso Shopping, un'altra applicazione nello stesso tenant di B2C. 
- **Criterio**: consente di mantenere una sessione utente esclusivamente per un criterio, indipendentemente dalle applicazioni che lo usano. Ad esempio, se un utente ha già effettuato l'accesso e completato un passaggio di Multi Factor Authentication, otterrà l'accesso a parti a sicurezza più elevata di più applicazioni, purché la sessione associata al criterio non scada.
- **Disabilitato**: impone all'utente di eseguire il percorso utente completo a ogni esecuzione del criterio. Ad esempio, ciò consente a più utenti di accedere all'applicazione, in uno scenario di desktop condiviso, anche se un singolo utente rimane connesso per l'intero periodo di tempo.

<!---HONumber=AcomDC_0518_2016-->