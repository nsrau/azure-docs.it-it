---
title: Protezione di Rendering remoto di Azure e dello spazio di archiviazione dei modelli
description: Applicare la protezione avanzata a un'applicazione di Rendering remoto per la sicurezza del contenuto
author: florianborn71
ms.author: flborn
ms.date: 06/15/2020
ms.topic: tutorial
ms.custom: devx-track-csharp
ms.openlocfilehash: 07374debf8d660d8f1c32788db3d218da611d539
ms.sourcegitcommit: b4f303f59bb04e3bae0739761a0eb7e974745bb7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/02/2020
ms.locfileid: "91650477"
---
# <a name="tutorial-securing-azure-remote-rendering-and-model-storage"></a>Esercitazione: Protezione di Rendering remoto di Azure e dello spazio di archiviazione dei modelli

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
>
> * Proteggere l'istanza di Archiviazione BLOB di Azure contenente i modelli di Rendering remoto di Azure
> * Eseguire l'autenticazione con Azure AD per accedere all'istanza di Rendering remoto di Azure
> * Usare le credenziali di Azure per l'autenticazione di Rendering remoto di Azure

## <a name="prerequisites"></a>Prerequisiti

* Questa esercitazione si basa sull'argomento [Esercitazione: Perfezionamento di materiali, illuminazione ed effetti](..\materials-lighting-effects\materials-lighting-effects.md).

## <a name="why-additional-security-is-needed"></a>Perché è necessaria una sicurezza aggiuntiva

Lo stato corrente dell'applicazione e il relativo accesso alle risorse di Azure sono come segue:

![Sicurezza iniziale](./media/security-one.png)

"ID account + chiave account" e "URL + token di firma di accesso condiviso" essenzialmente nell'insieme archiviano un nome utente e una password. Se ad esempio i valori di "ID account + chiave account" venissero esposti, un utente malintenzionato potrebbe facilmente usare le risorse di Rendering remoto di Azure senza il consenso dell'utente e a sue spese.

## <a name="securing-your-content-in-azure-blob-storage"></a>Protezione del contenuto in Archiviazione BLOB di Azure

Rendering remoto di Azure può accedere in sicurezza al contenuto di Archiviazione BLOB di Azure con la corretta configurazione. Vedere l'argomento [Procedura: Collegare gli account di archiviazione](../../../how-tos/create-an-account.md#link-storage-accounts) per configurare l'istanza di Rendering remoto di Azure con gli account di archiviazione BLOB.

Quando si usa un account di archiviazione BLOB collegato, si useranno metodi leggermente diversi per il caricamento dei modelli:

```csharp
var loadModelParams = new LoadModelFromSASParams(modelPath, modelEntity);
var loadModelAsync = ARRSessionService.CurrentActiveSession.Actions.LoadModelFromSASAsync(loadModelParams);
```

Le righe sopra riportate usano la versione `FromSAS` dell'azione di parametri e sessione. Devono essere convertite nelle versioni non di firma di accesso condiviso:

```csharp
var loadModelParams = new LoadModelParams(storageAccountPath, blobContainerName, modelPath, modelEntity);
var loadModelAsync = ARRSessionService.CurrentActiveSession.Actions.LoadModelAsync(loadModelParams);
```

Modificare **RemoteRenderingCoordinator** per caricare un modello personalizzato, da un account di archiviazione BLOB collegato.

