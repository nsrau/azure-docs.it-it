<properties
   pageTitle="Come concedere l'accesso a PIM | Microsoft Azure"
   description="Informazioni su come aggiungere ruoli agli utenti con l'estensione Azure Active Directory Privileged Identity Management per consentire la gestione di PIM."
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
   ms.date="04/15/2016"
   ms.author="kgremban"/>

# Come concedere l'accesso per la gestione di Azure AD Privileged Identity Management

Il primo utente che abilita Azure AD Privileged Identity Management (PIM) per un'organizzazione deve essere un amministratore globale. Tuttavia, poiché non hanno accesso a PIM per impostazione predefinita, gli altri amministratori globali non possono gestire le assegnazioni temporanee. Per concedere l'accesso a PIM, il primo utente può assegnare gli altri utenti al ruolo di amministratore della sicurezza. Questa assegnazione deve essere eseguita dall'interno PIM e non può essere modificata tramite PowerShell o altri portali.

> [AZURE.NOTE] Per la gestione di Azure AD PIM è necessario Azure Multi-Factor Authentication. Poiché gli account Microsoft non possono effettuare la registrazione per Azure MFA, gli utenti che eseguono l'accesso con un account Microsoft non possono accedere a Azure AD PIM.

Assicurarsi che siano sempre presenti almeno due utenti per il ruolo di amministratore della sicurezza per l'eventualità in cui un utente venga bloccato o ne venga eliminato l'account

## Concedere a un altro utente l'accesso per la gestione di PIM

1. Accedere al [portale di Azure](https://portal.azure.com/) e selezionare l'app **Azure AD Privileged Identity Management** nel dashboard.
2. Selezionare il ruolo **Amministratore della sicurezza**. Verrà visualizzato l'elenco degli utenti attualmente assegnati al ruolo.
3. Fare clic su **Aggiungi**. Verrà visualizzato il pannello della procedura guidata. Il ruolo sarà già selezionato.
4. Fare clic su **Selezionare gli utenti**. Verrà aperto il pannello dell'elenco utenti.
5. Immettere il nome dell'utente nel campo di ricerca. Se l'utente è incluso nella directory, l'account verrà visualizzato durante la digitazione.
6. Selezionare l'utente nell'elenco dei risultati della ricerca e fare clic su **Fine**.
7. Fare clic su **OK** per salvare la selezione. L'utente selezionato verrà visualizzato nell'elenco e il ruolo sarà temporaneo per impostazione predefinita.

  - Se si desidera rendere permanente il ruolo, fare clic sull'utente nell'elenco. Le informazioni relative all'utente verranno visualizzate in un nuovo pannello. Scegliere **Rendi permanente** dal menu delle informazioni dell'utente.

8. Inviare all'utente un collegamento alla documentazione di Azure in [Introduzione a Azure AD Privileged Identity Management](active-directory-privileged-identity-management-getting-started.md).


## Rimuovere i diritti di accesso di un altro utente per la gestione di PIM

Prima di rimuovere un utente dal ruolo di amministratore della sicurezza, assicurarsi che siano sempre presenti due utenti assegnati al ruolo.

1. Nel dashboard di PIM fare clic sul ruolo **Amministratore della sicurezza**. Verrà visualizzato l'elenco degli utenti attualmente assegnati al ruolo.
2. Selezionare l'utente nell'elenco degli utenti.
3. Fare clic su **Rimuovi**. Verrà visualizzato un messaggio di richiesta di conferma.
4. Fare clic su **Sì** per rimuovere il ruolo dall'utente.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Passaggi successivi
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!---HONumber=AcomDC_0420_2016-->