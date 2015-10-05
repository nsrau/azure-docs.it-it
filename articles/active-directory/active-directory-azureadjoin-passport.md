<properties 
	pageTitle="Panoramica di Microsoft Passport e informazioni dettagliate su questa nuova autenticazione basata su certificazione. | Microsoft Azure" 
	description="Argomento che spiega come configurare Aggiunta di Azure AD durante la procedura di configurazione iniziale guidata." 
	services="active-directory" 
	documentationCenter="" 
	authors="femila" 
	manager="stevenpo" 
	editor=""/>

<tags 
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/21/2015" 
	ms.author="femila"/>

# Autenticazione delle identità senza password con Microsoft Passport

Gli attuali metodi di autenticazione basati solo su password non sono sufficienti a garantire la protezione degli utenti. Gli utenti riutilizzano e dimenticano le password. Le password possono causare violazioni della protezione, phishing, oltre ad essere facilmente violabili. Sono inoltre difficili da ricordare e più soggette ad attacchi di tipo "pass-the-hash".

## Informazioni su Microsoft Passport
Microsoft Passport è un nuovo approccio di autenticazione basato su certificato o su chiave privata/pubblica disponibile per organizzazioni e singoli utenti grazie al quale non è più necessario usare password. Questo tipo di autenticazione si basa su credenziali a coppie di chiavi che possono sostituire le password e risultare efficaci in caso di violazioni, furti e phishing. Microsoft Passport consente agli utenti di eseguire l'autenticazione con un account Microsoft, un account di Active Directory, un account di Microsoft Azure Active Directory (AD) o un servizio non Microsoft che supporta l'autenticazione FIDO (Fast ID Online). Dopo una verifica iniziale in due passaggi durante la registrazione di Microsoft Passport, nel dispositivo dell'utente viene impostato un Microsoft Passport. L'utente deve quindi impostare un movimento, come Windows Hello o un PIN, che dovrà eseguire per verificare l'identità. Windows usa quindi Microsoft Passport per autenticare gli utenti e consentire loro di accedere a risorse e servizi protetti.

La chiave privata viene resa disponibile solo tramite un "movimento utente", come un PIN, un movimento biometrico o un dispositivo remoto, ad esempio una smart card usata per accedere al dispositivo. Queste informazioni vengono quindi collegate a un certificato o a una coppia di chiavi asimmetriche. La chiave privata viene attestata a livello hardware se il dispositivo è dotato di chip TPM (Trusted Platform Module). La chiave privata è sempre associata al dispositivo.

La chiave pubblica viene registrata con Azure Active Directory e Windows Server Active Directory (per la configurazione locale). I provider di identità (IDP) convalidano l'utente abbinando la chiave pubblica a quella privata e fornendo le informazioni di accesso tramite OTP (One Time Password), Phonefactor o un diverso meccanismo di notifica.
## Vantaggi dell'adozione di Microsoft Passport
L'abilitazione di Microsoft Passport consente alle aziende di proteggere ulteriormente le proprie risorse in quanto possono:

* Impostare Microsoft Passport specificando come opzione preferita quella hardware. In tal modo le chiavi verranno generate in TPM 1.2 o TPM 2.0 se disponibile e tramite software se TPM non è disponibile. 

* Definire la complessità e la lunghezza del PIN e scegliere se abilitare l'utilizzo di Hello nell'organizzazione.

* Configurare Microsoft Passport in modo da supportare scenari analoghi a quelli con smart card usando l'attendibilità basata su certificati.

## Come funziona
1. Le chiavi vengono generate a livello hardware. Numerosi computer sono dotati di chip TPM (Trusted Platform Module) integrati che consente di proteggere l'hardware integrando chiavi di crittografia nei dispositivi. Per generare le chiavi o i certificati che verranno codificati dalle chiavi generate, viene usato TPM 1.2 o TPM 2.0.

2. Queste chiavi associate all'hardware vengono attestate da TPM.

3. Un singolo movimento di sblocco consente di sbloccare il dispositivo e di accedere a più risorse se il dispositivo è stato aggiunto al dominio o ad Azure AD.

## Ciclo di vita di Microsoft Passport
Ciclo di vita dell'autenticazione con Microsoft Passport ![](./media/active-directory-azureadjoin/active-directory-azureadjoin-microsoft-passport.png) Il diagramma precedente illustra l'utilizzo della coppia di chiavi privata/pubblica e il meccanismo di convalida eseguito dal provider di identità. Ognuno di questi passaggi è illustrato in dettaglio di seguito:

1. L'utente dimostra la propria identità usando diversi metodi integrati (movimenti, smart card fisiche, autenticazione a più fattori) e invia queste informazioni al provider di identità (IDP), ad esempio Azure Active Directory o Active Directory.

2.  Il dispositivo crea quindi le chiavi, attesta la chiave, prende la parte pubblica della chiave, vi allega le istruzioni sulla postazione, effettua l'accesso e la invia al provider di identità in modo che venga registrata.

3. Non appena la parte pubblica della chiave viene registrata nel provider di identità, richiede al dispositivo di autenticarsi eseguendo l'accesso con la parte privata della chiave. Il provider di identità esegue la convalida e rilascia quindi il token di autenticazione che consente all'utente di accedere a risorse protette.

4. Non appena la parte pubblica della chiave viene registrata nel provider di identità, richiede al dispositivo di autenticarsi eseguendo l'accesso con la parte privata della chiave.

5. Il provider di identità esegue la convalida e rilascia quindi il token di autenticazione che consente all'utente e al dispositivo di accedere a risorse protette. I provider di identità possono scrivere app multipiattaforma o usare il supporto del browser tramite API JS/Webcrypto per creare e usare credenziali di Microsoft Passport per i propri utenti.

## Requisiti per la distribuzione
A livello aziendale
---------------------------
* Sottoscrizione di Azure

A livello dell'utente
-------------------------------------------------------------
* Il computer deve eseguire Windows 10 Professional o Enterprise SKU

## Informazioni aggiuntive

* [Estensione delle funzionalità del cloud ai dispositivi Windows 10 tramite Aggiunta ad Azure Active Directory](active-directory-azureadjoin-overview.md)
* [Configurare Aggiunta di Azure AD](active-directory-azureadjoin-setup.md)

<!---HONumber=Sept15_HO4-->