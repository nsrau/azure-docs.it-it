<properties
   pageTitle="Come eseguire una verifica della sicurezza | Microsoft Azure"
   description="Informazioni su come aggiungere ruoli alle identità con privilegi con l'estensione Azure Privileged Identity Management."
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
   ms.date="03/17/2016"
   ms.author="kgremban"/>

# Azure AD Privileged Identity Management: Come eseguire una verifica della sicurezza


È molto semplice esaminare l'accesso con privilegi dopo che è stata [avviata la revisione della sicurezza](active-directory-privileged-identity-management-how-to-start-security-review.md).

## Per i revisori: approvazione o rifiuto dell'accesso

### Verifica personale
1. Scegliere **Verifica l'accesso amministrativo** dal menu principale di PIM. Verrà visualizzato un elenco di revisioni della sicurezza.
2. Selezionare nell'elenco gli **utenti** per i quali si desidera modificare l'accesso. NOTA: l'accesso verrà effettivamente modificato. Questo processo prevede semplicemente la creazione di un elenco di controllo per coloro che desiderano modificare l'accesso per il ruolo. Dopo che è stato selezionato almeno un utente, vengono abilitati i pulsanti **Approva accesso** e **Nega accesso**.
3. Fare clic su **Approva accesso** o **Nega accesso** per gli utenti selezionati. Verrà visualizzata una notifica nel menu principale del portale di Azure e l'elenco di revisioni verrà eliminato. Chiudere il pannello **Rivedi ruoli Azure Active Directory**.

### Revisione self-service
1. L'utente riceverà un messaggio di posta elettronica che indica che è necessaria la revisione dell'accesso. Il messaggio di posta elettronica conterrà un collegamento per l'accesso al portale di Azure.
2. Dopo aver avuto accesso al portale, l'utente potrà approvare o negare il proprio accesso facendo clic sui pulsanti **Approva accesso** o **Nega accesso**. Il nome dell'utente verrà rimosso dall'elenco.

## Per i responsabili delle verifiche: gestione delle verifiche della sicurezza

## Completamento o arresto di una verifica
1. Passare al dashboard di PIM.
2. Selezionare la verifica della sicurezza che si vuole completare nell'elenco **Verifiche della sicurezza**. Verrà visualizzato il pannello con i dettagli della verifica della sicurezza.
3. Fare clic su **Interrompi verifica** per completare o arrestare la verifica. La verifica verrà archiviata e il pannello verrà rimosso.

## Esportazione di una verifica
È possibile esportare una verifica per l'uso con Excel o un altro programma che supporta i file CSV.

1. Passare al dashboard di PIM.
2. Fare clic sulla sezione **Verifiche della sicurezzaa** del dashboard. Verrà visualizzato il pannello **Verifiche della sicurezza**.
3. Fare clic sulla verifica della sicurezza che si vuole esportare. Verrà visualizzato il pannello con i dettagli della verifica della sicurezza.
4. Fare clic sul pulsante **Esporta**. Verrà avviato il download di un file CSV.

## Eliminazione di una verifica

> [AZURE.WARNING] Non verrà visualizzato un avviso prima dell'eliminazione. Accertarsi quindi di *volere* effettivamente eliminare la verifica.

1. Tornare al dashboard di PIM.
2. Fare clic sulla sezione **Revisioni sicurezza** del dashboard. Verrà visualizzato il pannello **Revisioni sicurezza**.
3. Fare clic sulla revisione della sicurezza che si desidera eliminare. Verrà visualizzato il pannello con i dettagli della revisione della sicurezza.
4. Fare clic sul pulsante **Elimina**.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Passaggi successivi
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!---HONumber=AcomDC_0323_2016-->