---
title: Usare un certificato SSL client nel codice dell'applicazione - Servizio app di Azure | Microsoft Docs
description: Informazioni su come usare i certificati client per la connessione alle risorse remote che li richiedono.
services: app-service\web
documentationcenter: ''
author: cephalin
manager: cfowler
editor: ''
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 05/29/2019
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 0c8c270681794621b2a12671d4bcf350cd6cc4d8
ms.sourcegitcommit: d7689ff43ef1395e61101b718501bab181aca1fa
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/06/2019
ms.locfileid: "71981122"
---
# <a name="use-an-ssl-certificate-in-your-application-code-in-azure-app-service"></a>Usare un certificato SSL nel codice dell'applicazione in Servizio app di Azure

Questa guida dettagliata illustra come usare certificati pubblici o privati nel codice dell'applicazione. Un esempio di caso d'uso è l'accesso dell'app a un servizio esterno che richiede l'autenticazione del certificato.

Questo approccio all'uso dei certificati nel codice usa la funzionalità SSL nel servizio app, che richiede che l'app sia nel livello **Basic** o superiore. In alternativa, è possibile [includere il file del certificato nel repository dell'app](#load-certificate-from-file), ma non è una procedura consigliata per i certificati privati.

Quando si consente al servizio app di gestire i certificati SSL, è possibile tenere separati i certificati e il codice dell'applicazione e proteggere i dati sensibili.

## <a name="upload-a-private-certificate"></a>Caricare un certificato privato

Prima di caricare un certificato privato, verificare che [soddisfi tutti i requisiti](app-service-web-tutorial-custom-ssl.md#prepare-a-private-certificate), ad eccezione del fatto che non è necessario configurarlo per l'autenticazione server.

Quando si è pronti per il caricamento, eseguire il comando seguente nella <a target="_blank" href="https://shell.azure.com" >cloud Shell</a>.

```azurecli-interactive
az webapp config ssl upload --name <app-name> --resource-group <resource-group-name> --certificate-file <path-to-PFX-file> --certificate-password <PFX-password> --query thumbprint
```

Copiare l'identificazione personale del certificato e vedere [rendere accessibile il certificato](#make-the-certificate-accessible).

## <a name="upload-a-public-certificate"></a>Caricare un certificato pubblico

I certificati pubblici sono supportati nel formato *CER* . Per caricare un certificato pubblico, il <a href="https://portal.azure.com" target="_blank">portale di Azure</a>e passare all'app.

Fare clic su **Impostazioni SSL** > **certificati pubblici (. cer)**  > **caricare il certificato pubblico** dall'area di spostamento a sinistra dell'app.

In **nome**Digitare un nome per il certificato. In **file di certificato CER**selezionare il file CER.

Fare clic su **Carica**.

![Caricare un certificato pubblico](./media/app-service-web-ssl-cert-load/private-cert-upload.png)

Una volta caricato il certificato, copiare l'identificazione personale del certificato e vedere [rendere accessibile il certificato](#make-the-certificate-accessible).

## <a name="import-an-app-service-certificate"></a>Importare un certificato del servizio app

Vedere [acquistare e configurare un certificato SSL per il servizio app Azure](web-sites-purchase-ssl-web-site.md).

Una volta importato il certificato, copiare l'identificazione personale del certificato e vedere [rendere accessibile il certificato](#make-the-certificate-accessible).

## <a name="make-the-certificate-accessible"></a>Rendere accessibile il certificato

Per usare un certificato caricato o importato nel codice dell'app, rendere accessibile l'identificazione personale con l'impostazione dell'app `WEBSITE_LOAD_CERTIFICATES`, eseguendo il comando seguente nel <a target="_blank" href="https://shell.azure.com" >cloud Shell</a>:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_LOAD_CERTIFICATES=<comma-separated-certificate-thumbprints>
```

Per rendere accessibili tutti i certificati, impostare il valore su `*`.

> [!NOTE]
> Questa impostazione inserisce i certificati specificati nell'archivio [User\My corrente](/windows-hardware/drivers/install/local-machine-and-current-user-certificate-stores) per la maggior parte dei piani tariffari, ma se l'app è in esecuzione nel livello **isolato** (ad esempio, l'app viene eseguita in un [ambiente del servizio app](environment/intro.md)), potrebbe essere necessario archiviare il sito [locale ](/windows-hardware/drivers/install/local-machine-and-current-user-certificate-stores)In alternativa, archivio Machine\My.
>

![Configurare l'impostazione dell'app](./media/app-service-web-ssl-cert-load/configure-app-setting.png)

Al termine fare clic su **Salva**.

I certificati configurati sono ora pronti per essere usati dal codice.

## <a name="load-the-certificate-in-code"></a>Caricare il certificato nel codice

Quando il certificato è accessibile, vi si accede nel codice C# tramite l'identificazione personale del certificato. Il codice seguente carica un certificato con l'identificazione personale `E661583E8FABEF4C0BEF694CBC41C28FB81CD870`.

```csharp
using System;
using System.Security.Cryptography.X509Certificates;

...
X509Store certStore = new X509Store(StoreName.My, StoreLocation.CurrentUser);
certStore.Open(OpenFlags.ReadOnly);
X509Certificate2Collection certCollection = certStore.Certificates.Find(
                            X509FindType.FindByThumbprint,
                            // Replace below with your certificate's thumbprint
                            "E661583E8FABEF4C0BEF694CBC41C28FB81CD870",
                            false);
// Get the first cert with the thumbprint
if (certCollection.Count > 0)
{
    X509Certificate2 cert = certCollection[0];
    // Use certificate
    Console.WriteLine(cert.FriendlyName);
}
certStore.Close();
...
```

<a name="file"></a>
## <a name="load-certificate-from-file"></a>Carica certificato da file

Se è necessario caricare un file di certificato dalla directory dell'applicazione, è preferibile caricarlo usando [FTPS](deploy-ftp.md) invece di [git](deploy-local-git.md), ad esempio. È necessario tenere i dati sensibili come un certificato privato fuori dal controllo del codice sorgente.

Anche se si sta caricando il file direttamente nel codice .NET, la libreria verifica comunque se il profilo utente corrente è caricato. Per caricare il profilo utente corrente, impostare l'impostazione dell'app `WEBSITE_LOAD_USER_PROFILE` con il comando seguente nel <a target="_blank" href="https://shell.azure.com" >cloud Shell</a>.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_LOAD_USER_PROFILE=1
```

Una volta impostata questa impostazione, nell'esempio C# seguente viene caricato un certificato denominato `mycert.pfx` dalla directory `certs` del repository dell'app.

```csharp
using System;
using System.Security.Cryptography.X509Certificates;

...
// Replace the parameter with "~/<relative-path-to-cert-file>".
string certPath = Server.MapPath("~/certs/mycert.pfx");

X509Certificate2 cert = GetCertificate(certPath, signatureBlob.Thumbprint);
...
```
