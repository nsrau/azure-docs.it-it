---
title: Creare un'app line-of-business in Azure con l'autenticazione di Azure Active Directory | Documentazione Microsoft
description: Informazioni su come creare un'applicazione line-of-business ASP.NET MVC nel servizio app di Azure che esegue l'autenticazione con Azure Active Directory
services: app-service\web, active-directory
documentationcenter: .net
author: cephalin
manager: erikre
editor: 
ms.assetid: ad947bdb-4463-43ff-a5e3-91d9b2169b60
ms.service: app-service-web
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: web
ms.date: 09/01/2016
ms.author: cephalin
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 6eadf0a521a32c5bc580908e4e4b7f4305e2bf7e
ms.contentlocale: it-it
ms.lasthandoff: 07/21/2017

---
# <a name="create-a-line-of-business-azure-app-with-azure-active-directory-authentication"></a>Creare un'app line-of-business in Azure con l'autenticazione di Azure Active Directory
Questo articolo illustra come creare un'app line-of-business .NET nelle [app Web del servizio app di Azure](http://go.microsoft.com/fwlink/?LinkId=529714) usando la funzionalità [Autenticazione/Autorizzazione](../app-service/app-service-authentication-overview.md). Spiega inoltre come usare l' [API Graph di Azure Active Directory](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog) per eseguire query sui dati della directory nell'applicazione.

