<properties
	pageTitle="Creare o modificare utenti in Azure AD"
	description="Argomento che descrive come creare o modificare account utente in Azure AD."
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
	ms.topic="article"
	ms.date="09/21/2015"
	ms.author="curtand"/>

# Creare o modificare utenti in Azure AD

È necessario creare un account per ogni utente che accederà a un servizio cloud Microsoft. È anche possibile modificare gli account utente oppure eliminarli quando non sono più necessari. Per impostazione predefinita, gli utenti non hanno autorizzazioni di amministratore, ma è possibile scegliere facoltativamente di assegnarle.

## Creare un utente

1. Fare clic su **Active Directory**, quindi sul nome della directory dell'organizzazione.
2. Nella pagina **Utenti** fare clic su **Aggiungi utente**.
3. Nella pagina **Informazioni sull'utente** per **Tipo di utente** selezionare:
	1. **Nuovo utente nell'organizzazione**: indica che si vuole creare un nuovo account utente da gestire nella directory.
	2. **Utente con account Microsoft esistente**: indica che si vuole aggiungere un account Microsoft esistente alla directory per collaborare su risorse di Azure con un coamministratore che accede ad Azure con un account Microsoft.
	3. **Utente in un'altra directory di Microsoft Azure AD**: indica che si vuole aggiungere alla directory un account utente originato da un'altra directory di Azure AD. Per selezionare un utente dall'altra directory, è necessario esserne membro.
4. A seconda dell'opzione selezionata, digitare un nome utente o un nome dell'account Microsoft con il quale l'utente eseguirà l'accesso.
5. Nella pagina **Profilo** dell'utente specificare il nome e il cognome dell'utente, un nome descrittivo e un ruolo dal menu a discesa Ruoli. Per altre informazioni sui ruoli utente e di amministratore, vedere [Assegnazione dei ruoli di amministratore in Azure AD](active-directory-assign-admin-roles.md). Specificare eventualmente **Abilita Multi-Factor Authentication**.
6. Nella pagina **Ottieni password temporanea** fare clic su **Crea**.

Se l'organizzazione usa più di un dominio, è opportuno essere a conoscenza dei problemi seguenti quando si crea un account utente:

- Per creare account utente con lo stesso nome dell'entità utente (UPN) per tutti i domini, creare prima, ad esempio, geoffgrisso@contoso.onmicrosoft.com seguito da geoffgrisso@contoso.com.
- Non è possibile creare geoffgrisso@contoso.com seguito da geoffgrisso@contoso.onmicrosoft.com.

## Modificare un utente

Se l'utente che si sta tentando di modificare è sincronizzato con il servizio Active Directory locale, verrà visualizzato un messaggio di errore e non sarà possibile modificare l'utente con questa procedura. Per modificare l'utente, usare gli strumenti di gestione del servizio Active Directory locale.

Per modificare un utente nel portale di gestione di Azure:

1. Fare clic su **Active Directory**, quindi sul nome della directory dell'organizzazione.
2. Nella pagina **Utenti** fare clic sul nome visualizzato dell'utente da modificare.
3. Salvare le modifiche e quindi fare clic su **Salva**.

## Reimpostare la password di un utente

1. Fare clic su **Active Directory**, quindi sul nome della directory dell'organizzazione.
2. Nella pagina **Utenti** fare clic sul nome visualizzato dell'utente da modificare.
3. Nella parte inferiore della pagina del portale, fare clic su **Reimposta password**.
4. Nella finestra di dialogo di reimpostazione della password, fare clic su **Reimposta**.
5. Fare clic sul segno di spunta per confermare che la password è stata reimpostata.

## Creare un utente esterno

In Azure AD si possono anche aggiungere utenti a una directory di Azure AD da un'altra directory di Azure AD o da un utente con un account Microsoft. Un utente può essere membro di un massimo di 20 directory.

Gli utenti aggiunti da un'altra directory sono utenti esterni. Gli utenti esterni possono collaborare con utenti già presenti in una directory, ad esempio in un ambiente di test, senza richiedere che eseguano l'accesso con nuovi account e credenziali. Al momento dell'accesso, gli utenti esterni vengono autenticati dalla propria home directory e l'autenticazione è valida per tutte le altre directory di cui sono membri.

Per creare un utente esterno, accedere al portale e per **Tipo di utente** selezionare **Utente in un'altra directory di Microsoft Azure AD**.

## Gestione e limiti dell'utente esterno

L'utente aggiunto da una directory esistente a una nuova viene considerato un utente esterno. Inizialmente, il nome visualizzato e il nome utente vengono copiati dalla "home directory" e applicati all'utente esterno nell'altra directory. Da questo momento, tutte le proprietà dell'oggetto utente esterno sono totalmente indipendenti: se si apportano modifiche all'utente nella home directory, ad esempio si modifica il nome dell'utente, si aggiunge una posizione e così via, queste modifiche non vengono propagate all'account utente esterno nell'altra directory.

