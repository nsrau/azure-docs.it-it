<properties linkid="develop-net-how-to-twilio-phone-call" urlDisplayName="Twilio Phone Call" pageTitle="How to make a phone call from Twilio (.NET) - Azure" metaKeywords="Azure .NET Twilio, Azure Twilio, Azure phone calls, Azure twilio, Azure SMS, Azure SMS, Azure voice calls, azure voice calls, Azure text messages, Azure text messages, ASP.NET twilio Azure" description="Learn how to make a phone call and send a SMS message with the Twilio API service on Azure. Code samples written in .NET." metaCanonical="" services="" documentationCenter=".NET" title="How to make a phone call using Twilio in a web role on Azure" authors="" solutions="" manager="" editor="" />

Come effettuare una chiamata tramite Twilio in un ruolo Web in Azure
====================================================================

In questa guida viene illustrato come utilizzare Twilio per effettuare una chiamata da una pagina Web ospitata in Azure. L'applicazione risultante chiede all'utente di inserire i valori relativi alla chiamata telefonica, come illustrato nella schermata seguente.

![Modulo di chiamata di Azure con Twilio e ASP.NET](./media/partner-twilio-cloud-services-dotnet-phone-call-web-role/WA_twilio_dotnet_basic_form.png)

SommarioSommario
----------------

