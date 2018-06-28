---
title: Esercitazione sulla configurazione di un'applicazione Web di Azure per la lettura di un segreto da un insieme di credenziali delle chiavi | Microsoft Docs
description: Esercitazione Configurare un'applicazione ASP.Net Core per la lettura di un segreto da un insieme di credenziali delle chiavi
services: key-vault
documentationcenter: ''
author: barclayn
manager: mbaldwin
ms.assetid: 0e57f5c7-6f5a-46b7-a18a-043da8ca0d83
ms.service: key-vault
ms.workload: identity
ms.topic: tutorial
ms.date: 05/17/2018
ms.author: barclayn
ms.custom: mvc
ms.openlocfilehash: b82eeb43c29fd52f4df2d453bb24bb2b3bd581ad
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/27/2018
ms.locfileid: "37030516"
---
# <a name="tutorial-configure-an-azure-web-application-to-read-a-secret-from-key-vault"></a>Esercitazione: Configurare un'applicazione Web di Azure per la lettura di un segreto da un insieme di credenziali delle chiavi

In questa esercitazione vengono presentati i passaggi necessari per ottenere un'applicazione Web di Azure per leggere le informazioni dall'insieme di credenziali delle chiavi usando le identità del servizio gestito. Si apprenderà come:

> [!div class="checklist"]
> * Creare un insieme di credenziali delle chiavi.
> * Archiviare un segreto nell'insieme di credenziali delle chiavi.
> * Creare un'applicazione Web di Azure.
> * Abilitare le identità del servizio gestito.
> * Concedere le autorizzazioni necessarie per l'applicazione per la lettura dei dati da un insieme di credenziali delle chiavi.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, in questa esercitazione è necessario eseguire l'interfaccia della riga di comando di Azure versione 2.0.4 o successiva. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure 2.0]( /cli/azure/install-azure-cli).

Per accedere ad Azure tramite l'interfaccia della riga di comando, è possibile digitare:

```azurecli
az login
```

## <a name="create-resource-group"></a>Creare un gruppo di risorse

