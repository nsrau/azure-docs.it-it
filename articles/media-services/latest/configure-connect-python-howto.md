---
title: Connettersi all'API v3 di servizi multimediali di Azure - Python
description: Informazioni su come connettersi all'API di servizi multimediali v3 con Python.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/15/2019
ms.author: juliako
ms.openlocfilehash: 971e36b600a2c6be516e39ce84ca5780a2f23bbd
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60733097"
---
# <a name="connect-to-media-services-v3-api---python"></a>Connettersi all'API servizi multimediali di v3 - Python

Questo articolo illustra come connettersi a servizi multimediali di Azure v3 Python SDK usando l'accesso dell'entità servizio nel metodo.

## <a name="prerequisites"></a>Prerequisiti

- Scaricare Python [python.org](https://www.python.org/downloads/)
- Assicurarsi di impostare il `PATH` variabile di ambiente
- [Creare un account di Servizi multimediali di Azure](create-account-cli-how-to.md). Assicurarsi di ricordare il nome del gruppo di risorse e il nome dell'account servizi multimediali.
- Seguire i passaggi nel [accesso API](access-api-cli-how-to.md) argomento. Registrare l'ID sottoscrizione, ID applicazione (ID client), la chiave di autenticazione (segreto) e l'ID tenant è necessario nel passaggio successivo.

## <a name="install-the-modules"></a>Installare i moduli

Per usare servizi multimediali di Azure con Python, è necessario installare questi moduli.

* Il `azure-mgmt-resource` modulo, che include i moduli di Azure per Active Directory.
* Il `azure-mgmt-media` modulo, che include le entità di servizi multimediali.

Aprire uno strumento da riga di comando e usare i comandi seguenti per installare i moduli.

```
pip3 install azure-mgmt-resource
pip3 install azure-mgmt-media==1.1.1
```

## <a name="connect-to-the-python-client"></a>Connettersi al client Python

1. Creare un file con un `.py` estensione
1. Aprire il file nell'editor preferito
1. Aggiungere il codice seguente al file. Il codice Importa i moduli necessari e crea l'oggetto credenziali di Active Directory che è necessario connettersi a servizi multimediali.

      Impostare i valori per i valori ottenuti da [accesso ad API](access-api-cli-how-to.md)

      ```
      import adal
      from msrestazure.azure_active_directory import AdalAuthentication
      from msrestazure.azure_cloud import AZURE_PUBLIC_CLOUD
      from azure.mgmt.media import AzureMediaServices
      from azure.mgmt.media.models import MediaService

      RESOURCE = 'https://management.core.windows.net/'
      # Tenant ID for your Azure Subscription
      TENANT_ID = '00000000-0000-0000-000000000000'
      # Your Service Principal App ID
      CLIENT = '00000000-0000-0000-000000000000'
      # Your Service Principal Password
      KEY = '00000000-0000-0000-000000000000'
      # Your Azure Subscription ID
      SUBSCRIPTION_ID = '00000000-0000-0000-000000000000'
      # Your Azure Media Service (AMS) Account Name
      ACCOUNT_NAME = 'amsv3account'
      # Your Resource Group Name
      RESOUCE_GROUP_NAME = 'AMSv3ResourceGroup'

      LOGIN_ENDPOINT = AZURE_PUBLIC_CLOUD.endpoints.active_directory
      RESOURCE = AZURE_PUBLIC_CLOUD.endpoints.active_directory_resource_id

      context = adal.AuthenticationContext(LOGIN_ENDPOINT + '/' + TENANT_ID)
      credentials = AdalAuthentication(
          context.acquire_token_with_client_credentials,
          RESOURCE,
          CLIENT,
          KEY
      )

      # The AMS Client
      # You can now use this object to perform different operations to your AMS account.
      client = AzureMediaServices(credentials, SUBSCRIPTION_ID)

      print("signed in")

      # Now that you are authenticated, you can manipulate the entities.
      # For example, list assets in your AMS account
      print (client.assets.list(RESOUCE_GROUP_NAME, ACCOUNT_NAME).get(0))
      ```

1. Eseguire il file

## <a name="next-steps"></a>Passaggi successivi

- Usare [Python SDK](https://aka.ms/ams-v3-python-sdk).
- Esaminare la documentazione [Informazioni di riferimento su Python](https://aka.ms/ams-v3-python-ref) di Servizi multimediali.
