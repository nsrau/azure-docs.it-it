---
title: Usare il certificato SSL nel servizio app Azure del codice dell'applicazione | Microsoft Docs
description: Informazioni su come usare i certificati client per la connessione alle risorse remote che li richiedono.
services: app-service
documentationcenter: ''
author: cephalin
manager: gwallace
editor: ''
ms.service: app-service
ms.workload: web
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/16/2019
ms.author: cephalin
ms.reviewer: yutlin
ms.custom: seodec18
ms.openlocfilehash: 1f042f72f82d2198472fe81670c697c0c4b28321
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73513692"
---
# <a name="use-an-ssl-certificate-in-your-application-code-in-azure-app-service"></a>Usare un certificato SSL nel codice dell'applicazione in Servizio app di Azure

Il codice dell'app del servizio app può fungere da client e accedere a un servizio esterno che richiede l'autenticazione del certificato. Questa guida dettagliata illustra come usare certificati pubblici o privati nel codice dell'applicazione.

Questo approccio all'uso dei certificati nel codice usa la funzionalità SSL nel servizio app, che richiede che l'app sia nel livello **Basic** o superiore. In alternativa, è possibile [includere il file del certificato nel repository dell'app](#load-certificate-from-file), ma non è una procedura consigliata per i certificati privati.

Quando si consente al servizio app di gestire i certificati SSL, è possibile tenere separati i certificati e il codice dell'applicazione e proteggere i dati sensibili.

## <a name="prerequisites"></a>Prerequisiti

Per completare questa guida pratica:

- [Creare un'app del Servizio app](/azure/app-service/)
- [Aggiungere un certificato all'app](configure-ssl-certificate.md)

## <a name="find-the-thumbprint"></a>Trovare l'identificazione personale

Nel menu a sinistra della <a href="https://portal.azure.com" target="_blank">portale di Azure</a>selezionare **servizi app** >  **\<nome app >** .

Dall'area di spostamento a sinistra dell'app selezionare **Impostazioni TLS/SSL**, quindi selezionare **certificati di chiave privata (con estensione pfx)** o **certificati di chiave pubblica (. cer)** .

Individuare il certificato che si vuole usare e copiare l'identificazione personale.

![Copiare l'identificazione personale del certificato](./media/configure-ssl-certificate/create-free-cert-finished.png)

## <a name="load-the-certificate"></a>Caricare il certificato

Per usare un certificato nel codice dell'app, aggiungere la relativa identificazione personale all'impostazione `WEBSITE_LOAD_CERTIFICATES` app, eseguendo il comando seguente nel <a target="_blank" href="https://shell.azure.com" >cloud Shell</a>:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_LOAD_CERTIFICATES=<comma-separated-certificate-thumbprints>
```

Per rendere accessibili tutti i certificati, impostare il valore su `*`.

> [!NOTE]
> Questa impostazione inserisce i certificati specificati nell'archivio [User\My corrente](/windows-hardware/drivers/install/local-machine-and-current-user-certificate-stores) per la maggior parte dei piani tariffari, ma nel livello **isolato** (ad esempio, l'app viene eseguita in un [ambiente del servizio app](environment/intro.md)), inserisce i certificati nel [Machine\My locale](/windows-hardware/drivers/install/local-machine-and-current-user-certificate-stores) negozio.
>

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

## <a name="more-resources"></a>Altre risorse

* [Proteggere un nome DNS personalizzato con un'associazione SSL](configure-ssl-bindings.md)
* [Applicare HTTPS](configure-ssl-bindings.md#enforce-https)
* [Applicare TLS 1.1/1.2](configure-ssl-bindings.md#enforce-tls-versions)
* [Domande frequenti: Certificati del servizio app](https://docs.microsoft.com/azure/app-service/faq-configuration-and-management/)
