<properties
   pageTitle="Come eseguire una verifica della sicurezza | Microsoft Azure"
   description="Informazioni su come eseguire una verifica con l'applicazione Azure Privileged Identity Management."
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

# Come eseguire una verifica della sicurezza in Azure AD Privileged Identity Management

Azure Active Directory (AD) Privileged Identity Management semplifica la gestione da parte delle aziende delle identità con privilegi e dell'accesso alle risorse in Azure AD e in altri Microsoft Online Services, ad esempio Office 365 o Microsoft Intune.

Se si è stati assegnati a un ruolo amministrativo, è possibile che l'amministratore della sicurezza dell'organizzazione richieda di eseguire regolarmente una verifica e confermare che il ruolo è ancora necessario. È possibile che si riceva un messaggio di posta elettronica contenente un collegamento o che si passi direttamente al [portale di Azure](https://portal.azure.com). Per eseguire una verifica automatica dei ruoli assegnati, seguire la procedura descritta in questo articolo.

Per gli amministratori della sicurezza interessati alle verifiche della sicurezza sono disponibili altre informazioni in [Come avviare una verifica della sicurezza](active-directory-privileged-identity-management-how-to-start-security-review.md).

## Aggiungere l'applicazione Privileged Identity Management

Per eseguire la verifica, è possibile usare l'applicazione Azure AD Privileged Identity Management (PIM) nel [portale di Azure](https://portal.azure.com/). Se l'applicazione Azure AD Privileged Identity Management non è disponibile nel portale, seguire questa procedura per iniziare.

1. Se non è già stato eseguito l'accesso, accedere al [portale di Azure](https://portal.azure.com/).
2. Se l'organizzazione ha più directory, fare clic sul proprio nome utente nell'angolo superiore destro del portale di Azure e selezionare la directory da usare.
3. Selezionare l'icona **Nuovo** nel riquadro di spostamento sinistro.
4. Selezionare **Sicurezza e identità** dal menu.
5. Selezionare **Azure AD Privileged Identity Management**.
6. Lasciare selezionata l'opzione **Aggiungi al dashboard** e fare clic sul pulsante **Crea**. Verrà aperta l'applicazione Privileged Identity Management.


## Approvare o negare l'accesso

L'accesso non verrà modificato fino a quando [la verifica non è stata completata](active-directory-privileged-identity-management-how-to-complete-review.md). Questo processo prevede semplicemente la creazione di un elenco di controllo per coloro che desiderano modificare l'accesso per il ruolo. Dopo che è stato selezionato almeno un utente, vengono abilitati i pulsanti **Approva accesso** e **Nega accesso**.

1. Nell'applicazione PIM fare clic su **Verifica l'accesso amministrativo** nel menu principale. Verrà visualizzato un elenco di revisioni della sicurezza.
2. Selezionare nell'elenco gli **utenti** per i quali si desidera modificare l'accesso.
3. Fare clic su **Approva accesso** o **Nega accesso** per gli utenti selezionati. Verrà visualizzata una notifica nel menu principale del portale di Azure e i nomi selezionati non saranno più visualizzati nell'elenco delle verifiche. Sarà possibile visualizzarli nuovamente modificando l'opzione di filtro. Chiudere il pannello **Rivedi ruoli Azure Active Directory**.


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Passaggi successivi
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!---HONumber=AcomDC_0420_2016-->