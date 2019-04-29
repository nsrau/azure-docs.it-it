---
title: '.NET SDK: Operazioni di gestione in Azure Data Lake archiviazione Gen1 account | Microsoft Docs'
description: Usare .NET SDK in Azure Data Lake Storage Gen1 per eseguire operazioni di gestione di account in Data Lake Storage Gen1
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.assetid: ea57d5a9-2929-4473-9d30-08227912aba7
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 8ab051d49e7ed67e642ef656dfb382ed07763ed2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60877444"
---
# <a name="account-management-operations-on-azure-data-lake-storage-gen1-using-net-sdk"></a>Operazioni di gestione di account in Azure Data Lake Storage Gen1 con .NET SDK
> [!div class="op_single_selector"]
> * [.NET SDK](data-lake-store-get-started-net-sdk.md)
> * [API REST](data-lake-store-get-started-rest-api.md)
> * [Python](data-lake-store-get-started-python.md)
>
>

Questo articolo descrive come eseguire operazioni di gestione di account in Azure Data Lake Storage Gen1 con .NET SDK. Le operazioni di gestione di account includono la creazione di un account Data Lake Storage Gen1, la visualizzazione di un elenco degli account in una sottoscrizione di Azure, l'eliminazione di account e così via.

Per istruzioni su come eseguire le operazioni di gestione di dati in Data Lake Storage Gen1 con .NET SDK, vedere [Operazioni del file system in Data Lake Storage Gen1 con .NET SDK](data-lake-store-data-operations-net-sdk.md).

## <a name="prerequisites"></a>Prerequisiti
* **Visual Studio 2013, 2015 o 2017**. Le istruzioni seguenti fanno riferimento a Visual Studio 2017.

* **Una sottoscrizione di Azure**. Vedere [Ottenere una versione di prova gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/).

## <a name="create-a-net-application"></a>Creare un'applicazione .NET
1. Aprire Visual Studio e creare un'applicazione console.
2. Scegliere **Nuovo** dal menu **File** e quindi fare clic su **Progetto**.
3. In **Nuovo progetto**digitare o selezionare i valori seguenti:

   | Proprietà | Value |
   | --- | --- |
   | Categoria |Templates/Visual C#/Windows |
   | Modello |Applicazione console |
   | NOME |CreateADLApplication |
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
                    _adlsAccountName = "<DATA-LAKE-STORAGE-GEN1-NAME>.azuredatalakestore.net"; 
                    _resourceGroupName = "<RESOURCE-GROUP-NAME>"; 
                    _location = "East US 2";
                    _subId = "<SUBSCRIPTION-ID>";                    
                }
            }
        }

Nelle sezioni rimanenti dell'articolo è possibile vedere come usare i metodi .NET disponibili per eseguire operazioni come autenticazione, caricamento di file e così via.

## <a name="authentication"></a>Authentication

* Per l'autenticazione dell'utente finale per l'applicazione, vedere [Autenticazione dell'utente finale con Data Lake Storage Gen1 con .NET SDK](data-lake-store-end-user-authenticate-net-sdk.md).
* Per l'autenticazione da servizio a servizio per l'applicazione, vedere [Autenticazione da servizio a servizio con Data Lake Storage Gen1 tramite .NET SDK](data-lake-store-service-to-service-authenticate-net-sdk.md).

## <a name="create-client-object"></a>Creare un oggetto client
Il frammento di codice seguente crea l'oggetto client dell'account Data Lake Storage Gen1, usato per inviare richieste di gestione di account al servizio, ad esempio per la creazione di account, l'eliminazione di account e così via.

    // Create client objects and set the subscription ID
    _adlsClient = new DataLakeStoreAccountManagementClient(armCreds) { SubscriptionId = _subId };
    
## <a name="create-a-data-lake-storage-gen1-account"></a>Creare un account Data Lake Storage Gen1
Il frammento di codice seguente crea un account Data Lake Storage Gen1 nella sottoscrizione di Azure fornita durante la creazione dell'oggetto client dell'account Data Lake Storage Gen1.

    // Create Data Lake Storage Gen1 account
    var adlsParameters = new DataLakeStoreAccount(location: _location);
    _adlsClient.Account.Create(_resourceGroupName, _adlsAccountName, adlsParameters);

## <a name="list-all-data-lake-storage-gen1-accounts-within-a-subscription"></a>Visualizzare un elenco di tutti gli account Data Lake Storage Gen1 all'interno di una sottoscrizione
Aggiungere il metodo seguente alla definizione di classe. Il frammento seguente elenca tutti gli account Data Lake Storage Gen1 all'interno di una determinata sottoscrizione di Azure.

    // List all Data Lake Storage Gen1 accounts within the subscription
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

## <a name="delete-a-data-lake-storage-gen1-account"></a>Eliminare un account Data Lake Storage Gen1
Il frammento di codice seguente elimina l'account Data Lake Storage Gen1 creato in precedenza.

    // Delete Data Lake Storage Gen1 account
    _adlsClient.Account.Delete(_resourceGroupName, _adlsAccountName);

## <a name="see-also"></a>Vedere anche 
* [Operazioni del file system in Data Lake Storage Gen1 con .NET SDK](data-lake-store-data-operations-net-sdk.md)
* [Riferimento a .NET SDK di Data Lake Storage Gen1](https://docs.microsoft.com/dotnet/api/overview/azure/data-lake-store?view=azure-dotnet)

## <a name="next-steps"></a>Passaggi successivi
* [Proteggere i dati in Data Lake Storage Gen1](data-lake-store-secure-data.md)
