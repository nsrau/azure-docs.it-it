<properties linkid="Deploy_a_Secure_ASP.NET_Web_Forms_App_with_Membership_OAuth_and_SQL_Database_to_an_Azure_Web_Site" pageTitle="Deploy a Secure ASP.NET Web Forms App with Membership, OAuth, and SQL Database to an Azure Website" metaKeywords="Azure WebForms Hello World Tutorial, Azure Getting Started with WebForms Tutorial, Azure C# WebForms Hello World Tutorial, SQL Azure C# WebForms Tutorial" description="This tutorial shows you how to build a secure ASP.NET 4.5 Web Forms web app that incorporates a SQL Database and deploy the application to Azure." metaCanonical="" services="web-sites" documentationCenter=".NET" title="Deploy a secure ASP.NET 4.5 Web Forms app to an Azure Website" authors="erikre"  solutions="" manager="wpickett" editor="mollybos"  />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="erikre"></tags>

# Distribuire un'app Web Form ASP.NET sicura con appartenenza, OAuth e database SQL in un sito Web di Azure

Questa esercitazione illustra come compilare un'app Web Web Form ASP.NET 4.5 che incorpora un database SQL e distribuirla in Azure.

> [WACOM.NOTE]
> Per la versione MVC di questa esercitazione, vedere [Distribuzione di un'app ASP.NET MVC 5 sicura con appartenenza, OAuth e database SQL in un sito Web di Azure][]

È possibile aprire gratuitamente un account Azure e, se non si dispone già di Visual Studio 2013, con l'SDK verrà installato automaticamente Visual Studio Express 2013 per il Web. È possibile iniziare a sviluppare per Azure gratuitamente.

In questa esercitazione si presuppone che l'utente non abbia mai usato Microsoft Azure. Al termine dell'esercitazione, si disporrà di un'applicazione Web sicura, basata sui dati, in esecuzione nel cloud e in grado di usare un database cloud.

Si apprenderà come:

-   Creare un progetto Web Form ASP.NET 4.5 sicuro e pubblicarlo in un sito Web di Azure.
-   Usare OAuth e il database di appartenenza ASP.NET per proteggere l'applicazione.
-   Usare un singolo database sia per l'appartenenza che per i dati dell'applicazione.
-   Usare l'estensione Web Forms Scaffolding per creare pagine Web che permettono di modificare i dati.
-   Utilizzare la nuova API di appartenenza per aggiungere utenti e ruoli.
-   Utilizzare un database SQL per l'archiviazione di dati in Azure.

Verrà creata una semplice applicazione Web di elenco contatti basata su Web Form ASP.NET 4.5 che usa Entity Framework per l'accesso al database. L'immagine seguente mostra la pagina Web Form che consente l'accesso in lettura e scrittura al database:

![Pagina Contatti - Modifica][]

> [WACOM.NOTE]
>  Per completare l'esercitazione, è necessario un account Azure. Se non si dispone di un account, è possibile [attivare i benefici della sottoscrizione MSDN][] oppure [iscriversi per ottenere una versione di valutazione gratuita][].

Questa esercitazione include le sezioni seguenti:

-   [Configurare l'ambiente di sviluppo][]
-   [Configurare l'ambiente Azure][]
-   [Creare un'applicazione Web Form ASP.NET][]
-   [Aggiungere un database all'applicazione][]
-   [Abilitare SSL per il progetto][]
-   [Aggiungere un provider OAuth 2.0][]
-   [Usare l'API di appartenenza per limitare l'accesso][]
-   [Distribuire l'applicazione con il database in Azure][]
-   [Verificare il database][]
-   [Passaggi successivi][]

## Configurare l'ambiente di sviluppo

Per iniziare, installare Visual Studio 2013 e Azure SDK per .NET per configurare l'ambiente di sviluppo.

1.  Installare [Visual Studio 2013][] se non è già stato installato.
2.  Installare [Azure SDK per Visual Studio 2013][]. Per questa esercitazione è necessario installare Visual Studio 2013 prima di installare Azure SDK per Visual Studio 2013.

    > [WACOM.NOTE]
    >  In base al numero di dipendenze da SDK già presenti nel computer, l'installazione dell'SDK può richiedere tempi prolungati, da alcuni minuti ad almeno mezz'ora.

3.  Quando viene richiesto di eseguire o installare il file eseguibile di installazione, fare clic su **Esegui**.
4.  Nell'**Installazione guidata piattaforma Web** fare clic su **Installa** e procedere con l'installazione.
    ![Web Platform Installer][]

    > [WACOM.NOTE]
    > Se l'SDK è già installato, gli elementi da installare saranno pari a 0. Il numero di elementi da installare è indicato nella parte inferiore sinistra della finestra dell'**Installazione guidata piattaforma Web**.

5.  Se **Visual Studio Update 2** non è installato, scaricare e installare **[Visual Studio 2013 Update 2][]** o versione successiva.

    > [WACOM.NOTE]
    > È necessario installare Visual Studio 2013 Update 2 o versione successiva per usare Goggle OAuth 2.0 ed SSL localmente senza avvisi. L'Update 2 è necessario anche per usare l'estensione Web Forms Scaffolding.

Al termine dell'installazione, saranno disponibili tutti gli strumenti necessari per avviare lo sviluppo.

## Configurare l'ambiente Azure

In questa sezione verrà configurato l'ambiente Azure tramite la creazione di un sito Web di Azure e un database SQL in Azure.

### Creare un sito Web e un database SQL in Azure

In questa esercitazione il sito Web di Azure verrà eseguito in un ambiente di hosting condiviso, ovvero in macchine virtuali condivise con altri siti Web di Azure. Un ambiente di hosting condiviso è un punto di partenza economicamente conveniente per iniziare a utilizzare il cloud. In seguito, in caso di incremento del traffico Web, è possibile scalare l'applicazione in modo da soddisfare le nuove esigenze tramite l'esecuzione su macchine virtuali dedicate. Se è necessaria un'architettura più complessa, è possibile eseguire la migrazione a un servizio cloud Azure. I servizi cloud vengono eseguiti su macchine virtuali dedicate, che possono essere configurate in base alle proprie esigenze specifiche.

Database SQL di Azure è un servizio di database relazionale basato sul cloud che si avvale delle tecnologie SQL Server. Gli strumenti e le applicazioni utilizzabili con SQL Server sono utilizzabili anche con database SQL.

1.  Nel [portale di gestione di Azure][] fare clic su **Siti Web** nella scheda di sinistra, quindi su **Nuovo**.
    ![Web Platform Installer][1]
2.  Fare clic su **Sito Web** e quindi su **Creazione personalizzata**.
    ![Custom Create][]
    Verrà visualizzata la procedura guidata **Nuovo sito Web - Creazione personalizzata**.

3.  Nel passaggio **Crea sito Web** della procedura guidata immettere nella casella **URL** una stringa da usare come URL univoco per l'applicazione. L'URL completo sarà costituito da quanto immesso in questa casella e dal suffisso visualizzato accanto alla casella di testo. L'illustrazione mostra un URL che è probabilmente già occupato, quindi **sarà necessario scegliere un URL diverso**.
    ![Contacts - Create New Web Site][]
4.  Nell'elenco a discesa Piano di hosting Web scegliere l'area geografica più vicina alla propria località. Questa impostazione consente di specificare il data center in cui verrà eseguita la macchina virtuale.
5.  Nell'elenco a discesa **Database** scegliere **Create a free 20 MB SQL database**.
6.  Nella casella **DB Connection String Name** non modificare il valore predefinito *DefaultConnection*.
7.  Fare clic sulla freccia a destra nella parte inferiore della casella.
    La procedura guidata procede al passaggio **Specifica impostazioni di database**.
8.  Nella casella **Nome** immettere *`ContactDB`*.
    ![Database Settings][]
9.  Nella casella **Server**selezionare **Nuovo database SQL**.
    In alternativa, se è stato creato in precedenza un database SQL Server, è possibile selezionarlo dall'elenco a discesa.
10. Impostare l'opzione **Regione** sulla stessa area specificata per la creazione del sito Web.
11. Immettere il **Nome di accesso** e la **Password** dell'amministratore.
    Se è stata selezionata l'opzione **Nuovo server di database SQL**, in questo passaggio non sarà necessario immettere un nome e una password esistenti, ma un nuovo nome e una nuova password, definiti ora e da utilizzare in seguito per l'accesso al database. Se è stato selezionato un database SQL Server creato in precedenza, verrà richiesta la password del nome account SQL Server creato in precedenza. Per questa esercitazione non verrà selezionata la casella **Avanzate**.
12. Fare clic sul segno di spunta nella parte inferiore destra della finestra di dialogo per indicare che le operazioni sono state completate.

Il **portale di gestione di Azure** torna alla pagina **Siti Web** e nella colonna **Stato** viene indicato che la creazione del sito è in corso. In genere, dopo meno di un minuto nella colonna **Stato** viene indicato che il sito è stato creato correttamente. Il numero di siti disponibili nell'account viene visualizzato nella barra di spostamento a sinistra accanto all'icona **Siti Web** e il numero di database viene visualizzato accanto all'icona **Database SQL**.

## Creare un'applicazione Web Form ASP.NET

Il sito Web di Azure è stato creato, ma non include ancora alcun contenuto. Il passaggio successivo consiste nel creare l'app Web di Visual Studio che verrà pubblicata in Azure.

### Creare il progetto

1.  Scegliere **Nuovo progetto** dal menu **File** in Visual Studio.
    ![File Menu - New Project][]
2.  Selezionare il gruppo di modelli a sinistra in **Modelli** -\> **Visual C\#** -\> **Web**.
3.  Scegliere il modello **Applicazione Web ASP.NET** nella colonna centrale.
4.  Assegnare al progetto il nome *ContactManager* e fare clic su **OK**.
    ![New Project Dialog][]

    > [WACOM.NOTE]
    > Il nome del progetto in questa serie di esercitazioni è **ContactManager**. È consigliabile usare questo nome di progetto esatto in modo che il codice fornito nel corso di tutta la serie di esercitazioni funzioni come previsto.

5.  Nella finestra di dialogo **Nuovo progetto ASP.NET** selezionare il modello **Web Form**. Se è selezionata, deselezionare la casella di controllo **Ospita nel cloud** e fare clic su **OK**.
    ![New ASP.NET Project dialog box][]
    Verrà creata l'applicazione Web Form.

    ### Aggiornare la pagina master

    In Web Form ASP.NET le pagine master consentono di creare un layout uniforme per le pagine dell'applicazione. Una singola pagina master definisce l'aspetto e il comportamento standard da applicare a tutte le pagine (o a un gruppo di pagine) dell'applicazione. È quindi possibile creare singole pagine di contenuto per il contenuto da visualizzare. Quando gli utenti richiedono le pagine di contenuto, ASP.NET le unisce alla pagina master per generare l'output che combina il layout della pagina master con il contenuto della pagina di contenuto.
    Il nuovo sito necessita del nome dell'applicazione e di un collegamento aggiornato. Il collegamento indirizza a una pagina che visualizza i dettagli del contatto. Per apportare queste modifiche, sarà necessario modificare il codice HTML nella pagina master.

6.  In **Esplora soluzioni** trovare e aprire la pagina *Site.Master*.
7.  Se la pagina è in visualizzazione **Progettazione**, passare alla visualizzazione **Origine dati**.
8.  Per aggiornare la pagina master, modificare o aggiungere i commenti visualizzati in giallo:

``` prettyprint
<%@ Master Language="C#" AutoEventWireup="true" CodeBehind="Site.master.cs" Inherits="ContactManager.SiteMaster" %>

<!DOCTYPE html>

<html lang="en">
<head runat="server">
    <meta charset="utf-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title><%: Page.Title %> - Contact Manager</title>

    <asp:PlaceHolder runat="server">         <%: Scripts.Render("~/bundles/modernizr") %>
    </asp:PlaceHolder>
    <webopt:bundlereference runat="server" path="~/Content/css" />
    <link href="~/favicon.ico" rel="shortcut icon" type="image/x-icon" />

</head>
<body>
    <form runat="server">
        <asp:ScriptManager runat="server">
            <Scripts>
                <%--To learn more about bundling scripts in ScriptManager see http://go.microsoft.com/fwlink/?LinkID=301884 --%>
                <%--Framework Scripts--%>
                <asp:ScriptReference Name="MsAjaxBundle" />
                <asp:ScriptReference Name="jquery" />
                <asp:ScriptReference Name="bootstrap" />
                <asp:ScriptReference Name="respond" />
                <asp:ScriptReference Name="WebForms.js" Assembly="System.Web" Path="~/Scripts/WebForms/WebForms.js" />
                <asp:ScriptReference Name="WebUIValidation.js" Assembly="System.Web" Path="~/Scripts/WebForms/WebUIValidation.js" />
                <asp:ScriptReference Name="MenuStandards.js" Assembly="System.Web" Path="~/Scripts/WebForms/MenuStandards.js" />
                <asp:ScriptReference Name="GridView.js" Assembly="System.Web" Path="~/Scripts/WebForms/GridView.js" />
                <asp:ScriptReference Name="DetailsView.js" Assembly="System.Web" Path="~/Scripts/WebForms/DetailsView.js" />
                <asp:ScriptReference Name="TreeView.js" Assembly="System.Web" Path="~/Scripts/WebForms/TreeView.js" />
                <asp:ScriptReference Name="WebParts.js" Assembly="System.Web" Path="~/Scripts/WebForms/WebParts.js" />
                <asp:ScriptReference Name="Focus.js" Assembly="System.Web" Path="~/Scripts/WebForms/Focus.js" />
                <asp:ScriptReference Name="WebFormsBundle" />
                <%--Site Scripts--%>
            </Scripts>
        </asp:ScriptManager>

        <div class="navbar navbar-inverse navbar-fixed-top">
            <div class="container">
                <div class="navbar-header">
                    <button type="button" class="navbar-toggle" data-toggle="collapse" data-target=".navbar-collapse">
                        <span class="icon-bar"></span>
                        <span class="icon-bar"></span>
                        <span class="icon-bar"></span>
                    </button>
                    <a class="navbar-brand" runat="server" id="ContactDemoLink" href="~/Contacts/Default.aspx">Contact Demo</a>
                </div>
                <div class="navbar-collapse collapse">
                    <ul class="nav navbar-nav">
                        <li><a runat="server" href="~/">Home</a></li>
                        <li><a runat="server" href="~/About">About</a></li>
                        <li><a runat="server" href="~/Contact">Contact</a></li>
                    </ul>
                    <asp:LoginView runat="server" ViewStateMode="Disabled">
                        <AnonymousTemplate>
                            <ul class="nav navbar-nav navbar-right">
                                <li><a runat="server" href="~/Account/Register">Register</a></li>
                                <li><a runat="server" href="~/Account/Login">Log in</a></li>
                            </ul>
                        </AnonymousTemplate>
                        <LoggedInTemplate>
                            <ul class="nav navbar-nav navbar-right">
                                <li><a runat="server" href="~/Account/Manage" title="Manage your account">Hello, <%: Context.User.Identity.GetUserName()  %> !</a></li>
                                <li>
                                    <asp:LoginStatus runat="server" LogoutAction="Redirect" LogoutText="Log off" LogoutPageUrl="~/" OnLoggingOut="Unnamed_LoggingOut" />
                                </li>
                            </ul>
                        </LoggedInTemplate>
                    </asp:LoginView>
                </div>
            </div>
        </div>
        <div class="container body-content">
            <asp:ContentPlaceHolder ID="MainContent" runat="server">
            </asp:ContentPlaceHolder>
            <hr />
            <footer>
                <p>© <%: DateTime.Now.Year %> - Contact Manager</p>
            </footer>
        </div>
    </form>
</body>
</html>
```

Più avanti in questa esercitazione verrà aggiunta l'estensione Web Forms Scaffolding. Lo scaffolding creerà la pagina a cui fa riferimento il collegamento "Contact Demo" indicato sopra.

### Eseguire l'applicazione in locale

1.  Fare clic con il pulsante destro del mouse sulla pagina **Default.aspx** in *Esplora soluzioni*, quindi scegliere **Imposta come pagina iniziale**.
2.  Premere **CTRL+F5** per eseguire l'applicazione.
    La pagina predefinita dell'applicazione verrà visualizzata nella finestra del browser predefinito.
    ![Contacts - Create New Web Site][2]

Non è necessario eseguire per il momento altre operazioni per creare l'applicazione che verrà distribuita in Azure. Successivamente, verranno aggiunte la funzionalità di database e le pagine necessarie per visualizzare e modificare i dati di contatto.

### Distribuire l'applicazione in Azure

Dopo avere creato ed eseguito l'applicazione in locale, è possibile distribuirla in Azure.

1.  In Visual Studio fare clic con il pulsante destro del mouse in **Esplora soluzioni** e scegliere **Pubblica** dal menu di scelta rapida.
    ![Select Publish][]
    Verrà visualizzata la finestra di dialogo **Pubblica sito Web**.

2.  Nella scheda **Profilo** della finestra di dialogo **Pubblica sito Web** fare clic su **Siti Web di Azure**.
    ![Publish Web dialog box][]
3.  Se non è stato effettuato l'accesso, fare clic sul pulsante **Accedi** nella finestra di dialogo **Selezione sito Web esistente**. Una volta effettuato l'accesso, selezionare il sito Web creato nella prima parte di questa esercitazione. Fare clic su **OK** per continuare.
    ![Select Existing Web Site dialog box][]
    Visual Studio scaricherà le impostazioni di pubblicazione.
4.  Nella finestra di dialogo **Pubblica sito Web** fare clic su **Pubblica**.
    ![Publish Web dialog box][3]
    Verrà visualizzato lo stato di pubblicazione complessivo nella finestra **Attività di pubblicazione sito Web** in Visual Studio:
    ![Web Publishing Activity][]

L'applicazione creata verrà ora eseguita nel cloud. Alla successiva distribuzione dell'applicazione da Visual Studio, verranno distribuiti solo i file modificati o nuovi.
 ![App nel browser][]

> [WACOM.NOTE]
> Se durante la pubblicazione in un sito Web già esistente viene restituito un errore, è possibile cancellare il percorso prima di aggiungere i nuovi file.
> Pubblicare di nuovo l'applicazione, ricordando di selezionare la scheda **Impostazioni** nella finestra di dialogo **Pubblica sito Web**. Impostare quindi la configurazione su **Debug** e selezionare l'opzione **Rimuovi i file aggiuntivi nella destinazione**. Selezionare **Pubblica** per distribuire di nuovo l'applicazione.
>  ![Finestra di dialogo Pubblica sito Web][]

## Aggiungere un database all'applicazione

Si passerà quindi all'aggiornamento dell'applicazione Web Form, in modo da aggiungere la funzionalità per la visualizzazione e l'aggiornamento dei contatti e per l'archiviazione dei dati in un database. Durante la procedura di creazione del progetto Web Form, per impostazione predefinita è stato creato anche un database. L'applicazione userà Entity Framework per accedere al database e per leggere e aggiornare i dati nel database.

### Aggiungere una classe modello di dati

Creare innanzitutto un semplice modello di dati usando il codice. Questo modello di dati sarà contenuto in una classe denominata `Contacts`. Il nome della classe `Contacts` è stato scelto per evitare un conflitto di nomi di classi con la classe `Contact` contenuta nel file Contact.aspx.cs creata dal modello Web Form.

1.  In **Esplora soluzioni** fare clic con il pulsante destro del mouse sulla cartella *Modelli* e scegliere **Aggiungi** -\> **Classe**.
    ![Select Class][]
    Verrà visualizzata la finestra di dialogo **Aggiungi nuovo elemento**.

2.  Assegnare alla nuova classe il nome *Contacts.cs*.
    ![Add New Item dialog box][]
3.  Sostituire il codice predefinito con il codice seguente:

    ``` prettyprint
    using System.ComponentModel.DataAnnotations;using System.Globalization;
    namespace ContactManager.Models{ public class Contacts { [ScaffoldColumn(false)][Key] public int ContactId { get; set; } public string Name { get; set; } public string Address { get; set; } public string City { get; set; } public string State { get; set; } public string Zip { get; set; } [DataType(DataType.EmailAddress)] public string Email { get; set; } }}
    ```

    </p>

La classe **Contacts** consente di definire i dati che verranno archiviati per ogni contatto, oltre a una chiave primaria, `ContactID`, necessaria per il database. La classe **Contacts** rappresenta i dati del contatto che verranno visualizzati. Ogni istanza di un oggetto Contacts corrisponderà a una riga all'interno di una tabella di database relazionale e ogni proprietà della classe Contacts sarà mappata a una colonna nella tabella di database relazionale. Più avanti in questa esercitazione verranno esaminati i dati dei contatti contenuti nel database.

### Web Forms Scaffolding

Nella sezione precedente è stata creata la classe modello **Contacts**. È ora possibile usare l'utilità di scaffolding per Web Form per generare le pagine *List*, *Insert*, *Edit* e *Delete* necessarie quando si usano questi dati. L'utilità di scaffolding per Web Form usa Entity Framework, Bootstrap e Dynamic Data. Per impostazione predefinita, l'utilità di scaffolding per Web Form è installata come un'estensione del progetto all'interno del modello di progetto in Visual Studio 2013.

La procedura seguente consente di usare l'utilità di scaffolding per Web Form.

1.  Sulla barra dei menu di Visual Studio selezionare **Strumenti** -\> **Estensioni e aggiornamenti**.
    Verrà visualizzata la finestra di dialogo **Estensioni e aggiornamenti**.
2.  Nel riquadro sinistro della finestra di dialogo selezionare **Online** -\> **Visual Studio Gallery** -\> **Strumenti** -\> **Scaffolding**.
3.  Se la voce 'Web Forms Scaffolding' non è presente nell'elenco, digitare 'Web Forms Scaffolding' nella casella di ricerca nella parte destra della finestra di dialogo.
4.  Se l'utilità di scaffolding per Web Form non è già installata, selezionare **Download** per scaricare e installare l'estensione 'Web Forms Scaffolding'. Se necessario, riavviare Visual Studio. Assicurarsi di salvare le modifiche al progetto, quando richiesto.
    ![Extensions and Updates dialog box][]
5.  Compilare il progetto (**CTRL+MAISUC+B**).
    Per usare il meccanismo scaffolding, è innanzitutto necessario compilare il progetto.
6.  In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul *progetto* e scegliere **Aggiungi** -\> **New Scaffolded Item**.
    Verrà visualizzata la finestra di dialogo **Add Scaffold**.
7.  Selezionare **Web Form** nel riquadro sinistro e quindi **Pagine Web Form che usano Entity Framework** dal riquadro centrale. Fare quindi clic su **Add**.
    ![Add Scaffold dialog box][]
    Verrà visualizzata la finestra di dialogo **Add Web Forms Pages**.

8.  Nella finestra di dialogo **Aggiungi pagine Web Form** impostare **Classe modello** su`Contact (ContactManager.Models)`. Impostare **Classe contesto di dati** su `ApplicationDbContext (ContactManager.Models)`. Fare quindi clic su **Aggiungi**.
    ![Add Web Forms Pages dialog box][]

L'utilità di scaffolding per Web Form aggiunge una nuova cartella contenente le pagine *Default.aspx*, *Delete.aspx*, *Edit.aspx* e *Insert.aspx*. L'utilità di scaffolding per Web Form crea inoltre una cartella *DynamicData* che contiene le cartelle *EntityTemplates* e *FieldTemplates*. La classe `ApplicationDbContext` verrà usata per il database di appartenenza e per i dati relativi ai contatti.

### Configurare l'applicazione per l'utilizzo del modello di dati

L'attività successiva consiste nell'abilitare la funzionalità Migrazioni Code First, in modo da creare il database basato sul modello di dati creato. Verranno anche aggiunti dati di esempio e un inizializzatore di dati.

1.  Scegliere **Gestione pacchetti NuGet** dal menu **Strumenti**, quindi fare clic su **Console di Gestione pacchetti**.
    ![Add Web Forms Pages dialog box][4]
2.  Nella finestra Console di Gestione pacchetti immettere il comando seguente:

    ``` prettyprint
    enable-migrations
    ```

    Il comando enable-migrations consente di creare una cartella *Migrations* e di inserire in tale cartella il file *Configuration.cs*, che può essere modificato per effettuare il seeding del database e configurare le migrazioni dei dati.

3.  Nella finestra **Console di Gestione pacchetti** immettere il comando seguente:

    ``` prettyprint
    add-migration Initial
    ```

    Il comando `add-migration Initial` consente di generare un file con nome <date_stamp>Initial nella cartella *Migrations* che crea il database. Il primo parametro (Initial) è arbitrario e viene usato per creare il nome del file. È possibile visualizzare i nuovi file di classe in **Esplora soluzioni**. Nella classe `Initial` il metodo `Up` consente di creare la tabella `Contact` e il metodo `Down`, che viene usato per tornare allo stato precedente, consente di rimuoverla.

4.  Aprire il file *Migrations\\Configuration.cs*.
5.  Aggiungere lo spazio dei nomi seguente:

    ``` prettyprint
    using ContactManager.Models;
    ```

6.  Sostituire il metodo `Seed` con il codice seguente:

    ``` prettyprint
    protected override void Seed(ContactManager.Models.ApplicationDbContext context){ context.Contacts.AddOrUpdate(p => p.Name, new Contacts { ContactId = 1, Name = "Ivan Irons", Address = "One Microsoft Way", City = "Redmond", State = "WA", Zip = "10999", Email = "ivani@wideworldimporters.com", }, new Contacts { ContactId = 2, Name = "Brent Scholl", Address = "5678 1st Ave W", City = "Redmond", State = "WA", Zip = "10999", Email = "brents@wideworldimporters.com", }, new Contacts { ContactId = 3, Name = "Terrell Bettis", Address = "9012 State St", City = "Redmond", State = "WA", Zip = "10999", Email = "terrellb@wideworldimporters.com", }, new Contacts { ContactId = 4, Name = "Jo Cooper", Address = "3456 Maple St", City = "Redmond", State = "WA", Zip = "10999", Email = "joc@wideworldimporters.com", }, new Contacts { ContactId = 5, Name = "Ines Burnett", Address = "7890 2nd Ave E", City = "Redmond", State = "WA", Zip = "10999", Email = "inesb@wideworldimporters.com", } );}
    ```

    Questo codice consente di inizializzare (effettuare il seeding) il database con le informazioni sui contatti. Per altre informazioni sul seeding del database, vedere la pagina relativa al [seeding e al debug di database di Entity Framework (EF)][].

7.  In **Console di Gestione pacchetti** immettere il comando seguente:

    ``` prettyprint
    update-database
    ```

    Il comando `update-database` consente di eseguire la prima migrazione al fine di creare il database. Per impostazione predefinita, il database viene creato come database LocalDB di SQL Server Express.
    ![Package Manager Console][]

### Eseguire l'applicazione in locale e visualizzare i dati

Eseguire l'applicazione adesso per verificare se i contatti vengono visualizzati.

1.  Innanzitutto, compilare il progetto **(CTRL+MAIUSC+B)**.
2.  Premere **CTRL+F5** per eseguire l'applicazione.
    Verrà aperta una finestra del browser che mostra la pagina *Default.aspx*.
3.  Selezionare il collegamento **Contact Demo** nella parte superiore della pagina per visualizzare la pagina *Contact List*.
    ![Contacts List Page][]

## Abilitare SSL per il progetto

Secure Sockets Layer (SSL) è un protocollo definito per consentire ai server Web e ai client Web di comunicare in modo più sicuro tramite l'uso della crittografia. Quando non si usa SSL, i dati inviati tra il client e il server possono essere soggetti allo sniffing dei pacchetti da qualsiasi soggetto con accesso fisico alla rete. Inoltre, numerosi schemi di autenticazione comuni non sono sicuri sul protocollo HTTP normale. In particolare, l'autenticazione di base e l'autenticazione basata su form inviano credenziali non crittografate. Per essere sicuri, questi schemi di autenticazione devono usare il protocollo SSL.

1.  In **Esplora soluzioni** fare clic sul progetto **ContactManager**, quindi premere **F4** per visualizzare la finestra di dialogo **Proprietà**.
2.  Modificare il valore di **SSL abilitato** in `true`.
3.  Copiare il valore di **SSL URL** per poterlo riutilizzare successivamente.
    L'URL SSL sarà `https://localhost:44300/` a meno che non siano stati creati siti Web SSL in precedenza (come illustrato di seguito).
    ![Project Properties][]
4.  In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul progetto **Contact Manager**, quindi scegliere **Proprietà**.
5.  Nella scheda a sinistra fare clic su **Web**.
6.  Modificare **URL progetto** affinché venga usato l'**URL SSL** salvato in precedenza.
    ![Project Web Properties][]
7.  Per salvare la pagina, premere **CTRL+S**.
8.  Premere **CTRL+F5** per eseguire l'applicazione.
    In Visual Studio verrà visualizzata un'opzione che consente di evitare eventuali avvisi SSL.
9.  Fare clic su **Sì** per considerare attendibile il certificato SSL di IIS Express e continuare.
    ![IIS Express SSL certificate details][]
    Verrà visualizzato un avviso di sicurezza.

10. Fare clic su **Sì** per installare il certificato in localhost.
    ![Security Warning dialog box][]
    Verrà visualizzata la finestra del browser.

È possibile testare l'applicazione Web in locale tramite SSL.

## Aggiungere un provider OAuth 2.0

In Web Form ASP.NET sono disponibili opzioni avanzate per l'appartenenza e l'autenticazione, tra cui OAuth. OAuth è un protocollo aperto che consente di eseguire l'autenticazione sicura in un metodo semplice e standard da applicazioni Web, per dispositivi mobili e desktop. Il modello Internet ASP.NET MVC usa OAuth per esporre Facebook, Twitter, Google e Microsoft come provider di autenticazione. Benché in questa esercitazione si utilizzi solo Google come provider di autenticazione, è possibile modificare con facilità il codice per utilizzare un altro provider. I passaggi per l'implementazione di altri provider sono molto simili ai passaggi disponibili in questa esercitazione.

Oltre all'autenticazione, nell'esercitazione verranno utilizzati anche i ruoli per implementare l'autorizzazione. Solo gli utenti aggiunti al ruolo `canEdit` saranno in grado di modificare i dati, ovvero creare, modificare o eliminare i contatti.

La procedura seguente consente di aggiungere un provider di autenticazione Google.

1.  Aprire il file *App\_Start\\Startup.Auth.cs*.
2.  Rimuovere i caratteri di commento dal metodo `app.UseGoogleAuthentication()` in modo che il metodo presenti l'aspetto seguente:

    ``` prettyprint
     app.UseGoogleAuthentication(new GoogleOAuth2AuthenticationOptions() { ClientId = "", ClientSecret = "" });
    ```

3.  Passare a [Google Developers Console][]. Sarà necessario eseguire l'accesso con l'account di posta elettronica per sviluppatori di Google (gmail.com). Se non si dispone di un account Google, selezionare il collegamento **Crea un account**.
    Verrà quindi visualizzata la pagina **Google Developers Console**.
    ![Google Developers Console][5]

4.  Fare clic sul pulsante **Create Project** e immettere un nome e un ID per il progetto (è possibile usare i valori predefiniti). Fare quindi clic sulla casella di controllo relativa a **Terms of Service** e quindi sul pulsante **Create**.
    ![Google - New Project][]
    Entro pochi secondi verrà creato il nuovo progetto e la pagina del nuovo progetto verrà visualizzata nel browser.
5.  Nella scheda sinistra, fare clic su **APIs & auth** e quindi su **Credentials**.
6.  In **OAuth** fare clic su **Create New Client ID**.
    Verrà visualizzata la finestra di dialogo **Create Client ID**.
    ![Google - Create Client ID][]
7.  Nella finestra di dialogo **Create Client ID** per il tipo di applicazione mantenere l'impostazione predefinita **Web application**.
8.  Impostare **Authorized JavaScript Origins** sull'URL SSL usato in precedenza in questa esercitazione (**<https://localhost:44300/>** a meno che non siano stati creati altri progetti SSL).
    Questo URL rappresenta l'origine dell'applicazione. Per questo esempio, sarà necessario immettere solo l'URL di test localhost. È tuttavia possibile immettere più URL per gestire gli URL per localhost e gli URL per l'ambiente di produzione.

9.  Per **Authorized Redirect URI**, immettere le impostazioni seguenti:

    ``` prettyprint
    https://localhost:44300/signin-google
    ```

    Questo valore è l'URI che verrà usato da ASP.NET OAuth per comunicare con il server OAuth di Google. Immettere l'URL SSL usato in precedenza (**<https://localhost:44300/>** a meno che non siano stati creati altri progetti SSL).

10. Fare clic sul pulsante **Create Client ID**.
11. In Visual Studio aggiornare il metodo `UseGoogleAuthentication` della pagina *Startup.Auth.cs* copiando e incollando i valori di **ID app** e **Chiave privata app** nel metodo. I valori di **ID app** e **Chiave privata app** mostrati di seguito sono valori di esempio non funzionanti.

    ``` prettyprint
    using System;using Microsoft.AspNet.Identity;using Microsoft.AspNet.Identity.EntityFramework;using Microsoft.AspNet.Identity.Owin;using Microsoft.Owin;using Microsoft.Owin.Security.Cookies;using Microsoft.Owin.Security.DataProtection;using Microsoft.Owin.Security.Google;using Owin;using ContactManager.Models;
    namespace ContactManager{ public partial class Startup {
        // For more information on configuring authentication, please visit http://go.microsoft.com/fwlink/?LinkId=301883
        public void ConfigureAuth(IAppBuilder app)
        {
            // Configure the db context and user manager to use a single instance per request
            app.CreatePerOwinContext(ApplicationDbContext.Create);
            app.CreatePerOwinContext<ApplicationUserManager>(ApplicationUserManager.Create);

            // Enable the application to use a cookie to store information for the signed in user
            // and to use a cookie to temporarily store information about a user logging in with a third party login provider
            // Configure the sign in cookie
            app.UseCookieAuthentication(new CookieAuthenticationOptions
            {
                AuthenticationType = DefaultAuthenticationTypes.ApplicationCookie,
                LoginPath = new PathString("/Account/Login"),
                Provider = new CookieAuthenticationProvider
                {
                    OnValidateIdentity = SecurityStampValidator.OnValidateIdentity<ApplicationUserManager, ApplicationUser>(
                        validateInterval: TimeSpan.FromMinutes(20),
                        regenerateIdentity: (manager, user) => user.GenerateUserIdentityAsync(manager))
                }
            });
            // Use a cookie to temporarily store information about a user logging in with a third party login provider
            app.UseExternalSignInCookie(DefaultAuthenticationTypes.ExternalCookie);

            // Uncomment the following lines to enable logging in with third party login providers
            //app.UseMicrosoftAccountAuthentication(
            //    clientId: "",
            //    clientSecret: "");

            //app.UseTwitterAuthentication(
            //   consumerKey: "",
            //   consumerSecret: "");

            //app.UseFacebookAuthentication(
            //   appId: "",
            //   appSecret: "");

            app.UseGoogleAuthentication(new GoogleOAuth2AuthenticationOptions()
            {
                ClientId = "<mark>000000000000.apps.googleusercontent.com</mark>",
                ClientSecret = "<mark>00000000000</mark>"
            });
        }
    }
    ```

     }

    </pre>

12. Premere **CTRL+F5** per compilare ed eseguire l'applicazione. Fare clic sul collegamento **Accedi**.
13. In **Use another service to log in** fare clic sul pulsante **Google**.
    ![Log in][]
14. Se occorre immettere le credenziali, l'utente verrà reindirizzato al sito Google per l'immissione delle credenziali.
    ![Google - Sign in][]
15. Dopo avere immesso le credenziali, verrà richiesto di concedere le autorizzazioni all'applicazione Web appena creata:
    ![Project Default Service Account][]
16. Fare clic **Accetta**. L'utente verrà reindirizzato alla pagina **Register** dell'applicazione **ContactManager** in cui è possibile registrare l'account Google.
    ![Register with your Google Account][]
17. Sarà possibile modificare il nome di registrazione dell'indirizzo di posta elettronica locale usato per l'account Gmail, anche se in generale è preferibile mantenere l'alias di posta elettronica predefinito, ovvero quello usato per l'autenticazione. Fare clic su **Log in**.

## Usare l'API di appartenenza per limitare l'accesso

ASP.NET Identity è il sistema di appartenenza usato per l'autenticazione quando si crea un'applicazione Web ASP.NET. Semplifica la creazione dei dati del profilo specifici dell'utente con i dati dell'applicazione. ASP.NET Identity consente inoltre di scegliere il modello di persistenza dei profili utente nell'applicazione. È possibile archiviare i dati in un database SQL Server o in un altro archivio dati, inclusi gli archivi dati *NoSQL*, come ad esempio le tabelle di Archiviazione di Azure.

Nel modello Web Form ASP.NET predefinito è disponibile la funzionalità di appartenenza incorporata che può essere usata immediatamente durante l'esecuzione dell'applicazione. Si userà ASP.NET Identity per aggiungere un ruolo amministratore e assegnare un utente a tale ruolo. Si apprenderà quindi a limitare l'accesso alla cartella di amministrazione e alle pagine in essa contenute che vengono usate per modificare i dati di contatto.

### Aggiungere un amministratore

ASP.NET Identity consente di aggiungere un ruolo amministratore e assegnare un utente a tale ruolo tramite il codice.

1.  In **Esplora soluzioni** aprire il file *Configuration.cs* nella cartella *Migrations*.
2.  Aggiungere le istruzioni `using` seguenti nello spazio dei nomi `ContactManger.Migrations`:

    ``` prettyprint
    using Microsoft.AspNet.Identity;using Microsoft.AspNet.Identity.EntityFramework;
    ```

3.  Aggiungere il metodo `AddUserAndRole` seguente alla classe `Configuration` dopo il metodo `Seed`:

    ``` prettyprint
     public void AddUserAndRole(ContactManager.Models.ApplicationDbContext context) { IdentityResult IdRoleResult; IdentityResult IdUserResult;
        var roleStore = new RoleStore&lt;IdentityRole&gt;(context);
        var roleMgr = new RoleManager&lt;IdentityRole&gt;(roleStore);

        if (!roleMgr.RoleExists(&quot;canEdit&quot;))
        {
            IdRoleResult = roleMgr.Create(new IdentityRole { Name = &quot;canEdit&quot; });
        }

        //var userStore = new UserStore&lt;ApplicationUser&gt;(context);
        //var userMgr = new UserManager&lt;ApplicationUser&gt;(userStore);
        var userMgr = new UserManager&lt;ApplicationUser&gt;(new UserStore&lt;ApplicationUser&gt;(context));

        var appUser = new ApplicationUser
        {
            UserName = &quot;canEditUser@wideworldimporters.com&quot;,
            Email = &quot;canEditUser@wideworldimporters.com&quot;
        };
        IdUserResult = userMgr.Create(appUser, &quot;Pa$$word1&quot;);

        if (!userMgr.IsInRole(userMgr.FindByEmail(&quot;canEditUser@wideworldimporters.com&quot;).Id, &quot;canEdit&quot;))
        {
          //  IdUserResult = userMgr.AddToRole(appUser.Id, &quot;canEdit&quot;);
            IdUserResult = userMgr.AddToRole(userMgr.FindByEmail(&quot;canEditUser@wideworldimporters.com&quot;).Id, &quot;canEdit&quot;);
        }
    }
    ```

    </pre>
4.  Aggiungere una chiamata al metodo `AddUserAndRole` all'inizio del metodo `Seed`. Si noti che è visualizzato solo l'inizio del metodo `Seed`.

    ``` prettyprint
     protected override void Seed(ContactManager.Models.ApplicationDbContext context) { AddUserAndRole(context);
    ```

5.  Dopo avere salvato le modifiche, nella **Console di Gestione pacchetti** eseguire il comando seguente:

    ``` prettyprint
    Update-Database
    ```

    Questo codice consente di creare un nuovo ruolo denominato `canEdit`, quindi di creare un nuovo utente locale con l'indirizzo di posta elettronica <canEditUser@wideworldimporters.com>. Il codice aggiunge quindi l'utente <canEditUser@wideworldimporters.com> al ruolo `canEdit`. Per altre informazioni, vedere la pagina relativa alle risorse di [ASP.NET Identity][].

    </p>

### Limitare l'accesso alla cartella Administration

L'applicazione di esempio **ContactManager** consente di visualizzare i contatti sia agli utenti anonimi che agli utenti che hanno eseguito l'accesso. Dopo avere completato questa sezione, tuttavia, solo gli utenti assegnati al ruolo "canEdit" potranno modificare i contatti.

Verrà creata una cartella denominata *Admin* a cui potranno accedere solo gli utenti assegnati al ruolo "canEdit".

1.  In **Esplora soluzioni** aggiungere una sottocartella alla cartella *Contacts* e denominare la sottocartella *Admin*.
2.  Spostare i file seguenti dalla cartella *Contacts* alla cartella *Contacts/Admin*:

    -   *Delete.aspx* e \* Delete.aspx.cs\*
    -   *Edit.aspx* e \* Edit.aspx.cs\*
    -   *Insert.aspx* e \* Insert.aspx.cs\*

3.  Aggiornare i riferimenti ai collegamenti in *Contacts/Default.aspx* aggiungendo "Admin/" prima dei riferimenti di pagina che indirizzano ai file *Insert.aspx*, *Edit.aspx* e *Delete.aspx*:

    ``` prettyprint
    <%@ Page Title="ContactsList" Language="C#" MasterPageFile="~/Site.Master" CodeBehind="Default.aspx.cs" Inherits="ContactManager.Contacts.Default" ViewStateMode="Disabled" %><%@ Register TagPrefix="FriendlyUrls" Namespace="Microsoft.AspNet.FriendlyUrls" %>
    <asp:Content runat="server" ContentPlaceHolderID="MainContent"> <h2>Contacts List</h2> <p> <asp:HyperLink runat="server" NavigateUrl="Admin/Insert.aspx" Text="Create new" /> </p> <div> <asp:ListView runat="server" DataKeyNames="ContactId" ItemType="ContactManager.Models.Contacts" AutoGenerateColumns="false" AllowPaging="true" AllowSorting="true" SelectMethod="GetData"> <EmptyDataTemplate> There are no entries found for Contacts </EmptyDataTemplate> <LayoutTemplate> <table class="table"> <thead> <tr> <th>Name</th> <th>Address</th> <th>City</th> <th>State</th> <th>Zip</th> <th>Email</th> <th> </th> </tr> </thead> <tbody> <tr runat="server" id="itemPlaceholder" /> </tbody> </table> </LayoutTemplate> <ItemTemplate> <tr> <td> <asp:DynamicControl runat="server" DataField="Name" ID="Name" Mode="ReadOnly" /> </td> <td> <asp:DynamicControl runat="server" DataField="Address" ID="Address" Mode="ReadOnly" /> </td> <td> <asp:DynamicControl runat="server" DataField="City" ID="City" Mode="ReadOnly" /> </td> <td> <asp:DynamicControl runat="server" DataField="State" ID="State" Mode="ReadOnly" /> </td> <td> <asp:DynamicControl runat="server" DataField="Zip" ID="Zip" Mode="ReadOnly" /> </td> <td> <asp:DynamicControl runat="server" DataField="Email" ID="Email" Mode="ReadOnly" /> </td> <td> <a href="Admin/Edit.aspx?ContactId=<%#: Item.ContactId%>">Edit</a> | <a href="Admin/Delete.aspx?ContactId=<%#: Item.ContactId%>">Delete</a> </td> </tr> </ItemTemplate> </asp:ListView> </div></asp:Content>
    ```

4.  Aggiornare i sei riferimenti del codice `Response.Redirect("Default.aspx")` a `Response.Redirect("~/Contacts/Default.aspx")` per i tre file seguenti:

    -   *Delete.aspx.cs*
    -   *Edit.aspx.cs*
    -   *Insert.aspx.cs*

    Ora i collegamenti funzioneranno correttamente durante la visualizzazione e l'aggiornamento dei dati di contatto.

5.  Per limitare l'accesso alla cartella *Admin*, in **Esplora soluzioni** fare clic con il pulsante destro del mouse sulla cartella *Admin* e scegliere **Aggiungi nuovo elemento**.
6.  Nell'elenco di modelli Web per Visual C# selezionare **File di configurazione Web** nell'elenco centrale, accettare il nome predefinito *Web.config* e quindi selezionare **Aggiungi**.
7.  Sostituire il contenuto XML esistente nel file *Web.config* con il contenuto seguente:

    ``` prettyprint
    <?xml version="1.0"?><configuration> <system.web> <authorization> <allow roles="canEdit"/> <deny users="*"/> </authorization> </system.web></configuration>
    ```

8.  Salvare il file *Web.config*.
    Il file *Web.config* specifica che solo gli utenti assegnati al ruolo"canEdit" possono accedere alle pagine contenute nella cartella *Admin*.

Quando un utente che non fa parte del ruolo "canEdit" tenta di modificare i dati, viene reindirizzato alla pagina *Log in*.

## Distribuire l'applicazione con il database in Azure

Ora che l'applicazione Web è stata completata, è possibile pubblicarla in Azure.

### Pubblicare l'applicazione

1.  In Visual Studio compilare il progetto (**CTRL+MAIUSC+B**).
2.  Fare clic con il pulsante destro del mouse in **Esplora soluzioni** e scegliere **Pubblica**.
    ![Publish menu option][]
    Verrà visualizzata la finestra di dialogo **Pubblica sito Web**.
    ![Publish Web dialog box][6]
3.  Nella scheda **Profilo** selezionare **Siti Web di Azure** come destinazione di pubblicazione, se questa non è già selezionata.
    ![Publish Web dialog box][7]
4.  Se l'accesso non è ancora stato eseguito, fare clic su **Accedi**.
5.  Selezionare il sito Web esistente creato in precedenza in questa esercitazione nella casella di riepilogo a discesa **Siti Web esistenti** e fare clic sul pulsante **OK**.
    ![Select Existing Web Site dialog box][8]
    Se viene richiesto di salvare le modifiche apportate al profilo, scegliere **Sì**.
6.  Fare clic sulla scheda **Impostazioni**.
    ![Select Existing Web Site dialog box][9]
7.  Impostare la casella di riepilogo a discesa **Configurazione** su **Debug**.
8.  Fare clic sull'icona con la **freccia verso il basso** accanto alla voce **ApplicationDbContext** e impostarla su **ContactDB**.
9.  Selezionare la casella di controllo **Esegui migrazioni Code First**.

    > [WACOM.NOTE]
    > In questo esempio selezionare questa casella di controllo solo se l'applicazione viene pubblicata per la prima volta. In questo modo, il metodo *Seed* nel file *Configuration.cs* verrà chiamato una volta sola.

10. Fare quindi clic su **Pubblica**.
    L'applicazione verrà pubblicata in Azure.

> [WACOM.NOTE]
> Se Visual Studio è stato chiuso e riaperto dopo la creazione del profilo di pubblicazione, è possibile che la stringa di connessione non sia visualizzata nell'elenco a discesa. In tale caso, invece di modificare il profilo di pubblicazione creato in precedenza, crearne uno in base alla stessa procedura eseguita in precedenza, quindi attenersi alle istruzioni visualizzate nella scheda **Settings**.

### Verificare l'applicazione in Azure

1.  Nel browser fare clic sul collegamento **Contact Demo**.
    Verrà visualizzato l'elenco contatti.
    ![Contacts listed in Browser][]

2.  Selezionare **Create new** nella pagina **Contacts List**.
    ![Contacts listed in Browser][10]
    L'utente verrà reindirizzato alla pagina **Login** perché l'accesso non è stato eseguito con un account che può modificare i contatti.
3.  Dopo avere immesso l'indirizzo di posta elettronica e la password indicati di seguito, fare clic sul pulsante **Log in**.
    **Indirizzo di posta elettronica**: `canEditUser@wideworldimporters.com`
    **Password**: `Pa$$word1`
    ![Pagina di accesso][]

4.  Immettere i nuovi dati per ogni campo e quindi fare clic su **Insert**.
    ![Add New Contact Page][]
    Verrà visualizzata la pagina *EditContactList.aspx* con il nuovo record.
    ![Add New Contact Page][11]

5.  Selezionare il collegamento **Log off**.

### Arrestare l'applicazione

Per evitare che altre persone registrino e usino questa applicazione di esempio, il sito Web verrà arrestato.

1.  Nel menu **Visualizza** di Visual Studio scegliere **Esplora server**.
2.  In **Esplora server** passare a **Siti Web**.
3.  Fare clic con il pulsante destro del mouse su ogni istanza del sito Web, quindi selezionare **Arresta sito Web**.
    ![Stop Web Site menu item][]

    In alternativa, dal portale di gestione di Microsoft Azure è possibile selezionare il sito Web, quindi fare clic sull'icona di **arresto** nella parte inferiore della pagina.
    ![Add New Contact Page][12]

## Verificare il database

È importante conoscere le procedure per visualizzare e modificare il database direttamente, in quanto in questo modo è possibile confermare i dati nel database e acquisire informazioni sul modo in cui i dati vengono archiviati in ogni tabella.

### Esaminare il database SQL di Azure

1.  In Visual Studio aprire **Esplora server** e passare a **ContactDB**.
2.  Fare clic con il pulsante destro del mouse su **ContactDB**, quindi scegliere **Apri in Esplora oggetti di SQL Server**.
    ![Open in SQL Server Object Explorer menu item][]
3.  Nella finestra di dialogo **Aggiungi regola firewall** selezionare **Aggiungi regola firewall**.

    > [WACOM.NOTE]
    > Se non è possibile espandere **Database SQL** e non è possibile visualizzare **ContactDB** da Visual Studio, è possibile seguire le istruzioni per aprire una porta del firewall o un intervallo di porte. A questo scopo, seguire le istruzioni fornite in **Configurazione delle regole del firewall per Azure** verso la fine dell'[esercitazione per MVC][Distribuzione di un'app ASP.NET MVC 5 sicura con appartenenza, OAuth e database SQL in un sito Web di Azure]. In alternativa, per verificare i dati del database locale è anche possibile compilare, eseguire e aggiungere i dati all'applicazione in locale (**CTRL+F5** da Visual Studio).

4.  Se viene visualizzata la finestra di dialogo **Connetti al server**, immettere la **password** creata all'inizio di questa esercitazione e fare clic sul pulsante **Connetti**.

    > [WACOM.NOTE]
    > Se non si ricorda la password, aprire il file del progetto locale per recuperarla. In **Esplocar soluzioni** espandere la cartella *Properties* e quindi la cartella *PublishProfiles*. Aprire il file *contactmanager.pubxml* (il nome del file dell'utente potrebbe essere diverso). Eseguire una ricerca all'interno del file per trovare la password di pubblicazione.

5.  Espandere il database **contactDB**, quindi espandere **Tabelle**.
6.  Fare clic con il pulsante destro del mouse sulla tabella **dbo.AspNetUsers** e selezionare **Visualizza dati**.
    ![View Data menu item][]
    Sarà possibile visualizzare i dati associati all'utente <canEditUser@contoso.com>.
    ![ContactManager window][]

### Aggiungere un utente al ruolo Admin modificando il database

Nei passaggi precedenti dell'esercitazione, per aggiungere utenti al ruolo canEdit è stato utilizzato codice. Un metodo alternativo consiste nel modificare i dati direttamente nelle tabelle di appartenenza. Nella procedura seguente viene illustrato come utilizzare questo metodo alternativo per aggiungere un utente a un ruolo.

1.  In **Esplora oggetti di SQL Server** fare clic con il pulsante destro del mouse su **dbo.AspNetUserRoles** e scegliere **Visualizza dati**.
    ![AspNetUserRoles data][]
2.  Copiare il valore di *RoleId* e incollarlo nella riga vuota (nuova).
    ![AspNetUserRoles data][13]
3.  Nella tabella **dbo.AspNetUsers** individuare l'utente da inserire nel ruolo, copiare l'*Id* dell'utente,
4.  quindi incollarlo nel campo **UserId** della nuova riga nella tabella **AspNetUserRoles**.

> [WACOM.NOTE]
> Microsoft sta attualmente lavorando alla creazione di uno strumento per semplificare la gestione di utenti e ruoli.

## Passaggi successivi

Per altre informazioni su Web Form ASP.NET, vedere l'articolo relativo alle [informazioni si Web Form ASP.NET][] nel sito Web dedicato ad ASP.NET e gli argomenti relativi alle [guide ed esercitazioni per Microsoft Azure][].

Questa esercitazione è basata sull'esercitazione MVC [Distribuzione di un'app ASP.NET MVC 5 sicura con appartenenza, OAuth e database SQL in un sito Web di Azure][] scritta da Rick Anderson (Twitter [@RickAndMSFT][]) con il supporto di Tom Dykstra e Barry Dorrans (Twitter [@blowdart][]).

Se lo si desidera, inviare commenti e suggerimenti sugli aspetti ritenuti utili e su eventuali miglioramenti da apportare, non solo in merito all'esercitazione ma anche ai prodotti illustrati nell'esercitazione. I commenti e suggerimenti degli utenti risulteranno utili per definire la priorità dei miglioramenti da apportare. È inoltre possibile richiedere nuovi argomenti e votare gli argomenti esistenti alla pagina [Show Me How With Code][].

  [Distribuzione di un'app ASP.NET MVC 5 sicura con appartenenza, OAuth e database SQL in un sito Web di Azure]: http://azure.microsoft.com/it-it/documentation/articles/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/
  [Pagina Contatti - Modifica]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms00.png
  [attivare i benefici della sottoscrizione MSDN]: /en-us/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F
  [iscriversi per ottenere una versione di valutazione gratuita]: /en-us/pricing/free-trial/?WT.mc_id=A261C142F
  [Configurare l'ambiente di sviluppo]: #set-up-the-development-environment
  [Configurare l'ambiente Azure]: #Set-up-the-Azure-environment
  [Creare un'applicazione Web Form ASP.NET]: #Create-an-ASP.NET-Web-Forms-Application
  [Aggiungere un database all'applicazione]: #Add-a-Database-to-the-Application
  [Abilitare SSL per il progetto]: #Enable-SSL-for-the-Project
  [Aggiungere un provider OAuth 2.0]: #Add-an-OAuth-2.0-Provider
  [Usare l'API di appartenenza per limitare l'accesso]: #Use-the-Membership-API-to-Restrict-Access
  [Distribuire l'applicazione con il database in Azure]: #Deploy-the-Application-with-the-Database-to-Azure
  [Verificare il database]: #Review-the-Database
  [Passaggi successivi]: #Next-Steps
  [Visual Studio 2013]: http://go.microsoft.com/fwlink/?LinkId=306566
  [Azure SDK per Visual Studio 2013]: http://go.microsoft.com/fwlink/?linkid=324322&clcid=0x409
  [Web Platform Installer]: ./media/web-sites-dotnet-web-forms-secure/Intro-SecureWebForms-01.png
  [Visual Studio 2013 Update 2]: http://www.microsoft.com/download/details.aspx?id=42666
  [portale di gestione di Azure]: https://manage.windowsazure.com/
  [1]: ./media/web-sites-dotnet-web-forms-secure/Intro-SecureWebForms-02.png
  [Custom Create]: ./media/web-sites-dotnet-web-forms-secure/Intro-SecureWebForms-03.png
  [Contacts - Create New Web Site]: ./media/web-sites-dotnet-web-forms-secure/Intro-SecureWebForms-04.png
  [Database Settings]: ./media/web-sites-dotnet-web-forms-secure/Intro-SecureWebForms-05.png
  [File Menu - New Project]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms01.png
  [New Project Dialog]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms02.png
  [New ASP.NET Project dialog box]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms03.png
  [2]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms04.png
  [Select Publish]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms05.png
  [Publish Web dialog box]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms06.png
  [Select Existing Web Site dialog box]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms07.png
  [3]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms08.png
  [Web Publishing Activity]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms09.png
  [App nel browser]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms10.png
  [Finestra di dialogo Pubblica sito Web]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms11.png
  [Select Class]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms12.png
  [Add New Item dialog box]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms13.png
  [Extensions and Updates dialog box]: ./media/web-sites-dotnet-web-forms-secure/ExtensionsAndUpdatesDB.png
  [Add Scaffold dialog box]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms13a.png
  [Add Web Forms Pages dialog box]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms13b.png
  [4]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms13c.png
  [seeding e al debug di database di Entity Framework (EF)]: http://blogs.msdn.com/b/rickandy/archive/2013/02/12/seeding-and-debugging-entity-framework-ef-dbs.aspx
  [Package Manager Console]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms13d.png
  [Contacts List Page]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms17.png
  [Project Properties]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms18.png
  [Project Web Properties]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms19.png
  [IIS Express SSL certificate details]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms20.png
  [Security Warning dialog box]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms21.png
  [Google Developers Console]: https://console.developers.google.com/
  [5]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms21a.png
  [Google - New Project]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms21b.png
  [Google - Create Client ID]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms21c.png
  [Log in]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms21d.png
  [Google - Sign in]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms21e.png
  [Project Default Service Account]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms21f.png
  [Register with your Google Account]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms21g.png
  [ASP.NET Identity]: http://www.asp.net/identity
  [Publish menu option]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms22.png
  [6]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms22a.png
  [7]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms23.png
  [8]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms25.png
  [9]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms26.png
  [Contacts listed in Browser]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms27.png
  [10]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms29.png
  [Pagina di accesso]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms28.png
  [Add New Contact Page]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms30.png
  [11]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms31.png
  [Stop Web Site menu item]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms26a.png
  [12]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms26b.png
  [Open in SQL Server Object Explorer menu item]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms32.png
  [View Data menu item]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms34.png
  [ContactManager window]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms35.png
  [AspNetUserRoles data]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms36.png
  [13]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms37.png
  [informazioni si Web Form ASP.NET]: http://www.asp.net/web-forms
  [guide ed esercitazioni per Microsoft Azure]: http://azure.microsoft.com/it-it/documentation/services/web-sites/#net
  [@RickAndMSFT]: https://twitter.com/RickAndMSFT
  [@blowdart]: https://twitter.com/blowdart
  [Show Me How With Code]: http://aspnet.uservoice.com/forums/228522-show-me-how-with-code
