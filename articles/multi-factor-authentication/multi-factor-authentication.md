<properties
	pageTitle="Informazioni su Azure Multi-Factor Authentication | Microsoft Azure"
	description="Questo argomento spiega che cos'è Multi-Factor Authentication (MFA) e perché usare questo servizio, fornisce informazioni sul client di Multi-Factor Authentication e sui diversi metodi e versioni disponibili. Azure Multi-Factor Authentication è un metodo di verifica dell'identità dell'utente che richiede l'uso di più fattori, oltre a un nome utente e una password. Fornisce un livello di sicurezza aggiuntivo agli accessi e alle transazioni degli utenti."
	keywords="Introduzione a MFA, panoramica di mfa, che cos'è mfa"
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
	ms.date="09/22/2016"
	ms.author="kgremban"/>

# Informazioni su Azure Multi-Factor Authentication
Multi-Factor Authentication (MFA) è un metodo di autenticazione che richiede più di un metodo di verifica e con il quale viene aggiunto un secondo livello di sicurezza critico agli accessi e alle transazioni degli utenti. In genere richiede due o più dei metodi di verifica seguenti:

- Un'informazione nota (in genere una password)
- Un oggetto che si possiede (un dispositivo attendibile non facile da duplicare, ad esempio un telefono)
- Una caratteristica fisica dell'utente (biometrica)

<center>![Nome utente e password](./media/multi-factor-authentication/pword.png) &#160;&#160;&#160;&#160;&#160;![Certificati](./media/multi-factor-authentication/phone.png) &#160;&#160;&#160;&#160;&#160;![Smartphone](./media/multi-factor-authentication/hware.png) &#160;&#160;&#160;&#160;&#160;![Smart card](./media/multi-factor-authentication/smart.png) &#160;&#160;&#160;&#160;&#160;![Smart card virtuale](./media/multi-factor-authentication/vsmart.png) &#160;&#160;&#160;&#160;&#160;![Nome utente e password](./media/multi-factor-authentication/cert.png)</center>


Azure multi-Factor Authentication contribuisce a salvaguardare l'accesso a dati e applicazioni rispondendo alla richiesta degli utenti di poter usare un processo di accesso semplice. Offre autenticazione avanzata tramite una gamma di semplici opzioni di verifica, ad esempio una telefonata, un SMS o una verifica dell'app per dispositivi mobili.

>[AZURE.VIDEO multi-factor-authentication-overview]

## Vantaggi dell'uso di Azure Multi-Factor Authentication

Oggi, ora più che mai, le persone costantemente connesse. Grazie a smartphone, tablet, portatili e computer, gli utenti possono contare su diverse opzioni per scegliere come connettersi e restare connessi in qualsiasi momento. Le persone possono accedere ai propri account e alle applicazioni da qualsiasi luogo e questo significa poter svolgere più attività e servire meglio i clienti.

Azure multi-Factor Authentication è una soluzione semplice da usare, scalabile e affidabile che offre un secondo metodo di autenticazione in modo che gli utenti siano sempre protetti.

![Facile da usare](./media/multi-factor-authentication/simple.png)| ![Scalabile](./media/multi-factor-authentication/scalable.png)| ![Sempre protetti](./media/multi-factor-authentication/protected.png)|![Affidabile](./media/multi-factor-authentication/reliable.png)
:-------------: | :-------------: | :-------------: | :-------------: |
**Facile da usare**|**Scalabile**|**Sempre protetti**|**Affidabile**

- **Facile da usare**: Azure Multi-Factor Authentication è semplice da configurare e usare. La protezione aggiuntiva offerta con Azure Multi-Factor Authentication consente agli utenti di usare e gestire i propri dispositivi. L'aspetto più importante è che in molti casi può essere configurata con pochi semplici clic.
- **Scalabile**: Azure Multi-Factor Authentication usa le potenzialità del cloud e si integra con Active Directory locale e con le app personalizzate. Questa protezione viene estesa anche agli scenari di importanza strategica con volumi elevati.
- **Sempre protetti**: Azure multi-Factor Authentication fornisce autenticazione avanzata grazie all'uso dei più elevati standard di settore.
- **Affidabile**: la disponibilità di Azure Multi-Factor Authentication è garantita al 99,9%. Il servizio viene considerato non disponibile quando non è in grado di ricevere o elaborare le richieste di autenticazione per l'autenticazione a più fattori.

