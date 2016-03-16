<properties
	pageTitle="Creare o modificare utenti in Azure Active Directory | Microsoft Azure"
	description="Questo argomento illustra come creare o modificare account utente in Azure Active Directory."
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
	ms.date="03/03/2016"
	ms.author="curtand;viviali"/>

# Creare o modificare utenti in Azure AD

È necessario aggiungere un account alla directory tenant per ogni utente che accederà a un servizio cloud Microsoft. È anche possibile modificare gli account utente oppure eliminarli quando non sono più necessari. Per impostazione predefinita, gli utenti non hanno autorizzazioni di amministratore, ma è possibile scegliere di assegnarle.

## Creare un utente

1. Fare clic su **Active Directory**, quindi selezionare il nome della directory dell'organizzazione.
2. Nella pagina **Utenti** fare clic su **Aggiungi utente**.
3. Nella pagina **Informazioni sull'utente** per **Tipo di utente** selezionare:

	- **Nuovo utente nell'organizzazione**: consente di creare un nuovo account utente nella directory.
	- **Utente con account Microsoft esistente**: consente di aggiungere un account utente Microsoft esistente alla directory. ad esempio un account Outlook.
	- **Utente in un'altra directory di Microsoft Azure AD**: consente di aggiungere alla directory un account utente originato da un'altra directory di Azure AD. Nota: per selezionare un utente nell'altra directory, è necessario essere un membro di tale directory.
	- **Utenti nelle società partner**: consente di invitare e autorizzare gli utenti delle società partner alla propria directory. Vedere [Azure Active Directory B2B Collaboration](active-directory-b2b-what-is-azure-ad-b2b.md).


4. In base all'opzione selezionata, immettere un nome utente, un indirizzo di posta elettronica o caricare un file con estensione csv specificando gli indirizzi di posta elettronica con cui gli utenti eseguiranno l'accesso.
5. Nella pagina **Profilo** dell'utente specificare il nome e il cognome dell'utente, un nome descrittivo e un ruolo dal menu a discesa Ruoli. Per altre informazioni sui ruoli utente e di amministratore, vedere [Assegnazione dei ruoli di amministratore in Azure AD](active-directory-assign-admin-roles.md). Specificare eventualmente **Abilita Multi-Factor Authentication**.
6. Nella pagina **Ottieni password temporanea** fare clic su **Crea**.

Se l'organizzazione usa più di un dominio, è opportuno essere a conoscenza dei problemi seguenti quando si crea un account utente:

- Per creare account utente con lo stesso nome dell'entità utente (UPN) per tutti i domini, creare prima, ad esempio, geoffgrisso@contoso.onmicrosoft.com seguito da geoffgrisso@contoso.com.
- Non è possibile creare geoffgrisso@contoso.com seguito da geoffgrisso@contoso.onmicrosoft.com.

## Modificare un utente

Per modificare un utente nel portale di Azure classico:

1. Fare clic su **Active Directory**, quindi sul nome della directory dell'organizzazione.
2. Nella pagina **Utenti** fare clic sul nome visualizzato dell'utente da modificare.
3. Salvare le modifiche e quindi fare clic su **Salva**.

Se l'utente che si sta tentando di modificare è sincronizzato con il servizio Active Directory locale, verrà visualizzato un messaggio di errore e non sarà possibile modificare l'utente con questa procedura. Per modificare l'utente, usare gli strumenti di gestione del servizio Active Directory locale.

## Reimpostare la password di un utente

1. Fare clic su **Active Directory**, quindi sul nome della directory dell'organizzazione.
2. Nella pagina **Utenti** fare clic sul nome visualizzato dell'utente da modificare.
3. Nella parte inferiore della pagina del portale, fare clic su **Reimposta password**.
4. Nella finestra di dialogo di reimpostazione della password, fare clic su **Reimposta**.
5. Fare clic sul segno di spunta per confermare che la password è stata reimpostata.

## Creare utenti esterni

In Azure AD è anche possibile aggiungere utenti a una directory di Azure AD con un account Microsoft da un'altra directory di Azure AD a cui si appartiene o da società partner tramite il caricamento di un file con estensione csv. Per creare un utente esterno, aggiungere un utente al portale e per **Tipo di utente** selezionare **Utente in un'altra directory di Microsoft Azure AD** o **Utenti nelle società partner**.

Gli utenti di qualsiasi tipo hanno origine da un'altra directory e vengono creati come **utenti esterni**. Gli utenti esterni possono collaborare con utenti che esistono già in una directory usando il rispettivo account singolo, senza dovere creare nuovi account e nuove credenziali. Al momento dell'accesso, gli utenti esterni vengono autenticati dalla propria home directory e l'autenticazione è valida per tutte le altre directory a cui sono stati aggiunti.

