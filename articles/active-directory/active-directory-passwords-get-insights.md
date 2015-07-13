<properties 
	pageTitle="Come ottenere informazioni dettagliate con i report di gestione delle password di Azure AD | Microsoft Azure" 
	description="Questo articolo descrive come usare i report per ottenere informazioni dettagliate sulle operazioni di gestione delle password nell'organizzazione." 
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

# Come ottenere informazioni dettagliate con i report di gestione delle password
Questa sezione descrive come è possibile usare i report di gestione delle password di Azure Active Directory per visualizzare come gli utenti usano la modifica e la reimpostazione delle password nell'organizzazione.

- [**Informazioni generali sui report di gestione delle password**](#overview-of-password-management-reports)
- [**Come visualizzare i report di gestione delle password**](#how-to-view-password-management-reports)
- [**Visualizzare le attività di registrazione per la reimpostazione delle password nell'organizzazione**](#view-password-reset-registration-activity)
- [**Visualizzare le attività di reimpostazione delle password nell'organizzazione**](#view-password-reset-activity)

## Informazioni generali sui report di gestione delle password
Dopo aver distribuito la funzionalità di reimpostazione delle password, uno dei passaggi successivi più comuni consiste nel verificarne l'uso nell'organizzazione. Ad esempio, è possibile ottenere informazioni dettagliate sulla modalità di registrazione degli utenti alla funzionalità di reimpostazione delle password o sul numero di reimpostazioni delle password eseguite negli ultimi giorni. Di seguito sono elencate alcune delle domande più comuni a cui sarà possibile rispondere con i report di gestione delle password presenti nel [portale di gestione di Azure](https://manage.windowsazure.com):

- Quante persone si sono registrate per la reimpostazione delle password?
- Chi ha eseguito la registrazione per la reimpostazione delle password?
- Quali dati vengono registrati?
- Quante persone hanno reimpostato le proprie password negli ultimi 7 giorni?
- Quali sono i metodi più comuni usati da utenti o amministratori per reimpostare le proprie password?
- Quali sono i problemi più comuni affrontati da utenti o amministratori quando provano a usare la reimpostazione delle password?
- Con quale frequenza gli amministratori reimpostano le proprie password?
- Sono state rilevate eventuali attività sospette nell'ambito della reimpostazione delle password?


## Come visualizzare i report di gestione delle password
Per trovare i report di gestione delle password, seguire questa procedura:

1.	Fare clic sull'estensione **Active Directory** nel [portale di gestione di Azure](https://manage.windowsazure.com).
2.	Selezionare la directory dall'elenco visualizzato nel portale.
3.	Fare clic sulla scheda **Report**.
4.	Controllare la sezione **Log attività**.
5.	Selezionare il report **Attività di reimpostazione password** o **Attività di registrazione reimpostazione password**.

    ![][001]

## Visualizzare le attività di registrazione per la reimpostazione delle password nell'organizzazione

Il report relativo alle attività di registrazione per la reimpostazione delle password illustra tutte le registrazioni per la reimpostazione delle password che si sono verificate nell'organizzazione. Questo report visualizza le registrazioni per la reimpostazione delle password di tutti agli utenti che hanno registrato correttamente le informazioni di autenticazione nel portale di registrazione per la reimpostazione delle password ([http://aka.ms/ssprsetup](http://aka.ms/ssprsetup)).

- **Intervallo di tempo massimo**: 1 mese
- **Numero massimo di righe**: illimitato
- **Scaricabile**: Sì, tramite file CSV

    ![][002]

### Descrizione delle colonne del report
L'elenco seguente descrive le colonne del report in modo dettagliato:

- **Utente**: l'utente che ha provato a eseguire un'operazione di registrazione per la reimpostazione delle password.
- **Ruolo**: il ruolo dell'utente nella directory.
- **Data e ora**: la data e l'ora del tentativo.
- **Dati registrati**: i dati di autenticazione specificati dall'utente durante la registrazione per la reimpostazione delle password.

### Descrizione dei valori del report
La tabella seguente descrive i valori consentiti per ogni colonna:

<table>
            <tbody><tr>
              <td>
                <p>
                  <strong>Colonna</strong>
                </p>
              </td>
              <td>
                <p>
                  <strong>Valori consentiti e relativi significati</strong>
                </p>
              </td>
            </tr>
            <tr>
              <td>
                <p>Dati registrati</p>
              </td>
              <td>
                <ul>
                  <li class="unordered">
                    <strong>Indirizzo di posta elettronica alternativo</strong>: l'utente ha usato un indirizzo di posta elettronica alternativo o di autenticazione per autenticarsi<br><br></li>
                </ul>
                <ul>
                  <li class="unordered">
                    <strong>Telefono ufficio</strong>: l'utente ha usato il telefono dell'ufficio per autenticarsi<br><br></li>
                </ul>
                <ul>
                  <li class="unordered">
                    <strong>Telefono cellulare</strong>: l'utente ha usato il telefono cellulare o di autenticazione per autenticarsi<br><br></li>
                </ul>
                <ul>
                  <li class="unordered">
                    <strong>Domande di sicurezza</strong>: l'utente ha usato le domande di sicurezza per autenticarsi<br><br></li>
                </ul>
                <ul>
                  <li class="unordered">
                    <strong>Qualsiasi combinazione dei valori precedenti (ad esempio Indirizzo di posta elettronica alternativo + Telefono cellulare)</strong>: si verifica quando viene specificato un criterio di 2 controlli e indica i due metodi usati dall'utente per l'autenticazione della richiesta di reimpostazione della propria password.<br><br></li>
                </ul>
              </td>
            </tr>
          </tbody></table>

## Visualizzare le attività di reimpostazione delle password nell'organizzazione

Questo report illustra tutti i tentativi di reimpostazione delle password che si sono verificati nell'organizzazione.

- **Intervallo di tempo massimo**: 1 mese
- **Numero massimo di righe**: illimitato
- **Scaricabile**: Sì, tramite file CSV

    ![][003]

### Descrizione delle colonne del report
L'elenco seguente descrive le colonne del report in modo dettagliato:

1. **Utente**: l'utente che ha provato a eseguire un'operazione di reimpostazione delle password (in base al campo ID utente specificato quando l'utente accede per reimpostare una password).
2. **Ruolo**: il ruolo dell'utente nella directory.
3. **Data e ora**: la data e l'ora del tentativo.
4. **Metodi usati**: i metodi di autenticazione usati dall'utente per questa operazione di reimpostazione.
5. **Risultato**: il risultato finale dell'operazione di reimpostazione delle password.
6. **Dettagli**: i dettagli del motivo per cui la reimpostazione delle password ha restituito un determinato valore. Sono inoltre inclusi eventuali passaggi che è possibile eseguire per risolvere un errore imprevisto.

### Descrizione dei valori del report
La tabella seguente descrive i valori consentiti per ogni colonna:

<table>
            <tbody><tr>
              <td>
                <p>
                  <strong>Colonna</strong>
                </p>
              </td>
              <td>
                <p>
                  <strong>Valori consentiti e relativi significati</strong>
                </p>
              </td>
            </tr>
            <tr>
              <td>
                <p>Metodi usati</p>
              </td>
              <td>
                <ul>
                  <li class="unordered">
                    <strong>Indirizzo di posta elettronica alternativo</strong>: l'utente ha usato un indirizzo di posta elettronica alternativo o di autenticazione per autenticarsi<br><br></li>
                </ul>
                <ul>
                  <li class="unordered">
                    <strong>Telefono ufficio</strong>: l'utente ha usato il telefono dell'ufficio per autenticarsi<br><br></li>
                </ul>
                <ul>
                  <li class="unordered">
                    <strong>Telefono cellulare</strong>: l'utente ha usato il telefono cellulare o di autenticazione per autenticarsi<br><br></li>
                </ul>
                <ul>
                  <li class="unordered">
                    <strong>Domande di sicurezza</strong>: l'utente ha usato le domande di sicurezza per autenticarsi<br><br></li>
                </ul>
                <ul>
                  <li class="unordered">
                    <strong>Qualsiasi combinazione dei valori precedenti (ad esempio Indirizzo di posta elettronica alternativo + Telefono cellulare)</strong>: si verifica quando viene specificato un criterio di 2 controlli e indica i due metodi usati dall'utente per l'autenticazione della richiesta di reimpostazione della propria password.<br><br></li>
                </ul>
              </td>
            </tr>
            <tr>
              <td>
                <p>Risultato</p>
              </td>
              <td>
                <ul>
                  <li class="unordered">
                    <strong>Operazione abbandonata</strong>: l'utente ha iniziato l'operazione di reimpostazione delle password, ma l'ha interrotta senza completarla<br><br></li>
                </ul>
                <ul>
                  <li class="unordered">
                    <strong>Bloccato</strong>: all'account utente è stato impedito l'uso della reimpostazione delle password per aver provato a usare la pagina di reimpostazione delle password o un singolo controllo di reimpostazione delle password troppe volte in un periodo di 24 ore<br><br></li>
                </ul>
                <ul>
                  <li class="unordered">
                    <strong>Operazione annullata</strong>: l'utente ha iniziato l'operazione di reimpostazione delle password, ma poi ha fatto clic sul pulsante Annulla per annullare la sessione mentre era in corso <br><br></li>
                </ul>
                <ul>
                  <li class="unordered">
                    <strong>Amministratore contattato</strong>: l'utente ha riscontrato un problema durante la sessione che non è riuscito a risolvere e ha quindi selezionato il collegamento "Contattare l'amministratore" invece di portare a termine il flusso di reimpostazione delle password<br><br></li>
                </ul>
                <ul>
                  <li class="unordered">
                    <strong>Operazione non riuscita</strong>: l'utente non è stato in grado di reimpostare una password, probabilmente perché non è stato configurato per usare questa funzionalità (ad esempio, non dispone di licenza, non sono presenti informazioni di autenticazione, la password è gestita in locale, ma il writeback non è attivo).<br><br></li>
                </ul>
                <ul>
                  <li class="unordered">
                    <strong>Operazione completata</strong>: l'operazione di reimpostazione delle password è stata eseguita correttamente.<br><br></li>
                </ul>
              </td>
            </tr>
            <tr>
              <td>
                <p>Dettagli</p>
              </td>
              <td>
                <p>Vedere la tabella seguente</p>
              </td>
            </tr>
          </tbody></table>

### Valori consentiti per la colonna dettagli
Di seguito è riportato l'elenco dei tipi di risultati previsti quando si usa il report relativo alle attività per la reimpostazione delle password:

<table>
            <tbody><tr>
              <td>
                <p>
                  <strong>Dettagli</strong>
                </p>
              </td>
              <td>
                <p>
                  <strong>Tipo di risultato</strong>
                </p>
              </td>
            </tr>
            <tr>
              <td>
                <p>L'utente ha abbandonato dopo avere completato l'opzione di verifica tramite posta elettronica.</p>
              </td>
              <td>
                <p>Operazione abbandonata</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>L'utente ha abbandonato dopo avere completato l'opzione di verifica tramite SMS al numero di cellulare.</p>
              </td>
              <td>
                <p>Operazione abbandonata</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>L'utente ha abbandonato dopo avere completato l'opzione di verifica tramite chiamata vocale al numero di cellulare.</p>
              </td>
              <td>
                <p>Operazione abbandonata</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>L'utente ha abbandonato dopo avere completato l'opzione di verifica tramite chiamata vocale al numero dell'ufficio.</p>
              </td>
              <td>
                <p>Operazione abbandonata</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>L'utente ha abbandonato dopo avere completato l'opzione di verifica tramite risposta alle domande di sicurezza.</p>
              </td>
              <td>
                <p>Operazione abbandonata</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>L'utente ha abbandonato dopo avere immesso il proprio ID utente.</p>
              </td>
              <td>
                <p>Operazione abbandonata</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>L'utente ha abbandonato dopo avere avviato l'opzione di verifica tramite posta elettronica.</p>
              </td>
              <td>
                <p>Operazione abbandonata</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>L'utente ha abbandonato dopo avere avviato l'opzione di verifica tramite SMS al numero di cellulare.</p>
              </td>
              <td>
                <p>Operazione abbandonata</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>L'utente ha abbandonato dopo avere avviato l'opzione di verifica tramite chiamata vocale al numero di cellulare.</p>
              </td>
              <td>
                <p>Operazione abbandonata</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>L'utente ha abbandonato dopo avere avviato l'opzione di verifica tramite chiamata vocale al numero dell'ufficio.</p>
              </td>
              <td>
                <p>Operazione abbandonata</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>L'utente ha abbandonato dopo avere avviato l'opzione di verifica tramite risposta alle domande di sicurezza.</p>
              </td>
              <td>
                <p>Operazione abbandonata</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>L'utente ha abbandonato prima di selezionare una nuova password.</p>
              </td>
              <td>
                <p>Operazione abbandonata</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>L'utente ha abbandonato durante la selezione di una nuova password.</p>
              </td>
              <td>
                <p>Operazione abbandonata</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>L'utente ha immesso troppi codici di verifica tramite posta elettronica non validi e rimarrà bloccato per 24 ore.</p>
              </td>
              <td>
                <p>Bloccato</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>L'utente ha immesso troppi codici di verifica SMS non validi a e rimarrà bloccato per 24 ore.</p>
              </td>
              <td>
                <p>Bloccato</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>L'utente ha superato il numero di tentativi consentiti per la verifica tramite chiamata vocale al telefono cellulare e rimarrà bloccato per 24 ore.</p>
              </td>
              <td>
                <p>Bloccato</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>L'utente ha superato il numero di tentativi consentiti per la verifica tramite chiamata vocale al telefono dell'ufficio e rimarrà bloccato per 24 ore.</p>
              </td>
              <td>
                <p>Bloccato</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>L'utente ha superato il numero di tentativi consentiti per le risposte alle domande di sicurezza e rimarrà bloccato per 24 ore.</p>
              </td>
              <td>
                <p>Bloccato</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>L'utente ha superato il numero di tentativi consentiti per la verifica del numero di telefono e rimarrà bloccato per 24 ore.</p>
              </td>
              <td>
                <p>Bloccato</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>L'utente ha annullato prima di passare i metodi di autenticazione necessari.</p>
              </td>
              <td>
                <p>Operazione annullata</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>L'utente ha annullato prima di inviare una nuova password.</p>
              </td>
              <td>
                <p>Operazione annullata</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>L'utente ha contattato un amministratore dopo aver tentato l'opzione di verifica tramite posta elettronica.</p>
              </td>
              <td>
                <p>Amministratore contattato</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>L'utente ha contattato un amministratore dopo aver tentato l'opzione di verifica tramite SMS al numero di cellulare.</p>
              </td>
              <td>
                <p>Amministratore contattato</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>L'utente ha contattato un amministratore dopo aver tentato l'opzione di verifica tramite chiamata vocale al numero di cellulare.</p>
              </td>
              <td>
                <p>Amministratore contattato</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>L'utente ha contattato un amministratore dopo aver tentato l'opzione di verifica tramite chiamata vocale al numero dell'ufficio.</p>
              </td>
              <td>
                <p>Amministratore contattato</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>L'utente ha contattato un amministratore dopo aver tentato l'opzione di verifica della domanda di sicurezza.</p>
              </td>
              <td>
                <p>Amministratore contattato</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>La reimpostazione della password non è abilitata per questo utente. Per risolvere il problema, abilitare la reimpostazione della password nella scheda Configura.</p>
              </td>
              <td>
                <p>Operazione non riuscita</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>All'utente non sono assegnate licenze. Per risolvere il problema, assegnare una licenza all'utente.</p>
              </td>
              <td>
                <p>Operazione non riuscita</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>L'utente ha tentato di reimpostare la password da un dispositivo senza i cookie abilitati.</p>
              </td>
              <td>
                <p>Operazione non riuscita</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>Il numero di metodi di autenticazione definito per l'account dell'utente non è sufficiente. Per risolvere il problema, aggiungere le informazioni di autenticazione.</p>
              </td>
              <td>
                <p>Operazione non riuscita</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>La password dell'utente è gestita in locale. Per risolvere il problema, è possibile abilitare il writeback delle password.</p>
              </td>
              <td>
                <p>Operazione non riuscita</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>Non è stato possibile raggiungere il servizio di reimpostazione della password locale. Verificare il log eventi del computer di sincronizzazione.</p>
              </td>
              <td>
                <p>Operazione non riuscita</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>Si è verificato un problema durante la reimpostazione della password locale dell'utente. Verificare il log eventi del computer di sincronizzazione.</p>
              </td>
              <td>
                <p>Operazione non riuscita</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>Questo utente non è un membro del gruppo di utenti autorizzati alla reimpostazione della password. Per risolvere il problema, aggiungere l'utente al gruppo.</p>
              </td>
              <td>
                <p>Operazione non riuscita</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>La reimpostazione della password è stata completamente disabilitata per questo tenant. Per risolvere il problema, vedere <a href="http://aka.ms/ssprtroubleshoot">http://aka.ms/ssprtroubleshoot</a>.</p>
              </td>
              <td>
                <p>Operazione non riuscita</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>La reimpostazione della password dell'utente è riuscita.</p>
              </td>
              <td>
                <p>Operazione completata</p>
              </td>
            </tr>
          </tbody></table>

<br/> <br/> <br/>

**Risorse aggiuntive**


* [Informazioni sulla gestione delle password](active-directory-passwords.md)
* [Funzionamento della gestione delle password](active-directory-passwords-how-it-works.md)
* [Introduzione alla gestione delle password](active-directory-passwords-getting-started.md)
* [Personalizzare la gestione delle password](active-directory-passwords-customize.md)
* [Procedure consigliate per la gestione delle password](active-directory-passwords-best-practices.md)
* [Domande frequenti sulla gestione delle password](active-directory-passwords-faq.md)
* [Risolvere i problemi relativi alla gestione delle password](active-directory-passwords-troubleshoot.md)
* [Altre informazioni](active-directory-passwords-learn-more.md)
* [Gestione delle password in MSDN](https://msdn.microsoft.com/library/azure/dn510386.aspx)



[001]: ./media/active-directory-passwords-get-insights/001.jpg "Image_001.jpg"
[002]: ./media/active-directory-passwords-get-insights/002.jpg "Image_002.jpg"
[003]: ./media/active-directory-passwords-get-insights/003.jpg "Image_003.jpg"
 

<!---HONumber=62-->