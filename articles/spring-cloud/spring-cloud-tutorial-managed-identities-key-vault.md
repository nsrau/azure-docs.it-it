---
title: "Esercitazione: Usare un'identità gestita per connettere Key Vault all'app di Azure Spring Cloud"
description: Configurare un'identità gestita per connettere Key Vault a un'app di Azure Spring Cloud
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 07/08/2020
ms.custom: devx-track-java
ms.openlocfilehash: 2b68c4857d3d688c42779be9b5f5fa6e43e0403e
ms.sourcegitcommit: 0820c743038459a218c40ecfb6f60d12cbf538b3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87116895"
---
# <a name="tutorial-use-a-managed-identity-to-connect-key-vault-to-an-azure-spring-cloud-app"></a>Esercitazione: Usare un'identità gestita per connettere Key Vault all'app di Azure Spring Cloud

Questo articolo illustra come creare un'identità gestita per un'app di Azure Spring Cloud e usarla per accedere ad Azure Key Vault.

Azure Key Vault può essere usato per archiviare in modo sicuro e controllare rigorosamente l'accesso a token, password, certificati, chiavi API e altri segreti dell'app. È possibile creare un'identità gestita in Azure Active Directory (AAD) ed eseguire l'autenticazione a qualsiasi servizio che supporti l'autenticazione di Azure AD, incluso Key Vault, senza visualizzare le credenziali nel codice.

## <a name="prerequisites"></a>Prerequisiti

