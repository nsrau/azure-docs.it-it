---
title: Avvio rapido per l'uso di Configurazione app di Azure con app Python | Microsoft Docs
description: In questa guida di avvio rapido viene creata un'app Python con Configurazione app di Azure per centralizzare l'archiviazione e la gestione delle impostazioni dell'applicazione separatamente dal codice.
services: azure-app-configuration
author: drewbatgit
ms.service: azure-app-configuration
ms.topic: quickstart
ms.custom: devx-track-python
ms.date: 9/17/2020
ms.author: drewbat
ms.openlocfilehash: 954f4edcd10d701d00d9cd23280aaac7c287992d
ms.sourcegitcommit: 83610f637914f09d2a87b98ae7a6ae92122a02f1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/13/2020
ms.locfileid: "91997452"
---
# <a name="quickstart-create-a-python-app-with-azure-app-configuration"></a>Avvio rapido: Crea un'app Python con Configurazione app di Azure

In questa guida di avvio rapido si userà Configurazione app di Azure per centralizzare l'archiviazione e la gestione delle impostazioni di un'applicazione usando la [libreria client di Configurazione app di Azure per Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/appconfiguration/azure-appconfiguration).

## <a name="prerequisites"></a>Prerequisiti

- Sottoscrizione di Azure: [creare un account gratuito](https://azure.microsoft.com/free/)
- Python 2.7 o 3.5 o versione successiva. Per informazioni sulla configurazione di Python in Windows, vedere la [documentazione di Python in Windows]( https://docs.microsoft.com/windows/python/)

## <a name="create-an-app-configuration-store"></a>Creare un archivio di Configurazione app

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

7. Selezionare **Esplora configurazioni** > **Crea** > **Coppia chiave-valore** per aggiungere le coppie chiave-valore seguenti:

    | Chiave | valore |
    |---|---|
    | TestApp:Settings:Message | Dati di Configurazione app di Azure |

    Lasciare vuoti i campi **Etichetta** e **Tipo di contenuto** per il momento.

8. Selezionare **Applica**.

## <a name="setting-up-the-python-app"></a>Configurazione dell'app Python

1. In questa esercitazione verrà creata una nuova directory per il progetto denominato *app-configuration-quickstart*.

    ```console
    mkdir app-configuration-quickstart
    ```

1. Passare alla directory *app-configuration-quickstart* appena creata.

    ```console
    cd app-configuration-quickstart
    ```

1. Installare la libreria client di Configurazione app di Azure usando il comando `pip install`.

    ```console
    pip install azure-appconfiguration
    ```

1. Creare un nuovo file denominato *app-configuration-quickstart.py* nella directory *app-configuration-quickstart* e aggiungere il codice seguente:

    ```python
    import os
    from azure.appconfiguration import AzureAppConfigurationClient, ConfigurationSetting
    
    try:
        print("Azure App Configuration - Python Quickstart")
        # Quickstart code goes here
    except Exception as ex:
        print('Exception:')
        print(ex)
    ```

> [!NOTE]
> I frammenti di codice di questa guida di avvio rapido consentiranno di iniziare a usare la libreria client di Configurazione app per Python. Per l'applicazione, è consigliabile gestire le eccezioni in base alle esigenze. Per altre informazioni sulla gestione delle eccezioni, vedere la [documentazione di Python SDK](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/appconfiguration/azure-appconfiguration).

## <a name="configure-your-app-configuration-connection-string"></a>Configurare la stringa di connessione di Configurazione app

1. Impostare una variabile di ambiente denominata **AZURE_APP_CONFIG_CONNECTION_STRING** sulla chiave di accesso all'archivio di Configurazione app. Eseguire il comando seguente alla riga di comando:

    ```cmd
    setx AZURE_APP_CONFIG_CONNECTION_STRING "connection-string-of-your-app-configuration-store"
    ```

    Se si usa Windows PowerShell, eseguire il comando seguente:

    ```azurepowershell
    $Env:AZURE_APP_CONFIG_CONNECTION_STRING = "connection-string-of-your-app-configuration-store"
    ```

    Se si usa macOS o Linux, eseguire il comando seguente:

    ```console
    export AZURE_APP_CONFIG_CONNECTION_STRING='connection-string-of-your-app-configuration-store'
    ```

2. Riavviare il prompt dei comandi per rendere effettiva la modifica. Stampare il valore della variabile di ambiente per verificare che sia impostato correttamente.

## <a name="code-samples"></a>Esempi di codice

I frammenti di codice di esempio di questa sezione illustrano come eseguire operazioni comuni con la libreria client di Configurazione app per Python. Aggiungere questi frammenti di codice al blocco `try` nel file *app-configuration-quickstart.py* creato in precedenza.

> [!NOTE]
> La libreria client di Configurazione app fa riferimento a un oggetto chiave-valore come `ConfigurationSetting`. Pertanto, in questo articolo, gli oggetti **chiave-valore** nell'archivio di Configurazione app verranno definiti come **impostazioni di configurazione**.

* [Connettersi a un archivio di Configurazione app](#connect-to-an-app-configuration-store)
* [Ottenere un'impostazione di configurazione](#get-a-configuration-setting)
* [Aggiungere un'impostazione di configurazione](#add-a-configuration-setting)
* [Ottenere un elenco di impostazioni di configurazione](#get-a-list-of-configuration-settings)
* [Bloccare un'impostazione di configurazione](#lock-a-configuration-setting)
* [Sbloccare un'impostazione di configurazione](#unlock-a-configuration-setting)
* [Aggiornare un'impostazione di configurazione](#update-a-configuration-setting)
* [Eliminare un'impostazione di configurazione](#delete-a-configuration-setting)

### <a name="connect-to-an-app-configuration-store"></a>Connettersi a un archivio di Configurazione app

Il frammento di codice seguente crea un'istanza di **AzureAppConfigurationClient** usando la stringa di connessione archiviata nelle variabili di ambiente.

```python
    connection_string = os.getenv('AZURE_APP_CONFIG_CONNECTION_STRING')
    app_config_client = AzureAppConfigurationClient.from_connection_string(connection_string)
```

### <a name="get-a-configuration-setting"></a>Ottenere un'impostazione di configurazione

Il frammento di codice seguente recupera un'impostazione di configurazione in base al nome `key`.

```python
    retrieved_config_setting = app_config_client.get_configuration_setting(key='TestApp:Settings:Message')
    print("\nRetrieved configuration setting:")
    print("Key: " + retrieved_config_setting.key + ", Value: " + retrieved_config_setting.value)
```

### <a name="add-a-configuration-setting"></a>Aggiungere un'impostazione di configurazione

Il frammento di codice seguente crea un oggetto `ConfigurationSetting` con i campi `key` e `value` e richiama il metodo `add_configuration_setting`. Questo metodo genererà un'eccezione se si prova ad aggiungere un'impostazione di configurazione già esistente nell'archivio. Per evitare questa eccezione, in alternativa è possibile usare il metodo [set_configuration_setting](#update-a-configuration-setting).

```python
    config_setting = ConfigurationSetting(
        key='TestApp:Settings:NewSetting',
        value='New setting value'
    )
    added_config_setting = app_config_client.add_configuration_setting(config_setting)
    print("\nAdded configuration setting:")
    print("Key: " + added_config_setting.key + ", Value: " + added_config_setting.value)
```

### <a name="get-a-list-of-configuration-settings"></a>Ottenere un elenco di impostazioni di configurazione

Il frammento di codice seguente recupera un elenco di impostazioni di configurazione. Gli argomenti `key_filter` e `label_filter` possono essere specificati per filtrare rispettivamente gli oggetti chiave-valore basati su `key` e `label`. Per altre informazioni sui filtri, vedere come [eseguire query sulle impostazioni di configurazione](./concept-key-value.md#query-key-values).

```python
    filtered_settings_list = app_config_client.list_configuration_settings(key_filter="TestApp*")
    print("\nRetrieved list of configuration settings:")
    for item in filtered_settings_list:
        print("Key: " + item.key + ", Value: " + item.value)
```

### <a name="lock-a-configuration-setting"></a>Bloccare un'impostazione di configurazione

Lo stato di blocco di un oggetto chiave-valore in Configurazione app è indicato dall'attributo `read_only` dell'oggetto `ConfigurationSetting`. Se `read_only` è `True`, l'impostazione è bloccata. È possibile richiamare il metodo `set_read_only` con l'argomento `read_only=True` per bloccare l'impostazione di configurazione.

```python
    locked_config_setting = app_config_client.set_read_only(added_config_setting, read_only=True)
    print("\nRead-only status for " + locked_config_setting.key + ": " + str(locked_config_setting.read_only))
```

### <a name="unlock-a-configuration-setting"></a>Sbloccare un'impostazione di configurazione

Se l'attributo `read_only` di `ConfigurationSetting` è `False`, l'impostazione è sbloccata. È possibile richiamare il metodo `set_read_only` con l'argomento `read_only=False` per sbloccare l'impostazione di configurazione.

```python
    unlocked_config_setting = app_config_client.set_read_only(locked_config_setting, read_only=False)
    print("\nRead-only status for " + unlocked_config_setting.key + ": " + str(unlocked_config_setting.read_only))
```

### <a name="update-a-configuration-setting"></a>Aggiornare un'impostazione di configurazione

È possibile usare il metodo `set_configuration_setting` per aggiornare un'impostazione esistente o crearne una nuova. Il frammento di codice seguente cambia il valore di un'impostazione di configurazione esistente.

```python
    added_config_setting.value = "Value has been updated!"
    updated_config_setting = app_config_client.set_configuration_setting(added_config_setting)
    print("\nUpdated configuration setting:")
    print("Key: " + updated_config_setting.key + ", Value: " + updated_config_setting.value)
```

### <a name="delete-a-configuration-setting"></a>Eliminare un'impostazione di configurazione

Il frammento di codice seguente elimina un'impostazione di configurazione in base al nome `key`.

```python
    deleted_config_setting = app_config_client.delete_configuration_setting(key="TestApp:Settings:NewSetting")
    print("\nDeleted configuration setting:")
    print("Key: " + deleted_config_setting.key + ", Value: " + deleted_config_setting.value)
```

## <a name="run-the-app"></a>Eseguire l'app

In questa guida di avvio rapido è stata creata un'app Python che usa la libreria client di Configurazione app di Azure per recuperare un'impostazione di configurazione creata tramite il portale di Azure, aggiungere una nuova impostazione, recuperare un elenco di impostazioni esistenti, quindi bloccare, sbloccare, aggiornare e infine eliminare un'impostazione.

A questo punto il file *app-configuration-quickstart.py* conterrà il codice seguente:

```python
import os
from azure.appconfiguration import AzureAppConfigurationClient, ConfigurationSetting

try:
    print("Azure App Configuration - Python Quickstart")
    # Quickstart code goes here

    connection_string = os.getenv('AZURE_APP_CONFIG_CONNECTION_STRING')
    app_config_client = AzureAppConfigurationClient.from_connection_string(connection_string)

    retrieved_config_setting = app_config_client.get_configuration_setting(key='TestApp:Settings:Message')
    print("\nRetrieved configuration setting:")
    print("Key: " + retrieved_config_setting.key + ", Value: " + retrieved_config_setting.value)

    config_setting = ConfigurationSetting(
        key='TestApp:Settings:NewSetting',
        value='New setting value'
    )
    added_config_setting = app_config_client.add_configuration_setting(config_setting)
    print("\nAdded configuration setting:")
    print("Key: " + added_config_setting.key + ", Value: " + added_config_setting.value)

    filtered_settings_list = app_config_client.list_configuration_settings(key_filter="TestApp*")
    print("\nRetrieved list of configuration settings:")
    for item in filtered_settings_list:
        print("Key: " + item.key + ", Value: " + item.value)

    locked_config_setting = app_config_client.set_read_only(added_config_setting, read_only=True)
    print("\nRead-only status for " + locked_config_setting.key + ": " + str(locked_config_setting.read_only))

    unlocked_config_setting = app_config_client.set_read_only(locked_config_setting, read_only=False)
    print("\nRead-only status for " + unlocked_config_setting.key + ": " + str(unlocked_config_setting.read_only))

    added_config_setting.value = "Value has been updated!"
    updated_config_setting = app_config_client.set_configuration_setting(added_config_setting)
    print("\nUpdated configuration setting:")
    print("Key: " + updated_config_setting.key + ", Value: " + updated_config_setting.value)

    deleted_config_setting = app_config_client.delete_configuration_setting(key="TestApp:Settings:NewSetting")
    print("\nDeleted configuration setting:")
    print("Key: " + deleted_config_setting.key + ", Value: " + deleted_config_setting.value)

except Exception as ex:
    print('Exception:')
    print(ex)
```

Nella finestra della console passare alla directory contenente il file *app-configuration-quickstart.py* ed eseguire il comando Python seguente per eseguire l'app:

```console
python app-configuration-quickstart.py
```

Dovrebbe venire visualizzato l'output seguente:

```output
Azure App Configuration - Python Quickstart

Retrieved configuration setting:
Key: TestApp:Settings:Message, Value: Data from Azure App Configuration

Added configuration setting:
Key: TestApp:Settings:NewSetting, Value: New setting value

Retrieved list of configuration settings:
Key: TestApp:Settings:Message, Value: Data from Azure App Configuration
Key: TestApp:Settings:NewSetting, Value: New setting value

Read-only status for TestApp:Settings:NewSetting: True

Read-only status for TestApp:Settings:NewSetting: False

Updated configuration setting:
Key: TestApp:Settings:NewSetting, Value: Value has been updated!

Deleted configuration setting:
Key: TestApp:Settings:NewSetting, Value: Value has been updated!
```

## <a name="clean-up-resources"></a>Pulire le risorse


[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Passaggi successivi

In questa guida di avvio rapido è stato creato un nuovo archivio di Configurazione app ed è stato illustrato come accedere alle coppie chiave-valore in un'app Python.

Per altri esempi di codice, vedere:

> [!div class="nextstepaction"]
> [Esempi della libreria client di Configurazione app di Azure](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/appconfiguration/azure-appconfiguration/samples)