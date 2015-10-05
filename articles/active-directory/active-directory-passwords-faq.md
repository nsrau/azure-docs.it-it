<properties
	pageTitle="Domande frequenti: Gestione delle password di Azure AD | Microsoft Azure"
	description="Domande frequenti sulla gestione delle password in Azure AD, tra cui reimpostazione della password, registrazione, report e writeback in Active Directory locale."
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
	ms.date="09/18/2015" 
	ms.author="asteen"/>

# Domande frequenti sulla gestione delle password
Di seguito sono riportate alcune domande frequenti su tutti gli aspetti riguardanti la gestione delle password

Nel caso di una domanda per la quale non si conosce la risposta o se si stanno cercando informazioni su un particolare problema, è possibile leggere le informazioni seguenti per verificare se è già stato affrontato. In caso contrario, è possibile formulare una domanda non trattata qui tramite i [forum di Azure AD](https://social.msdn.microsoft.com/Forums/home?forum=WindowsAzureAD) per ricevere una risposta non appena possibile.

Questo articolo di domande frequenti è suddiviso nelle sezioni seguenti:

- [**Domande sulla registrazione per la reimpostazione della password**](#password-reset-registration)
- [**Domande sulla reimpostazione della password**](#password-reset)
- [**Domande relative ai report di gestione delle password**](#password-management-reports)
- [**Domande sul writeback della password**](#password-writeback)

## Registrazione per la reimpostazione della password
 - **D: gli utenti possono registrare i propri dati per la reimpostazione della password?**

 > **R:** sì, a condizione che la reimpostazione della password sia abilitata, gli utenti con licenza possono accedere al portale di registrazione per la reimpostazione della password all'indirizzo http://aka.ms/ssprsetup per registrare le informazioni di autenticazione da usare per la reimpostazione della password. Gli utenti possono anche registrarsi tramite il pannello di accesso all'indirizzo http://myapps.microsoft.com, facendo clic sulla scheda del profilo e scegliendo l'opzione Esegui la registrazione per la reimpostazione della password. Per altre informazioni su come configurare gli utenti per la reimpostazione della password vedere l'articolo relativo alla configurazione degli utenti per la reimpostazione della password.

 - **D: è possibile definire i dati di reimpostazione della password per conto degli utenti?**

 > **R:** è possibile usando DirSync o PowerShell oppure tramite il [portale di gestione di Azure](https://manage.windowsazure.com) o il portale di amministrazione di Office. Per altre informazioni su questa funzionalità vedere il post di blog relativo al miglioramento della privacy per Azure AD MFA e i numeri di telefono per la reimpostazione della password e leggere l'argomento relativo alle informazioni sul modo in cui i dati vengono usati per la reimpostazione della password.

 - **D: è possibile sincronizzare i dati per le domande di sicurezza dall'ambiente locale??**

 > **R:** no, attualmente non è possibile, ma è un aspetto in fase di valutazione.

 - **D: gli utenti possono registrare i dati in modo che non siano visibili ad altri utenti?**

 > **R:** sì. I dati che gli utenti registrano tramite il portale di registrazione per la reimpostazione della password vengono salvati in campi di autenticazione privati visibili solo agli amministratori globali e agli utenti stessi. Per altre informazioni su questa funzionalità vedere il post di blog relativo al miglioramento della privacy per Azure AD MFA e i numeri di telefono per la reimpostazione della password e leggere l'argomento relativo alle informazioni sul modo in cui i dati vengono usati per la reimpostazione della password.

 - **D: gli utenti devono essere registrati prima di poter usare la funzionalità di reimpostazione della password?**

 > **R:** no. Se le informazioni di autenticazione definite per conto degli utenti sono sufficienti, non dovranno registrarsi. La reimpostazione della password funzionerà senza problemi, purché i dati archiviati nei campi appropriati nella directory siano formattati correttamente. Per altre informazioni, vedere l'articolo relativo alle informazioni sul modo in cui i dati vengono usati per la reimpostazione della password.

 - **D: è possibile sincronizzare o impostare i campi Telefono per l'autenticazione, Indirizzo di posta elettronica per l'autenticazione o Telefono per l'autenticazione alternativo per conto degli utenti?**

 > **R:** non attualmente, ma la possibilità di abilitare questa funzionalità è in fase di valutazione.

 - **D: In che modo il portale di registrazione riconosce le opzioni da visualizzare agli utenti?**

 > **R:** il portale di registrazione per la reimpostazione della password visualizza solo le opzioni che sono state abilitate per gli utenti nella sezione Criteri di reimpostazione password utente della scheda Configura della directory. Di conseguenza, se ad esempio non si abilitano le domande di sicurezza, gli utenti non potranno registrarsi per questa opzione.

 - **D: quando un utente viene considerato registrato?**

 > **R:** Quando ha almeno N informazioni di autenticazione definite, dove N corrisponde al valore dell'opzione Numero di metodi di autenticazione necessari impostata nel [portale di gestione di Azure](https://manage.windowsazure.com). Per altre informazioni vedere l'articolo relativo alla personalizzazione dei criteri di reimpostazione della password utente.


## Reimpostazione delle password

 - **D: quanto tempo occorre attendere prima di ricevere un messaggio di posta elettronica, un SMS o una telefonata relativa alla reimpostazione della password?**

 > **R:** messaggi di posta elettronica, SMS e telefonate dovrebbero arrivare entro 1 minuto, normalmente tra 5 e 20 secondi. Se non si riceve la notifica entro questo intervallo di tempo, controllare la cartella della posta indesiderata, verificare che il numero o l'indirizzo di posta elettronica di contatto sia quello previsto e che i dati di autenticazione nella directory siano formattati correttamente. Per altre informazioni sulla formattazione dei numeri di telefono e degli indirizzi di posta elettronica da usare con la reimpostazione della password, vedere l'articolo relativo alle informazioni sul modo in cui i dati vengono usati per la reimpostazione della password.

 - **D: Quali lingue sono supportate per la reimpostazione della password?**

 > **R:** l'interfaccia utente, i messaggi SMS e le chiamate vocali per la reimpostazione della password sono localizzati nelle stesse 40 lingue supportate in Office 365, ovvero: arabo, bulgaro, cinese semplificato, cinese tradizionale, croato, ceco, danese, olandese, inglese, estone, finlandese, francese, tedesco, greco, ebraico, hindi, ungherese, indonesiano, italiano, giapponese, kazaco, coreano, lettone, lituano, malese (Malaysia), norvegese (Bokmål), polacco, portoghese (Brasile), portoghese (Portogallo), rumeno, russo, serbo (alfabeto latino), slovacco, sloveno, spagnolo, svedese, tailandese, turco, ucraino e vietnamita.

 - **D: a quali parti dell'esperienza di reimpostazione della password vengono applicate le impostazioni di personalizzazione dell'organizzazione specificate nella scheda Configura della directory?**

 > **R:** il portale per la reimpostazione della password mostra il logo dell'organizzazione e consente di configurare il collegamento Contattare l'amministratore con un indirizzo di posta elettronica o un URL personalizzato. Nel corpo di tutti i messaggi di posta elettronica inviati dalla funzionalità di reimpostazione della password saranno inclusi il logo, i colori (in questo caso, rosso), e il nome dell'organizzazione, oltre al nome Da personalizzato. Di seguito è riportato un esempio con tutti gli elementi personalizzati. Per altre informazioni, vedere l'articolo relativo alla personalizzazione dell'aspetto degli elementi di reimpostazione della password.

  ![][001]

 - **D: in che modo è possibile spiegare agli utenti come fare per reimpostare le proprie password?**

 > **R:** è possibile indirizzare gli utenti direttamente al sito https://passwordreset.microsoftonline.com oppure invitarli a fare clic sul collegamento Problemi di accesso all'account disponibile in qualsiasi schermata di accesso con un ID aziendale o dell'istituto di istruzione. È possibile pubblicare questi collegamenti (o creare URL di reindirizzamento) in qualsiasi posizione facilmente accessibile agli utenti.

 - **D: questa pagina può essere usata da un dispositivo mobile?**

 > **R:** sì, la pagina funziona anche sui dispositivi mobili.

 - **D: è supportato lo sblocco di account Active Directory locali quando gli utenti reimpostano le password?**

 > **R:** sì, l'account verrà sbloccato automaticamente quando un utente reimposta la password e la funzionalità Writeback password è stata distribuita con tutte le versioni di Azure AD Connect o la versioni di Azure AD Sync 1.0.0485.0222 o successiva.

 - **D: come è possibile integrare la reimpostazione della password direttamente nell'esperienza di accesso desktop degli utenti?**

 > **D:** Attualmente non è possibile. Tuttavia, se questa funzionalità è indispensabile, i clienti di Azure AD Premium possono installare gratuitamente Gestione identità Microsoft e distribuire la soluzione di reimpostazione della password locale inclusa nel componente per soddisfare questo requisito.

 - **D: è possibile impostare domande di sicurezza diverse per impostazioni locali diverse?**

 > **R:** no, attualmente non è possibile, ma è un aspetto in fase di valutazione.

 - **D: Quante domande si possono configurare per l'opzione di autenticazione Domande di sicurezza?**

 > **R:** nel [portale di gestione di Azure](https://manage.windowsazure.com) è possibile configurare un massimo di 20 domande di sicurezza personalizzate.

 - **D: qual è la lunghezza delle domande di sicurezza?**

 > **R:** le domande di sicurezza possono avere una lunghezza compresa tra 3 e 200 caratteri.

 - **D: qual è la lunghezza delle risposte alle domande di sicurezza?**

 > **R:** le risposte possono avere una lunghezza compresa tra 3 e 40 caratteri.

 - **D: Le risposte duplicate alle domande di sicurezza vengono rifiutate?**

 > **R:** sì, vengono rifiutate.

 - **D: un utente può registrarsi più di una volta per la stessa domanda di sicurezza?**

 > **R:** no. Dopo che l'utente ha registrato una domanda particolare, non potrà registrarsi per quella domanda una seconda volta.

 - **D: è possibile impostare un limite minimo di domande di sicurezza per la registrazione e la reimpostazione?**

 > **R:** sì, è possibile impostare un limite per la registrazione e un altro per la reimpostazione. Possono essere richieste da 3 a 5 domande di sicurezza per la registrazione e da 3 a 5 domande di sicurezza per la reimpostazione.

 - **D: Se un utente ha registrato più del numero massimo di domande per reimpostare la password, come vengono selezionate le domande di sicurezza durante la reimpostazione?**

 > **R:** dal numero totale di domande per cui l'utente si è registrato vengono selezionate casualmente N domande di sicurezza, dove N è il numero minimo di domande richieste per la reimpostazione della password. Ad esempio, se un utente ha registrato 5 domande di sicurezza, ma per la reimpostazione ne sono richieste solo 3, ne verranno selezionate casualmente e presentate all'utente 3 di quelle 5 al momento della reimpostazione. Se un utente sbaglia le risposte alle domande, il processo di selezione verrà eseguito di nuovo per evitare la ripetizione (hammering) delle domande.

 - **D: viene impedito agli utenti di provare a reimpostare la password più volte in un breve periodo di tempo?**

 > **R:** sì. Nel processo di reimpostazione della password sono integrate diverse funzionalità di sicurezza. Gli utenti possono effettuare solo 5 tentativi di reimpostazione della password nell'arco di un'ora, prima di essere bloccati per 24 ore. Gli utenti possono convalidare un numero di telefono solo 5 volte nell'arco di un'ora, prima di essere bloccati per 24 ore. Gli utenti possono provare solo 5 volte un singolo metodo di autenticazione nell'arco di un'ora, prima di essere bloccati per 24 ore.

 - **D: per quanto tempo è valido il passcode monouso inviato per posta elettronica o SMS?**

 > **R:** La durata della sessione per la reimpostazione della password è di 105 minuti. Ciò significa che dall'inizio dell'operazione di reimpostazione della password l'utente ha 105 minuti per completare l'operazione. Allo scadere di questo periodo di tempo, il passcode monouso inviato per SMS o posta elettronica non sarà più valido.


## Report di gestione delle password

 - **D: quanto tempo trascorre prima che i dati vengono visualizzati nei report di gestione delle password?**

 > **R:** i dati dovrebbero essere visualizzati in tali report entro 5-10 minuti. In alcuni casi, potrebbero richiedere fino a un'ora.

 - **D: Come si possono filtrare i report di gestione delle password?**

 > **R:** per filtrare i report di gestione delle password, è possibile fare clic sulla piccola lente di ingrandimento all'estrema destra delle etichette di colonna, verso la parte superiore del report (vedere la schermata). Per applicare una funzione di filtro più completa, scaricare il report in Excel e creare una tabella pivot.

  ![][002]

 - **D: fino a quando risalgono i report di gestione delle password?**

 > **R:** questi report mostrano le operazioni che si sono verificate negli ultimi 30 giorni. Attualmente è in corso la valutazione della possibilità di estendere questo periodo di tempo. Per il momento, se occorre archiviare i dati, è possibile scaricare i report periodicamente e salvarli in un percorso separato.

 - **D: esiste un limite al numero massimo di righe visualizzabili nei report di gestione delle password?**

 > **R:** sì, è possibile visualizzare un massimo di 1.000 righe nei report di gestione delle password, sia quando vengono riprodotti nell'interfaccia utente sia quando vengono scaricati. Attualmente si sta esaminando come aumentare questo limite.


## Writeback password
 - **D: Come funziona il writeback della password in background?**

 > **R:** per una spiegazione dettagliata delle operazioni eseguite quando si abilita il writeback delle password, nonché del modo in cui i dati transitano dal sistema all'ambiente locale, vedere l'articolo relativo al funzionamento del writeback delle password. Per informazioni su come viene garantita l'elevata sicurezza del servizio di writeback della password, vedere la sezione in cui viene descritto il "modello di sicurezza per il writeback della password" nell'articolo relativo al funzionamento del writeback delle password.

 - **D: entro quanto tempo si attiva il funzionamento del writeback della password? Esiste un ritardo di sincronizzazione come per la sincronizzazione di hash della password?**

 > **R:** il writeback della password è immediato. Si tratta di una pipeline sincrona che funziona fondamentalmente in modo diverso rispetto alla sincronizzazione di hash della password. Il writeback della password consente agli utenti di avere un feedback in tempo reale sulla riuscita dell'operazione di reimpostazione o modifica della propria password. L'intervallo medio per un writeback della password riuscito è inferiore a 500 ms.

 - **D: per quali tipi di account funziona il writeback della password?**

 > **R:** per gli utenti federati e con sincronizzazione di hash della password.

 - **D: il writeback della password applica i criteri password del dominio?**

 > **R:** sì, applica validità, cronologia, complessità, filtri e qualsiasi altra restrizione eventualmente applicata alle password nel dominio locale.

 - **D: il writeback della password è sicuro? Come si può essere certi di non essere oggetto di un attacco?**

 > **R:** sì, il writeback della password è estremamente sicuro. Per altre informazioni sui 4 livelli di sicurezza implementati dal servizio di writeback della password, vedere la sezione in cui viene descritto il "modello di sicurezza per il writeback della password" nell'articolo relativo al relativo al funzionamento del writeback delle password.


<br/> <br/> <br/>

**Risorse aggiuntive**


* [Informazioni sulla gestione delle password](active-directory-passwords.md)
* [Funzionamento della gestione delle password](active-directory-passwords-how-it-works.md)
* [Introduzione alla gestione delle password](active-directory-passwords-getting-started.md)
* [Personalizzare la gestione delle password](active-directory-passwords-customize.md)
* [Procedure consigliate per la gestione delle password](active-directory-passwords-best-practices.md)
* [Come ottenere informazioni dettagliate con i report di gestione delle password](active-directory-passwords-get-insights.md)
* [Risolvere i problemi relativi alla gestione delle password](active-directory-passwords-troubleshoot.md)
* [Altre informazioni](active-directory-passwords-learn-more.md)
* [Gestione delle password in MSDN](https://msdn.microsoft.com/library/azure/dn510386.aspx)


[001]: ./media/active-directory-passwords-faq/001.jpg "Image_001.jpg"
[002]: ./media/active-directory-passwords-faq/002.jpg "Image_002.jpg"

<!---HONumber=Sept15_HO4-->