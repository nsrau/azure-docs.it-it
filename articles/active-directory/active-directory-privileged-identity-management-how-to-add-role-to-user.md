<properties
   pageTitle="Come aggiungere o rimuovere un ruolo utente | Microsoft Azure"
   description="Informazioni su come aggiungere ruoli alle identità con privilegi con l'applicazione Azure Active Directory Privileged Identity Management."
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
   ms.date="04/18/2016"
   ms.author="kgremban"/>

# Azure AD Privileged Identity Management: Come aggiungere o rimuovere un ruolo utente

Con Azure Active Directory, un amministratore globale o un amministratore della società può aggiornare gli utenti assegnati **in modo permanente** ai ruoli in Azure AD. Per questa operazione è necessario usare i cmdlet di PowerShell, ad esempio `Add-MsolRoleMember` e `Remove-MsolRoleMember`. In alternativa, è possibile usare il portale di Azure classico come illustrato in [Assegnazione dei ruoli amministratore in Azure Active Directory](active-directory-assign-admin-roles.md).

Anche l'applicazione Azure AD Privileged Identity Management consente agli amministratori della sicurezza di effettuare assegnazioni di ruolo permanenti. Tuttavia, permette agli amministratori di aggiungere o rimuovere candidati per l'assegnazione **temporanea** a questi ruoli. Un candidato può attivare il ruolo quando serve e le relative autorizzazioni scadono una volta completata l'operazione.

## Gestire i ruoli con PIM nel portale di Azure

All'interno dell'organizzazione è possibile assegnare gli utenti a diversi ruoli amministrativi in Azure AD. Queste assegnazioni di ruolo controllano le attività, ad esempio l'aggiunta o la rimozione di utenti o la modifica delle impostazioni del servizio, che gli utenti possono eseguire in Azure AD, Office 365 e altri servizi e applicazioni Microsoft. Per informazioni dettagliate sui ruoli disponibili, vedere [Ruoli in Azure AD PIM](active-directory-privileged-identity-management-roles.md).

Per aggiungere un utente a un ruolo o rimuovere un utente da un ruolo con Privileged Identity Management, passare al dashboard di PIM e quindi fare clic sul pulsante **Utenti con ruoli di amministratore** oppure selezionare un ruolo specifico, ad esempio Amministratore globale, dalla tabella dei ruoli.

> [AZURE.NOTE] Se ancora non è stato abilitato il servizio PIM nel portale di Azure, vedere [Introduzione ad Azure AD Privileged Identity Management](active-directory-privileged-identity-management-getting-started.md).

Per concedere a un altro utente l'accesso al servizio PIM stesso, vedere [come concedere l'accesso a PIM](active-directory-privileged-identity-management-how-to-give-access-to-pim.md) per informazioni dettagliate sui ruoli utente necessari per l'uso di PIM.

## Aggiungere un utente a un ruolo
Come indicato in precedenza, passare al pannello del ruolo selezionando un ruolo nel dashboard di Azure AD PIM oppure facendo clic sul pulsante **Utenti con ruoli di amministratore**.

1. Fare clic su **Aggiungi**.
  - Se per aprire il pannello è stato selezionato un ruolo utente nella tabella dei ruoli, il ruolo risulterà già selezionato.  
  - In caso contrario, fare clic su **Selezionare un ruolo** e scegliere un ruolo dall'elenco dei ruoli. Ad esempio **Amministratore password**.
2. Cercare l'utente nel pannello **Seleziona utenti**. Se l'utente è incluso nella directory, l'account verrà visualizzato durante la digitazione.
3. Selezionare l'utente nell'elenco dei risultati della ricerca e quindi fare clic su **Fine**.
4. Fare clic su **OK** per salvare la selezione. L'utente selezionato verrà visualizzato nell'elenco e il ruolo sarà temporaneo per impostazione predefinita.

  >[AZURE.NOTE] Se si desidera rendere permanente il ruolo, fare clic sull'utente nell'elenco. Le informazioni relative all'utente verranno visualizzate in un nuovo pannello. Scegliere **Rendi permanente** dal menu delle informazioni dell'utente. Sarà necessario eseguire questa operazione se l'utente non riesce a effettuare la registrazione ad Azure Multi-Factor Authentication (MFA) o se usa un account Microsoft. Gli amministratori temporanei devono effettuare la registrazione a MFA durante l'attivazione.

Ora che all'utente è stato assegnato un ruolo temporaneo, è necessario comunicargli che può attivarlo in base alle istruzioni fornite in [Come attivare o disattivare un ruolo](active-directory-privileged-identity-management-how-to-activate-role.md).

## Rimuovere un utente da un ruolo

È possibile rimuovere gli utenti da assegnazioni di ruoli temporanei, ma è necessario assicurarsi che sia sempre presente almeno un utente con ruolo di amministratore globale permanente.

Per rimuovere un utente specifico da un ruolo, seguire questa procedura:

1. Passare al pannello del ruolo selezionando un ruolo nel dashboard di Azure AD PIM oppure facendo clic sul pulsante **Utenti con ruoli di amministratore**.
2. Selezionare l'utente nell'elenco degli utenti.
3. Fare clic su **Rimuovi**. Verrà visualizzato un messaggio che richiede la conferma.
4. Fare clic su **Sì** per rimuovere il ruolo dall'utente.

Per informazioni sugli utenti a cui è necessario assegnare i ruoli, è possibile [avviare una verifica della sicurezza per il ruolo](active-directory-privileged-identity-management-how-to-start-security-review.md).


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Passaggi successivi
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!---HONumber=AcomDC_0420_2016-->