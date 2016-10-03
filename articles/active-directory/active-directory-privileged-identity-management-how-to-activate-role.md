<properties
   pageTitle="Come attivare o disattivare un ruolo | Microsoft Azure"
   description="Informazioni su come attivare i ruoli per le identità con privilegi con l'applicazione Azure Privileged Identity Management."
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
   ms.date="09/16/2016"
   ms.author="kgremban"/>  

# Come attivare o disattivare i ruoli in Azure AD Privileged Identity Management

Azure Active Directory (AD) Privileged Identity Management semplifica la gestione aziendale dell'accesso con privilegi alle risorse in Azure AD e in altri Microsoft Online Services, ad esempio Office 365 o Microsoft Intune.

Se un ruolo amministrativo è stato impostato come idoneo, è possibile attivare il ruolo quando è necessario eseguire un'attività che richiede questo ruolo. Se ad esempio si gestiscono occasionalmente le funzionalità di Office 365, è possibile che gli amministratori dei ruoli con privilegi dell'organizzazione non configurino l'utente come amministratore globale permanente perché tale ruolo interessa anche altri servizi. L'utente è invece considerato idoneo per ruoli di Azure AD, ad esempio amministratore di Exchange Online. È possibile chiedere l'assegnazione temporanea di quel ruolo quando si necessita di questi privilegi; si avrà il controllo amministrativo per un periodo di tempo predeterminato.

Questo articolo è indirizzato agli amministratori che devono attivare il proprio ruolo in Azure AD Privileged Identity Management (PIM). Verranno illustrati i passaggi per attivare un ruolo quando sono necessarie le autorizzazioni e per disattivarlo al termine.


## Aggiungere l'applicazione Privileged Identity Management

Usare l'applicazione Azure AD Privileged Identity Management nel [portale di Azure](https://portal.azure.com/) per richiedere l'attivazione di un ruolo, anche se si intende usare un altro portale o PowerShell. Se Azure AD Privileged Identity Management non è disponibile nel portale di Azure, seguire questa procedura per iniziare.

1. Accedere al [portale di Azure](https://portal.azure.com/).
2. Selezionare il nome utente nell'angolo superiore destro del portale di Azure e quindi selezionare la directory da usare.
3. Selezionare **More services** (Altri servizi) e usare la casella di testo Filtro per cercare **Azure AD Privileged Identity Management**.
4. Selezionare **Aggiungi al dashboard** e quindi fare clic sul pulsante **Crea**. Verrà aperta l'applicazione Privileged Identity Management.

## Attivare un ruolo

Quando è necessario usare un ruolo, è possibile richiedere l'attivazione tramite il pulsante **Attiva il mio ruolo** nell'applicazione Azure AD Privileged Identity Management.


1. Accedere al [portale di Azure](https://portal.azure.com/) e selezionare il riquadro Azure AD Privileged Identity Management.
2. Selezionare **Attiva il mio ruolo**. Verrà visualizzato un elenco di ruoli assegnati all'utente.
3. Selezionare il ruolo che si vuole attivare.
4. Selezionare **Attiva**. Verrà visualizzato il pannello **Richiedi l'attivazione del ruolo**.
5. Alcuni ruoli richiedono l'autenticazione Multi-Factor Authentication (MFA) prima di poter attivare il ruolo. È sufficiente eseguire l'autenticazione una volta per sessione.

    ![Schermata Verifica con MFA prima dell'attivazione del ruolo][2]  

6. Immettere il motivo della richiesta di attivazione nel campo di testo. Alcuni ruoli richiedono di specificare un numero di ticket.
7. Selezionare **OK**. Il ruolo sarà ora attivato e la modifica del ruolo sarà visibile in Microsoft Online Services.

## Disattivare un ruolo

Un ruolo attivato si disattiva automaticamente quando viene raggiunto il limite di tempo.

Prima del raggiungimento del limite di tempo, è possibile disattivare un ruolo manualmente nell'applicazione Azure AD Privileged Identity Management. Selezionare **Attiva il mio ruolo**, scegliere il ruolo che non più necessario usare e selezionare **Disattiva**.


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->  
## Passaggi successivi

Per altre informazioni su Azure AD Privileged Identity Management, vedere i collegamenti seguenti.

[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!--Image references-->  

[1]: ./media/active-directory-privileged-identity-management-configure/PIM_EnablePim.png
[2]: ./media/active-directory-privileged-identity-management-how-to-activate-role/PIM_activation_MFA.png

<!---HONumber=AcomDC_0921_2016-->