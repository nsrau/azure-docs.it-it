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
ms.date: 12/01/2017
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 763aadc50a8760b4265dbfc21e9278f909b68433
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60851120"
---
# <a name="use-an-ssl-certificate-in-your-application-code-in-azure-app-service"></a>Usare un certificato SSL nel codice dell'applicazione in Servizio app di Azure

Questa guida pratica illustra come usare uno dei certificati SSL caricati o importati nell'app del servizio app nel codice dell'applicazione. Un esempio di caso d'uso è l'accesso dell'app a un servizio esterno che richiede l'autenticazione del certificato. 

Questo approccio all'uso dei certificati SSL nel codice adotta la funzionalità SSL nel servizio app, che prevede che l'app sia nel livello **Basic** o superiore. In alternativa è possibile includere il file del certificato nella directory dell'applicazione e caricarla direttamente. Vedere [Alternativa: caricare il certificato come file](#file). Questa alternativa non consente tuttavia di nascondere la chiave privata nel certificato al codice dell'applicazione o allo sviluppatore. Se inoltre il codice dell'applicazione è in un repository open source, non è possibile tenere un certificato con una chiave privata nel repository.

Quando si consente al servizio app di gestire i certificati SSL, è possibile tenere separati i certificati e il codice dell'applicazione e proteggere i dati sensibili.

## <a name="prerequisites"></a>Prerequisiti

Per completare questa guida pratica:

- [Creare un'app del Servizio app](/azure/app-service/)
- [Eseguire il mapping di un nome DNS personalizzato all'app Web](app-service-web-tutorial-custom-domain.md)
- [Caricare un certificato SSL](app-service-web-tutorial-custom-ssl.md) o [importare un certificato del servizio app](web-sites-purchase-ssl-web-site.md) nell'app Web


## <a name="load-your-certificates"></a>Caricare i certificati

Per usare un certificato caricato o importato nel servizio app, renderlo prima accessibile al codice dell'applicazione usando l'impostazione dell'app `WEBSITE_LOAD_CERTIFICATES`.

Nel <a href="https://portal.azure.com" target="_blank">portale di Azure</a> aprire la pagina dell'app Web.

Nel riquadro di spostamento a sinistra fare clic su **Certificati SSL**.

![Certificato caricato](./media/app-service-web-tutorial-custom-ssl/certificate-uploaded.png)

Tutti i certificati SSL caricati e importati per questa app Web vengono visualizzati qui con le identificazioni personali. Copiare l'identificazione personale del certificato che si vuole usare.

Nel riquadro di spostamento a sinistra fare clic su **Impostazioni dell'applicazione**.

Aggiungere un'impostazione dell'app denominata `WEBSITE_LOAD_CERTIFICATES` e impostarne il valore sull'identificazione personale del certificato. Per rendere accessibili più certificati, usare valori di identificazione personale delimitati da virgole. Per rendere accessibili tutti i certificati, impostare il valore su `*`. Il certificato verrà inserito nell'archivio `CurrentUser\My`.

![Configurare l'impostazione dell'app](./media/app-service-web-ssl-cert-load/configure-app-setting.png)

Al termine fare clic su **Salva**.

Il certificato configurato ora può essere usato dal codice.

## <a name="use-certificate-in-c-code"></a>Usare il certificato nel codice C#

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
## <a name="alternative-load-certificate-as-a-file"></a>Alternativa: caricare un certificato come file

Questa sezione illustra come caricare un file di un certificato che si trova nella directory dell'applicazione. 

L'esempio di C# seguente carica un certificato denominato `mycert.pfx` dalla directory `certs` del repository dell'app.

```csharp
using System;
using System.Security.Cryptography.X509Certificates;

...
// Replace the parameter with "~/<relative-path-to-cert-file>".
string certPath = Server.MapPath("~/certs/mycert.pfx");

X509Certificate2 cert = GetCertificate(certPath, signatureBlob.Thumbprint);
...
```

