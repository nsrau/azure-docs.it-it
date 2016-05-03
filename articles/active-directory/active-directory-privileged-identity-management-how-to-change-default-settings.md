<properties
   pageTitle="Come gestire le impostazioni di attivazione del ruolo | Microsoft Azure"
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
   ms.date="04/15/2016"
   ms.author="kgremban"/>

# Come gestire le impostazioni di attivazione del ruolo in Azure AD Privileged Identity Management

Un amministratore della sicurezza può personalizzare Azure AD Privileged Identity Management (PIM) nell'organizzazione, ad esempio modificando l'esperienza di un utente che attiva l'assegnazione di un ruolo temporaneo.

## Gestire le impostazioni di attivazione del ruolo

1. Accedere al [portale di Azure](https://portal.azure.com) e selezionare l'app **Azure AD Privileged Identity Management** dal dashboard.
2. Selezionare il ruolo che si vuole gestire dalla tabella ruoli.
3. Fare clic su **Impostazioni**.
4. Impostare la durata di attivazione predefinita in ore regolando il dispositivo di scorrimento o inserendo il numero di ore nel campo di testo. Il valore massimo è 72 ore.
5. Fare clic su **Abilita** o **Disabilita** per specificare se inviare le notifiche sull'attivazione agli amministratori. L'abilitazione delle notifiche può risultare utile per individuare le attività di amministratore non autorizzate.
6. Fare clic su **Abilita** per consentire agli amministratori di immettere informazioni sulla creazione di ticket nella richiesta di attivazione. Queste informazioni possono essere utili in seguito durante il controllo degli accessi del ruolo.
7. Fare clic su **Abilita** o **Disabilita** per specificare se richiedere l'autenticazione a più fattori per una richiesta di attivazione.
8. Fare clic su **Save**.

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

Per ulteriori informazioni sull'utilizzo dell’MFA con PIM, vedere [Come richiedere l’MFA](active-directory-privileged-identity-management-how-to-require-mfa.md).

<!--PLACEHOLDER: Need an explanation of what the temporary Global Administrator setting is for.-->

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Passaggi successivi
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!---HONumber=AcomDC_0420_2016-->