Creare un gruppo di risorse con il comando [az group create](/cli/azure/group#az_group_create). Un gruppo di risorse di Azure è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite.

L'esempio seguente crea un gruppo di risorse denominato *ContosoResourceGroup* nella posizione *eastus*.

```azurecli
# To list locations: az account list-locations --output table
az group create --name "ContosoResourceGroup" --location "East US"
```

In tutta l'esercitazione viene usato il gruppo di risorse appena creato.

## <a name="create-an-azure-key-vault"></a>Creare un Azure Key Vault

Verrà di seguito creato un insieme di credenziali delle chiavi nel gruppo di risorse creato nel passaggio precedente. Anche se in tutta l'esercitazione viene usato "ContosoKeyVault" come nome per l'insieme di credenziali delle chiavi, è necessario usare un nome univoco. Specificare le informazioni seguenti:

* Nome dell'insieme di credenziali **ContosoKeyVault**.
* Nome del gruppo di risorse **ContosoResourceGroup**.
* Località **Stati Uniti orientali**.

```azurecli
az keyvault create --name "ContosoKeyVault" --resource-group "ContosoResourceGroup" --location "East US"
```

L'output di questo comando mostra le proprietà dell'insieme di credenziali delle chiavi appena creato. Prendere nota delle due proprietà elencate di seguito:

* **Nome dell'insieme di credenziali**: nell'esempio corrisponde a **ContosoKeyVault**. Il nome dell'insieme di credenziali delle chiavi verrà usato per tutti i comandi relativi all'insieme di credenziali delle chiavi.
* **URI dell'insieme di credenziali**: nell'esempio è https://<YourKeyVaultName>.vault.azure.net/. Le applicazioni che usano l'insieme di credenziali tramite l'API REST devono usare questo URI.

>[!IMPORTANT]
> Se viene visualizzato l'errore "Parameter 'vault_name' must conform to the following pattern: '^[a-zA-Z0-9-]{3,24}$'" (Il parametro 'vault_name' deve essere conforme al modello seguente: '^[a-zA-Z0-9-]{3,24}$'), ciò indica che il valore del parametro -name non era univoco o non era conforme al requisito di una stringa composta da 3 a 24 caratteri alfanumerici.

A questo punto, l'account Azure è l'unico autorizzato a eseguire qualsiasi operazione su questo nuovo insieme di credenziali.

## <a name="add-a-secret-to-key-vault"></a>Aggiungere un segreto all'insieme di credenziali delle chiavi

Verrà aggiunto un segreto per illustrare il funzionamento di questa operazione. Si potrebbe archiviare una stringa di connessione SQL o qualsiasi altra informazione che è necessario conservare in modo sicuro, rendendola però disponibile per l'applicazione. In questa esercitazione la password sarà denominata **AppSecret** e archivierà il valore **MySecret**.

Digitare i comandi seguenti per creare un segreto nell'insieme di credenziali delle chiavi chiamato **AppSecret** che archivierà il valore **MySecret**:

```azurecli
az keyvault secret set --vault-name "ContosoKeyVault" --name "AppSecret" --value "MySecret"
```

Per visualizzare il valore contenuto nel segreto come testo normale:

```azurecli
az keyvault secret show --name "AppSecret" --vault-name "ContosoKeyVault"
```

Questo comando mostra le informazioni del segreto, incluso l'URI. Dopo aver completato questi passaggi sarà disponibili un URI per un segreto in Azure Key Vault. Prendere nota di queste informazioni. Saranno necessarie in un passaggio successivo.

## <a name="create-a-web-app"></a>Creare un'app Web

In questa sezione si crea un'applicazione ASP.NET MVC che verrà poi distribuita in Azure come App Web. Per altre informazioni sulle app Web di Azure, vedere [Panoramica delle app Web](../app-service/app-service-web-overview.md).

1. In Visual Studio creare un progetto selezionando **File > Nuovo > Progetto**. 

2. Nella finestra di dialogo **Nuovo progetto** selezionare **Visual C# > Web > Applicazione Web ASP.NET Core**.

3. Assegnare all'applicazione il nome **WebKeyVault** e quindi selezionare **OK**.
   >[!IMPORTANT]
   > È necessario assegnare il nome WebKeyVault all'app, in modo che il codice copiato e incollato corrisponda allo spazio dei nomi. Se il sito ha un nome diverso, sarà necessario modificare il codice in modo che corrisponda al nome del sito.

    ![Finestra di dialogo Nuovo progetto ASP.NET](media/tutorial-web-application-keyvault/aspnet-dialog.png)

4. È possibile distribuire qualsiasi tipo di app Web ASP.NET Core in Azure. Per questa esercitazione, selezionare il modello **Applicazione Web** e verificare che l'autenticazione sia impostata su **Nessuna autenticazione**.

    ![Finestra di dialogo Nessuna autenticazione per ASP.NET](media/tutorial-web-application-keyvault/aspnet-noauth.png)

5. Selezionare **OK**.

6. Dopo che è stato creato il progetto ASP.NET Core, viene visualizzata la home page di ASP.NET Core con vari collegamenti alle risorse con informazioni utili per iniziare.

7. Nel menu selezionare **Debug > Avvia senza eseguire debug** per eseguire l'app Web in locale.

## <a name="modify-the-web-app"></a>Modificare l'app Web

Per l'applicazione Web devono essere installati due pacchetti NuGet. Per installarli seguire questa procedura:

1. In Esplora soluzioni fare clic con il pulsante destro del mouse sul nome del sito Web.
2. Scegliere **Gestisci pacchetti NuGet per la soluzione**.
3. Selezionare la casella di controllo accanto alla casella di ricerca. **Includi versione preliminare**
4. Cercare i due pacchetti NuGet elencati di seguito e confermarne l'aggiunta alla soluzione:

    * [Microsoft.Azure.Services.AppAuthentication (anteprima)](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) - Semplifica il recupero dei token di accesso per gli scenari di autenticazione da servizio a servizio di Azure. 
    * [Microsoft.Azure.KeyVault](https://www.nuget.org/packages/Microsoft.Azure.KeyVault/2.4.0-preview) - Contiene i metodi per interagire con Key Vault.

5. Usare Esplora soluzioni per aprire `Program.cs` e sostituire il contenuto del file Program.cs con il codice seguente. Sostituire ```<YourKeyVaultName>``` con il nome dell'insieme di credenziali delle chiavi:

    ```csharp
    
    using Microsoft.AspNetCore;
    using Microsoft.AspNetCore.Hosting;
    using Microsoft.Azure.KeyVault;
    using Microsoft.Azure.Services.AppAuthentication;
    using Microsoft.Extensions.Configuration;
    using Microsoft.Extensions.Configuration.AzureKeyVault;
    
        namespace WebKeyVault
        {
        public class Program
        {
        public static void Main(string[] args)
        {
        BuildWebHost(args).Run();
        }
    
            public static IWebHost BuildWebHost(string[] args) =>
            WebHost.CreateDefaultBuilder(args)
                .ConfigureAppConfiguration((ctx, builder) =>
                {
                    var keyVaultEndpoint = GetKeyVaultEndpoint();
                    if (!string.IsNullOrEmpty(keyVaultEndpoint))
                    {
                        var azureServiceTokenProvider = new AzureServiceTokenProvider();
                        var keyVaultClient = new KeyVaultClient(
                            new KeyVaultClient.AuthenticationCallback(
                                azureServiceTokenProvider.KeyVaultTokenCallback));
                        builder.AddAzureKeyVault(
                            keyVaultEndpoint, keyVaultClient, new DefaultKeyVaultSecretManager());
                    }
                }
             )
                .UseStartup<Startup>()
                .Build();
    
            private static string GetKeyVaultEndpoint() => "https://<YourKeyVaultName>.vault.azure.net";
        }
        }
    ```

6. Usare Esplora soluzioni per passare alla sezione **Pagine** e aprire `About.cshtml`. Sostituire il contenuto di **About.cshtml.cs** con il codice seguente:

    ```csharp
    
    using Microsoft.AspNetCore.Mvc.RazorPages;
    using Microsoft.Extensions.Configuration;
    
    namespace WebKeyVault.Pages
    {
        public class AboutModel : PageModel
        {
            public AboutModel(IConfiguration configuration)
            {
                _configuration = configuration;
            }
    
            private readonly IConfiguration _configuration = null;
            public string Message { get; set; }
    
            public void OnGet()
            {
                Message = "My key val = " +  _configuration["AppSecret"];
            }
        }
    }
    
    ```

7. Dal menu principale scegliere **Debug** > **Avvia senza eseguire debug**. Quando viene visualizzato il browser, passare alla pagina **About**. Viene visualizzato il valore per AppSecret.

>[!IMPORTANT]
> Se viene visualizzato un messaggio di errore HTTP 502.5 - Errore del processo, verificare il nome dell'insieme di credenziali delle chiavi specificato in `Program.cs`

## <a name="publish-the-web-application-to-azure"></a>Pubblicare l'applicazione Web in Azure

1. Sopra l'editor selezionare **WebKeyVault**.
2. Selezionare **Publish** (Pubblica), quindi **Start** (Inizia).
3. Creare un nuovo **servizio app** e selezionare **Publish**.
4. Selezionare **Create**.

>[!IMPORTANT]
> Verrà visualizzata una finestra del browser con un messaggio di errore 502.5 - Errore del processo. Si tratta di un comportamento previsto. Sarà necessario concedere i diritti di identità di applicazione per leggere i segreti dall'insieme di credenziali delle chiavi.

## <a name="enable-managed-service-identity"></a>Abilitare l'identità del servizio gestito

Azure Key Vault consente di archiviare in modo sicuro le credenziali e altre chiavi e segreti, ma è necessario autenticare il codice in Key Vault per recuperarle. Identità del servizio gestito consente di risolvere il problema in maniera più semplice, assegnando ai servizi di Azure un'identità gestita automaticamente in Azure Active Directory, ovvero Azure AD. È possibile usare questa identità per l'autenticazione a qualsiasi servizio che supporti l'autenticazione di Azure AD, incluso Key Vault, senza inserire le credenziali nel codice.

1. Tornare all'interfaccia della riga di comando di Azure
2. Eseguire il comando assign-identity per creare l'identità per l'applicazione:

```azurecli
az webapp identity assign --name "WebKeyVault" --resource-group "ContosoResourcegroup"
```

>[!NOTE]
>Questo comando equivale a passare al portale e impostare **Identità del servizio gestito** su **Attivato** nelle proprietà dell'applicazione Web.

## <a name="grant-rights-to-the-application-identity"></a>Concedere i diritti all'identità di applicazione

Usando il portale di Azure passare ai criteri di accesso dell'insieme di credenziali delle chiavi e concedere a se stessi l'accesso Gestione dei segreti all'insieme di credenziali delle chiavi. In questo modo sarà possibile eseguire l'applicazione nel computer di sviluppo locale.

1. Cercare l'insieme di credenziali delle chiavi nella finestra di dialogo **Cerca risorse** nel portale di Azure.
2. Selezionare **Criteri di accesso**.
3. Selezionare **Aggiungi nuovo**, quindi nella sezione **Autorizzazioni segrete** selezionare **Recupera** ed **Elenca**.
4. Selezionare **Selezionare un'entità** e aggiungere l'identità di applicazione. Avrà lo stesso nome dell'applicazione.
5. Scegliere **OK**.

L'account in Azure e l'identità di applicazione hanno ora i diritti per la lettura delle informazioni dall'insieme di credenziali delle chiavi. Quando si aggiorna la pagina dovrebbe essere visualizzata la pagina di destinazione del sito. Se si seleziona **Informazioni**, viene visualizzato il valore archiviato nell'insieme di credenziali delle chiavi.

## <a name="clean-up-resources"></a>Pulire le risorse

Per eliminare un gruppo di risorse e tutte le relative risorse, usare il comando **az group delete**.

  ```azurecli
  az group delete -n "ContosoResourceGroup"
  ```

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Guida per gli sviluppatori per Azure Key Vault](key-vault-developers-guide.md)
