---
title: API front-end di Azure per l'autenticazione
description: Spiega come usare l'API front-end C# per l'autenticazione
author: florianborn71
ms.author: flborn
ms.date: 02/12/2010
ms.topic: how-to
ms.openlocfilehash: 04296a3dab61fdb569126abc1bc1f975d69e226d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80681350"
---
# <a name="use-the-azure-frontend-apis-for-authentication"></a>Usare le API front-end di Azure per l'autenticazione

In questa sezione verrà descritto come usare l'API C# per l'autenticazione.

## <a name="azurefrontendaccountinfo"></a>AzureFrontendAccountInfo

AzureFrontendAccountInfo viene utilizzato per configurare le informazioni di autenticazione per un' ```AzureFrontend``` istanza di nell'SDK.

I campi importanti sono:

```cs

    public class AzureFrontendAccountInfo
    {
        // Something akin to "<region>.mixedreality.azure.com"
        public string AccountDomain;

        // Can use one of:
        // 1) ID and Key.
        // 2) AuthenticationToken.
        // 3) AccessToken.
        public string AccountId = Guid.Empty.ToString();
        public string AccountKey = string.Empty;
        public string AuthenticationToken = string.Empty;
        public string AccessToken = string.Empty;
    }

```

Per la parte _Region_ nel dominio, usare un' [area vicina all'utente](../reference/regions.md).

