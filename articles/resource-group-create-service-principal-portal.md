<properties
   pageTitle="Creare un'applicazione e un'entità servizio di Active Directory nel portale | Microsoft Azure"
   description="Descrive come creare una nuova applicazione ed entità servizio di Active Directory da usare con il controllo degli accessi in base al ruolo in Gestione risorse di Azure per gestire l'accesso alle risorse."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="wpickett"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="01/20/2016"
   ms.author="tomfitz"/>

# Creare un'applicazione e un'entità servizio di Active Directory tramite il portale

## Panoramica
Quando si ha un processo automatico o un'applicazione che deve accedere alle risorse o modificarle, è possibile usare il portale classico per creare un'applicazione Active Directory. Quando si crea un'applicazione di Active Directory tramite il portale classico, vengono create sia l'applicazione sia un'entità servizio. È possibile eseguire l'applicazione nella propria identità o con l'identità dell'utente connesso dell'applicazione. Questi due metodi di autenticazione delle applicazioni vengono definiti come interattivo (l'utente esegue l'accesso) e non interattivo (l'app fornisce le proprie credenziali). In modalità non interattiva è necessario assegnare l'entità servizio a un ruolo con autorizzazioni corrette.

Questo argomento spiega come creare una nuova applicazione e un'entità servizio usando il portale classico. Attualmente, è necessario usare il portale classico per creare una nuova applicazione di Active Directory. Questa funzionalità verrà aggiunta al portale di Azure in una versione successiva. È possibile usare il portale per assegnare l'applicazione a un ruolo. È inoltre possibile eseguire questi passaggi tramite Azure PowerShell o l’Interfaccia della riga di comando di Azure. Per altre informazioni sull'uso di PowerShell o dell'interfaccia della riga di comando con l'entità servizio, vedere [Autenticazione di un'entità servizio con Gestione risorse di Azure](resource-group-authenticate-service-principal.md).

## Concetti
1. Azure Active Directory (AAD): servizio di gestione delle identità e degli accessi pensato per il cloud. Per altre informazioni, vedere [Informazioni su Azure Active Directory](active-directory/active-directory-whatis.md)
2. Entità servizio: istanza di un'applicazione in una directory.
3. Applicazione AD: record di directory in AAD che identifica un'applicazione in AAD. 

Per una spiegazione più dettagliata delle applicazioni e delle entità servizio, vedere [Oggetti applicazione e Oggetti entità servizio](active-directory/active-directory-application-objects.md). Per altre informazioni sull'autenticazione in Active Directory, vedere [Scenari di autenticazione per Azure AD](active-directory/active-directory-authentication-scenarios.md).


## Creare l'applicazione

Per le applicazioni interattive e non interattiva, è necessario creare e configurare l'applicazione Active Directory.

1. Accedere all'account di Azure tramite il [portale classico](https://manage.windowsazure.com/).

2. Selezionare **Active Directory** dal riquadro di sinistra.

     ![selezionare Active Directory][1]

3. Selezionare la directory da usare per la creazione della nuova applicazione.

     ![scegliere la directory][2]

3. Per visualizzare le applicazioni nella directory, fare clic su **Applicazioni**.

     ![visualizzare le applicazioni][11]

