<properties
	pageTitle="Informazioni su Azure Multi-Factor Authentication | Microsoft Azure"
	description="Questo argomento spiega che cos'è Multi-Factor Authentication e perché usare questo servizio, fornisce inoltre informazioni sul client di Multi-Factor Authentication e sui diversi metodi e versioni disponibili. Azure Multi-Factor Authentication è un metodo di verifica dell'identità dell'utente che richiede l'uso di più fattori, oltre a un nome utente e una password. Fornisce un livello di sicurezza aggiuntivo agli accessi e alle transazioni degli utenti."
	keywords="Introduzione a MFA, panoramica di mfa, che cos'è mfa"
	services="multi-factor-authentication"
	documentationCenter=""
	authors="billmath"
	manager="stevenpo"
	editor="curtland"/>

<tags
	ms.service="multi-factor-authentication"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article" 
	ms.date="04/07/2016"
	ms.author="billmath"/>

# Informazioni su Azure Multi-Factor Authentication
Multi-Factor Authentication (MFA) è un metodo di autenticazione che richiede più di un metodo di verifica e con il quale viene aggiunto un secondo livello di sicurezza critico agli accessi e alle transazioni degli utenti. In genere richiede due o più dei metodi di verifica seguenti:

- Un'informazione nota (in genere una password)
- Un oggetto che si possiede (un dispositivo attendibile non facile da duplicare, ad esempio un telefono)
- Una caratteristica fisica dell'utente (biometrica)

<center>![Nome utente e password](./media/multi-factor-authentication/pword.png) &#160;&#160;&#160;&#160;&#160;![Certificati](./media/multi-factor-authentication/phone.png) &#160;&#160;&#160;&#160;&#160;![Smartphone](./media/multi-factor-authentication/hware.png) &#160;&#160;&#160;&#160;&#160;![Smart card](./media/multi-factor-authentication/smart.png) &#160;&#160;&#160;&#160;&#160;![Smart card virtuale](./media/multi-factor-authentication/vsmart.png) &#160;&#160;&#160;&#160;&#160;![Nome utente e password](./media/multi-factor-authentication/cert.png)</center>



Azure Multi-Factor Authentication è un metodo di verifica dell'identità dell'utente che richiede l'uso di più fattori, oltre a un nome utente e una password. Fornisce un secondo livello di sicurezza agli accessi e alle transazioni degli utenti.

Azure multi-Factor Authentication contribuisce a salvaguardare l'accesso a dati e applicazioni rispondendo alla richiesta degli utenti di poter usare un processo di accesso semplice. Offre autenticazione avanzata tramite una gamma di semplici opzioni di verifica, ad esempio una telefonata, un SMS, una notifica dell'app mobile o un codice di verifica e token OATH di terze parti.

Per una panoramica del funzionamento di Azure Multi-Factor Authentication, vedere il video seguente.


> [AZURE.VIDEO multi-factor-authentication-overview]

##Vantaggi dell'uso di Azure Multi-Factor Authentication

Oggi, ora più che mai, le persone costantemente connesse. Grazie a smartphone, tablet, portatili e computer, gli utenti possono contare su diverse opzioni per scegliere come connettersi e restare connessi in qualsiasi momento. Le persone possono accedere i propri account e alle applicazioni da qualsiasi luogo e questo significa poter svolgere più attività e servire meglio i propri clienti.

Azure multi-Factor Authentication è una soluzione semplice da usare, scalabile e affidabile che offre un secondo metodo di autenticazione in modo che gli utenti siano sempre protetti.

![Facile da usare](./media/multi-factor-authentication/simple.png)| ![Scalabile](./media/multi-factor-authentication/scalable.png)| ![Sempre protetti](./media/multi-factor-authentication/protected.png)|![Affidabile](./media/multi-factor-authentication/reliable.png)
:-------------: | :-------------: | :-------------: | :-------------: |
**Facile da usare**|**Scalabile**|**Sempre protetti**|**Affidabile**

