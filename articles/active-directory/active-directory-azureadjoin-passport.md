<properties
	pageTitle="Autenticazione delle identità senza password con Microsoft Passport | Microsoft Azure"
	description="Fornisce una panoramica di Microsoft Passport e informazioni aggiuntive sulla distribuzione di Microsoft Passport."
	services="active-directory"
	documentationCenter=""
	authors="femila"
	manager="swadhwa"
	editor=""
	tags="azure-classic-portal"/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/27/2016"
	ms.author="femila"/>

# Autenticazione delle identità senza password con Microsoft Passport

Gli attuali metodi di autenticazione basati solo su password non sono sufficienti a garantire la protezione degli utenti. Gli utenti riutilizzano e dimenticano le password. Le password possono causare violazioni della protezione, phishing, oltre ad essere facilmente violabili. Sono inoltre difficili da ricordare e più soggette ad attacchi di tipo "[pass-the-hash](https://technet.microsoft.com/dn785092.aspx)".

## Informazioni su Microsoft Passport
Microsoft Passport è un approccio di autenticazione basato su certificato o su chiave privata/pubblica disponibile per organizzazioni e utenti grazie al quale non è più necessario usare password. Questo tipo di autenticazione si basa su credenziali a coppie di chiavi che possono sostituire le password e risultare efficaci in caso di violazioni, furti e phishing.

 Microsoft Passport consente agli utenti di eseguire l'autenticazione a un account Microsoft, un account di Windows Server Active Directory, un account di Microsoft Azure Active Directory (Azure AD) o un servizio non Microsoft che supporta l'autenticazione FIDO (Fast IDentity Online). Dopo una verifica iniziale in due passaggi durante la registrazione di Microsoft Passport, nel dispositivo dell'utente viene configurato Microsoft Passport. L'utente deve quindi impostare un movimento, come Windows Hello o un PIN. L'utente esegue il movimento per verificare la propria identità. Windows usa quindi Microsoft Passport per autenticare gli utenti e consentire loro di accedere a risorse e servizi protetti.

La chiave privata viene resa disponibile solo tramite un "movimento utente", come un PIN, un movimento biometrico o un dispositivo remoto, ad esempio una smart card usata per accedere al dispositivo. Queste informazioni vengono quindi collegate a un certificato o a una coppia di chiavi asimmetriche. La chiave privata viene attestata a livello hardware se il dispositivo è dotato di chip TPM (Trusted Platform Module). La chiave privata è sempre associata al dispositivo.

La chiave pubblica viene registrata con Azure Active Directory e Windows Server Active Directory (per la configurazione locale). I provider di identità (IDP) convalidano l'utente abbinando la chiave pubblica a quella privata e fornendo le informazioni di accesso tramite OTP (One Time Password), PhoneFactor o un altro meccanismo di notifica.

## Vantaggi dell'adozione di Microsoft Passport da parte delle aziende

L'abilitazione di Microsoft Passport consente alle aziende di proteggere ulteriormente le proprie risorse in quanto possono:

* Configurare Microsoft Passport con l'opzione hardware preferita. Ciò significa che le chiavi verranno generate su TPM 1.2 o TPM 2.0, se disponibile. Quando TPM non è disponibile, la chiave verrà generata dal software.

* Definire la complessità e la lunghezza del PIN e scegliere se abilitare l'utilizzo di Hello nell'organizzazione.

* Configurare Microsoft Passport in modo da supportare scenari analoghi a quelli con smart card usando l'attendibilità basata su certificati.

## Funzionamento di Microsoft Passport
1. Le chiavi vengono generate a livello hardware da TPM o dal software. Numerosi dispositivi sono dotati di chip TPM integrato che consente di proteggere l'hardware integrando chiavi di crittografia nei dispositivi. Tramite TPM 1.2 o TPM 2.0 vengono generate chiavi o certificati creati da chiavi generate.

2. Queste chiavi associate all'hardware vengono attestate da TPM.

3. Un singolo movimento di sblocco consente di sbloccare il dispositivo. Questo movimento consente di accedere a più risorse se il dispositivo è stato aggiunto al dominio o ad Azure AD.

## Funzionamento del ciclo di vita di Microsoft Passport

![Ciclo di vita di Microsoft Passport](./media/active-directory-azureadjoin/active-directory-azureadjoin-microsoft-passport.png)

Il diagramma precedente illustra la coppia di chiavi privata/pubblica e il meccanismo di convalida eseguito dal provider di identità. Ognuno di questi passaggi è illustrato in dettaglio di seguito:

1. L'utente dimostra la propria identità usando diversi metodi integrati (movimenti, smart card fisiche, autenticazione a più fattori) e invia queste informazioni a un provider di identità (IDP), ad esempio Azure Active Directory o Active Directory locale.

2. Il dispositivo crea quindi la chiave, la attesta, prende la parte pubblica della chiave, vi allega le istruzioni sulla postazione, esegue l'accesso e la invia al provider di identità in modo che venga registrata.

4. Non appena la parte pubblica della chiave viene registrata nel provider di identità, questo richiede al dispositivo di autenticarsi eseguendo l'accesso con la parte privata della chiave.

5. Il provider di identità esegue quindi la convalida e rilascia il token di autenticazione che consente all'utente e al dispositivo di accedere alle risorse protette. I provider di identità possono scrivere app multipiattaforma o usare il supporto del browser tramite API JavaScript/Webcrypto per creare e usare credenziali di Microsoft Passport per i propri utenti.

## Requisiti di distribuzione per Microsoft Passport
### A livello aziendale

* L'azienda ha una sottoscrizione di Azure.

### A livello dell'utente

* Nel computer dell'utente viene eseguito Windows 10 Professional o Enterprise.

Per istruzioni dettagliate sulla distribuzione, vedere [Abilitare Microsoft Passport per lavorare all'interno dell'organizzazione](active-directory-azureadjoin-passport-deployment.md).


## Informazioni aggiuntive

* [Windows 10 per le aziende: modalità d'uso dei dispositivi di lavoro](active-directory-azureadjoin-windows10-devices-overview.md)
* [Estensione delle funzionalità del cloud ai dispositivi Windows 10 tramite Aggiunta ad Azure Active Directory](active-directory-azureadjoin-user-upgrade.md)
* [Scenari di utilizzo per Aggiunta ad Azure AD](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Connettere dispositivi appartenenti a un dominio ad Azure AD per usufruire di Windows 10](active-directory-azureadjoin-devices-group-policy.md)
* [Configurare Aggiunta di Azure AD](active-directory-azureadjoin-setup.md)

<!---HONumber=AcomDC_0928_2016-->