>[AZURE.VIDEO windows-azure-multi-factor-authentication]


## Come funziona Azure Multi-Factor Authentication

La sicurezza dell'autenticazione a più fattori si fonda sull'approccio basato su livelli. La manomissione di più fattori rappresenta una sfida significativa per gli autori di attacchi. Anche se un utente malintenzionato riesce a ottenere la password dell'utente, questa risulta inutile se non è in possesso del dispositivo attendibile. Se l'utente smarrisce il dispositivo, la persona che lo trova non potrà usarlo a meno che non ne conosca anche la password.

![Verifica](./media/multi-factor-authentication-how-it-works/howitworks.png)



Azure multi-Factor Authentication contribuisce a salvaguardare l'accesso a dati e applicazioni rispondendo alla richiesta degli utenti di poter usare un processo di accesso semplice. Offre ulteriore sicurezza richiedendo una seconda forma di autenticazione nonché un'autenticazione avanzata tramite una gamma di opzioni di verifica semplice:

- Chiamata telefonica
- SMS
- Notifica dell'app per dispositivi mobili, che consente agli utenti di scegliere il metodo preferito
- Codice di verifica dell'app per dispositivi mobili
- Token OATH di terze parti

>[AZURE.VIDEO multi-factor-authentication-deep-dive-securing-access-on-premises]

## Metodi disponibili per la multi-factor authentication
Quando un utente accede, una verifica aggiuntiva viene inviata all'utente. Di seguito è riportato un elenco di metodi che possono essere utilizzati per la seconda verifica.

