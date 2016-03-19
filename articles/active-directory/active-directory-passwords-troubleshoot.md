<properties 
	pageTitle="Risoluzione dei problemi: Gestione delle password di Azure AD | Microsoft Azure" 
	description="Passaggi comuni per la risoluzione dei problemi di gestione delle password di Azure AD, tra cui reimpostazione, modifica, writeback, registrazione e informazioni da includere per ottenere assistenza." 
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
	ms.date="02/16/2016" 
	ms.author="asteen"/>

# Come risolvere i problemi relativi alla gestione delle password
Questo articolo fornisce informazioni su come risolvere i problemi relativi alla gestione delle password. È possibile risolvere la maggior parte dei problemi che potrebbero verificarsi con i pochi semplici passaggi illustrati di seguito, che spiegano come risolvere i problemi della distribuzione:

* [**Informazioni da includere quando è necessaria l’assistenza**](#information-to-include-when-you-need-help)
* [**Problemi relativi alla configurazione di gestione delle password nel portale di gestione di Azure**](#troubleshoot-password-reset-configuration-in-the-azure-management-portal)
* [**Problemi relativi ai report di gestione delle password nel portale di gestione di Azure**](#troubleshoot-password-management-reports-in-the-azure-management-portal)
* [**Problemi relativi al portale di registrazione per la reimpostazione della password**](#troubleshoot-the-password-reset-registration-portal)
* [**Problemi relativi al portale di reimpostazione della password**](#troubleshoot-the-password-reset-portal)
* [**Problemi relativi al writeback della password**](#troubleshoot-password-writeback)
  - [Codici errore del registro eventi di writeback della password](#password-writeback-event-log-error-codes)
  - [Problemi relativi alla connettività di writeback della password](#troubleshoot-password-writeback-connectivity)

Se dopo aver eseguito i passaggi seguenti i problemi non vengono risolti, è possibile pubblicare una domanda nei [forum di Azure AD](https://social.msdn.microsoft.com/forums/azure/home?forum=WindowsAzureAD) o contattare il personale del supporto tecnico che analizzerà il problema il prima possibile.

## Informazioni da includere quando è necessaria assistenza

Se non è possibile risolvere il problema con le indicazioni fornite di seguito, è possibile contattare il personale del supporto tecnico. Al momento di contattarli, è consigliabile includere le informazioni seguenti:

 - **Descrizione generale dell'errore**: qual è esattamente il messaggio di errore visualizzato? Se non è stato visualizzato alcun messaggio di errore, descrivere dettagliatamente il comportamento imprevisto riscontrato.
 - **Pagina**: in quale pagina ci si trovava quando è stato visualizzato l'errore? (Includere l'URL)
 - **Data /Ora/ Fuso orario** - quando (ora e data) è stato visualizzato l'errore? (Includere il fuso orario)
 - **Codice di supporto** - qual è stato il codice di supporto generato quando è stato visualizzato l'errore? (Per trovare questo codice, riprodurre l'errore, quindi fare clic sul collegamento Codice di supporto nella parte inferiore della schermata e inviare al personale del supporto tecnico il GUID risultante). 
   - Se è visualizzata una pagina senza un codice di supporto nella parte inferiore, premere F12 ed eseguire una ricerca di SID e CID, quindi inviare i due risultati al personale del supporto tecnico.

    ![][001]

 - **ID utente**: l'ID dell'utente che ha visualizzato l'errore (ad esempio user@contoso.com)?
 - **Informazioni sull'utente** - si tratta di un utente federato, con sincronizzazione di hash della password o solo cloud? All'utente è assegnata una licenza AAD Premium o AAD Basic?
 - **Registro eventi dell'applicazione**: se si usa il writeback della password e l'errore si verifica nell'infrastruttura locale, comprimere una copia del registro eventi dell'applicazione dal server Azure AD Connect e inviarla insieme alla richiesta.

Queste informazioni consentiranno di risolvere il problema nel più breve tempo possibile.


## Risolvere i problemi relativi alla configurazione di reimpostazione password nel portale di gestione di Azure
Se si verifica un errore mentre si configura la reimpostazione della password, è possibile provare a risolverlo attenendosi ai passaggi di risoluzione dei problemi seguenti:

<table>
          <tbody><tr>
            <td>
              <p>
                <strong>Scenario di errore</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Errore visualizzato dall'utente</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Soluzione</strong>
              </p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Non è possibile visualizzare la sezione <strong>Criteri di reimpostazione password utente</strong> nella scheda <strong>Configura</strong> del portale di gestione di Azure</p>
            </td>
            <td>
              <p>La sezione <strong>Criteri di reimpostazione password utente</strong> non è visibile nella scheda <strong>Configura</strong> del portale di gestione di Azure.</p>
            </td>
            <td>
              <p>Questo problema può verificarsi se all'amministratore che esegue l'operazione non è assegnata una licenza AAD Premium o AAD Basic. </p>
              <p>Per correggere l'errore, assegnare una licenza AAD Premium o AAD Basic all'account amministratore in questione tramite la scheda <strong>Licenze</strong> e riprovare.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Nella sezione <strong>Criteri di reimpostazione password utente</strong> non è possibile visualizzare le opzioni di configurazione descritte nella documentazione</p>
            </td>
            <td>
              <p>La sezione <strong>Criteri di reimpostazione password utente</strong> è visibile, ma il solo flag visualizzato nella sezione è <strong>Utenti abilitati per la reimpostazione della password</strong>.</p>
            </td>
            <td>
              <p>Le restanti opzioni dell'interfaccia utente verranno visualizzate dopo avere impostato il flag <strong>Utenti abilitati per la reimpostazione della password</strong> su <strong>Sì</strong>.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Non è possibile visualizzare un'opzione di configurazione specifica</p>
            </td>
            <td>
              <p>Ad esempio, l'opzione <strong>Numero di giorni dopo cui gli utenti devono confermare i propri dati di contatto</strong> non è visibile scorrendo la sezione <strong>Criteri di reimpostazione password utente</strong> (o altri esempi dello stesso problema).</p>
            </td>
            <td>
              <p>Molti elementi dell'interfaccia utente rimangono nascosti finché non sono necessari. Provare ad abilitare tutte le opzioni che si desidera visualizzare nella pagina.</p>
              <p>Vedere <a href="../active-directory-passwords-customize#password-management-behavior">Personalizzazione dei criteri di reimpostazione della password utente</a> per altre informazioni su tutti i controlli disponibili.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Non è possibile visualizzare l'opzione di configurazione <strong>Writeback delle password in Active Directory locale</strong></p>
            </td>
            <td>
              <p>L'opzione <strong>Writeback delle password in Active Directory locale</strong> non è visibile nella scheda <strong>Configura</strong> del portale di gestione di Azure.</p>
            </td>
            <td>
              <p>Questa opzione è visibile solo se è stato scaricato Azure AD Connect ed è stata eseguita la configurazione del writeback della password. Dopo avere eseguito queste operazioni, l'opzione verrà visualizzata consentendo di abilitare o disabilitare il writeback dal cloud.</p>
              <p>Vedere <a href="../active-directory-passwords-getting-started#enable-users-to-reset-or-change-their-ad-passwords">Come abilitare/disabilitare il writeback della password</a> per altre informazioni su come eseguire questa operazione.</p>
            </td>
          </tr>
        </tbody></table>

## Risolvere i problemi relativi ai report di gestione delle password nel portale di gestione di Azure
Se si verifica un errore mentre si usano i report di gestione delle password, è possibile provare a risolverlo attenendosi ai passaggi di risoluzione dei problemi seguenti:

<table>
          <tbody><tr>
            <td>
              <p>
                <strong>Scenario di errore</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Errore visualizzato dall'utente</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Soluzione</strong>
              </p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Non è possibile visualizzare i report di gestione delle password</p>
            </td>
            <td>
              <p>I report <strong>Attività di reimpostazione password</strong> e <strong>Attività di registrazione reimpostazione password</strong> non sono visibili tra i report <strong>Log attività</strong> nella scheda <strong>Report</strong>.</p>
            </td>
            <td>
              <p>Questo problema può verificarsi se all'amministratore che esegue l'operazione non è assegnata una licenza AAD Premium o AAD Basic. </p>
              <p>Per correggere l'errore, assegnare una licenza AAD Premium o AAD Basic all'account amministratore in questione tramite la scheda <strong>Licenze</strong> e riprovare.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Le registrazioni utente vengono visualizzate più volte</p>
            </td>
            <td>
              <p>Quando un utente registra domande di sicurezza, numero di cellulare e indirizzo di posta elettronica alternativi, ogni voce viene visualizzata su righe distinte, anziché su una sola.</p>
            </td>
            <td>
              <p>Attualmente, quando un utente esegue la registrazione, non è possibile presupporre che compilerà tutte le voci presenti nella pagina di registrazione. Di conseguenza, ogni singolo dato inserito viene attualmente registrato come un evento distinto.</p>
              <p>Per aggregare i dati, è possibile scaricare il report e aprire i dati come tabella pivot in Excel per una maggiore flessibilità.</p>
            </td>
          </tr>
        </tbody></table>

## Risolvere i problemi relativi al portale di registrazione per la reimpostazione della password
Se si verifica un errore mentre si registra un utente per la reimpostazione della password, è possibile provare a risolverlo attenendosi ai passaggi di risoluzione dei problemi seguenti:

<table>
          <tbody><tr>
            <td>
              <p>
                <strong>Scenario di errore</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Errore visualizzato dall'utente</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Soluzione</strong>
              </p>
            </td>
          </tr>
          <tr>
            <td>
              <p>La directory non è abilitata per la reimpostazione della password</p>
            </td>
            <td>
              <p>L'amministratore non ha abilitato l'utente all'uso di questa funzionalità.</p>
            </td>
            <td>
              <p>Impostare il flag <strong>Utenti abilitati per la reimpostazione della password</strong> su <strong>Sì</strong> e scegliere <strong>Salva</strong> nella scheda di configurazione della directory nel portale di gestione di Azure. È necessario che all'amministratore che esegue l'operazione sia assegnata una licenza Azure AD Premium o Basic.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>All'utente non è assegnata una licenza AAD Premium o AAD Basic</p>
            </td>
            <td>
              <p>L'amministratore non ha abilitato l'utente all'uso di questa funzionalità.</p>
            </td>
            <td>
              <p>Assegnare all'utente una licenza Azure AD Premium o Azure AD Basic tramite la scheda <strong>Licenze</strong> nel portale di gestione di Azure. È necessario che all'amministratore che esegue l'operazione sia assegnata una licenza Azure AD Premium o Basic.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Errore di elaborazione della richiesta</p>
            </td>
            <td>
              <p>L'utente visualizza un errore che indica:</p>
              <p>
                
              </p>
              <p>Errore di elaborazione della richiesta </p>
              <p>durante il tentativo di reimpostare una password.</p>
            </td>
            <td>
              <p>Questo errore può essere causato da molti problemi, ma in genere è dovuto a un'interruzione del servizio o a un problema di configurazione che non è possibile risolvere. </p>
              <p>Se viene visualizzato questo errore e il suo impatto sull'attività aziendale è significativo, contattare il supporto tecnico che fornirà assistenza nel più breve tempo possibile. Vedere <a href="#information-to-include-when-you-need-help">Informazioni da includere quando è necessaria assistenza</a> per sapere cosa è necessario fornire al personale del supporto tecnico per agevolare una risoluzione rapida.</p>
            </td>
          </tr>
        </tbody></table>

## Risolvere i problemi relativi al portale di reimpostazione della password
Se si verifica un errore mentre si reimposta una password per un utente, è possibile provare a risolverlo attenendosi ai passaggi di risoluzione dei problemi seguenti:

<table>
          <tbody><tr>
            <td>
              <p>
                <strong>Scenario di errore</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Errore visualizzato dall'utente</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Soluzione</strong>
              </p>
            </td>
          </tr>
          <tr>
            <td>
              <p>La directory non è abilitata per la reimpostazione della password</p>
            </td>
            <td>
              <p>Account non abilitato per la reimpostazione della password</p>
              <p>L'amministratore non ha configurato l'account per l'utilizzo di questo servizio. </p>
              <p>
                
              </p>
              <p>Se necessario, Microsoft può contattare un amministratore dell'organizzazione e richiedere la reimpostazione della password.</p>
            </td>
            <td>
              <p>Impostare il flag <strong>Utenti abilitati per la reimpostazione della password</strong> su <strong>Sì</strong> e scegliere <strong>Salva</strong> nella scheda di configurazione della directory nel portale di gestione di Azure. È necessario che all'amministratore che esegue l'operazione sia assegnata una licenza Azure AD Premium o Basic.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>All'utente non è assegnata una licenza AAD Premium o AAD Basic</p>
            </td>
            <td>
              <p>Le password degli account non amministrativi non possono essere reimpostate automaticamente, tuttavia è possibile contattare l'amministratore dell'organizzazione per richiedere la reimpostazione.</p>
            </td>
            <td>
              <p>Assegnare all'utente una licenza Azure AD Premium o Azure AD Basic tramite la scheda <strong>Licenze</strong> nel portale di gestione di Azure. È necessario che all'amministratore che esegue l'operazione sia assegnata una licenza Azure AD Premium o Basic.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>La directory è abilitata per la reimpostazione della password, ma le informazioni di autenticazione dell'utente sono mancanti o errate.</p>
            </td>
            <td>
              <p>Account non abilitato per la reimpostazione della password</p>
              <p>L'amministratore non ha configurato l'account per l'utilizzo di questo servizio. </p>
              <p>
                
              </p>
              <p>Se necessario, Microsoft può contattare un amministratore dell'organizzazione e richiedere la reimpostazione della password.</p>
            </td>
            <td>
              <p>Prima di procedere, verificare che i dati di contatto dell'utente archiviati nella directory siano corretti. Vedere <a href="../active-directory-passwords-learn-more#what-data-is-used-by-password-reset">Dati usati per la reimpostazione della password</a> per informazioni su come configurare le informazioni di autenticazione nella directory in modo che non venga visualizzato questo errore.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>La directory è abilitata per la reimpostazione della password, ma per l'utente è archiviato un solo metodo di contatto mentre i criteri sono impostati in modo da richiedere due passaggi di verifica</p>
            </td>
            <td>
              <p>Account non abilitato per la reimpostazione della password</p>
              <p>L'amministratore non ha configurato l'account per l'utilizzo di questo servizio. </p>
              <p>
                
              </p>
              <p>Se necessario, Microsoft può contattare un amministratore dell'organizzazione e richiedere la reimpostazione della password.</p>
            </td>
            <td>
              <p>Prima di procedere verificare che per l'utente siano disponibili almeno due metodi di contatto configurati correttamente, (ad esempio Cellulare e Telefono ufficio). Vedere <a href="../active-directory-passwords-learn-more#what-data-is-used-by-password-reset">Dati usati per la reimpostazione della password</a> per informazioni su come configurare le informazioni di autenticazione nella directory in modo che non venga visualizzato questo errore.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>La directory è abilitata per la reimpostazione della password, ma non è possibile contattare l'utente anche se questo è configurato correttamente </p>
            </td>
            <td>
              <p>Errore imprevisto durante il tentativo di contattare l'utente.</p>
            </td>
            <td>
              <p>Questo errore può essere causato da un problema temporaneo del servizio o da dati di contatto configurati in modo errato che non sono stati rilevati correttamente. Se l'utente attende 10 secondi, verrà visualizzato un messaggio in cui si chiede di riprovare, con un collegamento per «contattare l'amministratore». Facendo clic sull'opzione per riprovare, la chiamata verrà nuovamente inviata, mentre facendo clic sul collegamento per «contattare l'amministratore» verrà inviato tramite posta elettronica agli amministratori utenti, password o globali (in tale ordine di precedenza) un modulo per richiedere la reimpostazione della password per tale account utente.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>L'utente non riceve mai un SMS o una telefonata per la reimpostazione della password</p>
            </td>
            <td>
              <p>L'utente fa clic su "Invia messaggio di testo" o "Chiama utente", ma non riceve mai il messaggio o la chiamata.</p>
            </td>
            <td>
              <p>Il numero di telefono nella directory potrebbe non essere corretto. Verificare che il numero di telefono sia nel formato "+ccc xxxyyyzzzzXeeee". Per altre informazioni sulla formattazione dei numeri di telefono da usare con la reimpostazione della password, vedere <a href="../active-directory-passwords-learn-more#what-data-is-used-by-password-reset">Dati usati per la reimpostazione della password</a>.</p>
              <p>Se è necessario un interno per contattare l'utente in questione, tenere presente che la reimpostazione della password non supporta numeri interni, anche se ne viene specificato uno nella directory (vengono rimossi prima dell'invio della chiamata). Provare a usare un numero senza interno o a integrare l'interno nel numero di telefono nel proprio PBX.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>L'utente non riceve mai il messaggio di posta elettronica relativo alla reimpostazione della password</p>
            </td>
            <td>
              <p>L'utente fa clic su "Invia messaggio di posta elettronica all'utente", ma non riceve mai il messaggio.</p>
            </td>
            <td>
              <p>La causa più comune di questo problema è il rifiuto del messaggio da parte di un filtro protezione da posta indesiderata. Cercare il messaggio di posta elettronica nella cartella della posta indesiderata o della posta eliminata.</p>
              <p>Assicurarsi anche di cercare il messaggio nell'indirizzo di posta elettronica corretto. Molte persone usano indirizzi di posta elettronica molto simili e cercano il messaggio nella cartella Posta in arrivo sbagliata. Se nessuna di queste opzioni funziona, è anche possibile che l'indirizzo di posta elettronica nella directory non sia corretto. Assicurarsi che l'indirizzo di posta elettronica sia quello corretto e quindi riprovare. Per altre informazioni sulla formattazione degli indirizzi di posta elettronica da usare con la reimpostazione della password, vedere <a href="../active-directory-passwords-learn-more#what-data-is-used-by-password-reset">Dati usati per la reimpostazione della password</a>.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Sono stati impostati criteri di reimpostazione password, ma quando la funzionalità viene usata con un account amministratore i criteri non vengono applicati</p>
            </td>
            <td>
              <p>Per gli account amministratore che reimpostano le password vengono visualizzate le stesse opzioni abilitate per reimpostazione della password, indirizzo di posta elettronica e numero di cellulare, indipendentemente dai criteri impostati nella sezione <strong>Criteri di reimpostazione password utente</strong> della scheda <strong>Configura</strong>.</p>
            </td>
            <td>
              <p>Le opzioni configurate nella sezione <strong>Criteri di reimpostazione password utente</strong> della scheda <strong>Configura</strong> si applicano solo agli utenti finali dell'organizzazione.</p>
              <p>Microsoft gestisce e controlla i criteri di reimpostazione delle password amministratore per assicurare il massimo livello di sicurezza.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>All'utente viene impedito di provare a reimpostare la password troppe volte in uno stesso giorno</p>
            </td>
            <td>
              <p>L'utente visualizza un errore che indica:</p>
              <p>
                
              </p>
              <p>Utilizzare un'altra opzione.</p>
              <p>Sono stati eseguiti troppi tentativi di verifica dell'account nell'ultima ora. Per motivi di sicurezza, è necessario aspettare 24 ore prima di riprovare. </p>
              <p>Se necessario, Microsoft può contattare un amministratore dell'organizzazione e richiedere la reimpostazione della password.</p>
            </td>
            <td>
              <p>Per impedire agli utenti di provare a reimpostare le password troppe volte in un breve intervallo di tempo, viene applicato un meccanismo di limitazione automatico. Il meccanismo viene applicato quando:</p>
              <ol class="ordered">
                <li>
										L'utente tenta di convalidare un numero di telefono 5 volte in un'ora.&lt;br>&lt;br></li>
                <li>
										L'utente tenta di usare il controllo per le domande di sicurezza 5 volte in un'ora.&lt;br>&lt;br></li>
                <li>
										L'utente tenta di reimpostare una password per lo stesso account utente 5 volte in un'ora.&lt;br>&lt;br></li>
              </ol>
              <p>Per correggere l'errore, chiedere all'utente di attendere 24 ore dopo l'ultimo tentativo, dopodiché sarà in grado di reimpostare la password.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>L'utente visualizza un errore quando convalida il numero di telefono</p>
            </td>
            <td>
              <p>Quando l'utente tenta di verificare un numero di telefono da usare come metodo di autenticazione, visualizza un errore che indica:</p>
              <p>
                
              </p>
              <p>Il numero di telefono specificato non è corretto.</p>
            </td>
            <td>
              <p>Questo errore si verifica quando il numero di telefono immesso non corrisponde a quello archiviato.</p>
              <p>Assicurarsi che l'utente immetta il numero di telefono completo, inclusi il codice paese e il prefisso, quando tenta di usare un metodo basato sul telefono per la reimpostazione della password.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Errore di elaborazione della richiesta</p>
            </td>
            <td>
              <p>L'utente visualizza un errore che indica:</p>
              <p>
                
              </p>
              <p>Errore di elaborazione della richiesta </p>
              <p>durante il tentativo di reimpostare una password.</p>
            </td>
            <td>
              <p>Questo errore può essere causato da molti problemi, ma in genere è dovuto a un'interruzione del servizio o a un problema di configurazione che non è possibile risolvere. </p>
              <p>Se viene visualizzato questo errore e il suo impatto sull'attività aziendale è significativo, contattare il supporto tecnico che fornirà assistenza nel più breve tempo possibile. Vedere <a href="#information-to-include-when-you-need-help">Informazioni da includere quando è necessaria assistenza</a> per sapere cosa è necessario fornire al personale del supporto tecnico per agevolare una risoluzione rapida.</p>
            </td>
          </tr>
        </tbody></table>

## Risolvere i problemi relativi al writeback della password
Se si verifica un errore mentre si abilita, disabilita o usa il writeback della password, è possibile provare a risolverlo attenendosi ai passaggi di risoluzione dei problemi seguenti:

<table>
          <tbody><tr>
            <td>
              <p>
                <strong>Scenario di errore</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Errore visualizzato dall'utente</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Soluzione</strong>
              </p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Errori generali di caricamento e avvio</p>
            </td>
            <td>
              <p>Il servizio di reimpostazione della password non si avvia in locale con l'errore 6800 nel registro eventi dell'applicazione del computer che esegue Azure AD Connect.</p>
              <p>
                
              </p>
              <p>Dopo il caricamento, gli utenti federati o con sincronizzazione di hash della password non possono reimpostare le password.</p>
            </td>
            <td>
              <p>Quando è abilitato il writeback della password, il motore di sincronizzazione chiama la libreria di writeback per eseguire la configurazione (caricamento) interagendo con il servizio di caricamento cloud. Gli eventuali errori che si verificano durante il caricamento o durante l'avvio dell'endpoint WCF per il writeback della password saranno riflessi nel registro eventi del computer che esegue Azure AD Connect.</p>
              <p>Se è stato configurato il writeback, durante il riavvio del servizio ADSync verrà avviato l'endpoint WCF. Se tuttavia l'avvio dell'endpoint non riesce, il servizio di sincronizzazione viene avviato e viene semplicemente registrato l'evento 6800. La presenza di questo evento indica che l'endpoint di writeback della password non è stato avviato. I dettagli del registro eventi per questo evento (6800) e le voci del registro eventi generate dal componente PasswordResetService indicheranno i motivi per cui non è stato possibile avviare l'endpoint. Esaminare gli errori nel registro eventi e provare a riavviare Azure AD Connect se il writeback della password ancora non funziona. Se il problema persiste, provare a disabilitare e riabilitare il writeback della password.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Errore di configurazione del writeback durante l'installazione di Azure AD Connect</p>
            </td>
            <td>
              <p>Nell'ultimo passaggio del processo di installazione di Azure AD Connect viene visualizzato un errore che indica che non è stato possibile configurare il writeback della password.</p>
              <p>
                
              </p>
              <p>Il registro eventi dell'applicazione Azure AD Connect contiene l'errore 32009 il cui testo indica che non è stato possibile recuperare il token di autenticazione.</p>
            </td>
            <td>
              <p>Questo errore si verifica nei due casi seguenti:</p>
              <ul>
                <li class="unordered">
										È stata specificata una password non corretta per l'account amministratore globale specificato all'inizio del processo di installazione di Azure AD Connect.&lt;br>&lt;br></li>
              </ul>
              <ul>
                <li class="unordered">
										È stato eseguito un tentativo di usare un utente federato per l'account amministratore globale specificato all'inizio del processo di installazione di Azure AD Connect.&lt;br>&lt;br></li>
              </ul>
              <p>Per correggere l'errore, accertarsi di non usare un account federato per l'amministratore globale specificato all'inizio del processo di installazione di Azure AD Connect e che la password specificata sia corretta.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Errore di configurazione del writeback durante l'installazione di Azure AD Connect</p>
            </td>
            <td>
              <p>Il registro eventi del computer che esegue Azure AD Connect contiene l'errore 32002 generato da PasswordResetService.</p>
              <p>
                
              </p>
              <p>L'errore indica: "Errore di connessione al bus di servizio. Il provider del token non è stato in grado di fornire un token di sicurezza".</p>
              <p>
                
              </p>
            </td>
            <td>
              <p>La causa principale di questo errore è dovuta al fatto che il servizio di reimpostazione della password in esecuzione nell'ambiente locale non è in grado di connettersi all'endpoint del bus di servizio nel cloud. Questo errore è in genere causato da una regola del firewall che blocca una connessione in uscita a una porta o a un indirizzo Web specifico.</p>
              <p>
                
              </p>
              <p>Accertarsi che il firewall consenta le connessioni in uscita per:</p>
              <ul>
                <li class="unordered">
										Tutto il traffico su TCP 443 (HTTPS)&lt;br>&lt;br></li>
              </ul>
              <ul>
                <li class="unordered">
										Le connessioni in uscita &lt;br>&lt;br></li>
              </ul>
              <p>
                
              </p>
              <p>Dopo aver aggiornato queste regole, riavviare il computer che esegue Azure AD Connect. Il writeback della password dovrebbe funzionare di nuovo.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>L'endpoint di writeback della password locale non è raggiungibile</p>
            </td>
            <td>
              <p>Dopo un certo periodo di tempo, gli utenti federati o con sincronizzazione di hash della password non possono reimpostare le password.</p>
            </td>
            <td>
              <p>In rari casi, è possibile che il servizio di writeback della password non venga riavviato quando si riavvia Azure AD Connect. In questi casi, controllare innanzitutto se il writeback della password risulta abilitato in locale. A tale scopo, è possibile usare la procedura guidata di Azure AD Connect o PowerShell (vedere la sezione precedente relativa alle procedure). Se la funzionalità appare abilitata, provare ad abilitarla o a disabilitarla di nuovo tramite l'interfaccia utente o PowerShell. Per altre informazioni su come eseguire questa operazione, vedere il passaggio 2, relativo a come abilitare il writeback della password nel computer di sincronizzazione della directory e configurare le regole del firewall, in <a href="../active-directory-passwords-getting-started#enable-users-to-reset-or-change-their-ad-passwords">Come abilitare/disabilitare il writeback della password</a></p>
              <p>
                
              </p>
              <p>Se il problema persiste, provare a disinstallare completamente e a reinstallare Azure AD Connect.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Errori di autorizzazione</p>
            </td>
            <td>
              <p>Gli utenti federati o con sincronizzazione di hash della password che tentano di reimpostare le password visualizzano un errore, dopo l'invio della password, che indica che si è verificato un problema del servizio.</p>
              <p>
                
              </p>
              <p>Inoltre, durante le operazioni di reimpostazione della password è possibile che nei registri eventi locali venga visualizzato un errore di accesso negato per l'agente di gestione.</p>
            </td>
            <td>
              <p>Se nel registro eventi sono presenti errori di questo tipo, verificare che l'account dell'agente di gestione AD (specificato nella procedura guidata al momento della configurazione) disponga delle autorizzazioni necessarie per il writeback della password.</p>
              <p>
                
              </p>
              <p>NOTA: dopo aver concesso l'autorizzazione, la relativa propagazione nel controller di dominio tramite l'attività in background sdprop può richiedere fino a un'ora. </p>
              <p>Per il funzionamento della reimpostazione della password, è necessario che l'autorizzazione sia indicata nel descrittore di sicurezza dell'oggetto utente per il quale viene reimpostata la password. Finché l'autorizzazione non sarà indicata nell'oggetto utente, per la reimpostazione della password continuerà a verificarsi un errore di accesso negato.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Errore durante la configurazione del writeback della password dalla configurazione guidata di Azure AD Connect </p>
            </td>
            <td>
              <p>Errore relativo all’«impossibilità di individuare l'agente di gestione» nella procedura guidata durante l'abilitazione/disabilitazione del writeback della password.</p>
            </td>
            <td>
              <p>C'è un bug noto nella versione rilasciata di Azure AD Connect che si manifesta nella situazione seguente:</p>
              <ol class="ordered">
                <li>
										Si configura Azure AD Connect per il tenant abc.com (dominio verificato) usando le credenziali. Di conseguenza, viene creato il connettore AAD denominato "abc.com - AAD".&lt;br>&lt;br></li>
                <li>
										Si modificano quindi le credenziali di AAD per il connettore, tramite l'interfaccia utente di sincronizzazione precedente (si noti che si tratta dello stesso tenant, ma di un nome di dominio diverso). &lt;br>&lt;br></li>
                <li>
										A questo punto si tenta di abilitare/disabilitare il writeback della password. La procedura guidata costruirà il nome del connettore, "abc.onmicrosoft.com - AAD", usando le credenziali e lo passerà al cmdlet per il writeback della password. L'operazione non riuscirà perché non esiste un connettore creato con questo nome.&lt;br>&lt;br></li>
              </ol>
              <p>Tale problema è stato risolto nelle build più recenti. Se si dispone di una build precedente, l'unica soluzione alternativa consiste nell'usare il cmdlet di PowerShell per abilitare/disabilitare la funzionalità. Per altre informazioni su come eseguire questa operazione, vedere il passaggio 2, relativo a come abilitare il writeback della password nel computer di sincronizzazione della directory e configurare le regole del firewall, in <a href="../active-directory-passwords-getting-started#enable-users-to-reset-or-change-their-ad-passwords">Come abilitare/disabilitare il writeback della password</a></p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Non è possibile reimpostare la password per gli utenti di gruppi speciali, ad esempio Domain Admins/Enterprise Admins e così via.</p>
            </td>
            <td>
              <p>Gli utenti federati o con sincronizzazione di hash della password che fanno parte di gruppi protetti e che tentano di reimpostare le password visualizzano un errore, dopo l'invio della password, che indica che si è verificato un problema del servizio.</p>
            </td>
            <td>
              <p>Gli utenti con privilegi in Active Directory sono protetti mediante AdminSDHolder. Per altre informazioni, vedere <a href="https://technet.microsoft.com/magazine/2009.09.sdadminholder.aspx">http://technet.microsoft.com/magazine/2009.09.sdadminholder.aspx</a>. </p>
              <p>
                
              </p>
              <p>Ciò significa che i descrittori di sicurezza di questi oggetti vengono verificati periodicamente per controllare se corrispondono a quello specificato in AdminSDHolder e, se sono diversi, vengono reimpostati. Le autorizzazioni aggiuntive necessarie per il writeback della password non vengono perciò propagate a questi utenti. Ciò può comportare il mancato funzionamento del writeback della password per tali utenti. Di conseguenza, la gestione delle password per gli utenti appartenenti a questi gruppi non è supportata, in quanto non rispetta il modello di sicurezza di AD.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Le operazioni di reimpostazione non riescono per l'impossibilità di trovare l'oggetto</p>
            </td>
            <td>
              <p>Gli utenti federati o con sincronizzazione di hash della password che tentano di reimpostare le password visualizzano un errore, dopo l'invio della password, che indica che si è verificato un problema del servizio.</p>
              <p>
                
              </p>
              <p>Inoltre, durante le operazioni di reimpostazione della password, è possibile che venga visualizzato un errore nei registri eventi del servizio Azure AD Connect che indica che non è stato possibile trovare l'oggetto.</p>
            </td>
            <td>
              <p>Questo errore indica di solito che il motore di sincronizzazione non è in grado di trovare l'oggetto utente nello spazio connettore AAD o nell'oggetto spazio connettore AD o MV collegato. </p>
              <p>
                
              </p>
              <p>Per risolvere il problema, assicurarsi che l'utente sia effettivamente sincronizzato dall'ambiente locale ad AAD tramite l'istanza corrente di Azure AD Connect e controllare lo stato degli oggetti negli spazi connettore e nell'oggetto MV. Verificare che l'oggetto Servizi certificati Active Directory sia connesso all'oggetto MV tramite la regola "Microsoft.InfromADUserAccountEnabled.xxx".</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Le operazioni di reimpostazione non riescono a causa del rilevamento di più corrispondenze</p>
            </td>
            <td>
              <p>Gli utenti federati o con sincronizzazione di hash della password che tentano di reimpostare le password visualizzano un errore, dopo l'invio della password, che indica che si è verificato un problema del servizio.</p>
              <p>
                
              </p>
              <p>Inoltre, durante le operazioni di reimpostazione della password, è possibile che venga visualizzato un errore nei registri eventi del servizio Azure AD Connect che indica che sono state trovate più corrispondenze.</p>
            </td>
            <td>
              <p>Ciò indica che il motore di sincronizzazione ha rilevato che l'oggetto MV è connesso a più oggetti Servizi certificati Active Directory tramite "Microsoft.InfromADUserAccountEnabled.xxx". Questo significa che l'utente dispone di un account abilitato in più foreste. </p>
              <p>
                
              </p>
              <p>Questo scenario non è attualmente supportato per il writeback della password.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Le operazioni con le password non riescono a causa di un errore di configurazione.</p>
            </td>
            <td>
              <p>Le operazioni con le password non riescono a causa di un errore di configurazione. Il registro eventi dell'applicazione contiene l'errore 6329 di Azure AD Connect con un testo simile al seguente: 0x8023061f (Operazione non riuscita perché la sincronizzazione della password non è abilitata in questo agente di gestione).</p>
            </td>
            <td>
              <p>Ciò si verifica se si modifica la configurazione di Azure AD Connect per aggiungere una nuova foresta AD (o per rimuovere e aggiungere nuovamente una foresta esistente) <strong>dopo</strong> avere abilitato la funzionalità di writeback della password. Le operazioni con le password per gli utenti in queste foreste appena aggiunte avranno esito negativo. Per risolvere il problema, disabilitare e riabilitare la funzionalità di writeback della password dopo avere completato le modifiche di configurazione della foresta.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Il writeback delle password che sono state reimpostate dagli utenti funziona correttamente, ma il writeback delle password modificate da un utente o reimpostate per un utente da un amministratore ha esito negativo.</p>
            </td>
            <td>
              <p>Quando si tenta di reimpostare una password per conto di un utente dal portale di gestione di Azure, viene visualizzato il messaggio seguente: "Il servizio di reimpostazione delle password in esecuzione nell'ambiente locale non supporta la reimpostazione delle password utente da parte degli amministratori. Per risolvere il problema, eseguire l'aggiornamento alla versione più recente di Azure AD Connect".</p>
            </td>
            <td>
              <p>Ciò si verifica quando la versione del motore di sincronizzazione non supporta l'operazione di writeback della password specifica che è stata usata. Le versioni di Azure AD Connect successive alla 1.0.0419.0911 supportano tutte le operazioni di gestione delle password, incluso il writeback di reimpostazione della password, il writeback di modifica della password e il writeback di reimpostazione della password avviato dall'amministratore dal portale di gestione di Azure.&#160; Le versioni di DirSync successive alla 1.0.6862 supportano solo il writeback di reimpostazione della password. Per risolvere questo problema, è consigliabile installare la versione più recente di Azure AD Connect o Azure Active Directory Connect (per altre informazioni, vedere <a href="../active-directory-aadconnect#download-azure-ad-connect">Strumenti di integrazione di directory</a>). In questo modo, sarà anche possibile usare al meglio il writeback della password nell'organizzazione.</p>
            </td>
          </tr>
        </tbody></table>


## Codici errore del registro eventi di writeback della password
Una procedura consigliata per la risoluzione dei problemi relativi al writeback della password consiste nell'esaminare il registro eventi dell'applicazione nel computer che esegue Azure AD Connect. Questo registro conterrà eventi di due origini di interesse per il writeback della password. L'origine PasswordResetService descrive le operazioni e i problemi correlati all'operazione di writeback della password. L'origine ADSync descrive le operazioni e i problemi correlati all'impostazione delle password nell'ambiente AD.

<table>
          <tbody><tr>
            <td>
              <p>
                <strong>Codice</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Nome/Messaggio</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Origine</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Descrizione</strong>
              </p>
            </td>
          </tr>
          <tr>
            <td>
              <p>6329</p>
            </td>
            <td>
              <p>BAIL: MMS(4924) 0x80230619 - "Una restrizione impedisce la modifica della password in quella corrente specificata."</p>
            </td>
            <td>
              <p>ADSync</p>
            </td>
            <td>
              <p>Questo evento si verifica quando il servizio di writeback della password tenta di impostare una password nella directory locale che non soddisfa i requisiti di validità, cronologia, complessità o filtro del dominio.</p>
              <ul>
                <li class="unordered">
										Se è prevista una validità minima della password e di recente la password è stata modificata in tale intervallo di tempo, non sarà possibile modificarla di nuovo finché non si raggiunge il periodo di validità specificato nel dominio. A scopo di test, è consigliabile impostare la validità minima su 0.&lt;br>&lt;br></li>
              </ul>
              <ul>
                <li class="unordered">
										Se sono abilitati requisiti per la cronologia delle password, sarà necessario selezionare una password che non sia stata usata nelle ultime N volte, dove N è l'impostazione della cronologia delle password. Se si seleziona una password che è stata usata nelle ultime N volte, si verifica un errore. A scopo di test, è consigliabile impostare la cronologia su 0.&lt;br>&lt;br></li>
              </ul>
              <ul>
                <li class="unordered">
										Se sono abilitati requisiti di complessità della password, verranno applicati tutti quando l'utente tenta di modificare o reimpostare la password.&lt;br>&lt;br></li>
              </ul>
              <ul>
                <li class="unordered">
										Se sono abilitati filtri delle password e un utente sceglie una password che non soddisfa i criteri di filtro, l'operazione di reimpostazione o di modifica non riuscirà.&lt;br>&lt;br></li>
              </ul>
            </td>
          </tr>
          <tr>
            <td>
              <p>HR 8023042</p>
            </td>
            <td>
              <p>Il motore di sincronizzazione ha restituito un errore hr = 80230402, messaggio = Tentativo di ottenere un oggetto non riuscito. Sono presenti voci duplicate con lo stesso ancoraggio</p>
            </td>
            <td>
              <p>ADSync</p>
            </td>
            <td>
              <p>Questo evento si verifica quando lo stesso id utente è abilitato in più domini. Ad esempio, se si esegue la sincronizzazione di foreste di Account/Resource e lo stesso id utente è presente e abilitato in ciascuna di esse, questo errore può verificarsi.  </p>
              <p>Questo errore può verificarsi anche se si utilizza un attributo di ancoraggio non univoco (come alias o UPN) e due utenti condividono lo stesso attributo di ancoraggio.</p>
              <p>Per risolvere questo problema, assicurarsi di non avere alcun utente duplicato all'interno dei domini e di utilizzare un attributo di ancoraggio univoco per ogni utente.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31001</p>
            </td>
            <td>
              <p>PasswordResetStart </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Questo evento indica che il servizio locale ha rilevato una richiesta di reimpostazione della password per un utente federato o con sincronizzazione di hash della password originata dal cloud. Si tratta del primo evento di ogni operazione di writeback per la reimpostazione della password.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31002</p>
            </td>
            <td>
              <p>PasswordResetSuccess </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Questo evento indica che un utente ha selezionato una nuova password durante un'operazione di reimpostazione della password, è stato determinato che la password soddisfa i relativi requisiti aziendali e la password è stata scritta correttamente nell'ambiente AD locale.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31003</p>
            </td>
            <td>
              <p>PasswordResetFail </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Questo evento indica che un utente ha selezionato una password che è pervenuta correttamente all'ambiente locale, ma quando si è tentato di impostare la password nell'ambiente AD locale si è verificato un errore. Questo problema può verificarsi per diversi motivi:</p>
              <ul>
                <li class="unordered">
										La password dell'utente non soddisfa i requisiti di validità, cronologia, complessità o filtro per il dominio. Per risolvere il problema, provare una password completamente nuova.&lt;br>&lt;br></li>
              </ul>
              <ul>
                <li class="unordered">
										L'account del servizio dell'agente di gestione non dispone delle autorizzazioni appropriate per impostare la nuova password nell'account utente in questione.&lt;br>&lt;br></li>
              </ul>
              <ul>
                <li class="unordered">
										L'account dell'utente appartiene a un gruppo protetto, ad esempio Domain Admins o Enterprise Admins, che non consente operazioni di impostazione delle password.&lt;br>&lt;br></li>
              </ul>
              <p>Per altre informazioni su altre situazioni che possono causare questo errore, vedere <a href="#troubleshoot-password-writeback">Risolvere i problemi relativi al writeback della password</a>.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31004</p>
            </td>
            <td>
              <p>OnboardingEventStart </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Questo evento si verifica se si abilita il writeback della password con Azure AD Connect e indica che è stato avviato il caricamento dell'organizzazione nel servizio Web di writeback della password.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31005</p>
            </td>
            <td>
              <p>OnboardingEventSuccess </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Questo evento indica che il processo di caricamento è stato completato e che la funzionalità di writeback della password è pronta per l'uso.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31006</p>
            </td>
            <td>
              <p>ChangePasswordStart </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Questo evento indica che il servizio locale ha rilevato una richiesta di modifica della password per un utente federato o con sincronizzazione di hash della password originata dal cloud. Si tratta del primo evento di ogni operazione di writeback per la modifica della password.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31007</p>
            </td>
            <td>
              <p>ChangePasswordSuccess </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Questo evento indica che un utente ha selezionato una nuova password durante un'operazione di modifica della password, è stato determinato che la password soddisfa i relativi requisiti aziendali e la password è stata scritta correttamente nell'ambiente AD locale.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31008</p>
            </td>
            <td>
              <p>ChangePasswordFail </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Questo evento indica che un utente ha selezionato una password che è pervenuta correttamente all'ambiente locale, ma quando si è tentato di impostare la password nell'ambiente AD locale si è verificato un errore. Questo problema può verificarsi per diversi motivi:</p>
              <ul>
                <li class="unordered">
										La password dell'utente non soddisfa i requisiti di validità, cronologia, complessità o filtro per il dominio. Per risolvere il problema, provare una password completamente nuova.&lt;br>&lt;br></li>
              </ul>
              <ul>
                <li class="unordered">
										L'account del servizio dell'agente di gestione non dispone delle autorizzazioni appropriate per impostare la nuova password nell'account utente in questione.&lt;br>&lt;br></li>
              </ul>
              <ul>
                <li class="unordered">
										L'account dell'utente appartiene a un gruppo protetto, ad esempio Domain Admins o Enterprise Admins, che non consente operazioni di impostazione delle password.&lt;br>&lt;br></li>
              </ul>
              <p>Per altre informazioni su altre situazioni che possono causare questo errore, vedere <a href="#troubleshoot-password-writeback">Risolvere i problemi relativi al writeback della password</a>.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31009</p>
            </td>
            <td>
              <p>ResetUserPasswordByAdminStart </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Il servizio locale ha rilevato una richiesta di reimpostazione della password per un utente federato o con sincronizzazione di hash della password originata dall'amministratore per conto di un utente. Si tratta del primo evento di ogni operazione di writeback per la reimpostazione della password avviata dall'amministratore.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31010</p>
            </td>
            <td>
              <p>ResetUserPasswordByAdminSuccess </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>L'amministratore ha selezionato una nuova password durante un'operazione di reimpostazione della password avviata dall'amministratore, è stato determinato che la password soddisfa i relativi requisiti aziendali e la password è stata scritta correttamente nell'ambiente AD locale.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31011</p>
            </td>
            <td>
              <p>ResetUserPasswordByAdminFail </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>L'amministratore ha selezionato una password per conto di un utente, che è pervenuta correttamente all'ambiente locale, ma quando si è tentato di impostare la password nell'ambiente AD locale si è verificato un errore. Questo problema può verificarsi per diversi motivi:</p>
              <ul>
                <li class="unordered">
										La password dell'utente non soddisfa i requisiti di validità, cronologia, complessità o filtro per il dominio. Per risolvere il problema, provare una password completamente nuova.&lt;br>&lt;br></li>
              </ul>
              <ul>
                <li class="unordered">
										L'account del servizio dell'agente di gestione non dispone delle autorizzazioni appropriate per impostare la nuova password nell'account utente in questione.&lt;br>&lt;br></li>
              </ul>
              <ul>
                <li class="unordered">
										L'account dell'utente appartiene a un gruppo protetto, ad esempio Domain Admins o Enterprise Admins, che non consente operazioni di impostazione delle password.&lt;br>&lt;br></li>
              </ul>
              <p>Per altre informazioni su altre situazioni che possono causare questo errore, vedere <a href="#troubleshoot-password-writeback">Risolvere i problemi relativi al writeback della password</a>.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31012</p>
            </td>
            <td>
              <p>OffboardingEventStart </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Questo evento si verifica se si disabilita il writeback della password con Azure AD Connect e indica che è stato avviato lo scaricamento dell'organizzazione nel servizio Web di writeback della password.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31013</p>
            </td>
            <td>
              <p>OffboardingEventSuccess </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Questo evento indica che il processo di scaricamento è stato completato e che la funzionalità di writeback della password è stata disabilitata.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31014</p>
            </td>
            <td>
              <p>OffboardingEventFail </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Questo evento indica che il processo di scaricamento non è riuscito. Il problema potrebbe essere causato da un errore di autorizzazione nell'account amministratore locale o cloud specificato durante la configurazione o dal tentativo di usare un account amministratore globale cloud federato quando si disabilita il writeback della password. Per risolvere il problema, controllare le autorizzazioni amministrative e verificare che non venga usato un account federato durante la configurazione della funzionalità di writeback della password.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31015</p>
            </td>
            <td>
              <p>WriteBackServiceStarted </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Questo evento indica che il servizio di writeback della password è stato avviato correttamente ed è pronto ad accettare le richieste di gestione delle password dal cloud.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31016</p>
            </td>
            <td>
              <p>WriteBackServiceStopped </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Questo evento indica che il servizio di writeback della password è stato arrestato e che le richieste di gestione delle password dal cloud non riusciranno.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31017</p>
            </td>
            <td>
              <p>AuthTokenSuccess </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Questo evento indica che è stato recuperato un token di autorizzazione per l'amministratore globale specificato durante l'installazione di Azure AD Connect per avviare il processo di scaricamento o caricamento.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31018</p>
            </td>
            <td>
              <p>KeyPairCreationSuccess </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Questo evento indica che è stata creata la chiave di crittografia password che verrà usata per crittografare le password dal cloud da inviare all'ambiente locale.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32000</p>
            </td>
            <td>
              <p>UnknownError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Questo evento indica un errore sconosciuto durante un'operazione di gestione delle password. Per altri dettagli, fare riferimento al testo dell'eccezione. In caso di problemi, provare a disabilitare e riabilitare il writeback della password. Se il problema persiste, includere una copia del registro eventi e le informazioni interne specificate nell'ID di traccia da inviare al personale del supporto tecnico.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32001</p>
            </td>
            <td>
              <p>ServiceError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Questo evento indica che si è verificato un errore di connessione al servizio cloud di reimpostazione della password, che in genere viene generato quando il servizio locale non è riuscito a connettersi al servizio Web di reimpostazione della password. </p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32002</p>
            </td>
            <td>
              <p>ServiceBusError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Questo evento indica che si è verificato un errore di connessione all'istanza del bus di servizio del tenant. È possibile che le connessioni in uscita nell'ambiente locale siano bloccate. Verificare che nel firewall siano consentite le connessioni su TCP 443 e verso <a href="https://ssprsbprodncu-sb.accesscontrol.windows.net/">https://ssprsbprodncu-sb.accesscontrol.windows.net/</a> e riprovare. Se i problemi persistono, provare a disabilitare e riabilitare il writeback della password.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32003</p>
            </td>
            <td>
              <p>InPutValidationError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Questo evento indica che l'input passato all'API del servizio Web non è valido. Riprovare a eseguire l'operazione.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32004</p>
            </td>
            <td>
              <p>DecryptionError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Questo evento indica che si è verificato un errore di decrittografia della password ricevuta dal cloud. La causa potrebbe essere una mancata corrispondenza della chiave di decrittografia tra il servizio cloud e l'ambiente locale. Per risolvere il problema, disabilitare e riabilitare il writeback della password nell'ambiente locale.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32005</p>
            </td>
            <td>
              <p>ConfigurationError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Durante il caricamento, le informazioni specifiche del tenant vengono salvate in un file di configurazione nell'ambiente locale. Questo evento indica che si è verificato un errore durante il salvataggio del file o che durante l'avvio del servizio si è verificato un errore di lettura del file. Per risolvere il problema, provare a disabilitare e riabilitare il writeback della password per forzare la riscrittura del file di configurazione. </p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32006</p>
            </td>
            <td>
              <p>EndPointConfigurationError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>DEPRECATO: questo evento non è presente in Azure AD Connect, ma solo nelle prime build di DirSync che supportano il writeback.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32007</p>
            </td>
            <td>
              <p>OnBoardingConfigUpdateError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Durante il caricamento, i dati vengono inviati dal cloud al servizio di reimpostazione della password locale. I dati vengono quindi scritti in un file in memoria prima di essere inviati al servizio di sincronizzazione per l'archiviazione sicura delle informazioni su disco. Questo evento indica un problema di scrittura o aggiornamento dei dati in memoria. Per risolvere il problema, provare a disabilitare e riabilitare il writeback della password per forzare la riscrittura della configurazione.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32008</p>
            </td>
            <td>
              <p>ValidationError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Questo evento indica che è stata ricevuta una risposta non valida dal servizio Web di reimpostazione della password. Per risolvere il problema, provare a disabilitare e riabilitare il writeback della password.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32009</p>
            </td>
            <td>
              <p>AuthTokenError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Questo evento indica che non è stato possibile ottenere un token di autorizzazione per l'account amministratore globale specificato durante l'installazione di Azure AD Connect. L'errore può essere causato da una password o un nome utente non valido specificato per l'account amministratore globale o perché è stato specificato un account amministratore globale federato. Per risolvere il problema, ripetere la configurazione con il nome utente e la password corretti e verificare che l'amministratore sia un account gestito (solo cloud o con sincronizzazione della password).</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32010</p>
            </td>
            <td>
              <p>CryptoError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Questo evento indica che si è verificato un errore durante la generazione della chiave di crittografia password o durante la decrittografia di una password ricevuta dal servizio cloud. L'errore indica probabilmente un problema con l'ambiente. Per altre informazioni e per risolvere il problema, esaminare i dettagli del registro eventi. Per risolvere il problema, è anche possibile provare a disabilitare e riabilitare il servizio di writeback della password.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32011</p>
            </td>
            <td>
              <p>OnBoardingServiceError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Questo evento indica che il servizio locale non è stato in grado di comunicare correttamente con il servizio Web di reimpostazione della password per avviare il processo di caricamento. Potrebbe dipendere da una regola del firewall o da un problema nel recupero di un token di autorizzazione per il tenant. Per risolvere il problema, assicurarsi che le connessioni in uscita su TCP 443 e TCP 9350-9354 o verso <a href="https://ssprsbprodncu-sb.accesscontrol.windows.net/">https://ssprsbprodncu-sb.accesscontrol.windows.net/</a> non siano bloccate e che l'account amministratore AAD usato per il caricamento non sia federato. </p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32012</p>
            </td>
            <td>
              <p>OnBoardingServiceDisableError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>DEPRECATO: questo evento non è presente in Azure AD Connect, ma solo nelle prime build di DirSync che supportano il writeback.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32013</p>
            </td>
            <td>
              <p>OffBoardingError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Questo evento indica che il servizio locale non è stato in grado di comunicare correttamente con il servizio Web di reimpostazione della password per avviare il processo di scaricamento. Potrebbe dipendere da una regola del firewall o da un problema nel recupero di un token di autorizzazione per il tenant. Per risolvere il problema, assicurarsi che le connessioni in uscita sulla porta 443 o verso <a href="https://ssprsbprodncu-sb.accesscontrol.windows.net/">https://ssprsbprodncu-sb.accesscontrol.windows.net/</a> non siano bloccate e che l'account amministratore AAD usato per lo scaricamento non sia federato. </p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32014</p>
            </td>
            <td>
              <p>ServiceBusWarning </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Questo evento indica che è stato necessario ripetere la connessione all'istanza del bus di servizio del tenant. In condizioni normali non dovrebbe essere un problema, ma se l'evento viene visualizzato più volte, è opportuno verificare la connessione di rete al bus di servizio, specialmente se si tratta di una connessione con larghezza di banda limitata o ad alta latenza.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32015</p>
            </td>
            <td>
              <p>ReportServiceHealthError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Per monitorare l'integrità del servizio di writeback della password, vengono inviati dati heartbeat al servizio Web di reimpostazione della password ogni 5 minuti. Questo evento indica che si è verificato un errore durante l'invio delle informazioni sull'integrità al servizio Web cloud. Le informazioni sull'integrità non includono dati OII o PII. Sono semplicemente un heartbeat e statistiche di base del servizio per poter fornire informazioni sullo stato del servizio nel cloud.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>33001</p>
            </td>
            <td>
              <p>ADUnKnownError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Questo evento indica che è stato restituito un errore sconosciuto da AD. Per altre informazioni sull'errore, verificare se nel registro eventi del server Azure AD Connect sono presenti eventi dall'origine ADSync.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>33002</p>
            </td>
            <td>
              <p>ADUserNotFoundError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Questo evento indica che l'utente che sta tentando di reimpostare o modificare una password non è stato trovato nella directory locale. Il problema può verificarsi quando l'utente è stato eliminato in locale ma non nel cloud o se è presente un problema di sincronizzazione. Verificare i log di sincronizzazione, oltre ai dettagli più recenti relativi all'esecuzione della sincronizzazione.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>33003</p>
            </td>
            <td>
              <p>ADMutliMatchError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Quando una richiesta di reimpostazione o modifica della password ha origine dal cloud, viene usato l'ancoraggio cloud specificato durante l'installazione di Azure AD Connect per determinare la modalità di collegamento della richiesta a un utente nell'ambiente locale. Questo evento indica che nella directory locale sono stati trovati due utenti con lo stesso attributo di ancoraggio cloud. Verificare i log di sincronizzazione, oltre ai dettagli più recenti relativi all'esecuzione della sincronizzazione.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>33004</p>
            </td>
            <td>
              <p>ADPermissionsError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Questo evento indica che l'account del servizio agente di gestione non dispone delle autorizzazioni appropriate nell'account in questione per impostare una nuova password. Assicurarsi che l'account agente di gestione nella foresta dell'utente disponga delle autorizzazioni di modifica e reimpostazione della password per tutti gli oggetti nella foresta. Per altre informazioni su come eseguire questa operazione, vedere <a href="../active-directory-passwords-getting-started#step-4-set-up-the-appropriate-active-directory-permissions">Passaggio 4: Impostare le autorizzazioni di Active Directory appropriate</a>.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>33005</p>
            </td>
            <td>
              <p>ADUserAccountDisabled </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Questo evento indica che si è tentato di reimpostare o modificare una password per un account disabilitato in locale. Abilitare l'account e ripetere l'operazione.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>33006</p>
            </td>
            <td>
              <p>ADUserAccountLockedOut </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Questo evento indica che si è tentato di reimpostare o modificare una password per un account bloccato in locale. I blocchi possono verificarsi quando un utente ha tentato troppe volte di eseguire un'operazione di modifica o reimpostazione della password in un breve intervallo di tempo. Sbloccare l'account e ripetere l'operazione.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>33007</p>
            </td>
            <td>
              <p>ADUserIncorrectPassword </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Questo evento indica che l'utente ha specificato una password corrente non corretta durante l'esecuzione di un'operazione di modifica della password. Specificare la password corrente corretta e riprovare.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>33008</p>
            </td>
            <td>
              <p>ADPasswordPolicyError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Questo evento si verifica quando il servizio di writeback della password tenta di impostare una password nella directory locale che non soddisfa i requisiti di validità, cronologia, complessità o filtro del dominio.</p>
              <ul>
                <li class="unordered">
										Se è prevista una validità minima della password e di recente la password è stata modificata in tale intervallo di tempo, non sarà possibile modificarla di nuovo finché non si raggiunge il periodo di validità specificato nel dominio. A scopo di test, è consigliabile impostare la validità minima su 0.&lt;br>&lt;br></li>
              </ul>
              <ul>
                <li class="unordered">
										Se sono abilitati requisiti per la cronologia delle password, sarà necessario selezionare una password che non sia stata usata nelle ultime N volte, dove N è l'impostazione della cronologia delle password. Se si seleziona una password che è stata usata nelle ultime N volte, si verifica un errore. A scopo di test, è consigliabile impostare la cronologia su 0.&lt;br>&lt;br></li>
              </ul>
              <ul>
                <li class="unordered">
										Se sono abilitati requisiti di complessità della password, verranno applicati tutti quando l'utente tenta di modificare o reimpostare la password.&lt;br>&lt;br></li>
              </ul>
              <ul>
                <li class="unordered">
										Se sono abilitati filtri delle password e un utente sceglie una password che non soddisfa i criteri di filtro, l'operazione di reimpostazione o di modifica non riuscirà.&lt;br>&lt;br></li>
              </ul>
            </td>
          </tr>
          <tr>
            <td>
              <p>33009</p>
            </td>
            <td>
              <p>ADConfigurationError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Questo evento indica che si è verificato un problema durante la scrittura di una password nella directory locale, a causa di un problema di configurazione con Active Directory. Per altre informazioni sul tipo di errore, verificare se nel registro eventi dell'applicazione del computer che esegue Azure AD Connect sono presenti messaggi del servizio ADSync. </p>
            </td>
          </tr>
          <tr>
            <td>
              <p>34001</p>
            </td>
            <td>
              <p>ADPasswordPolicyOrPermissionError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>DEPRECATO: questo evento non è presente in Azure AD Connect, ma solo nelle prime build di DirSync che supportano il writeback.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>34002</p>
            </td>
            <td>
              <p>ADNotReachableError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>DEPRECATO: questo evento non è presente in Azure AD Connect, ma solo nelle prime build di DirSync che supportano il writeback.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>34003</p>
            </td>
            <td>
              <p>ADInvalidAnchorError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>DEPRECATO: questo evento non è presente in Azure AD Connect, ma solo nelle prime build di DirSync che supportano il writeback.</p>
            </td>
          </tr>
        </tbody></table>
## Risolvere i problemi relativi alla connettività di writeback della password

Se si verificano interruzioni del servizio con il componente di writeback della password di Azure AD Connect, ecco alcuni passaggi rapidi che è possibile eseguire per risolvere il problema:

 - [Riavviare il servizio di sincronizzazione di Azure AD Connect](#restart-the-azure-AD-sync-service)
 - [Disabilitare e riabilitare la funzionalità di writeback della password](#disable-and-re-enable-the-password-writeback-feature)
 - [Installare la versione più recente di Azure AD Connect](#install-the-latest-azure-ad-connect-release)
 - [Risolvere i problemi relativi al writeback della password](#troubleshoot-password-writeback)

In generale, è consigliabile eseguire questi passaggi nell'ordine indicato per ripristinare il servizio nel modo più rapido.

### Riavviare il servizio di sincronizzazione di Azure AD Connect
Il riavvio del servizio di sincronizzazione di Azure AD Connect può aiutare a risolvere i problemi di connettività o altri problemi temporanei con il servizio.

 1.	Con un account amministratore fare clic su **Avvia** nel server che esegue **Azure AD Connect**.
 2.	Digitare **"services.msc"** nella casella di ricerca e premere **INVIO**.
 3.	Cercare la voce **Microsoft Azure AD Connect**.
 4.	Fare clic con il pulsante destro del mouse sulla voce relativa al servizio, scegliere **Riavvia** e attendere il completamento dell'operazione.

    ![][002]

Questi passaggi consentiranno di ristabilire la connessione con il servizio cloud e risolvere eventuali interruzioni che si sono verificate. Se il riavvio del servizio di sincronizzazione non risolve il problema, come passaggio successivo è consigliabile provare a disabilitare e riabilitare la funzionalità di writeback della password.

### Disabilitare e riabilitare la funzionalità di writeback della password
Disabilitando e riabilitando la funzionalità di writeback della password è possibile risolvere i problemi di connettività.

 1.	Con un account amministratore aprire la **Configurazione guidata di Azure AD Connect**.
 2.	Nella finestra di dialogo **Connessione ad Azure AD** immettere le **credenziali di amministratore globale di Azure AD**.
 3.	Nella finestra di dialogo **Connessione a Servizi di dominio Active Directory** immettere le **credenziali di amministratore di Servizi di dominio Active Directory**.
 4.	Nella finestra di dialogo **Identificazione univoca per gli utenti** fare clic sul pulsante **Avanti**.
 5.	Nella finestra di dialogo **Funzionalità facoltative** deselezionare la casella di controllo **Writeback password**.

    ![][003]

 6.	Fare clic su **Avanti** nelle pagine rimanenti senza apportare alcuna modifica fino a quando non viene visualizzata la pagina **Pronto per la configurazione**.
 7.	Assicurarsi che nella pagina di configurazione l'**opzione Writeback password sia disabilitata** e quindi fare clic sul pulsante verde **Configura** per salvare le modifiche.
 8.	Nella finestra di dialogo **Operazione completata** deselezionare l'opzione **Sincronizza adesso** e quindi fare clic su **Fine** per chiudere la procedura guidata.
 9.	Aprire di nuovo la **Configurazione guidata di Azure AD Connect**.
 10.	**Ripetere i passaggi da 2 a 8**, assicurandosi però di **selezionare l'opzione Writeback password** nella schermata **Funzionalità facoltative** per riabilitare il servizio.

    ![][004]

Questi passaggi consentiranno di ristabilire la connessione con il servizio cloud e risolvere eventuali interruzioni che si sono verificate.

Se la disabilitazione e la riabilitazione della funzionalità di writeback della password non risolvono il problema, come passaggio successivo è consigliabile provare a reinstallare Azure AD Connect.

### Installare la versione più recente di Azure AD Connect
La reinstallazione del pacchetto Azure AD Connect consente di risolvere eventuali problemi di configurazione che potrebbero impedire la connessione ai servizi cloud o la gestione delle password nell'ambiente Active Directory locale. Si consiglia di eseguire questo passaggio solo dopo aver provato a eseguire i due passaggi descritti in precedenza.

 1.	Scaricare la versione più recente di Azure AD Connect [qui](active-directory-aadconnect.md#download-azure-ad-connect).
 2.	Poiché Azure AD Connect è già stato installato, è sufficiente eseguire un aggiornamento sul posto per aggiornare l'installazione di Azure AD Connect alla versione più recente.
 3.	Eseguire il pacchetto scaricato e seguire le istruzioni visualizzate per aggiornare il computer che esegue Azure AD Connect. Non sono necessarie altre operazioni manuali, a meno che non siano state personalizzate le regole di sincronizzazione. In tal caso, è necessario **eseguire il backup prima di procedere con l'aggiornamento e ridistribuire manualmente le regole dopo aver completato l'operazione**.

Questi passaggi consentiranno di ristabilire la connessione con il servizio cloud e risolvere eventuali interruzioni che si sono verificate.

Se la reinstallazione della versione più recente del server Azure AD Connect non risolve il problema, come ultimo passaggio è consigliabile provare a disabilitare e riabilitare il writeback della password dopo aver installato la versione più recente del QFE di sincronizzazione.

Se anche in questo modo il problema non si risolve, vedere [Risolvere i problemi relativi al writeback della password](#troubleshoot-password-writeback) e le [domande frequenti sulla gestione delle password di Azure AD](active-directory-passwords-faq.md) per verificare se il problema viene trattato in queste pagine.


<br/> <br/> <br/>

## Collegamenti alla documentazione relativa alla reimpostazione della password
Di seguito vengono forniti collegamenti a tutte le pagine della documentazione relative alla reimpostazione della password in Azure AD:

* [**Reimpostare la password personale**](active-directory-passwords-update-your-own-password.md): informazioni su come reimpostare o modificare la password personale come utente del sistema
* [**Funzionamento**](active-directory-passwords-how-it-works.md): informazioni sui sei diversi componenti del servizio e sulle relative funzioni
* [**Introduzione**](active-directory-passwords-getting-started.md): informazioni su come consentire agli utenti di reimpostare e modificare le password cloud o locali
* [**Personalizzazione**](active-directory-passwords-customize.md): informazioni su come personalizzare l'aspetto e il comportamento del servizio in base alle esigenze dell'organizzazione
* [**Procedure consigliate**](active-directory-passwords-best-practices.md): informazioni su come distribuire rapidamente e gestire in modo efficace le password nell'organizzazione
* [**Informazioni dettagliate**](active-directory-passwords-get-insights.md): informazioni sulle funzionalità di creazione report integrate
* [**Domande frequenti**](active-directory-passwords-faq.md): risposte alle domande frequenti
* [**Ulteriori informazioni**](active-directory-passwords-learn-more.md): approfondimenti sui dettagli tecnici del funzionamento del servizio



[001]: ./media/active-directory-passwords-troubleshoot/001.jpg "Image_001.jpg"
[002]: ./media/active-directory-passwords-troubleshoot/002.jpg "Image_002.jpg"
[003]: ./media/active-directory-passwords-troubleshoot/003.jpg "Image_003.jpg"
[004]: ./media/active-directory-passwords-troubleshoot/004.jpg "Image_004.jpg"

<!---HONumber=AcomDC_0218_2016-->