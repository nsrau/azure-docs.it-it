---
title: Connettersi all'API di servizi multimediali di Azure V3-Python
description: Questo articolo illustra come connettersi all'API di servizi multimediali V3 con Python.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: devx-track-python
ms.openlocfilehash: 1a72b0d90a33b011468b5ac42d529befb7acae42
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/18/2020
ms.locfileid: "94844898"
---
# <a name="connect-to-media-services-v3-api---python"></a>Connettersi all'API di servizi multimediali V3-Python

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Questo articolo illustra come connettersi a servizi multimediali di Azure V3 Python SDK usando il metodo di accesso dell'entità servizio.

## <a name="prerequisites"></a>Prerequisiti

- Scaricare Python da [Python.org](https://www.python.org/downloads/)
- Assicurarsi di impostare la `PATH` variabile di ambiente
- [Creare un account di Servizi multimediali di Azure](./create-account-howto.md). Assicurarsi di ricordare il nome del gruppo di risorse e quello dell'account di Servizi multimediali.
- Seguire la procedura descritta nell'argomento [Accedere alle API](./access-api-howto.md). Registrare l'ID sottoscrizione, l'ID applicazione (ID client), la chiave di autenticazione (segreto) e l'ID tenant necessario nel passaggio successivo.

> [!IMPORTANT]
> Vedere [Convenzioni di denominazione](media-services-apis-overview.md#naming-conventions).

## <a name="install-the-modules"></a>Installare i moduli

Per usare servizi multimediali di Azure con Python, è necessario installare questi moduli.

* Il `azure-mgmt-resource` modulo, che include i moduli di Azure per Active Directory.
* Il `azure-mgmt-media` modulo, che include le entità di servizi multimediali.

Aprire uno strumento da riga di comando e usare i comandi seguenti per installare i moduli.

```
pip3 install azure-mgmt-resource
pip3 install azure-mgmt-media==3.0.0
```

## <a name="connect-to-the-python-client"></a>Connettersi al client Python

1. Creazione di un file con `.py` estensione
1. Aprire il file nell'editor preferito
1. Aggiungere il codice seguente al file. Il codice importa i moduli richiesti e crea l'oggetto credenziali Active Directory necessario per connettersi a servizi multimediali.

      Impostare i valori delle variabili sui valori ottenuti dalle [API di accesso](./access-api-howto.md)

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

1. Esegui il file

## <a name="next-steps"></a>Passaggi successivi

- Usare [Python SDK](https://aka.ms/ams-v3-python-sdk).
- Esaminare la documentazione [Informazioni di riferimento su Python](/python/api/overview/azure/mediaservices/management) di Servizi multimediali.
