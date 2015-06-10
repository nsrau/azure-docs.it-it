<properties 
	pageTitle="Gestione identità con privilegi di Azure AD" 
	description="Questo argomento fornisce informazioni sull'estensione Gestione identità con privilegi di Azure AD e spiega come configurarla." 
	services="active-directory" 
	documentationCenter="" 
	authors="Justinha" 
	manager="TerryLan" 
	editor="LisaToft"/>

<tags 
	ms.service="active-directory" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/04/2015" 
	ms.author="Justinha"/>

# Gestione identità con privilegi di Azure AD

Gestione identità con privilegi di Azure AD consente di gestire, controllare e monitorare le identità con privilegi e il relativo accesso alle risorse in Azure AD e in altri Microsoft Online Services, ad esempio Office 365 o Intune.

Per consentire agli utenti di eseguire operazioni con privilegi, spesso, in passato, le organizzazioni hanno dovuto concedere agli utenti accessi con privilegi permanenti in Azure AD, per le risorse di Azure o Office 365 o per altre app SaaS. Per molti clienti questo rappresenta un rischio di sicurezza crescente per le risorse ospitate nel cloud, in quanto non è possibile monitorare completamente ciò che gli utenti fanno con i privilegi amministrativi. Un account utente con accesso privilegiato compromesso comporta un rischio elevato per la sicurezza complessiva del cloud. Gestione identità con privilegi di Azure AD consente di risolvere questo rischio.

Gestione identità con privilegi di Azure AD disponibile in anteprima permette di:

- Individuare gli utenti amministratori di Azure AD
- Abilitare l'accesso amministrativo su richiesta "just-in-time" alle risorse della directory
- Ottenere i report sulla cronologia di accesso degli amministratori e sulle modifiche alle assegnazioni degli amministratori 
- Ricevere avvisi relativi all'accesso a un ruolo con privilegi 

In questa versione di anteprima, Gestione identità con privilegi di Azure AD è in grado di gestire i ruoli aziendali predefiniti di Azure Active Directory:

- Amministratore globale 
- Amministratore fatturazione 
- Amministratore del servizio  
- Amministratore utenti 
- Amministratore password 

## Accesso amministratore just-in-time 

In passato, si poteva assegnare un utente a un ruolo amministrativo tramite il portale di gestione di Azure o Windows PowerShell. Di conseguenza, l'utente diventava un **amministratore permanente**, sempre attivo nel ruolo assegnato. In questa versione di anteprima è stato aggiunto il supporto per un **amministratore temporaneo**, ovvero un utente che deve completare un processo di attivazione per il ruolo assegnato. Il processo di attivazione cambia l'assegnazione di un ruolo all'utente in Azure AD da inattiva ad attiva.

## Abilitazione di Gestione identità con privilegi per la directory

Per iniziare a usare Gestione identità con privilegi di Azure AD, accedere al [portale di gestione di Microsoft Azure](https://portal.azure.com/). Al momento, Gestione identità con privilegi di Azure AD è disponibile solo nel portale di gestione di Microsoft Azure. Per abilitare Gestione identità con privilegi di Azure AD per una directory è necessario essere un amministratore globale.

![][1]

Al termine dell'inizializzazione di questa estensione, l'utente diventerà il primo **Amministratore della sicurezza** della directory. Solo l'amministratore della sicurezza può accedere a questa estensione per gestire l'accesso per gli altri amministratori. Durante l'inizializzazione, verrà aggiunto un riquadro di Gestione identità con privilegi d Azure AD nella schermata iniziale del portale di anteprima di Azure.

## Dashboard di Gestione identità con privilegi 

In Gestione identità con privilegi di Azure AD è disponibile un dashboard che visualizza informazioni importanti, tra cui:

- Numero di utenti assegnati a ogni ruolo con privilegi  
- Numero di amministratori permanenti e temporanei 
- Cronologia di accesso dell'amministratore 

![][2]

## Gestione dei ruoli con privilegi 

Gestione identità con privilegi di Azure AD consente di gestire gli amministratori tramite l'aggiunta o la rimozione di amministratori permanenti o temporanei a ogni ruolo.

![][3]

## Configurare le impostazioni di attivazione del ruolo 

L'impostazione di attivazione del ruolo consente di configurare le proprietà di attivazione del ruolo temporaneo, ad esempio:

- Durata del periodo di attivazione del ruolo
- Notifica di attivazione del ruolo 
- Informazioni che l'utente deve specificare durante il processo di attivazione del ruolo  

![][4]

## Attivazione del ruolo  

Per attivare un ruolo, un amministratore temporaneo deve richiedere un'"attivazione" con vincoli di tempo per il ruolo. È possibile richiedere l'attivazione usando l'opzione **Attiva il mio ruolo** in Gestione identità con privilegi di Azure AD.

Un amministratore che vuole attivare un ruolo deve inizializzare Gestione identità con privilegi di Azure AD nel portale di anteprima di Azure.

Qualsiasi tipo di amministratore può usare Gestione identità con privilegi di Azure AD per attivare il suo ruolo.
 
L'attivazione del ruolo ha un vincolo di tempo. Nelle impostazioni di attivazione del ruolo è possibile configurare la durata dell'attivazione e le informazioni che l'amministratore dovrà fornire per attivare il ruolo.

![][5]

## Cronologia di attivazione dei ruoli

Gestione identità con privilegi di Azure AD consente anche di rilevare le modifiche nella cronologia di attivazione dei ruoli e delle assegnazioni dei ruoli con privilegi. A tale scopo, usare le opzioni del log di controllo seguenti:

![][6]

## Passaggi successivi

[Blog di Microsoft Azure](http://azure.microsoft.com/blog/) [Controllo degli accessi in base al ruolo](role-based-access-control-configure.md)

<!--Image references-->
[1]: ./media/active-directory-privileged-identity-management-configure/Search_PIM.png
[2]: ./media/active-directory-privileged-identity-management-configure/PIM_Dash.png
[3]: ./media/active-directory-privileged-identity-management-configure/PIM_AddRemove.png
[4]: ./media/active-directory-privileged-identity-management-configure/PIM_RoleActivationSettings.png
[5]: ./media/active-directory-privileged-identity-management-configure/PIM_RequestActivation.png
[6]: ./media/active-directory-privileged-identity-management-configure/PIM_ActivationHistory.png

<!---HONumber=58-->