L'unico collegamento tra i due oggetti consiste nel fatto che l'utente effettua sempre l'autenticazione nella home directory o con il proprio account Microsoft. Per questo motivo non viene visualizzata un'opzione per la reimpostazione della password o per abilitare Multi-Factor Authentication per un account utente esterno: attualmente, i criteri di autenticazione per la home directory o l'account Microsoft sono gli unici valutati durante l'accesso dell'utente.

> [AZURE.NOTE]È comunque possibile disabilitare l'utente esterno nella directory con il conseguente blocco dell'accesso alla directory.

Se un utente viene eliminato nella home directory o se viene annullato il relativo account Microsoft, l'utente esterno continua a esistere nella directory. L'utente non può tuttavia accedere alle risorse nella directory, perché non può più effettuare l'autenticazione alla propria home directory o all'account Microsoft.

Un utente amministratore di più directory può gestire ciascuna directory nel portale di gestione di Azure. Tuttavia, altre applicazioni come Office 365 non offrono attualmente la possibilità di assegnare i servizi e di accedervi come utente esterno di un'altra directory. In futuro verrà fornito agli sviluppatori materiale sussidiario con indicazioni su come usare le proprie app con gli utenti membri di più directory.

Esistono attualmente delle limitazioni, perché un amministratore può concedere il consenso solo a un'applicazione multi-tenant nella home directory e può eseguire il provisioning solo per le app SaaS e SSO tramite il pannello di accesso della home directory. Gli utenti di account Microsoft hanno le stesse limitazioni perché non possono concedere il consenso a un'applicazione multi-tenant o usare il pannello di accesso.

## Utenti guest

**Guest** indica un utente della directory il cui valore in Tipo utente è impostato su "Guest". Per gli utenti normali questo valore è impostato su "Membro", per indicare che sono membri della directory. Gli utenti guest vengono creati quando si condivide una risorsa con utenti esterni alla directory, ad esempio quando si aggiunge un account Microsoft alla sottoscrizione di Azure o si condivide un documento in SharePoint con un utente esterno.

Nella directory gli utenti guest hanno un set di diritti limitato. Questi diritti limitano la capacità degli utenti guest di individuare informazioni su altri utenti nella directory, pur consentendo di interagire con gli utenti e i gruppi associati alle risorse su cui stanno lavorando. Ad esempio, un utente guest assegnato a una sottoscrizione di Azure potrà visualizzare altri utenti e gruppi associati a tale sottoscrizione. Gli utenti guest possono anche individuare altri utenti nella directory a cui deve essere consentito l'accesso alla sottoscrizione, purché conoscano l'indirizzo di posta elettronica completo dell'utente. Un utente guest può visualizzare solo un set limitato di proprietà relative ad altri utenti, ad esempio nome visualizzato, indirizzo di posta elettronica, nome dell'entità utente (UPN) e foto di anteprima.

## Configurare i criteri di accesso dell'utente

La scheda **Configura** di una directory include le opzioni per il controllo degli accessi per gli utenti esterni. Tali opzioni possono essere modificate unicamente da un amministratore globale di directory all'interno dell'interfaccia utente (non sono disponibili i metodi Windows PowerShell e API) del portale di Azure completo. Per aprire la scheda **Configura** nel portale di Azure, fare clic su **Active Directory**, quindi selezionare il nome della directory.

![][1]

In seguito è possibile modificare le opzioni di controllo degli accessi per gli utenti esterni.

![][2]

Per impostazione predefinita, gli ospiti non possono enumerare i contenuti della directory. Pertanto non possono visualizzare gli utenti e i gruppi presenti nell'area **Elenco membri**. Possono cercare un utente digitandone l'indirizzo e-mail completo e, in seguito, concedere l'accesso. Segue una descrizione delle restrizioni predefinite per gli ospiti:

- Non possono enumerare gli utenti e i gruppi nella directory.
- Possono visualizzare solo alcuni dati di un utente, se ne conoscono l'indirizzo e-mail.
- Possono visualizzare solo alcuni dati di un gruppo, se ne conoscono il nome.

La possibilità per gli ospiti di visualizzare dettagli limitati di un utente o gruppo consente loro di invitare altre persone e visualizzare alcuni dettagli relativi alle persone con cui collaborano.

## Passaggi successivi

- [Amministrazione di Azure AD](active-directory-administer.md)
- [Gestire password in Azure AD](active-directory-manage-passwords.md)
- [Gestire gruppi in Azure AD](active-directory-manage-groups.md)

<!--Image references-->
[1]: ./media/active-directory-create-users/RBACDirConfigTab.png
[2]: ./media/active-directory-create-users/RBACGuestAccessControls.png

<!---HONumber=Oct15_HO3-->