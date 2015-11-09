<properties
   pageTitle="Formato del token utente esterno per l'anteprima di Azure Active Directory B2B Collaboration | Microsoft Azure"
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

# Formato del token utente esterno per l'anteprima di Azure Active Directory (Azure AD) B2B Collaboration
Le attestazioni per un token di Azure AD standard sono descritte nell’articolo [Tipi di attestazioni e di token supportati](active-directory-token-and-claims.md) su azure.microsoft.com.

Le attestazioni che sono diverse per un utente esterno autenticato di B2B Collaboration sono come di seguito indicate<br/> - **OID:** l'ID oggetto dal tenant risorsa<br/> - **TID**: l’ID tenant dal tenant della risorsa<br/> - **Autorità di certificazione**: è il tenant della risorsa<br/> - **IDP**: questo è il tenant home dell'utente<br/> - **AltSecId**: questo è l'ID di protezione alternativo, che risulta opaco per l’utente<br/>

## Articoli correlati
Vedere gli altri articoli sulla collaborazione B2B di Azure.

- [Che cos'è Azure AD B2B Collaboration?](active-directory-b2b-what-is-azure-ad-b2b.md)
- [Funzionamento](active-directory-b2b-how-it-works.md)
- [Procedura dettagliata](active-directory-b2b-detailed-walkthrough.md)
- [Riferimento sul formato di file CSV](active-directory-b2b-references-csv-file-format.md)
- [Modifiche agli attributi dell'oggetto utente esterno](active-directory-b2b-references-external-user-object-attribute-changes.md)
- [Limitazioni correnti della versione di anteprima](active-directory-b2b-current-preview-limitations.md)

<!---HONumber=Nov15_HO1-->