4. Se non è stata creata un'applicazione in questa directory, dovrebbe essere visualizzata un'immagine simile a questa riportata di seguito. Fare clic su **AGGIUNGI APPLICAZIONE**

     ![aggiungere un'applicazione][6]

     In alternativa, fare clic su **Aggiungi** nel riquadro inferiore.

     ![aggiungere][12]

5. Selezionare il tipo di applicazione da creare. Per questa esercitazione non verrà usata un'applicazione della raccolta.

     ![nuova applicazione][10]

6. Immettere il nome dell'applicazione e selezionare il tipo di applicazione da usare. Selezionare il tipo di applicazione da creare. Per questa esercitazione, si sceglierà di creare un'**APPLICAZIONE WEB APPLICATION E/O API WEB** e fare clic sul pulsante Avanti.

     ![assegnare un nome all'applicazione][9]

7. Compilare le proprietà per l'app. Per **URL ACCESSO** specificare l'URI a un sito Web che descrive l'applicazione. L'esistenza del sito Web non viene convalidata. Per **URI ID APP** specificare l'URI che identifica l'applicazione. L'univocità o l'esistenza dell'endpoint non viene convalidata. Se è stata selezionata **Applicazione client nativa** per il tipo di applicazione, si dovrà fornire un valore per **URI di reindirizzamento**. Fare clic su **Completa** per creare l'applicazione AAD.

     ![proprietà dell'applicazione][4]

L'applicazione è stata creata.

## Ottenere l'ID client e l'ID tenant

Quando si accede a livello di codice all'applicazione, è necessario l'ID dell'applicazione. Selezionare la scheda **Configura** e copiare il valore di **ID CLIENT**.
  
   ![id client][5]

In alcuni casi, è necessario passare l'id tenant con la richiesta di autenticazione. È possibile recuperare l'ID tenant selezionando **Visualizza endpoint** nella parte inferiore della schermata e recuperare l'ID come illustrato di seguito.

   ![tenant id](./media/resource-group-create-service-principal-portal/save-tenant.png)

## Crea una chiave di autenticazione

Se l'applicazione verrà eseguita con le proprie credenziali, è necessario creare una chiave per l'applicazione.

1. Fare clic sulla scheda **Configura** per configurare la password dell'applicazione.

     ![configura applicazione][3]

2. Scorrere fino alla sezione **Chiavi** e selezionare la durata della validità della password.

     ![chiavi][7]

3. Selezionare **Salva** per creare la chiave.

     ![salvare][13]

     Viene visualizzata la chiave salvata, che è possibile copiare. Non si sarà in grado di recuperare la chiave in un secondo momento,quindi la si dovrebbe copiare ora.

     ![chiave salvata][8]

A questo punto, l'applicazione è pronta e l'entità servizio è stata creata nel tenant. Quando si accede come entità servizio, assicurarsi di usare:

* **ID CLIENT**: come nome utente.
* **CHIAVE**: come password.

## Impostare autorizzazioni delegate

Se l'applicazione accede alle risorse per conto dell'utente connesso, è necessario concedere all'applicazione l'autorizzazione delegata per accedere alle altre applicazioni. Eseguire questa operazione nella sezione **Autorizzazioni per altre applicazioni** della scheda **Configura**. Per impostazione predefinita, un'autorizzazione delegata è già abilitata per Azure Active Directory. Lasciare questa autorizzazione delegata invariata.

1. Selezionare **Aggiungi applicazione**.

2. Nell'elenco selezionare il **API di gestione del servizio Microsoft Azure**.

      ![seleziona app](./media/resource-group-create-service-principal-portal/select-app.png)

3. Aggiungere l'autorizzazione delegata **Accedi a gestione del servizio Azure (anteprima)** all'API di gestione del servizio.

       ![seleziona autorizzazione](./media/resource-group-create-service-principal-portal/select-permissions.png)

4. Salvare la modifica.

## Configurare applicazioni multi-tenant

Se gli utenti da altre directory di Azure Active Directory possono fornire il consenso all'applicazione ed eseguire l'accesso, è necessario abilitare multi-tenancy. Nella scheda **Configura** impostare **L'applicazione è multi-tenant** su **Sì**.

![multi-tenant](./media/resource-group-create-service-principal-portal/multi-tenant.png)

## Assegnare l'applicazione al ruolo

Se l'applicazione non è in esecuzione con l'identità dell'utente connesso, è necessario assegnare l'applicazione a un ruolo per concedere le autorizzazioni per l'esecuzione di azioni. Per assegnare l'applicazione a un ruolo, passare dal portale classico al [portale di Azure](https://portal.azure.com). È necessario decidere quale ruolo aggiungere all'applicazione e indicare l'ambito del ruolo. Per informazioni sui ruoli disponibili, vedere [RBAC: Ruoli predefiniti](./active-directory/role-based-access-built-in-roles.md). È possibile impostare l'ambito al livello della sottoscrizione, del gruppo di risorse o della risorsa. Le autorizzazioni vengono ereditate a livelli inferiori dell'ambito. Se ad esempio si aggiunge un'applicazione al ruolo Lettore per un gruppo di risorse, l'applicazione è in grado di leggere il gruppo di risorse e le risorse in esso contenute.

1. Nel portale passare al livello dell'ambito al quale si vuole assegnare l'applicazione. Per questo argomento è possibile passare a un gruppo di risorse e nel relativo pannello selezionare l'icona **Accesso**.

     ![selezionare gli utenti](./media/resource-group-create-service-principal-portal/select-users.png)

2. Selezionare **Aggiungi**.

     ![selezionare aggiungi](./media/resource-group-create-service-principal-portal/select-add.png)

3. Selezionare il ruolo **Lettore** o il ruolo al quale si vuole assegnare l'applicazione.

     ![selezionare il ruolo](./media/resource-group-create-service-principal-portal/select-role.png)

4. Quando viene visualizzato per la prima volta l'elenco degli utenti che è possibile aggiungere al ruolo, le applicazioni non verranno visualizzate. Verranno visualizzati solo il gruppo e gli utenti.

     ![mostrare gli utenti](./media/resource-group-create-service-principal-portal/show-users.png)

5. Per individuare l'applicazione, è necessario cercarla. Digitando il nome dell'applicazione, l'elenco delle opzioni disponibili cambierà. Quando l'applicazione viene visualizzata nell'elenco, selezionarla.

     ![assegnare al ruolo](./media/resource-group-create-service-principal-portal/assign-to-role.png)

6. Selezionare **OK** per completare l'assegnazione al ruolo. L'applicazione dovrebbe essere ora visualizzata nell'elenco degli utenti assegnati a un ruolo per il gruppo di risorse.

     ![mostrare](./media/resource-group-create-service-principal-portal/show-app.png)

Per ulteriori informazioni sull'assegnazione di utenti e applicazioni a ruoli tramite il portale, vedere [Gestire gli accessi tramite il portale di gestione di Azure](../role-based-access-control-configure/#manage-access-using-the-azure-management-portal).

## Ottenere token di accesso nel codice

Se si usa .NET, è possibile recuperare il token di accesso per l'applicazione con il codice seguente.

È prima necessario installare la libreria di autenticazione di Active Directory nel progetto di Visual Studio. Il modo più semplice per eseguire questa operazione è usare il pacchetto NuGet. Aprire la Console di Gestione pacchetti e digitare i comandi seguenti.

    PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.19.208020213
    PM> Update-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Safe

Per accedere con l'ID e il segreto dell'applicazione, usare il metodo seguente per recuperare il token.

    public static string GetAccessToken()
    {
        var authenticationContext = new AuthenticationContext("https://login.windows.net/{tenantId or tenant name}");  
        var credential = new ClientCredential(clientId: "{application id}", clientSecret: "{application password}");
        var result = authenticationContext.AcquireToken(resource: "https://management.core.windows.net/", clientCredential:credential);

        if (result == null) {
            throw new InvalidOperationException("Failed to obtain the JWT token");
        }

        string token = result.AccessToken;

        return token;
    }

Per accedere per conto dell'utente, usare il metodo seguente per recuperare il token.

    public static string GetAcessToken()
    {
        var authenticationContext = new AuthenticationContext("https://login.windows.net/{tenant id}");
        var result = authenticationContext.AcquireToken(resource: "https://management.core.windows.net/", {application id}, new Uri({redirect uri});

        if (result == null) {
            throw new InvalidOperationException("Failed to obtain the JWT token");
        }

        string token = result.AccessToken;

        return token;
    }

È possibile passare il token nell'intestazione della richiesta con il codice seguente:

    string token = GetAcessToken();
    request.Headers.Add(HttpRequestHeader.Authorization, "Bearer " + token);


## Passaggi successivi

- Per informazioni su come specificare i criteri di sicurezza, vedere [Controllo degli accessi in base al ruolo](./active-directory/role-based-access-control-configure.md).  
- Per una dimostrazione video di questi passaggi, vedere l'articolo relativo all'[abilitazione della gestione a livello di codice di una risorsa di Azure con Azure Active Directory](https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Enabling-Programmatic-Management-of-an-Azure-Resource-with-Azure-Active-Directory).
- Per altre informazioni sull'uso di Azure PowerShell o dell'interfaccia della riga di comando di Azure con applicazioni ed entità servizio di Active Directory, incluso l'uso di un certificato per l'autenticazione, vedere [Autenticazione di un'entità servizio con Gestione risorse di Azure](./resource-group-authenticate-service-principal.md).
- Per indicazioni su come implementare la sicurezza con Gestione risorse di Azure, vedere [Considerazioni sulla sicurezza per Gestione risorse di Azure](best-practices-resource-manager-security.md).


<!-- Images. -->
[1]: ./media/resource-group-create-service-principal-portal/active-directory.png
[2]: ./media/resource-group-create-service-principal-portal/active-directory-details.png
[3]: ./media/resource-group-create-service-principal-portal/application-configure.png
[4]: ./media/resource-group-create-service-principal-portal/app-properties.png
[5]: ./media/resource-group-create-service-principal-portal/client-id.png
[6]: ./media/resource-group-create-service-principal-portal/create-application.png
[7]: ./media/resource-group-create-service-principal-portal/create-key.png
[8]: ./media/resource-group-create-service-principal-portal/save-key.png
[9]: ./media/resource-group-create-service-principal-portal/tell-us-about-your-application.png
[10]: ./media/resource-group-create-service-principal-portal/what-do-you-want-to-do.png
[11]: ./media/resource-group-create-service-principal-portal/view-applications.png
[12]: ./media/resource-group-create-service-principal-portal/add-icon.png
[13]: ./media/resource-group-create-service-principal-portal/save-icon.png

<!---HONumber=AcomDC_0128_2016-->