<properties
   pageTitle="Introduzione ad Azure AD Privileged Identity Management | Microsoft Azure"
   description="Informazioni su come gestire le identità con privilegi con l'applicazione Azure Active Directory Privileged Identity Management nel portale di Azure."
   services="active-directory"
   documentationCenter=""
   authors="kgremban"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="04/28/2016"
   ms.author="kgremban"/>

# Introduzione ad Azure AD Privileged Identity Management


Azure Active Directory (AD) Privileged Identity Management consente di gestire, controllare e monitorare le identità con privilegi e l'accesso alle risorse in Azure AD e in altri Microsoft Online Services, ad esempio Office 365 o Microsoft Intune.

Questo articolo descrive come aggiungere l'app Azure AD PIM al dashboard del portale di Azure.

## Aggiungere l'applicazione Privileged Identity Management

Prima di usare Azure AD Privileged Identity Management è necessario aggiungere l'applicazione al dashboard del portale di Azure.

1. Accedere al [portale di Azure](https://portal.azure.com/) come amministratore globale della directory.
2. Se l'organizzazione ha più directory, fare clic sul proprio nome utente nell'angolo superiore destro del portale di Azure e selezionare la directory in cui si userà PIM.
3. Selezionare l'icona **Nuovo** nel riquadro di spostamento sinistro.
4. Selezionare **Sicurezza e identità**.
5. Selezionare **Azure AD Privileged Identity Management**.
6. Selezionare **Aggiungi al dashboard** e quindi fare clic sul pulsante **Crea**. Verrà aperta l'applicazione Privileged Identity Management.


Se si è il primo utente a usare Azure AD Privileged Identity Management nella directory, la [procedura guidata relativa alla sicurezza](active-directory-privileged-identity-management-security-wizard.md) descriverà passo passo la procedura di assegnazione iniziale. Al termine della procedura guidata, si diventa il primo **Amministratore della sicurezza** della directory. Solo l'amministratore della sicurezza può accedere a questa applicazione per gestire l'accesso per gli altri amministratori.

In alternativa, se sono stati assegnati uno o più ruoli da un altro amministratore della sicurezza, sarà possibile scegliere il ruolo da attivare. Se si usa un ruolo di amministratore della sicurezza, è anche possibile scegliere l'opzione **Gestisci le identità**.


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Passaggi successivi

La panoramica di [Azure AD Privileged Identity Management](active-directory-privileged-identity-management-configure.md) include informazioni su come gestire l'accesso amministratore nell'organizzazione.

[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!---HONumber=AcomDC_0504_2016-->