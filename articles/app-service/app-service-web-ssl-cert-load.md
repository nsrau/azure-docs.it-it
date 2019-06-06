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
ms.devlang: na
ms.topic: article
ms.date: 05/29/2019
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: ead1892062912840c9931ae60d11c90975ad26ac
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/04/2019
ms.locfileid: "66475115"
---
# <a name="use-an-ssl-certificate-in-your-application-code-in-azure-app-service"></a>Usare un certificato SSL nel codice dell'applicazione in Servizio app di Azure

Questa guida illustra come usare i certificati pubblici o privati nel codice dell'applicazione. Un esempio di caso d'uso è l'accesso dell'app a un servizio esterno che richiede l'autenticazione del certificato.

Questo approccio all'utilizzo dei certificati nel codice viene utilizzato il protocollo SSL funzionalità nel servizio App, che richiede l'app sia **base** livello o versione successiva. In alternativa, è possibile [includere il file del certificato nell'archivio app](#load-certificate-from-file), ma non è una procedura consigliata per i certificati privati.

Quando si consente al servizio app di gestire i certificati SSL, è possibile tenere separati i certificati e il codice dell'applicazione e proteggere i dati sensibili.

## <a name="upload-a-private-certificate"></a>Caricare un certificato privato

Prima di caricare un certificato privato, assicurarsi che [soddisfa i requisiti](app-service-web-tutorial-custom-ssl.md#prepare-a-private-certificate), ad eccezione del fatto che non è necessario essere configurati per l'autenticazione Server.

Quando si è pronti per caricare, eseguire il comando seguente <a target="_blank" href="https://shell.azure.com" >Cloud Shell</a>.

```azurecli-interactive
az webapp config ssl upload --name <app-name> --resource-group <resource-group-name> --certificate-file <path-to-PFX-file> --certificate-password <PFX-password> --query thumbprint
```

Copiare l'identificazione personale del certificato e vedere [rendere accessibile il certificato](#make-the-certificate-accessible).

## <a name="upload-a-public-certificate"></a>Caricare un certificato pubblico

Certificati pubblici sono supportati nel *CER* formato. Per caricare un certificato pubblico, il <a href="https://portal.azure.com" target="_blank">portale di Azure</a>e passare all'app.

Fare clic su **impostazioni SSL** > **certificati pubblici (con estensione cer)**  > **carica certificato pubblico** dal riquadro di spostamento a sinistra dell'app.

Nelle **nome**, digitare un nome per il certificato. Nelle **file di certificato CER**, selezionare il file CER.

Fare clic su **Carica**.

![Caricare un certificato pubblico](./media/app-service-web-ssl-cert-load/private-cert-upload.png)

Una volta caricato il certificato, copiare l'identificazione personale del certificato e vedere [rendere accessibile il certificato](#make-the-certificate-accessible).

## <a name="import-an-app-service-certificate"></a>Importare un certificato del servizio App

Visualizzare [acquistare e configurare un certificato SSL per il servizio App di Azure](web-sites-purchase-ssl-web-site.md).

Dopo aver importato il certificato, copiare l'identificazione personale del certificato e vedere [rendere accessibile il certificato](#make-the-certificate-accessible).

## <a name="make-the-certificate-accessible"></a>Rendere accessibile il certificato

Per usare un certificato caricato o importato nel codice dell'app, rendere accessibili con l'identificazione personale di `WEBSITE_LOAD_CERTIFICATES` impostazione dell'app, eseguendo il comando seguente nel <a target="_blank" href="https://shell.azure.com" >Cloud Shell</a>:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_LOAD_CERTIFICATES=<comma-separated-certificate-thumbprints>
```

Per rendere accessibili tutti i certificati, impostare il valore su `*`.

> [!NOTE]
> Questa impostazione posiziona i certificati specificati nel [User\My corrente](/windows-hardware/drivers/install/local-machine-and-current-user-certificate-stores) store per la maggior parte dei piani tariffari, ma nel **Isolated** livello (ad esempio l'app viene eseguita in un [ambiente del servizio App](environment/intro.md)), che inserisce i certificati il [Machine\My locale](/windows-hardware/drivers/install/local-machine-and-current-user-certificate-stores) archiviare.
>

![Configurare l'impostazione dell'app](./media/app-service-web-ssl-cert-load/configure-app-setting.png)

Al termine fare clic su **Salva**.

I certificati configurati sono ora pronti per essere usato dal codice.

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
## <a name="load-certificate-from-file"></a>Caricare il certificato da file

Se è necessario caricare un file di certificato dalla directory dell'applicazione, è preferibile a caricarlo utilizzando [FTPS](deploy-ftp.md) invece di [Git](deploy-local-git.md), ad esempio. È consigliabile mantenere i dati sensibili, ad esempio un certificato privato all'esterno di controllo del codice sorgente.

Anche se si sta caricando il file direttamente nel codice .NET, la libreria ancora consente di verificare se il profilo utente corrente viene caricato. Per caricare il profilo utente corrente, impostare il `WEBSITE_LOAD_USER_PROFILE` impostazione dell'app con il comando seguente nel <a target="_blank" href="https://shell.azure.com" >Cloud Shell</a>.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_LOAD_USER_PROFILE=1
```

Dopo aver impostato questa impostazione, il seguente C# esempio carica un certificato denominato `mycert.pfx` dal `certs` directory del repository dell'app.

```csharp
using System;
using System.Security.Cryptography.X509Certificates;

...
// Replace the parameter with "~/<relative-path-to-cert-file>".
string certPath = Server.MapPath("~/certs/mycert.pfx");

X509Certificate2 cert = GetCertificate(certPath, signatureBlob.Thumbprint);
...
```