- **Facile da usare**: Azure Multi-Factor Authenticaton è semplice da configurare e usare. La protezione aggiuntiva fornita con Azure Multi-Factor Authentication consente agli utenti di usare e gestire i propri dispositivi e, in molti casi, di configurare la soluzione con pochi semplici clic.
- **Scalabile**: Azure Multi-Factor Authenticaton sfrutta le potenzialità del cloud e si integra con Active Directory locale e con le app personalizzate. Questa protezione viene estesa anche agli scenari di importanza strategica con volumi elevati.
- **Sempre protetti**: Azure multi-Factor Authentication fornisce autenticazione avanzata grazie all'uso dei più elevati standard di settore.
- **Affidabile**: la disponibilità di Azure Multi-Factor Authentication è garantita al 99,9%. Il servizio viene considerato non disponibile quando non è in grado di ricevere o elaborare le richieste di autenticazione per l'autenticazione a più fattori.  

Per altre informazioni sui motivi per cui è consigliabile usare Azure Multi-Factor Authentication, vedere il video seguente.

> [AZURE.VIDEO windows-azure-multi-factor-authentication]


## Come funziona Azure Multi-Factor Authentication

La sicurezza dell'autenticazione a più fattori si fonda sull'approccio basato su livelli. La manomissione di più fattori rappresenta una sfida significativa per gli autori di attacchi. Tuttavia, anche se un autore di attacco riesce a ottenere la password dell'utente, questa risulta inutile se non è in possesso del dispositivo attendibile. Se l'utente smarrisce il dispositivo, la persona che lo trova non potrà utilizzarlo a meno che non ne conosca anche la password.

![Verifica](./media/multi-factor-authentication-how-it-works/howitworks.png)



Azure multi-Factor Authentication contribuisce a salvaguardare l'accesso a dati e applicazioni rispondendo alla richiesta degli utenti di poter usare un processo di accesso semplice. Offre ulteriore sicurezza richiedendo una seconda forma di autenticazione nonché un'autenticazione avanzata tramite una gamma di opzioni di verifica semplice:

- Chiamata telefonica
- SMS
- Notifica dell'app per dispositivi mobili, che consente agli utenti di scegliere il metodo preferito
- Codice di verifica dell'app per dispositivi mobili
- Token OATH di terze parti

Per ulteriori informazioni su come funziona, vedere il video seguente.

> [AZURE.VIDEO multi-factor-authentication-deep-dive-securing-access-on-premises]

## Metodi disponibili per la multi-factor authentication
Quando un utente accede, una verifica aggiuntiva viene inviata all'utente. Di seguito è riportato un elenco di metodi che possono essere utilizzati per la seconda verifica.

Metodo di verifica | Descrizione
------------- | ------------- |
Chiamata telefonica | Viene effettuata una chiamata allo smartphone dell'utente chiedendogli di verificare che sta effettuando l'accesso premendo il simbolo #. Questa operazione completerà il processo di verifica. Questa opzione è configurabile e può essere modificata con un codice specificato dall'utente.
SMS | Verrà inviato un SMS allo smartphone dell'utente con un codice di 6 cifre. Immettere il codice per completare il processo di verifica.
Notifica dell'app per dispositivi mobili | Una richiesta di verifica verrà inviata allo smartphone dell'utente chiedendogli di completare la verifica selezionando Verifica dall'app per dispositivi mobili. Ciò si verifica se la notifica dell'app è stata selezionata come metodo di verifica principale. Se l'utente riceve la richiesta quando non sta effettuando l'accesso, potrà scegliere di segnalare la frode.
Codice di verifica dell'app per dispositivi mobili | Un codice di verifica verrà inviato all'app per dispositivi mobili in esecuzione sullo smartphone dell'utente. Ciò si verifica se il codice di verifica è stato selezionato come metodo di verifica principale.


## Versioni disponibili di Azure Multi-Factor Authentication
Azure Multi-Factor Authentication è disponibile in tre diverse versioni. Nella tabella seguente ognuna di queste versioni è descritta in modo più dettagliato.

