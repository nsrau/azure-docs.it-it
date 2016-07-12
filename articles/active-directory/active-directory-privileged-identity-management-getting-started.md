<properties
   pageTitle="Introduzione ad Azure AD Privileged Identity Management | Microsoft Azure"
   description="Informazioni su come gestire le identità con privilegi con l'applicazione Azure Active Directory Privileged Identity Management nel portale di Azure."
   services="active-directory"
   documentationCenter=""
   authors="kgremban"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="06/29/2016"
   ms.author="kgremban"/>

# Introduzione ad Azure AD Privileged Identity Management


Azure Active Directory (AD) Privileged Identity Management consente di gestire, controllare e monitorare le identità con privilegi e l'accesso alle risorse in Azure AD e in altri Microsoft Online Services, ad esempio Office 365 o Microsoft Intune.

Questo articolo descrive come aggiungere l'app Azure AD PIM al dashboard del portale di Azure.

## Aggiungere l'applicazione Privileged Identity Management

Prima di usare Azure AD Privileged Identity Management è necessario aggiungere l'applicazione al dashboard del portale di Azure.

1. Accedere al [portale di Azure](https://portal.azure.com/) come amministratore globale della directory.
2. Se l'organizzazione ha più directory, fare clic sul proprio nome utente nell'angolo superiore destro del portale di Azure e selezionare la directory in cui si userà PIM.
3. Selezionare **Nuovo** > **Sicurezza e identità** > **Azure AD Privileged Identity Management**.

    ![Abilitare PIM nel portale][1]

4. Selezionare **Aggiungi al dashboard** e quindi fare clic su **Crea**. Verrà aperta l'applicazione Privileged Identity Management.


Se si è il primo utente a usare Azure AD Privileged Identity Management nella directory, la [procedura guidata relativa alla sicurezza](active-directory-privileged-identity-management-security-wizard.md) descriverà passo passo la procedura di assegnazione iniziale. Al termine della procedura guidata si diventa automaticamente primo **Amministratore della sicurezza** e **Amministratore del ruolo con privilegi** della directory. Solo un amministratore del ruolo con privilegi può accedere a questa applicazione per gestire l'accesso per gli altri amministratori.

Se sono stati assegnati uno o più ruoli, sarà possibile scegliere **Attiva i ruoli personali**. Se si è Amministratore del ruolo con privilegi,sarà anche disponibile un'opzione **Gestione dei ruoli con privilegi**.


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Passaggi successivi

La [panoramica di Azure AD Privileged Identity Management](active-directory-privileged-identity-management-configure.md) include altre informazioni su come gestire l'accesso amministrativo nell'organizzazione.

[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!--Image references-->

[1]: ./media/active-directory-privileged-identity-management-configure/PIM_EnablePim.png

<!---HONumber=AcomDC_0706_2016-->