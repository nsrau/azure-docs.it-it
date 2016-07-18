<properties
	pageTitle="Azure AD Privileged Identity Management | Microsoft Azure"
	description="Argomento che descrive Azure AD Privileged Identity Management e illustra come usare PIM per migliorare la sicurezza del cloud."
	services="active-directory"
	documentationCenter=""
	authors="kgremban"
	manager="femila"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/01/2016"
	ms.author="kgremban"/>

# Gestione identità con privilegi di Azure AD

Con Azure Active Directory (AD) Privileged Identity Management è possibile gestire, controllare e monitorare l'accesso alle risorse in Azure AD e in altri Microsoft Online Services, ad esempio Office 365 o Microsoft Intune.

Le organizzazioni desiderano ridurre al minimo il numero di utenti che hanno accesso a informazioni o risorse protette per ridurre le probabilità che un utente malintenzionato acceda al sistema. Tuttavia, gli utenti devono comunque eseguire operazioni con privilegi nelle app Azure, Office 365 o SaaS. Poiché il lavoro deve essere svolto, le organizzazioni assegnano agli utenti l'accesso con privilegi in Azure AD senza monitorare le attività svolte con i privilegi amministrativi. Gestione identità con privilegi di Azure AD consente di risolvere questo rischio.

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

In passato, si poteva assegnare un utente a un ruolo amministrativo tramite il portale di gestione di Azure o Windows PowerShell. Di conseguenza, l'utente diventava un **amministratore permanente**, sempre attivo nel ruolo assegnato. Azure AD Privileged Identity Management introduce il concetto di **amministratore idoneo**. Gli amministratori idonei devono essere utenti che necessitano dell'accesso con privilegi in modo occasionale, non con cadenza quotidiana. Il ruolo è inattivo fino a quando l'utente che necessita dell'accesso non completa un processo di attivazione e diventa amministratore attivo per un periodo di tempo predeterminato.

## Abilitare Privileged Identity Management per la directory

È possibile iniziare a usare Azure AD Privileged Identity Management nel [portale di Azure](https://portal.azure.com/).

>[AZURE.NOTE] Per abilitare Azure AD Privileged Identity Management per una directory è necessario essere un amministratore globale con un account aziendale (come @yourdomain.com) e non con un account Microsoft, come @outlook.com.

1. Accedere al [portale di Azure](https://portal.azure.com/) come amministratore globale della directory.
2. Se l'organizzazione ha più directory, fare clic sul proprio nome utente nell'angolo superiore destro del portale di Azure e selezionare la directory in cui si userà Azure AD Privileged Identity Management.
3. Selezionare **Nuovo** > **Sicurezza e identità** > **Azure AD Privileged Identity Management**.

	![Abilitare PIM nel portale][1]

4. Selezionare l'opzione **Aggiungi al dashboard** e quindi fare clic sul pulsante **Crea**. Verrà visualizzato il dashboard Privileged Identity Management

Se si è il primo utente a usare Azure AD Privileged Identity Management nella directory, la [procedura guidata relativa alla sicurezza](active-directory-privileged-identity-management-security-wizard.md) descriverà passo passo la procedura di assegnazione iniziale. Al termine della procedura guidata si diventa automaticamente primo **amministratore della sicurezza** e **amministratore dei ruoli con privilegi** della directory.

Solo l'amministratore dei ruoli con privilegi può gestire l'accesso per gli altri amministratori. È possibile [consentire ad altri utenti di eseguire operazioni di gestione in PIM](active-directory-privileged-identity-management-how-to-give-access-to-pim.md).

## Dashboard di Gestione identità con privilegi

In Azure AD Privileged Identity Management è disponibile un dashboard che visualizza informazioni importanti, tra cui:

- Avvisi che segnalano opportunità di miglioramento della sicurezza
- Numero di utenti assegnati a ogni ruolo con privilegi
- Numero di amministratori permanenti e idonei
- Verifiche di accesso in corso

![Dashboard PIM - Schermata][2]

## Gestione dei ruoli con privilegi

Con Azure AD Privileged Identity Management è possibile gestire gli amministratori tramite l'aggiunta o la rimozione di amministratori permanenti o idonei a ogni ruolo.

![Aggiunta/Rimozione di amministratori PIM - Schermata][3]

## Configurare le impostazioni di attivazione del ruolo

L'impostazione di attivazione del ruolo consente di configurare le proprietà di attivazione del ruolo idoneo, ad esempio:

- Durata del periodo di attivazione del ruolo
- Notifica di attivazione del ruolo
- Informazioni che l'utente deve specificare durante il processo di attivazione del ruolo

![Impostazioni di PIM - Attivazione dell'amministratore - Schermata][4]

Nell'immagine notare che i pulsanti per **Multi-Factor Authentication** sono disabilitati. Per determinati ruoli con privilegi elevati, è necessaria l'autenticazione MFA per una protezione avanzata.

## Attivazione del ruolo  

Per attivare un ruolo, un amministratore idoneo deve richiedere un'"attivazione" con vincoli di tempo per il ruolo. È possibile richiedere l'attivazione usando l'opzione **Attiva il mio ruolo** in Gestione identità con privilegi di Azure AD.

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

[1]: ./media/active-directory-privileged-identity-management-configure/PIM_EnablePim.png
[2]: ./media/active-directory-privileged-identity-management-configure/PIM_Dash.png
[3]: ./media/active-directory-privileged-identity-management-configure/PIM_AddRemove.png
[4]: ./media/active-directory-privileged-identity-management-configure/PIM_RoleActivationSettings.png
[5]: ./media/active-directory-privileged-identity-management-configure/PIM_RequestActivation.png
[6]: ./media/active-directory-privileged-identity-management-configure/PIM_ActivationHistory.png

<!---HONumber=AcomDC_0706_2016-->