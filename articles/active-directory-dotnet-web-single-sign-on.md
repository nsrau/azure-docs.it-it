<properties linkid="" urlDisplayName="" pageTitle="" metaKeywords="" description="" metaCanonical="" services="" documentationCenter="" title="Web Single Sign-On with .NET and Azure Active Directory" authors="" solutions="" manager="" editor="" />

Single Sign-On Web con .NET e Azure Active Directory
====================================================

Introduzione
------------

In questa esercitazione viene illustrato come utilizzare Azure Active Directory per abilitare Single Sign-On per gli utenti di Office 365. Si apprenderà come:

-   Eseguire il provisioning dell'applicazione Web nel tenant del cliente
-   Proteggere l'applicazione mediante WS-Federation

### Prerequisiti

Per questa procedura dettagliata sono necessari i prerequisiti seguenti nell'ambiente di sviluppo:

-   Visual Studio 2010 SP1
-   Microsoft .NET Framework 4.0
-   [ASP.NET MVC 3](http://www.microsoft.com/en-us/download/details.aspx?id=4211)
-   [Windows Identity Foundation 1.0 Runtime](http://www.microsoft.com/en-us/download/details.aspx?id=17331)
-   [Windows Identity Foundation 3.5 SDK](http://www.microsoft.com/en-us/download/details.aspx?id=4451)
-   Internet Information Services (IIS) 7.5 con SSL abilitato
-   Windows PowerShell
-   [Cmdlet di Office 365 PowerShell](http://onlinehelp.microsoft.com/en-us/office365-enterprises/ff652560.aspx)

### Sommario

-   [Introduzione](#introduction)
-   [Passaggio 1: Creare un'applicazione ASP.NET MVC](#createapp)
-   [Passaggio 2: Eseguire il provisioning dell'applicazione nel tenant della directory della società](#provisionapp)
-   [Passaggio 3: Proteggere l'applicazione mediante WS-Federation per l'accesso dei dipendenti](#protectapp)
-   [Riepilogo](#summary)

Passaggio 1: Creare un'applicazione ASP.NET MVC
-----------------------------------------------

In questo passaggio viene descritto come creare una semplice applicazione ASP.NET MVC 3 che rappresenterà una risorsa protetta. L'accesso a questa risorsa verrà concesso tramite autenticazione federata gestita dal servizio token di sicurezza della società, descritto più avanti nell'esercitazione.

1.  Aprire Visual Studio come amministratore.
2.  Scegliere **Nuovo progetto** dal menu **File**.
3.  Nel menu dei modelli a sinistra scegliere **Web**, quindi fare clic su **Applicazione Web ASP.NET MVC 3**. Assegnare al progetto il nome *OrgIdFederationSample*.
4.  Nella finestra di dialogo **Nuovo progetto ASP.NET MVC 3** selezionare li modello **Vuoto**, quindi fare clic su **OK**.
5.  Dopo la generazione del nuovo progetto in Visual Studio, fare clic con il pulsante destro del mouse sulla cartella **Controllers** in **Esplora soluzioni**, scegliere **Aggiungi** e quindi fare clic su **Controller**.
6.  Nella finestra di dialogo **Aggiungi controller** assegnare al nuovo controller il nome *HomeController.cs*, quindi fare clic su **Aggiungi**.
7.  Verrà creato e aperto il file **HomeController.cs**. Nel file del codice fare clic con il pulsante destro del mouse sul metodo ***Index()*** e scegliere **Aggiungi visualizzazione**.
8.  Nella finestra di dialogo **Aggiungi visualizzazione** fare clic su **OK**. Verrà creato un file **Index.cshtml** in una nuova cartella denominata **Home**.
9.  Fare clic con il pulsante destro del mouse sul progetto **OrgIdFederationSample** in **Esplora soluzioni**, quindi scegliere **Proprietà**.
10. Nella finestra Proprietà fare clic su **Web** nel menu di sinistra, quindi selezionare **Usa server Web IIS locale**. È possibile che venga visualizzata una finestra di dialogo in cui si chiede di creare una directory virtuale per il progetto. Fare clic su **Sì**.
11. Compilare ed eseguire l'applicazione. Verrà visualizzata la pagina di indice del controller Home.

Passaggio 2: Eseguire il provisioning dell'applicazione nel tenant della directory della società
------------------------------------------------------------------------------------------------

In questo passaggio vengono descritti il provisioning dell'applicazione MVC nel tenant di un cliente e la configurazione di Single Sign-On. Queste attività devono essere eseguite da un amministratore di Azure Active Directory del cliente. Dopo avere completato questo passaggio, i dipendenti della società potranno eseguire l'autenticazione all'applicazione Web utilizzando gli account Office 365.

Il processo di provisioning inizia con la creazione di una nuova entità servizio per l'applicazione. In Azure Active Directory le entità servizio vengono utilizzate per registrare e autenticare le applicazioni per la directory.

1.  Se necessario, scaricare e installare i cmdlet di Office 365 PowerShell.
2.  Nel menu **Start** eseguire la console **Modulo dei Microsoft Online Services per Windows PowerShell**. Questa console offre un ambiente da riga di comando per la configurazione di attributi relativi al tenant di Office 365, ad esempio la creazione e la modifica di entità servizio.
3.  Per importare il modulo **MSOnlineExtended** richiesto, digitare il comando seguente e premere INVIO:

         Import-Module MSOnlineExtended -Force

4.  Per connettersi alla directory di Office 365, è necessario fornire le credenziali di amministratore della società. Digitare il comando seguente e premere INVIO, quindi immettere le credenziali quando richiesto:

         Connect-MsolService

5.  A questo punto è possibile creare una nuova entità servizio per l'applicazione. Digitare il comando seguente e premere INVIO:

         New-MsolServicePrincipal -ServicePrincipalNames @("OrgIdFederationSample/localhost") -DisplayName "Federation Sample Web Site" -Type Symmetric -Usage Verify -StartDate "12/01/2012" -EndDate "12/01/2013" 

    In questo passaggio verranno visualizzate informazioni simili alle seguenti:

         The following symmetric key was created as one was not supplied qY+Drf20Zz+A4t2w e3PebCopoCugO76My+JMVsqNBFc=
         DisplayName           : Federation Sample Web Site
         ServicePrincipalNames : {OrgIdFederationSample/localhost}
         ObjectId              : 59cab09a-3f5d-4e86-999c-2e69f682d90d
         AppPrincipalId        : 7829c758-2bef-43df-a685-717089474505
         TrustedForDelegation  : False
         AccountEnabled        : True
         KeyType               : Symmetric
         KeyId                 : f1735cbe-aa46-421b-8a1c-03b8f9bb3565
         StartDate             : 12/01/2012 08:00:00 a.m.
         EndDate               : 12/01/2013 08:00:00 a.m.
         Usage                 : Verify 

    **Nota**

    È consigliabile salvare questo output, in particolare la chiave simmetrica generata. Questa chiave viene visualizzata solo durante la creazione dell'entità servizio e non sarà recuperabile in seguito. Gli altri valori sono necessari per utilizzare l'API Graph per leggere e scrivere informazioni nella directory.

6.  Con l'ultimo passaggio viene impostato l'URL di risposta per l'applicazione, ossia l'indirizzo a cui vengono inviate le risposte in seguito ai tentativi di autenticazione. Digitare i comandi seguenti e premere INVIO:

         $replyUrl = New-MsolServicePrincipalAddresses -Address "https://localhost/OrgIdFederationSample" 

         Set-MsolServicePrincipal -AppPrincipalId "7829c758-2bef-43df-a685-717089474505" -Addresses $replyUrl 

A questo punto, il provisioning dell'applicazione Web è stato completato nella directory ed è possibile utilizzarla per consentire ai dipendenti della società l'accesso Single Sign-On al Web.

Passaggio 3: Proteggere l'applicazione mediante WS-Federation per l'accesso dei dipendenti
------------------------------------------------------------------------------------------

In questo passaggio viene illustrato come aggiungere il supporto per l'accesso federato mediante Windows Identity Foundation (WIF). Verrà inoltre aggiunta una pagina di accesso e verranno configurati i criteri di attendibilità tra l'applicazione e il tenant della directory.

1.  In Visual Studio fare clic con il pulsante destro del mouse sul progetto **OrgIdFederationSample** e scegliere **"Add STS reference..."**. Questa opzione del menu di scelta rapida è stata aggiunta durante l'installazione di WIF SDK.
2.  Nella finestra di dialogo **Federation Utility**, specificare l'URI in **Application URI** nel formato seguente:

         spn:<Your AppPrincipalId>@<Your Directory Domain>

    **spn** specifica che l'URI è il nome di un'entità servizio, **Your AppPrincpalId** è il valore GUID di **AppPrincipalId** generato quando è stata creata un'entità servizio e **Your Directory Domain** è il dominio \*.onmicrosoft.com per il tenant della directory. Per questa applicazione di esempio, viene specificato un valore completo per l'URI dell'applicazione, come illustrato di seguito:

         spn:7829c758-2bef-43df-a685-717089474505@awesomecomputers.onmicrosoft.com

    Dopo aver immesso l'URI dell'applicazione, fare clic su **Avanti**.

3.  Verrà visualizzata una finestra di dialogo di avviso indicante che l'applicazione non è ospitata in una connessione HTTPS sicura. Questo messaggio è dovuto al fatto che l'utilità di federazione non riconosce il formato **spn:**, ma l'applicazione utilizzerà comunque una connessione HTTPS sicura. Fare clic su **Sì** per continuare.

4.  Nella pagina successiva dell'utilità di federazione selezionare **Use an existing STS**, quindi in **STS WS-Federation metadata document location** immettere l'URL del documento di metadati WS-Federation. Questo URL viene specificato nel formato seguente:

         https://accounts.accesscontrol.windows.net/<Domain Name or Tenant ID>/FederationMetadata/2007-06/FederationMetadata.xml 

    Per questa applicazione, il percorso dei metadati di WS-Federation viene specificato come illustrato di seguito:

         https://accounts.accesscontrol.windows.net/fabrikam.onmicrosoft.com/FederationMetadata/2007-06/FederationMetadata.xml 

    Dopo aver immesso il percorso dei metadati, fare clic su **Avanti**.

5.  Nella pagina successiva dell'utilità di federazione selezionare **Disable certificate chain validation**, quindi fare clic su **Avanti**.

6.  Nella pagina successiva dell'utilità di federazione selezionare **No encryption**, quindi fare clic su **Avanti**.

7.  Nella pagina successiva dell'utilità di federazione vengono visualizzate le attestazioni fornite dal servizio token di sicurezza. Esaminare le attestazioni, quindi fare clic su **Avanti**.

8.  A questo punto verrà configurato IIS (Internet Information Services) per il supporto di SSL nell'ambiente di sviluppo. Per aprire Gestione IIS, è possibile digitare *inetmgr* al prompt **Esegui**.

9.  In Gestione IIS espandere la cartella **Sites** nel menu di sinistra, quindi fare clic su **Default Web Site Home**. Nel menu **Azioni** a destra scegliere **Binding**.

10. Nella finestra di dialogo **Binding sito** fare clic su **Aggiungi**. Nella finestra di dialogo **Aggiungi binding sito** cambiare la voce dell'elenco a discesa **Tipo** in ***https***, quindi in **Certificato SSL** selezionare **IIS Express Development Certificate**. Fare clic su **OK**.

11. In Gestione IIS scegliere **Pool di applicazioni** dal menu di sinistra, quindi selezionare la voce **ASP.NET v4.0** nell'elenco e scegliere **Impostazioni avanzate** dal menu **Azioni** a destra.

12. Nella finestra di dialogo **Impostazioni avanzate** impostare la proprietà **Carica profilo utente** su **True**. Fare clic su **OK**.

13. In Visual Studio aprire il file **Web.config** da **Esplora soluzioni** nella radice del progetto.

14. Nel file **Web.config** individuare la sezione **wsFederation** e aggiungere un attributo **reply** con lo stesso valore della variabile **\$replyUrl** specificato durante la creazione dell'entità servizio, ad esempio:

        <wsFederation passiveRedirectEnabled="true" issuer="https://accounts.accesscontrol.windows.net/v2/wsfederation" realm="spn: 7829c758-2bef-43df-a685-717089474505" requireHttps="false" reply="https://localhost/OrgIdFederationSample" /> 

15. Aggiungere un nodo **httpRuntime** all'interno della sezione **system.web** con l'attributo **requestValidationMode** impostato su **2.0**, ad esempio:

        <system.web>
            <httpRuntime requestValidationMode="2.0" /> 
            ...

    Salvare il file **Web.config**.

16. A questo punto, dopo aver abilitato l'autenticazione per l'applicazione Web, è necessario modificare la pagina **Index** per presentare le attestazioni di un utente autenticato. Aprire il file **Index.cshtml** nella cartella **Home** della cartella **Views**.

17. Aggiungere il frammento di codice seguente nel file **Index.cshtml** e salvarlo:

        <p>
            @if (User.Identity.IsAuthenticated)
            {
            <ul>
                @foreach (string claim in ((Microsoft.IdentityModel.Claims.IClaimsIdentity)this.User.Identity).Claims.Select(c => c.ClaimType + " : " + c.Value))
                {
                    <li>@claim</li>
                }
            </ul>
            }
        </p> 

18. Dopo aver salvato le modifiche apportate al file **Index.cshtml**, premere **F5** per eseguire l'applicazione. Si verrà reindirizzati alla pagina del provider di identità di Office 365, dove è possibile effettuare l'accesso utilizzando le credenziali del tenant della directory, ad esempio *john.doe@awesomecomputers.onmicrosoft.com*.

19. Dopo aver effettuato l'accesso con le proprie credenziali, si verrà reindirizzati alla pagina di indice del controller Home, dove vengono visualizzate le attestazioni dell'account. Ciò dimostra che l'autenticazione all'applicazione è stata eseguita correttamente tramite le funzionalità Single Sign-On fornite da Azure Active Directory.

Riepilogo
---------

In questa esercitazione è stato illustrato come creare e configurare un'applicazione a singolo tenant che utilizza le funzionalità Single Sign-On di Azure Active Directory. È anche possibile creare applicazioni multitenant per Azure Active Directory consultando l'esercitazione seguente: [Sviluppo di applicazioni per cloud multitenant con Azure Active Directory](http://g.microsoftonline.com/0AX00en/121).