Metodo di verifica | Descrizione
------------- | ------------- |
Chiamata telefonica | Viene effettuata una chiamata al telefono dell'utente chiedendogli di verificare che stia effettuando l'accesso. Premendo il tasto # verrà completato il processo di verifica. Questa opzione è configurabile e può essere modificata con un codice specificato dall'utente.
SMS | Viene inviato un SMS allo smartphone dell'utente con un codice di 6 cifre. Immettere il codice per completare il processo di verifica.
Notifica dell'app per dispositivi mobili | Viene inviata una richiesta allo smartphone dell'utente chiedendogli di completare la verifica selezionando **Verifica** dall'app per dispositivi mobili. Questa operazione viene eseguita se la notifica dell'app è il metodo di verifica primario. Se l'utente riceve la richiesta quando non sta effettuando l'accesso, potrà segnalare la frode.</li><br><p> L'app Microsoft Authenticator è disponibile per [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071), [Android](http://go.microsoft.com/fwlink/?Linkid=825072) e [IOS](http://go.microsoft.com/fwlink/?Linkid=825073).
Codice di verifica dell'app per dispositivi mobili | Viene inviato un codice di verifica all'app per dispositivi mobili in esecuzione sullo smartphone dell'utente. Questa operazione viene eseguita se è stato selezionato un codice di verifica come metodo di verifica principale.</li><br><p> L'app Microsoft Authenticator è disponibile per [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071), [Android](http://go.microsoft.com/fwlink/?Linkid=825072) e [IOS](http://go.microsoft.com/fwlink/?Linkid=825073).


## Versioni disponibili di Azure Multi-Factor Authentication
Azure Multi-Factor Authentication è disponibile in tre diverse versioni. Nella tabella seguente ognuna di queste versioni è descritta in modo più dettagliato.

Versione | Descrizione
------------- | ------------- |
Multi-Factor Authentication per Office 365 | Questa versione funziona solo con le applicazioni di Office 365 e viene gestita dal portale di Office 365. Gli amministratori possono quindi proteggere le risorse di Office 365 con multi-factor authentication. Questa versione viene fornita con una sottoscrizione a Office 365.
Multi-Factor Authentication per amministratori di Azure | Lo stesso subset di funzionalità di Multi-Factor Authentication per Office 365 è disponibile per tutti gli amministratori di Azure senza costi aggiuntivi. Ogni account amministrativo della sottoscrizione di Azure può ora usufruire di un livello di protezione aggiuntivo abilitando la funzionalità di multi-factor authentication di base. Un amministratore che voglia accedere al portale di Azure per creare una macchina virtuale, un sito Web, gestire l'archiviazione o usare qualsiasi altro servizio di Azure può aggiungere Multi-Factor Authentication all'account di amministratore.
Azure Multi-Factor Authentication | Azure multi-Factor Authentication offre la più ampia gamma di funzionalità. Mette a disposizione opzioni di configurazione aggiuntive tramite il [portale di Azure classico](http://manage.windowsazure.com), segnalazione avanzata e il supporto per un intervallo di applicazioni locali e cloud. Azure Multi-Factor Authentication fa parte di Azure Active Directory Premium ed Enterprise Mobility Suite.

## Confronto tra le funzionalità delle versioni
La tabella seguente indica un elenco delle funzionalità disponibili nelle varie versioni di Azure Multi-Factor Authentication.


Funzionalità | Multi-Factor Authentication per Office 365 (incluso nelle SKU di Office 365)|Multi-Factor Authentication per amministratori di Azure (incluso con una sottoscrizione Azure) | Azure Multi-Factor Authentication (incluso in Azure AD Premium ed Enterprise Mobility Suite)
------------- | :-------------: |:-------------: |:-------------: |
Gli amministratori possono proteggere gli account tramite Multi-Factor Authentication| ● | ● (disponibile solo per gli account amministratore di Azure)|●
App per dispositivi mobili come secondo fattore|● | ● | ●
Chiamata telefonica come secondo fattore|● | ● | ●
SMS come secondo fattore|● | ● | ●
Password di app per i client che non supportano MFA|● | ● | ●
Controllo amministrazione sui metodi di autenticazione| ● | ● | ●
Modalità PIN| | | ●
Avviso di illecito| | | ●
Report MFA| | | ●
Bypass monouso| | | ●
Messaggi di saluto personalizzati per le telefonate| | | ●
Personalizzazione dell'ID chiamante per le telefonate| | | ●
Conferma evento| | | ●
IP attendibili| | | ●
Memorizzazione di MFA per dispositivi attendibili |● | ● | ●
SDK MFA | | | ● richiede un provider di Multi-Factor Authentication e una sottoscrizione completa di Azure
MFA per applicazioni locali che utilizzano il server MFA| | | ●

## Come ottenere Azure Multi-Factor Authentication

Per usare tutte le funzionalità offerte da Azure Multi-Factor Authentication, oltre a quelle messe a disposizione per gli utenti di Office 365 e gli amministratori di Azure, sono disponibili diverse opzioni:

1.	Acquistare le licenze di Azure Multi-Factor Authentication e assegnarle agli utenti.
2.	Acquistare licenze con Azure Multi-Factor Authentication incluso nel pacchetto, ad esempio Azure Active Directory Premium, Enterprise Mobility Suite o Enterprise Cloud Suite e assegnarle agli utenti.
3.	Creare un provider Azure Multi-Factor Authentication all'interno di una sottoscrizione di Azure. Se non si ha ancora una sottoscrizione di Azure, è possibile iscriversi per ottenere una sottoscrizione di valutazione. Le sottoscrizioni di valutazione devono essere convertite in sottoscrizioni normali prima della scadenza.

Quando si usa un provider di Azure Multi-Factor Authentication, sono disponibili due modelli di uso fatturati tramite la sottoscrizione di Azure:


- **Per utente**. Per le aziende che vogliono abilitare la multi-factor authentication per un numero fisso di dipendenti che richiedono regolarmente l'autenticazione.
- **Per autenticazione**. Per le aziende che vogliono abilitare la multi-factor authentication per un gruppo esteso di utenti esterni che non richiedono regolarmente l'autenticazione.

Azure multi-Factor Authentication fornisce metodi di verifica selezionabili per cloud e server. Ciò significa che è possibile scegliere i metodi da rendere disponibili agli utenti per l'uso con l'autenticazione a più fattori. Questa funzionalità è attualmente in anteprima pubblica per la versione cloud dell'autenticazione a più fattori. Per altre informazioni, vedere [Metodi di verifica selezionabili](multi-factor-authentication-whats-next.md#selectable-verification-methods).

Per ulteriori informazioni sui prezzi, vedere [Prezzi - Multi-Factor Authentication (MFA).](https://azure.microsoft.com/pricing/details/multi-factor-authentication/)

Scegliere il modello per postazione o basato sul consumo più adatto per l'organizzazione. Quindi, per iniziare vedere [Introduzione](multi-factor-authentication-get-started.md).

## Passaggi successivi

Per iniziare a usare Azure Multi-Factor Authentication, il primo passaggio consiste nello [scegliere tra MFA nel cloud o locale](multi-factor-authentication-get-started.md)

<!---HONumber=AcomDC_0928_2016-->