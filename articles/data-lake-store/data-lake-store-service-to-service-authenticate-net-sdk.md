---
title: 'Autenticazione da servizio a servizio: .NET SDK con Azure Data Lake Storage Gen1 tramite Azure Active Directory | Microsoft Docs'
description: Informazioni su come ottenere l'autenticazione da servizio a servizio con Azure Data Lake Storage Gen1 usando Azure Active Directory con .NET SDK
services: data-lake-store
documentationcenter: ''
author: twooley
manager: cgronlun
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 96c496ef67e26a3079577bf52e9d019d963467b8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65915847"
---
# <a name="service-to-service-authentication-with-azure-data-lake-storage-gen1-using-net-sdk"></a>Autenticazione da servizio a servizio con Azure Data Lake Storage Gen1 tramite .NET SDK
> [!div class="op_single_selector"]
> * [Uso di Java](data-lake-store-service-to-service-authenticate-java.md)
> * [Uso di .NET SDK](data-lake-store-service-to-service-authenticate-net-sdk.md)
> * [Uso di Python](data-lake-store-service-to-service-authenticate-python.md)
> * [Uso dell'API REST](data-lake-store-service-to-service-authenticate-rest-api.md)
>
>

Questo articolo illustra come usare .NET SDK per eseguire l'autenticazione da servizio a servizio con Azure Data Lake Storage Gen1. Per l'autenticazione dell'utente finale con Data Lake Storage Gen1 tramite .NET SDK, vedere [Autenticazione dell'utente finale con Data Lake Storage Gen1 usando .NET SDK](data-lake-store-end-user-authenticate-net-sdk.md).

## <a name="prerequisites"></a>Prerequisiti
* **Visual Studio 2013 o versione successiva**. Le istruzioni seguenti usano Visual Studio 2019.

