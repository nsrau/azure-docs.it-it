<properties
   pageTitle="Come gestire le impostazioni di attivazione del ruolo | Microsoft Azure"
   description="Informazioni su come modificare le impostazioni predefinite per identità con privilegi con l'estensione Azure Active Directory Privileged Identity Management."
   services="active-directory"
   documentationCenter=""
   authors="kgremban"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="06/30/2016"
   ms.author="kgremban"/>

# Come gestire le impostazioni di attivazione del ruolo in Azure AD Privileged Identity Management

Un amministratore dei ruoli con privilegi può personalizzare Azure AD Privileged Identity Management (PIM) nell'organizzazione, ad esempio modificando l'esperienza di un utente che attiva l'assegnazione di idoneo al ruolo.

## Gestire le impostazioni di attivazione del ruolo

1. Accedere al [portale di Azure](https://portal.azure.com) e selezionare l'app **Azure AD Privileged Identity Management** dal dashboard.
2. Selezionare **Gestione dei ruoli con privilegi** > **Impostazioni** > **Ruoli con privilegi**.
3. Selezionare il ruolo del quale gestire le impostazioni.

La pagina Impostazioni per ogni ruolo contiene numerose impostazioni che è possibile configurare. Queste impostazioni incidono solo sugli utenti che sono amministratori idonei, non sugli amministratori permanenti.

**Attivazioni**: tempo in ore per cui un ruolo rimane attivo prima della scadenza. Questo valore può essere compreso tra 1 e 72 ore.

**Notifiche**: è possibile scegliere se il sistema invia messaggi di posta elettronica agli amministratori per confermare che hanno attivato un ruolo. Questa opzione può essere utile per il rilevamento di attivazioni non autorizzate o dannose.

**Ticket di evento imprevisto/richiesta**: è possibile scegliere se si vuole richiedere che gli amministratori idonei includano un numero di ticket quando attivano il proprio ruolo. Questa opzione può essere utile quando si eseguono i controlli di accesso dei ruoli.

**Multi-Factor Authentication**: è possibile scegliere se richiedere agli utenti di verificare la propria identità con MFA prima di attivare i ruoli. La verifica è necessaria solo una volta per ogni sessione, non ogni volta che si attiva un ruolo. Tenere presente due suggerimenti quando si abilita l'autenticazione MFA:

- Gli utenti che dispongono di account Microsoft per i relativi indirizzi di posta elettronica (in genere @outlook.com) non possono eseguire la registrazione per Azure MFA. Se si vuole assegnare ruoli agli utenti con account Microsoft, renderli amministratori permanenti o disabilitare l'autenticazione MFA per il ruolo.

- Non è possibile disabilitare l'autenticazione MFA per i ruoli con privilegi elevati per Azure AD e Office365. Si tratta di una funzionalità di sicurezza poiché è necessaria una protezione elevata per questi ruoli:

    - Amministratore di applicazioni
    - Amministratore server proxy applicazione
    - Amministratore fatturazione
    - Amministratore di conformità
    - Amministratore del servizio CRM
    - Responsabile approvazione per l'accesso a Customer Lockbox
    - Ruolo con autorizzazioni di scrittura nella directory
    - Amministratore di Exchange
    - Amministratore globale
    - Amministratore del servizio Intune
    - Amministratore della cassetta postale
    - Supporto partner - Livello 1
    - Supporto partner - Livello 2
    - Amministratore dei ruoli con privilegi
    - Amministratore della sicurezza
    - Amministratore di SharePoint
    - Amministratore di Skype for Business
    - Amministratore account utente

Per ulteriori informazioni sull'utilizzo dell’MFA con PIM, vedere [Come richiedere l’MFA](active-directory-privileged-identity-management-how-to-require-mfa.md).

<!--PLACEHOLDER: Need an explanation of what the temporary Global Administrator setting is for.-->

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Passaggi successivi
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!---HONumber=AcomDC_0907_2016-->