<properties 
   pageTitle="Aggiunta di Azure Active Directory utilizzando servizi connessi in Visual Studio"
   description="Aggiungere un Azure Active Directory utilizzando la finestra di dialogo di Visual Studio Aggiungere servizi connessi"
   services="visual-studio-online"
   documentationCenter="n/a"
   authors="patshea123"
   manager="douge"
   editor="tlee" />
<tags  ms.service="visual-studio-online"" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="na" ms.date="08/12/2015" ms.author="patshea" />

# Aggiunta di Azure Active Directory utilizzando servizi connessi in Visual Studio 

##Panoramica
Tramite Azure Active Directory (Azure AD), è possibile supportare Single Sign-On (SSO) per le applicazioni web ASP.NET MVC o l'autenticazione di Active Directory nei servizi Web API. Con l'autenticazione AD Azure, gli utenti possono utilizzare gli account da Azure AD per connettersi alle applicazioni web. I vantaggi dell'autenticazione AD Azure con l'API Web comprendono la protezione avanzata dei dati quando si espone un'API da un'applicazione web. Con Azure AD, non è necessario gestire un sistema di autenticazione distinto con la gestione di account e utente.

## Pypes di progetto supportati

Per connettersi a Windows Azure nei seguenti tipi di progetto, è possibile utilizzare la finestra di dialogo dei servizi connessi.

- Progetti ASP.NET MVC

- Progetti ASP.NET Web API


### Connettersi a Azure AD utilizzando la finestra di dialogo dei servizi connessi

1. È necessario disporre di un account Azure. Se non si dispone di un account Azure, è possibile iscriversi per un [account gratuito](http://go.microsoft.com/fwlink/?LinkId=518146).

1. In Visual Studio, aprire il menu di scelta rapida di **riferimenti** nodo del progetto e scegliere **Aggiungi servizi connessi**.
1. Selezionare **Autenticazione AD Azure** e poi scegliere **Configura**.

    ![Scegliere Aggiungere l'Autenticazione di Azure AD](./media/vs-azure-tools-connected-services-add-active-directory/connected-services-add-active-directory.png)

1. La prima pagina della **Configurazione dell’Autenticazione AD Azure**, controllare **Configurare Single Sign-on con Azure AD**.

    Se il progetto viene configurato con un'altra configurazione per l'autenticazione, la procedura guidata avvisa l'utente che continuando verrà disattivata la configurazione precedente.

    ![Configurare Azure AD nella procedura guidata](./media/vs-azure-tools-connected-services-add-active-directory/configure-azure-ad-wizard-1.png)

1.  Nella seconda pagina, selezionare un dominio dall’elenco a discesa **Dominio**. L'elenco di domini contiene tutti i domini accessibili dagli account elencati nella finestra di dialogo Impostazioni Account. In alternativa, è possibile immettere un nome di dominio se non è possibile trovare quello che si sta cercando, ad esempio mydomain.onmicrosoft.com. È possibile scegliere l'opzione per creare una nuova app Azure AD o utilizzare le impostazioni da un'app di Azure AD esistente.

    ![Configurare Azure AD nella procedura guidata](./media/vs-azure-tools-connected-services-add-active-directory/configure-azure-ad-wizard-2.png)


1. Nella terza pagina della procedura guidata, assicurarsi che **Lettura dati directory** sia selezionata. La procedura guidata compilerà il **Client Secret**.

    ![Configurare Azure AD nella procedura guidata](./media/vs-azure-tools-connected-services-add-active-directory/configure-azure-ad-wizard-3.png)

1. Fare clic sul pulsante **Finish**. La finestra di dialogo consente di aggiungere il codice di configurazione necessario e i riferimenti per abilitare il progetto per l'autenticazione di Azure AD. È possibile visualizzare il dominio di Active Directory nel portale di Azure.

    ![Trovare il dominio nel Portale di gestione di Azure.](./media/vs-azure-tools-connected-services-add-active-directory/IC765882.png)

1. Esaminare la pagina introduttiva che verrà visualizzata nel browser per idee sui passaggi successivi, e la pagina Cosa è successo per vedere come è stato modificato il progetto. Se si desidera controllare che tutto abbia funzionato, aprire uno dei file di configurazione modificati e verificare che le impostazioni indicate in Cosa è successo siano disponibili. Ad esempio, il Web. config principale in un progetto ASP.NET MVC avrà queste impostazioni aggiunte:

        <appSettings> 
            <add key="ida:ClientId" value="ClientId from the new Azure AD App" />
            <add key="ida:AADInstance" value="https://login.windows.net/" />
            <add key="ida:Domain" value="Your selected domain" />
            <add key="ida:TenantId" value="The Id of your selected Azure AD Tenant" />
            <add key="ida:PostLogoutRedirectUri" value="The default redirect URI from the project" />
        </appSettings>

## Come viene modificato il progetto

Quando si esegue la procedura guidata, Visual Studio aggiunge Azure AD e i riferimenti al progetto associati. Anche i file di configurazione e i file di codice nel progetto vengono modificati per aggiungere supporto per Azure AD. Le modifiche specifiche apportate in Visual Studio dipendono dal tipo di progetto. Per informazioni dettagliate come i progetti MVC ASP.NET vengono modificati, vedere [Cosa è successo - Progetti MVC](http://go.microsoft.com/fwlink/p/?LinkID=513809). Per i progetti Web API, vedere [Cosa è successo: Progetti Web API](http://go.microsoft.com/fwlink/p/?LinkId=513810).

##Passaggi successivi

Porre domande e ottenere assistenza.

 - [Forum MSDN: Azure AD](https://social.msdn.microsoft.com/forums/azure/home?forum=WindowsAzureAD)

 - [Documentazione di Azure AD](http://azure.microsoft.com/documentation/services/active-directory/)

 - [Post di blog: Introduzione a Windows Azure](http://blogs.msdn.com/b/brunoterkaly/archive/2014/03/03/introduction-to-windows-azure-active-directory.aspx)

<!---HONumber=August15_HO8-->