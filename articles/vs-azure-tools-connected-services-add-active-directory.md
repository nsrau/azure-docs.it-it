<properties 
   pageTitle="Aggiunta di Azure Active Directory mediante servizi connessi in Visual Studio | Microsoft Azure"
   description="Aggiungere un'istanza di Azure Active Directory usando la finestra di dialogo Aggiungi Servizi connessi di Visual Studio"
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags 
   ms.service="active-directory"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="05/08/2016"
   ms.author="tarcher" />

# Aggiunta di Azure Active Directory mediante servizi connessi in Visual Studio 

##Panoramica
Tramite Azure Active Directory (Azure AD), è possibile supportare Single Sign-On (SSO) per le applicazioni web ASP.NET MVC o l'autenticazione di Active Directory nei servizi Web API. Con l'autenticazione AD Azure, gli utenti possono usare gli account da Azure AD per connettersi alle applicazioni web. I vantaggi dell'autenticazione AD Azure con l'API Web comprendono la protezione avanzata dei dati quando si espone un'API da un'applicazione web. Con Azure AD, non è necessario gestire un sistema di autenticazione distinto con la gestione di account e utente.

## Tipi di progetto supportati

Per connettersi ad Azure AD nei seguenti tipi di progetto, è possibile usare la finestra di dialogo dei servizi connessi.

- Progetti ASP.NET MVC

- Progetti ASP.NET Web API


### Connettersi a Azure AD usando la finestra di dialogo dei servizi connessi

1. È necessario disporre di un account Azure. Se non si ha un account Azure, è possibile iscriversi per ottenere una [versione di prova gratuita](http://go.microsoft.com/fwlink/?LinkId=518146).

1. In Visual Studio, aprire il menu di scelta rapida di **riferimenti** nodo del progetto e scegliere **Aggiungi servizi connessi**.
1. Selezionare **Autenticazione AD Azure** e poi scegliere **Configurazione**.

    ![Scegliere Aggiungere l'Autenticazione di Azure AD](./media/vs-azure-tools-connected-services-add-active-directory/connected-services-add-active-directory.png)

1. Nella prima pagina della **Configurazione dell’Autenticazione AD Azure**, controllare **Configurazione Single Sign-on con Azure AD**.

    Se il progetto viene configurato con un'altra configurazione per l'autenticazione, la procedura guidata avvisa l'utente che continuando verrà disattivata la configurazione precedente.

    ![Configurare Azure AD nella procedura guidata](./media/vs-azure-tools-connected-services-add-active-directory/configure-azure-ad-wizard-1.png)

1.  Nella seconda pagina, selezionare un dominio dall’elenco a discesa **Dominio**. L'elenco di domini contiene tutti i domini accessibili dagli account elencati nella finestra di dialogo Impostazioni Account. In alternativa, è possibile immettere un nome di dominio se non è possibile trovare quello che si sta cercando, ad esempio mydomain.onmicrosoft.com. È possibile scegliere l'opzione per creare una nuova app Azure AD o usare le impostazioni da un'app di Azure AD esistente.

    ![Configurare Azure AD nella procedura guidata](./media/vs-azure-tools-connected-services-add-active-directory/configure-azure-ad-wizard-2.png)


1. Nella terza pagina della procedura guidata, assicurarsi che **Lettura dati directory** sia selezionata. La procedura guidata compilerà il **Client Secret**.

    ![Configurare Azure AD nella procedura guidata](./media/vs-azure-tools-connected-services-add-active-directory/configure-azure-ad-wizard-3.png)

1. Fare clic sul pulsante **Finish**. La finestra di dialogo consente di aggiungere il codice di configurazione necessario e i riferimenti per abilitare il progetto per l'autenticazione di Azure AD. È possibile visualizzare il dominio di Active Directory nel [portale di Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Esaminare la pagina introduttiva visualizzata nel browser per ottenere informazioni sui passaggi successivi e la pagina relativa ai risultati per verificare il modo in cui è stato modificato il progetto. Per controllare che tutte le operazioni siano state eseguite correttamente, aprire uno dei file di configurazione modificati, quindi verificare che le impostazioni indicate nella pagina dei risultati siano presenti. Ad esempio, il Web. config principale in un progetto ASP.NET MVC avrà queste impostazioni aggiunte:

        <appSettings> 
            <add key="ida:ClientId" value="ClientId from the new Azure AD App" />
            <add key="ida:AADInstance" value="https://login.windows.net/" />
            <add key="ida:Domain" value="Your selected domain" />
            <add key="ida:TenantId" value="The Id of your selected Azure AD Tenant" />
            <add key="ida:PostLogoutRedirectUri" value="The default redirect URI from the project" />
        </appSettings>

## Come viene modificato il progetto

Quando si esegue la procedura guidata, Visual Studio aggiunge Azure AD e i riferimenti al progetto associati. Anche i file di configurazione e i file di codice nel progetto vengono modificati per aggiungere supporto per Azure AD. Le modifiche specifiche apportate in Visual Studio dipendono dal tipo di progetto. Per informazioni dettagliate sul modo in cui vengono modificati i progetti MVC ASP.NET, vedere [Risultati - Progetti MVC](http://go.microsoft.com/fwlink/p/?LinkID=513809). Per i progetti Web API, vedere [Cosa è successo: Progetti Web API](http://go.microsoft.com/fwlink/p/?LinkId=513810).

##Passaggi successivi

Porre domande e ottenere assistenza.

 - [Forum MSDN: Azure AD](https://social.msdn.microsoft.com/forums/azure/home?forum=WindowsAzureAD)

 - [Documentazione di Azure AD](https://azure.microsoft.com/documentation/services/active-directory/)

 - [Post di blog: Introduzione a Microsoft Azure](http://blogs.msdn.com/b/brunoterkaly/archive/2014/03/03/introduction-to-windows-azure-active-directory.aspx)

<!---HONumber=AcomDC_0720_2016-->