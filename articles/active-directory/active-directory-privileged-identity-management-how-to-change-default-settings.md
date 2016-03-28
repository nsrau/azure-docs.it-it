<properties
   pageTitle="Azure AD Privileged Identity Management: Come modificare o visualizzare le impostazioni predefinite per un ruolo"
   description="Informazioni su come modificare le impostazioni predefinite per identità con privilegi con l'estensione Azure Active Directory Privileged Identity Management."
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
   ms.date="03/10/2016"
   ms.author="kgremban"/>

# Azure AD Privileged Identity Management: Come modificare o visualizzare le impostazioni di attivazione predefinite per un ruolo

## Modifica e visualizzazione dell'attivazione del ruolo predefinito
1. Dal dashboard, fare clic sul ruolo da configurare dalla tabella dei ruoli.
2. Fare clic su **Impostazioni**.
3. Impostare la durata di attivazione predefinita in ore regolando il dispositivo di scorrimento o inserendo il numero di ore nel campo di testo.
4. Fare clic su **Abilita** o **Disabilita** se si vuole che le notifiche sull'attivazione vengano o non vengano inviate agli amministratori.
5. Fare clic su **Abilita** o **Disabilita** per consentire o non consentire agli amministratori di immettere informazioni sulla creazione di ticket nella loro richiesta di attivazione.
6. Fare clic su **Abilita** o **Disabilita** per richiedere o non richiedere la Multi-Factor Authentication per una richiesta di attivazione.

    Non è possibile disabilitare la Multi-Factor Authentication per ruoli con privilegi elevati per Azure AD e Office365, inclusi i seguenti:

    - Amministratore globale
    - Amministratore account utente
    - Ruolo con autorizzazioni di scrittura nella directory
    - Supporto partner - Livello 1
    - Supporto partner - Livello 2
    - Amministratore fatturazione
    - Amministratore della sicurezza
    - Amministratore di Exchange
    - Amministratore della cassetta postale
    - Amministratore di Skype for Business
    - Amministratore di SharePoint
    - Amministratore di conformità

7. Fare clic su **Abilita** o **Disabilita** per consentire agli amministratori globali di essere temporanei.
8. Fare clic su **Save**.

Per ulteriori informazioni sull'utilizzo dell’MFA con PIM, vedere [Come richiedere l’MFA](active-directory-privileged-identity-management-how-to-require-mfa.md).

<!--PLACEHOLDER: Need an explanation of what the temporary Global Administrator setting is for.-->

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Passaggi successivi
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!---HONumber=AcomDC_0316_2016-->