-   [Prerequisiti](#twilio-prereqs)
-   [Procedura: Creare un modulo Web per l'esecuzione di una chiamata](#howtocreateform)
-   [Procedura: Creare il codice per l'esecuzione della chiamata](#howtocreatecode)
-   [Passaggi successivi](#nextsteps)
-   [Vedere anche](#seealso)

PrerequisitiPrerequisiti
------------------------

Per utilizzare il codice in questo argomento è necessario eseguire le operazioni seguenti:

1.  Ottenere un account e un token di autenticazione Twilio. Per iniziare a utilizzare Twilio, effettuare l'iscrizione alla pagina [https://www.twilio.com/try-twilio](http://www.twilio.com/try-twilio). Per informazioni sui prezzi di Twilio, visitare la pagina <http://www.twilio.com/pricing>. Per informazioni sull'API fornita da Twilio, vedere <http://www.twilio.com/voice/api>.
2.  Verificare il proprio numero di telefono con Twilio. Per informazioni su come verificare il numero di telefono, vedere <https://www.twilio.com/user/account/phone-numbers/verified#>. In alternativa, anziché utilizzare un numero esistente, è possibile acquistare un numero di telefono Twilio.
     Ai fini di questo esempio, il numero di telefono dell'ambiente sandbox di Twilio verrà utilizzato per inviare un messaggio al numero di telefono verificato. Il numero dell'ambiente sandbox può essere utilizzato solo per l'invio a numeri di telefono verificati.
3.  Aggiungere la libreria .NET di Twilio al ruolo Web. Vedere "Per aggiungere le librerie Twilio al progetto di ruolo Web" più avanti in questo argomento.

È necessario conoscere le modalità di creazione di un ruolo Web di base in Azure.

Creare un modulo Web per l'esecuzione di una chiamataProcedura: Creare un modulo Web per l'esecuzione di una chiamata
---------------------------------------------------------------------------------------------------------------------

### Per aggiungere le librerie Twilio al progetto di ruolo Web:

1.  Aprire la soluzione in Visual Studio.
2.  Fare clic con il pulsante destro del mouse su **Riferimenti**.
3.  Scegliere **Manage NuGet Packages**.
4.  Fare clic su **Online**.
5.  Nella casella di ricerca online digitare *twilio*.
6.  Fare clic su **Install** sul pacchetto Twilio.

Nel codice seguente viene illustrato come creare un modulo Web per recuperare i dati utente per l'esecuzione di una chiamata. In questo esempio viene creato un ruolo Web ASP.NET denominato **TwilioCloud**.

    <%@ Page Title="Home Page" Language="C#" MasterPageFile="~/Site.master"
        AutoEventWireup="true" CodeBehind="Default.aspx.cs"
        Inherits="WebRole1._Default" %>

    <asp:Content ID="HeaderContent" runat="server" ContentPlaceHolderID="HeadContent">
    </asp:Content>
    <asp:Content ID="BodyContent" runat="server" ContentPlaceHolderID="MainContent">
        <div>
            <asp:BulletedList ID="varDisplay" runat="server" BulletStyle="NotSet">
            </asp:BulletedList>
        </div>
        <div>
            <p>Fill in all fields and click <b>Make this call</b>.</p>
            <div>
                To:<br /><asp:TextBox ID="toNumber" runat="server" /><br /><br />
                Message:<br /><asp:TextBox ID="message" runat="server" /><br /><br />
                <asp:Button ID="callpage" runat="server" Text="Make this call"
                    onclick="callpage_Click" />
            </div>
        </div>
    </asp:Content>

Creare il codiceProcedura: Creare il codice per l'esecuzione della chiamata
---------------------------------------------------------------------------

Il codice seguente, chiamato quando l'utente completa il modulo, crea il messaggio di chiamata e genera la chiamata. In questo esempio, il codice viene eseguito sul gestore dell'evento onclick del pulsante del modulo. Nel codice seguente sostituire i valori segnaposto assegnati a **accountSID** e **authToken** con il proprio account e il token di autenticazione Twilio.

    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Web;
    using System.Web.UI;
    using System.Web.UI.WebControls;
    using Twilio;

    namespace WebRole1
    {
        public partial class _Default : System.Web.UI.Page
        {
            protected void Page_Load(object sender, EventArgs e)
            {

            }

            protected void callpage_Click(object sender, EventArgs e)
            {
                // Call porcessing happens here.

                // Use your account SID and authentication token instead of
                // the placeholders shown here.
                string accountSID = "ACNNNNNNNNNNNNNNNNNNNNNNNNNNNNNN";
                string authToken =  "NNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNN";

                // Instantiate an instance of the Twilio client.
                TwilioRestClient client;
                client = new TwilioRestClient(accountSID, authToken);

                // Retrieve the account, used later to retrieve the
                Twilio.Account account = client.GetAccount();
                string APIversuion = client.ApiVersion;
                string TwilioBaseURL = client.BaseUrl;

                this.varDisplay.Items.Clear();
                if (this.toNumber.Text == "" || this.message.Text == "")
                {
                    this.varDisplay.Items.Add(
                            "You must enter a phone number and a message.");
                }
                else
                {
                    // Retrieve the values entered by the user.
                    string to = this.toNumber.Text;
                    string myMessage = this.message.Text;

                    // Create a URL using the Twilio message and the user-entered
                    // text. You must replace spaces in the user's text with '%20'
                    // to make the text suitable for a URL.
                    String Url = "http://twimlets.com/message?Message%5B0%5D="
                            + myMessage.Replace(" ", "%20");

                    // Diplay the enpoint, API version, and the URL for the message.
                    this.varDisplay.Items.Add("Using Tilio endpoint "
                        + TwilioBaseURL);
                    this.varDisplay.Items.Add("Twilioclient API Version is "
                        + APIversuion);
                    this.varDisplay.Items.Add("The URL is " + Url);

                    // Instantiate the call options that are passed
                    // to the outbound call.
                    CallOptions options = new CallOptions();

                    // Set the call From, To, and URL values into a hash map.
                    // This sample uses the sandbox number provided by Twilio
                    // to make the call.
                    options.From = "+14155992671";
                    options.To = to;
                    options.Url = Url;

                    // Place the call.
                    var call = client.InitiateOutboundCall(options);
                    this.varDisplay.Items.Add("Call status: " + call.Status);
                }
            }
        }
    }

La chiamata viene effettuata e vengono visualizzati l'endpoint Twilio, la versione dell'API e lo stato della chiamata. Nella schermata seguente viene illustrato l'output di un'esecuzione di esempio.

![Risposta a chiamata di Azure tramite Twilio e ASP.NET](./media/partner-twilio-cloud-services-dotnet-phone-call-web-role/WA_twilio_dotnet_basic_form_output.png)

Per ulteriori informazioni su TwiML, vedere <http://www.twilio.com/docs/api/twiml>. Per ulteriori informazioni su &lt;Say\> e altri verbi TwiML, vedere <http://www.twilio.com/docs/api/twiml/say>.

Passaggi successiviPassaggi successivi
--------------------------------------

Questo codice ha lo scopo di illustrare le funzionalità di base dell'utilizzo di Twilio in un ruolo Web ASP.NET in Azure. Prima di eseguire la distribuzione in Azure in produzione, può essere necessario aggiungere ulteriori funzionalità per la gestione degli errori o per altri scopi. Ad esempio:

-   Anziché utilizzare un modulo Web, è possibile utilizzare l'archivio BLOB di Azure o un'istanza di database SQL di Azure per l'archiviazione di numeri di telefono e testo delle chiamate. Per informazioni sull'utilizzo dei BLOB in Azure, vedere [Come utilizzare il servizio di archiviazione BLOB di Azure in .NET](https://www.windowsazure.com/en-us/develop/net/how-to-guides/blob-storage/). Per informazioni sull'utilizzo del database SQL, vedere [Come utilizzare il database SQL di Azure in applicazioni .NET](https://www.windowsazure.com/en-us/develop/net/how-to-guides/sql-database/).
-   È possibile utilizzare RoleEnvironment.getConfigurationSettings per recuperare l'ID e il token di autenticazione dell'account Twilio dalle impostazioni di configurazione della distribuzione, anziché impostare i valori come hardcoded nel modulo. Per informazioni sulla classe RoleEnvironment, vedere [Spazio dei nomi Microsoft.WindowsAzure.ServiceRuntime](http://msdn.microsoft.com/en-us/library/windowsazure/microsoft.windowsazure.serviceruntime.aspx).
-   Leggere le linee guida sulla sicurezza di Twilio all'indirizzo [https://www.twilio.com/docs/security](http://www.twilio.com/docs/security).
-   Per ulteriori informazioni su Twilio, visitare la pagina [https://www.twilio.com/docs](http://www.twilio.com/docs).

Vedere ancheVedere anche
------------------------

-   [Come utilizzare Twilio per le funzionalità voce ed SMS in un ruolo Web](/en-us/develop/net/how-to-guides/twilio/)

