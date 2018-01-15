---
title: '.NET SDK: Operazioni di gestione di account in Azure Data Lake Store | Microsoft Docs'
description: Usare Azure Data Lake Store con .NET SDK per eseguire operazioni di gestione di account in Data Lake Store
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: ea57d5a9-2929-4473-9d30-08227912aba7
ms.service: data-lake-store
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/09/2018
ms.author: nitinme
ms.openlocfilehash: 682e8c9fc9e7f16d6b69cd73535c76e6c53c6b49
ms.sourcegitcommit: 9292e15fc80cc9df3e62731bafdcb0bb98c256e1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/10/2018
---
# <a name="account-management-operations-on-azure-data-lake-store-using-net-sdk"></a>Operazioni di gestione di account in Azure Data Lake Store con .NET SDK
> [!div class="op_single_selector"]
> * [.NET SDK](data-lake-store-get-started-net-sdk.md)
> * [API REST](data-lake-store-get-started-rest-api.md)
> * [Python](data-lake-store-get-started-python.md)
>
>

Questo articolo illustra come eseguire operazioni di gestione degli account in Data Lake Store con .NET SDK. Le operazioni di gestione di account includono la creazione di un account Data Lake Store, la visualizzazione di un elenco degli account in una sottoscrizione di Azure, l'eliminazione di account e così via.

Per istruzioni su come eseguire le operazioni di gestione di dati in Data Lake Store con .NET SDK, vedere [Operazioni del file system in Data Lake Store con .NET SDK](data-lake-store-data-operations-net-sdk.md).

## <a name="prerequisites"></a>Prerequisiti
* **Visual Studio 2013, 2015 o 2017**. Le istruzioni seguenti fanno riferimento a Visual Studio 2017.

* **Una sottoscrizione di Azure**. Vedere [Ottenere una versione di prova gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/).

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
        using System.Security.Cryptography.X509Certificates; // Required only if you are using an Azure AD application created with certificates
                
        using Microsoft.Rest;
        using Microsoft.Rest.Azure.Authentication;
        using Microsoft.Azure.Management.DataLake.Store;
        using Microsoft.Azure.Management.DataLake.Store.Models;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;

7. Dichiarare le variabili e fornire i valori per i segnaposto. Assicurarsi anche che il percorso locale e il nome file forniti siano presenti nel computer.

        namespace SdkSample
        {
            class Program
            {
                private static DataLakeStoreAccountManagementClient _adlsClient;
                
                private static string _adlsAccountName;
                private static string _resourceGroupName;
                private static string _location;
                private static string _subId;

                private static void Main(string[] args)
                {
                    _adlsAccountName = "<DATA-LAKE-STORE-NAME>"; 
                    _resourceGroupName = "<RESOURCE-GROUP-NAME>"; 
                    _location = "East US 2";
                    _subId = "<SUBSCRIPTION-ID>";                    
                }
            }
        }

Nelle sezioni rimanenti dell'articolo è possibile vedere come usare i metodi .NET disponibili per eseguire operazioni come autenticazione, caricamento di file e così via.

## <a name="authentication"></a>Autenticazione

* Per l'autenticazione dell'utente finale per l'applicazione, vedere [End-user authentication with Data Lake Store using .NET SDK](data-lake-store-end-user-authenticate-net-sdk.md) (Autenticazione dell'utente finale con Data Lake Store tramite .NET SDK).
* Per l'autenticazione da servizio a servizio per l'applicazione, vedere [Service-to-service authentication with Data Lake Store using .NET SDK](data-lake-store-service-to-service-authenticate-net-sdk.md) (Autenticazione da servizio a servizio con Data Lake Store tramite .NET SDK).

## <a name="create-client-object"></a>Creare un oggetto client
Il frammento di codice seguente crea l'oggetto client account Data Lake Store, usato per inviare richieste di gestione di account al servizio, ad esempio per la creazione di account, l'eliminazione di account e così via.

    // Create client objects and set the subscription ID
    _adlsClient = new DataLakeStoreAccountManagementClient(armCreds) { SubscriptionId = _subId };
    
## <a name="create-a-data-lake-store-account"></a>Creare un account Archivio Data Lake
Il frammento di codice seguente crea un account Data Lake Store nella sottoscrizione di Azure fornita durante la creazione dell'oggetto client account Data Lake Store.

    // Create Data Lake Store account
    var adlsParameters = new DataLakeStoreAccount(location: _location);
    _adlsClient.Account.Create(_resourceGroupName, _adlsAccountName, adlsParameters);

## <a name="list-all-data-lake-store-accounts-within-a-subscription"></a>Elencare tutti gli account Data Lake Store all'interno di una sottoscrizione
Aggiungere il metodo seguente alla definizione di classe. Il frammento seguente elenca tutti gli account Data Lake Store all'interno di una determinata sottoscrizione di Azure.

    // List all Data Lake Store accounts within the subscription
    public static List<DataLakeStoreAccountBasic> ListAdlStoreAccounts()
    {
        var response = _adlsClient.Account.List(_adlsAccountName);
        var accounts = new List<DataLakeStoreAccountBasic>(response);

        while (response.NextPageLink != null)
        {
            response = _adlsClient.Account.ListNext(response.NextPageLink);
            accounts.AddRange(response);
        }

        return accounts;
    }

## <a name="delete-a-data-lake-store-account"></a>Eliminare un account Archivio Data Lake
Il frammento di codice seguente elimina l'account Data Lake Store creato in precedenza.

    // Delete Data Lake Store account
    _adlsClient.Account.Delete(_resourceGroupName, _adlsAccountName);

## <a name="see-also"></a>Vedere anche 
* [Operazioni del file system in Data Lake Store con .NET SDK](data-lake-store-data-operations-net-sdk.md)
* [Informazioni di riferimento su .NET SDK con Data Lake Store](https://docs.microsoft.com/dotnet/api/overview/azure/data-lake-store?view=azure-dotnet)

## <a name="next-steps"></a>Passaggi successivi
* [Proteggere i dati in Data Lake Store](data-lake-store-secure-data.md)
