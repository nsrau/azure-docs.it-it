## <a name="test-your-code"></a>Testare il codice

Per eseguire il progetto, in Visual Studio selezionare **F5**. Viene visualizzata l'applicazione **MainWindow**, come mostrato di seguito:

![Testare l'applicazione](./media/active-directory-develop-guidedsetup-windesktop-test/samplescreenshot.png)

Alla prima esecuzione dell'applicazione, quando si seleziona il pulsante **Call Microsoft Graph API** (Chiama API Microsoft Graph), viene richiesto l'accesso. Usare un account Azure Active Directory (aziendale o dell'istituto di istruzione) o un account Microsoft (live.com, outlook.com) per eseguire il test.

![Accedere all'applicazione](./media/active-directory-develop-guidedsetup-windesktop-test/signinscreenshot.png)

### <a name="provide-consent-for-application-access"></a>Specificare il consenso per l'accesso all'applicazione
Al primo accesso all'applicazione viene richiesto anche di specificare il consenso per permettere all'applicazione di accedere al profilo e di completare l'accesso per l'utente, come mostrato di seguito: 

![Specificare il consenso per l'accesso all'applicazione](./media/active-directory-develop-guidedsetup-windesktop-test/consentscreen.png)

### <a name="view-application-results"></a>Visualizzare i risultati dell'applicazione
Dopo l'accesso dovrebbero essere visualizzate le informazioni sul profilo utente restituite dalla chiamata all'API Microsoft Graph . I risultati vengono visualizzati nella casella **API Call Results** (Risultati della chiamata API). Le informazioni di base sul token acquisito tramite la chiamata a `AcquireTokenAsync` o `AcquireTokenSilentAsync` dovrebbero essere visibili nella casella **Token Info** (Info sul token). I risultati contengono le proprietà seguenti:

|Proprietà  |Format  |DESCRIZIONE |
|---------|---------|---------|
|**Nome** |Nome e cognome dell'utente |Nome e cognome dell'utente.|
|**Nome utente** |<span>user@domain.com</span> |Nome utente usato per identificare l'utente.|
|**Token Expires** (Scadenza token) |Datetime |Ora in cui scadrà il token. MSAL estende la data di scadenza rinnovando il token in base alla necessità.|
|**Token di accesso** |string |Stringa di token inviata alle richieste HTTP che necessitano di un'*Intestazione dell'autorizzazione*.|

<!--start-collapse-->
### <a name="more-information-about-scopes-and-delegated-permissions"></a>Altre informazioni sugli ambiti e sulle autorizzazioni delegate

L'API Microsoft Graph richiede l'ambito *user.read* per leggere il profilo dell'utente. Per impostazione predefinita, questo ambito viene aggiunto automaticamente in ogni applicazione registrata nel portale di registrazione dell'applicazione. Altre API per Microsoft Graph e le API personalizzate per il server di back-end potrebbero richiedere anche altri ambiti. L'API Microsoft Graph richiede l'ambito *Calendars.Read* per elencare i calendari dell'utente.

Per accedere ai calendari dell'utente nel contesto di un'applicazione, aggiungere l'autorizzazione delegata *Calendars.Read* alle informazioni di registrazione dell'applicazione. Aggiungere quindi l'ambito *Calendars.Read* alla chiamata `acquireTokenSilent`. 

>[!NOTE]
>Con l'aumentare del numero di ambiti è possibile che all'utente venga chiesto di esprimere anche altri tipi di consenso.

<!--end-collapse-->

[!INCLUDE  [Help and support](./active-directory-develop-help-support-include.md)]
