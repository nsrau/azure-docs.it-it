---
title: Usare un certificato TLS/SSL nel codiceUse a TLS/SSL certificate in code
description: Informazioni su come usare i certificati client nel codice. Eseguire l'autenticazione con risorse remote con un certificato client o eseguire attività di crittografia con esse.
ms.topic: article
ms.date: 11/04/2019
ms.reviewer: yutlin
ms.custom: seodec18
ms.openlocfilehash: d76bac60bae11f0843d81de523030154af62a373
ms.sourcegitcommit: 98e79b359c4c6df2d8f9a47e0dbe93f3158be629
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/07/2020
ms.locfileid: "80811690"
---
# <a name="use-a-tlsssl-certificate-in-your-code-in-azure-app-service"></a>Usare un certificato TLS/SSL nel codice nel servizio app di AzureUse a TLS/SSL certificate in your code in Azure App Service

Nel codice dell'applicazione è possibile accedere [ai certificati pubblici o privati aggiunti al servizio app.](configure-ssl-certificate.md) Il codice dell'app può fungere da client e accedere a un servizio esterno che richiede l'autenticazione del certificato oppure eseguire attività di crittografia. In questa guida alle procedure viene illustrato come utilizzare certificati pubblici o privati nel codice dell'applicazione.

Questo approccio all'uso dei certificati nel codice usa la funzionalità TLS nel servizio app, che richiede che l'app sia nel livello **Basic** o superiore. Se l'app è nel livello **Gratuito** o **Condiviso,** è possibile [includere il file del certificato nel repository dell'app.](#load-certificate-from-file)

Quando si consente al servizio app di gestire i certificati TLS/SSL, è possibile gestire i certificati e il codice dell'applicazione separatamente e proteggere i dati sensibili.

## <a name="prerequisites"></a>Prerequisiti

Per completare questa guida pratica:

- [Creare un'app del servizio appCreate an App Service app](/azure/app-service/)
- [Aggiungere un certificato all'app](configure-ssl-certificate.md)

## <a name="find-the-thumbprint"></a>Trovare l'identificazione personale

Nel <a href="https://portal.azure.com" target="_blank">portale di Azure</a>scegliere Nome **App Services** > **\<app **servizi>dal menu a sinistra.

Nel riquadro di spostamento sinistro dell'app seleziona **Impostazioni TLS/SSL**, quindi Certificati chiave privata (con estensione **pfx)** o Certificati di **chiave pubblica (con estensione cer)**.

Individuare il certificato che si desidera utilizzare e copiare l'identificazione personale.

![Copiare l'identificazione personale del certificato](./media/configure-ssl-certificate/create-free-cert-finished.png)

## <a name="make-the-certificate-accessible"></a>Rendere accessibile il certificato

Per accedere a un certificato nel codice dell'app, aggiungi l'identificazione personale all'impostazione dell'app `WEBSITE_LOAD_CERTIFICATES` eseguendo il comando seguente in Cloud <a target="_blank" href="https://shell.azure.com" >Shell:</a>

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_LOAD_CERTIFICATES=<comma-separated-certificate-thumbprints>
```

Per rendere accessibili tutti i certificati, impostare il valore su `*`.

## <a name="load-certificate-in-windows-apps"></a>Caricare il certificato nelle app di Windows

L'impostazione `WEBSITE_LOAD_CERTIFICATES` dell'app rende i certificati specificati accessibili all'app ospitata di Windows nell'archivio certificati di Windows e il percorso dipende dal [piano tariffario:](overview-hosting-plans.md)

- **Livello isolato** - in [Computer locale - My](/windows-hardware/drivers/install/local-machine-and-current-user-certificate-stores). 
- Tutti gli altri livelli - in [Utente corrente - My](/windows-hardware/drivers/install/local-machine-and-current-user-certificate-stores).

Nel codice di C, si accede al certificato dall'identificazione personale del certificato. Il codice seguente carica un certificato con l'identificazione personale `E661583E8FABEF4C0BEF694CBC41C28FB81CD870`.

```csharp
using System;
using System.Linq;
using System.Security.Cryptography.X509Certificates;

string certThumbprint = "E661583E8FABEF4C0BEF694CBC41C28FB81CD870";
bool validOnly = false;

using (X509Store certStore = new X509Store(StoreName.My, StoreLocation.CurrentUser))
{
  certStore.Open(OpenFlags.ReadOnly);

  X509Certificate2Collection certCollection = certStore.Certificates.Find(
                              X509FindType.FindByThumbprint,
                              // Replace below with your certificate's thumbprint
                              certThumbprint,
                              validOnly);
  // Get the first cert with the thumbprint
  X509Certificate2 cert = certCollection.OfType<X509Certificate>().FirstOrDefault();

  if (cert is null)
      throw new Exception($"Certificate with thumbprint {certThumbprint} was not found");

  // Use certificate
  Console.WriteLine(cert.FriendlyName);
  
  // Consider to call Dispose() on the certificate after it's being used, avaliable in .NET 4.6 and later
}
```

Nel codice Java il certificato viene eseguito dall'archivio "Windows-MY" utilizzando il campo Nome comune soggetto (vedere [Certificato con chiave pubblica](https://en.wikipedia.org/wiki/Public_key_certificate)). Il codice seguente mostra come caricare un certificato di chiave privata:The following code shows how to load a private key certificate:

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

Per le lingue che non supportano o offrono un supporto insufficiente per l'archivio certificati di Windows, vedere [Caricare il certificato dal file](#load-certificate-from-file).

## <a name="load-certificate-in-linux-apps"></a>Caricare il certificato nelle app LinuxLoad certificate in Linux apps

Le `WEBSITE_LOAD_CERTIFICATES` impostazioni dell'app rendono i certificati specificati accessibili alle app ospitate da Linux (incluse le app contenitore personalizzate) come file. I file si trovano nelle seguenti directory:

- Certificati privati `/var/ssl/private` `.p12` - (file)
- Certificati pubblici `/var/ssl/certs` `.der` - ( file)

I nomi dei file del certificato sono le identificazioni personale del certificato. Il seguente codice c'è illustrato come caricare un certificato pubblico in un'app Linux.The following C's code shows how to load a public certificate in a Linux app.

```csharp
using System;
using System.IO;
using System.Security.Cryptography.X509Certificates;

...
var bytes = File.ReadAllBytes("/var/ssl/certs/<thumbprint>.der");
var cert = new X509Certificate2(bytes);

// Use the loaded certificate
```

Per informazioni su come caricare un certificato TLS/SSL da un file in Node.js, PHP, Python, Java o Ruby, vedere la documentazione relativa al rispettivo linguaggio o piattaforma Web.

## <a name="load-certificate-from-file"></a>Carica certificato da file

Se è necessario caricare un file di certificato caricato manualmente, è preferibile caricare il certificato utilizzando [FTPS](deploy-ftp.md) anziché [Git](deploy-local-git.md), ad esempio. È consigliabile mantenere i dati sensibili come un certificato privato fuori dal controllo del codice sorgente.

> [!NOTE]
> ASP.NET e ASP.NET Core in Windows devono accedere all'archivio certificati anche se si carica un certificato da un file. Per caricare un file di certificato in un'app Windows .NET, caricare il profilo utente corrente con il comando seguente in <a target="_blank" href="https://shell.azure.com" >Cloud Shell:</a>
>
> ```azurecli-interactive
> az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_LOAD_USER_PROFILE=1
> ```
>
> Questo approccio all'uso dei certificati nel codice usa la funzionalità TLS nel servizio app, che richiede che l'app sia nel livello **Basic** o superiore.

L'esempio di codice in c'è il seguente caricamento di un certificato pubblico da un percorso relativo nell'app:The following C' example loads a public certificate from a relative path in your app:

```csharp
using System;
using System.IO;
using System.Security.Cryptography.X509Certificates;

...
var bytes = File.ReadAllBytes("~/<relative-path-to-cert-file>");
var cert = new X509Certificate2(bytes);

// Use the loaded certificate
```

Per informazioni su come caricare un certificato TLS/SSL da un file in Node.js, PHP, Python, Java o Ruby, vedere la documentazione relativa al rispettivo linguaggio o piattaforma Web.

## <a name="more-resources"></a>Altre risorse

* [Proteggere un nome DNS personalizzato con un'associazione TLS/SSL nel servizio app di AzureSecure a custom DNS name with a TLS/SSL binding in Azure App Service](configure-ssl-bindings.md)
* [Applicare HTTPS](configure-ssl-bindings.md#enforce-https)
* [Applicare TLS 1.1/1.2](configure-ssl-bindings.md#enforce-tls-versions)
* [Domande frequenti: Certificati del servizio app](https://docs.microsoft.com/azure/app-service/faq-configuration-and-management/)