* **Una sottoscrizione di Azure**. Vedere [Ottenere una versione di prova gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Creare un'applicazione "Web" di Azure Active Directory**. È necessario avere completato i passaggi descritti in [Autenticazione da servizio a servizio con Data Lake Storage Gen1 tramite Azure Active Directory](data-lake-store-service-to-service-authenticate-using-active-directory.md).

## <a name="create-a-net-application"></a>Creare un'applicazione .NET
1. In Visual Studio, selezionare la **File** dal menu **New**e quindi **progetto**.
2. Scegli **App Console (.NET Framework)** , quindi selezionare **successivo**.
3. Nelle **nome progetto**, immettere `CreateADLApplication`, quindi selezionare **Create**.

4. Aggiungere i pacchetti NuGet al progetto.

   1. Fare clic con il pulsante destro del mouse sul nome del progetto in Esplora soluzioni e scegliere **Gestisci pacchetti NuGet**.
   2. Nella scheda **Gestione pacchetti NuGet** assicurarsi che **Origine pacchetto** sia impostato su **nuget.org** e che la casella di controllo **Includi versione preliminare** sia selezionata.
   3. Cercare e installare i pacchetti NuGet seguenti:

      * `Microsoft.Azure.Management.DataLake.Store` - Questa esercitazione usa v2.1.3-preview.
      * `Microsoft.Rest.ClientRuntime.Azure.Authentication` - Questa esercitazione usa la versione 2.2.12.

        ![Aggiungere un'origine NuGet](./media/data-lake-store-get-started-net-sdk/data-lake-store-install-nuget-package.png "Creare un nuovo account Azure Data Lake")
   4. Chiudere **Gestione pacchetti NuGet**.

5. Aprire **Program.cs**, eliminare il codice esistente e quindi includere le istruzioni seguenti per aggiungere riferimenti agli spazi dei nomi.

```csharp
using System;
using System.IO;
using System.Linq;
using System.Text;
using System.Threading;
using System.Collections.Generic;
using System.Security.Cryptography.X509Certificates; // Required only if you are using an Azure AD application created with certificates

using Microsoft.Rest;
using Microsoft.Rest.Azure.Authentication;
using Microsoft.Azure.Management.DataLake.Store;
using Microsoft.Azure.Management.DataLake.Store.Models;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
```

## <a name="service-to-service-authentication-with-client-secret"></a>Autenticazione da servizio a servizio con il segreto client
Aggiungere questo frammento di codice nell'applicazione client .NET. Sostituire i valori segnaposto con i valori recuperati da un'applicazione Web Azure AD indicata come prerequisito. Questo frammento di codice consente di autenticare l'applicazione **in modo non interattivo** con Data Lake Storage Gen1 usando il segreto client/la chiave per l'applicazione Web Azure AD.

```csharp
private static void Main(string[] args)
{
    // Service principal / application authentication with client secret / key
    // Use the client ID of an existing AAD "Web App" application.
    string TENANT = "<AAD-directory-domain>";
    string CLIENTID = "<AAD_WEB_APP_CLIENT_ID>";
    System.Uri ARM_TOKEN_AUDIENCE = new System.Uri(@"https://management.core.windows.net/");
    System.Uri ADL_TOKEN_AUDIENCE = new System.Uri(@"https://datalake.azure.net/");
    string secret_key = "<AAD_WEB_APP_SECRET_KEY>";
    var armCreds = GetCreds_SPI_SecretKey(TENANT, ARM_TOKEN_AUDIENCE, CLIENTID, secret_key);
    var adlCreds = GetCreds_SPI_SecretKey(TENANT, ADL_TOKEN_AUDIENCE, CLIENTID, secret_key);
}
```

Il frammento precedente usa una funzione helper `GetCreds_SPI_SecretKey`. Il codice per questa funzione helper è disponibile [qui su GitHub](https://github.com/Azure-Samples/data-lake-analytics-dotnet-auth-options#getcreds_spi_secretkey).

## <a name="service-to-service-authentication-with-certificate"></a>Autenticazione da servizio a servizio con il certificato

Aggiungere questo frammento di codice nell'applicazione client .NET. Sostituire i valori segnaposto con i valori recuperati da un'applicazione Web Azure AD indicata come prerequisito. Questo frammento di codice consente di autenticare l'applicazione **in modo non interattivo** con Data Lake Storage Gen1 usando il certificato per l'applicazione Web Azure AD. Per le istruzioni su come creare un'applicazione Azure AD, vedere [Create service principal with certificates](../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-self-signed-certificate) (Creare un'entità servizio con i certificati).

```csharp
private static void Main(string[] args)
{
    // Service principal / application authentication with certificate
    // Use the client ID and certificate of an existing AAD "Web App" application.
    string TENANT = "<AAD-directory-domain>";
    string CLIENTID = "<AAD_WEB_APP_CLIENT_ID>";
    System.Uri ARM_TOKEN_AUDIENCE = new System.Uri(@"https://management.core.windows.net/");
    System.Uri ADL_TOKEN_AUDIENCE = new System.Uri(@"https://datalake.azure.net/");
    var cert = new X509Certificate2(@"d:\cert.pfx", "<certpassword>");
    var armCreds = GetCreds_SPI_Cert(TENANT, ARM_TOKEN_AUDIENCE, CLIENTID, cert);
    var adlCreds = GetCreds_SPI_Cert(TENANT, ADL_TOKEN_AUDIENCE, CLIENTID, cert);
}
```

Il frammento precedente usa una funzione helper `GetCreds_SPI_Cert`. Il codice per questa funzione helper è disponibile [qui su GitHub](https://github.com/Azure-Samples/data-lake-analytics-dotnet-auth-options#getcreds_spi_cert).

## <a name="next-steps"></a>Passaggi successivi
In questo articolo si è appreso come usare l'autenticazione da servizio a servizio per eseguire l'autenticazione con Data Lake Storage Gen1 usando .NET SDK. È ora possibile leggere gli articoli seguenti per informazioni su come usare .NET SDK in Data Lake Storage Gen1.

* [Operazioni di gestione di account in Data Lake Storage Gen1 con .NET SDK](data-lake-store-get-started-net-sdk.md)
* [Operazioni dati in Data Lake Storage Gen1 con .NET SDK](data-lake-store-data-operations-net-sdk.md)