Le informazioni sull'account possono essere ottenute dal portale, come descritto nel paragrafo [recuperare le informazioni sull'account](create-an-account.md#retrieve-the-account-information) .

## <a name="azure-frontend"></a>Front-end di Azure

Le classi pertinenti sono ```AzureFrontend``` e ```AzureSession```. ```AzureFrontend```viene usato per la gestione degli account e la funzionalità a livello di account, che include la conversione di asset e la creazione della sessione di rendering. ```AzureSession```viene usato per le funzionalità a livello di sessione e include: aggiornamento della sessione, query, rinnovo e rimozione delle autorizzazioni.

Ogni apertura/creazione ```AzureSession``` manterrà un riferimento al front-end che lo ha creato. Per arrestare il sistema, è necessario deallocare tutte le sessioni prima che il front-end venga deallocato.

La deallocazione di una sessione non arresterà la macchina virtuale `AzureSession.StopAsync` in Azure. deve essere chiamata in modo esplicito.

Dopo che una sessione è stata creata e il suo stato è stato contrassegnato come pronto, può connettersi al runtime di rendering remoto `AzureSession.ConnectToRuntime`con.

### <a name="threading"></a>Threading

Tutte le chiamate asincrone AzureSession e AzureFrontend vengono completate in un thread in background, non nel thread principale dell'applicazione.

### <a name="conversion-apis"></a>API di conversione

Per ulteriori informazioni sul servizio di conversione, vedere [l'API REST di conversione del modello](conversion/conversion-rest-api.md).

#### <a name="start-asset-conversion"></a>Avvia conversione asset

``` cs
private StartConversionAsync _pendingAsync = null;

void StartAssetConversion(AzureFrontend frontend, string modelName, string modelUrl, string assetContainerUrl)
{
    _pendingAsync = frontend.StartConversionAsync(
        new AssetConversionParams(modelName, modelUrl, assetContainerUrl));
    _pendingAsync.Completed +=
        (StartConversionAsync res) =>
        {
            if (res.IsRanToCompletion)
            {
                //use res.Result
            }
            else
            {
                Console.WriteLine("Failed to start asset conversion!");
            }
        };

        _pendingAsync = null;
}
```

#### <a name="get-conversion-status"></a>Ottenere lo stato della conversione

``` cs
private ConversionStatusAsync _pendingAsync = null
void GetConversionStatus(AzureFrontend frontend, string assetId)
{
    _pendingAsync = frontend.GetConversionStatusAsync(assetId);
    _pendingAsync.Completed +=
        (ConversionStatusAsync res) =>
        {
            if (res.IsRanToCompletion)
            {
                //use res.Result
            }
            else
            {
                Console.WriteLine("Failed to get status of asset conversion!");
            }

            _pendingAsync = null;
        };
}
```

### <a name="rendering-apis"></a>Rendering delle API

Per informazioni dettagliate sulla gestione delle sessioni, vedere [l'API REST di gestione delle sessioni](session-rest-api.md) .

Una sessione di rendering può essere creata dinamicamente nel servizio oppure un ID di sessione già esistente può essere ' aperto ' in un oggetto AzureSession.

#### <a name="create-rendering-session"></a>Crea sessione di rendering

``` cs
private CreateSessionAsync _pendingAsync = null;
void CreateRenderingSession(AzureFrontend frontend, RenderingSessionVmSize vmSize, ARRTimeSpan maxLease)
{
    _pendingAsync = frontend.CreateNewRenderingSessionAsync(
        new RenderingSessionCreationParams(vmSize, maxLease));

    _pendingAsync.Completed +=
        (CreateSessionAsync res) =>
        {
            if (res.IsRanToCompletion)
            {
                //use res.Result
            }
            else
            {
                Console.WriteLine("Failed to create session!");
            }
            _pendingAsync = null;
        };
}
```

#### <a name="open-an-existing-rendering-session"></a>Aprire una sessione di rendering esistente

L'apertura di una sessione esistente è una chiamata sincrona.

``` cs
void CreateRenderingSession(AzureFrontend frontend, string sessionId)
{
    AzureSession session = frontend.OpenRenderingSession(sessionId);
    // Query session status, etc.
}
```

#### <a name="get-current-rendering-sessions"></a>Ottenere sessioni di rendering correnti

``` cs
private SessionPropertiesArrayAsync _pendingAsync = null;
void GetCurrentRenderingSessions(AzureFrontend frontend)
{
    _pendingAsync = frontend.GetCurrentRenderingSessionsAsync();
    _pendingAsync.Completed +=
        (SessionPropertiesArrayAsync res) =>
        {
            if (res.IsRanToCompletion)
            {
                //use res.Result
            }
            else
            {
                Console.WriteLine("Failed to get current rendering sessions!");
            }
            _pendingAsync = null;
        };
}
```

### <a name="session-apis"></a>API di sessione

#### <a name="get-rendering-session-properties"></a>Ottenere le proprietà della sessione di rendering

``` cs
private SessionPropertiesAsync _pendingAsync = null;
void GetRenderingSessionProperties(AzureSession session)
{
    _pendingAsync = session.GetPropertiesAsync();
    _pendingAsync.Completed +=
        (SessionPropertiesAsync res) =>
        {
            if (res.IsRanToCompletion)
            {
                //use res.Result
            }
            else
            {
                Console.WriteLine("Failed to get properties of session!");
            }
            _pendingAsync = null;
        };
}
```

#### <a name="update-rendering-session"></a>Aggiorna sessione di rendering

``` cs
private SessionAsync _pendingAsync;
void UpdateRenderingSession(AzureSession session, ARRTimeSpan updatedLease)
{
    _pendingAsync = session.RenewAsync(
        new RenderingSessionUpdateParams(updatedLease));
    _pendingAsync.Completed +=
        (SessionAsync res) =>
        {
            if (res.IsRanToCompletion)
            {
                Console.WriteLine("Rendering session renewed succeeded!");
            }
            else
            {
                Console.WriteLine("Failed to renew rendering session!");
            }
            _pendingAsync = null;
        };
}
```

#### <a name="stop-rendering-session"></a>Interrompi sessione di rendering

``` cs
private SessionAsync _pendingAsync;
void StopRenderingSession(AzureSession session)
{
    _pendingAsync = session.StopAsync();
    _pendingAsync.Completed +=
        (SessionAsync res) =>
        {
            if (res.IsRanToCompletion)
            {
                Console.WriteLine("Rendering session stopped successfully!");
            }
            else
            {
                Console.WriteLine("Failed to stop rendering session!");
            }
            _pendingAsync = null;
        };
}
```

#### <a name="connect-to-arr-inspector"></a>Connetti a controllo ARR

``` cs
private ArrInspectorAsync _pendingAsync = null;
void ConnectToArrInspector(AzureSession session, string hostname)
{
    _pendingAsync = session.ConnectToArrInspectorAsync(hostname);
    _pendingAsync.Completed +=
        (ArrInspectorAsync res) =>
        {
            if (res.IsRanToCompletion)
            {
                // Launch the html file with default browser
                string htmlPath = res.Result;
#if WINDOWS_UWP
                UnityEngine.WSA.Application.InvokeOnUIThread(async () =>
                {
                    var file = await Windows.Storage.StorageFile.GetFileFromPathAsync(htmlPath);
                    await Windows.System.Launcher.LaunchFileAsync(file);
                }, true);
#else
                InvokeOnAppThreadAsync(() =>
                {
                    System.Diagnostics.Process.Start("file:///" + htmlPath);
                });
#endif
            }
            else
            {
                Console.WriteLine("Failed to connect to ARR inspector!");
            }
        };
}
```

## <a name="next-steps"></a>Passaggi successivi

* [Creare un account](create-an-account.md)
* [Script di Azure PowerShell di esempio](../samples/powershell-example-scripts.md)
