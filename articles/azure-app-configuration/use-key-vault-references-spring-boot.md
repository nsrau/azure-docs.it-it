---
title: Esercitazione per l'uso dei riferimenti a Key Vault di Configurazione app di Azure in un'app Java Spring Boot | Microsoft Docs
description: Questa esercitazione illustra come usare i riferimenti a Key Vault di Configurazione app di Azure da un'app Java Spring Boot
services: azure-app-configuration
documentationcenter: ''
author: lisaguthrie
manager: maiye
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.topic: tutorial
ms.date: 12/16/2019
ms.author: lcozzens
ms.custom: mvc
ms.openlocfilehash: d1fb963753577e9518d93262f9c9c7a1cf984005
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2020
ms.locfileid: "77656008"
---
# <a name="tutorial-use-key-vault-references-in-a-java-spring-app"></a>Esercitazione: Usare i riferimenti a Key Vault in un'app Java Spring

Questa esercitazione illustra come usare il servizio Configurazione app di Azure insieme ad Azure Key Vault. Configurazione app e Key Vault sono servizi complementari usati side-by-side nella maggior parte delle distribuzioni di applicazioni.

Configurazione app consente di usare insieme i servizi creando chiavi che fanno riferimento a valori archiviati in Key Vault. Quando crea tali chiavi, Configurazione app archivia gli URI dei valori di Key Vault piuttosto che i valori stessi.

L'applicazione usa il provider client di Configurazione app per recuperare i riferimenti a Key Vault, così come per qualsiasi altra chiave archiviata in Configurazione app. In questo caso, i valori archiviati in Configurazione app sono URI che fanno riferimento ai valori contenuti in Key Vault. Non si tratta dei valori o delle credenziali di Key Vault. Poiché riconosce le chiavi come riferimenti a Key Vault, il provider client usa Key Vault per recuperare i relativi valori.

L'applicazione è responsabile dell'autenticazione corretta sia per Configurazione app che per Key Vault. I due servizi non comunicano direttamente.

Questa esercitazione illustra come implementare i riferimenti a Key Vault nel codice. Si basa sull'app Web presentata nelle guide introduttive. Prima di continuare, completare le procedure descritte in [Creare un'app Java Spring con Configurazione app](./quickstart-java-spring-app.md).

