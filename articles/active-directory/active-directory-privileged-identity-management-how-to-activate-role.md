<properties
   pageTitle="Come attivare o disattivare un ruolo | Microsoft Azure"
   description="Informazioni su come attivare i ruoli per le identità con privilegi con l'applicazione Azure Privileged Identity Management."
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
   ms.date="05/19/2016"
   ms.author="kgremban"/>

# Come attivare o disattivare i ruoli in Azure AD Privileged Identity Management

Azure Active Directory (AD) Privileged Identity Management semplifica la gestione da parte delle aziende delle identità con privilegi e dell'accesso alle risorse in Azure AD e in altri Microsoft Online Services, ad esempio Office 365 o Microsoft Intune.

Questo articolo è indirizzato agli amministratori che devono attivare il proprio ruolo in Azure AD Privileged Identity Management (PIM). Verranno illustrati i passaggi per attivare un ruolo quando sono necessarie le autorizzazioni e per disattivarlo al termine.

Se si è stati assegnati a un ruolo amministrativo, è possibile attivare il ruolo quando è necessario eseguire un'attività che richiede tale ruolo. Ad esempio, se un utente deve gestire Office 365 solo saltuariamente, gli amministratori dei ruoli con privilegi dell'organizzazione non devono configurare l'utente come amministratore permanente. L'utente sarà invece candidato al ruolo di amministratore globale o amministratore di Exchange Online in Azure AD. Ciò significa che è possibile richiedere l'assegnazione di un ruolo temporaneo quando sono necessari tali privilegi e si avrà il controllo amministrativo di Office 365 per un periodo di tempo predeterminato.


## Aggiungere l'applicazione Privileged Identity Management

Usare l'applicazione Azure AD Privileged Identity Management nel [portale di Azure](https://portal.azure.com/) per richiedere l'attivazione di un ruolo anche se si userà un altro portale o PowerShell. Se Azure AD Privileged Identity Management non è disponibile nel portale di Azure, seguire questa procedura per iniziare.

1. Se non è già stato eseguito l'accesso, accedere al [portale di Azure](https://portal.azure.com/).
2. Se l'organizzazione ha più directory, fare clic sul proprio nome utente nell'angolo superiore destro del portale di Azure e selezionare la directory da usare.
3. Scegliere l’icona **Nuovo** nel riquadro di spostamento sinistro.
4. Selezionare **Sicurezza e identità** dal menu Crea.
5. Selezionare **Azure AD Privileged Identity Management**.
6. Selezionare **Aggiungi al dashboard** e quindi fare clic sul pulsante **Crea**. Verrà aperta l'applicazione Privileged Identity Management.

## Attivare un ruolo

Quando è necessario un ruolo, è possibile richiedere l'attivazione usando il pulsante **Attiva il mio ruolo** nell'applicazione Azure AD Privileged Identity Management.


1. Accedere al [portale di Azure](https://portal.azure.com/) e selezionare il riquadro Azure AD Privileged Identity Management.
2. Selezionare **Attiva il mio ruolo**. Verrà visualizzato un elenco di ruoli assegnati all'utente.
3. Selezionare il ruolo che si vuole attivare.
4. Selezionare **Attiva**. Verrà visualizzato il pannello **Richiedi attivazione ruolo**.
5. Per alcuni ruoli, ad esempio il ruolo amministratore globale, è necessaria l'autenticazione MFA (Multi-Factor Authentication) per attivare il ruolo. Se non è stata eseguita l'autenticazione MFA al momento dell'accesso, potrebbe essere necessario eseguirla prima di attivare il ruolo.
6. Immettere il motivo della richiesta di attivazione nel campo di testo. L'amministratore dei ruoli con privilegi potrebbe anche richiedere di specificare un numero di ticket.
7. Selezionare **OK**. Il ruolo verrà attivato e la modifica del ruolo sarà visibile in Microsoft Online Services.

## Disattivare un ruolo

Un ruolo attivato si disattiva automaticamente quando viene raggiunto il limite di tempo.

Prima del raggiungimento del limite di tempo, è possibile disattivare un ruolo manualmente nell'applicazione Azure AD Privileged Identity Management. Selezionare **Attiva il mio ruolo**, individuare il ruolo non più necessario e selezionare **Disattiva**.


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Passaggi successivi

Per altre informazioni su Azure AD Privileged Identity Management, vedere i collegamenti seguenti.

[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!---HONumber=AcomDC_0525_2016-->