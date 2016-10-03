<properties 
	pageTitle="Azure Multi-Factor Authentication - Come funziona"
	description="Azure multi-Factor Authentication contribuisce a salvaguardare l'accesso a dati e applicazioni rispondendo alla richiesta degli utenti di poter usare un processo di accesso semplice. Offre ulteriore sicurezza richiedendo una seconda forma di autenticazione nonché un'autenticazione avanzata tramite una gamma di opzioni di verifica semplice."
	services="multi-factor-authentication"
	documentationCenter=""
	authors="kgremban"
	manager="femila"
	editor="curtland"/> 

<tags
	ms.service="multi-factor-authentication"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/04/2016"
	ms.author="kgremban"/>

#Come funziona Azure Multi-Factor Authentication

La sicurezza dell'autenticazione a più fattori si fonda sull'approccio basato su livelli. La manomissione di più fattori rappresenta una sfida significativa per gli autori di attacchi. Tuttavia, anche se un autore di attacco riesce a ottenere la password dell'utente, questa risulta inutile se non è in possesso del dispositivo attendibile. Se l'utente smarrisce il dispositivo, la persona che lo trova non potrà utilizzarlo a meno che non ne conosca anche la password.

![Verifica](./media/multi-factor-authentication-how-it-works/howitworks.png)



Azure multi-Factor Authentication contribuisce a salvaguardare l'accesso a dati e applicazioni rispondendo alla richiesta degli utenti di poter usare un processo di accesso semplice. Offre ulteriore sicurezza richiedendo una seconda forma di autenticazione nonché un'autenticazione avanzata tramite una gamma di opzioni di verifica semplice:

- Chiamata telefonica
- SMS
- Notifica dell'app per dispositivi mobili, che consente agli utenti di scegliere il metodo preferito
- Codice di verifica dell'app per dispositivi mobili
- Token OATH di terze parti

Per ulteriori informazioni su come funziona, vedere il video seguente.

>[AZURE.VIDEO multi-factor-authentication-deep-dive-securing-access-on-premises]

##Metodi disponibili per la multi-factor authentication
Quando un utente accede, una verifica aggiuntiva viene inviata all'utente. Di seguito è riportato un elenco di metodi che possono essere utilizzati per la seconda verifica.

Metodo di verifica | Descrizione
------------- | ------------- |
Chiamata telefonica | Viene effettuata una chiamata allo smartphone dell'utente chiedendogli di verificare che sta effettuando l'accesso premendo il simbolo #. Questa operazione completerà il processo di verifica. Questa opzione è configurabile e può essere modificata con un codice specificato dall'utente.
SMS | Verrà inviato un SMS allo smartphone dell'utente con un codice di 6 cifre. Immettere il codice per completare il processo di verifica.
Notifica dell'app per dispositivi mobili | Una richiesta di verifica verrà inviata allo smartphone dell'utente chiedendogli di completare la verifica selezionando Verifica dall'app per dispositivi mobili. Ciò si verifica se la notifica dell'app è stata selezionata come metodo di verifica principale. Se l'utente riceve la richiesta quando non sta effettuando l'accesso, potrà scegliere di segnalare la frode.
Codice di verifica dell'app per dispositivi mobili | Un codice di verifica verrà inviato all'app per dispositivi mobili in esecuzione sullo smartphone dell'utente. Ciò si verifica se il codice di verifica è stato selezionato come metodo di verifica principale.


##Versioni disponibili di Azure Multi-Factor Authentication
Azure Multi-Factor Authentication è disponibile in tre diverse versioni. Nella tabella seguente ognuna di queste versioni è descritta in modo più dettagliato.