Per completare i passaggi riportati in questa esercitazione, è possibile usare qualsiasi editor di codice. Ad esempio, [Visual Studio Code](https://code.visualstudio.com/) è un editor di codice multipiattaforma disponibile per i sistemi operativi Windows, macOS e Linux.

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
> * Creare una chiave di Configurazione app che faccia riferimento a un valore archiviato in Key Vault.
> * Accedere al valore di questa chiave da un'applicazione Java Spring.

## <a name="prerequisites"></a>Prerequisiti

* Sottoscrizione di Azure: [creare un account gratuito](https://azure.microsoft.com/free/)
* [Java Development Kit (JDK)](https://docs.microsoft.com/java/azure/jdk) versione 8 supportato.
* [Apache Maven](https://maven.apache.org/download.cgi) versione 3.0 o successive.

## <a name="create-a-vault"></a>Creare un insieme di credenziali

1. Selezionare l'opzione **Crea una risorsa** nell'angolo superiore sinistro del portale di Azure:

    ![Output dopo la creazione dell'insieme di credenziali delle chiavi](./media/quickstarts/search-services.png)
1. Nella casella di ricerca immettere **Insieme di credenziali delle chiavi**.
1. Nell'elenco dei risultati scegliere **Insiemi di credenziali delle chiavi** a sinistra.
1. In **Insiemi di credenziali delle chiavi** selezionare **Aggiungi**.
1. Alla destra di **Crea un insieme di credenziali delle chiavi** specificare le informazioni seguenti:
    * Selezionare **Sottoscrizione** per scegliere una sottoscrizione.
    * In **Gruppo di risorse** selezionare **Crea nuovo** e immettere un nome per il gruppo di risorse.
    * In **Nome dell'insieme di credenziali delle chiavi** è necessario un nome univoco. Per questa esercitazione immettere **Contoso-vault2**.
    * Nell'elenco a discesa **Area** scegliere una località.
1. Lasciare invariati i valori predefiniti delle altre opzioni di **Crea un insieme di credenziali delle chiavi**.
1. Selezionare **Create** (Crea).

A questo punto, l'account Azure è l'unico autorizzato ad accedere a questo nuovo insieme di credenziali.

![Output dopo la creazione dell'insieme di credenziali delle chiavi](./media/quickstarts/vault-properties.png)

## <a name="add-a-secret-to-key-vault"></a>Aggiungere un segreto all'istanza di Key Vault

Per aggiungere un segreto all'insieme di credenziali, sono sufficienti alcuni passaggi aggiuntivi. In questo caso aggiungere un messaggio da usare per testare il recupero da Key Vault. Nel messaggio, denominato **Message**, viene archiviato il valore "Hello from Key Vault".

1. Nella pagina delle proprietà di Key Vault selezionare **Segreti**.
1. Selezionare **Genera/Importa**.
1. Nel riquadro **Crea un segreto** immettere i valori seguenti:
    * **Opzioni di caricamento**: immettere **Manual**.
    * **Name**: immettere **Message**.
    * **Value**: immettere **Hello from Key Vault**.
1. Lasciare invariati i valori predefiniti delle altre proprietà di **Crea un segreto**.
1. Selezionare **Create** (Crea).

## <a name="add-a-key-vault-reference-to-app-configuration"></a>Aggiungere a Configurazione app un riferimento a Key Vault

1. Accedere al [portale di Azure](https://portal.azure.com). Selezionare **Tutte le risorse** e quindi l'istanza di archivio di Configurazione app creata nell'argomento di avvio rapido.

1. Selezionare **Esplora configurazioni**.

1. Selezionare **+ Crea** > **Riferimento all'insieme di credenziali delle chiavi** e quindi specificare i valori seguenti:
    * **Chiave**: selezionare **/application/config.keyvaultmessage**
    * **Etichetta**: lasciare vuoto questo valore.
    * **Sottoscrizione**, **Gruppo di risorse** e **Insieme di credenziali delle chiavi**: immettere i valori corrispondenti ai valori dell'insieme di credenziali delle chiavi creati nella sezione precedente.
    * **Segreto**: selezionare il segreto denominato **Message** creato nella sezione precedente.

## <a name="connect-to-key-vault"></a>Connettersi a Key Vault

1. Per questa esercitazione viene usata un'entità servizio per l'autenticazione in Key Vault. Per creare questa entità servizio, usare il comando [az ad sp create-for-rbac](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) dell'interfaccia della riga di comando di Azure:

    ```azurecli
    az ad sp create-for-rbac -n "http://mySP" --sdk-auth
    ```

    Questa operazione restituisce una serie di coppie chiave-valore:

    ```console
    {
    "clientId": "7da18cae-779c-41fc-992e-0527854c6583",
    "clientSecret": "b421b443-1669-4cd7-b5b1-394d5c945002",
    "subscriptionId": "443e30da-feca-47c4-b68f-1636b75e16b3",
    "tenantId": "35ad10f1-7799-4766-9acf-f2d946161b77",
    "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
    "resourceManagerEndpointUrl": "https://management.azure.com/",
    "activeDirectoryGraphResourceId": "https://graph.windows.net/",
    "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
    "galleryEndpointUrl": "https://gallery.azure.com/",
    "managementEndpointUrl": "https://management.core.windows.net/"
    }
    ```

1. Eseguire il comando seguente per consentire all'entità servizio di accedere all'insieme di credenziali delle chiavi:

    ```console
    az keyvault set-policy -n <your-unique-keyvault-name> --spn <clientId-of-your-service-principal> --secret-permissions delete get
    ```

1. Eseguire il comando seguente per ottenere l'ID oggetto, quindi aggiungerlo a Configurazione app.

    ```console
    az ad sp show --id <clientId-of-your-service-principal>
    az role assignment create --role "App Configuration Data Reader" --assignee-object-id <objectId-of-your-service-principal> --resource-group <your-resource-group>
    ```

1. Creare le variabili di ambiente seguenti, usando i valori dell'entità servizio visualizzati nel passaggio precedente:

    * **AZURE_CLIENT_ID**: *clientId*
    * **AZURE_CLIENT_SECRET**: *clientSecret*
    * **AZURE_TENANT_ID**: *tenantId*

> [!NOTE]
> Queste credenziali di Key Vault vengono usate solo all'interno dell'applicazione.  L'applicazione esegue l'autenticazione direttamente con Key Vault usando queste credenziali senza coinvolgere il servizio Configurazione app.  Key Vault fornisce l'autenticazione sia per l'applicazione che per il servizio Configurazione app senza condividere o esporre le chiavi.

## <a name="update-your-code-to-use-a-key-vault-reference"></a>Aggiornare il codice per usare un riferimento a Key Vault

1. Aprire *MessageProperties.java*. Aggiungere una nuova variabile denominata *keyVaultMessage*:

    ```java
    private String keyVaultMessage;

    public String getKeyVaultMessage() {
        return keyVaultMessage;
    }

    public void setKeyVaultMessage(String keyVaultMessage) {
        this.keyVaultMessage = keyVaultMessage;
    }
    ```

1. Aprire *HelloController.java*. Aggiornare il metodo *getMessage* per includere il messaggio recuperato da Key Vault.

    ```java
    @GetMapping
    public String getMessage() {
        return "Message: " + properties.getMessage() + "\nKey Vault message: " + properties.getKeyVaultMessage();
    }
    ```

1. Creare un nuovo file denominato *AzureCredentials.java* e aggiungere il codice seguente.

    ```java
    package com.example;

    import com.azure.core.credential.TokenCredential;
    import com.azure.identity.EnvironmentCredentialBuilder;
    import com.microsoft.azure.spring.cloud.config.AppConfigurationCredentialProvider;
    import com.microsoft.azure.spring.cloud.config.KeyVaultCredentialProvider;

    public class AzureCredentials implements AppConfigurationCredentialProvider, KeyVaultCredentialProvider{

        @Override
        public TokenCredential getKeyVaultCredential(String uri) {
            return getCredential();
        }

        @Override
        public TokenCredential getAppConfigCredential(String uri) {
            return getCredential();
        }

        private TokenCredential getCredential() {
            return new EnvironmentCredentialBuilder().build();
        }

    }
    ```

1. Creare un nuovo file denominato *AppConfiguration.java*. E aggiungere il codice seguente.

    ```java
    package com.example;

    import org.springframework.context.annotation.Bean;
    import org.springframework.context.annotation.Configuration;

    @Configuration
    public class AppConfiguration {

        @Bean
        public AzureCredentials azureCredentials() {
            return new AzureCredentials();
        }
    }
    ```

1. Creare un nuovo file nella directory META-INF delle risorse denominato *spring.factories*.

    ```factories
    org.springframework.cloud.bootstrap.BootstrapConfiguration=\
    com.example.AppConfiguration
    ```

1. Compilare l'applicazione Spring Boot con Maven ed eseguirla, ad esempio:

    ```shell
    mvn clean package
    mvn spring-boot:run
    ```

1. Quando l'applicazione è in esecuzione, è possibile testarla usando *curl*, ad esempio:

      ```shell
      curl -X GET http://localhost:8080/
      ```

    Dovrebbe essere visualizzato il messaggio indicante che è stato eseguito l'accesso nell'archivio di Configurazione app. Viene visualizzato anche il messaggio immesso in Key Vault.

## <a name="clean-up-resources"></a>Pulire le risorse

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stata creata una chiave di Configurazione app che fa riferimento a un valore archiviato in Key Vault. Per informazioni su come usare i flag di funzionalità nell'applicazione Java Spring, continuare con l'esercitazione successiva.

> [!div class="nextstepaction"]
> [Integrazione dell'identità gestita](./quickstart-feature-flag-spring-boot.md)
