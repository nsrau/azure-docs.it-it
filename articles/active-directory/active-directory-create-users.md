<properties
	pageTitle="Aggiungere nuovi utenti o utenti con account Microsoft in Azure Active Directory | Microsoft Azure"
	description="Illustra come aggiungere nuovi utenti o modificare le informazioni sugli utenti in Azure Active Directory."
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
	ms.date="03/31/2016"
	ms.author="curtand;viviali"/>

# Aggiungere nuovi utenti o utenti con account Microsoft in Azure Active Directory

Aggiungere utenti per popolare la directory. Questo articolo illustra come aggiungere nuovi utenti dell'organizzazione e come aggiungere utenti con account Microsoft. Per altre informazioni sull'aggiunta di utenti da altre directory in Azure Active Directory o l'aggiunta di utenti da società partner, vedere [Aggiungere utenti da altre directory o società partner in Azure Active Directory](active-directory-create-users-external.md). Gli utenti aggiunti non hanno autorizzazioni di amministratore per impostazione predefinita, ma è possibile assegnare loro dei ruoli in qualsiasi momento.

## Aggiungere un utente

1. Accedere al [portale di Azure classico](https://manage.windowsazure.com) con un account di amministratore globale per la directory.
2. Selezionare **Active Directory** e quindi il nome della directory dell'organizzazione.
3. Selezionare la scheda **Utenti** e quindi nella barra dei comandi selezionare **Aggiungi utente**.
4. Nella pagina **Informazioni sull'utente** in **Tipo di utente** selezionare:

	- **Nuovo utente nell'organizzazione**: consente di aggiungere un nuovo account utente nella directory.
	- **Utente con account Microsoft esistente**: consente di aggiungere un account utente Microsoft esistente alla directory, ad esempio un account Outlook.

5. In base al **Tipo di utente** immettere un nome utente, per un nuovo utente, o un indirizzo di posta elettronica, per un utente con un account Microsoft.
6. Nella pagina **Profilo** dell'utente specificare nome e cognome, un nome descrittivo e un ruolo utente dall'elenco **Ruoli**. Per altre informazioni sui ruoli utente e di amministratore, vedere [Assegnazione dei ruoli di amministratore in Azure AD](active-directory-assign-admin-roles.md). Specificare eventualmente **Abilita Multi-Factor Authentication** per l'utente.
7. Nella pagina **Ottieni password temporanea** selezionare **Crea**.

> [AZURE.IMPORTANT] Se l'organizzazione usa più di un dominio, è opportuno essere a conoscenza dei problemi seguenti quando si aggiunge un account utente:
>
> - Per aggiungere account utente con lo stesso nome dell'entità utente (UPN) per tutti i domini, aggiungere **prima**, ad esempio, geoffgrisso@contoso.onmicrosoft.com **seguito** da geoffgrisso@contoso.com.
> - **Non** aggiungere geoffgrisso@contoso.com prima di aggiungere geoffgrisso@contoso.onmicrosoft.com. Quest'ordine è importante e può essere complesso da annullare.

## Modificare le informazioni utente

È possibile modificare tutti gli attributi utente tranne l'ID oggetto.

1. Aprire la directory.
2. Selezionare la scheda **Utenti** e quindi il nome visualizzato dell'utente che si vuole modificare.
3. Salvare le modifiche e quindi fare clic su **Salva**.

Se l'utente che si sta modificando è sincronizzato con il servizio Active Directory locale, non è possibile modificare le informazioni utente con questa procedura. Per modificare l'utente, usare gli strumenti di gestione del servizio Active Directory locale.

## Gestione e limiti dell'utente guest

Gli account guest rappresentano utenti di altre directory che sono stati invitati alla directory per accedere a documenti di SharePoint, applicazioni o altre risorse di Azure. Un account guest nella directory ha l'attributo UserType sottostante impostato su "Guest". Per gli utenti normali, ovvero i membri della directory, l'attributo UserType è impostato su "Membro".

Nella directory gli utenti guest hanno un set di diritti limitato. Questi diritti limitano la possibilità per gli utenti guest di trovare informazioni sugli altri utenti nella directory. Gli utenti guest possono comunque interagire con gli utenti e i gruppi associati alle risorse su cui stanno lavorando. Gli utenti guest possono:

- Visualizzare altri utenti e gruppi associati a una sottoscrizione di Azure a cui sono stati assegnati
- Visualizzare i membri dei gruppi a cui appartengono
- Cercare altri utenti nella directory, se conoscono l'indirizzo di posta elettronica completo dell'utente
- Visualizzare solo un set limitato di attributi degli utenti cercati, ad esempio solo il nome visualizzato, l'indirizzo di posta elettronica, il nome dell'entità utente e la foto di anteprima
- Ottenere un elenco di domini verificati nella directory
- Autorizzare applicazioni, concedendo loro lo stesso accesso disponibile per i membri nella propria directory

## Impostare i criteri di accesso degli utenti guest

La scheda **Configura** di una directory include le opzioni per il controllo dell'accesso per gli utenti guest. Tali opzioni possono essere modificate unicamente da un amministratore globale di directory nel portale di Azure classico. Al momento non è disponibile alcun metodo di PowerShell o API.

Per aprire la scheda **Configura** nel portale di Azure classico, selezionare **Active Directory** e quindi il nome della directory.

![Scheda Configura in Azure Active Directory][1]

Si potranno quindi modificare le opzioni per controllare l'accesso per gli utenti guest.

![opzioni di controllo di accesso per gli utenti guest][2]


## Passaggi successivi

- [Aggiungere utenti da altre directory o società partner in Azure Active Directory](active-directory-create-users-external.md)
- [Amministrazione di Azure AD](active-directory-administer.md)
- [Gestire password in Azure AD](active-directory-manage-passwords.md)
- [Gestire gruppi in Azure AD](active-directory-manage-groups.md)

<!--Image references-->
[1]: ./media/active-directory-create-users/RBACDirConfigTab.png
[2]: ./media/active-directory-create-users/RBACGuestAccessControls.png

<!---HONumber=AcomDC_0504_2016-->