<properties
   pageTitle="Azure Privileged Identity Management: Come aggiungere un ruolo a un utente"
   description="Informazioni su come aggiungere ruoli alle identità con privilegi con l'estensione Azure Privileged Identity Management."
   services="active-directory"
   documentationCenter=""
   authors="IHenkel"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="na"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="08/31/2015"
   ms.author="inhenk"/>

# Azure Privileged Identity Management: Come aggiungere o rimuovere un ruolo utente

## Aggiunta o rimozione di un ruolo utente
È possibile procedere in diversi modi per passare al pannello **Aggiungi utenti gestiti** dell'interfaccia PIM. Di seguito è elencata la sequenza di clic di ciascun metodo di accesso:

## Percorsi per aggiungere o rimuovere un ruolo per un utente
- Dashboard > Utenti in ruoli amministrativi > Aggiungi o rimuovi
- Dashboard > Riepilogo ruoli > Elenco di tutti gli utenti > Aggiungi o rimuovi
- Dashboard > clic sul ruolo utente nella tabella dei ruoli (ad esempio Amministratore globale) > Aggiungi o rimuovi

## Aggiungere un ruolo a un utente dalla sezione Utenti in ruoli amministrativi o da Riepilogo ruoli - Elenco di tutti gli utenti
Dopo essere passati al pannello **Aggiungi utenti gestiti**...

1. Fare clic su **Selezionare un ruolo**. Se per accedere è stato selezionato un ruolo utente nella tabella dei ruoli, il ruolo risulterà già selezionato.
2. Selezionare un ruolo dall'elenco dei ruoli, ad esempio **Amministratore password**. Verrà visualizzato il pannello **Seleziona utenti**.
3. Immettere il nome dell'utente a cui si desidera aggiungere il ruolo nel campo di ricerca. Se l'utente è incluso nella directory, gli account verranno visualizzati durante la digitazione, così come gli altri utenti con nomi simili.
4. Selezionare l'utente nell'elenco e fare clic su **Operazione completata**.
5. Fare clic su **OK** per salvare la selezione. Se non si esegue quest'ultima operazione, la selezione non verrà salvata. L'utente selezionato verrà visualizzato nell'elenco e il ruolo sarà temporaneo.
6. Se si desidera rendere permanente il ruolo, fare clic sull'utente nell'elenco. Le informazioni relative all'utente verranno visualizzate in un nuovo pannello. Scegliere **Rendi permanente** dal menu delle informazioni dell'utente.
7. Fare clic su **Attiva** per avviare una richiesta di attivazione del ruolo per l'utente. Immettere il motivo per l'attivazione nel campo di testo **Motivo della richiesta**. A questo punto, il ruolo verrà attivato automaticamente per l'utente e verrà inviata una notifica agli amministratori globali.

## Rimuovere un utente da un ruolo
1. Individuare l'utente nell'elenco dei ruoli utente usando uno dei percorsi sopra descritti.
2. Selezionare l'utente nell'elenco degli utenti.
3. Fare clic su **Rimuovi**. Verrà visualizzato un messaggio di richiesta di conferma.
4. Fare clic su Sì per rimuovere il ruolo dall'utente.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Passaggi successivi
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!---HONumber=Oct15_HO3-->