Versione | Descrizione
------------- | ------------- |
Multi-Factor Authentication per Office 365 | Questa versione funziona solo con le applicazioni di Office 365 e viene gestita dal portale di Office 365. Gli amministratori possono quindi proteggere le risorse di Office 365 con multi-factor authentication. Questa versione viene fornita con una sottoscrizione a Office 365.
Multi-Factor Authentication per amministratori di Azure | Lo stesso subset di funzionalità di Multi-Factor Authentication per Office 365 sarà disponibile per tutti gli amministratori di Azure senza costi aggiuntivi. Ogni account amministrativo della sottoscrizione di Azure può ora usufruire di un ulteriore livello di protezione abilitando la funzionalità di multi-factor authentication di base. Ora, un amministratore che desidera accedere al portale di Azure per creare una macchina virtuale, un sito Web, gestire l'archiviazione, i servizi mobili o qualsiasi altro servizio di Azure può aggiungere l'autenticazione a più fattori all'account di amministratore.
Azure Multi-Factor Authentication | Azure multi-Factor Authentication offre la più ampia gamma di funzionalità. <br><br>Fornisce opzioni di configurazione aggiuntive tramite il portale di gestione di Azure, segnalazione avanzata e il supporto per una gamma di applicazioni locali e cloud. Azure Multi-Factor Authentication può essere acquistato come licenza autonoma ed è incluso nel pacchetto di Azure Active Directory Premium ed Enterprise Mobility Suite. <br><br>Può anche essere acquistato su una base di consumo creando un provider di Azure Multi-Factor Authentication in una sottoscrizione di Azure.
##Confronto tra le funzionalità delle versioni
Nella tabella seguente viene fornito un elenco delle funzionalità disponibili nelle varie versioni di Azure Multi-Factor Authentication.


Funzionalità | Multi-Factor Authentication per Office 365 (incluso nelle SKU di Office 365)|Multi-Factor Authentication per amministratori di Azure (incluso con una sottoscrizione Azure) | Azure Multi-Factor Authentication (incluso in Azure AD Premium ed Enterprise Mobility Suite)
------------- | :-------------: |:-------------: |:-------------: |
Gli amministratori possono proteggere gli account tramite Multi-Factor Authentication| * | * (disponibile solo per gli account Administrator di Azure)|*
App per dispositivi mobili come secondo fattore|* | * | *
Chiamata telefonica come secondo fattore|* | * | *
SMS come secondo fattore|* | * | *
Password di app per i client che non supportano MFA|* | * | *
Controllo amministrazione sui metodi di autenticazione| *| *| *
Modalità PIN| | | *
Avviso di illecito| | | *
Report MFA| | | *
Bypass monouso| | | *
Messaggi di saluto personalizzati per le telefonate| | | *
Personalizzazione dell'ID chiamante per le telefonate| | | *
Conferma evento| | | *
IP attendibili| | | *
Sospendere MFA per dispositivi memorizzati (anteprima pubblica)| | | *
SDK MFA| | | *
MFA per applicazioni locali che utilizzano il server MFA| | | *


##Come ottenere Azure Multi-Factor Authentication

Se si desiderano tutte le funzionalità offerte da Azure Multi-Factor Authentication, anziché solo quelle fornite per gli utenti di Office 365 e gli amministratori di Azure, sono disponibili diverse opzioni:

1.	Acquistare le licenze di Azure Multi-Factor Authentication e assegnarle agli utenti.
2.	Acquistare licenze con Azure Multi-Factor Authentication incluso nel pacchetto, ad esempio Azure Active Directory Premium o Enterprise Mobility Suite e assegnarle agli utenti.
3.	Creare un provider Azure Multi-Factor Authentication all'interno di una sottoscrizione di Azure. Se non si ha ancora una sottoscrizione di Azure, è possibile iscriversi per ottenere una sottoscrizione di valutazione. Le sottoscrizioni di valutazione devono essere convertite in sottoscrizioni regolari prima della scadenza.

Quando si usa un provider di Azure Multi-Factor Authentication, sono disponibili due modelli di uso fatturati tramite la sottoscrizione di Azure:


- **Per utente**. In genere, per le aziende che desiderano abilitare la multi-factor authentication per un numero fisso di dipendenti che richiedono regolarmente l'autenticazione.
- **Per autenticazione**. In genere, per le aziende che desiderano abilitare la multi-factor authentication per un gruppo esteso di utenti esterni che non richiedono regolarmente l'autenticazione.

Per ulteriori informazioni sui prezzi, vedere [Prezzi - Multi-Factor Authentication (MFA).](https://azure.microsoft.com/pricing/details/multi-factor-authentication/)

Scegliere il modello per postazione o basato sul consumo più adatto per l'organizzazione. Quindi, per iniziare vedere [Introduzione](multi-factor-authentication-get-started.md)

<!---HONumber=AcomDC_0921_2016-->