---
title: "Autenticazione dell'utente finale: API REST con Gen1 di archiviazione di Azure Data Lake tramite Azure Active Directory | Microsoft Docs"
description: Informazioni su come ottenere l'autenticazione dell'utente finale con Azure Data Lake Storage Gen1 tramite Azure Active Directory usando l'API REST
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 0ef65c23ee1bf4f064695779b71c8616427da204
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60877823"
---
# <a name="end-user-authentication-with-azure-data-lake-storage-gen1-using-rest-api"></a>Autenticazione dell'utente finale con Azure Data Lake Storage Gen1 tramite API REST
> [!div class="op_single_selector"]
> * [Uso di Java](data-lake-store-end-user-authenticate-java-sdk.md)
> * [Uso di .NET SDK](data-lake-store-end-user-authenticate-net-sdk.md)
> * [Uso di Python](data-lake-store-end-user-authenticate-python.md)
> * [Uso dell'API REST](data-lake-store-end-user-authenticate-rest-api.md)
> 
>  

Questo articolo illustra come usare l'API REST per eseguire l'autenticazione dell'utente finale con Azure Data Lake Storage Gen1. Per l'autenticazione da servizio a servizio con Data Lake Storage Gen1 tramite API REST, vedere [Autenticazione da servizio a servizio con Data Lake Storage Gen1 tramite API REST](data-lake-store-service-to-service-authenticate-rest-api.md).

## <a name="prerequisites"></a>Prerequisiti

* **Una sottoscrizione di Azure**. Vedere [Ottenere una versione di prova gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Creare un'applicazione "nativa" di Azure Active Directory**. È necessario avere completato i passaggi descritti in [Autenticazione dell'utente finale con Data Lake Storage Gen1 usando Azure Active Directory](data-lake-store-end-user-authenticate-using-active-directory.md).

* **[cURL](https://curl.haxx.se/)** . In questo articolo viene usato cURL per illustrare come effettuare chiamate API REST con un account Data Lake Storage Gen1.

## <a name="end-user-authentication"></a>Autenticazione dell'utente finale
L'autenticazione dell'utente finale è l'approccio consigliato se si vuole che un utente finale acceda all'applicazione tramite Azure AD. L'applicazione può quindi accedere alle risorse di Azure con lo stesso livello di accesso dell'utente che ha effettuato l'accesso. L'utente deve fornire le sue credenziali periodicamente affinché l'applicazione possa mantenere attivo l'accesso.

L'accesso effettuato dall'utente finale comporta l'assegnazione di un token di accesso e un token di aggiornamento all'applicazione. Il token di accesso viene associato a ogni richiesta effettuata a Data Lake Storage Gen1 o Data Lake Analytics ed è valido per un'ora per impostazione predefinita. Il token di aggiornamento può essere usato per ottenere un nuovo token di accesso, ed è valido per un massimo di due settimane per impostazione predefinita (se usato di frequente). È possibile usare due diversi approcci per l'accesso degli utenti finali.

In questo scenario, l'applicazione richiede all'utente di accedere e tutte le operazioni vengono eseguite nel contesto utente. Eseguire la procedura seguente:

1. Tramite l'applicazione, reindirizzare l'utente all'URL seguente:

        https://login.microsoftonline.com/<TENANT-ID>/oauth2/authorize?client_id=<APPLICATION-ID>&response_type=code&redirect_uri=<REDIRECT-URI>

   > [!NOTE]
   > \<REDIRECT-URI&gt; deve essere codificato per essere usato in un URL. Pertanto, per https://localhost , usare `https%3A%2F%2Flocalhost` )

    Per questa esercitazione, è possibile sostituire i valori segnaposto nell'URL precedente e incollare quest'ultimo nella barra degli indirizzi di un web browser. Si verrà reindirizzati per l'autenticazione tramite l'accesso ad Azure. Dopo aver eseguito correttamente l'accesso, la risposta verrà visualizzata nella barra degli indirizzi del browser. La risposta sarà nel formato seguente:

        http://localhost/?code=<AUTHORIZATION-CODE>&session_state=<GUID>

2. Acquisire il codice di autorizzazione dalla risposta. Per questa esercitazione è possibile copiare il codice di autorizzazione dalla barra degli indirizzi del Web browser e passarla nella richiesta POST all'endpoint di token, come illustrato nel frammento di codice di seguito:

        curl -X POST https://login.microsoftonline.com/<TENANT-ID>/oauth2/token \
        -F redirect_uri=<REDIRECT-URI> \
        -F grant_type=authorization_code \
        -F resource=https://management.core.windows.net/ \
        -F client_id=<APPLICATION-ID> \
        -F code=<AUTHORIZATION-CODE>

   > [!NOTE]
   > In questo caso non è necessario codificare \<REDIRECT-URI>.
   > 
   > 

3. La risposta è un oggetto JSON che contiene un token di accesso, ad esempio, `"access_token": "<ACCESS_TOKEN>"`, e un token di aggiornamento, ad esempio, `"refresh_token": "<REFRESH_TOKEN>"`. L'applicazione usa il token di accesso quando si accede ad Azure Data Lake Storage Gen1 e il token di aggiornamento quando un token di accesso scade per ottenerne un altro.

        {"token_type":"Bearer","scope":"user_impersonation","expires_in":"3599","expires_on":"1461865782","not_before":    "1461861882","resource":"https://management.core.windows.net/","access_token":"<REDACTED>","refresh_token":"<REDACTED>","id_token":"<REDACTED>"}

4. Quando il token di accesso scade, è possibile richiederne uno nuovo tramite il token di aggiornamento come illustrato nel frammento di codice seguente:

        curl -X POST https://login.microsoftonline.com/<TENANT-ID>/oauth2/token  \
             -F grant_type=refresh_token \
             -F resource=https://management.core.windows.net/ \
             -F client_id=<APPLICATION-ID> \
             -F refresh_token=<REFRESH-TOKEN>

Per altre informazioni sull'autenticazione utente interattiva, vedere [Flusso di concessione del codice di autorizzazione](https://msdn.microsoft.com/library/azure/dn645542.aspx).

## <a name="next-steps"></a>Passaggi successivi
In questo articolo è stato descritto come usare l'autenticazione da servizio a servizio per eseguire l'autenticazione con Azure Data Lake Storage Gen1 usando l'API REST. È ora possibile leggere gli articoli seguenti per informazioni su come usare l'API REST in Azure Data Lake Storage Gen1.

* [Operazioni di gestione di account in Data Lake Storage Gen1 con API REST](data-lake-store-get-started-rest-api.md)
* [Operazioni dati in Data Lake Storage Gen1 con API REST](data-lake-store-data-operations-rest-api.md)

