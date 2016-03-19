<properties 
	pageTitle="Personalizzare: Gestione delle password di Azure AD | Microsoft Azure" 
	description="Come personalizzare l'aspetto, il comportamento e le notifiche di Gestione delle password in Azure AD in base alle proprie esigenze." 
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

# Personalizzazione di Gestione delle password in base alle esigenze dell'organizzazione
Per offrire agli utenti la migliore esperienza possibile, è consigliabile esplorare e provare tutte le opzioni di configurazione disponibili di Gestione delle password. Di fatto, è possibile iniziare l'esplorazione immediatamente passando alla scheda di configurazione dell’**estensione Active Directory** nel [portale di gestione di Azure](https://manage.windowsazure.com). In questo argomento vengono descritte tutte le diverse personalizzazioni di Gestione delle password che è possibile effettuare come amministratore nella scheda **Configura** della directory all'interno del [portale di gestione di Azure](https://manage.windowsazure.com), tra cui:

| Argomento | |
| --------- | --------- |
| Come è possibile abilitare o disabilitare la reimpostazione della password? | [Impostazione: Utenti abilitati per la reimpostazione della password](#users-enabled-for-password-reset) |
| Come è possibile limitare la reimpostazione delle password a un gruppo specifico di utenti? | [Limitare la reimpostazione delle password a determinati utenti](#restrict-access-to-password-reset) |
| Come è possibile modificare i metodi di autenticazione supportati? | [Impostazione: Metodi di autenticazione disponibili per gli utenti](#authentication-methods-available-to-users) |
| Come è possibile modificare il numero di metodi di autenticazione necessari? | [Impostazione: Numero di metodi di autenticazione necessari](#number-of-authentication-methods-required) |
| Come è possibile configurare domande di sicurezza personalizzate? | [Impostazione: Domande di sicurezza personalizzate](#custom-security-questions) |
| Come è possibile configurare domande di sicurezza localizzate predefinite? | [Impostazione: Domande di sicurezza basate su informazioni note](#knowledge-based-security-questions) |
| Come è possibile modificare il numero di domande di sicurezza necessarie? | [Impostazione: Numero di domande di sicurezza per la registrazione o la reimpostazione](#number-of-questions-required-to-register) |
| Come è possibile forzare gli utenti a registrarsi durante la procedura di accesso? | [Implementazione della funzione di reimpostazione della password basata sulla registrazione forzata](#require-users-to-register-when-signing-in) |
| Come è possibile forzare gli utenti a riconfermare periodicamente la propria registrazione? | [Impostazione: Numero di giorni prima che agli utenti venga chiesto di riconfermare le informazioni di autenticazione](#number-of-days-before-users-must-confirm-their-contact-data) |
| Come è possibile personalizzare le modalità con cui un utente può entrare in contatto con un amministratore? | [Impostazione: Personalizzare il collegamento "Contattare l'amministratore?"](#customize-the-contact-your-administrator-link) |
| Come è possibile consentire agli utenti di sbloccare account di Active Directory senza reimpostare una password? | [Impostazione: Consenti agli utenti di sbloccare gli account senza reimpostare la password](#allow-users-to-unlock-accounts-without-resetting-their-password) |
| Come è possibile abilitare le notifiche di reimpostazione della password per gli utenti? | [Impostazione: Notifica a utenti e amministratori quando è stata reimpostata la propria password](#notify-users-and-admins-when-their-own-password-has-been-reset) |
| Come è possibile abilitare le notifiche di reimpostazione della password per gli amministratori? | [Impostazione: Notifica agli amministratori quando altri amministratori reimpostano le proprie password](#notify-admins-when-other-admins-reset-their-own-passwords) |
| Come è possibile personalizzare la veste grafica della funzione di reimpostazione della password? | [Impostazione: Nome, marchio e logo dell'azienda](#password-managment-look-and-feel) |


## Aspetto di Gestione delle password
Nella tabella seguente viene descritto come ciascun controllo influisce sull'esperienza degli utenti che effettuano la registrazione per la reimpostazione delle password e che reimpostano le proprie password. È possibile configurare queste opzioni nella sezione **Proprietà della directory** della scheda **Configura** della directory all’interno del [portale di gestione di Azure](https://manage.windowsazure.com).

<table>
            <tbody><tr>
              <td>
                <p>
                  <strong>Controllo dei criteri</strong>
                </p>
              </td>
              <td>
                <p>
                  <strong>Descrizione</strong>
                </p>
              </td>
              <td>
                <p>
                  <strong>Effetti</strong>
                </p>
              </td>
            </tr>
            <tr>
              <td>
                <div id="directory-name">
                  <p>Nome della directory</p>
                </div>
              </td>
              <td>
                <p>Determina il nome dell’organizzazione che utenti o amministratori visualizzano nelle comunicazioni tramite messaggi di posta elettronica relativi alla reimpostazione delle password</p>
              </td>
              <td>
                <p>
                  <strong>Messaggi di posta elettronica "Contattare l'amministratore":</strong>
                </p>
                <ul>
                  <li class="unordered">
												Determina il nome descrittivo dell’indirizzo del mittente, ad esempio "Microsoft per conto di <strong>Wingtip Toys</strong>"<br><br></li>
                  <li class="unordered">
												Determina il nome oggetto del messaggio di posta elettronica, ad esempio "Codice di verifica del messaggio di posta elettronica dell'account <strong>Wingtip Toys</strong>"<br><br></li>
                </ul>
                <p>
                  <strong>Messaggi di posta elettronica di reimpostazione delle password:</strong>
                </p>
                <ul>
                  <li class="unordered">
												Determina il nome descrittivo dell’indirizzo del mittente, ad esempio "Microsoft per conto di <strong>Wingtip Toys</strong>"<br><br></li>
                </ul>
              </td>
            </tr>
            <tr>
              <td>
                <div id="sign-in-and-access-panel-page-appearance">
                  <p>Aspetto della pagina di accesso e del pannello di accesso</p>
                </div>
              </td>
              <td>
                <p>Determina se gli utenti che visitano la pagina di reimpostazione delle password visualizzano il logo Microsoft o il logo personalizzato. Questo elemento di configurazione consente inoltre di aggiungere informazioni personalizzate alla pagina di accesso e al pannello di accesso.</p>
                <p>
                  
                </p>
                <p>Ulteriori informazioni sulla funzionalità di personalizzazione del tenant sono disponibili in <a href="https://technet.microsoft.com/library/dn532270.aspx">Aggiungere informazioni personalizzate distintive dell’azienda alla pagina di accesso e al pannello di accesso</a>.</p>
              </td>
              <td>
                <p>
                  <strong>Portale di reimpostazione delle password:</strong>
                </p>
                <ul>
                  <li class="unordered">
												Determina se il logo personalizzato viene visualizzato o meno nella parte superiore del portale di reimpostazione delle password al posto del logo Microsoft predefinito.<br><br></li>
                  <li class="unordered">
                    <strong>Nota:</strong> se si passa direttamente alla pagina di reimpostazione delle password, il logo personalizzato potrebbe non essere visualizzato nella prima pagina del portale di reimpostazione delle password. Una volta che un utente immette il proprio ID utente e fa clic su Avanti, viene visualizzato il logo personalizzato. È possibile forzare la visualizzazione del logo personalizzato nel caricamento della pagina passando il parametro whr nella pagina di reimpostazione delle password, come segue: <a href="https://passwordreset.microsoftonline.com?whr=wingtiptoysonline.com">https://passwordreset.microsoftonline.com?whr=wingtiptoysonline.com</a><br><br></li>
                </ul>
                <p>
                  <strong>Messaggi di posta elettronica "Contattare l'amministratore":</strong>
                </p>
                <ul>
                  <li class="unordered">
												Determina se il logo personalizzato viene visualizzato o meno nella parte inferiore dei messaggi di posta elettronica inviati agli amministratori quando l’utente sceglie di contattare l'amministratore facendo clic sul collegamento "Contattare l'amministratore" dell’interfaccia utente di reimpostazione delle password.<br><br></li>
                </ul>
                <p>
                  <strong>Messaggi di posta elettronica di reimpostazione delle password:</strong>
                </p>
                <ul>
                  <li class="unordered">
												Determina se il logo personalizzato viene visualizzato o meno nella parte inferiore dei messaggi di posta elettronica inviati agli utenti quando reimpostano le password.<br><br></li>
                </ul>
              </td>
            </tr>
          </tbody></table>

## Comportamento di Gestione delle password
Nella tabella seguente viene descritto come ciascun controllo influisce sull'esperienza degli utenti che effettuano la registrazione per la reimpostazione delle password e che reimpostano le proprie password. È possibile configurare queste opzioni nella sezione **Criteri di reimpostazione password dell’utente** della scheda **Configura** della directory nel [portale di gestione di Azure](https://manage.windowsazure.com).

> [AZURE.NOTE] Per visualizzare i controlli dei criteri, all'account amministratore usato deve essere assegnata una licenza AAD Premium.<br><br>I controlli dei criteri si applicano solo agli utenti finali che reimpostano le proprie password e non agli amministratori. **Gli amministratori dispongono di un criterio predefinito di posta elettronica alternativa e/o di telefono cellulare specificato da Microsoft che non può essere modificato.**

<table>
            <tbody><tr>
              <td>
                <p>
                  <strong>Controllo dei criteri</strong>
                </p>
              </td>
              <td>
                <p>
                  <strong>Descrizione</strong>
                </p>
              </td>
              <td>
                <p>
                  <strong>Effetti</strong>
                </p>
              </td>
            </tr>
            <tr>
              <td>
                <div id="users-enabled-for-password-reset">
                  <p>Utenti abilitati per la reimpostazione delle password</p>
                </div>
              </td>
              <td>
                <p>Determina se la reimpostazione delle password è abilitata per gli utenti in questa directory. </p>
              </td>
              <td>
                <p>
                  <strong>Portale di registrazione:</strong>
                </p>
                <ul>
                  <li class="unordered">
												Se impostato su no, nessun utente può registrare i dati della richiesta di verifica.<br><br></li>
                  <li class="unordered">
												Se impostato su Sì, qualsiasi utente finale nella directory può registrare i dati della richiesta di verifica visitando il portale di registrazione in <a href="http://aka.ms/ssprsetup">http://aka.ms/ssprsetup</a>.<br><br></li>
                  <li class="unordered">
                    <strong>Nota:</strong> per poter effettuare la registrazione per la reimpostazione delle password, agli utenti deve essere stata assegnata una licenza Azure AD Premium o Basic.<br><br></li>
                </ul>
                <p>
                  <strong>Portale di reimpostazione delle password:</strong>
                </p>
                <ul>
                  <li class="unordered">
												Se impostato su No, gli utenti visualizzano un messaggio che informa che, per reimpostare la password, è necessario contattare l'amministratore.<br><br></li>
                  <li class="unordered">
												Se impostato su Sì, gli utenti possono reimpostare automaticamente le password visitando il sito <a href="http://passwordreset.microsoftonline.com">http://passwordreset.microsoftonline.com</a> oppure facendo clic sul collegamento <strong>Impossibile accedere all'account</strong> in qualsiasi pagina di accesso ID organizzazione.<br><br></li>
                  <li class="unordered">
                    <strong>Nota:</strong> per poter reimpostare le password, agli utenti deve essere stata assegnata una licenza Azure AD Premium o Basic.<br><br></li>
                </ul>
              </td>
            </tr>
            <tr>
              <td>
                <div id="restrict-access-to-password-reset">
                  <p>Limitare l'accesso per la reimpostazione delle password</p>
                </div>
              </td>
              <td>
                <p>Determina se è consentito usare la reimpostazione delle password solo a un determinato gruppo di utenti. (Visibile solo se <strong>Utenti abilitati per la reimpostazione delle password</strong> è impostato su <strong>Sì</strong>).</p>
              </td>
              <td>
                <p>
                  <strong>Portale di registrazione:</strong>
                </p>
                <ul>
                  <li class="unordered">
												Se impostato su No, tutti gli utenti finali nella directory possono effettuare la registrazione per la reimpostazione delle password in <a href="http://aka.ms/ssprsetup">http://aka.ms/ssprsetup</a><br><br></li>
                  <li class="unordered">
												Se impostato su Sì, solo gli utenti finali specificati nel controllo <strong>Gruppo che può eseguire la reimpostazione delle password</strong> possono effettuare la registrazione per la reimpostazione delle password in <a href="http://aka.ms/ssprsetup">http://aka.ms/ssprsetup</a><br><br></li>
                </ul>
                <p>
                  <strong>Portale di reimpostazione delle password:</strong>
                </p>
                <ul>
                  <li class="unordered">
												Se impostato su No, tutti gli utenti finali nella directory possono reimpostare le password.<br><br></li>
                  <li class="unordered">
												Se impostato su Sì, solo gli utenti finali specificati nel controllo <strong>Gruppo che può eseguire la reimpostazione delle password</strong> possono reimpostare le password.<br><br></li>
                </ul>
              </td>
            </tr>
            <tr>
              <td>
                <div id="group-that-can-perform-password-reset">
                  <p>Gruppo che può eseguire la reimpostazione delle password</p>
                </div>
              </td>
              <td>
                <p>Determina quale gruppo di utenti finali è autorizzato a usare la reimpostazione delle password. </p>
                <p>
                  
                </p>
                <p>(Visibile solo se <strong>Limitare l’accesso per la reimpostazione delle password</strong> è impostato su <strong>Sì</strong>).</p>
              </td>
              <td>
                <p>
                  <strong>Nota:</strong>
                </p>
                <ul>
                  <li class="unordered">
												Se non viene specificato alcun gruppo e si fa clic su <strong>Salva</strong>, un gruppo vuoto denominato <strong>SSPRSecurityGroupUsers</strong> viene creato automaticamente.<br><br></li>
                  <li class="unordered">
												Se si vuole specificare il proprio gruppo, è possibile fornire il proprio nome visualizzato.<br><br></li>
                </ul>
                <p>
                  <strong>Portale di registrazione:</strong>
                </p>
                <ul>
                  <li class="unordered">
												Se <strong>Limitare l'accesso per la reimpostazione delle password</strong> è impostato su <strong>Sì</strong>, solo gli utenti finali di questo gruppo sono in grado di effettuare la registrazione per la reimpostazione delle password. <br><br></li>
                </ul>
                <p>
                  <strong>Portale di reimpostazione delle password:</strong>
                </p>
                <ul>
                  <li class="unordered">
												Se <strong>Limitare l'accesso per la reimpostazione delle password</strong> è impostato su <strong>Sì</strong>, solo gli utenti finali di questo gruppo sono in grado di effettuare la reimpostazione delle password.<br><br></li>
                </ul>
              </td>
            </tr>
            <tr>
              <td>
                <div id="authentication-methods-available-to-users">
                  <p>Metodi di autenticazione disponibili per gli utenti</p>
                </div>
              </td>
              <td>
                <p>Determina le richieste di verifica che un utente è autorizzato a usare per reimpostare la propria password.</p>
                <p>
                  
                </p>
                <p>(Visibile solo se <strong>Utenti abilitati per la reimpostazione delle password</strong> è impostato su <strong>Sì</strong>).</p>
              </td>
              <td>
                <p>
                  
                </p>
                <p>
                  
                </p>
                <p>
                  <strong>Nota:</strong>
                </p>
                <ul>
                  <li class="unordered">
												È necessario selezionare almeno un'opzione.<br><br></li>
                  <li class="unordered">
												Si consiglia l'abilitazione di almeno 2 opzioni per offrire agli utenti la massima flessibilità durante la reimpostazione delle password.<br><br></li>
                  <li class="unordered">
												Se si usano le domande di sicurezza, si consiglia di usarle in combinazione con un altro metodo di autenticazione, in quanto le domande di sicurezza possono essere meno sicure dei metodi reimpostazione delle password basati sul telefono o la posta elettronica.<br><br></li>
                </ul>
                <p>
                  <strong>Campi di directory da usare</strong>
                </p>
                <ul>
                  <li class="unordered">
												Telefono ufficio corrisponde all’attributo <strong>Telefono ufficio</strong> di un oggetto utente nella directory.<br><br></li>
                  <li class="unordered">
												Telefono cellulare corrisponde all’attributo <strong>Cellulare per l’autenticazione</strong> (non visibile pubblicamente) o all’attributo <strong>Telefono cellulare</strong> (visibile pubblicamente) di un oggetto utente nella directory. Il servizio cerca i dati prima in <strong>Telefono per l’autenticazione</strong> e, se non sono presenti, esegue il fallback all’attributo <strong>Telefono cellulare</strong>.<br><br></li>
                  <li class="unordered">
												Indirizzo di posta elettronica alternativo corrisponde all’attributo <strong>Indirizzo di posta elettronica per l’autenticazione</strong> (non visibile pubblicamente) o all’attributo <strong>Indirizzo di posta elettronica alternativo</strong> di un oggetto utente nella directory. Il servizio cerca i dati prima in <strong>Indirizzo di posta elettronica per l’autenticazione</strong> e, se non sono presenti, esegue il fallback all’attributo <strong>Indirizzo di posta elettronica alternativo</strong>.<br><br></li>
                  <li class="unordered">
												Le domande di sicurezza vengono archiviate privatamente e in modo sicuro in un oggetto utente nella directory e gli utenti possono rispondere a tali domande solo durante la registrazione. Per motivi di sicurezza, attualmente un amministratore non può modificare o visualizzare queste risposte in alcun modo.<br><br></li>
                  <li class="unordered">
                    <strong>Nota: </strong>per impostazione predefinita, solo gli attributi cloud Telefono ufficio e Telefono cellulare vengono sincronizzati con la directory cloud dalla directory locale. Per ulteriori informazioni sugli attributi locali sincronizzati con il cloud, vedere <a href="https://msdn.microsoft.com/library/azure/dn764938.aspx">Attributi sincronizzati con Azure AD.</a><br><br></li>
                </ul>
                <p>
                  <strong>Portale di registrazione:</strong>
                </p>
                <ul>
                  <li class="unordered">
												Influisce sui metodi di autenticazione visualizzati durante la registrazione degli utenti. Se non si abilita un determinato metodo di autenticazione, gli utenti non saranno in grado di effettuare la registrazione automatica per tale metodo di autenticazione.<br><br></li>
                  <li class="unordered">
                    <strong>Nota: </strong>gli utenti non sono attualmente in grado di registrare i propri numeri di telefono ufficio, tale metodo di autenticazione deve essere definito dall'amministratore.<br><br></li>
                </ul>
                <p>
                  <strong>Portale di reimpostazione delle password:</strong>
                </p>
                <ul>
                  <li class="unordered">
												Determina i metodi di autenticazione che un utente può usare come richieste di verifica per un determinato passaggio di verifica. Ad esempio, se per un utente sono disponibili dati in entrambi i campi <strong>Telefono ufficio</strong> e <strong>Telefono per l'autenticazione</strong> in Azure Active Directory, l’utente può usare uno di questi metodi di autenticazione per ripristinare la propria password.<br><br></li>
                  <li class="unordered">
                    <strong>Nota: </strong>gli utenti saranno in grado di reimpostare la password se e solo se sono presenti dati nei metodi di autenticazione abilitati dall’amministratore.<br><br></li>
                </ul>
              </td>
            </tr>
            <tr>
              <td>
                <div id="number-of-authentication-methods-required">
                  <p>Numero di metodi di autenticazione necessari</p>
                </div>
              </td>
              <td>
                <p>Determina il numero minimo di metodi di autenticazione disponibili che un utente deve usare per reimpostare la propria password.</p>
                <p>
                  
                </p>
                <p>(Visibile solo se <strong>Utenti abilitati per la reimpostazione delle password</strong> è impostato su <strong>Sì</strong>).</p>
              </td>
              <td>
                <p>
                  <strong>Nota:</strong>
                </p>
                <ul>
                  <li class="unordered">
												Può essere impostato su 1 o 2 metodi di autenticazione necessari.<br><br></li>
                </ul>
                <p>
                  <strong>Portale di registrazione:</strong>
                </p>
                <ul>
                  <li class="unordered">
												Determina il numero minimo di metodi di autenticazione che un utente deve registrare per poter essere in grado di terminare l'esperienza di registrazione.<br><br></li>
                </ul>
                <p>
                  <strong>Portale di reimpostazione delle password:</strong>
                </p>
                <ul>
                  <li class="unordered">
												Influisce sul numero di passaggi di verifica che un utente deve effettuare per poter essere in grado di reimpostare una password. Un passaggio di verifica viene definito come un passaggio effettuato da un utente usando le informazioni di autenticazione, ad esempio una chiamata al telefono dell’ufficio o un messaggio di posta elettronica all’indirizzo di posta elettronica alternativo, per verificare l'account.<br><br></li>
                  <li class="unordered">
                    <strong>Nota:</strong> se un utente non dispone della quantità necessaria di informazioni di autenticazione definite sul proprio account per reimpostare correttamente la password in base ai criteri impostati, viene visualizzata una pagina di errore che invita l’utente a richiedere a un amministratore di reimpostare la password.  <br><br></li>
                </ul>
              </td>
            </tr>
            <tr>
              <td>
                <div id="number-of-questions-required-to-register">
                  <p>Numero di domande necessarie per la registrazione</p>
                </div>
              </td>
              <td>
                <p>Determina il numero minimo di domande a cui un utente deve rispondere quando effettua la registrazione con l’opzione delle domande di sicurezza.</p>
                <p>(Visibile solo se la casella di controllo <strong>Domande di sicurezza</strong> è abilitata).</p>
              </td>
              <td>
                <p>
                  <strong>Nota:</strong>
                </p>
                <ul>
                  <li class="unordered">
												Può essere impostato su 3-5 domande necessarie per la registrazione.<br><br></li>
                  <li class="unordered">
												Il numero di domande necessarie per la registrazione deve essere maggiore o uguale al numero di domande necessarie per la reimpostazione.<br><br></li>
                  <li class="unordered">
												È consigliabile impostare il numero di domande necessarie per la registrazione su un valore maggiore rispetto al numero di domande necessarie per la reimpostazione, in modo che gli utenti abbiano maggiore flessibilità durante la reimpostazione delle password. Questa configurazione è inoltre più sicura perché l’utente dovrà rispondere a domande selezionate casualmente dal pool di tutte le domande registrate.<br><br></li>
                </ul>
                <p>
                  <strong>Portale di registrazione:</strong>
                </p>
                <ul>
                  <li class="unordered">
												Determina il numero minimo di domande a cui un utente deve rispondere per essere considerato completamente registrato per la reimpostazione delle password.<br><br></li>
                </ul>
              </td>
            </tr>
            <tr>
              <td>
                <div id="number-of-questions-required-to-reset">
                  <p>Numero di domande necessarie per la reimpostazione </p>
                </div>
              </td>
              <td>
                <p>Determina il numero minimo di domande a cui un utente deve rispondere quando reimposta una password.</p>
                <p>
                  
                </p>
                <p>(Visibile solo se la casella di controllo <strong>Domande di sicurezza</strong> è abilitata).</p>
              </td>
              <td>
                <p>
                  <strong>Nota:</strong>
                </p>
                <ul>
                  <li class="unordered">
												Può essere impostato su 3-5 domande necessarie per la reimpostazione.<br><br></li>
                  <li class="unordered">
												Il numero di domande necessarie per la reimpostazione deve essere minore o uguale al numero di domande necessarie per la registrazione.<br><br></li>
                </ul>
                <p>
                  <strong>Portale di reimpostazione delle password:</strong>
                </p>
                <ul>
                  <li class="unordered">
												Determina il numero minimo di domande a cui un utente deve rispondere affinché la password dell’utente possa essere reimpostata.<br><br></li>
                  <li class="unordered">
												Al momento della reimpostazione della password, questo numero di domande verrà selezionato casualmente dall'elenco totale di domande registrate dell’utente. Ad esempio, se l'utente ha registrato 5 domande, 3 delle 5 domande verranno selezionate casualmente quando l'utente accede per reimpostare una password. L'utente deve quindi rispondere correttamente a tutte queste domande affinché la password possa essere reimpostata.<br><br></li>
                </ul>
              </td>
            </tr>
            <tr>
              <td>
                <div id="knowledge-based-security-questions">
                  <p>Le domande di sicurezza si basano sulla conoscenza</p>
                </div>
              </td>
              <td>
                <p>Definisce le domande di sicurezza pre-definite che gli utenti possono scegliere durante la registrazione per la reimpostazione delle password e durante la reimpostazione delle password.</p>
                <p>
                  
                </p>
                <p>(Visibile solo se la casella di controllo <strong>Domande di sicurezza</strong> è abilitata).</p>
              </td>
              <td>
                <p>
                  <strong>Nota:</strong>
                </p>
                <ul>
                  <li class="unordered">
												Tutte le domande basate sulla conoscenza verranno localizzate nel set completo di lingue di O365 basato sulle impostazioni locali del browser dell'utente.<br><br></li>
                  <li class="unordered">
												È possibile definire fino a un totale di 20 domande (la somma delle domande personalizzate e basate sulla conoscenza).<br><br></li>
                 <li class="unordered">
												Il limite minimo di caratteri della risposta è 3 caratteri.<br><br></li>
                  <li class="unordered">
												Il limite massimo di caratteri della risposta è 40 caratteri.<br><br></li>
                  <li class="unordered">
												Gli utenti potrebbero non rispondere alla stessa domanda due volte.<br><br></li>
                  <li class="unordered">
												Gli utenti potrebbero non fornire la stessa risposta a due diverse domande due volte.<br><br></li>
                  <li class="unordered">
												Qualsiasi set di caratteri può essere usato per definire le risposte (compresi i caratteri Unicode).<br><br></li>
                  <li class="unordered">
												Il numero di domande definite deve essere maggiore o uguale al numero di domande necessarie per la registrazione.<br><br></li>
                </ul>
                <p>
                  <strong>Portale di registrazione:</strong>
                </p>
                <ul>
                  <li class="unordered">
												Determina le domande a cui un utente è in grado di fornire risposte durante la registrazione per la reimpostazione delle password.<br><br></li>
                </ul>
                <p>
                  <strong>Portale di reimpostazione delle password:</strong>
                </p>
                <ul>
                  <li class="unordered">
												Determina le domande che un utente è in grado di usare per reimpostare una password.<br><br></li>
                </ul>
              </td>
            </tr>
            <tr>
              <td>
                <div id="custom-security-questions">
                  <p>Domande di sicurezza personalizzate</p>
                </div>
              </td>
              <td>
                <p>Definisce le domande di sicurezza che gli utenti possono scegliere durante la registrazione per la reimpostazione delle password e durante la reimpostazione delle password.</p>
                <p>
                  
                </p>
                <p>(Visibile solo se la casella di controllo <strong>Domande di sicurezza</strong> è abilitata).</p>
              </td>
              <td>
                <p>
                  <strong>Nota:</strong>
                </p>
                <ul>
                  <li class="unordered">
												È possibile definire fino a un totale di 20 domande (la somma delle domande personalizzate e basate sulla conoscenza).<br><br></li>
                  <li class="unordered">
												Il limite massimo di caratteri della domanda è 200 caratteri.<br><br></li>
                  <li class="unordered">
												Il limite minimo di caratteri della risposta è 3 caratteri.<br><br></li>
                  <li class="unordered">
												Il limite massimo di caratteri della risposta è 40 caratteri.<br><br></li>
                  <li class="unordered">
												Gli utenti potrebbero non rispondere alla stessa domanda due volte.<br><br></li>
                  <li class="unordered">
												Gli utenti potrebbero non fornire la stessa risposta a due diverse domande due volte.<br><br></li>
                  <li class="unordered">
												Qualsiasi set di caratteri può essere usato per definire domande e risposte (compresi i caratteri Unicode).<br><br></li>
                  <li class="unordered">
												Il numero di domande definite deve essere maggiore o uguale al numero di domande necessarie per la registrazione.<br><br></li>
                  <li class="unordered">
												La definizione di diverse domande per impostazioni locali diverse non è supportata per le domande personalizzate. Tutte le domande personalizzate verranno visualizzate nella lingua in cui vengono immesse nell'interfaccia utente di amministrazione, anche se le impostazioni locali del browser dell'utente sono diverse. Se è necessario che queste domande vengano localizzate, utilizzare invece le domande "basate sulla conoscenza".<br><br></li>
                </ul>
                <p>
                  <strong>Portale di registrazione:</strong>
                </p>
                <ul>
                  <li class="unordered">
												Determina le domande a cui un utente è in grado di fornire risposte durante la registrazione per la reimpostazione delle password.<br><br></li>
                </ul>
                <p>
                  <strong>Portale di reimpostazione delle password:</strong>
                </p>
                <ul>
                  <li class="unordered">
												Determina le domande che un utente è in grado di usare per reimpostare una password.<br><br></li>
                </ul>
              </td>
            </tr>
            <tr>
              <td>
                <div id="require-users-to-register-when-signing-in">
                  <p>Richiedere agli utenti di registrarsi all'accesso?</p>
                </div>
                <p>
                  
                </p>
              </td>
              <td>
                <p>Determina se a un utente viene richiesto di registrare i dati di contatto per la reimpostazione delle password la volta successiva che accede.  
                </p>
                <p>Questa funzionalità può essere utilizzata in qualsiasi pagina di accesso che utilizza un account aziendale o di un istituto di istruzione. Tali pagine includono tutte le versioni di Office 365, il Portale di gestione di Azure, il Pannello di accesso e tutte le applicazioni federate o sviluppate in modo personalizzato che effettuano l'accesso tramite Azure AD.
                </p>
                <p>
                  
                </p>
                <p>La registrazione applicata viene utilizzata solo per gli utenti per i quali è abilitata la reimpostazione della password. Pertanto, se sono state utilizzate la funzionalità "Limitare l'accesso per la reimpostazione delle password" e la reimpostazione della password con ambito per un gruppo specifico di utenti, solo agli utenti di questo gruppo verrà richiesto all'accesso di registrarsi per la reimpostazione della password.</p>
                <p>
                  
                </p>
                <p>(Visibile solo se <strong>Utenti abilitati per la reimpostazione delle password</strong> è impostato su <strong>Sì</strong>).</p>
              </td>
              <td>
                <p>
                  
                </p>
                <p>
                  
                </p>
                <p>
                  <strong>Nota:</strong>
                </p>
                <ul>
                  <li class="unordered">
												Se si disabilita questa funzionalità, è possibile anche inviare manualmente gli utenti a <a href="http://aka.ms/ssprsetup">http://aka.ms/ssprsetup</a> per registrare i relativi dati di contatto.  <br><br></li>
                  <li class="unordered">
												Gli utenti possono inoltre raggiungere il portale di registrazione facendo clic sul collegamento <strong>Registrazione per la reimpostazione delle password</strong> nella scheda Profilo del pannello di accesso.<br><br></li>
                  <li class="unordered">
												La registrazione tramite questo metodo può essere chiusa facendo clic sul pulsante Annulla o chiudendo la finestra ma, se non effettuano la registrazione, gli utenti ricevono messaggi fastidiosi ogni volta che eseguono l'accesso.<br><br></li>
                </ul>
                <p>
                  <strong>Portale di registrazione:</strong>
                </p>
                <ul>
                  <li class="unordered">
												Questa impostazione non influisce sul comportamento del portale di registrazione, determina, invece, se il portale di registrazione viene visualizzato o meno agli utenti quando accedono al pannello di accesso.<br><br></li>
                </ul>
              </td>
            </tr>
            <tr>
              <td>
                <div id="number-of-days-before-users-must-confirm-their-contact-data">
                  <p>Numero di giorni prima che gli utenti debbano confermare i dati di contatto</p>
                </div>
              </td>
              <td>
                <p>Quando <strong>Richiedere agli utenti di effettuare la registrazione</strong> è attivato, questa impostazione determina il periodo di tempo che può trascorrere prima che un utente debba riconfermare i dati. </p>
                <p>
                  
                </p>
                <p>(Visibile solo se <strong>Richiedere agli utenti di effettuare la registrazione quando accedono al pannello di accesso</strong> è impostato su <strong>Sì</strong>).</p>
              </td>
              <td>
                <p>
                  
                </p>
                <p>
                  
                </p>
                <p>
                  <strong>Nota: </strong>
                </p>
                <ul>
                  <li class="unordered">
												Vengono accettati i valori compresi tra 0 e 730 giorni; 0 giorni indica che agli utenti non verrà mai richiesto di riconfermare i dati di contatto.<br><br></li>
                </ul>
                <p>
                  <strong>Portale di registrazione:</strong>
                </p>
                <ul>
                  <li class="unordered">
												Questa impostazione non influisce sul comportamento del portale di registrazione, determina, invece, se il portale di registrazione viene visualizzato o meno agli utenti quando i relativi dati di contatto devono essere riconfermati. <br><br></li>
                </ul>
              </td>
            </tr>
            <tr>
              <td>
                <div id="customize-the-contact-your-administrator-link">
                  <p>Personalizzare il collegamento Contattare l’amministratore</p>
                </div>
              </td>
              <td>
                <p>Controlla se il collegamento Contattare l’amministratore (visualizzato a sinistra) viene visualizzato nel portale di reimpostazione delle password quando si verifica un errore o se un utente attende troppo a lungo che un'operazione punti a un URL o a un indirizzo di posta elettronica personalizzato.</p>
                <p>
                  
                </p>
                <p>(Visibile solo se <strong>Utenti abilitati per la reimpostazione delle password</strong> è impostato su <strong>Sì</strong>).</p>
              </td>
              <td>
                <p>
                  <strong>Nota: </strong>
                </p>
                <ul>
                  <li class="unordered">
												Se si abilita questa impostazione, è necessario scegliere un URL o un indirizzo di posta elettronica personalizzato completando il campo <strong>Indirizzo di posta elettronica o url personalizzato </strong> immediatamente al di sotto di questa impostazione.<br><br></li>
                </ul>
                <p>
                  <strong>Portale di reimpostazione delle password:</strong>
                </p>
                <ul>
                  <li class="unordered">
												Se impostato su No, gli utenti che fanno clic sul collegamento evidenziato inviano un messaggio di posta elettronica all'indirizzo di posta elettronica principale di tutti gli amministratori tenant richiedendo che la propria password venga reimpostata. Questo messaggio di posta elettronica viene inviato seguendo la logica in basso:<br><br></li>
                  <li class="unordered">
                    <ul>
                      <li class="unordered">
														Se sono presenti amministratori password, il messaggio viene inviato a tutti gli amministratori password con un massimo di 100 destinatari totali.<br><br></li>
                      <li class="unordered">
														Se non sono presenti amministratori password, il messaggio viene inviato a tutti gli amministratori utenti con un massimo di 100 destinatari totali.<br><br></li>
                      <li class="unordered">
														Se non sono presenti amministratori utenti, il messaggio viene inviato a tutti gli amministratori globali con un massimo di 100 destinatari totali.<br><br></li>
                    </ul>
                  </li>
                  <li class="unordered">
												Se impostata su Sì, questa impostazione personalizza il comportamento del collegamento evidenziato in precedenza per puntare a un URL o a un indirizzo di posta elettronica personalizzato a cui gli utenti possono passare per ottenere assistenza per la reimpostazione delle password.<br><br></li>
                  <li class="unordered">
												Se si specifica un URL, viene aperto in una nuova scheda.<br><br></li>
                  <li class="unordered">
												Se si specifica un indirizzo di posta elettronica, viene creato un collegamento mailto a questo indirizzo di posta elettronica.<br><br></li>
                </ul>
              </td>
            </tr>
            <tr>
              <td>
                <div id="custom-email-address-or-URL">
                  <p>Indirizzo di posta elettronica o URL personalizzato</p>
                </div>
              </td>
              <td>
                <p>Controlla l'indirizzo di posta elettronica o l'URL a cui punta il collegamento <strong>Contattare l'amministratore</strong>. </p>
                <p>
                  
                </p>
                <p>(Visibile solo se <strong>Personalizzare il collegamento Contattare l’amministratore</strong> è impostato su <strong>Sì</strong>).</p>
              </td>
              <td>
                <p>
                  <strong>Nota:</strong>
                </p>
                <ul>
                  <li class="unordered">
												Deve essere un URL intranet o extranet o un indirizzo di posta elettronica valido.<br><br></li>
                </ul>
                <p>
                  <strong>Portale di reimpostazione delle password:</strong>
                </p>
                <ul>
                  <li class="unordered">
												Modifica l’elemento a cui punta il collegamento <strong>Contattare l’amministratore</strong>.<br><br></li>
                  <li class="unordered">
												Se si specifica un indirizzo di posta elettronica, il collegamento diventa un collegamento "mailto" a tale indirizzo di posta elettronica.<br><br></li>
                  <li class="unordered">
												Se si specifica un URL, il collegamento diventa un href standard che punta all'URL che verrà aperto in una nuova scheda.  <br><br></li>
                </ul>
              </td>
            </tr>
            <tr>
              <td>
                <div id="write-back-passwords-to-on-premises-directory">
                  <p>Writeback della password in una directory locale</p>
                </div>
              </td>
              <td>
                <p>Controlla se Writeback password è abilitato per questa directory e, in questo caso, indica lo stato del servizio di writeback locale.</p>
                <p>
                  
                </p>
                <p>Questa impostazione è utile se si desidera disabilitare temporaneamente il servizio senza riconfigurare Azure AD Connect.</p>
              </td>
              <td>
                <p>
                  
                </p>
                <p>
                  <strong>Nota:</strong>
                </p>
                <ul>
                  <li class="unordered">
												Questo controllo viene visualizzato solo se Writeback password è stato installato scaricando la versione più recente di Azure AD Connect e abilitando l’opzione <strong>Writeback password</strong> nella schermata di selezione <strong>Funzionalità facoltative</strong>.<br><br></li>
                  <li class="unordered">
												Se Writeback password è stato abilitato e si ritiene che esista un problema di configurazione con il servizio, è possibile aprire questa scheda ed esaminare l’etichetta <strong>Stato del servizio writeback password</strong> per individuare eventuali problemi.<br><br></li>
                  <li class="unordered">
												Gli stati che possono essere visualizzati sono i seguenti:<br><br><ul><li class="unordered"><strong>Configurato</strong>: tutto funziona come previsto<br><br></li><li class="unordered"><strong>Non configurato</strong>: il servizio writeback è stato installato, ma è impossibile raggiungerlo, verificare che le connessioni in uscita non si blocchino su 443 e provare a reinstallare il servizio se continuano a verificarsi problemi.<br><br></li></ul></li>
                </ul>
                <p>
                  <strong>Portale di registrazione:</strong>
                </p>
                <ul>
                  <li class="unordered">
												Se il servizio writeback è distribuito e configurato e questa opzione è impostata su <strong>No</strong> il servizio writeback viene disabilitato e gli utenti federati e con sincronizzazione degli hash delle password non sono in grado di effettuare la registrazione per la reimpostazione delle proprie password.<br><br></li>
                  <li class="unordered">
												Se l'opzione è impostata su <strong>Sì</strong>, il servizio writeback viene abilitato e gli utenti federati e con sincronizzazione degli hash delle password sono in grado di reimpostare le proprie password.<br><br></li>
                </ul>
                <p>
                  <strong>Portale di reimpostazione delle password:</strong>
                </p>
                <ul>
                  <li class="unordered">
												Se il servizio writeback è distribuito e configurato e questa opzione è impostata su <strong>No</strong> il servizio writeback viene disabilitato e gli utenti federati e con sincronizzazione degli hash delle password non sono in grado di reimpostare le proprie password.<br><br></li>
                  <li class="unordered">
												Se l'opzione è impostata su <strong>Sì</strong>, il servizio writeback viene abilitato e gli utenti federati e con sincronizzazione degli hash delle password sono in grado di reimpostare le proprie password.<br><br></li>
                </ul>
              </td>
            </tr>
             <tr>
              <td>
                <div id="allow-users-to-unlock-accounts-without-resetting-their-password">
                  <p>Allow users to unlock accounts without resetting their password</p>
                </div>
              </td>
              <td>
              
              <p>Stabilisce se gli utenti che visitano il portale di reimpostazione della password devono avere la possibilità di sbloccare il proprio account di Active Directory locale senza reimpostare la password. Per impostazione predefinita, Azure AD sblocca sempre gli account quando si esegue una reimpostazione della password. Questa impostazione consente di separare le due operazioni.</p>
              
              <p>Se impostata su "sì", agli utenti viene offerta la possibilità di scegliere se reimpostare la password e sbloccare l'account o se sbloccare l'account senza reimpostare la password. </p>
              
              <p>Se impostata su "no", gli utenti potranno eseguire le operazioni di reimpostazione della password e di sblocco dell'account solo in abbinamento.</p>

              </td>
              <td>
                <p>
                  <strong>Nota:</strong>
                </p>
                <ul>
                  <li class="unordered">
												Per utilizzare questa funzionalità, è necessario installare la versione di agosto 2015 o una versione successiva di Azure AD Connect (v. 1.0.8667.0 o superiore).<br><br><a href="http://www.microsoft.com/download/details.aspx?id=47594">Fare clic qui per scaricare la versione più recente di Azure AD Connect.</a></li>
                        
                  <li class="unordered">
                    <strong>Nota:</strong> per testare questa funzionalità, è necessario abilitare il writeback delle password e utilizzare un account originato in locale (ad esempio un utente federato o con password sincronizzata) e bloccato. Per gli utenti non locali e che non hanno un account bloccato non verrà visualizzata l'opzione di sblocco del proprio account.</li>
                </ul>
                <p>
                  <strong>Portale di reimpostazione delle password:</strong>
                </p>
                <ul>
                  <li class="unordered">
												Dopo che questa opzione è stata abilitata, quando un utente con un account locale bloccato raggiunge il portale di reimpostazione delle password ha la possibilità di sbloccare l'account senza reimpostare la password.<br><br>Si noti che se si utilizza il writeback delle password gli account vengono sbloccati automaticamente quando si reimposta la password. Questa opzione quindi si limita a separare tali operazioni.<br><br>Si tratta di un'opzione particolarmente utile se molte delle chiamate all'helpdesk sono richieste di sblocco di account.</li>
                </ul>
              </td>
            </tr>
          </tbody></table>

## Notifiche di Gestione delle password
Nella tabella seguente viene descritto come ciascun controllo influisce sull'esperienza degli utenti e degli amministratori che ricevono notifiche di reimpostazione delle password. È possibile configurare queste opzioni nella sezione **Notifiche** della scheda **Configura** della directory nel [portale di gestione di Azure](https://manage.windowsazure.com).

<table>
            <tbody><tr>
              <td>
                <p>
                  <strong>Controllo dei criteri</strong>
                </p>
              </td>
              <td>
                <p>
                  <strong>Descrizione</strong>
                </p>
              </td>
              <td>
                <p>
                  <strong>Effetti</strong>
                </p>
              </td>
            </tr>
            <tr>
              <td>
                <div id="notify-admins-when-other-admins-reset-their-own-passwords">
                  <p>Inviare una notifica agli amministratori quando altri amministratori reimpostano le proprie password</p>
                </div>
              </td>
              <td>
                <p>Determina se a tutti gli amministratori globali viene inviata o meno una notifica tramite un messaggio di posta elettronica all’indirizzo di posta elettronica principale quando un altro amministratore di qualsiasi tipo reimposta la propria password.</p>
              </td>
              <td>
                <p>
                  <strong>Portale di reimpostazione delle password:</strong>
                </p>
                <ul>
                  <li class="unordered">
												Se impostato su no, non viene inviata alcuna notifica.<br><br></li>
                  <li class="unordered">
												Se impostato su Sì, a tutti gli altri amministratori globali viene inviata una notifica quando un singolo amministratore reimposta la propria password.<br><br></li>
                  <li class="unordered">
												Questa notifica viene inviata tramite un messaggio di posta elettronica agli indirizzi di posta elettronica principali di tutti gli altri amministratori globali dell'organizzazione.<br><br></li>
                </ul>
                <p>
                  <strong>Esempio:</strong>
                </p>
                <ul>
                  <li class="unordered">
												Se questa opzione è stata abilitata, quando l’amministratore A reimposta la propria password e nel tenant sono presenti altri 3 amministratori, B, C e D, gli amministratori B, C e D ricevono un messaggio di posta elettronica che indica che l’amministratore A ha reimpostato la propria password.<br><br></li>
                </ul>
              </td>
            </tr>
            <tr>
              <td>
                <div id="notify-users-and-admins-when-their-own-password-has-been-reset">
                  <p>Inviare una notifica a utenti e amministratori quando la loro password è stata reimpostata</p>
                </div>
              </td>
              <td>
                <p>Determina se gli utenti finali o gli amministratori che reimpostano le proprie password ricevono o meno una notifica tramite un messaggio di posta elettronica quando la loro password è stata reimpostata.</p>
              </td>
              <td>
                <p>
                  <strong>Portale di reimpostazione delle password:</strong>
                </p>
                <ul>
                  <li class="unordered">
												Se impostato su no, non viene inviata alcuna notifica.<br><br></li>
                  <li class="unordered">
												Se impostato su Sì, ogni volta che un utente o un amministratore reimposta la propria password, riceve una notifica che indica che la sua password è stata reimpostata.<br><br></li>
                  <li class="unordered">
												Questa notifica viene inviata tramite un messaggio di posta elettronica all’indirizzo di posta elettronica principale e alternativo (o di autenticazione) dell'utente che ha reimpostato la propria password.<br><br></li>
                </ul>
              </td>
            </tr>
          </tbody></table>


<br/> <br/> <br/>

## Collegamenti alla documentazione relativa alla reimpostazione della password
Di seguito vengono forniti collegamenti a tutte le pagine della documentazione relative alla reimpostazione della password in Azure AD:

* [**Reimpostare la password personale**](active-directory-passwords-update-your-own-password.md): informazioni su come reimpostare o modificare la password personale come utente del sistema
* [**Funzionamento**](active-directory-passwords-how-it-works.md): informazioni sui sei diversi componenti del servizio e sulle relative funzioni
* [**Introduzione**](active-directory-passwords-getting-started.md): informazioni su come consentire agli utenti di reimpostare e modificare le password cloud o locali
* [**Procedure consigliate**](active-directory-passwords-best-practices.md): informazioni su come distribuire rapidamente e gestire in modo efficace le password nell'organizzazione
* [**Informazioni dettagliate**](active-directory-passwords-get-insights.md): informazioni sulle funzionalità di creazione report integrate
* [**Domande frequenti**](active-directory-passwords-faq.md): risposte alle domande frequenti
* [**Risoluzione dei problemi**](active-directory-passwords-troubleshoot.md): informazioni su come risolvere rapidamente eventuali problemi con il servizio
* [**Ulteriori informazioni**](active-directory-passwords-learn-more.md): approfondimenti sui dettagli tecnici del funzionamento del servizio


[001]: ./media/active-directory-passwords-customize/001.jpg "Image_001.jpg"

<!---HONumber=AcomDC_0218_2016-->