1. Se non è già stato fatto, completare l'argomento [Procedura: Collegare gli account di archiviazione](../../../how-tos/create-an-account.md#link-storage-accounts) per concedere all'istanza di Rendering remoto di Azure l'autorizzazione per accedere all'istanza di Archiviazione BLOB.
1. Aggiungere il seguente metodo **LoadModel** modificato a **RemoteRenderingCoordinator**, subito sotto il metodo **LoadModel** corrente:

    ```csharp
    /// <summary>
    /// Loads a model from blob storage that has been linked to the ARR instance
    /// </summary>
    /// <param name="storageAccountName">The storage account name, this contains the blob containers </param>
    /// <param name="blobContainerName">The blob container name, i.e. arroutput</param>
    /// <param name="modelPath">The relative path inside the container to the model, i.e. test/MyCustomModel.arrAsset</param>
    /// <param name="parent">The parent Transform for this remote entity</param>
    /// <param name="progress">A call back method that accepts a float progress value [0->1]</param>
    /// <returns></returns>
    public async Task<Entity> LoadModel(string storageAccountName, string blobContainerName, string modelPath, Transform parent = null, ProgressHandler progress = null)
    {
        //Create a root object to parent a loaded model to
        var modelEntity = ARRSessionService.CurrentActiveSession.Actions.CreateEntity();

        //Get the game object representation of this entity
        var modelGameObject = modelEntity.GetOrCreateGameObject(UnityCreationMode.DoNotCreateUnityComponents);

        //Ensure the entity will sync its transform with the server
        var sync = modelGameObject.GetComponent<RemoteEntitySyncObject>();
        sync.SyncEveryFrame = true;

        //Parent the new object under the defined parent
        if (parent != null)
        {
            modelGameObject.transform.SetParent(parent, false);
            modelGameObject.name = parent.name + "_Entity";
        }

    #if UNITY_WSA
        //Anchor the model in the world, prefer anchoring parent if there is one
        if (parent != null)
        {
            parent.gameObject.AddComponent<WorldAnchor>();
        }
        else
        {
            modelGameObject.AddComponent<WorldAnchor>();
        }
    #endif

        //Load a model that will be parented to the entity
        var loadModelParams = new LoadModelParams($"{storageAccountName}.blob.core.windows.net", blobContainerName, modelPath, modelEntity);
        var loadModelAsync = ARRSessionService.CurrentActiveSession.Actions.LoadModelAsync(loadModelParams);
        if (progress != null)
            loadModelAsync.ProgressUpdated += progress;
        var result = await loadModelAsync.AsTask();
        return modelEntity;
    }
    ```

    Per la maggior parte, questo codice è identico al metodo `LoadModel` originale, ma la versione di firma di accesso condiviso delle chiamate al metodo è stata sostituita con le versioni non di firma di accesso condiviso.

    Agli argomenti sono inoltre stati aggiunti gli input `storageAccountName` e `blobContainerName`. Questo nuovo metodo **LoadModel** verrà chiamato da un altro metodo simile al primo metodo **LoadTestModel** creato nella prima esercitazione.

