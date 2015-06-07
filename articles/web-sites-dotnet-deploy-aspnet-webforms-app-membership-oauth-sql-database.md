<properties 
	pageTitle="Creare e distribuire un'app Web Form ASP.NET sicura con appartenenza, OAuth e database SQL in Azure App Service" 
	description="Questa esercitazione illustra come compilare un'app Web Web Form ASP.NET 4.5 che incorpora un database SQL e distribuirla in Azure." 
	services="web-sites" 
	documentationCenter=".net" 
	authors="Erikre" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="02/09/2015" 
	ms.author="erikre"/>


# Creare e distribuire un'app Web Form ASP.NET sicura con appartenenza, OAuth e database SQL in Azure App Service

##Panoramica
Questa esercitazione illustra come compilare un'app Web Web Form ASP.NET 4.5 che incorpora un database SQL e distribuirla in Azure.

>[AZURE.NOTE]Per una versione MVC di questa esercitazione, vedere [Creazione di un'app ASP.NET MVC con autenticazione e database SQL e alla distribuzione in Azure App Service](web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database.md).

È possibile aprire gratuitamente un account Azure e, se non si dispone già di Visual Studio 2013, con l'SDK verrà installato automaticamente Visual Studio Express 2013 per il Web. È possibile iniziare a sviluppare per Azure gratuitamente.

In questa esercitazione si presuppone che l'utente non abbia mai usato Microsoft Azure. Al termine dell'esercitazione, si disporrà di un'applicazione Web sicura, basata sui dati, in esecuzione nel cloud e in grado di usare un database cloud.

Si apprenderà come:

- Creare un progetto Web Form ASP.NET 4.5 sicuro e pubblicarlo in un sito Web di Azure.
- Usare OAuth e il database di appartenenza ASP.NET per proteggere l'applicazione.
- Usare un singolo database sia per l'appartenenza che per i dati dell'applicazione.
- Usare l'estensione Web Forms Scaffolding per creare pagine Web che permettono di modificare i dati.
- Utilizzare la nuova API di appartenenza per aggiungere utenti e ruoli.
- Utilizzare un database SQL per l'archiviazione di dati in Azure.

Verrà creata una semplice applicazione Web di elenco contatti basata su Web Form ASP.NET 4.5 che usa Entity Framework per l'accesso al database. L'immagine seguente mostra la pagina Web Form che consente l'accesso in lettura e scrittura al database:

![Pagina Contatti - Modifica](./media/web-sites-dotnet-web-forms-secure/SecureWebForms00.png)

>[AZURE.NOTE]Per completare l'esercitazione, è necessario un account Azure. Se non si dispone di un account, è possibile <a href="/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F" target="_blank">attivare i benefici della sottoscrizione MSDN</a> oppure <a href="/pricing/free-trial/?WT.mc_id=A261C142F" target="_blank">iscriversi per ottenere una versione di valutazione gratuita</a>. Per iniziare a usare Siti Web di Azure prima di iscriversi per ottenere un account, visitare la pagina all'indirizzo <a href="https://trywebsites.azurewebsites.net/">https://trywebsites.azurewebsites.net</a> in cui è possibile creare immediatamente e gratuitamente un sito di base ASP.NET temporaneo in Siti Web di Azure. Non è necessario fornire una carta di credito né impegnarsi in alcun modo.

##Configurare l'ambiente di sviluppo 
Per iniziare, installare Visual Studio 2013 e Azure SDK per .NET per configurare l'ambiente di sviluppo.

1. Installare [Visual Studio 2013](http://go.microsoft.com/fwlink/?LinkId=306566) se non è già stato installato.  
2. Installare [Azure SDK per Visual Studio 2013](http://go.microsoft.com/fwlink/?linkid=324322&clcid=0x409). Per questa esercitazione è necessario installare Visual Studio 2013 prima di installare Azure SDK per Visual Studio 2013.  

	>[AZURE.NOTE]in base al numero di dipendenze da SDK già presenti nel computer, l'installazione dell'SDK può richiedere tempi lunghi, da alcuni minuti ad almeno mezz'ora.

3. Quando viene richiesto di eseguire o installare il file eseguibile di installazione, fare clic su **Esegui**.
4. Nell'**Installazione guidata piattaforma** Web fare clic su  **Installa** e procedere con l'installazione. ![Installazione guidata piattaforma Web](./media/web-sites-dotnet-web-forms-secure/Intro-SecureWebForms-01.png)  

	>[AZURE.NOTE]Se l'SDK è già installato, gli elementi da installare saranno pari a 0. Il numero di elementi da installare è indicato nella parte inferiore sinistra della finestra dell'**Installazione guidata piattaforma Web**.

5. Se **Visual Studio Update 2** non è installato, scaricare e installare **[Visual Studio 2013 Update 2](http://www.microsoft.com/download/details.aspx?id=42666)** o versione successiva.

	>[AZURE.NOTE]È necessario installare Visual Studio 2013 Update 2 o versione successiva per usare Goggle OAuth 2.0 ed SSL localmente senza avvisi. L'Update 2 è necessario anche per usare l'estensione Web Forms Scaffolding.

Al termine dell'installazione, saranno disponibili tutti gli strumenti necessari per avviare lo sviluppo.

##Configurare l'ambiente Azure
In questa sezione verrà configurato l'ambiente Azure tramite la creazione di un sito Web di Azure e un database SQL in Azure.

###Creare un sito Web e un database SQL in Azure 
In questa esercitazione il sito Web di Azure verrà eseguito in un ambiente di hosting condiviso, ovvero in macchine virtuali condivise con altri siti Web di Azure. Un ambiente di hosting condiviso è un punto di partenza economicamente conveniente per iniziare a utilizzare il cloud. In seguito, in caso di incremento del traffico Web, è possibile scalare l'applicazione in modo da soddisfare le nuove esigenze tramite l'esecuzione su macchine virtuali dedicate. Se è necessaria un'architettura più complessa, è possibile eseguire la migrazione a un servizio cloud Azure. I servizi cloud vengono eseguiti su macchine virtuali dedicate, che possono essere configurate in base alle proprie esigenze specifiche.

Database SQL di Azure è un servizio di database relazionale basato sul cloud che si avvale delle tecnologie SQL Server. Gli strumenti e le applicazioni utilizzabili con SQL Server sono utilizzabili anche con database SQL.

1. Nel [portale di gestione di Azure](https://manage.windowsazure.com/) fare clic su **Web Apps** nella scheda di sinistra, quindi su **New**. ![Installazione guidata piattaforma Web](./media/web-sites-dotnet-web-forms-secure/Intro-SecureWebForms-02.png)
2. Fare clic su **Web Site**, quindi su **Custom Create**. ![Custom Create](./media/web-sites-dotnet-web-forms-secure/Intro-SecureWebForms-03.png) Verrà aperta la procedura guidata **New Web App - Custom Create**.  

3. Nel passaggio **Create Web Site** della procedura guidata immettere nella casella **URL** una stringa da utilizzare come URL univoco per l'applicazione. L'URL completo sarà costituito da quanto immesso in questa casella e dal suffisso visualizzato accanto alla casella di testo. L'illustrazione mostra un URL che è probabilmente già occupato, quindi **sarà necessario scegliere un URL diverso**. ![Contatti - Crea nuovo sito Web](./media/web-sites-dotnet-web-forms-secure/Intro-SecureWebForms-04.png)
4. Nell'elenco a discesa Piano di hosting Web scegliere l'area geografica più vicina alla propria località. Questa impostazione consente di specificare il data center in cui verrà eseguita la macchina virtuale.
5. Nell'elenco a discesa **Database** scegliere **Create a free 20 MB SQL database**.
6. Nella casella **DB Connection String Name** non modificare il valore predefinito *DefaultConnection*.
7. Fare clic sulla freccia nella parte inferiore della finestra di dialogo. Verrà visualizzato il passaggio **Specify database settings** della procedura guidata.
8. Nel **nome** immettere *`ContactDB`*. ![Impostazioni database](./media/web-sites-dotnet-web-forms-secure/Intro-SecureWebForms-05.png)  
9. Nella casella **Server** selezionare **New SQL Database server**. In alternativa, se è stato creato in precedenza un database SQL Server, è possibile selezionare tale database SQL Server dall'elenco a discesa.
10. Impostare l'opzione **Region** sulla stessa area specificata per la creazione del sito Web.
11. Immettere un **Login Name** e una **Password** per l'amministratore. Se è stata selezionata l'opzione **New SQL Database server**, in questo passaggio non sarà necessario immettere un nome e una password esistenti, ma un nuovo nome e una nuova password, definiti ora e da utilizzare in seguito per l'accesso al database. Se è stato selezionato un database SQL Server creato in precedenza, verrà richiesta la password del nome account SQL Server creato in precedenza. Per questa esercitazione non verrà selezionata la casella **Avanzate**.
12. Fare clic sul segno di spunta nella parte inferiore destra della finestra di dialogo per indicare che le operazioni sono state completate.

Il **portale di gestione di Azure** torna alla pagina **Siti Web** e nella colonna **Stato** viene indicato che la creazione del sito è in corso. In genere, dopo meno di un minuto nella colonna **Stato** viene indicato che il sito è stato creato correttamente. Il numero di siti disponibili nell'account viene visualizzato nella barra di spostamento a sinistra accanto all'icona **Web App** e il numero di database viene visualizzato accanto all'icona **SQL Databases**.
##Creare un'applicazione Web Form ASP.NET 
È stato creato un'applicazione web, ma non è ancora alcun contenuto al suo interno. Il passaggio successivo consiste nel creare l'app Web di Visual Studio che verrà pubblicata in Azure.
###Creare il progetto 
1. Scegliere **Nuovo progetto** dal menu **File** di Visual Studio. ![Dal Menu file - nuovo progetto](./media/web-sites-dotnet-web-forms-secure/SecureWebForms01.png)  
2. Selezionare il gruppo di modelli a sinistra in **Modelli** -> **Visual C#** -> **Web**. 
3. Scegliere il modello **Applicazione Web ASP.NET** nella colonna centrale.
4. Assegnare all'applicazione il nome *ContactManager* e fare clic su **OK**. ![Finestra di dialogo Nuovo progetto](./media/web-sites-dotnet-web-forms-secure/SecureWebForms02.png)  

	>[AZURE.NOTE]Il nome del progetto in questa serie di esercitazioni è **ContactManager**. È consigliabile usare questo nome di progetto esatto in modo che il codice fornito nel corso di tutta la serie di esercitazioni funzioni come previsto.

5. Nella finestra di dialogo **Nuovo progetto ASP.NET** selezionare il modello **Web Form**. Deselezionare la casella di controllo **Ospita nel cloud** se è selezionata e fare clic su **OK**. ![Finestra di dialogo Nuovo progetto ASP.NET](./media/web-sites-dotnet-web-forms-secure/SecureWebForms03.png) Verrà creata l'applicazione Web Form.
###Aggiornare la pagina master
In Web Form ASP.NET le pagine master consentono di creare un layout uniforme per le pagine dell'applicazione. Una singola pagina master definisce l'aspetto e il comportamento standard da applicare a tutte le pagine (o a un gruppo di pagine) dell'applicazione. È quindi possibile creare singole pagine di contenuto per il contenuto da visualizzare. Quando gli utenti richiedono le pagine di contenuto, ASP.NET le unisce alla pagina master per generare l'output che combina il layout della pagina master con il contenuto della pagina di contenuto. Il nuovo sito necessita del nome dell'applicazione e di un collegamento aggiornato. Il collegamento indirizza a una pagina che visualizza i dettagli del contatto. Per apportare queste modifiche, sarà necessario modificare il codice HTML nella pagina master.

1. In **Esplora soluzioni** trovare e aprire la pagina *Site.Master*.
2. Se la pagina è in visualizzazione **Progettazione**, passare alla visualizzazione **Origine dati**.
3. Per aggiornare la pagina master, modificare o aggiungere i commenti visualizzati in giallo:

<pre class="prettyprint">
&lt;%@ Master Language="C#" AutoEventWireup="true" CodeBehind="Site.master.cs" Inherits="ContactManager.SiteMaster" %>

&lt;!DOCTYPE html>

&lt;html lang="en">
&lt;head runat="server">
    &lt;meta charset="utf-8" />
    &lt;meta name="viewport" content="width=device-width, initial-scale=1.0" />
    Page.Title %> - <mark>Contact Manager</mark>&lt;/title>

    &lt;asp:PlaceHolder runat="server">
        @Scripts.Render("~/bundles/modernizr")
    &lt;/asp:PlaceHolder>
    &lt;webopt:bundlereference runat="server" path="~/Content/css" />
    &lt;link href="~/favicon.ico" rel="shortcut icon" type="image/x-icon" />

&lt;/head>
&lt;body>
    &lt;form runat="server">
        &lt;asp:ScriptManager runat="server">
            &lt;Scripts>
                &lt;%--To learn more about bundling scripts in ScriptManager see http://go.microsoft.com/fwlink/?LinkID=301884 --%>
                &lt;%--Framework Scripts--%>
                &lt;asp:ScriptReference Name="MsAjaxBundle" />
                &lt;asp:ScriptReference Name="jquery" />
                &lt;asp:ScriptReference Name="bootstrap" />
                &lt;asp:ScriptReference Name="respond" />
                &lt;asp:ScriptReference Name="WebForms.js" Assembly="System.Web" Path="~/Scripts/WebForms/WebForms.js" />
                &lt;asp:ScriptReference Name="WebUIValidation.js" Assembly="System.Web" Path="~/Scripts/WebForms/WebUIValidation.js" />
                &lt;asp:ScriptReference Name="MenuStandards.js" Assembly="System.Web" Path="~/Scripts/WebForms/MenuStandards.js" />
                &lt;asp:ScriptReference Name="GridView.js" Assembly="System.Web" Path="~/Scripts/WebForms/GridView.js" />
                &lt;asp:ScriptReference Name="DetailsView.js" Assembly="System.Web" Path="~/Scripts/WebForms/DetailsView.js" />
                &lt;asp:ScriptReference Name="TreeView.js" Assembly="System.Web" Path="~/Scripts/WebForms/TreeView.js" />
                &lt;asp:ScriptReference Name="WebParts.js" Assembly="System.Web" Path="~/Scripts/WebForms/WebParts.js" />
                &lt;asp:ScriptReference Name="Focus.js" Assembly="System.Web" Path="~/Scripts/WebForms/Focus.js" />
                &lt;asp:ScriptReference Name="WebFormsBundle" />
                &lt;%--Site Scripts--%>
            &lt;/Scripts>
        &lt;/asp:ScriptManager>

        &lt;div class="navbar navbar-inverse navbar-fixed-top">
            &lt;div class="container">
                &lt;div class="navbar-header">
                    &lt;button type="button" class="navbar-toggle" data-toggle="collapse" data-target=".navbar-collapse">
                        &lt;span class="icon-bar">&lt;/span>
                        &lt;span class="icon-bar">&lt;/span>
                        &lt;span class="icon-bar">&lt;/span>
                    &lt;/button>
                    &lt;a class="navbar-brand" runat="server" <mark>id="ContactDemoLink"</mark> href="~/<mark>Contacts/Default.aspx</mark>"><mark>Contact Demo</mark>&lt;/a>
                &lt;/div>
                &lt;div class="navbar-collapse collapse">
                    &lt;ul class="nav navbar-nav">
                        &lt;li>&lt;a runat="server" href="~/">Home&lt;/a>&lt;/li>
                        &lt;li>&lt;a runat="server" href="~/About">About&lt;/a>&lt;/li>
                        &lt;li>&lt;a runat="server" href="~/Contact">Contact&lt;/a>&lt;/li>
                    &lt;/ul>
                    &lt;asp:LoginView runat="server" ViewStateMode="Disabled">
                        &lt;AnonymousTemplate>
                            &lt;ul class="nav navbar-nav navbar-right">
                                &lt;li>&lt;a runat="server" href="~/Account/Register">Register&lt;/a>&lt;/li>
                                &lt;li>&lt;a runat="server" href="~/Account/Login">Log in&lt;/a>&lt;/li>
                            &lt;/ul>
                        &lt;/AnonymousTemplate>
                        &lt;LoggedInTemplate>
                            &lt;ul class="nav navbar-nav navbar-right">
                                &lt; li >&lt; runat = "server" href = "~/Account/Manage" title = "Gestisci account" > salve, &lt; %: % Context.User.Identity.GetUserName() >! &lt;/a >&lt; / li >
                                &lt;li>
                                    &lt;asp:LoginStatus runat="server" LogoutAction="Redirect" LogoutText="Log off" LogoutPageUrl="~/" OnLoggingOut="Unnamed_LoggingOut" />
                                &lt;/li>
                            &lt;/ul>
                        &lt;/LoggedInTemplate>
                    &lt;/asp:LoginView>
                &lt;/div>
            &lt;/div>
        &lt;/div>
        &lt;div class="container body-content">
            &lt;asp:ContentPlaceHolder ID="MainContent" runat="server">
            &lt;/asp:ContentPlaceHolder>
            &lt;hr />
            &lt;footer>
                &lt;p>&amp;copy; &lt;%: DateTime.Now.Year %> - <mark>Contact Manager</mark>&lt;/p>
            &lt;/footer>
        &lt;/div>
    &lt;/form>
&lt;/body>
&lt;/html>
</pre>

Più avanti in questa esercitazione verrà aggiunta l'estensione Web Forms Scaffolding. Lo scaffolding creerà la pagina a cui fa riferimento il collegamento "Contact Demo" indicato sopra.
###Eseguire l'applicazione in locale 
1. Fare clic con il pulsante destro del mouse sulla pagina **Default.aspx** in *Esplora soluzioni*, quindi scegliere **Imposta come pagina iniziale**. 
2. Premere quindi **CTRL+F5** per eseguire l'applicazione. La pagina predefinita dell'applicazione verrà visualizzata nella finestra del browser predefinito. ![Contatti - Crea nuovo sito Web](./media/web-sites-dotnet-web-forms-secure/SecureWebForms04.png)  

Non è necessario eseguire per il momento altre operazioni per creare l'applicazione che verrà distribuita in Azure. Successivamente, verranno aggiunte la funzionalità di database e le pagine necessarie per visualizzare e modificare i dati di contatto.
###Distribuire l'applicazione in Azure
Dopo avere creato ed eseguito l'applicazione in locale, è possibile distribuirla in Azure.

1. In Visual Studio fare clic con il pulsante destro del mouse sul progetto in **Esplora soluzioni** e scegliere **Pubblica** dal menu di scelta rapida. ![Select publish](./media/web-sites-dotnet-web-forms-secure/SecureWebForms05.png) La finestra di dialogo **Pubblica sito Web** viene visualizzata.  

2. Nella scheda **Profilo** della finestra di dialogo **Pubblica sito Web** fare clic su **App Web di Azure**. ![Finestra di dialogo Pubblica sito Web](./media/web-sites-dotnet-web-forms-secure/SecureWebForms06.png)
3. Se non è stato effettuato l'accesso, fare clic sul pulsante **Accedi** nella finestra di dialogo **Selezione sito Web esistente**. Una volta effettuato l'accesso, selezionare il sito Web creato nella prima parte di questa esercitazione. Fare clic su **OK** per continuare. ![Selezionare la finestra di dialogo sito Web esistente](./media/web-sites-dotnet-web-forms-secure/SecureWebForms07.png) Visual Studio scaricherà le impostazioni di pubblicazione.
4. Nella finestra di dialogo **Pubblica sito Web** fare clic su **Pubblica**. ![Finestra di dialogo Pubblica sito Web](./media/web-sites-dotnet-web-forms-secure/SecureWebForms08.png) Si vedrà lo stato di pubblicazione generale nella finestra **Attività di pubblicazione sito Web** di Visual Studio: ![Attività di pubblicazione sul Web](./media/web-sites-dotnet-web-forms-secure/SecureWebForms09.png)  

L'applicazione creata verrà ora eseguita nel cloud. Alla successiva distribuzione dell'applicazione da Visual Studio, verranno distribuiti solo i file modificati o nuovi.![App nel browser](./media/web-sites-dotnet-web-forms-secure/SecureWebForms10.png)

>[AZURE.NOTE]Se si verifica un errore durante la pubblicazione in un sito Web già esistente, è possibile cancellare il percorso prima di aggiungere i nuovi file. Pubblicare di nuovo l'applicazione, ricordando di selezionare la scheda **Impostazioni** nella finestra di dialogo **Pubblica App Web**. Impostare quindi la configurazione su **Debug** e selezionare l'opzione **Rimuovi i file aggiuntivi nella destinazione**. Selezionare **Pubblica** per distribuire di nuovo l'applicazione. ![Finestra di dialogo Pubblica sito Web](./media/web-sites-dotnet-web-forms-secure/SecureWebForms11.png)

##Aggiungere un database all'applicazione 
Si passerà quindi all'aggiornamento dell'applicazione Web Form, in modo da aggiungere la funzionalità per la visualizzazione e l'aggiornamento dei contatti e per l'archiviazione dei dati in un database. Durante la procedura di creazione del progetto Web Form, per impostazione predefinita è stato creato anche un database. L'applicazione userà Entity Framework per accedere al database e per leggere e aggiornare i dati nel database.
###Aggiungere una classe modello di dati 
Creare innanzitutto un semplice modello di dati usando il codice. Questo modello di dati sarà contenuto in una classe denominata `Contacts`. Il nome della classe `Contacts` è stato scelto per evitare un conflitto di nomi di classi con la classe `Contact` contenuta nel file Contact.aspx.cs creata dal modello Web Form.

1. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sulla cartella *Modelli* e quindi scegliere **Aggiungi** -> **Classe**. ![Seleziona classe](./media/web-sites-dotnet-web-forms-secure/SecureWebForms12.png) La finestra di dialogo **Aggiungi nuovo elemento** viene visualizzata.  

2. Assegnare alla nuova classe il nome *Contacts.cs*. ![Finestra di dialogo Aggiungi nuovo elemento](./media/web-sites-dotnet-web-forms-secure/SecureWebForms13.png)
3. Sostituire il codice predefinito con il codice seguente: 
	<pre class="prettyprint">
	using System.ComponentModel.DataAnnotations;
	using System.Globalization;
	
	namespace ContactManager.Models
	{
	    public class Contacts
	    {
	        [ScaffoldColumn(false)]
	        [Key]
	        public int ContactId { get; set; }
	        public string Name { get; set; }
	        public string Address { get; set; }
	        public string City { get; set; }
	        public string State { get; set; }
	        public string Zip { get; set; }
	        [DataType(DataType.EmailAddress)]
	        public string Email { get; set; }
	    }
	}
	</pre>

La classe **Contacts** consente di definire i dati che verranno archiviati per ogni contatto, oltre a una chiave primaria, `ContactID`, necessaria per il database. La classe **Contacts** rappresenta i dati del contatto che verranno visualizzati. Ogni istanza di un oggetto Contacts corrisponderà a una riga all'interno di una tabella di database relazionale e ogni proprietà della classe Contacts sarà mappata a una colonna nella tabella di database relazionale. Più avanti in questa esercitazione verranno esaminati i dati dei contatti contenuti nel database.

###Web Forms Scaffolding 
Nella sezione precedente è stata creata la classe modello **Contacts**. È ora possibile usare l'utilità di scaffolding per Web Form per generare le pagine *List*, *Insert*, *Edit* e *Delete* necessarie quando si usano questi dati. L'utilità di scaffolding per Web Form usa Entity Framework, Bootstrap e Dynamic Data. Per impostazione predefinita, l'utilità di scaffolding per Web Form è installata come un'estensione del progetto all'interno del modello di progetto in Visual Studio 2013.

La procedura seguente consente di usare l'utilità di scaffolding per Web Form.

1. Sulla barra dei menu di Visual Studio selezionare **Strumenti** -> **Estensioni e aggiornamenti**. La finestra di dialogo **Estensioni e aggiornamenti** viene visualizzata.
2. Nel riquadro sinistro della finestra di dialogo selezionare **Online** -> **Visual Studio Gallery** -> **Strumenti** -> **Scaffolding**.
3. Se la voce "Web Forms Scaffolding" non è presente nell'elenco, digitare "Web Forms Scaffolding" nella casella di ricerca nella parte destra della finestra di dialogo.  
4. Se l'utilità di scaffolding per Web Form non è già installata, selezionare **Download** per scaricare e installare l'estensione 'Web Forms Scaffolding'. Se necessario, riavviare Visual Studio. Assicurarsi di salvare le modifiche al progetto, quando richiesto. ![Finestra di dialogo estensioni e aggiornamenti](./media/web-sites-dotnet-web-forms-secure/ExtensionsAndUpdatesDB.png)  
5. Creare il progetto (**Ctrl+Shift+B**). Per usare il meccanismo scaffolding, è innanzitutto necessario creare il progetto.  
6. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul nome del *progetto* e quindi scegliere **Aggiungi** -> **New Scaffolded Item**. La finestra di dialogo **Add Scaffold** viene visualizzata.
7. Selezionare **Web Form** nel riquadro sinistro e quindi **Pagine Web Form che usano Entity Framework** dal riquadro centrale. Fare quindi clic su **Aggiungi**. ![Finestra di dialogo Add Scaffold dell'API Web](./media/web-sites-dotnet-web-forms-secure/SecureWebForms13a.png) La finestra di dialogo **Aggiungi pagine Web Form** viene visualizzata.  

8. Nella finestra di dialogo **Aggiungi pagine Web Form** impostare **Classe modello** su `Contact (ContactManager.Models)` Impostare **Classe contesto di dati** su `ApplicationDbContext (ContactManager.Models)`. Fare quindi clic su **Aggiungi**. ![Pagine Web Form, finestra di dialogo Aggiungi](./media/web-sites-dotnet-web-forms-secure/SecureWebForms13b.png)

L'utilità di scaffolding per Web Form aggiunge una nuova cartella contenente le pagine *Default.aspx*, *Delete.aspx*, *Edit.aspx* e *Insert.aspx*. L'utilità di scaffolding per Web Form crea inoltre una cartella *DynamicData* che contiene le cartelle *EntityTemplates* e *FieldTemplates*. La classe`ApplicationDbContext` verrà usata per il database di appartenenza e per i dati relativi ai contatti.

###Configurare l'applicazione per l'utilizzo del modello di dati 
L'attività successiva consiste nell'abilitare la funzionalità Migrazioni Code First, in modo da creare il database basato sul modello di dati creato. Verranno anche aggiunti dati di esempio e un inizializzatore di dati.

1. Scegliere **Gestione pacchetti NuGet** dal menu **Strumenti**, quindi fare clic su **Console di Gestione pacchetti**. ![Pagine Web Form, finestra di dialogo Aggiungi](./media/web-sites-dotnet-web-forms-secure/SecureWebForms13c.png)  
2. Nella finestra della Console di gestione pacchetti, immettere il comando seguente: <pre class="prettyprint"> enable-migrations </pre> le migrazioni di abilitazione di comandi Crea un *migrazioni* cartella e inserire in tale cartella un *Configuration.cs* file che è possibile modificare per il seeding del database e configurare le migrazioni dei dati.  
3. Nel **Console di gestione pacchetti** finestra, immettere il comando seguente: <pre class="prettyprint"> aggiungere-migration Initial </pre> il `add-migration Initial` comando genera un file denominato <date_stamp>iniziale nel *migrazioni* cartella che crea il database. Il primo parametro (Initial) è arbitrario e viene usato per creare il nome del file. È possibile visualizzare i nuovi file di classe in **Esplora soluzioni**. Nella classe `Initial` il metodo `Up` consente di creare la tabella `Contact` e il metodo `Down`, che viene usato per tornare allo stato precedente, consente di rimuoverla.  
4. Aprire il file *Migrations\\Configuration.cs*. 
5. Aggiungere lo spazio dei nomi seguente: <pre class="prettyprint"> utilizzando ContactManager. Models; </pre>  
6. Sostituire il metodo `Seed` con il codice seguente: <pre class="prettyprint"> protected override void Seed(ContactManager.Models.ApplicationDbContext context) { context.Contacts.AddOrUpdate(p => p.Name, new Contacts { ContactId = 1, Name = "Ivan Irons", Address = "One Microsoft Way", City = "Redmond", State = "WA", Zip = "10999", Email = "ivani@wideworldimporters.com", }, new Contacts { ContactId = 2, Name = "Brent Scholl", Address = "5678 1st Ave W", City = "Redmond", State = "WA", Zip = "10999", Email = "brents@wideworldimporters.com", }, new Contacts { ContactId = 3, Name = "Terrell Bettis", Address = "9012 State St", City = "Redmond", State = "WA", Zip = "10999", Email = "terrellb@wideworldimporters.com", }, new Contacts { ContactId = 4, Name = "Jo Cooper", Address = "3456 Maple St", City = "Redmond", State = "WA", Zip = "10999", Email = "joc@wideworldimporters.com", }, new Contacts { ContactId = 5, Name = "Ines Burnett", Address = "7890 2nd Ave E", City = "Redmond", State = "WA", Zip = "10999", Email = "inesb@wideworldimporters.com", } ); } </pre> This code initializes (seeds) the database with the contact information. Per ulteriori informazioni sul seeding del database, vedere la pagina relativa al [seeding e al debug di database di Entity Framework (EF)](http://blogs.msdn.com/b/rickandy/archive/2013/02/12/seeding-and-debugging-entity-framework-ef-dbs.aspx).  
7. Nel **Console di gestione pacchetti** immettere il comando: <pre class="prettyprint"> aggiornamento database </pre> il `update-database` viene eseguita la migrazione prima che crea il database. Per impostazione predefinita, il database viene creato come database LocalDB SQL Server Express.![Console di gestione pacchetti](./media/web-sites-dotnet-web-forms-secure/SecureWebForms13d.png)  

###Eseguire l'applicazione in locale e visualizzare i dati 
Eseguire l'applicazione adesso per verificare se i contatti vengono visualizzati.

1. Innanzitutto, compilare il progetto (**Ctrl+Shift+B**).  
2. Premere **Ctrl+F5** per eseguire l'applicazione. Si aprirà il browser e verrà visualizzata la pagina *Default.aspx*.
3. Selezionare il collegamento **Contact Demo** nella parte superiore della pagina per visualizzare la pagina *Contact List*. ![Pagina elenco contatti](./media/web-sites-dotnet-web-forms-secure/SecureWebForms17.png)  

##Abilitazione di SSL per il progetto 
Secure Sockets Layer (SSL) è un protocollo definito per consentire ai server Web e ai client Web di comunicare in modo più sicuro tramite l'uso della crittografia. Quando non si usa SSL, i dati inviati tra il client e il server possono essere soggetti allo sniffing dei pacchetti da qualsiasi soggetto con accesso fisico alla rete. Inoltre, numerosi schemi di autenticazione comuni non sono sicuri sul protocollo HTTP normale. In particolare, l'autenticazione di base e l'autenticazione basata su form inviano credenziali non crittografate. Per essere sicuri, questi schemi di autenticazione devono usare il protocollo SSL.

1. In **Esplora soluzioni** fare clic sul progetto **ContactManager**, quindi premere **F4** per visualizzare la finestra di dialogo **Proprietà**. 
2. Modificare il valore di **SSL abilitato** in `true`. 
3. Copiare il valore di **URL SSL**L per poterlo riutilizzare successivamente. L'URL SSL sarà `https://localhost:44300/` a meno che non siano state create app Web SSL in precedenza.![Proprietà del progetto](./media/web-sites-dotnet-web-forms-secure/SecureWebForms18.png)  
4. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul progetto **Contact Manager**, quindi scegliere **Proprietà**.
5. Nella scheda a sinistra fare clic su **Web**.
6. Modificare **URL progetto** affinché venga usato l**'URL SSL** salvato in precedenza ![Proprietà del progetto Web](./media/web-sites-dotnet-web-forms-secure/SecureWebForms19.png)  
7. Per salvare la pagina, premere **CTRL+S**.
8. Premere **Ctrl+F5** per eseguire l'applicazione. In Visual Studio verrà visualizzata un'opzione che consente di evitare eventuali avvisi SSL.  
9. Fare clic su **Sì** per considerare attendibile il certificato SSL di IIS Express e continuare. ![Dettagli del certificato SSL di IIS Express](./media/web-sites-dotnet-web-forms-secure/SecureWebForms20.png) Verrà visualizzato un avviso di sicurezza.  

10. Fare clic su **Sì** per installare il certificato per l'host locale. ![Finestra di dialogo Avviso di sicurezza](./media/web-sites-dotnet-web-forms-secure/SecureWebForms21.png) Verrà visualizzata la finestra del browser.

È possibile testare l'applicazione Web in locale tramite SSL.



##Aggiungere un provider OAuth 2.0 
In Web Form ASP.NET sono disponibili opzioni avanzate per l'appartenenza e l'autenticazione, tra cui OAuth. OAuth è un protocollo aperto che consente di eseguire l'autenticazione sicura in un metodo semplice e standard da applicazioni Web, per dispositivi mobili e desktop. Il modello Internet ASP.NET MVC usa OAuth per esporre Facebook, Twitter, Google e Microsoft come provider di autenticazione. Benché in questa esercitazione si utilizzi solo Google come provider di autenticazione, è possibile modificare con facilità il codice per utilizzare un altro provider. I passaggi per l'implementazione di altri provider sono molto simili ai passaggi disponibili in questa esercitazione.

Oltre all'autenticazione, nell'esercitazione verranno utilizzati anche i ruoli per implementare l'autorizzazione. Solo gli utenti aggiunti al ruolo `canEdit` saranno in grado di modificare i dati, ovvero creare, modificare o eliminare i contatti.

La procedura seguente consente di aggiungere un provider di autenticazione Google.

1. Aprire il file *App_Start\\Startup.Auth.cs*. 
2. Rimuovere i caratteri di commento dal `app.UseGoogleAuthentication()` metodo in modo che il metodo appare come segue: <pre class="prettyprint"> app. UseGoogleAuthentication (nuovo GoogleOAuth2AuthenticationOptions() {ClientId = "", ClientSecret = ""}); </pre>
3. Passare a [Google Developers Console](https://console.developers.google.com/). Sarà necessario eseguire l'accesso con l'account di posta elettronica per sviluppatori di Google (gmail.com). Se non si dispone di un account Google, selezionare il collegamento **Crea un account**. Verrà quindi visualizzata la pagina  **Google Developers Console**. ![Google Developers Console](./media/web-sites-dotnet-web-forms-secure/SecureWebForms21a.png)  

4. Fare clic sul pulsante **Create Project** e immettere un nome e un ID per il progetto (è possibile usare i valori predefiniti). Fare quindi clic sulla casella di controllo **agreement** e quindi sul pulsante **Create** ![Google - nuovo progetto](./media/web-sites-dotnet-web-forms-secure/SecureWebForms21b.png) Entro pochi secondi verrà creato il nuovo progetto e la pagina del nuovo progetto verrà visualizzata nel browser.
5. Nella scheda sinistra, fare clic su **APIs & auth** e quindi su **Credentials**.
6. Fare clic su **Create New Client ID** in **OAuth**. Verrà visualizzata la finestra di dialogo **Create Client ID**. ![Google - creare ID Client](./media/web-sites-dotnet-web-forms-secure/SecureWebForms21c.png)  
7. Nella finestra di dialogo **Create Client ID** per il tipo di applicazione mantenere l'impostazione predefinita **Web application**.  
8. Impostare **Authorized JavaScript Origins** sull'URL SSL usato in precedenza in questa esercitazione **https://localhost:44300/** a meno che non siano stati creati altri progetti SSL). Questo URL rappresenta l'origine dell'applicazione. Per questo esempio, sarà necessario immettere solo l'URL di test localhost. È tuttavia possibile immettere più URL per gestire gli URL per localhost e gli URL per l'ambiente di produzione.  

9. Impostare il **autorizzato URI di reindirizzamento** al seguente: <pre class="prettyprint"> https://localhost:44300/signin-google </pre> questo valore è l'URI che OAuth ASP.NET agli utenti di comunicare con il server OAuth di google. Immettere l'URL SSL usato in precedenza (*https://localhost:44300/** a meno che non siano stati creati altri progetti SSL).
10. Fare clic sul pulsante **Create Client ID**.
11. In Visual Studio aggiornare il metodo `UseGoogleAuthentication` della pagina *Startup.Auth.cs* copiando e incollando i valori di **ID app** e **Chiave privata app** nel metodo. Il **AppId** e **segreto dell'App** valori riportati di seguito sono riportati alcuni esempi e verrà funziona. <pre class="prettyprint"> utilizzando System using Microsoft.AspNet.Identity; mediante EntityFramework; tramite Microsoft.AspNet.Identity.Owin; tramite Microsoft.Owin; utilizzando Cookies; utilizzando DataProtection; utilizzando owin; utilizzando Owin; utilizzando ContactManager. Models;
	
	namespace ContactManager { public partial class Startup {
	
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
	} </pre>  
12. Premere **CTRL+F5** per compilare ed eseguire l'applicazione. Fare clic sul collegamento **Accedi**.
13. In **Use another service to log in**, fare clic su **Google**. ![// Log in](./media/web-sites-dotnet-web-forms-secure/SecureWebForms21d.png)  
14. Se occorre immettere le credenziali, l'utente verrà reindirizzato al sito Google per l'immissione delle credenziali.![Google - accesso](./media/web-sites-dotnet-web-forms-secure/SecureWebForms21e.png)  
15. Dopo avere immesso le credenziali, verrà richiesto di concedere le autorizzazioni all'applicazione Web appena creata:![Account del servizio di progetto predefinito](./media/web-sites-dotnet-web-forms-secure/SecureWebForms21f.png)  
16. Fare clic **Accept**. L'utente verrà reindirizzato alla pagina **Register** dell'applicazione **ContactManager** in cui è possibile registrare l'account Google. ![Registrare con l'Account Google](./media/web-sites-dotnet-web-forms-secure/SecureWebForms21g.png)  
17. Sarà possibile modificare il nome di registrazione dell'indirizzo di posta elettronica locale usato per l'account Gmail, anche se in generale è preferibile mantenere l'alias di posta elettronica predefinito, ovvero quello usato per l'autenticazione. Fare clic su **Log in**.

##Usare l'API di appartenenza per limitare l'accesso 
ASP.NET Identity è il sistema di appartenenza usato per l'autenticazione quando si crea un'applicazione Web ASP.NET. Semplifica la creazione dei dati del profilo specifici dell'utente con i dati dell'applicazione. ASP.NET Identity consente inoltre di scegliere il modello di persistenza dei profili utente nell'applicazione. È possibile archiviare i dati in un database SQL Server o in un altro archivio dati, inclusi gli archivi dati *NoSQL*, come ad esempio le tabelle di Archiviazione di Azure.

Nel modello Web Form ASP.NET predefinito è disponibile la funzionalità di appartenenza incorporata che può essere usata immediatamente durante l'esecuzione dell'applicazione. Si userà ASP.NET Identity per aggiungere un ruolo amministratore e assegnare un utente a tale ruolo. Si apprenderà quindi a limitare l'accesso alla cartella di amministrazione e alle pagine in essa contenute che vengono usate per modificare i dati di contatto.

###Aggiungere un amministratore 
ASP.NET Identity consente di aggiungere un ruolo amministratore e assegnare un utente a tale ruolo tramite il codice.

1. In **Esplora soluzioni** aprire il file *Configuration.cs* nella cartella *Migrations*.
2. Aggiungere il seguente `using` istruzioni all'interno i `ContactManger.Migrations` dello spazio dei nomi: <pre class="prettyprint"> using Microsoft.AspNet.Identity; tramite EntityFramework; </pre>
3. Aggiungere il seguente `AddUserAndRole` metodo per la `Configuration` classe dopo la `Seed` metodo: <pre class="prettyprint"> public void AddUserAndRole(ContactManager.Models.ApplicationDbContext context) {IdentityResult IdRoleResult; IdentityResult IdUserResult;

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
	</pre>
4. Aggiungere una chiamata al metodo `AddUserAndRole` all'inizio del metodo `Seed`. Si noti che solo l'inizio del `Seed` metodo è attualmente visualizzata. <pre class="prettyprint"> protected override void Seed (ContactManager.Models.ApplicationDbContext context) { <mark>AddUserAndRole(context);</mark> </pre>  
5. Dopo aver salvato le modifiche, nel **Console di gestione pacchetti** eseguire il comando seguente: <pre class="prettyprint"> Update-Database </pre> questo codice crea un nuovo ruolo denominato `canEdit` e crea un nuovo utente locale con il messaggio di posta elettronica di canEditUser@wideworldimporters.com. Quindi, il codice aggiunge canEditUser@wideworldimporters.com per il `canEdit` ruolo. Per altre informazioni, vedere la pagina relativa alle risorse di [ASP.NET Identity](http://www.asp.net/identity).  

###Limitare l'accesso alla cartella Administration 
L'applicazione di esempio **ContactManager** consente di visualizzare i contatti sia agli utenti anonimi che agli utenti che hanno eseguito l'accesso. Dopo avere completato questa sezione, tuttavia, solo gli utenti assegnati al ruolo "canEdit" potranno modificare i contatti.

Verrà creata una cartella denominata *Admin* a cui potranno accedere solo gli utenti assegnati al ruolo "canEdit".

1. In **Esplora soluzioni** aggiungere una sottocartella alla cartella *Contacts* e denominare la sottocartella *Admin*.
2. Spostare i file seguenti dalla cartella *Contacts* alla cartella *Contacts/Admin*:  
	- *Delete.aspx * e * Delete.aspx.cs*
	- *Edit.aspx * e * Edit.aspx.cs*
	- *Insert.aspx * e * Insert.aspx.cs* 
3. Aggiornare i riferimenti di collegamento in *Contacts/Default.aspx* aggiungendo "Admin /" prima i riferimenti di pagine che si collegano a *Insert*, *Edit. aspx*, e *Delete.aspx*: <pre class="prettyprint"> &lt;%@ Page Title=&quot;ContactsList&quot; Language=&quot;C#&quot; MasterPageFile=&quot;~/Site.Master&quot; CodeBehind=&quot;Default.aspx.cs&quot; Inherits=&quot;ContactManager.Contacts.Default&quot; ViewStateMode=&quot;Disabled&quot; %&gt; &lt;%@ Register TagPrefix=&quot;FriendlyUrls&quot; Namespace=&quot;Microsoft.AspNet.FriendlyUrls&quot; %&gt;
	
	&lt;asp:Content runat=&quot;server&quot; ContentPlaceHolderID=&quot;MainContent&quot;&gt; &lt;h2&gt;Contacts List&lt;/h2&gt; &lt;p&gt; &lt;asp:HyperLink runat=&quot;server&quot; NavigateUrl=&quot;<mark>Admin/</mark>Insert.aspx&quot; Text=&quot;Create new&quot; /&gt; &lt;/p&gt; &lt;div&gt; &lt;asp:ListView runat=&quot;server&quot; DataKeyNames=&quot;ContactId&quot; ItemType=&quot;ContactManager.Models.Contacts&quot; AutoGenerateColumns=&quot;false&quot; AllowPaging=&quot;true&quot; AllowSorting=&quot;true&quot; SelectMethod=&quot;GetData&quot;&gt; &lt;EmptyDataTemplate&gt; There are no entries found for Contacts &lt;/EmptyDataTemplate&gt; &lt;LayoutTemplate&gt; &lt;table class=&quot;table&quot;&gt; &lt;thead&gt; &lt;tr&gt; &lt;th&gt;Name&lt;/th&gt; &lt;th&gt;Address&lt;/th&gt; &lt;th&gt;City&lt;/th&gt; &lt;th&gt;State&lt;/th&gt; &lt;th&gt;Zip&lt;/th&gt; &lt;th&gt;Email&lt;/th&gt; &lt;th&gt;&amp;nbsp;&lt;/th&gt; &lt;/tr&gt; &lt;/thead&gt; &lt;tbody&gt; &lt;tr runat=&quot;server&quot; id=&quot;itemPlaceholder&quot; /&gt; &lt;/tbody&gt; &lt;/table&gt; &lt;/LayoutTemplate&gt; &lt;ItemTemplate&gt; &lt;tr&gt; &lt;td&gt; &lt;asp:DynamicControl runat=&quot;server&quot; DataField=&quot;Name&quot; ID=&quot;Name&quot; Mode=&quot;ReadOnly&quot; /&gt; &lt;/td&gt; &lt;td&gt; &lt;asp:DynamicControl runat=&quot;server&quot; DataField=&quot;Address&quot; ID=&quot;Address&quot; Mode=&quot;ReadOnly&quot; /&gt; &lt;/td&gt; &lt;td&gt; &lt;asp:DynamicControl runat=&quot;server&quot; DataField=&quot;City&quot; ID=&quot;City&quot; Mode=&quot;ReadOnly&quot; /&gt; &lt;/td&gt; &lt;td&gt; &lt;asp:DynamicControl runat=&quot;server&quot; DataField=&quot;State&quot; ID=&quot;State&quot; Mode=&quot;ReadOnly&quot; /&gt; &lt;/td&gt; &lt;td&gt; &lt;asp:DynamicControl runat=&quot;server&quot; DataField=&quot;Zip&quot; ID=&quot;Zip&quot; Mode=&quot;ReadOnly&quot; /&gt; &lt;/td&gt; &lt;td&gt; &lt;asp:DynamicControl runat=&quot;server&quot; DataField=&quot;Email&quot; ID=&quot;Email&quot; Mode=&quot;ReadOnly&quot; /&gt; &lt;/td&gt; &lt;td&gt; &lt;a href=&quot;<mark>Admin/</mark>Edit.aspx?ContactId=&lt;%#: Item.ContactId%&gt;&quot;&gt;Edit&lt;/a&gt; | &lt;a href=&quot;<mark>Admin/</mark>Delete.aspx?ContactId=&lt;%#: Item.ContactId%&gt;&quot;&gt;Delete&lt;/a&gt; &lt;/td&gt; &lt;/tr&gt; &lt;/ItemTemplate&gt; &lt;/asp:ListView&gt; &lt;/div&gt; &lt;/asp:Content&gt; </pre>
4. Aggiornare i sei riferimenti del codice `Response.Redirect("Default.aspx")` a `Response.Redirect("~/Contacts/Default.aspx")` per i tre file seguenti:  
	- *Delete.aspx.cs*
	- *Edit.aspx.cs*
	- *INSERT.aspx.cs*  

	Ora i collegamenti funzioneranno correttamente durante la visualizzazione e l'aggiornamento dei dati di contatto.
5. Per limitare l'accesso alla cartella *Admin*, in **Esplora soluzioni** fare clic con il pulsante destro del mouse sulla cartella *Admin* e scegliere **Aggiungi nuovo elemento**.
6. Nell'elenco di modelli Web per Visual C# selezionare **File di configurazione Web** nell'elenco centrale, accettare il nome predefinito *Web.config* e quindi selezionare **Aggiungi**.
7. Sostituire il contenuto inXML esistente nel file *Web.config* con il codice seguente:<pre class="prettyprint"> &lt;?xml version=&quot;1.0&quot;?&gt; &lt;configuration&gt; &lt;system.web&gt; &lt;authorization&gt; &lt;allow roles=&quot;canEdit&quot;/&gt; &lt;deny users=&quot;*&quot;/&gt; &lt;/authorization&gt; &lt;/system.web&gt; &lt;/configuration&gt; </pre>
8. Salvare il file *Web.config*. Il file *Web.config* specifica che solo gli utenti assegnati al ruolo "canEdit" possono accedere alle pagine contenute nella cartella *Admin*. 

Quando un utente che non fa parte del ruolo "canEdit" tenta di modificare i dati, viene reindirizzato alla pagina *Log in*.

##Distribuire l'applicazione con il database in Azure 
Ora che l'applicazione Web è stata completata, è possibile pubblicarla in Azure.

###Pubblicare l'applicazione 
1. In Visual Studio compilare il progetto (**Ctrl+Shift+B**).
2. Fare clic con il pulsante destro del mouse in **Esplora soluzioni**, quindi scegliere **Pubblica**. ![Opzione di menu per la pubblicazione](./media/web-sites-dotnet-web-forms-secure/SecureWebForms22.png) La finestra di dialogo **Pubblica sito Web** viene visualizzata. ![Finestra di dialogo Pubblica sito Web](./media/web-sites-dotnet-web-forms-secure/SecureWebForms22a.png)  
3. Nella scheda **Profilo** selezionare **App Web di Azure** come destinazione di pubblicazione, se questa non è già selezionata. ![Finestra di dialogo Pubblica sito Web](./media/web-sites-dotnet-web-forms-secure/SecureWebForms23.png)  
4. Se l'accesso non è ancora stato eseguito, fare clic su **Accedi**.
5. Selezionare il sito Web esistente creato in precedenza in questa esercitazione nella casella di riepilogo a discesa **Siti Web esistenti** e fare clic sul pulsante **OK**. ![Selezionare la finestra di dialogo sito Web esistente](./media/web-sites-dotnet-web-forms-secure/SecureWebForms25.png) Se viene richiesto di salvare le modifiche apportate al profilo, scegliere **Sì**.
6. Fare clic sulla scheda **Impostazioni**. ![Selezionare la finestra di dialogo sito Web esistente](./media/web-sites-dotnet-web-forms-secure/SecureWebForms26.png)  
7. Impostare la casella di riepilogo a discesa **Configurazione** su **Debug**.
8. Fare clic sull'icona con la **freccia verso il basso** accanto alla voce **ApplicationDbContext** e impostarla su **ContactDB**.
9. Selezionare la casella di controllo **Esegui migrazioni Code First**.  

	>[AZURE.NOTE]In questo esempio selezionare questa casella di controllo solo se l'applicazione viene pubblicata per la prima volta. In questo modo, il metodo *Seed* nel file *Configuration.cs* verrà chiamato una volta sola.

10. Fare quindi clic su  **Pubblica**. L'applicazione verrà pubblicata in Azure.

>[AZURE.NOTE]Se Visual Studio è stato chiuso e riaperto dopo la creazione del profilo di pubblicazione, è possibile che la stringa di connessione non sia visualizzata nell'elenco a discesa. In tale caso, invece di modificare il profilo di pubblicazione creato in precedenza, crearne uno in base alla stessa procedura eseguita in precedenza, quindi attenersi alle istruzioni visualizzate nella scheda **Settings**.

###Verificare l'applicazione in Azure 
1. Nel browser fare clic sul collegamento **Contact Demo**. Viene visualizzato l'elenco di contatti. ![Contatti elencati nel Browser](./media/web-sites-dotnet-web-forms-secure/SecureWebForms27.png)  

2. Selezionare **Create new** nella pagina **Contacts List** ![Contatti elencati nel Browser](./media/web-sites-dotnet-web-forms-secure/SecureWebForms29.png) Si viene reindirizzati alla pagina **Login**perché l'account con cui si è connessi non può modificare i contatti.
3. Dopo avere immesso l'indirizzo di posta elettronica e la password indicati di seguito, fare clic sul pulsante **Log in**. **Email**: `canEditUser@wideworldimporters.com` **Password**: `Pa$$word1` ![Pagina di accesso](./media/web-sites-dotnet-web-forms-secure/SecureWebForms28.png)  

4. Immettere nuovi dati per ogni campo e quindi premere il pulsante **Insert**. ![Pagina Add New Contact](./media/web-sites-dotnet-web-forms-secure/SecureWebForms30.png)Viene visualizzata la pagina *EditContactList.aspx* con il nuovo record. ![Pagina Add New Contact](./media/web-sites-dotnet-web-forms-secure/SecureWebForms31.png)

5. Selezionare il collegamento **Log off**.

###Arrestare l'applicazione 
Per evitare che altre persone registrino e usino questa applicazione di esempio, il sito Web verrà arrestato.

1. Nel menu **Visualizza** di Visual Studio scegliere **Esplora server**. 
2. In **Esplora server**, accedere ad **App Web**.
3. Fare clic con il pulsante destro del mouse su ogni istanza del sito Web, quindi selezionare **Arresta sito Web**. ![Arrestare la voce di menu sito Web](./media/web-sites-dotnet-web-forms-secure/SecureWebForms26a.png)  

	In alternativa, dal portale di gestione di Microsoft Azure è possibile selezionare il sito Web, quindi fare clic sull'icona di **arresto** nella parte inferiore della pagina. ![Pagina Add New Contact](./media/web-sites-dotnet-web-forms-secure/SecureWebForms26b.png)

##Verificare il database 
È importante conoscere le procedure per visualizzare e modificare il database direttamente, in quanto in questo modo è possibile confermare i dati nel database e acquisire informazioni sul modo in cui i dati vengono archiviati in ogni tabella.

###Esaminare il database SQL di Azure 
1. In Visual Studio aprire **Esplora server** e passare a **ContactDB**.
2. Fare clic con il pulsante destro del mouse su **ContactDB**, quindi scegliere **Apri in Esplora oggetti di SQL Server**. ![Apri nella voce di menu di Esplora oggetti di SQL Server](./media/web-sites-dotnet-web-forms-secure/SecureWebForms32.png)  
3. Nella finestra di dialogo **Aggiungi regola firewall** selezionare **Aggiungi regola firewall**.  

	>[AZURE.NOTE]Se non è possibile espandere **Database SQL** e non è possibile visualizzare **ContactDB** da Visual Studio, è possibile seguire le istruzioni per aprire una porta del firewall o un intervallo di porte. A questo scopo, seguire le istruzioni fornite in **Configurazione delle regole del firewall per Azure** verso la fine dell'[esercitazione per MVC](web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database.md). In alternativa, per verificare i dati del database locale è anche possibile compilare, eseguire e aggiungere i dati all'applicazione in locale (**CTRL+F5** da Visual Studio).

4. Se viene visualizzata la finestra di dialogo **Connetti al server**, immettere la **password** creata all'inizio di questa esercitazione e fare clic sul pulsante **Connetti**.

	>[AZURE.NOTE]**Esplora soluzioni***Proprietà**PublishProfiles**ContactManager.pubxml*
5. Espandere il database **contactDB**, quindi espandere **Tabelle**.
6. Fare clic con il pulsante destro del mouse sulla tabella **dbo.AspNetUsers** quindi scegliere **Visualizza dati**. ![Voce di menu Visualizza dati](./media/web-sites-dotnet-web-forms-secure/SecureWebForms34.png) È possibile visualizzare i dati associati il canEditUser@contoso.com utente. ![Finestra ContactManager](./media/web-sites-dotnet-web-forms-secure/SecureWebForms35.png)  

###Aggiungere un utente al ruolo Admin modificando il database 
Nei passaggi precedenti dell'esercitazione, per aggiungere utenti al ruolo canEdit è stato utilizzato codice. Un metodo alternativo consiste nel modificare i dati direttamente nelle tabelle di appartenenza. Nella procedura seguente viene illustrato come utilizzare questo metodo alternativo per aggiungere un utente a un ruolo.

1. In **Esplora oggetti di SQL Server** fare clic con il pulsante destro del mouse su **dbo.AspNetUserRoles** e scegliere **Visualizza dati**. ![Dati AspNetUserRoles](./media/web-sites-dotnet-web-forms-secure/SecureWebForms36.png)  
2. Copiare il valore di *RoleId* e incollarlo nella riga vuota (nuova). ![Dati AspNetUserRoles](./media/web-sites-dotnet-web-forms-secure/SecureWebForms37.png)  
3. Nella tabella **dbo.AspNetUsers** individuare l'utente da inserire nel ruolo, copiare l'*Id* dell'utente,
4. quindi incollarlo **nel campo **UserId** della nuova riga nella tabella **AspNetUserRoles**.  

>[AZURE.NOTE]Microsoft sta attualmente lavorando alla creazione di uno strumento per semplificare la gestione di utenti e ruoli.

##Passaggi successivi
Per altre informazioni su Web Form ASP.NET, vedere l'articolo relativo alle [informazioni si Web Form ASP.NET](http://www.asp.net/web-forms) nel sito Web dedicato ad ASP.NET e gli argomenti relativi alle [guide ed esercitazioni per Microsoft Azure](http://azure.microsoft.com/documentation/services/web-sites/#net).

Questa esercitazione è basata sull'esercitazione MVC [Distribuzione di un'app ASP.NET MVC 5 sicura con appartenenza, OAuth e database SQL in un sito Web di Azure](web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database.md) scritta da Rick Anderson (Twitter [@RickAndMSFT](https://twitter.com/RickAndMSFT)) con il supporto di Tom Dykstra e Barry Dorrans (Twitter [@blowdart](https://twitter.com/blowdart)).

Se lo si desidera, inviare commenti e suggerimenti sugli aspetti ritenuti utili e su eventuali miglioramenti da apportare, non solo in merito all'esercitazione ma anche ai prodotti illustrati nell'esercitazione. I commenti e suggerimenti degli utenti risulteranno utili per definire la priorità dei miglioramenti da apportare. È inoltre possibile richiedere nuovi argomenti e votare gli argomenti esistenti alla pagina [Show Me How With Code](http://aspnet.uservoice.com/forums/228522-show-me-how-with-code).


<!--HONumber=54-->