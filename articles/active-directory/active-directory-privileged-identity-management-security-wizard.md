<properties
   pageTitle="Procedura guidata relativa alla sicurezza di Azure AD Privileged Identity Management"
   description="Quando si usa l'estensione Azure Active Directory Privileged Identity Management per la prima volta, viene visualizzata una procedura guidata sulla sicurezza. Questo articolo descrive i passaggi della procedura guidata."
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

# Procedura guidata relativa alla sicurezza di Azure AD Privileged Identity Management

Se si è il primo utente a eseguire Azure Privileged Identity Management (PIM) per l'organizzazione, viene visualizzata una procedura guidata. La procedura guidata offre informazioni sui rischi di sicurezza delle identità con privilegi e su come usare PIM per ridurre tali rischi. Se si vuole farlo in seguito, non è necessario apportare modifiche alle assegnazioni dei ruoli esistenti nella procedura guidata.

## Cosa aspettarsi

Prima che l'organizzazione inizi a usare PIM, tutte le assegnazioni dei ruoli sono permanenti, ovvero gli utenti sono sempre presenti nei ruoli anche se in quel momento non necessitano dei privilegi del ruolo. Il primo passaggio della procedura guidata visualizza un elenco dei ruoli con privilegi elevati e il numero di utenti attualmente presenti in tali ruoli. È possibile visualizzare i dettagli di un ruolo particolare per visualizzare altre informazioni sugli utenti nel caso in cui uno o più utenti non siano noti.

Il secondo passaggio della procedura guidata offre la possibilità di modificare le assegnazioni dei ruoli di amministratore.

> [AZURE.WARNING] È importante che siano presenti almeno un amministratore globale e più amministratori dei ruoli con privilegi con account aziendali e non account Microsoft. Se è presente un solo amministratore dei ruoli con privilegi, l'organizzazione non sarà in grado di gestire PIM se tale account viene eliminato. Inoltre, non modificare le assegnazioni dei ruoli in temporanee se un utente ha un account Microsoft. Se si prevede di richiedere l'autenticazione MFA per l'attivazione del ruolo, l'utente verrà bloccato.


Dopo aver apportato le modifiche, la procedura guidata non verrà più visualizzata. Al successivo uso di PIM, anche da parte di un altro amministratore dei ruoli con privilegi, verrà visualizzato il dashboard di PIM.

- Se si vuole aggiungere o rimuovere gli utenti dai ruoli o modificare le assegnazioni da permanenti a temporanee, vedere [Azure AD Privileged Identity Management: Come aggiungere o rimuovere un ruolo utente](active-directory-privileged-identity-management-how-to-add-role-to-user.md) per altre informazioni.
- Per concedere a più utenti l'accesso per la gestione di PIM, vedere l'articolo [Come concedere l'accesso per la gestione di Azure AD Privileged Identity Management](active-directory-privileged-identity-management-how-to-give-access-to-pim.md).



## Passaggi successivi
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!---HONumber=AcomDC_0525_2016-->