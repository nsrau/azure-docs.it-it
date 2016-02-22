<properties
   pageTitle="Anteprima di Azure AD B2B Collaboration: funzionamento | Microsoft Azure"
   description="Descrive come Azure Active Directory B2B Collaboration supporta le relazioni tra società abilitando i partner commerciali ad accedere in modo selettivo alle applicazioni aziendali."
   services="active-directory"
   documentationCenter=""
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
   ms.date="02/03/2016"
   ms.author="viviali"/>

# Anteprima di Collaborazione B2B di Azure AD: funzionamento
Azure AD B2B Collaboration si basa su un modello di invito e riscatto. Si specificano gli indirizzi di posta elettronica delle parti con cui si vuole collaborare, con le applicazioni che dovranno usare. Azure AD invia loro un invito tramite posta elettronica con un collegamento. L'utente partner segue il collegamento e riceve la richiesta di accedere con l'account Azure AD o di ottenere un nuovo account Azure AD.

1. L'amministratore invita gli utenti partner caricando un [file con estensione csv strutturato](active-directory-b2b-references-csv-file-format.md) usando il portale di Azure.
2. Il portale invia i messaggi di posta elettronica a questi utenti partner.
3. Gli utenti partner fanno clic sul collegamento nel messaggio di posta elettronica e ricevono la richiesta di accedere con le credenziali aziendali (se sono già in Azure AD) o di iscriversi come utente di Azure AD B2B Collaboration.
4. Gli utenti partner vengono reindirizzati all'applicazione a cui sono stati invitati e a cui ora hanno accesso.

## Operazioni di directory
Gli utenti partner esistono in Azure AD come utenti esterni. Per questo l'amministratore può effettuare il provisioning delle licenze, assegnare l'appartenenza ai gruppi e concedere l'accesso alle app aziendali con il portale di Azure o con Azure PowerShell proprio come per gli utenti aziendali.

Anche se non è necessaria una sottoscrizione a pagamento ad Azure AD (Basic o Premium) per usare Azure AD B2B, i tenant che hanno una sottoscrizione a pagamento ad Azure AD (Basic o Premium) hanno diritto ai vantaggi aggiuntivi seguenti:

 - Gli amministratori possono assegnare i gruppi alle app, semplificando la gestione dell'accesso degli utenti invitati.
 - La personalizzazione dei tenant amministratore viene usata per personalizzare gli inviti tramite posta elettronica e l'esperienza di riscatto, offrendo maggiore contesto agli utenti partner invitati.

## Articoli correlati
 Vedere gli altri articoli su Azure B2B Collaboration.

 - [Che cos'è Azure AD B2B Collaboration?](active-directory-b2b-what-is-azure-ad-b2b.md)
 - [Procedura dettagliata](active-directory-b2b-detailed-walkthrough.md)
 - [Riferimento sul formato di file CSV](active-directory-b2b-references-csv-file-format.md)
 - [Formato del token per l'utente esterno](active-directory-b2b-references-external-user-token-format.md)
 - [Modifiche agli attributi dell'oggetto utente esterno](active-directory-b2b-references-external-user-object-attribute-changes.md)
 - [Limitazioni correnti della versione di anteprima](active-directory-b2b-current-preview-limitations.md)
 - [Indice di articoli per la gestione di applicazioni in Azure Active Directory](active-directory-apps-index.md)

<!---HONumber=AcomDC_0211_2016-->