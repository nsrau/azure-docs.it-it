<properties
   pageTitle="Azure Active Directory B2B Collaboration | Microsoft Azure"
   description="Azure Active Directory B2B Collaboration consente ai partner aziendali di accedere alle proprie risorse aziendali con ciascuno dei propri utenti rappresentato da un unico account di Azure AD"
   services="active-directory"
   documentationCenter=""
   authors="curtand"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="08/23/2016"
   ms.author="curtand"/>

# Azure Active Directory B2B Collaboration

Collaborazione B2B di Azure Active Directory (Azure AD) consente di abilitare l'accesso alle applicazioni aziendali da identità gestite dai partner. È possibile creare relazioni tra società invitando e autorizzando gli utenti di società partner ad accedere alle proprie risorse. Questo consente di ridurre la complessità perché ogni società esegue la federazione con Azure Active Directory una sola volta e ogni utente è rappresentato da un singolo account Azure AD e di aumentare la sicurezza se i partner commerciale gestiscono gli account in Azure AD perché l'accesso degli utenti che terminano il rapporto di lavoro con le società viene revocato per impedire l'accesso non autorizzato tramite l'appartenenza alle directory interne. Per i partner aziendali che ancora non usano Azure AD, B2B Collaboration offre un'esperienza di iscrizione semplificata per fornire gli account Azure AD ai propri partner aziendali.

-   I partner aziendali usano le proprie credenziali di accesso, evitando così la necessità di dover gestire una directory del partner esterna e di dover rimuovere l'accesso quando gli utenti terminano il rapporto di lavoro con l'organizzazione del partner.

-   L'accesso alle app viene gestito indipendentemente dal ciclo di vita dell'account del partner aziendale. Questo significa, ad esempio, che è possibile revocare l'accesso senza che sia necessario alcun intervento dal parte del reparto IT o del partner aziendale.

## Capabilities

B2B Collaboration semplifica la gestione e migliora la sicurezza dell'accesso dei partner alle risorse aziendali, incluse le app SaaS, come ad esempio Office 365, Salesforce, Servizi di Azure e qualsiasi applicazione in grado di riconoscere attestazioni locale, cloud o per dispositivi mobili. B2B Collaboration consente ai partner di gestire i propri account e alle aziende di applicare criteri di sicurezza all'accesso dei partner.

Azure Active Directory B2B Collaboration è facile da configurare grazie a una procedura di iscrizione semplificata per partner di tutte le dimensioni. Per i partner che non usano il servizio Azure Active Directory è previsto un processo di verifica tramite posta elettronica. È anche facile da gestire perché non prevede directory esterne o configurazioni federative in base al partner.

## Processo di Collaborazione B2B

1. Azure AD B2B Collaboration consente all'amministratore della società di invitare e autorizzare un set di utenti esterni caricando un file CSV (con valori delimitati da virgole) con un massimo di 2000 righe nel portale di B2B Collaboration.

  ![Finestra di dialogo Carica file CSV](./media/active-directory-b2b-collaboration-overview/upload-csv.png)

2. Il portale invierà agli utenti esterni gli inviti tramite posta elettronica.

3. L'utente invitato eseguirà l'accesso a un account aziendale Microsoft esistente (gestito in Azure AD) o creerà un nuovo account aziendale in Azure AD.

4. Dopo aver eseguito l'accesso, l'utente verrà reindirizzato all'app che è stata condivisa.

Gli inviti a indirizzi di posta elettronica personali, ad esempio Gmail o [*comcast.net*](http://comcast.net/) non sono attualmente supportati.

Per altre informazioni sul funzionamento di B2B Collaboration, guardare [questo video](http://aka.ms/aadshowb2b).

## Passaggi successivi
Vedere gli altri articoli su Collaborazione B2B di Azure AD.

- [Che cos'è Azure AD B2B Collaboration?](active-directory-b2b-what-is-azure-ad-b2b.md)
- [Funzionamento](active-directory-b2b-how-it-works.md)
- [Procedura dettagliata](active-directory-b2b-detailed-walkthrough.md)
- [Riferimento sul formato di file CSV](active-directory-b2b-references-csv-file-format.md)
- [Formato del token per l'utente esterno](active-directory-b2b-references-external-user-token-format.md)
- [Modifiche agli attributi dell'oggetto utente esterno](active-directory-b2b-references-external-user-object-attribute-changes.md)
- [Limitazioni correnti della versione di anteprima](active-directory-b2b-current-preview-limitations.md)
- [Indice di articoli per la gestione di applicazioni in Azure Active Directory](active-directory-apps-index.md)

<!---HONumber=AcomDC_0824_2016-->