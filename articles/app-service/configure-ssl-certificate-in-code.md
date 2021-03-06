---
title: Usare un certificato TLS/SSL nel codice
description: Informazioni su come usare i certificati client nel codice. Eseguire l'autenticazione con le risorse remote con un certificato client o eseguire attività crittografiche.
ms.topic: article
ms.date: 09/22/2020
ms.reviewer: yutlin
ms.custom: seodec18
ms.openlocfilehash: b4e184f827875ebebd40ab976ef63e77ee702d49
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93126040"
---
# <a name="use-a-tlsssl-certificate-in-your-code-in-azure-app-service"></a>Usare un certificato TLS/SSL nel codice nel Servizio app di Azure

Nel codice dell'applicazione è possibile accedere ai [certificati pubblici o privati aggiunti al servizio app](configure-ssl-certificate.md). Il codice dell'app può fungere da client e accedere a un servizio esterno che richiede l'autenticazione del certificato o potrebbe dover eseguire attività crittografiche. Questa guida dettagliata illustra come usare certificati pubblici o privati nel codice dell'applicazione.

Questo approccio all'uso dei certificati nel codice consente di usare la funzionalità TLS nel servizio app, che richiede che l'app sia nel livello **Basic** o superiore. Se l'app è a livello **gratuito** o **condiviso** , è possibile [includere il file del certificato nel repository dell'app](#load-certificate-from-file).

Quando si consente al servizio app di gestire i certificati TLS/SSL, è possibile gestire i certificati e il codice dell'applicazione separatamente e proteggere i dati sensibili.

## <a name="prerequisites"></a>Prerequisiti

Per completare questa guida pratica:

- [Creare un'app del Servizio app](./index.yml)
- [Aggiungere un certificato all'app](configure-ssl-certificate.md)

## <a name="find-the-thumbprint"></a>Trovare l'identificazione personale

Nel menu a sinistra del <a href="https://portal.azure.com" target="_blank">portale di Azure</a> scegliere **Servizi app** >  **\<app-name>** .

Dall'area di spostamento a sinistra dell'app selezionare **Impostazioni TLS/SSL** , quindi selezionare **certificati di chiave privata (con estensione pfx)** o **certificati di chiave pubblica (. cer)** .

Individuare il certificato che si vuole usare e copiare l'identificazione personale.

![Copiare l'identificazione personale del certificato](./media/configure-ssl-certificate/create-free-cert-finished.png)

## <a name="make-the-certificate-accessible"></a>Rendere accessibile il certificato

Per accedere a un certificato nel codice dell'app, aggiungere l'identificazione personale all' `WEBSITE_LOAD_CERTIFICATES` impostazione dell'app, eseguendo il comando seguente nella <a target="_blank" href="https://shell.azure.com" >cloud Shell</a>:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_LOAD_CERTIFICATES=<comma-separated-certificate-thumbprints>
```

Per rendere accessibili tutti i certificati, impostare il valore su `*` .

## <a name="load-certificate-in-windows-apps"></a>Caricare il certificato nelle app di Windows

L' `WEBSITE_LOAD_CERTIFICATES` impostazione dell'app rende accessibili i certificati specificati all'app ospitata di Windows nell'archivio certificati di Windows, nella [User\My corrente](/windows-hardware/drivers/install/local-machine-and-current-user-certificate-stores).

Nel codice C# si accede al certificato dall'identificazione personale del certificato. Il codice seguente carica un certificato con l'identificazione personale `E661583E8FABEF4C0BEF694CBC41C28FB81CD870`.

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

## <a name="load-certificate-from-file"></a>Carica certificato da file

Se è necessario caricare un file di certificato caricato manualmente, è preferibile caricare il certificato usando [FTPS](deploy-ftp.md) invece di [git](deploy-local-git.md), ad esempio. È necessario tenere i dati sensibili come un certificato privato fuori dal controllo del codice sorgente.

> [!NOTE]
> ASP.NET e ASP.NET Core in Windows devono accedere all'archivio certificati anche se si carica un certificato da un file. Per caricare un file di certificato in un'app .NET di Windows, caricare il profilo utente corrente con il comando seguente nel <a target="_blank" href="https://shell.azure.com" >cloud Shell</a>:
>
> ```azurecli-interactive
> az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_LOAD_USER_PROFILE=1
> ```
>
> Questo approccio all'uso dei certificati nel codice consente di usare la funzionalità TLS nel servizio app, che richiede che l'app sia nel livello **Basic** o superiore.

L'esempio C# seguente carica un certificato pubblico da un percorso relativo nell'app:

```csharp
using System;
using System.IO;
using System.Security.Cryptography.X509Certificates;

...
var bytes = File.ReadAllBytes("~/<relative-path-to-cert-file>");
var cert = new X509Certificate2(bytes);

// Use the loaded certificate
```

Per informazioni su come caricare un certificato TLS/SSL da un file in Node.js, PHP, Python, Java o Ruby, vedere la documentazione relativa alla rispettiva lingua o piattaforma Web.

## <a name="load-certificate-in-linuxwindows-containers"></a>Caricare il certificato nei contenitori Linux/Windows

Le `WEBSITE_LOAD_CERTIFICATES` impostazioni dell'app rendono i certificati specificati accessibili alle app contenitore Windows o Linux (inclusi i contenitori Linux predefiniti) come file. I file si trovano nelle directory seguenti:

| Piattaforma contenitore | Certificati pubblici | Certificati privati |
| - | - | - |
| Contenitore Windows | `C:\appservice\certificates\public` | `C:\appservice\certificates\private` |
| Contenitore Linux | `/var/ssl/certs` | `/var/ssl/private` |

I nomi dei file di certificato sono le identificazioni personali del certificato. 

> [!NOTE]
> Il servizio app inserisce i percorsi dei certificati nei contenitori di Windows come le variabili di ambiente seguenti `WEBSITE_PRIVATE_CERTS_PATH` ,, `WEBSITE_INTERMEDIATE_CERTS_PATH` `WEBSITE_PUBLIC_CERTS_PATH` e `WEBSITE_ROOT_CERTS_PATH` . È preferibile fare riferimento al percorso del certificato con le variabili di ambiente anziché impostare come hardcoded il percorso del certificato, nel caso in cui i percorsi del certificato cambino in futuro.
>

Inoltre, i [contenitori di Windows Server Core](configure-custom-container.md#supported-parent-images) caricano automaticamente i certificati nell'archivio certificati, in **LocalMachine\MY** . Per caricare i certificati, seguire lo stesso modello di [caricamento del certificato nelle app di Windows](#load-certificate-in-windows-apps). Per i contenitori basati su Windows nano, usare i percorsi di file indicati in precedenza per [caricare il certificato direttamente da file](#load-certificate-from-file).

Il codice C# seguente illustra come caricare un certificato pubblico in un'app Linux.

```csharp
using System;
using System.IO;
using System.Security.Cryptography.X509Certificates;

...
var bytes = File.ReadAllBytes("/var/ssl/certs/<thumbprint>.der");
var cert = new X509Certificate2(bytes);

// Use the loaded certificate
```

Per informazioni su come caricare un certificato TLS/SSL da un file in Node.js, PHP, Python, Java o Ruby, vedere la documentazione relativa alla rispettiva lingua o piattaforma Web.

## <a name="more-resources"></a>Altre risorse

* [Proteggere un nome DNS personalizzato con un'associazione TLS/SSL nel Servizio app di Azure](configure-ssl-bindings.md)
* [Applicare HTTPS](configure-ssl-bindings.md#enforce-https)
* [Applicare TLS 1.1/1.2](configure-ssl-bindings.md#enforce-tls-versions)
* [FAQ: App Service Certificates](./faq-configuration-and-management.md) (Domande frequenti: certificati del servizio app)