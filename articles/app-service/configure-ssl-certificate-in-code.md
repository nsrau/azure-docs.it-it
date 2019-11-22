---
title: Usare il certificato SSL nel servizio app Azure di codice | Microsoft Docs
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
ms.date: 11/04/2019
ms.author: cephalin
ms.reviewer: yutlin
ms.custom: seodec18
ms.openlocfilehash: 1546ded1977e1e26792189e1d992d106d3d77ef2
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74271292"
---
# <a name="use-an-ssl-certificate-in-your-code-in-azure-app-service"></a>Usare un certificato SSL nel codice nel servizio app Azure

Nel codice dell'applicazione è possibile accedere ai [certificati pubblici o privati aggiunti al servizio app](configure-ssl-certificate.md). Il codice dell'app può fungere da client e accedere a un servizio esterno che richiede l'autenticazione del certificato o potrebbe dover eseguire attività crittografiche. Questa guida dettagliata illustra come usare certificati pubblici o privati nel codice dell'applicazione.

Questo approccio all'uso dei certificati nel codice usa la funzionalità SSL nel servizio app, che richiede che l'app sia nel livello **Basic** o superiore. Se l'app è a livello **gratuito** o **condiviso** , è possibile [includere il file del certificato nel repository dell'app](#load-certificate-from-file).

Quando si consente al servizio app di gestire i certificati SSL, è possibile tenere separati i certificati e il codice dell'applicazione e proteggere i dati sensibili.

## <a name="prerequisites"></a>prerequisiti

Per completare questa guida pratica:

- [Creare un'app del Servizio app](/azure/app-service/)
- [Aggiungere un certificato all'app](configure-ssl-certificate.md)

## <a name="find-the-thumbprint"></a>Trovare l'identificazione personale

Nel menu a sinistra del <a href="https://portal.azure.com" target="_blank">portale di Azure</a> scegliere **Servizi app** >  **\<nome app>** .

Dall'area di spostamento a sinistra dell'app selezionare **Impostazioni TLS/SSL**, quindi selezionare **certificati di chiave privata (con estensione pfx)** o **certificati di chiave pubblica (. cer)** .

Individuare il certificato che si vuole usare e copiare l'identificazione personale.

![Copiare l'identificazione personale del certificato](./media/configure-ssl-certificate/create-free-cert-finished.png)

## <a name="make-the-certificate-accessible"></a>Rendere accessibile il certificato

Per accedere a un certificato nel codice dell'app, aggiungere l'identificazione personale all'impostazione `WEBSITE_LOAD_CERTIFICATES` app, eseguendo il comando seguente nel <a target="_blank" href="https://shell.azure.com" >cloud Shell</a>:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_LOAD_CERTIFICATES=<comma-separated-certificate-thumbprints>
```

Per rendere accessibili tutti i certificati, impostare il valore su `*`.

## <a name="load-certificate-in-windows-apps"></a>Caricare il certificato nelle app di Windows

L'impostazione dell'app `WEBSITE_LOAD_CERTIFICATES` rende accessibili i certificati specificati all'app ospitata in Windows nell'archivio certificati di Windows e il percorso dipende dal piano [tariffario](overview-hosting-plans.md):

- Livello **isolato** in [Machine\My locale](/windows-hardware/drivers/install/local-machine-and-current-user-certificate-stores). 
- Tutti gli altri livelli, nella [User\My corrente](/windows-hardware/drivers/install/local-machine-and-current-user-certificate-stores).

Nel C# codice è possibile accedere al certificato dall'identificazione personale del certificato. Il codice seguente carica un certificato con l'identificazione personale `E661583E8FABEF4C0BEF694CBC41C28FB81CD870`.

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

Nel codice Java è possibile accedere al certificato dall'archivio "Windows-MY" usando il campo nome comune del soggetto (vedere [certificato chiave pubblica](https://en.wikipedia.org/wiki/Public_key_certificate)). Nel codice seguente viene illustrato come caricare un certificato di chiave privata:

```java
import org.springframework.web.bind.annotation.RestController;
import org.springframework.web.bind.annotation.RequestMapping;
import java.security.KeyStore;
import java.security.cert.Certificate;
import java.security.PrivateKey;

...
KeyStore ks = KeyStore.getInstance("Windows-MY");
ks.load(null, null); 
Certificate cert = ks.getCertificate("<subject-cn>");
PrivateKey privKey = (PrivateKey) ks.getKey("<subject-cn>", ("<password>").toCharArray());

// Use the certificate and key
...
```

Per le lingue che non supportano o non offrono supporto insufficiente per l'archivio certificati di Windows, vedere [caricare il certificato da un file](#load-certificate-from-file).

## <a name="load-certificate-in-linux-apps"></a>Caricare il certificato nelle app Linux

Le impostazioni dell'app `WEBSITE_LOAD_CERTIFICATES` rendono i certificati specificati accessibili alle app ospitate da Linux, incluse le app contenitore personalizzate, come file. I file si trovano nelle directory seguenti:

- Certificati privati-`/var/ssl/private` (file `.p12`)
- Certificati pubblici-`/var/ssl/certs` (file `.der`)

I nomi dei file di certificato sono le identificazioni personali del certificato. Il codice C# seguente illustra come caricare un certificato pubblico in un'app Linux.

```csharp
using System;
using System.Security.Cryptography.X509Certificates;

...
var bytes = System.IO.File.ReadAllBytes("/var/ssl/certs/<thumbprint>.der");
var cert = new X509Certificate2(bytes);

// Use the loaded certificate
```

Per informazioni su come caricare un certificato SSL da un file in node. js, PHP, Python, Java o Ruby, vedere la documentazione relativa alla rispettiva lingua o piattaforma Web.

## <a name="load-certificate-from-file"></a>Carica certificato da file

Se è necessario caricare un file di certificato caricato manualmente, è preferibile caricare il certificato usando [FTPS](deploy-ftp.md) invece di [git](deploy-local-git.md), ad esempio. È necessario tenere i dati sensibili come un certificato privato fuori dal controllo del codice sorgente.

> [!NOTE]
> ASP.NET e ASP.NET Core in Windows devono accedere all'archivio certificati anche se si carica un certificato da un file. Per caricare un file di certificato in un'app .NET di Windows, caricare il profilo utente corrente con il comando seguente nel <a target="_blank" href="https://shell.azure.com" >cloud Shell</a>:
>
> ```azurecli-interactive
> az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_LOAD_USER_PROFILE=1
> ```
>
> Questo approccio all'uso dei certificati nel codice usa la funzionalità SSL nel servizio app, che richiede che l'app sia nel livello **Basic** o superiore.

Nell'esempio C# seguente viene caricato un certificato pubblico da un percorso relativo nell'app:

```csharp
using System;
using System.Security.Cryptography.X509Certificates;

...
var bytes = System.IO.File.ReadAllBytes("~/<relative-path-to-cert-file>");
var cert = new X509Certificate2(bytes);

// Use the loaded certificate
```

Per informazioni su come caricare un certificato SSL da un file in node. js, PHP, Python, Java o Ruby, vedere la documentazione relativa alla rispettiva lingua o piattaforma Web.

## <a name="more-resources"></a>Altre risorse

* [Proteggere un nome DNS personalizzato con un'associazione SSL](configure-ssl-bindings.md)
* [Applicare HTTPS](configure-ssl-bindings.md#enforce-https)
* [Applicare TLS 1.1/1.2](configure-ssl-bindings.md#enforce-tls-versions)
* [Domande frequenti: Certificati del servizio app](https://docs.microsoft.com/azure/app-service/faq-configuration-and-management/)