Versione | Descrizione
------------- | ------------- |
Multi-Factor Authentication per Office 365 | Questa versione funziona solo con le applicazioni di Office 365 e viene gestita dal portale di Office 365. Gli amministratori possono quindi proteggere le risorse di Office 365 con multi-factor authentication. Questa versione viene fornita con una sottoscrizione a Office 365.
Multi-Factor Authentication per amministratori di Azure | Lo stesso subset di funzionalità di Multi-Factor Authentication per Office 365 sarà disponibile per tutti gli amministratori di Azure senza costi aggiuntivi. Ogni account amministrativo della sottoscrizione di Azure può ora usufruire di un ulteriore livello di protezione abilitando la funzionalità di multi-factor authentication di base. Ora, un amministratore che desidera accedere al portale di Azure per creare una macchina virtuale, un sito Web, gestire l'archiviazione, i servizi mobili o qualsiasi altro servizio di Azure può aggiungere l'autenticazione a più fattori all'account di amministratore.
Azure Multi-Factor Authentication | Azure multi-Factor Authentication offre la più ampia gamma di funzionalità. Fornisce opzioni di configurazione aggiuntive tramite il portale di gestione di Azure, segnalazione avanzata e il supporto per un intervallo di applicazioni locali e cloud. Azure Multi-Factor Authentication fa parte di Azure Active Directory Premium.

## Confronto tra le funzionalità delle versioni
Nella tabella seguente viene fornito un elenco delle funzionalità disponibili nelle varie versioni di Azure Multi-Factor Authentication.


Funzionalità | Multi-Factor Authentication per Office 365 (incluso nelle SKU di Office 365)|Multi-Factor Authentication per amministratori di Azure (incluso con una sottoscrizione Azure) | Azure Multi-Factor Authentication (incluso in Azure AD Premium ed Enterprise Mobility Suite)
------------- | :-------------: |:-------------: |:-------------: |
Gli amministratori possono proteggere gli account tramite Multi-Factor Authentication| * | * (disponibile solo per gli account Administrator di Azure)|*
App per dispositivi mobili come secondo fattore|* | * | *
Chiamata telefonica come secondo fattore|* | * | *
SMS come secondo fattore|* | * | *
Password di app per i client che non supportano MFA|* | * | *
Controllo amministrazione sui metodi di autenticazione| *|* | *
Modalità PIN| | | *
Avviso di illecito| | | *
Report MFA| | | *
Bypass monouso| | | *
Messaggi di saluto personalizzati per le telefonate| | | *
Personalizzazione dell'ID chiamante per le telefonate| | | *
Conferma evento| | | *
IP attendibili| | | *
Memorizzazione di MFA per dispositivi attendibili |* | * | *
SDK MFA| | | *
MFA per applicazioni locali che utilizzano il server MFA| | | *



## Come ottenere Azure Multi-Factor Authentication

Se si desiderano tutte le funzionalità offerte da Azure Multi-Factor Authentication, anziché solo quelle fornite per gli utenti di Office 365 e gli amministratori di Azure, sono disponibili diverse opzioni:

1.	Acquistare le licenze di Azure Multi-Factor Authentication e assegnarle agli utenti.
2.	Acquistare licenze con Azure Multi-Factor Authentication incluso nel pacchetto, ad esempio Azure Active Directory Premium, Enterprise Mobility Suite o Enterprise Cloud Suite e assegnarle agli utenti.
3.	Creare un provider Azure Multi-Factor Authentication all'interno di una sottoscrizione di Azure. Se non si ha ancora una sottoscrizione di Azure, è possibile iscriversi per ottenere una sottoscrizione di valutazione. Le sottoscrizioni di valutazione devono essere convertite in sottoscrizioni regolari prima della scadenza.

Quando si usa un provider di Azure Multi-Factor Authentication, sono disponibili due modelli di uso fatturati tramite la sottoscrizione di Azure:


- **Per utente**. In genere, per le aziende che desiderano abilitare la multi-factor authentication per un numero fisso di dipendenti che richiedono regolarmente l'autenticazione.
- **Per autenticazione**. In genere, per le aziende che desiderano abilitare la multi-factor authentication per un gruppo esteso di utenti esterni che non richiedono regolarmente l'autenticazione.

Azure multi-Factor Authentication fornisce metodi di verifica selezionabili per cloud e server. Ciò significa che è possibile scegliere i metodi da rendere disponibili agli utenti per l'uso con l'autenticazione a più fattori. Questa funzionalità è attualmente in anteprima pubblica per la versione cloud dell'autenticazione a più fattori. Per altre informazioni vedere [Metodi di verifica selezionabili](multi-factor-authentication-whats-next.md#selectable-verification-methods-public-preview).

