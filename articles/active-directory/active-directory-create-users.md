<properties
	pageTitle="Aggiungere utenti o modificare le informazioni utente in Azure Active Directory | Microsoft Azure"
	description="Illustra come aggiungere utenti o modificare le informazioni utente in Azure Active Directory, inclusi gli utenti esterni e guest."
	services="active-directory"
	documentationCenter=""
	authors="curtand"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="03/24/2016"
	ms.author="curtand;viviali"/>

# Aggiungere o modificare utenti in Azure Active Directory

È necessario aggiungere un account alla directory del tenant per ogni utente che accederà a un servizio cloud Microsoft. Gli utenti aggiunti non hanno autorizzazioni di amministratore per impostazione predefinita, ma è possibile assegnare loro dei ruoli in qualsiasi momento.

## Aggiungere un utente

1. Accedere al [portale di Azure classico](https://manage.windowsazure.com) con un account amministratore globale per la directory.
2. Selezionare **Active Directory** e quindi selezionare il nome della directory dell'organizzazione.
3. Selezionare la scheda **Utenti** e quindi nell'area di comando selezionare **Aggiungi utente**.
4. Nella pagina **Informazioni sull'utente** in **Tipo di utente** selezionare:

	- **Nuovo utente nell'organizzazione**: consente di aggiungere un nuovo account utente nella directory.
	- **Utente con account Microsoft esistente**: consente di aggiungere un account utente Microsoft esistente alla directory, ad esempio un account Outlook.
	- **Utente in un'altra directory di Microsoft Azure AD**: consente di aggiungere alla directory un account utente originato da un'altra directory di Azure AD. Nota: per selezionare un utente nell'altra directory, è necessario essere un membro di tale directory.
	- **Utenti nelle società partner**: consente di invitare e autorizzare gli utenti delle società partner alla propria directory. Vedere [Collaborazione B2B di Azure Active Directory](active-directory-b2b-what-is-azure-ad-b2b.md).


5. In base al tipo di utente selezionato, immettere un nome utente, un indirizzo di posta elettronica o caricare un file CSV specificando gli indirizzi di posta elettronica con cui gli utenti eseguiranno l'accesso.
6. Nella pagina **Profilo** dell'utente specificare il nome e il cognome dell'utente, un nome descrittivo e un ruolo dall'elenco **Ruoli**. Per altre informazioni sui ruoli utente e di amministratore, vedere [Assegnazione dei ruoli di amministratore in Azure AD](active-directory-assign-admin-roles.md). Specificare eventualmente **Abilita Multi-Factor Authentication**.
7. Nella pagina **Ottieni password temporanea** selezionare **Crea**.

> [AZURE.IMPORTANT] Se l'organizzazione usa più di un dominio, è opportuno essere a conoscenza dei problemi seguenti quando si aggiunge un account utente:
>
> - Per aggiungere account utente con lo stesso nome dell'entità utente (UPN) per tutti i domini, aggiungere prima, ad esempio, geoffgrisso@contoso.onmicrosoft.com seguito da geoffgrisso@contoso.com.
> - Non si deve aggiungere geoffgrisso@contoso.com prima di aggiungere geoffgrisso@contoso.onmicrosoft.com. Quest'ordine è estremamente importante e può essere complesso da annullare.

## Modificare le informazioni utente

È possibile modificare tutti gli attributi utente tranne l'ID oggetto dell'utente.

1. Aprire la directory dell'organizzazione.
2. Selezionare la scheda **Utenti** e quindi selezionare il nome visualizzato dell'utente che si vuole modificare.
3. Salvare le modifiche e quindi fare clic su **Salva**.

Se l'utente che si sta provando a modificare è sincronizzato con il servizio Active Directory locale, verrà visualizzato un messaggio di errore e non sarà possibile modificare le informazioni utente con questa procedura. Per modificare l'utente, usare gli strumenti di gestione del servizio Active Directory locale.

## Reimpostare la password di un utente

1. Aprire la directory dell'organizzazione.
2. Selezionare la scheda **Utenti** e quindi selezionare il nome visualizzato dell'utente che si vuole modificare.
3. Nell'area di comando selezionare **Reimposta Password**.
4. Nella finestra di dialogo di reimpostazione della password, fare clic su **Reimposta**.
5. Selezionare il segno di spunta per completare la reimpostazione della password.

## Aggiungere utenti esterni

In Azure AD è anche possibile aggiungere utenti a una directory di Azure AD con un account Microsoft da un'altra directory di Azure AD a cui si appartiene o da società partner tramite il caricamento di un file con estensione csv. Per aggiungere un utente esterno, aggiungere un utente al portale e per **Tipo di utente** selezionare **Utente in un'altra directory di Microsoft Azure AD** o **Utenti nelle società partner**.

Gli utenti di qualsiasi tipo hanno origine da un'altra directory e vengono aggiunti come **utenti esterni**. Gli utenti esterni possono collaborare con utenti che esistono già in una directory usando il rispettivo account singolo, senza dovere aggiungere nuovi account e nuove credenziali. Al momento dell'accesso, gli utenti esterni vengono autenticati dalla propria home directory e l'autenticazione è valida per tutte le altre directory a cui sono stati aggiunti.

## Gestione e limiti dell'utente esterno

Quando si aggiunge un utente da un'altra directory alla propria directory, tale utente viene considerato un utente esterno nella propria directory. Inizialmente, il nome visualizzato e il nome utente vengono copiati dalla "home directory" e applicati all'utente esterno nella propria directory. Da questo momento, tutte le proprietà dell'account dell'utente esterno sono totalmente indipendenti: se si apportano modifiche all'utente nella home directory, ad esempio si modifica il nome dell'utente, si aggiunge una posizione e così via, queste modifiche non vengono propagate all'account dell'utente esterno nella propria directory.

L'unico collegamento tra i due account consiste nel fatto che l'utente esegue sempre l'autenticazione nella home directory o con il proprio account Microsoft. Per questo motivo non viene visualizzata un'opzione per la reimpostazione della password o per abilitare l'autenticazione a più fattori per un utente esterno: attualmente, i criteri di autenticazione per la home directory o l'account Microsoft sono gli unici valutati durante l'accesso dell'utente.

> [AZURE.NOTE]
È comunque possibile disabilitare l'utente esterno nella directory con il conseguente blocco dell'accesso alla directory.

Se un utente viene eliminato nella home directory o se viene annullato il relativo account Microsoft, l'utente esterno continua a esistere nella propria directory. L'utente non può tuttavia accedere alle risorse nella directory, perché non può più effettuare l'autenticazione con la home directory o l'account Microsoft.

Ecco i servizi che attualmente supportano l'accesso da parte di utenti esterni di Azure AD:

- **Portale di Azure classico**: consente a un utente esterno che è amministratore di più directory di gestire ognuna di queste directory.
- **SharePoint Online**: consente a un utente esterno di accedere alle risorse autorizzate di SharePoint Online se la condivisione esterna è abilitata.
- **Dynamics CRM**: consente a un utente esterno di accedere alle risorse autorizzate in Dynamics CRM se l'utente ha ottenuto una licenza con PowerShell.

Ecco le limitazioni note degli utenti esterni di Azure AD:

- Gli utenti esterni che sono amministratori non possono aggiungere utenti da società partner a directory (collaborazione B2B) esterne alla rispettiva home directory.
- Gli utenti esterni non possono autorizzare applicazioni multi-tenant in directory esterne alla propria home directory.
- Visual Studio Online non supporta attualmente l'accesso da parte di utenti esterni*.
- PowerBI non supporta attualmente l'accesso da parte di utenti esterni.
- Il portale Office non supporta la concessione di licenze a utenti esterni.

* Visual Studio Online consente l'accesso da parte di utenti esterni che eseguono l'autenticazione tramite account Microsoft, ma non da parte di utenti esterni che eseguono l'autenticazione tramite account aziendali o dell'istituto di istruzione.

## Gestione e limiti dell'utente guest

Gli account guest rappresentano utenti da altre directory che sono stati invitati alla directory per accedere a una risorsa specifica, ad esempio un documento di SharePoint Online, un'applicazione o una risorsa di Azure. Un account guest nella directory ha l'attributo UserType sottostante impostato su "Guest". Gli utenti normali (ovvero i membri della directory) hanno un attributo UserType "Membro".

Nella directory gli utenti guest hanno un set di diritti limitato. Questi diritti limitano la capacità degli utenti guest di individuare informazioni su altri utenti nella directory, pur consentendo di interagire con gli utenti e i gruppi associati alle risorse su cui stanno lavorando. Gli utenti guest possono:

- Visualizzare altri utenti e gruppi associati a una sottoscrizione di Azure a cui sono stati assegnati
- Visualizzare i membri dei gruppi a cui appartengono
- Cercare altri utenti nella directory, se conoscono l'indirizzo di posta elettronica completo dell'utente
- Visualizzare solo un set limitato di attributi degli utenti cercati, ad esempio solo il nome visualizzato, l'indirizzo di posta elettronica, il nome dell'entità utente e la foto di anteprima
- Ottenere un elenco di domini verificati nella directory del tenant
- Autorizzare applicazioni, concedendo loro lo stesso accesso disponibile per i membri nella propria directory

## Impostare i criteri di accesso dell'utente

La scheda **Configura** di una directory include le opzioni per il controllo degli accessi per gli utenti esterni. Tali opzioni possono essere modificate unicamente da un amministratore globale di directory nel portale di Azure classico (non sono disponibili i metodi Windows PowerShell e API).

Per aprire la scheda **Configura** nel portale di Azure classico, selezionare **Active Directory** e quindi selezionare il nome della directory.

![Scheda Configura in Azure Active Directory][1]

In seguito è possibile modificare le opzioni di controllo degli accessi per gli utenti esterni.

![][2]


## Passaggi successivi

- [Amministrazione di Azure AD](active-directory-administer.md)
- [Gestire password in Azure AD](active-directory-manage-passwords.md)
- [Gestire gruppi in Azure AD](active-directory-manage-groups.md)

<!--Image references-->
[1]: ./media/active-directory-create-users/RBACDirConfigTab.png
[2]: ./media/active-directory-create-users/RBACGuestAccessControls.png

<!---HONumber=AcomDC_0330_2016-->