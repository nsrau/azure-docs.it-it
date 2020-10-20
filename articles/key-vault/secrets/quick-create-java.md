---
title: 'Avvio rapido: Libreria client di Azure Key Vault per Java'
description: Fornisce criteri di formato e contenuto per la scrittura di argomenti di avvio rapido per le librerie client di Azure SDK.
author: msmbaldwin
ms.custom: devx-track-java
ms.author: mbaldwin
ms.date: 10/20/2019
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.openlocfilehash: 87d7bbaa40226e02726b92cf7f7705c8028149f7
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92019631"
---
# <a name="quickstart-azure-key-vault-secret-client-library-for-java"></a>Avvio rapido: Libreria client dei segreti di Azure Key Vault per Java

Introduzione alla libreria client dei segreti di Azure Key Vault per Java. Seguire questi passaggi per installare il pacchetto e provare il codice di esempio per le attività di base.

Risorse aggiuntive:

* [Codice sorgente](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault)
* [Documentazione di riferimento delle API](https://azure.github.io/azure-sdk-for-java)
* [Documentazione del prodotto](index.yml)
* [Esempi](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault/azure-security-keyvault-secrets/src/samples/java/com/azure/security/keyvault/secrets)

## <a name="prerequisites"></a>Prerequisiti

- Una sottoscrizione di Azure: [creare un account gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Java Development Kit (JDK)](/java/azure/jdk/) versione 8 o successiva
- [Apache Maven](https://maven.apache.org)
- [Interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli)

Questo argomento di avvio rapido presuppone l'esecuzione dell'[interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli) e di [Apache Maven](https://maven.apache.org) in una finestra del terminale Linux.

## <a name="setting-up"></a>Configurazione

Questo argomento di avvio rapido usa la libreria di identità di Azure con l'interfaccia della riga di comando di Azure per autenticare l'utente nei servizi di Azure. Gli sviluppatori possono anche usare Visual Studio o Visual Studio Code per autenticare le chiamate. Per altre informazioni, vedere [Autenticare il client con la libreria client Azure Identity](https://docs.microsoft.com/java/api/overview/azure/identity-readme).

### <a name="sign-in-to-azure"></a>Accedere ad Azure

1. Eseguire il comando `login`.

    ```azurecli-interactive
    az login
    ```

    Se l'interfaccia della riga di comando può aprire il browser predefinito, eseguirà questa operazione e caricherà una pagina di accesso di Azure.

    In caso contrario, aprire una pagina del browser all'indirizzo [https://aka.ms/devicelogin](https://aka.ms/devicelogin) e immettere il codice di autorizzazione visualizzato nel terminale.

2. Accedere con le credenziali dell'account nel browser.

### <a name="create-new-java-console-app"></a>Creare una nuova app console Java

In una finestra della console usare il comando `mvn` per creare una nuova app console Java denominata `akv-java`.

```console
mvn archetype:generate -DgroupId=com.keyvault.quickstart
                       -DartifactId=akv-java
                       -DarchetypeArtifactId=maven-archetype-quickstart
                       -DarchetypeVersion=1.4
                       -DinteractiveMode=false
```

L'output della generazione del progetto sarà simile al seguente:

```console
[INFO] ----------------------------------------------------------------------------
[INFO] Using following parameters for creating project from Archetype: maven-archetype-quickstart:1.4
[INFO] ----------------------------------------------------------------------------
[INFO] Parameter: groupId, Value: com.keyvault.quickstart
[INFO] Parameter: artifactId, Value: akv-java
[INFO] Parameter: version, Value: 1.0-SNAPSHOT
[INFO] Parameter: package, Value: com.keyvault.quickstart
[INFO] Parameter: packageInPathFormat, Value: com/keyvault/quickstart
[INFO] Parameter: package, Value: com.keyvault.quickstart
[INFO] Parameter: groupId, Value: com.keyvault.quickstart
[INFO] Parameter: artifactId, Value: akv-java
[INFO] Parameter: version, Value: 1.0-SNAPSHOT
[INFO] Project created from Archetype in dir: /home/user/quickstarts/akv-java
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time:  38.124 s
[INFO] Finished at: 2019-11-15T13:19:06-08:00
[INFO] ------------------------------------------------------------------------
```

Passare alla cartella akv-java/ appena creata.

```console
cd akv-java
```

### <a name="install-the-package"></a>Installare il pacchetto

Aprire il file *pom.xml* nell'editor di testo. Aggiungere gli elementi di dipendenza seguenti al gruppo di dipendenze.

```xml
    <dependency>
      <groupId>com.azure</groupId>
      <artifactId>azure-security-keyvault-secrets</artifactId>
      <version>4.0.0</version>
    </dependency>

    <dependency>
      <groupId>com.azure</groupId>
      <artifactId>azure-identity</artifactId>
      <version>1.0.0</version>
    </dependency>
```

### <a name="create-a-resource-group-and-key-vault"></a>Creare un gruppo di risorse e un insieme di credenziali delle chiavi

[!INCLUDE [Create a resource group and key vault](../../../includes/key-vault-rg-kv-creation.md)]

#### <a name="grant-access-to-your-key-vault"></a>Concedere l'accesso all'insieme di credenziali delle chiavi

Creare un criterio di accesso per l'insieme di credenziali delle chiavi che concede l'autorizzazione per il segreto all'account utente

```console
az keyvault set-policy --name <YourKeyVaultName> --upn user@domain.com --secret-permissions delete get list set
```

#### <a name="set-environment-variables"></a>Impostare le variabili di ambiente

Questa applicazione usa il nome dell'insieme di credenziali delle chiavi come variabile di ambiente denominata `KEY_VAULT_NAME`.

Windows
```cmd
set KEY_VAULT_NAME=<your-key-vault-name>
````
Windows PowerShell
```powershell
$Env:KEY_VAULT_NAME=<your-key-vault-name>
```

macOS o Linux
```cmd
export KEY_VAULT_NAME=<your-key-vault-name>
```

## <a name="object-model"></a>Modello a oggetti

La libreria client dei segreti di Azure Key Vault per Java consente di gestire i segreti. La sezione [Esempi di codice](#code-examples) illustra come creare un client e come impostare, recuperare ed eliminare un segreto.

L'intera app console è disponibile [di seguito](#sample-code).

## <a name="code-examples"></a>Esempi di codice

### <a name="add-directives"></a>Aggiungere le direttive

Aggiungere le direttive seguenti all'inizio del codice:

```java
import com.azure.identity.DefaultAzureCredentialBuilder;

import com.azure.security.keyvault.secrets.SecretClient;
import com.azure.security.keyvault.secrets.SecretClientBuilder;
import com.azure.security.keyvault.secrets.models.KeyVaultSecret;
```

### <a name="authenticate-and-create-a-client"></a>Autenticare e creare un client

In questo argomento di avvio rapido viene usato l'utente connesso per eseguire l'autenticazione in Key Vault, che è il metodo preferito per lo sviluppo locale. Per le applicazioni distribuite in Azure, l'identità gestita deve essere assegnata al servizio app o alla macchina virtuale. Per altre informazioni, vedere [Informazioni sulle identità gestite per le risorse di Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

Nell'esempio seguente, il nome dell'insieme di credenziali delle chiavi viene esteso al relativo URI, nel formato "https://\<your-key-vault-name\>.vault.azure.net". Questo esempio usa la classe ['DefaultAzureCredential()'](https://docs.microsoft.com/java/api/com.azure.identity.defaultazurecredential), che consente di usare lo stesso codice in ambienti diversi con opzioni diverse per specificare l'identità. Per altre informazioni, vedere [Autenticazione DefaultAzureCredential](https://docs.microsoft.com/java/api/overview/azure/identity-readme). 

```java
String keyVaultName = System.getenv("KEY_VAULT_NAME");
String kvUri = "https://" + keyVaultName + ".vault.azure.net";

SecretClient secretClient = new SecretClientBuilder()
    .vaultUrl(kvUri)
    .credential(new DefaultAzureCredentialBuilder().build())
    .buildClient();
```

### <a name="save-a-secret"></a>Salvare un segreto

Ora che l'applicazione è autenticata, è possibile inserire un segreto nell'insieme di credenziali delle chiavi usando il metodo `secretClient.setSecret`. È necessario specificare un nome per il segreto. In questo esempio è stato assegnato il valore "mySecret" alla variabile `secretName`.  

```java
secretClient.setSecret(new KeyVaultSecret(secretName, secretValue));
```

È possibile verificare che il segreto sia stato impostato con il comando [az keyvault secret show](/cli/azure/keyvault/secret?#az-keyvault-secret-show):

```azurecli
az keyvault secret show --vault-name <your-unique-keyvault-name> --name mySecret
```

### <a name="retrieve-a-secret"></a>Recuperare un segreto

È ora possibile recuperare il valore impostato in precedenza con il metodo `secretClient.getSecret`

```java
KeyVaultSecret retrievedSecret = secretClient.getSecret(secretName);
 ```

e accedere al valore del segreto recuperato con `retrievedSecret.getValue()`.

### <a name="delete-a-secret"></a>consente di eliminare un segreto

Per eliminare infine il segreto dall'insieme di credenziali delle chiavi, è possibile usare il metodo `secretClient.beginDeleteSecret`.

```java
secretClient.beginDeleteSecret(secretName);
```

È possibile verificare che il segreto non sia più dotato del comando [az keyvault secret show](/cli/azure/keyvault/secret?#az-keyvault-secret-show):

```azurecli
az keyvault secret show --vault-name <your-unique-keyvault-name> --name mySecret
```

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non sono più necessari, è possibile usare l'interfaccia della riga di comando di Azure o Azure PowerShell per rimuovere l'insieme di credenziali delle chiavi e il gruppo di risorse corrispondente.

```azurecli
az group delete -g "myResourceGroup"
```

```azurepowershell
Remove-AzResourceGroup -Name "myResourceGroup"
```

## <a name="sample-code"></a>Codice di esempio

```java
package com.keyvault.quickstart;

import java.io.Console;   

import com.azure.identity.DefaultAzureCredentialBuilder;

import com.azure.security.keyvault.secrets.SecretClient;
import com.azure.security.keyvault.secrets.SecretClientBuilder;
import com.azure.security.keyvault.secrets.models.KeyVaultSecret;

public class App {

    public static void main(String[] args) throws InterruptedException, IllegalArgumentException {

        String keyVaultName = System.getenv("KEY_VAULT_NAME");
        String kvUri = "https://" + keyVaultName + ".vault.azure.net";

        System.out.printf("key vault name = %s and kv uri = %s \n", keyVaultName, kvUri);

        SecretClient secretClient = new SecretClientBuilder()
            .vaultUrl(kvUri)
            .credential(new DefaultAzureCredentialBuilder().build())
            .buildClient();


        Console con = System.console();  

        String secretName = "mySecret";

        System.out.println("Input the value of your secret > ");
        String secretValue = con.readLine();

        System.out.print("Creating a secret in " + keyVaultName + " called '" + secretName + "' with the value '" + secretValue + "` ... ");

        secretClient.setSecret(new KeyVaultSecret(secretName, secretValue));

        System.out.println("done.");

        System.out.println("Forgetting your secret.");
        secretValue = "";
        System.out.println("Your secret is '" + secretValue + "'.");

        System.out.println("Retrieving your secret from " + keyVaultName + ".");

        KeyVaultSecret retrievedSecret = secretClient.getSecret(secretName);

        System.out.println("Your secret is '" + retrievedSecret.getValue() + "'.");
        System.out.print("Deleting your secret from " + keyVaultName + " ... ");

        secretClient.beginDeleteSecret(secretName);

        System.out.println("done.");


    }
}
```

## <a name="next-steps"></a>Passaggi successivi

In questo argomento di avvio rapido è stato creato un insieme di credenziali delle chiavi, quindi è stato archiviato e recuperato un segreto. Per altre informazioni sul servizio Key Vault e su come integrarlo nelle applicazioni, continuare con gli articoli seguenti.

- Leggere una [panoramica di Azure Key Vault](../general/overview.md)
- Vedere la [Guida per gli sviluppatori per Azure Key Vault](../general/developers-guide.md)
- Come [Proteggere l'accesso a un insieme di credenziali delle chiavi](../general/secure-your-key-vault.md)
- Esaminare le [procedure consigliate per Azure Key Vault](../general/best-practices.md)