Per ulteriori informazioni sui prezzi, vedere [Prezzi - Multi-Factor Authentication (MFA).](https://azure.microsoft.com/pricing/details/multi-factor-authentication/)

Scegliere il modello per postazione o basato sul consumo più adatto per l'organizzazione. Quindi, per iniziare vedere [Introduzione](multi-factor-authentication-get-started.md).

## Scegliere la soluzione di sicurezza Multi-Factor più adatta

Poiché esistono diverse versioni di Azure Multi-Factor Authentication, è necessario stabilire un paio di aspetti per scoprire quale versione sia quella corretta da utilizzare. Aspetti da considerare:

-	[Cosa si sta tentando di proteggere?](#what-am-i-trying-to-secure)
-	[Dove si trovano gli utenti?](#where-are-the-users-located)

Nelle sezioni seguenti verranno fornite istruzioni su come determinare ciascun aspetto.

### Cosa si sta tentando di proteggere?

Per determinare la soluzione Multi-Factor Authentication corretta, innanzitutto è necessario stabilire ciò che si sta tentando di proteggere con un secondo metodo di autenticazione. Si tratta di un'applicazione in Azure? Oppure si tratta, ad esempio, di un sistema di accesso remoto? Durante la definizione degli elementi da proteggere, stabiliremo dove abilitare la modalità Multi-Factor Authentication.



Cosa si intende proteggere| Multi-Factor Authentication nel cloud|Server Multi-Factor Authentication
------------- | :-------------: | :-------------: |
App prodotte direttamente da Microsoft|* |* |
App SaaS nella Raccolta app|* |* |
Le applicazioni IIS pubblicate tramite proxy app per Azure AD|* |* |
Le applicazioni IIS non pubblicate tramite proxy app per Azure AD | |* |
Accesso remoto, ad esempio VPN, RDG| |* |



### Dove si trovano gli utenti?

Successivamente, a seconda di dove si trovano gli utenti, è possibile determinare la soluzione corretta da utilizzare, ovvero Multi-Factor Authentication nel cloud o locale mediante Server MFA.



Ubicazione degli utenti| Soluzione
------------- | :------------- |
Azure Active Directory| Multi-Factor Authentication nel cloud|
Azure AD e AD locale usando la federazione con AD FS| Sia MFA nel cloud che il server MFA sono opzioni possibili
Azure AD e AD locale con DirSync, Azure AD Sync, Azure AD Connect, senza sincronizzazione di password|Sia MFA nel cloud che il server MFA sono opzioni possibili
Azure AD e AD locale con DirSync, Azure AD Sync, Azure AD Connect, con sincronizzazione di password|Multi-Factor Authentication nel cloud
Active Directory locale|Server Multi-Factor Authentication

Nella tabella seguente è riportato un confronto tra le funzionalità disponibili in una soluzione Multi-Factor Authentication nel cloud e una soluzione server Multi-Factor Authentication.

 | Multi-Factor Authentication nel cloud | Server Multi-Factor Authentication
------------- | :-------------: | :-------------: |
Notifica dell'app per dispositivi mobili come secondo fattore | ● | ● |
Codice di verifica dell'app per dispositivi mobili come secondo fattore | ● | ●
Chiamata telefonica come secondo fattore | ● | ●
SMS unidirezionale come secondo fattore | ● | ●
SMS bidirezionale come secondo fattore | | ●
Token hardware come secondo fattore | | ●
Password di app per i client che non supportano MFA | ● |  
Controllo amministrazione sui metodi di autenticazione | ● | ●
Modalità PIN | | ●
Avviso di illecito | ● | ●
Report MFA | ● | ●
Bypass monouso | | ●
Messaggi di saluto personalizzati per le telefonate | ● | ●
ID chiamante personalizzabile per le telefonate | ● | ●
IP attendibili | ● | ●
Memorizzazione di MFA per dispositivi attendibili| ● |  
Accesso condizionale | ● | ●
Cache | | ●

Dopo aver stabilito se è necessario utilizzare la modalità Multi-Factor Authentication nel cloud o il server MFA locale, sarà possibile iniziare a impostare e utilizzare Azure Multi-Factor Authentication. **Selezionare l'icona che rappresenta lo scenario.**

<center> [![Cloud](./media/multi-factor-authentication-get-started/cloud2.png)](multi-factor-authentication-get-started-cloud.md) &#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;[![Proofup](./media/multi-factor-authentication-get-started/server2.png)](multi-factor-authentication-get-started-server.md) &#160;&#160;&#160;&#160;&#160; </center>

<!---HONumber=AcomDC_0420_2016-->