1. Aggiungere il metodo seguente a **RemoteRenderingCoordinator** subito dopo **LoadTestModel**

    ```csharp
    private bool loadingLinkedCustomModel = false;

    [SerializeField]
    private string storageAccountName;
    public string StorageAccountName {
        get => storageAccountName.Trim();
        set => storageAccountName = value;
    }

    [SerializeField]
    private string blobContainerName;
    public string BlobContainerName {
        get => blobContainerName.Trim();
        set => blobContainerName = value;
    }

    [SerializeField]
    private string modelPath;
    public string ModelPath {
        get => modelPath.Trim();
        set => modelPath = value;
    }

    [ContextMenu("Load Linked Custom Model")]
    public async void LoadLinkedCustomModel()
    {
        if (CurrentCoordinatorState != RemoteRenderingState.RuntimeConnected)
        {
            Debug.LogError("Please wait for the runtime to connect before loading the test model. Try again later.");
            return;
        }
        if (loadingLinkedCustomModel)
        {
            Debug.Log("Linked Test model already loading or loaded!");
            return;
        }
        loadingLinkedCustomModel = true;

        // Create a parent object to use for positioning
        GameObject testParent = new GameObject("LinkedCustomModel");
        testParent.transform.position = new Vector3(0f, 0f, 3f);

        await LoadModel(StorageAccountName, BlobContainerName, ModelPath, testParent.transform, (progressValue) => Debug.Log($"Loading Test Model progress: {Math.Round(progressValue * 100, 2)}%"));
    }
    ```

    Questo codice aggiunge tre variabili stringa aggiuntive al componente **RemoteRenderingCoordinator**.
    ![Screenshot che evidenzia il nome dell'account di archiviazione, il nome del contenitore BLOB e il percorso del modello del componente RemoteRenderingCoordinator.](./media/storage-account-linked-model.png)

1. Aggiungere i valori al componente **RemoteRenderingCoordinator**. Dopo aver seguito l'argomento di [avvio rapido per la conversione del modello](../../../quickstarts/convert-model.md), i valori saranno:

    * **Nome dell'account di archiviazione**: il nome dell'account di archiviazione, ovvero il nome univoco globale scelto per l'account di archiviazione. Nell'argomento di avvio rapido era *arrtutorialstorage*, ma il proprio valore sarà diverso.
    * **Nome del contenitore BLOB**: arroutput, ovvero il contenitore di archiviazione BLOB
    * **Percorso del modello**: la combinazione di "outputFolderPath" e "outputAssetFileName" definiti nel file *arrconfig.json*. Nell'argomento di avvio rapido era "outputFolderPath":"converted/robot", "outputAssetFileName": "robot.arrAsset". Il che comporterebbe un valore del percorso del modello "converted/robot/robot.arrAsset", ma il proprio valore sarà diverso.

    >[!TIP]
    > Se si [esegue lo script **Conversion. ps1**](../../../quickstarts/convert-model.md#run-the-conversion), senza l'argomento "-UseContainerSas", verranno restituiti tutti i valori indicati sopra invece del token di firma di accesso condiviso. ![Modello collegato](./media/converted-output.png)
1. Per il momento, rimuovere o disabilitare il GameObject **TestModel** per consentire il caricamento del modello personalizzato.
1. Riprodurre la scena e connettersi a una sessione remota.
1. Fare clic con il pulsante destro del mouse su **RemoteRenderingCoordinator** e scegliere **Load Linked Custom Model** (Carica il modello personalizzato collegato).
    ![Caricare il modello collegato](./media/load-linked-model.png)

Questa procedura ha aumentato la sicurezza dell'applicazione rimuovendo il token di firma di accesso condiviso dall'applicazione locale.

Lo stato corrente dell'applicazione e il relativo accesso alle risorse di Azure sono ora come segue:

![Maggiore sicurezza](./media/security-two.png)

È presente un'altra password, la chiave dell'account, da rimuovere dall'applicazione locale. Questa operazione può essere eseguita con l'autenticazione di Azure Active Directory (AAD).

## <a name="azure-active-directory-azure-ad-authentication"></a>Autenticazione di Azure Active Directory (Azure AD)

Con l'autenticazione di AAD sarà possibile determinare quali utenti o gruppi usano Rendering remoto di Azure in modo più controllato. Rendering remoto di Azure prevede il supporto predefinito per accettare [token di accesso](https://docs.microsoft.com/azure/active-directory/develop/access-tokens) invece dell'uso di una chiave dell'account. I token di accesso possono essere paragonati a una chiave specifica dell'utente, limitata nel tempo, che sblocca solo determinate parti della risorsa specifica per cui è stata richiesta.

Lo script **RemoteRenderingCoordinator** include un delegato denominato **ARRCredentialGetter**, che contiene un metodo che restituisce un oggetto **AzureFrontendAccountInfo**, usato per configurare la gestione della sessione remota. È possibile assegnare un metodo diverso a **ARRCredentialGetter**, consentendo di usare un flusso di accesso di Azure, generando un oggetto **AzureFrontendAccountInfo** che contiene un token di accesso di Azure. Questo token di accesso sarà specifico per l'utente che effettua l'accesso.

1. Seguire l'argomento [Procedura: Configurare l'autenticazione - Autenticazione per le applicazioni distribuite](../../../how-tos/authentication.md#authentication-for-deployed-applications), in particolare seguire le istruzioni riportate nella sezione [Autenticazione degli utenti di Azure AD](https://docs.microsoft.com/azure/spatial-anchors/concepts/authentication?tabs=csharp#azure-ad-user-authentication) nella documentazione di Ancoraggi nello spazio di Azure. Ciò comporta la registrazione di una nuova applicazione di Azure Active Directory e la configurazione dell'accesso all'istanza di Rendering remoto di Azure.
1. Dopo aver configurato la nuova applicazione di AAD, controllare che l'applicazione corrisponda alle immagini seguenti:

    **Applicazione AAD -> Autenticazione** ![Autenticazione dell'app](./media/app-authentication-public.png)

    **Applicazione AAD -> Autorizzazioni delle API** ![API di app](./media/request-api-permissions-step-five.png)

1. Dopo aver configurato l'account di Rendering remoto, verificare che la configurazione corrisponda all'immagine seguente:

    **Rendering remoto di Azure -> Controllo di accesso (IAM)** ![Ruolo di Rendering remoto di Azure](./media/azure-remote-rendering-role-assignment-complete.png)

    >[!NOTE]
    > Un ruolo di *Proprietario* non è sufficiente per gestire le sessioni tramite l'applicazione client. A ogni utente a cui si vuole concedere la possibilità di gestire le sessioni è necessario fornire il ruolo di **client di Rendering remoto**. A ogni utente che dovrà gestire le sessioni e convertire i modelli è necessario fornire il ruolo di **amministratore di Rendering remoto**.

Con le configurazioni lato Azure implementate, è ora necessario modificare il modo in cui il codice si connette al servizio Rendering remoto di Azure. A questo scopo, implementare un'istanza di **BaseARRAuthentication**, che restituirà un nuovo oggetto **AzureFrontendAccountInfo**. In questo caso, le informazioni dell'account verranno configurate con il token di accesso di Azure.

1. Creare un nuovo script denominato **AADAuthentication** e sostituirne il codice con quello seguente:

    ```csharp
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License. See LICENSE in the project root for license information.

    using Microsoft.Azure.RemoteRendering;
    using Microsoft.Identity.Client;
    using System;
    using System.Linq;
    using System.Threading;
    using System.Threading.Tasks;
    using UnityEngine;

    public class AADAuthentication : BaseARRAuthentication
    {
        [SerializeField]
        private string accountDomain;
        public string AccountDomain
        {
            get => accountDomain.Trim();
            set => accountDomain = value;
        }

        [SerializeField]
        private string activeDirectoryApplicationClientID;
        public string ActiveDirectoryApplicationClientID
        {
            get => activeDirectoryApplicationClientID.Trim();
            set => activeDirectoryApplicationClientID = value;
        }

        [SerializeField]
        private string azureTenantID;
        public string AzureTenantID
        {
            get => azureTenantID.Trim();
            set => azureTenantID = value;
        }

        [SerializeField]
        private string azureRemoteRenderingAccountID;
        public string AzureRemoteRenderingAccountID
        {
            get => azureRemoteRenderingAccountID.Trim();
            set => azureRemoteRenderingAccountID = value;
        }

        public override event Action<string> AuthenticationInstructions;

        string authority => "https://login.microsoftonline.com/" + AzureTenantID;

        string redirect_uri = "https://login.microsoftonline.com/common/oauth2/nativeclient";

        string[] scopes => new string[] { "https://sts.mixedreality.azure.com/mixedreality.signin" };

        public void OnEnable()
        {
            RemoteRenderingCoordinator.ARRCredentialGetter = GetAARCredentials;
            this.gameObject.AddComponent<ExecuteOnUnityThread>();
        }

        public async override Task<AzureFrontendAccountInfo> GetAARCredentials()
        {
            var result = await TryLogin();
            if (result != null)
            {
                Debug.Log("Account signin successful " + result.Account.Username);

                var AD_Token = result.AccessToken;

                return await Task.FromResult(new AzureFrontendAccountInfo(AccountDomain, AzureRemoteRenderingAccountID, "", AD_Token, ""));
            }
            else
            {
                Debug.LogError("Error logging in");
            }
            return default;
        }

        private Task DeviceCodeReturned(DeviceCodeResult deviceCodeDetails)
        {
            //Since everything in this task can happen on a different thread, invoke responses on the main Unity thread
            ExecuteOnUnityThread.Enqueue(() =>
            {
                // Display instructions to the user for how to authenticate in the browser
                Debug.Log(deviceCodeDetails.Message);
                AuthenticationInstructions?.Invoke(deviceCodeDetails.Message);
            });

            return Task.FromResult(0);
        }

        public override async Task<AuthenticationResult> TryLogin()
        {
            var clientApplication = PublicClientApplicationBuilder.Create(ActiveDirectoryApplicationClientID).WithAuthority(authority).WithRedirectUri(redirect_uri).Build();
            AuthenticationResult result = null;
            try
            {
                var accounts = await clientApplication.GetAccountsAsync();

                if (accounts.Any())
                {
                    result = await clientApplication.AcquireTokenSilent(scopes, accounts.First()).ExecuteAsync();

                    return result;
                }
                else
                {
                    try
                    {
                        result = await clientApplication.AcquireTokenWithDeviceCode(scopes, DeviceCodeReturned).ExecuteAsync(CancellationToken.None);
                        return result;
                    }
                    catch (MsalUiRequiredException ex)
                    {
                        Debug.LogError("MsalUiRequiredException");
                        Debug.LogException(ex);
                    }
                    catch (MsalServiceException ex)
                    {
                        Debug.LogError("MsalServiceException");
                        Debug.LogException(ex);
                    }
                    catch (MsalClientException ex)
                    {
                        Debug.LogError("MsalClientException");
                        Debug.LogException(ex);
                        // Mitigation: Use interactive authentication
                    }
                    catch (Exception ex)
                    {
                        Debug.LogError("Exception");
                        Debug.LogException(ex);
                    }
                }
            }
            catch (Exception ex)
            {
                Debug.LogError("GetAccountsAsync");
                Debug.LogException(ex);
            }

            return null;
        }
    }
    ```

>[!NOTE]
> Questo codice non è completo e non è pronto per un'applicazione commerciale. Come minimo, ad esempio, si dovrà aggiungere anche la possibilità di disconnettersi. A questo scopo, è possibile usare il metodo `Task RemoveAsync(IAccount account)` fornito dall'applicazione client. Questo codice è destinato esclusivamente all'uso nell'esercitazione, mentre la propria implementazione sarà specifica per l'applicazione.

Il codice prova prima di tutto a ottenere il token in modo invisibile all'utente usando **AquireTokenSilent**. Questa operazione riuscirà se l'utente ha già eseguito l'autenticazione dell'applicazione. Se non riesce, passare a una strategia che coinvolge maggiormente l'utente.

Per questo codice viene usato il [flusso di codice del dispositivo](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-device-code) per ottenere un token di accesso. Questo flusso consente all'utente di accedere al suo account Azure in un computer o in un dispositivo mobile e di ricevere il token risultante nell'applicazione HoloLens.

La parte più importante di questa classe dal punto di vista di Rendering remoto di Azure è questa riga:

```csharp
return await Task.FromResult(new AzureFrontendAccountInfo(AccountDomain, AzureRemoteRenderingAccountID, "", AD_Token, ""));
```

Qui viene creato un nuovo oggetto **AzureFrontendAccountInfo** usando il dominio dell'account, l'ID account e il token di accesso. Questo token viene quindi usato dal servizio Rendering remoto di Azure per eseguire query, creare e partecipare alle sessioni di rendering remoto, purché l'utente sia autorizzato in base alle autorizzazioni basate sul ruolo configurate in precedenza.

Con questa modifica, lo stato corrente dell'applicazione e il relativo accesso alle risorse di Azure sono come segue:

![Sicurezza ancora maggiore](./media/security-three.png)

Poiché le credenziali dell'utente non sono archiviate nel dispositivo (o in questo caso neanche immesse nel dispositivo), il rischio di esposizione è molto basso. A questo punto il dispositivo usa un token di accesso specifico dell'utente e limitato nel tempo per accedere a Rendering remoto di Azure, che usa il controllo di accesso (IAM) per accedere all'account di archiviazione BLOB. Questi due passaggi hanno rimosso completamente le "password" dal codice sorgente e hanno aumentato notevolmente la sicurezza. Tuttavia, non si tratta del livello massimo di sicurezza disponibile. Spostando la gestione di modelli e sessioni in un servizio Web sarà possibile migliorare ulteriormente la sicurezza. Per ulteriori considerazioni sulla sicurezza, vedere il capitolo sulla [preparazione delle applicazioni per la commercializzazione](../commercial-ready/commercial-ready.md).

### <a name="testing-aad-auth"></a>Test dell'autenticazione di AAD

Nell'editor di Unity, se l'autenticazione di AAD è attiva, sarà necessario eseguire l'autenticazione ogni volta che si avvia l'applicazione. Nel dispositivo il passaggio di autenticazione viene eseguito la prima volta ed è richiesto di nuovo solo quando il token scade o viene invalidato.

1. Aggiungere il componente **AADAuthentication** al GameObject **RemoteRenderingCoordinator**.

    ![Componente di autenticazione di AAD](./media/azure-active-directory-auth-component.png)

1. Immettere i valori per ID client e ID tenant. Questi valori sono disponibili nella pagina Panoramica della registrazione dell'app:

    * Il **dominio dell'account** è lo stesso dominio usato in **RemoteRenderingCoordinator**.
    * L'**ID client dell'applicazione di Active Directory** è l'*ID applicazione (client)* trovato nella registrazione dell'app di AAD (vedere l'immagine seguente).
    * L'**ID tenant di Azure** è l'*ID directory (tenant)* trovato nella registrazione dell'app di AAD (vedere l'immagine seguente).
    * L'**ID account di Rendering remoto di Azure** è lo stesso **ID account** usato per **RemoteRenderingCoordinator**.

    ![Screenshot che evidenzia l'ID applicazione (client) e l'ID della directory (tenant).](./media/app-overview-data.png)

1. Premere Play nell'editor di Unity e fornire il consenso per l'esecuzione di una sessione.
    Poiché il componente **AADAuthentication** include un controller di visualizzazione, viene collegato automaticamente per visualizzare una richiesta dopo il pannello modale di autorizzazione della sessione.
1. Seguire le istruzioni disponibili nel pannello a destra di **AppMenu**.
    Dovrebbe essere visualizzata una schermata simile alla seguente: ![Illustrazione che mostra il riquadro delle istruzioni visualizzato a destra di AppMenu.](./media/device-flow-instructions.png)
    Dopo aver immesso il codice fornito nel dispositivo secondario (o nel browser sullo stesso dispositivo) e aver eseguito l'accesso con le credenziali, all'applicazione richiedente, in questo caso l'editor di Unity, verrà restituito un token di accesso.
1. A questo punto, tutti gli elementi dell'applicazione dovrebbero procedere normalmente. Se non si procede attraverso le fasi come previsto, verificare la presenza di eventuali errori nella console di Unity.

## <a name="build-to-device"></a>Compilare nel dispositivo

Se l'applicazione viene compilata con MSAL nel dispositivo, è necessario includere un file nella cartella **Assets** del progetto. In questo modo il compilatore compilerà correttamente l'applicazione usando il componente *Microsoft.Identity.Client.dll* incluso negli **asset dell'esercitazione**.

1. Aggiungere un nuovo file in **Assets** denominato **link.xml**
1. Aggiungere quanto segue per il file:

    ```xml
    <linker>
        <assembly fullname="Microsoft.Identity.Client" preserve="all"/>
        <assembly fullname="System.Runtime.Serialization" preserve="all"/>
        <assembly fullname="System.Core">
            <type fullname="System.Linq.Expressions.Interpreter.LightLambda" preserve="all" />
        </assembly>
    </linker>
    ```

1. Salvare le modifiche

Seguire i passaggi descritti nell'argomento [Avvio rapido: Distribuire l'esempio di Unity in HoloLens - Compilare il progetto di esempio](../../../quickstarts/deploy-to-hololens.md#build-the-sample-project), per compilare in HoloLens.

## <a name="next-steps"></a>Passaggi successivi

La parte restante di questa serie di esercitazioni contiene argomenti concettuali per la creazione di un'applicazione pronta per la produzione che usa Rendering remoto di Azure.

> [!div class="nextstepaction"]
> [Passaggio successivo: Preparazione dell'applicazione per la commercializzazione](../commercial-ready/commercial-ready.md)
