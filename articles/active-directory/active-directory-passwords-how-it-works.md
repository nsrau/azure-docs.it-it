<properties 
	pageTitle="Come funziona: gestione delle password di Azure AD | Microsoft Azure" 
	description="Informazioni sui diversi componenti della gestione delle password in Azure AD, ad esempio dove gli utenti registrano, reimpostano e modificano le password e dove gli amministratori configurano, creano report e abilitano la gestione delle password di Active Directory locale." 
	services="active-directory" 
	documentationCenter="" 
	authors="asteen" 
	manager="kbrint" 
	editor="billmath"/>

<tags 
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/08/2015" 
	ms.author="asteen"/>

# Funzionamento della gestione delle password
La gestione delle password in Azure Active Directory è costituita da più componenti logici descritti di seguito. Fare clic su ogni collegamento, per altre informazioni sul componente specifico.

- [**Portale di configurazione per la gestione delle password**](#password-management-configuration-portal). Gli amministratori possono controllare diversi aspetti della gestione delle password nei relativi tenant passando alla scheda Configura della propria directory nel [portale di gestione di Azure](https://manage.windowsazure.com).
- [**Portale di registrazione utente**](#user-registration-portal). Gli utenti possono effettuare la registrazione automatica per la reimpostazione della password tramite questo portale Web.
- [**Portale di reimpostazione della password utente**](#user-password-reset-portal). Gli utenti possono reimpostare la propria password usando una quantità di test diversi in conformità ai criteri di reimpostazione della password controllati dall'amministratore.
- [**Portale di modifica della password utente**](#user-password-change-portal). Gli utenti possono modificare la propria password in qualsiasi momento immettendo la vecchia password e selezionandone una nuova tramite questo portale Web.
- [** Report di gestione delle password**](#password-management-reports). Gli amministratori possono visualizzare e analizzare le attività di registrazione e di reimpostazione delle password nel tenant passando alla sezione "Report attività" della scheda "Report" della propria directory nel [portale di gestione di Azure](https://manage.windowsazure.com)
- [**Componente di writeback delle password di Azure AD Connect**](#password-writeback-component-of-azure-ad-connect). Gli amministratori possono scegliere di abilitare la funzionalità Writeback password durante l'installazione di Azure AD Connect per abilitare la gestione di password utente federate o sincronizzate dal cloud.

## Portale di configurazione per la gestione delle password
È possibile configurare i criteri di gestione delle password per una directory specifica tramite il [portale di gestione di Azure](https://manage.windowsazure.com) passando alla sezione **Criteri di reimpostazione password utente** nella scheda **Configura** della directory. In questa pagina di configurazione è possibile controllare numerosi aspetti della gestione delle password nell'organizzazione, tra cui:

- Abilitazione e disabilitazione della reimpostazione della password per tutti gli utenti di una directory.
- Impostazione del numero di test (uno o due) che un utente deve superare per reimpostare la password.
- Impostazione dei tipi specifici di test da abilitare per gli utenti dell'organizzazione tra quelli elencati di seguito:
 - Cellulare (codice di verifica tramite SMS o chiamata vocale)
 - Telefono ufficio (chiamata vocale)
 - Indirizzo di posta elettronica alternativo (codice di verifica ricevuto tramite posta elettronica)
 - Domande di sicurezza (autenticazione basata sulle informazioni)
- Impostazione del numero di domande che un utente deve registrare per usare il metodo autenticazione basato sulle domande di sicurezza (visibile solo se le domande di sicurezza sono abilitate).
- Impostazione del numero di domande che un utente deve specificare durante la reimpostazione per usare il metodo autenticazione basato sulle domande di sicurezza (visibile solo se le domande di sicurezza sono abilitate).
- Definizione delle domande di sicurezza personalizzate che un utente può scegliere per registrarsi per l'uso del metodo di autenticazione basato sulle domande di sicurezza (visibile solo se sono abilitate le domande di sicurezza).
- Obbligo per gli utenti di effettuare la registrazione per la reimpostazione della password quando accedono al pannello di accesso dell'applicazione all'indirizzo [http://myapps.microsoft.com](http://myapps.microsoft.com).
- Obbligo per gli utenti di riconfermare i dati registrati in precedenza dopo un numero configurabile di giorni trascorsi (visibili solo se è abilitata la registrazione applicata)
- Specifica di un indirizzo di posta elettronica o di un URL del supporto tecnico personalizzato che verrà visualizzato agli utenti nel caso in cui non riescano a reimpostare la password
- Abilitazione o disabilitazione del writeback delle password (quando il writeback è stato distribuito mediante AAD Connect)
- Visualizzazione dello stato dell'agente di writeback delle password (quando il writeback delle password è stato distribuito mediante AAD Connect)
- Abilitazione di notifiche di posta elettronica agli utenti quando la propria password è stata reimpostata (presente nella sezione **Notifiche** del [ portale di gestione di Azure](https://manage.windowsazure.com))
- Abilitazione di notifiche di posta elettronica agli amministratori quando altri amministratori reimpostano la propria password (presente nella sezione **Notifiche** del [ portale di gestione di Azure](https://manage.windowsazure.com))
- Personalizzazione del portale di reimpostazione della password dell'utente e dei messaggi di posta elettronica di reimpostazione della password con il logo e il nome dell'organizzazione mediante la funzionalità di personalizzazione del tenant (presente nella sezione **Proprietà directory** del [portale di gestione di Azure](https://manage.windowsazure.com)

Per altre informazioni sulla configurazione della gestione delle password nell'organizzazione, vedere [Introduzione alla gestione delle password](active-directory-passwords-getting-started.md).

##Portale di registrazione utente
Prima che gli utenti possano reimpostare la password, è necessario aggiornare gli account utente cloud con i dati di autenticazione corretti, per consentire che possano superare il numero appropriato di test di reimpostazione della password definiti dall'amministratore. Gli amministratori possono inoltre definire le informazioni di autenticazione per conto dell'utente tramite i portali Web di Azure o Office, DirSync/Azure AD Connect o Windows PowerShell.

Se tuttavia si preferisce che gli utenti registrino i propri dati, è disponibile anche una pagina Web in cui gli utenti possono fornire tali informazioni. Questa pagina consente agli utenti di specificare le informazioni di autenticazione in conformità con i criteri di reimpostazione della password abilitati nella propria organizzazione. Al termine della verifica, i dati vengono archiviati nell'account utente cloud e possono essere usati per il ripristino dell'account in un secondo momento. Ecco come appare il portale di registrazione:

  ![][001]

Per altre informazioni, vedere [Introduzione alla gestione delle password](active-directory-passwords-getting-started.md) e [Procedure consigliate per la gestione delle password](active-directory-passwords-best-practices.md).

##Portale di reimpostazione della password utente
Dopo aver abilitato la reimpostazione delle password self-service, aver configurato i relativi criteri per l'organizzazione e aver verificato che nella directory siano inseriti i dati di contatto appropriati degli utenti, questi potranno reimpostare automaticamente la password da qualsiasi pagina Web alla quale si accede con un account aziendale o dell'istituto di istruzione, ad esempio [portal.microsoftonline.com](https://portal.microsoftonline.com). In tali pagine verrà visualizzato un collegamento **Problemi di accesso all'account?**.

  ![][002]

Facendo clic su questo collegamento verrà avviato il portale di reimpostazione delle password self-service.

  ![][003]

Per altre informazioni su come gli utenti possono reimpostare le proprie password, vedere [Introduzione alla gestione delle password](active-directory-passwords-getting-started.md).

##Portale di modifica della password utente
Se gli utenti vogliono modificare la password, possono usare il portale di modifica delle password in qualsiasi momento. Gli utenti possono accedere al portale di modifica delle password tramite la pagina Profilo riquadro di accesso facendo clic sul collegamento "Cambia password" dall'interno delle applicazioni di Office 365. Quando le password scadono, agli utenti verrà chiesto di modificarle automaticamente all'accesso.

  ![][004]

In entrambi i casi, se è stato abilitato il writeback delle password e l'utente è federato o con password sincronizzata, le password modificate vengono scritte in Active Directory locale. Ecco come appare il portale di modifica delle password:

  ![][005]

Per altre informazioni su come gli utenti possono modificare le password di Active Directory locale, vedere [Introduzione alla gestione delle password](active-directory-passwords-getting-started.md).

##Report di gestione delle password
Passando alla scheda **Report** e cercando nella sezione the **Log attività** saranno visualizzati due report di gestione delle password: **Attività di reimpostazione password** e **Attività di registrazione reimpostazione password**. Usando questi due report, è possibile ottenere una visualizzazione degli utenti registrati per la reimpostazione della password o che usano questo servizio nell'organizzazione. Ecco come appaiono i report nel [portale di gestione di Azure](https://manage.windowsazure.com):

  ![][006]

Per altre informazioni, vedere [Come ottenere informazioni dettagliate con i report di gestione delle password](active-directory-passwords-get-insights.md).

##Componente di writeback delle password di Azure AD Connect
Se le password degli utenti dell'organizzazione hanno origine dall'ambiente locale (tramite la federazione o la sincronizzazione delle password), è possibile installare la versione più recente di Azure AD Connect per abilitare l'aggiornamento delle password direttamente dal cloud. Ciò significa che quando gli utenti dimenticano o vogliono modificare la password di AD, possono farlo direttamente dal Web. Ecco dove trovare Writeback password nell'installazione guidata di Azure AD Connect:

  ![][007]

Per altre informazioni su Azure AD Connect, vedere [Introduzione ad Azure AD Connect](active-directory-aadconnect.md). Per altre informazioni sulla funzionalità Writeback password, vedere [Introduzione alla gestione delle password](active-directory-passwords-getting-started.md).


<br/> <br/> <br/>

**Risorse aggiuntive**


* [Informazioni sulla gestione delle password](active-directory-passwords.md)
* [Introduzione alla gestione delle password](active-directory-passwords-getting-started.md)
* [Personalizzare la gestione delle password](active-directory-passwords-customize.md)
* [Procedure consigliate per la gestione delle password](active-directory-passwords-best-practices.md)
* [Come ottenere informazioni dettagliate con i report di gestione delle password](active-directory-passwords-get-insights.md)
* [Domande frequenti sulla gestione delle password](active-directory-passwords-faq.md)
* [Risolvere i problemi relativi alla gestione delle password](active-directory-passwords-troubleshoot.md)
* [Altre informazioni](active-directory-passwords-learn-more.md)
* [Gestione delle password in MSDN](https://msdn.microsoft.com/library/azure/dn510386.aspx)



[001]: ./media/active-directory-passwords-how-it-works/001.jpg "Image_001.jpg"
[002]: ./media/active-directory-passwords-how-it-works/002.jpg "Image_002.jpg"
[003]: ./media/active-directory-passwords-how-it-works/003.jpg "Image_003.jpg"
[004]: ./media/active-directory-passwords-how-it-works/004.jpg "Image_004.jpg"
[005]: ./media/active-directory-passwords-how-it-works/005.jpg "Image_005.jpg"
[006]: ./media/active-directory-passwords-how-it-works/006.jpg "Image_006.jpg"
[007]: ./media/active-directory-passwords-how-it-works/007.jpg "Image_007.jpg"
 

<!---HONumber=July15_HO3-->