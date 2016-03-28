<properties
   pageTitle="Azure AD Privileged Identity Management: Come aggiungere un ruolo a un utente"
   description="Informazioni su come aggiungere ruoli alle identità con privilegi con l'estensione Azure Active Directory Privileged Identity Management."
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
   ms.date="03/08/2016"
   ms.author="kgremban"/>

# Azure AD Privileged Identity Management: Come aggiungere o rimuovere un ruolo utente

## Aggiunta o rimozione di un ruolo utente
È possibile procedere in diversi modi per passare al pannello **Aggiungi utenti gestiti** dell'interfaccia PIM (Privileged Identity Management). Di seguito è elencata la sequenza di clic di ciascun metodo di accesso:

- Dashboard > Utenti in ruoli amministrativi > Aggiungi o rimuovi
- Dashboard > Riepilogo ruoli > Elenco di tutti gli utenti > Aggiungi o rimuovi
- Dashboard > clic sul ruolo utente nella tabella dei ruoli (ad esempio Amministratore globale) > Aggiungi o rimuovi

## Aggiungere un utente a un ruolo
Dopo essere passati al pannello **Aggiungi utenti gestiti**:

1. Fare clic su **Selezionare un ruolo**. Se per accedere è stato selezionato un ruolo utente nella tabella dei ruoli, il ruolo risulterà già selezionato.
2. Selezionare un ruolo dall'elenco dei ruoli, ad esempio **Amministratore password**. Verrà visualizzato il pannello **Seleziona utenti**.
3. Immettere il nome dell'utente nel campo di ricerca. Se l'utente è incluso nella directory, gli account verranno visualizzati durante la digitazione, così come gli altri utenti con nomi simili.
4. Selezionare l'utente nell'elenco e fare clic su **Operazione completata**.
5. Fare clic su **OK** per salvare la selezione. L'utente selezionato verrà visualizzato nell'elenco e il ruolo sarà temporaneo.
6. Se si desidera rendere permanente il ruolo, fare clic sull'utente nell'elenco. Le informazioni relative all'utente verranno visualizzate in un nuovo pannello. Scegliere **Rendi permanente** dal menu delle informazioni dell'utente.

    Sarà necessario eseguire questa operazione se l'utente non riesce a effettuare la registrazione ad Azure Muti-Fator Authentication (MFA) o se usa un account Microsoft. Gli amministratori temporanei devono effettuare la registrazione a MFA durante l'attivazione.

7. Fare clic su **Attiva** per avviare una richiesta di attivazione del ruolo per l'utente. Immettere il motivo per l'attivazione nel campo di testo **Motivo della richiesta**. A questo punto, il ruolo verrà attivato automaticamente per l'utente e verrà inviata una notifica agli amministratori globali.

## Rimuovere un utente da un ruolo
1. Individuare l'utente nell'elenco dei ruoli utente usando uno dei percorsi sopra descritti.
2. Selezionare l'utente nell'elenco degli utenti.
3. Fare clic su **Rimuovi**. Verrà visualizzato un messaggio di richiesta di conferma.
4. Fare clic su **Sì** per rimuovere il ruolo dall'utente.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Passaggi successivi
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!---HONumber=AcomDC_0316_2016-->