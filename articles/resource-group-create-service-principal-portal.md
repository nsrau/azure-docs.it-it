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
   ms.date="12/01/2015"
   ms.author="tomfitz"/>

# Creare un'applicazione e un'entità servizio di Active Directory tramite il portale

## Panoramica
Quando si dispone di un processo automatico o di un'applicazione che deve accedere a una risorsa nella sottoscrizione o modificarla, è possibile usare il portale per creare un'applicazione di Active Directory e assegnarla a un ruolo con le autorizzazioni corrette. Quando si crea un'applicazione di Active Directory tramite il portale, vengono create sia l'applicazione che un'entità servizio. L'entità servizio viene usata quando si impostano le autorizzazioni.

Questo argomento spiega come creare una nuova applicazione e un'entità servizio usando il portale di Azure. Attualmente, è necessario usare il portale di Microsoft Azure per creare una nuova applicazione di Active Directory. Questa funzionalità verrà aggiunta al portale di anteprima di Azure in una versione successiva. È possibile usare il portale di anteprima per assegnare l'applicazione a un ruolo. È inoltre possibile eseguire questi passaggi tramite Azure PowerShell o l’Interfaccia della riga di comando di Azure. Per altre informazioni, vedere [Autenticazione di un'entità servizio con Gestione risorse di Azure](resource-group-authenticate-service-principal.md).

## Concetti
1. Azure Active Directory (AAD): servizio di gestione delle identità e degli accessi pensato per il cloud. Per altre informazioni, vedere [Informazioni su Azure Active Directory](active-directory/active-directory-whatis.md)
2. Entità servizio: istanza di un'applicazione in una directory.
3. Applicazione AD: record di directory in AAD che identifica un'applicazione in AAD. 

Per una spiegazione più dettagliata delle applicazioni e delle entità servizio, vedere [Oggetti applicazione e Oggetti entità servizio](active-directory/active-directory-application-objects.md). Per altre informazioni sull'autenticazione in Active Directory, vedere [Scenari di autenticazione per Azure AD](active-directory/active-directory-authentication-scenarios.md).


## Creare gli oggetti applicazione ed entità servizio

1. Accedere all'account Azure tramite il [portale](https://manage.windowsazure.com/).

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

6. Immettere il nome dell'applicazione e selezionare il tipo di applicazione da usare. Poiché si intende usare l'entità servizio dell'applicazione per l'autenticazione con Gestione risorse di Azure, creare un'**APPLICAZIONE WEB E/O API WEB** e fare clic sul pulsante Avanti.

     ![assegnare un nome all'applicazione][9]

7. Compilare le proprietà per l'app. Per **URL ACCESSO** specificare l'URI a un sito Web che descrive l'applicazione. L'esistenza del sito Web non viene convalidata. Per **URI ID APP** specificare l'URI che identifica l'applicazione. L'univocità o l'esistenza dell'endpoint non viene convalidata. Fare clic su **Completa** per creare l'applicazione AAD.

     ![proprietà dell'applicazione][4]

## Creare una chiave di autenticazione per l'applicazione
A questo punto, nel portale dovrebbe essere selezionata l'applicazione specificata.

1. Fare clic sulla scheda **Configura** per configurare la password dell'applicazione.

     ![configura applicazione][3]

2. Scorrere fino alla sezione **Chiavi** e selezionare la durata della validità della password.

     ![chiavi][7]

3. Selezionare **Salva** per creare la chiave.

     ![salvare][13]

     Viene visualizzata la chiave salvata, che è possibile copiare. Non si sarà in grado di recuperare la chiave in un secondo momento,quindi la si dovrebbe copiare ora.

     ![chiave salvata][8]

4. Ora è possibile usare la chiave per l'autenticazione come entità servizio. Per l'accesso è necessario l'**ID CLIENT** oltre alla **CHIAVE**. Andare sull'**ID CLIENT** e copiarlo.
  
     ![id client][5]

5. In alcuni casi, è necessario passare l'id tenant con la richiesta di autenticazione. È possibile recuperare l'id tenant selezionando **Visualizzare endpoint** e recuperare l'id come illustrato di seguito.

     ![tenant id](./media/resource-group-create-service-principal-portal/save-tenant.png)

A questo punto, l'applicazione è pronta e l'entità servizio è stata creata nel tenant. Quando si accede come entità servizio, assicurarsi di usare:

* **ID CLIENT**: come nome utente.
* **CHIAVE**: come password.

## Assegnazione di un'applicazione a un ruolo

È necessario assegnare l'applicazione a un ruolo per garantirle le autorizzazioni per l'esecuzione di azioni. È possibile utilizzare il [portale di anteprima](https://portal.azure.com) per assegnare l'applicazione di Active Directory a un ruolo con le autorizzazioni corrette.

Per iniziare a usare il controllo dell’accesso nel portale di anteprima, selezionare l’icona **Accesso**.

![selezionare gli utenti](./media/resource-group-create-service-principal-portal/select-users.png)

Selezionare il ruolo che si desidera assegnare all'applicazione, e cercare l'applicazione.

![selezionare gli utenti](./media/resource-group-create-service-principal-portal/assign-to-role.png)

Per ulteriori informazioni sull'assegnazione di utenti e applicazioni a ruoli tramite il portale, vedere [Gestire gli accessi tramite il portale di gestione di Azure](../role-based-access-control-configure/#manage-access-using-the-azure-management-portal).

## Ottenere token di accesso nel codice

Se si usa .NET, è possibile recuperare il token di accesso per l'applicazione con il codice seguente.

È prima necessario installare la libreria di autenticazione di Active Directory nel progetto di Visual Studio. Il modo più semplice per eseguire questa operazione è usare il pacchetto NuGet. Aprire la Console di Gestione pacchetti e digitare i comandi seguenti.

    PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.19.208020213
    PM> Update-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Safe

Nell'applicazione, aggiungere un metodo simile al seguente per recuperare il token.

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

## Passaggi successivi

- Per informazioni su come specificare i criteri di sicurezza, vedere [Gestione e controllo dell'accesso alle risorse](resource-group-rbac.md).  
- Per una dimostrazione video di questi passaggi, vedere l'articolo relativo all'[abilitazione della gestione a livello di codice di una risorsa di Azure con Azure Active Directory](https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Enabling-Programmatic-Management-of-an-Azure-Resource-with-Azure-Active-Directory).
- Per altre informazioni sull'uso di Azure PowerShell o dell'interfaccia della riga di comando di Azure per lavorare con applicazioni ed entità servizio di Active Directory, oltre che su come usare un certificato per l'autenticazione, vedere [Autenticazione di un'entità servizio con Gestione risorse di Azure](./resource-group-authenticate-service-principal.md).
- Per indicazioni su come implementare la sicurezza con Gestione risorse di Azure, vedere [Considerazioni sulla sicurezza per Gestione risorse di Azure](best-practices-resource-manager-security.md)


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

<!---HONumber=AcomDC_1203_2015-->