## Gestione e limiti dell'utente esterno

Quando si aggiunge un utente da un'altra directory alla propria directory, tale utente viene considerato un utente esterno nella propria directory. Inizialmente, il nome visualizzato e il nome utente vengono copiati dalla "home directory" e applicati all'utente esterno nella propria directory. Da questo momento, tutte le proprietà dell'account dell'utente esterno sono totalmente indipendenti: se si apportano modifiche all'utente nella home directory, ad esempio si modifica il nome dell'utente, si aggiunge una posizione e così via, queste modifiche non vengono propagate all'account dell'utente esterno nella propria directory.

L'unico collegamento tra i due account consiste nel fatto che l'utente esegue sempre l'autenticazione nella home directory o con il proprio account Microsoft. Per questo motivo non viene visualizzata un'opzione per la reimpostazione della password o per abilitare l'autenticazione a più fattori per un utente esterno: attualmente, i criteri di autenticazione per la home directory o l'account Microsoft sono gli unici valutati durante l'accesso dell'utente.

> [AZURE.NOTE]
È comunque possibile disabilitare l'utente esterno nella directory con il conseguente blocco dell'accesso alla directory.

Se un utente viene eliminato nella home directory o se viene annullato il relativo account Microsoft, l'utente esterno continua a esistere nella propria directory. L'utente non può tuttavia accedere alle risorse nella propria directory, perché non può più effettuare l'autenticazione alla propria home directory o all'account Microsoft.

Ecco i servizi che attualmente supportano l'accesso da parte di utenti esterni di Azure AD:

- **Portale di Azure classico**: consente a un utente esterno che è amministratore di più directory di gestire ognuna di queste directory.
- **SharePoint Online**: consente a un utente esterno di accedere alle risorse autorizzate di SharePoint Online se la condivisione esterna è abilitata.
- **Dynamics CRM**: consente a un utente esterno di accedere alle risorse autorizzate in Dynamics CRM se l'utente ha ottenuto una licenza tramite PowerShell.

Ecco le limitazioni note degli utenti esterni di Azure AD:

- Gli utenti esterni che sono amministratori non possono aggiungere utenti da società partner a directory (collaborazione B2B) esterne alla rispettiva home directory.
- Gli utenti esterni non possono autorizzare applicazioni multi-tenant in directory esterne alla propria home directory.
- Visual Studio Online non supporta attualmente l'accesso da parte di utenti esterni*.
- PowerBI non supporta attualmente l'accesso da parte di utenti esterni.
- Il portale Office non supporta la concessione di licenze a utenti esterni.

* Visual Studio Online consente l'accesso da parte di utenti esterni che eseguono l'autenticazione tramite account Microsoft, ma non da parte di utenti esterni che eseguono l'autenticazione tramite account aziendali o dell'istituto di istruzione.

## Gestione e limiti dell'utente guest

**Guest** indica un account utente della directory il cui attributo UserType è impostato su "Guest". Per gli utenti normali questo valore è impostato su "Membro", per indicare che sono membri della directory. Gli utenti guest rappresentano utenti da altre directory che sono stati invitati alla directory per accedere a una risorsa specifica, ad esempio un documento di SharePoint Online, un'applicazione o una risorsa di Azure.

Nella directory gli utenti guest hanno un set di diritti limitato. Questi diritti limitano la capacità degli utenti guest di individuare informazioni su altri utenti nella directory, pur consentendo di interagire con gli utenti e i gruppi associati alle risorse su cui stanno lavorando. Gli utenti guest possono:

- Visualizzare altri utenti e gruppi associati a una sottoscrizione di Azure a cui sono stati assegnati
- Visualizzare i membri dei gruppi a cui appartengono
- Cercare altri utenti nella directory, purché conoscano l'indirizzo di posta elettronica completo dell'utente
- Visualizzare solo un set limitato di attributi degli utenti cercati, ad esempio solo il nome visualizzato, l'indirizzo di posta elettronica, il nome dell'entità utente e la foto di anteprima
- Ottenere un elenco di domini verificati nel tenant
- Autorizzare applicazioni, concedendo loro lo stesso accesso disponibile per i membri nella propria directory

## Configurare i criteri di accesso dell'utente

La scheda **Configura** di una directory include le opzioni per il controllo degli accessi per gli utenti esterni. Tali opzioni possono essere modificate unicamente da un amministratore globale di directory all'interno dell'interfaccia utente (non sono disponibili i metodi Windows PowerShell e API) nel portale di Azure classico. Per aprire la scheda **Configura** nel portale di Azure classico, fare clic su **Active Directory**, quindi selezionare il nome della directory.

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

<!---HONumber=AcomDC_0309_2016-->