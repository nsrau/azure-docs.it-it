<properties
	pageTitle="Aggiungere utenti da altre directory o società partner in Azure Active Directory | Microsoft Azure"
	description="Illustra come aggiungere utenti o modificare le informazioni sugli utenti in Azure Active Directory, inclusi gli utenti esterni e guest."
	services="active-directory"
	documentationCenter=""
	authors="curtand"
	manager="femila"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="08/02/2016"
	ms.author="curtand"/>

# Aggiungere utenti da altre directory o società partner in Azure Active Directory

> [AZURE.SELECTOR]
- [Portale di Azure](active-directory-create-users-external-azure-portal.md)
- [Portale di Azure classico](active-directory-create-users-external.md)

Questo articolo illustra come aggiungere utenti da altre directory in Azure Active Directory o aggiungere utenti da società partner. Per informazioni sull'aggiunta di nuovi utenti nell'organizzazione e di utenti che possiedono account Microsoft, vedere [Aggiungere nuovi utenti o utenti con account Microsoft in Azure Active Directory](active-directory-create-users.md). Gli utenti aggiunti non hanno autorizzazioni di amministratore per impostazione predefinita, ma è possibile assegnare loro dei ruoli in qualsiasi momento.

## Aggiungere un utente

1. Accedere al [portale di Azure classico](https://manage.windowsazure.com) con un account di amministratore globale per la directory.

2. Selezionare **Active Directory** e quindi aprire la directory.

3. Selezionare la scheda **Utenti** e quindi nella barra dei comandi selezionare **Aggiungi utente**.

4. Nella pagina **Informazioni sull'utente** in **Tipo di utente** selezionare:

	- **Utente in un'altra directory di Microsoft Azure AD**: consente di aggiungere alla directory un account utente originato da un'altra directory di Azure AD. È possibile selezionare un utente in un'altra directory solo se si è membri di tale directory.
	- **Utenti nelle società partner**: consente di invitare e autorizzare utenti delle società partner nella propria directory. Vedere [Collaborazione B2B di Azure Active Directory](active-directory-b2b-what-is-azure-ad-b2b.md). È necessario [caricare un file CSV che specifica gli indirizzi di posta elettronica](active-directory-b2b-references-csv-file-format.md).

6. Nella pagina **Profilo** dell'utente specificare nome e cognome, un nome descrittivo e un ruolo utente dall'elenco **Ruoli**. Per altre informazioni sui ruoli utente e di amministratore, vedere [Assegnazione dei ruoli di amministratore in Azure Active Directory](active-directory-assign-admin-roles.md). Specificare eventualmente **Abilita Multi-Factor Authentication** per l'utente.

7. Nella pagina **Ottieni password temporanea** selezionare **Crea**.

> [AZURE.IMPORTANT] Se l'organizzazione usa più di un dominio, è opportuno essere a conoscenza dei problemi seguenti quando si aggiunge un account utente:
>
> - Per aggiungere account utente con lo stesso nome dell'entità utente (UPN) per tutti i domini, aggiungere **prima**, ad esempio, geoffgrisso@contoso.onmicrosoft.com **seguito** da geoffgrisso@contoso.com.
> - **Non** aggiungere geoffgrisso@contoso.com prima di aggiungere geoffgrisso@contoso.onmicrosoft.com. Quest'ordine è importante e può essere complesso da annullare.

Se si modificano le informazioni per un utente la cui identità è sincronizzata con il servizio di Active Directory locale, non sarà possibile modificare le informazioni sull'utente nel portale di Azure classico. Per modificare le informazioni sull'utente, usare gli strumenti di gestione del servizio Active Directory locale.

## Aggiungere utenti esterni

È anche possibile aggiungere utenti da un'altra directory di Azure AD di appartenenza o dalle società partner caricando un file CSV. Per aggiungere un utente esterno, per **Tipo di utente** specificare **Utente in un'altra directory di Microsoft Azure AD** o **Utenti nelle società partner**.

Gli utenti di questi due tipi sono originati da un'altra directory e vengono aggiunti come **utenti esterni**. Gli utenti esterni possono collaborare con altri utenti in una directory senza la necessità di aggiungere nuovi account e credenziali. Al momento dell'accesso, gli utenti esterni si autenticano con la propria home directory e l'autenticazione è valida per tutte le altre directory a cui sono stati aggiunti.

## Gestione e limiti dell'utente esterno

Quando si aggiunge un utente da un'altra directory alla propria directory, tale utente viene considerato un utente esterno nella propria directory. Il nome visualizzato e il nome utente vengono copiati dalla relativa home directory e usati per l'utente esterno nella propria directory. Da questo momento, le proprietà dell'account utente esterno sono completamente indipendenti. Le eventuali modifiche apportate alle proprietà dell'utente nella relativa home directory non vengono propagate all'account utente esterno nella propria directory.

L'unico collegamento tra i due account consiste nel fatto che l'utente esegue sempre l'autenticazione nella propria home directory o con il proprio account Microsoft. Per questo motivo, per gli utenti esterni non sono disponibili opzioni per reimpostare o per abilitare l'autenticazione a più fattori. Attualmente, i criteri di autenticazione della home directory o dell'account Microsoft sono gli unici criteri che vengono valutati quando l'utente esegue l'accesso.

> [AZURE.NOTE]
È comunque possibile disabilitare l'utente esterno nella directory, con il conseguente blocco dell'accesso alla propria directory.

Se un utente viene eliminato nella home directory o se viene annullato il relativo account Microsoft, l'utente esterno continua a esistere nella propria directory, ma non può accedere alle risorse nella directory, perché non è più autorizzato a effettuare l'autenticazione con la home directory o l'account Microsoft.

### Servizi che attualmente supportano l'accesso da parte di utenti esterni di Azure AD

- **Portale di Azure classico**: consente a un utente esterno con il ruolo di amministratore di più directory di gestire ognuna di esse.
- **SharePoint Online**: se la condivisione esterna è abilitata, consente a un utente esterno di accedere alle risorse autorizzate di SharePoint Online.
- **Dynamics CRM**: se all'utente è stata concessa una licenza tramite PowerShell, consente a un utente esterno di accedere alle risorse autorizzate di Dynamics CRM.
- **Dynamics AX**: se all'utente è stata concessa una licenza tramite PowerShell, consente a un utente esterno di accedere alle risorse autorizzate di Dynamics AX. Le limitazioni per gli [utenti esterni di Azure AD](#known-limitations-of-azure-ad-external-users) e gli [utenti guest](#guest-user-management-and-limitations) si applicano anche agli utenti esterni di Dynamics AX.

### Limitazioni note per gli utenti esterni di Azure AD

- Gli utenti esterni che sono amministratori non possono aggiungere utenti da società partner a directory (collaborazione B2B) esterne alla relativa home directory.
- Gli utenti esterni non possono autorizzare applicazioni multi-tenant in directory esterne alla relativa home directory.
- PowerBI al momento non supporta l'accesso da parte di utenti esterni
- Il portale di Office non supporta la concessione di licenze a utenti esterni
- Per quanto riguarda Azure AD PowerShell, gli utenti esterni vengono registrati nella rispettiva home directory e non possono gestire directory in cui sono utenti esterni


## Passaggi successivi

- [Aggiungere o modificare utenti in Azure Active Directory](active-directory-create-users.md)
- [Amministrazione di Azure AD](active-directory-administer.md)
- [Gestire password in Azure AD](active-directory-manage-passwords.md)
- [Gestire gruppi in Azure AD](active-directory-manage-groups.md)

<!---HONumber=AcomDC_0914_2016-->