<properties
	pageTitle="Azure AD Privileged Identity Management | Microsoft Azure"
	description="Argomento che descrive Azure AD Privileged Identity Management e illustra come usare PIM per migliorare la sicurezza del cloud."
	services="active-directory"
	documentationCenter=""
	authors="kgremban"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/19/2016"
	ms.author="kgremban"/>

# Gestione identità con privilegi di Azure AD

Azure Active Directory (AD) Privileged Identity Management consente di gestire, controllare e monitorare le identità con privilegi e l'accesso alle risorse in Azure AD e in altri Microsoft Online Services, ad esempio Office 365 o Microsoft Intune.

In alcuni casi gli utenti hanno la necessità di eseguire operazioni privilegiate in risorse di Azure o Office 365 o altre app SaaS. Per questa ragione, è spesso necessario che le organizzazioni concedano agli utenti l'accesso con privilegi permanente in Azure AD. Questo rappresenta un rischio di sicurezza crescente per le risorse ospitate nel cloud poiché le organizzazioni non sono in grado di monitorare completamente le operazioni eseguite dagli utenti con i privilegi amministrativi. Inoltre, se un account utente con accesso privilegiato è compromesso, tale singola violazione può compromettere la sicurezza dell'intero cloud. Gestione identità con privilegi di Azure AD consente di risolvere questo rischio.

Azure AD Privileged Identity Management consente di effettuare le operazioni seguenti:

- Individuare gli utenti amministratori di Azure AD
- Abilitare l'accesso come amministratore Just-In-Time su richiesta ai Microsoft Online Services, ad esempio Office 365 e Intune
- Ottenere report sulla cronologia degli accessi degli amministratori e sulle modifiche alle assegnazioni degli amministratori
- Ricevere avvisi relativi all'accesso a un ruolo con privilegi

Azure AD Privileged Identity Management è in grado di gestire i ruoli aziendali predefiniti di Azure Active Directory che includono:

- Amministratore globale
- Amministratore fatturazione
- Amministratore del servizio  
- Amministratore utenti
- Amministratore password

## Accesso amministratore just-in-time

In passato, si poteva assegnare un utente a un ruolo amministratore tramite il portale di gestione di Azure precedente o Windows PowerShell. Di conseguenza, l'utente diventava un **amministratore permanente** per il ruolo, sempre attivo nel ruolo assegnato. Azure AD Privileged Identity Management introduce il concetto di **amministratore temporaneo** per un ruolo, ovvero un utente che deve completare un processo di attivazione per il ruolo assegnato. Il processo di attivazione cambia l'assegnazione dell'utente a un ruolo in Azure AD da inattiva ad attiva per un periodo di tempo specificato, ad esempio otto ore.

## Abilitare Privileged Identity Management per la directory

Per iniziare a usare Azure AD Privileged Identity Management, accedere al [portale di Azure](https://portal.azure.com/). Azure AD Privileged Identity Management non è disponibile nelle versioni precedenti del portale classico.

>[AZURE.NOTE] Per abilitare Azure AD Privileged Identity Management per una directory è necessario essere un amministratore globale con un account aziendale, non un account Microsoft.

1. Accedere al [portale di Azure](https://portal.azure.com/) come amministratore globale della directory.
2. Se l'organizzazione ha più directory, fare clic sul proprio nome utente nell'angolo superiore destro del portale di Azure e selezionare la directory in cui si userà Azure AD Privileged Identity Management.
3. Scegliere l’icona **Nuovo** nel riquadro di spostamento sinistro.
4. Selezionare **Sicurezza e identità**.
5. Selezionare **Azure AD Privileged Identity Management**.
6. Selezionare l'opzione **Aggiungi al dashboard** e quindi fare clic sul pulsante **Crea**. Verrà visualizzato il dashboard Privileged Identity Management

Se si è il primo utente a usare Azure AD Privileged Identity Management nella directory, la [procedura guidata relativa alla sicurezza](active-directory-privileged-identity-management-security-wizard.md) descriverà passo passo la procedura di assegnazione iniziale. Al termine della procedura guidata si diventa automaticamente primo **amministratore della sicurezza** e **amministratore dei ruoli con privilegi** della directory.

Solo l'amministratore dei ruoli con privilegi può accedere all'app PIM (Privileged Identity Management) per gestire l'accesso per gli altri amministratori. È possibile [consentire ad altri utenti di eseguire operazioni di gestione in PIM](active-directory-privileged-identity-management-how-to-give-access-to-pim.md).

## Dashboard di Gestione identità con privilegi

In Azure AD Privileged Identity Management è disponibile un dashboard che visualizza informazioni importanti, tra cui:

- Numero di utenti assegnati a ogni ruolo con privilegi  
- Numero di amministratori permanenti e temporanei
- Cronologia degli accessi di ogni amministratore

![Dashboard PIM - Schermata][2]

## Gestione dei ruoli con privilegi

Gestione identità con privilegi di Azure AD consente di gestire gli amministratori tramite l'aggiunta o la rimozione di amministratori permanenti o temporanei a ogni ruolo.

![Aggiunta/Rimozione di amministratori PIM - Schermata][3]

## Configurare le impostazioni di attivazione del ruolo

L'impostazione di attivazione del ruolo consente di configurare le proprietà di attivazione del ruolo temporaneo, ad esempio:

- Durata del periodo di attivazione del ruolo
- Notifica di attivazione del ruolo
- Informazioni che l'utente deve specificare durante il processo di attivazione del ruolo  

![Impostazioni di PIM - Attivazione dell'amministratore - Schermata][4]

## Attivazione del ruolo  

Per attivare un ruolo, un amministratore temporaneo deve richiedere un'"attivazione" con vincoli di tempo per il ruolo. È possibile richiedere l'attivazione usando l'opzione **Attiva il mio ruolo** in Gestione identità con privilegi di Azure AD.

Un amministratore che vuole attivare un ruolo deve inizializzare Azure AD Privileged Identity Management nel portale di Azure.

Qualsiasi tipo di amministratore può usare Gestione identità con privilegi di Azure AD per attivare il suo ruolo.

L'attivazione del ruolo ha un vincolo di tempo. Nelle impostazioni di attivazione del ruolo è possibile impostare la durata dell'attivazione e le informazioni che l'amministratore dovrà specificare per attivare il ruolo.

![Richiesta di attivazione del ruolo dell'amministratore PIM - Schermata][5]

## Cronologia di attivazione dei ruoli

Azure AD Privileged Identity Management consente anche di rilevare le modifiche nella cronologia di attivazione dei ruoli e delle assegnazioni dei ruoli con privilegi. A tale scopo, usare le opzioni del log di controllo seguenti:

![Cronologia di attivazione PIM - Schermata][6]

## Passaggi successivi
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!--Image references-->

[2]: ./media/active-directory-privileged-identity-management-configure/PIM_Dash.png
[3]: ./media/active-directory-privileged-identity-management-configure/PIM_AddRemove.png
[4]: ./media/active-directory-privileged-identity-management-configure/PIM_RoleActivationSettings.png
[5]: ./media/active-directory-privileged-identity-management-configure/PIM_RequestActivation.png
[6]: ./media/active-directory-privileged-identity-management-configure/PIM_ActivationHistory.png

<!---HONumber=AcomDC_0525_2016-->