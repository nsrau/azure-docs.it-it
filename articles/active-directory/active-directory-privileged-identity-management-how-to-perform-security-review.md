<properties
   pageTitle="Come eseguire una verifica dell'accesso | Microsoft Azure"
   description="Informazioni su come eseguire una verifica con l'applicazione Azure Privileged Identity Management."
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
   ms.date="07/01/2016"
   ms.author="kgremban"/>

# Come eseguire una verifica dell'accesso in Azure AD Privileged Identity Management

Azure Active Directory (AD) Privileged Identity Management semplifica la gestione da parte delle aziende delle identità con privilegi e dell'accesso alle risorse in Azure AD e in altri Microsoft Online Services, ad esempio Office 365 o Microsoft Intune.

Se si è stati assegnati a un ruolo amministrativo, è possibile che l'amministratore dei ruoli con privilegi dell'organizzazione richieda di eseguire regolarmente una verifica e confermare che il ruolo sia ancora necessario. È possibile che si riceva un messaggio di posta elettronica contenente un collegamento o che si passi direttamente al [portale di Azure](https://portal.azure.com). Per eseguire una verifica automatica dei ruoli assegnati, seguire la procedura descritta in questo articolo.

Per gli amministratori dei ruoli con privilegi interessati alle verifiche della sicurezza sono disponibili altre informazioni in [Come avviare una verifica della sicurezza in Azure AD Privileged Identity Management](active-directory-privileged-identity-management-how-to-start-security-review.md).

## Aggiungere l'applicazione Privileged Identity Management

Per eseguire la verifica, è possibile usare l'applicazione Azure AD Privileged Identity Management (PIM) nel [portale di Azure](https://portal.azure.com/). Se l'applicazione Azure AD Privileged Identity Management non è disponibile nel portale, seguire questa procedura per iniziare.

1. Accedere al [portale di Azure](https://portal.azure.com/).
2. Se l'organizzazione ha più directory, fare clic sul proprio nome utente nell'angolo superiore destro del portale di Azure e selezionare la directory da usare.
3. Selezionare **Nuovo** > **Sicurezza e identità** > **Azure AD Privileged Identity Management**.

	![Abilitare PIM nel portale][1]

4. Selezionare l'opzione **Aggiungi al dashboard** e quindi fare clic sul pulsante **Crea**. Verrà visualizzato il dashboard Privileged Identity Management


## Approvare o negare l'accesso

L'approvazione o la negazione dell'accesso indica semplicemente al revisore se si sta usando ancora il ruolo. Scegliere **Approva** se si vuole restare nel ruolo o **Nega** se l'accesso non è più necessario. Lo stato cambierà solo dopo il revisore avrà applicato i risultati. Seguire questa procedura per trovare e completare la verifica dell'accesso:

1. Nell'applicazione PIM selezionare **Verifica dell'accesso con privilegi**. Eventuali verifiche di accesso in sospeso verranno visualizzate nel pannello Verifiche di accesso di Azure AD.
2. Selezionare la verifica da completare.
3. A meno che non si abbia creato la verifica, si verrà visualizzati come unico utente nella verifica. Selezionare il segno di spunta accanto al nome.
4. Scegliere **Approva** o **Nega**. Potrebbe essere necessario includere un motivo per la decisione nella casella di testo **Specifica il motivo**.
5. Chiudere il pannello **Rivedi ruoli Azure Active Directory**.


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Passaggi successivi
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!--Image references-->

[1]: ./media/active-directory-privileged-identity-management-configure/PIM_EnablePim.png

<!---HONumber=AcomDC_0706_2016-->