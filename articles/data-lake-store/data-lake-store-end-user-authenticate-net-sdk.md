---
title: 'Autenticazione dell''utente finale: .NET SDK con Data Lake Store tramite Azure Active Directory | Microsoft Docs'
description: Informazioni su come ottenere l'autenticazione dell'utente finale con Data Lake Store tramite Azure Active Directory con .NET SDK
services: data-lake-store
documentationcenter: 
author: nitinme
manager: cgronlun
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/11/2017
ms.author: nitinme
ms.openlocfilehash: 43cb2225d7fbf2fc7f8d93c78415f0f2c1a3fa25
ms.sourcegitcommit: d03907a25fb7f22bec6a33c9c91b877897e96197
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/12/2017
---
# <a name="end-user-authentication-with-data-lake-store-using-net-sdk"></a>Autenticazione dell'utente finale con Data Lake Store tramite .NET SDK
> [!div class="op_single_selector"]
> * [Uso di Java](data-lake-store-end-user-authenticate-java-sdk.md)
> * [Uso di .NET SDK](data-lake-store-end-user-authenticate-net-sdk.md)
> * [Uso di Python](data-lake-store-end-user-authenticate-python.md)
> * [Uso dell'API REST](data-lake-store-end-user-authenticate-rest-api.md)
> 
>  

Questo articolo illustra come usare .NET SDK per eseguire l'autenticazione dell'utente finale con Azure Data Lake Store. Per l'autenticazione da servizio a servizio con Data Lake Store tramite .NET SDK, vedere [Service-to-service authentication with Data Lake Store using NET SDK](data-lake-store-service-to-service-authenticate-net-sdk.md) (Autenticazione da servizio a servizio con Data Lake Store tramite NET SDK).

## <a name="prerequisites"></a>Prerequisiti
* **Visual Studio 2013, 2015 o 2017**. Le istruzioni seguenti fanno riferimento a Visual Studio 2017.

* **Una sottoscrizione di Azure**. Vedere [Ottenere una versione di valutazione gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Creare un'applicazione "nativa" di Azure Active Directory**. È necessario avere completato i passaggi descritti in [Autenticazione dell'utente finale con Data Lake Store usando Azure Active Directory](data-lake-store-end-user-authenticate-using-active-directory.md).

## <a name="create-a-net-application"></a>Creare un'applicazione .NET
1. Aprire Visual Studio e creare un'applicazione console.
2. Scegliere **Nuovo** dal menu **File** e quindi fare clic su **Progetto**.
3. In **Nuovo progetto**digitare o selezionare i valori seguenti:

   | Proprietà | Valore |
   | --- | --- |
   | Categoria |Templates/Visual C#/Windows |
   | Modello |Applicazione console |
   | Nome |CreateADLApplication |

4. Fare clic su **OK** per creare il progetto.

5. Aggiungere i pacchetti NuGet al progetto.

   1. Fare clic con il pulsante destro del mouse sul nome del progetto in Esplora soluzioni e scegliere **Gestisci pacchetti NuGet**.
   2. Nella scheda **Gestione pacchetti NuGet** assicurarsi che **Origine pacchetto** sia impostato su **nuget.org** e che la casella di controllo **Includi versione preliminare** sia selezionata.
   3. Cercare e installare i pacchetti NuGet seguenti:

      * `Microsoft.Azure.Management.DataLake.Store` - Questa esercitazione usa v2.1.3-preview.
      * `Microsoft.Rest.ClientRuntime.Azure.Authentication` - Questa esercitazione usa la versione 2.2.12.

        ![Aggiungere un'origine NuGet](./media/data-lake-store-get-started-net-sdk/data-lake-store-install-nuget-package.png "Creare un nuovo account Azure Data Lake")
   4. Chiudere **Gestione pacchetti NuGet**.

6. Aprire **Program.cs**, eliminare il codice esistente e quindi includere le istruzioni seguenti per aggiungere riferimenti agli spazi dei nomi.

        using System;
        using System.IO;
        using System.Linq;
        using System.Text;
        using System.Threading;
        using System.Collections.Generic;
                
        using Microsoft.Rest;
        using Microsoft.Rest.Azure.Authentication;
        using Microsoft.Azure.Management.DataLake.Store;
        using Microsoft.Azure.Management.DataLake.Store.Models;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;
        

## <a name="end-user-authentication"></a>Autenticazione dell'utente finale
Aggiungere questo frammento di codice nell'applicazione client .NET. Sostituire i valori segnaposto con i valori recuperati da un'applicazione nativa Azure AD indicata come prerequisito. Questo frammento di codice consente di autenticare l'applicazione **in modo interattivo** con Data Lake Store, ovvero viene chiesto di immettere le credenziali di Azure.

Per semplicità, il frammento seguente usa valori predefiniti per ID client e URI di reindirizzamento validi con qualsiasi sottoscrizione di Azure. Nel frammento seguente è sufficiente fornire il valore per l'ID del tenant. È possibile recuperare l'ID del tenant usando le istruzioni fornite in [ottenere l'ID tenant](../azure-resource-manager/resource-group-create-service-principal-portal.md#get-tenant-id).
    
    private static void Main(string[] args)
    {
        //User login via interactive popup
        string TENANT = "<AAD-directory-domain>";
        string CLIENTID = "1950a258-227b-4e31-a9cf-717495945fc2";
        System.Uri ARM_TOKEN_AUDIENCE = new System.Uri(@"https://management.core.windows.net/");
        System.Uri ADL_TOKEN_AUDIENCE = new System.Uri(@"https://datalake.azure.net/");
        string MY_DOCUMENTS = System.Environment.GetFolderPath(System.Environment.SpecialFolder.MyDocuments);
        string TOKEN_CACHE_PATH = System.IO.Path.Combine(MY_DOCUMENTS, "my.tokencache");
        var tokenCache = GetTokenCache(TOKEN_CACHE_PATH);
        var armCreds = GetCreds_User_Popup(TENANT, ARM_TOKEN_AUDIENCE, CLIENTID, tokenCache);
        var adlCreds = GetCreds_User_Popup(TENANT, ADL_TOKEN_AUDIENCE, CLIENTID, tokenCache);
    }

Informazioni utili sul frammento di codice precedente:

* Il frammento precedente usa le funzioni helper `GetTokenCache` e `GetCreds_User_Popup`. Il codice per queste funzioni helper è disponibile [in Github](https://github.com/Azure-Samples/data-lake-analytics-dotnet-auth-options#gettokencache).
* Per velocizzare il completamento dell'esercitazione, il frammento usa un ID client applicazione nativa disponibile per impostazione predefinita per tutte le sottoscrizioni di Azure. È quindi possibile **usare questo frammento così com'è nell'applicazione**.
* Se, tuttavia, si vuole usare il proprio dominio di Azure AD e il proprio ID client dell'applicazione, è necessario creare un'applicazione nativa di Azure AD e quindi usare l'ID tenant di Azure AD, l'ID client e l'URI di reindirizzamento per l'applicazione creata. Per istruzioni, vedere [Autenticazione dell'utente finale con Data Lake Store tramite Azure Active Directory](data-lake-store-end-user-authenticate-using-active-directory.md).

  
## <a name="next-steps"></a>Passaggi successivi
In questo articolo si è appreso come usare l'autenticazione dell'utente finale per eseguire l'autenticazione con Azure Data Lake Store usando .NET SDK. È ora possibile leggere gli articoli seguenti per informazioni su come usare .NET SDK in Azure Data Lake Store.

* [Operazioni di gestione di account in Data Lake Store con .NET SDK](data-lake-store-get-started-net-sdk.md)
* [Operazioni dati in Data Lake Store con .NET SDK](data-lake-store-data-operations-net-sdk.md)

