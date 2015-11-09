<properties
   pageTitle="Limitazioni correnti della versione di anteprima per la collaborazione B2B di Azure Active Directory | Microsoft Azure"
   description="Azure Active Directory B2B supporta le relazioni tra società abilitando i partner commerciali ad accedere in modo selettivo alle applicazioni aziendali"
   services="active-directory"
   authors="viv-liu"
   manager="cliffdi"
   editor=""
   tags=""/>

<tags
   ms.service="active-directory"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="identity"
   ms.date="10/27/2015"
   ms.author="viviali"/>

# Limitazioni correnti della versione di anteprima per la collaborazione B2B di Azure Active Directory (Azure AD)

- La Multi-Factor Authentication (MFA) non è supportata per gli utenti esterni. Ad esempio, se Contoso dispone dell’autenticazione a più fattori, ma Partner Org non ne dispone, agli utenti di Partner Org non è concessa l'autenticazione a più fattori (MFA) dalla collaborazione B2B.
- Gli inviti sono possibili solo tramite CSV; gli inviti singoli e l'accesso all'API non sono supportati.
- Solo gli amministratori globali di Azure Active Directory possono caricare file con estensione csv.
- Gli inviti a indirizzi di posta elettronica personali, ad esempio hotmail.com, Gmail.com o comcast.net non sono attualmente supportati.
- L’accesso degli utenti esterni ad applicazioni locali non è testato.
- Gli utenti esterni non vengono automaticamente eliminati quando l'utente effettivo viene eliminato dalla loro directory.
- Gli inviti a liste di distribuzione non sono supportati.
- Un numero massimo di 2.000 record può essere caricato tramite CSV.

## Articoli correlati
Vedere gli altri articoli sulla collaborazione B2B di Azure:

- [Informazioni sulla collaborazione B2B di Azure AD](active-directory-b2b-what-is-azure-ad-b2b.md)
- [Funzionamento](active-directory-b2b-how-it-works.md)
- [Procedura dettagliata](active-directory-b2b-detailed-walkthrough.md)
- [Riferimento sul formato di file CSV](active-directory-b2b-references-csv-file-format.md)
- [Formato del token per l'utente esterno](active-directory-b2b-references-external-user-token-format.md)
- [Modifiche agli attributi dell'oggetto utente esterno](active-directory-b2b-references-external-user-object-attribute-changes.md)

<!---HONumber=Nov15_HO1-->