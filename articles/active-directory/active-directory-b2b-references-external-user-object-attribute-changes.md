<properties
   pageTitle="L’attributo dell’oggetto utente esterno cambia per l'anteprima di B2B Azure Active Directory Collaboration | Microsoft Azure"
   description="Azure Active Directory B2B supporta le relazioni tra aziende abilitando i partner commerciali ad accedere in modo selettivo alle applicazioni aziendali"
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
   ms.workload="na"
   ms.date="10/27/2015"
   ms.author="viviali"/>

# L’attributo dell’oggetto utente esterno cambia per l'anteprima di B2B Azure Active Directory (Azure AD) Collaboration
Ciascun utente in una directory di Azure AD è rappresentato da un oggetto utente. L'oggetto utente in Azure AD subisce modifiche dell’attributo in diverse fasi del flusso di invito-riscatto della B2B Collaboration. L'oggetto utente che rappresenta l'utente partner nella directory dispone di attributi che cambiano al momento del riscatto, quando l'utente partner fa clic sul collegamento nel messaggio di posta elettronica di invito. In particolare:

- Gli attributi **SignInName** e **AltSecId** vengono popolati
- L’attributo **DisplayName** viene modificato dal nome principale dell'utente (user\_fabrikam.com#EXT#@contoso.com) al nome di accesso (user@fabrikam.com)

Il rilevamento di questi attributi in Azure AD può aiutare a risolvere i problemi relativi a un utente partner che ha riscattato invito di B2B Collaboration.

## Articoli correlati
Vedere gli altri articoli sulla collaborazione B2B di Azure.

- [Che cos'è Azure AD B2B Collaboration?](active-directory-b2b-what-is-azure-ad-b2b.md)
- [Funzionamento](active-directory-b2b-how-it-works.md)
- [Procedura dettagliata](active-directory-b2b-detailed-walkthrough.md)
- [Riferimento sul formato di file CSV](active-directory-b2b-references-csv-file-format.md)
- [Formato del token per l'utente esterno](active-directory-b2b-references-external-user-token-format.md)
- [Limitazioni correnti della versione di anteprima](active-directory-b2b-current-preview-limitations.md)

<!---HONumber=Nov15_HO1-->