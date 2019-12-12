---
title: 'Avvio rapido: Libreria client di Azure Key Vault per Java'
description: Fornisce criteri di formato e contenuto per la scrittura di argomenti di avvio rapido per le librerie client di Azure SDK.
author: msmbaldwin
ms.author: mbaldwin
ms.date: 10/20/2019
ms.service: key-vault
ms.topic: quickstart
ms.openlocfilehash: e06c8d5373f8740e8091daae242a4f7af6c0ea4a
ms.sourcegitcommit: d614a9fc1cc044ff8ba898297aad638858504efa
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/10/2019
ms.locfileid: "74995865"
---
# <a name="quickstart-azure-key-vault-client-library-for-java"></a>Guida introduttiva: Libreria client di Azure Key Vault per Java

Introduzione alla libreria client di Azure Key Vault per Java. Seguire questi passaggi per installare il pacchetto e provare il codice di esempio per le attività di base.

L'insieme di credenziali delle chiavi di Azure consente di proteggere le chiavi e i segreti di crittografia usati da servizi e applicazioni cloud. Usare la libreria client di Azure Key Vault per Java per:

- Aumentare la sicurezza e il controllo di chiavi e password.
- Creare e importare chiavi di crittografia in pochi minuti.
- Ridurre la latenza con ridondanza globale e su scala cloud.
- Semplificare e automatizzare le attività per i certificati SSL/TLS.
- Usare moduli di protezione hardware convalidati in base agli standard FIPS 140-2 livello 2.

[Codice sorgente](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault) | [Informazioni di riferimento per l'API](https://azure.github.io/azure-sdk-for-java) | [Documentazione del prodotto](index.yml) | [Esempi](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault/azure-security-keyvault-secrets/src/samples/java/com/azure/security/keyvault/secrets)

## <a name="prerequisites"></a>Prerequisiti

- Una sottoscrizione di Azure: [creare un account gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Java Development Kit (JDK)](/java/azure/jdk/?view=azure-java-stable) versione 8 o successiva
- [Apache Maven](https://maven.apache.org)
- [Interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli?view=azure-cli-latest) o [Azure PowerShell](/powershell/azure/overview)

Questo argomento di avvio rapido presuppone l'esecuzione dell'[interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli?view=azure-cli-latest) e di [Apache Maven](https://maven.apache.org) in una finestra del terminale Linux.

## <a name="setting-up"></a>Configurazione

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

In questo argomento di avvio rapido si usa un'istanza creata in precedenza di Azure Key Vault. È possibile creare un insieme di credenziali delle chiavi seguendo la procedura descritta negli argomenti di avvio rapido per l'[interfaccia della riga di comando di Azure](quick-create-cli.md), [Azure PowerShell](quick-create-powershell.md) o il [portale di Azure](quick-create-portal.md). In alternativa, è possibile eseguire i comandi dell'interfaccia della riga di comando di Azure riportati di seguito.

> [!Important]
> Ogni insieme di credenziali delle chiavi deve avere un nome univoco. Negli esempi seguenti sostituire <your-unique-keyvault-name> con il nome dell'insieme di credenziali delle chiavi in uso.

```azurecli
az group create --name "myResourceGroup" -l "EastUS"

az keyvault create --name <your-unique-keyvault-name> -g "myResourceGroup"
```

### <a name="create-a-service-principal"></a>Creare un'entità servizio

Il modo più semplice per autenticare un'applicazione basata sul cloud consiste nell'usare un'identità gestita. Per informazioni dettagliate, vedere [Usare un'identità gestita del servizio app per accedere ad Azure Key Vault](managed-identity.md). Per semplicità, tuttavia, in questo avvio rapido viene creata un'applicazione desktop, per la quale sono richiesti un'entità servizio e un criterio di controllo di accesso.

Creare un'entità servizio usando il comando [az ad sp create-for-rbac](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) dell'interfaccia della riga di comando di Azure:

```azurecli
az ad sp create-for-rbac -n "http://mySP" --sdk-auth
```

Questa operazione restituisce una serie di coppie chiave-valore. 

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

Prendere nota dei valori clientId, clientSecret e tenantId che verranno usati nei due passaggi seguenti.

#### <a name="give-the-service-principal-access-to-your-key-vault"></a>Concedere all'entità servizio l'accesso all'insieme di credenziali delle chiavi

Creare un criterio di accesso per l'insieme di credenziali delle chiavi che conceda l'autorizzazione all'entità servizio, passando il valore di clientId al comando [az keyvault set-policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy). Concedere all'entità servizio le autorizzazioni get, list e set sia per le chiavi che per i segreti.

```azurecli
az keyvault set-policy -n <your-unique-keyvault-name> --spn <clientId-of-your-service-principal> --secret-permissions delete get list set --key-permissions create decrypt delete encrypt get list unwrapKey wrapKey
```

#### <a name="set-environmental-variables"></a>Impostare le variabili di ambiente

Il metodo DefaultAzureCredential usato nell'applicazione si basa sulle tre variabili di ambiente `AZURE_CLIENT_ID`, `AZURE_CLIENT_SECRET`e `AZURE_TENANT_ID`. Impostare queste variabili sui valori clientId, clientSecret e tenantId annotati nel passaggio [Creare un'entità servizio](#create-a-service-principal) precedente. Usare il formato `export VARNAME=VALUE` per impostare le variabili di ambiente. Questo metodo imposta solo le variabili per la shell corrente e per i processi creati dalla shell. Per aggiungere in modo permanente queste variabili all'ambiente, modificare il file `/etc/environment `. 

Sarà anche necessario salvare il nome dell'insieme di credenziali delle chiavi come una variabile di ambiente denominata `KEY_VAULT_NAME`.

```console
export AZURE_CLIENT_ID=<your-clientID>

export AZURE_CLIENT_SECRET=<your-clientSecret>

export AZURE_TENANT_ID=<your-tenantId>

export KEY_VAULT_NAME=<your-key-vault-name>
````

## <a name="object-model"></a>Modello a oggetti

La libreria client di Azure Key Vault per Java consente di gestire le chiavi e gli asset correlati, come i certificati e i segreti. Gli esempi di codice riportati di seguito illustrano come creare un client e come impostare, recuperare ed eliminare un segreto.

L'intera app console è disponibile all'indirizzo https://github.com/Azure-Samples/key-vault-dotnet-core-quickstart/tree/master/key-vault-console-app.

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

L'autenticazione rispetto all'insieme di credenziali delle chiavi e la creazione di un client dell'insieme di credenziali delle chiavi dipendono dalle variabili di ambiente indicate nel passaggio precedente [Impostare le variabili di ambiente](#set-environmental-variables). Il nome dell'insieme di credenziali delle chiavi viene espanso nel relativo URI, nel formato `https://<your-key-vault-name>.vault.azure.net`.

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

È possibile verificare che il segreto sia stato impostato con il comando [az keyvault secret show](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show):

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

È possibile verificare che il segreto non sia più dotato del comando [az keyvault secret show](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show):

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

- Leggere una [panoramica di Azure Key Vault](key-vault-overview.md)
- Vedere la [Guida per gli sviluppatori per Azure Key Vault](key-vault-developers-guide.md)
- Vedere le informazioni su [chiavi, segreti e certificati](about-keys-secrets-and-certificates.md)
- Esaminare le [procedure consigliate per Azure Key Vault](key-vault-best-practices.md)