* [Iscriversi per ottenere una sottoscrizione di Azure](https://azure.microsoft.com/free/)
* [Installare l'interfaccia della riga di comando di Azure versione 2.0.67 o successiva](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
* [Installare Maven 3.0 o versione successiva](https://maven.apache.org/download.cgi)

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse
Un gruppo di risorse è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite. Creare un gruppo di risorse che contenga sia l'insieme di credenziali delle chiavi che Spring Cloud usando il comando [az group create](/cli/azure/group?view=azure-cli-latest#az-group-create):

```azurecli-interactive
az group create --name "myResourceGroup" -l "EastUS"
```

## <a name="set-up-your-key-vault"></a>Configurare l'insieme di credenziali delle chiavi
Per creare un insieme di credenziali delle chiavi, usare il comando [az keyvault create](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-create):

> [!Important]
> Ogni insieme di credenziali delle chiavi deve avere un nome univoco. Negli esempi seguenti sostituire <your-keyvault-name> con il nome dell'insieme di credenziali delle chiavi.

```azurecli-interactive
az keyvault create --name "<your-keyvault-name>" -g "myResourceGroup"
```

Prendere nota del valore di `vaultUri` restituito, che avrà il formato "https://<your-keyvault-name>.vault.azure.net". Verrà usato nel passaggio successivo.

È ora possibile inserire un segreto nell'insieme di credenziali delle chiavi usando il comando [az keyvault secret set](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-set):

```azurecli-interactive
az keyvault secret set --vault-name "<your-keyvault-name>" \
    --name "connectionString" \
    --value "jdbc:sqlserver://SERVER.database.windows.net:1433;database=DATABASE;"
```

## <a name="create-azure-spring-cloud-service-and-app"></a>Creare il servizio e l'app di Azure Spring Cloud
Dopo aver installato l'estensione corrispondente, creare un'istanza di Azure Spring Cloud con il comando dell'interfaccia della riga di comando di Azure `az spring-cloud create`. 

```azurecli-interactive
az extension add --name spring-cloud
az spring-cloud create -n "myspringcloud" -g "myResourceGroup"
```
L'esempio seguente crea un'app denominata `springapp` con un'identità gestita assegnata dal sistema, come richiesto dal parametro `--assign-identity`.

```azurecli
az spring-cloud app create -n "springapp" -s "myspringcloud" -g "myResourceGroup" --is-public true --assign-identity
export SERVICE_IDENTITY=$(az spring-cloud app show --name "springapp" -s "myspringcloud" -g "myResourceGroup" | jq -r '.identity.principalId')
```

Prendere nota del valore di `url` restituito, che avrà il formato "https://<your-app-name>.azuremicroservices.io". Verrà usato nel passaggio successivo.


## <a name="grant-your-app-access-to-key-vault"></a>Concedere all'app l'accesso a Key Vault
Usare `az keyvault set-policy` per concedere all'app l'accesso appropriato in Key Vault.
```azurecli
az keyvault set-policy --name "<your-keyvault-name>" --object-id ${SERVICE_IDENTITY} --secret-permissions set get list
```

## <a name="build-a-sample-spring-boot-app-with-spring-boot-starter"></a>Creare un'app Spring Boot di esempio con Spring Boot Starter
Questa app avrà le autorizzazioni per ottenere i segreti da Azure Key Vault. Usare l'utilità di avvio: [Spring Boot Starter per i segreti di Azure Key Vault](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/spring/azure-spring-boot-starter-keyvault-secrets).  Azure Key Vault viene aggiunto come istanza di Spring **PropertySource**.  È possibile accedere facilmente ai segreti archiviati in Azure Key Vault e usarli come qualsiasi proprietà di configurazione esternalizzata, come le proprietà dei file. 

1. Generare un progetto di esempio da start.spring.io con Azure Key Vault Spring Starter. 
    ```azurecli
    curl https://start.spring.io/starter.tgz -d dependencies=web,azure-keyvault-secrets -d baseDir=springapp -d bootVersion=2.3.1.RELEASE | tar -xzvf -
    ```

2. Specificare l'insieme di credenziali delle chiavi nell'app. 

    ```azurecli
    cd springapp
    vim src/main/resources/application.properties
    ```

    Per usare l'identità gestita per le app di Azure Spring Cloud, aggiungere proprietà con il contenuto seguente a src/main/resources/application.properties.

    ```
    azure.keyvault.enabled=true
    azure.keyvault.uri=https://<your-keyvault-name>.vault.azure.net
    ```
    > [!Note] 
    > È necessario aggiungere l'URL dell'insieme di credenziali delle chiavi in `application.properties` come indicato sopra. In caso contrario, tale URL potrebbe non essere acquisito durante il runtime.

3. Aggiungere il codice di esempio a src/main/java/com/example/demo/DemoApplication.java. Recupera la stringa di connessione dall'insieme di credenziali delle chiavi. 

    ```Java
    package com.example.demo;

    import org.springframework.boot.SpringApplication;
    import org.springframework.boot.autoconfigure.SpringBootApplication;
    import org.springframework.beans.factory.annotation.Value;
    import org.springframework.boot.CommandLineRunner;
    import org.springframework.web.bind.annotation.GetMapping;
    import org.springframework.web.bind.annotation.RestController;

    @SpringBootApplication
    @RestController
    public class SecretsApplication implements CommandLineRunner {

        @Value("${connectionString}")
        private String connectionString;

        public static void main(String[] args) {
          SpringApplication.run(SecretsApplication.class, args);
        }

        @GetMapping("get")
        public String get() {
          return connectionString;
        }

        public void run(String... varl) throws Exception {
          System.out.println(String.format("\nConnection String stored in Azure Key Vault:\n%s\n",connectionString));
        }
      }
    ```

    Il file pom.xml contiene la dipendenza di `azure-keyvault-secrets-spring-boot-starter`. Aggiungere questa dipendenza al progetto in pom.xml. 

    ```xml
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-keyvault-secrets-spring-boot-starter</artifactId>
    </dependency>
    ```

4. Creare il pacchetto dell'app di esempio. 

    ```azurecli
    mvn clean package
    ```

5. A questo punto si può distribuire l'app in Azure con il comando dell'interfaccia della riga di comando di Azure `az spring-cloud app deploy`. 

    ```azurecli
    az spring-cloud app deploy -n "springapp" -s "myspringcloud" -g "myResourceGroup" --jar-path target/demo-0.0.1-SNAPSHOT.jar
    ```

6.  Per testare l'app, accedere all'endpoint pubblico o all'endpoint di test.

    ```azurecli
    curl https://myspringcloud-springapp.azuremicroservices.io/get
    ```

    Verrà visualizzato un messaggio che indica che è stato ottenuto il segreto connectionString dall'insieme di credenziali delle chiavi https://<your-keyvault-name>.vault.azure.net/: jdbc:sqlserver://SERVER.database.windows.net:1433;database=DATABASE.

## <a name="build-sample-spring-boot-app-with-java-sdk"></a>Creare un'app Spring Boot di esempio con Java SDK

Questa app di esempio può impostare e ottenere i segreti da Azure Key Vault. La [libreria client dei segreti di Azure Key Vault per Java](https://docs.microsoft.com/java/api/overview/azure/security-keyvault-secrets-readme?view=azure-java-stablelibrary) fornisce il supporto per l'autenticazione basata su token di Azure Active Directory in Azure SDK. Fornisce un set di implementazioni di **TokenCredential** che possono essere usate per creare client Azure SDK per supportare l'autenticazione basata su token di AAD.

La libreria client dei segreti di Azure Key Vault consente di archiviare e controllare in modo sicuro l'accesso a token, password, chiavi API e altri segreti. Usando questa libreria è possibile creare, recuperare, aggiornare, eliminare, ripulire, elencare ed eseguire il backup e il ripristino dei segreti e delle relative versioni.

1. Clonare un progetto di esempio. 

    ```azurecli
    git clone https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples.git
    ```

2. Specificare l'insieme di credenziali delle chiavi nell'app. 

    ```azurecli
    cd Azure-Spring-Cloud-Samples/managed-identity-keyvault
    vim src/main/resources/application.properties
    ```

    Per usare l'identità gestita per le app di Azure Spring Cloud, aggiungere proprietà con il contenuto seguente a *src/main/resources/application.properties*.

    ```
    azure.keyvault.enabled=true
    azure.keyvault.uri=https://<your-keyvault-name>.vault.azure.net
    ```

3. Includere [ManagedIdentityCredentialBuilder](https://docs.microsoft.com/java/api/com.azure.identity.managedidentitycredentialbuilder?view=azure-java-stable) per ottenere il token da Azure Active Directory e [SecretClientBuilder](https://docs.microsoft.com/java/api/com.azure.security.keyvault.secrets.secretclientbuilder?view=azure-java-stable) per impostare o ottenere i segreti da Key Vault nel codice.

    Ottenere l'esempio dal file [MainController.java](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/blob/master/managed-identity-keyvault/src/main/java/com/microsoft/azure/MainController.java#L28) del progetto di esempio clonato.

    Includere anche `azure-identity` e `azure-security-keyvault-secrets` come dipendenza nel file pom.xml. Ottenere l'esempio dal file [pom.xml](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/blob/master/managed-identity-keyvault/pom.xml#L21) del progetto di esempio clonato. 

4. Creare il pacchetto dell'app di esempio. 

    ```azurecli
    mvn clean package
    ```

5. A questo punto, distribuire l'app in Azure con il comando dell'interfaccia della riga di comando di Azure `az spring-cloud app deploy`. 

    ```azurecli
    az spring-cloud app deploy -n "springapp" -s "myspringcloud" -g "myResourceGroup" --jar-path target/asc-managed-identity-keyvault-sample-0.1.0.jar
    ```

6. Accedere all'endpoint pubblico o all'endpoint di test per testare l'app. 

    Ottenere prima di tutto il valore del segreto impostato in Azure Key Vault. 
    ```azurecli
    curl https://myspringcloud-springapp.azuremicroservices.io/secrets/connectionString
    ```

    Verrà visualizzato un messaggio che indica che è stato ottenuto il segreto connectionString dall'insieme di credenziali delle chiavi https://<your-keyvault-name>.vault.azure.net/: jdbc:sqlserver://SERVER.database.windows.net:1433;database=DATABASE.

    Creare quindi un segreto e recuperarlo usando Java SDK. 
    ```azurecli
    curl -X PUT https://myspringcloud-springapp.azuremicroservices.io/secrets/test?value=success

    curl https://myspringcloud-springapp.azuremicroservices.io/secrets/test
    ```

    Verrà visualizzato un messaggio che indica che è stato ottenuto il valore del test del segreto dall'insieme di credenziali delle chiavi https://<your-keyvault-name>.vault.azure.net: success. 

## <a name="next-steps"></a>Passaggi successivi

* [Come accedere a un BLOB di archiviazione con un'identità gestita in Azure Spring Cloud](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/managed-identity-storage-blob)
* [Come abilitare l'identità gestita assegnata dal sistema per l'applicazione Azure Spring Cloud](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-howto-enable-system-assigned-managed-identity)
* [Altre informazioni sulle identità gestite per le risorse di Azure](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/active-directory/managed-identities-azure-resources/overview.md)
* [Autenticazione di Azure Spring Cloud con Key Vault in GitHub Actions](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-github-actions-key-vault)