Il tenant di Azure Active Directory usato può essere una directory solo di Azure. In alternativa, può essere [sincronizzato con l'istanza di Active Directory locale](../active-directory/active-directory-aadconnect.md) per creare un'unica esperienza Single Sign-On per gli utenti locali e remoti. In questo articolo si usa la directory predefinita per l'account Azure.

<a name="bkmk_build"></a>

## <a name="what-you-will-build"></a>Obiettivo di compilazione
Verrà compilata una semplice applicazione CRUD (Create-Read-Update-Delete) line-of-business nelle app Web di Servizio app di Azure per tenere traccia degli elementi di lavoro con le seguenti funzionalità:

* Autenticazione degli utenti con Azure Active Directory
* Esecuzione di query su utenti e gruppi della directory tramite l' [API Graph di Azure Active Directory](http://msdn.microsoft.com/library/azure/hh974476.aspx)
* Usare il modello MVC ASP.NET *Nessuna autenticazione*

Se è necessario il Controllo degli accessi in base al ruolo per l'applicazione line-of-business in Azure, vedere il [passaggio successivo](#next).

<a name="bkmk_need"></a>

## <a name="what-you-need"></a>Elementi necessari
[!INCLUDE [free-trial-note](../../includes/free-trial-note.md)]

Per completare questa esercitazione sarà necessario quanto segue:

* Un tenant di Azure Active Directory con utenti in diversi gruppi
* Autorizzazioni per la creazione di applicazioni nel tenant di Azure Active Directory
* Visual Studio 2013 Update 4 o versione successiva
* [Azure SDK 2.8.1 o versione successiva](https://azure.microsoft.com/downloads/)

<a name="bkmk_deploy"></a>

## <a name="create-and-deploy-a-web-app-to-azure"></a>Creare e distribuire un'app Web in Azure
1. In Visual Studio fare clic su **File** > **Nuovo** > **Progetto**.
2. Selezionare **Applicazione Web ASP.NET**, assegnare un nome al progetto e fare clic su **OK**.
3. Selezionare il modello **MVC**, quindi modificare l'autenticazione in **Nessuna autenticazione**. Assicurarsi che **Host nel cloud** sia selezionata e fare clic su **OK**.
   
    ![](./media/web-sites-dotnet-lob-application-azure-ad/1-create-mvc-no-authentication.png)
4. Nella finestra di dialogo **Crea servizio App** fare clic su **Aggiungi un account** e quindi su **Aggiungi un account** nell'elenco a discesa per accedere al proprio account Azure.
5. Dopo aver eseguito l'accesso, configurare l'app Web. Creare un gruppo di risorse e un nuovo piano di servizio app facendo clic sul pulsante **Nuovo** corrispondente. Fare clic su **Esplorare servizi di Azure aggiuntivi** per continuare.
   
    ![](./media/web-sites-dotnet-lob-application-azure-ad/2-create-app-service.png)
6. Nella scheda **Servizi** fare clic su **+** per aggiungere un database SQL per l'app. 
   
    ![](./media/web-sites-dotnet-lob-application-azure-ad/3-add-sql-database.png)
7. In **Configura database SQL** fare clic su **Nuovo** per creare un'istanza di SQL Server.
8. In **Configura SQL Server**configurare l'istanza di SQL Server. Fare clic su **OK**, **OK** e su **Crea** per avviare la creazione dell'app in Azure.
9. In **Attività del servizio app di Azure**è possibile vedere quando termina la creazione dell'app. Fare clic su **Pubblica &lt;*nomeapp*> in questa app Web adesso**, quindi fare clic su **Pubblica**. 
   
    Al termine del processo di Visual Studio, nel browser verrà aperta l'app pubblicata. 
   
    ![](./media/web-sites-dotnet-lob-application-azure-ad/4-published-shown-in-browser.png)

<a name="bkmk_auth"></a>

## <a name="configure-authentication-and-directory-access"></a>Configurare l'autenticazione e l'accesso alla directory
1. Accedere al [Portale di Azure](https://portal.azure.com).
2. Nel menu a sinistra fare clic su **Servizi app** > **&lt;*nomeapp*>** > **Autenticazione/Autorizzazione**.
   
    ![](./media/web-sites-dotnet-lob-application-azure-ad/5-app-service-authentication.png)
3. Attivare l'autenticazione di Azure Active Directory facendo clic su **Attivato** > **Azure Active Directory** > **Rapida** > **OK**.
   
    ![](./media/web-sites-dotnet-lob-application-azure-ad/6-authentication-express.png)
4. Fare clic su **Salva** nella barra dei comandi.
   
    ![](./media/web-sites-dotnet-lob-application-azure-ad/7-authentication-save.png)
   
    Dopo aver salvato le impostazioni di autenticazione, provare a passare di nuovo all'app nel browser. Le impostazioni predefinite applicano l'autenticazione all'intera app. Se non si è già connessi, si viene reindirizzati a una schermata di accesso. Dopo aver effettuato l'accesso, verrà visualizzata l'app protetta tramite HTTPS. Quindi, è necessario abilitare l'accesso ai dati della directory. 
5. Passare al [portale classico](https://manage.windowsazure.com).
6. Nel menu a sinistra fare clic su **Active Directory** > **Directory predefinita** > **Applicazioni** > **&lt;*nomeapp*>**.
   
    ![](./media/web-sites-dotnet-lob-application-azure-ad/8-find-aad-application.png)
   
    Questa è l'applicazione Azure Active Directory creata automaticamente dal servizio app per abilitare la Autorizzazione/Autenticazione.
7. Fare clic su **Utenti** e **Gruppi** per assicurarsi di avere alcuni utenti e gruppi nella directory. In caso contrario, creare alcuni utenti e gruppi di test.
   
    ![](./media/web-sites-dotnet-lob-application-azure-ad/9-create-users-groups.png)
8. Fare clic su **Configura** per configurare l'applicazione.
9. Scorrere verso il basso fino alla sezione **Chiavi** e aggiungere una chiave selezionando una durata. Fare quindi clic su **Autorizzazioni delegate** e selezionare **Leggi i dati della directory**. 
   Fare clic su **Save**.
   
    ![](./media/web-sites-dotnet-lob-application-azure-ad/10-configure-aad-application.png)
10. Dopo aver salvato le impostazioni, scorrere tornando alla sezione **Chiavi** e fare clic su **Copia** per copiare la chiave client. 
    
     ![](./media/web-sites-dotnet-lob-application-azure-ad/11-get-app-key.png)
    
    > [!IMPORTANT]
    > Se si esce dalla pagina ora, non sarà mai più possibile accedere a questa chiave client.
    > 
    > 
11. Configurare quindi l'app Web con questa chiave. Accedere a [Esplora risorse di Azure](https://resources.azure.com) con il proprio account Azure.
12. Nella parte superiore della pagina fare clic su **Lettura/Scrittura** per apportare modifiche in Esplora risorse di Azure.
    
    ![](./media/web-sites-dotnet-lob-application-azure-ad/12-resource-manager-writable.png)
13. Trovare le impostazioni di autenticazione per l'app disponibili in sottoscrizioni > **&lt;*nomesottoscrizione*>** > **gruppidirisorse** > **&lt;*nomegruppodirisorse*>** > **provider** > **Microsoft.Web** > **siti** > **&lt;*nomeapp*>** > **config** > **authsettings**.
14. Fare clic su **Modifica**.
    
    ![](./media/web-sites-dotnet-lob-application-azure-ad/13-edit-authsettings.png)
15. Nel riquadro di modifica impostare le proprietà `clientSecret` e `additionalLoginParams` come indicato di seguito.
    
        ...
        "clientSecret": "<client key from the Azure Active Directory application>",
        ...
        "additionalLoginParams": ["response_type=code id_token", "resource=https://graph.windows.net"],
        ...
16. Fare clic su **Put** nella parte superiore per inviare le modifiche.
    
    ![](./media/web-sites-dotnet-lob-application-azure-ad/14-edit-parameters.png)
17. A questo punto, per verificare se è disponibile il token di autorizzazione per accedere all'API Graph di Azure Active Directory, passare a **https://&lt;*nomeapp*>.azurewebsites.net/.auth/me** nel browser. Se è stato configurato tutto correttamente, nella risposta JSON verrà visualizzata la proprietà `access_token` .
    
    Il percorso URL `~/.auth/me` è gestito dall'autenticazione/autorizzazione del servizio app per fornire all'utente tutte le informazioni relative alla sessione autenticata. Per altre informazioni, vedere [Autenticazione e autorizzazione nel servizio app di Azure](../app-service/app-service-authentication-overview.md).
    
    > [!NOTE]
    > Il percorso URL `access_token` ha un periodo di scadenza. L'autenticazione/autorizzazione del servizio app offre tuttavia funzionalità di aggiornamento del token con `~/.auth/refresh`. Per altre informazioni sull'uso, vedere [App Service Token Store](https://cgillum.tech/2016/03/07/app-service-token-store/)(Archivio Token del servizio app).
    > 
    > 

Ora si eseguirà un'operazione utile con i dati della directory.

<a name="bkmk_crud"></a>

## <a name="add-line-of-business-functionality-to-your-app"></a>Aggiungere funzionalità line-of-business all'app
Verrà creato un semplice progetto di gestione degli elementi di lavoro CRUD.  

1. Nella cartella ~\Models creare un file di classe denominato WorkItem.cs e sostituire `public class WorkItem {...}` con il codice seguente:
   
     using System.ComponentModel.DataAnnotations;
   
     public class WorkItem   {
   
         [Key]
         public int ItemID { get; set; }
         public string AssignedToID { get; set; }
         public string AssignedToName { get; set; }
         public string Description { get; set; }
         public WorkItemStatus Status { get; set; }
     }
   
     public enum WorkItemStatus   {
   
         Open,
         Investigating,
         Resolved,
         Closed
     }
2. Compilare il progetto per rendere accessibile il nuovo modello alla logica di scaffolding in Visual Studio.
3. Aggiungere un nuovo elemento di scaffolding `WorkItemsController` nella cartella ~\Controllers. Fare clic con il pulsante destro del mouse su **Controller**, scegliere **Aggiungi** e selezionare **Nuovo elemento di scaffolding**. 
4. Selezionare **Controller MVC 5 con visualizzazioni, che usa Entity Framework** e quindi fare clic su **Aggiungi**.
5. Selezionare il modello creato, fare clic su **+**, quindi su **Aggiungi** per aggiungere un contesto dei dati e infine fare clic su **Aggiungi**.
   
   ![](./media/web-sites-dotnet-lob-application-azure-ad/16-add-scaffolded-controller.png)
6. In ~\Views\WorkItems\Create.cshtml (un elemento sottoposto automaticamente a scaffolding) individuare il metodo helper `Html.BeginForm` e apportare le modifiche evidenziate di seguito:  
   
   <pre class="prettyprint">
   @model WebApplication1.Models.WorkItem
   
   @{
    ViewBag.Title = &quot;Create&quot;;
   }
   
   &lt;h2&gt;Create&lt;/h2&gt;
   
   @using (Html.BeginForm(<mark>&quot;Create&quot;, &quot;WorkItems&quot;, FormMethod.Post, new { id = &quot;main-form&quot; }</mark>)) 
   {
    @Html.AntiForgeryToken()
   
    &lt;div class=&quot;form-horizontal&quot;&gt;
        &lt;h4&gt;WorkItem&lt;/h4&gt;
        &lt;hr /&gt;
        @Html.ValidationSummary(true, &quot;&quot;, new { @class = &quot;text-danger&quot; })
        &lt;div class=&quot;form-group&quot;&gt;
            @Html.LabelFor(model =&gt; model.AssignedToID, htmlAttributes: new { @class = &quot;control-label col-md-2&quot; })
            &lt;div class=&quot;col-md-10&quot;&gt;
                @Html.EditorFor(model =&gt; model.AssignedToID, new { htmlAttributes = new { @class = &quot;form-control&quot;<mark>, @type = &quot;hidden&quot;</mark> } })
                @Html.ValidationMessageFor(model =&gt; model.AssignedToID, &quot;&quot;, new { @class = &quot;text-danger&quot; })
            &lt;/div&gt;
        &lt;/div&gt;
   
        &lt;div class=&quot;form-group&quot;&gt;
            @Html.LabelFor(model =&gt; model.AssignedToName, htmlAttributes: new { @class = &quot;control-label col-md-2&quot; })
            &lt;div class=&quot;col-md-10&quot;&gt;
                @Html.EditorFor(model =&gt; model.AssignedToName, new { htmlAttributes = new { @class = &quot;form-control&quot; } })
                @Html.ValidationMessageFor(model =&gt; model.AssignedToName, &quot;&quot;, new { @class = &quot;text-danger&quot; })
            &lt;/div&gt;
        &lt;/div&gt;
   
        &lt;div class=&quot;form-group&quot;&gt;
            @Html.LabelFor(model =&gt; model.Description, htmlAttributes: new { @class = &quot;control-label col-md-2&quot; })
            &lt;div class=&quot;col-md-10&quot;&gt;
                @Html.EditorFor(model =&gt; model.Description, new { htmlAttributes = new { @class = &quot;form-control&quot; } })
                @Html.ValidationMessageFor(model =&gt; model.Description, &quot;&quot;, new { @class = &quot;text-danger&quot; })
            &lt;/div&gt;
        &lt;/div&gt;
   
        &lt;div class=&quot;form-group&quot;&gt;
            @Html.LabelFor(model =&gt; model.Status, htmlAttributes: new { @class = &quot;control-label col-md-2&quot; })
            &lt;div class=&quot;col-md-10&quot;&gt;
                @Html.EnumDropDownListFor(model =&gt; model.Status, htmlAttributes: new { @class = &quot;form-control&quot; })
                @Html.ValidationMessageFor(model =&gt; model.Status, &quot;&quot;, new { @class = &quot;text-danger&quot; })
            &lt;/div&gt;
        &lt;/div&gt;
   
        &lt;div class=&quot;form-group&quot;&gt;
            &lt;div class=&quot;col-md-offset-2 col-md-10&quot;&gt;
                &lt;input type=&quot;submit&quot; value=&quot;Create&quot; class=&quot;btn btn-default&quot;<mark> id=&quot;submit-button&quot;</mark> /&gt;
            &lt;/div&gt;
        &lt;/div&gt;
    &lt;/div&gt;
   }
   
   &lt;div&gt;
    @Html.ActionLink(&quot;Back to List&quot;, &quot;Index&quot;)
   &lt;/div&gt;
   
   @section Scripts {
    @Scripts.Render(&quot;~/bundles/jqueryval&quot;)
    <mark>&lt;script&gt;
        // People/Group Picker Code
        var maxResultsPerPage = 14;
        var input = document.getElementById(&quot;AssignedToName&quot;);
   
        // Access token from request header, and tenantID from claims identity
        var token = &quot;@Request.Headers[&quot;X-MS-TOKEN-AAD-ACCESS-TOKEN&quot;]&quot;;
        var tenant =&quot;@(System.Security.Claims.ClaimsPrincipal.Current.Claims
                        .Where(c => c.Type == &quot;http://schemas.microsoft.com/identity/claims/tenantid&quot;)
                        .Select(c => c.Value).SingleOrDefault())&quot;;
   
        var picker = new AadPicker(maxResultsPerPage, input, token, tenant);
   
        // Submit the selected user/group to be asssigned.
        $(&quot;#submit-button&quot;).click({ picker: picker }, function () {
            if (!picker.Selected())
                return;
            $(&quot;#main-form&quot;).get()[0].elements[&quot;AssignedToID&quot;].value = picker.Selected().objectId;
        });
    &lt;/script&gt;</mark>
   }
   </pre>
   
   Si noti che `token` e `tenant` vengono usati dall'oggetto `AadPicker` per eseguire chiamate all'API Graph di Azure Active Directory. Si aggiungerà `AadPicker` in un secondo momento.     
   
   > [!NOTE]
   > È anche possibile ottenere `token` e `tenant` dal lato client con `~/.auth/me`, ma si tratterebbe di una chiamata server aggiuntiva. Ad esempio:
   > 
   > $.ajax({ dataType: "json", url: "/.auth/me", success: function (data) { var token = data[0].access_token; var tenant = data[0].user_claims .find(c => c.typ === 'http://schemas.microsoft.com/identity/claims/tenantid') .val; } });
   > 
   > 
7. Apportare le stesse modifiche con ~\Views\WorkItems\Edit.cshtml.
8. L'oggetto `AadPicker` è definito in uno script che è necessario aggiungere al progetto. Fare clic con il pulsante destro del mouse sulla cartella ~\Scripts, scegliere **Aggiungi** e fare clic sul **file JavaScript**. Digitare `AadPickerLibrary` come nome file e fare clic su **OK**.
9. Copiare il contenuto da [qui](https://raw.githubusercontent.com/cephalin/active-directory-dotnet-webapp-roleclaims/master/WebApp-RoleClaims-DotNet/Scripts/AadPickerLibrary.js) in ~\Scripts\AadPickerLibrary.js.
   
   Nello script l'oggetto `AadPicker` chiama l' [API Graph di Azure Active Directory](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog) per cercare gli utenti e i gruppi corrispondenti all'input.  
10. Anche ~\Scripts\AadPickerLibrary.js usa il [widget di completamento automatico dell'interfaccia utente jQuery](https://jqueryui.com/autocomplete/). È quindi necessario aggiungere l'interfaccia utente di jQuery al progetto. Fare clic con il pulsante destro del mouse sul progetto e scegliere **Gestisci pacchetti NuGet**.
11. In Gestione pacchetti NuGet fare clic su Sfoglia, digitare **jquery-ui** nella barra di ricerca e fare clic su **jQuery.UI.Combined**.
    
    ![](./media/web-sites-dotnet-lob-application-azure-ad/17-add-jquery-ui-nuget.png)
12. Nel riquadro di destra fare clic su **Installa**, quindi fare clic su **OK** per continuare.
13. Aprire ~\App_Start\BundleConfig.cs e apportare le modifiche evidenziate di seguito:  
    
    <pre class="prettyprint">
    public static void RegisterBundles(BundleCollection bundles)
    {
        bundles.Add(new ScriptBundle(&quot;~/bundles/jquery&quot;).Include(
                    &quot;~/Scripts/jquery-{version}.js&quot;<mark>,
                    &quot;~/Scripts/jquery-ui-{version}.js&quot;,
                    &quot;~/Scripts/AadPickerLibrary.js&quot;</mark>));
    
        bundles.Add(new ScriptBundle(&quot;~/bundles/jqueryval&quot;).Include(
                    &quot;~/Scripts/jquery.validate*&quot;));
    
        // Use the development version of Modernizr to develop with and learn from. Then, when you&#39;re
        // ready for production, use the build tool at http://modernizr.com to pick only the tests you need.
        bundles.Add(new ScriptBundle(&quot;~/bundles/modernizr&quot;).Include(
                    &quot;~/Scripts/modernizr-*&quot;));
    
        bundles.Add(new ScriptBundle(&quot;~/bundles/bootstrap&quot;).Include(
                    &quot;~/Scripts/bootstrap.js&quot;,
                    &quot;~/Scripts/respond.js&quot;));
    
        bundles.Add(new StyleBundle(&quot;~/Content/css&quot;).Include(
                    &quot;~/Content/bootstrap.css&quot;,
                    &quot;~/Content/site.css&quot;<mark>,
                    &quot;~/Content/themes/base/jquery-ui.css&quot;</mark>));
    }
    </pre>
    
    Sono disponibili altri modi più efficienti per gestire i file CSS e JavaScript nell'app. Tuttavia, per semplicità, sarà sufficiente occuparsi dei bundle caricati con ogni visualizzazione.
14. In ~\Global.asax aggiungere infine la riga di codice seguente al metodo `Application_Start()`. `Ctrl`+`.` su ogni errore di risoluzione dei nomi per correggerlo.
    
        AntiForgeryConfig.UniqueClaimTypeIdentifier = ClaimTypes.NameIdentifier;
    
    > [!NOTE]
    > Questa riga di codice è necessaria perché il modello MVC predefinito usa <code>[ValidateAntiForgeryToken]</code> in alcune azioni. A causa del comportamento descritto da [Brock Allen](https://twitter.com/BrockLAllen) nella pagina [MVC 4, AntiForgeryToken and Claims](http://brockallen.com/2012/07/08/mvc-4-antiforgerytoken-and-claims/) (MVC 4, AntiForgeryToken e attestazioni), è possibile che la convalida del token antifalsificazione con HTTP POST non riesca per i motivi seguenti:
    > 
    > * Azure Active Directory non invia http://schemas.microsoft.com/accesscontrolservice/2010/07/claims/identityprovider, richiesto per impostazione predefinita dal token antifalsificazione.
    > * Se Azure Active Directory prevede la sincronizzazione della directory con AD FS, l'attendibilità AD FS non invia per impostazione predefinita l'attestazione http://schemas.microsoft.com/accesscontrolservice/2010/07/claims/identityprovider, anche se è possibile configurare manualmente AD FS per l'invio di questa attestazione.
    > 
    > `ClaimTypes.NameIdentifies` specifica l'attestazione `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier`, che non viene fornita da Azure Active Directory.  
    > 
    > 
15. A questo punto, pubblicare le modifiche. Fare clic con il pulsante destro del mouse sul progetto e scegliere **Pubblica**.
16. Fare clic su **Impostazioni**, assicurarsi che sia disponibile una stringa di connessione al database SQL, selezionare **Aggiorna database** per apportare le modifiche allo schema per il modello e fare clic su **Pubblica**.
    
    ![](./media/web-sites-dotnet-lob-application-azure-ad/18-publish-crud-changes.png)
17. Nel browser passare a https://&lt;*nomeapp*>.azurewebsites.net/workitems e fare clic su **Crea nuovo**.
18. Fare clic sulla casella **AssignedToName** . Gli utenti e i gruppi del tenant di Azure Active Directory verranno visualizzati in un elenco a discesa. È possibile digitare per filtrare o usare la chiave `Up` o `Down` oppure fare clic per selezionare l'utente o il gruppo. 
    
    ![](./media/web-sites-dotnet-lob-application-azure-ad/19-use-aadpicker.png)
19. Fare clic su **Salva** per salvare le modifiche. Quindi, fare clic su **Modifica** nell'elemento di lavoro creato per osservare lo stesso comportamento.

A questo punto è in esecuzione un'app line-of-business in Azure con accesso alla directory. Con l'API Graph è possibile fare molto di più. Vedere [Azure AD Graph API reference](https://msdn.microsoft.com/library/azure/ad/graph/api/api-catalog)(Informazioni di riferimento sull'API Graph di Azure AD).

<a name="next"></a>

## <a name="next-step"></a>passaggio successivo
Se è necessario il controllo degli accessi in base al ruolo per l'app line-of-business in Azure, vedere [WebApp-RoleClaims-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-roleclaims) per un esempio del team di Azure Active Directory. Viene illustrato come abilitare i ruoli per un'applicazione Azure Active Directory e quindi autorizzare gli utenti con `[Authorize]` .

Se l'app line-of-business deve accedere a dati locali, vedere [Accedere alle risorse locali usando connessioni ibride nel servizio app di Azure](web-sites-hybrid-connection-get-started.md).

<a name="bkmk_resources"></a>

## <a name="further-resources"></a>Altre risorse
* [Autenticazione e autorizzazione nel servizio app di Azure](../app-service/app-service-authentication-overview.md)
* [Eseguire l'autenticazione con l'istanza locale di Active Directory nell'app Azure](web-sites-authentication-authorization.md)
* [Creare un'app line-of-business in Azure con l'autenticazione di AD FS](web-sites-dotnet-lob-application-adfs.md)
* [App Service Auth and the Azure AD Graph API (Autenticazione del servizio app e API Graph di Azure AD)](https://cgillum.tech/2016/03/25/app-service-auth-aad-graph-api/)
* [Esempi e documentazione su Microsoft Azure Active Directory](https://github.com/AzureADSamples)
* [Token e tipi di attestazioni supportati di Azure Active Directory](http://msdn.microsoft.com/library/azure/dn195587.aspx)

