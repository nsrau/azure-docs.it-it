---
title: Avvio rapido - Libreria client di Azure Key Vault per Node.js (v4)
description: Informazioni su come creare, recuperare ed eliminare segreti da Azure Key Vault usando la libreria client Node.js
author: msmbaldwin
ms.author: mbaldwin
ms.date: 10/20/2019
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.openlocfilehash: 1157fb69704af59a75989b22338b88a8576428ce
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87076667"
---
# <a name="quickstart-azure-key-vault-client-library-for-nodejs-v4"></a>Avvio rapido: Libreria client di Azure Key Vault per Node.js (v4)

Iniziare a usare la libreria client di Azure Key Vault per Node.js. Seguire questi passaggi per installare il pacchetto e provare il codice di esempio per le attività di base.

L'insieme di credenziali delle chiavi di Azure consente di proteggere le chiavi e i segreti di crittografia usati da servizi e applicazioni cloud. Usare la libreria client di Key Vault per Node.js per:

- Aumentare la sicurezza e il controllo di chiavi e password.
- Creare e importare chiavi di crittografia in pochi minuti.
- Ridurre la latenza con ridondanza globale e su scala cloud.
- Semplificare e automatizzare le attività per i certificati TLS/SSL.
- Usare moduli di protezione hardware convalidati in base agli standard FIPS 140-2 livello 2.

[Documentazione di riferimento sull'API](https://docs.microsoft.com/javascript/api/overview/azure/key-vault-index?view=azure-node-latest) | [Codice sorgente della libreria](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/keyvault) | [Pacchetto (npm)](https://www.npmjs.com/package/@azure/keyvault-secrets)

## <a name="prerequisites"></a>Prerequisiti

- Una sottoscrizione di Azure: [creare un account gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Versione aggiornata di [Node.js](https://nodejs.org) per il sistema operativo in uso.
- [Interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli?view=azure-cli-latest) o [Azure PowerShell](/powershell/azure/)

Questa Guida di avvio rapido presuppone l'esecuzione dell'[interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli?view=azure-cli-latest) in una finestra del terminale Linux.

## <a name="setting-up"></a>Configurazione

### <a name="install-the-package"></a>Installare il pacchetto

Nella finestra della console installare la libreria dei segreti di Azure Key Vault per Node.js.

```console
npm install @azure/keyvault-secrets
```

Per questa Guida di avvio rapido, sarà necessario installare anche il pacchetto azure.identity package:

```console
npm install @azure/identity
```

### <a name="create-a-resource-group-and-key-vault"></a>Creare un gruppo di risorse e un insieme di credenziali delle chiavi

[!INCLUDE [Create a resource group and key vault](../../../includes/key-vault-rg-kv-creation.md)]

### <a name="create-a-service-principal"></a>Creare un'entità servizio

[!INCLUDE [Create a service principal](../../../includes/key-vault-sp-creation.md)]

#### <a name="give-the-service-principal-access-to-your-key-vault"></a>Concedere all'entità servizio l'accesso all'insieme di credenziali delle chiavi

[!INCLUDE [Give the service principal access to your key vault](../../../includes/key-vault-sp-kv-access.md)]

#### <a name="set-environmental-variables"></a>Impostare le variabili di ambiente

[!INCLUDE [Set environmental variables](../../../includes/key-vault-set-environmental-variables.md)]

## <a name="object-model"></a>Modello a oggetti

La libreria client di Azure Key Vault per Node.js consente di gestire le chiavi e gli asset correlati, come i certificati e i segreti. Gli esempi di codice riportati di seguito illustrano come creare un client e come impostare, recuperare ed eliminare un segreto.

L'intera app console è disponibile all'indirizzo https://github.com/Azure-Samples/key-vault-dotnet-core-quickstart/tree/master/key-vault-console-app.

## <a name="code-examples"></a>Esempi di codice

### <a name="add-directives"></a>Aggiungere le direttive

Aggiungere le direttive seguenti all'inizio del codice:

```javascript
const { DefaultAzureCredential } = require("@azure/identity");
const { SecretClient } = require("@azure/keyvault-secrets");
```

### <a name="authenticate-and-create-a-client"></a>Autenticare e creare un client

L'autenticazione rispetto all'insieme di credenziali delle chiavi e la creazione di un client dell'insieme di credenziali delle chiavi dipendono dalle variabili di ambiente del precedente passaggio [Impostare le variabili di ambiente](#set-environmental-variables) e dal [costruttore SecretClient](/javascript/api/@azure/keyvault-secrets/secretclient?view=azure-node-latest#secretclient-string--tokencredential--pipelineoptions-). 

Il nome dell'insieme di credenziali delle chiavi viene espanso nel relativo URI, nel formato `https://<your-key-vault-name>.vault.azure.net`. 

```javascript
const keyVaultName = process.env["KEY_VAULT_NAME"];
const KVUri = "https://" + keyVaultName + ".vault.azure.net";

const credential = new DefaultAzureCredential();
const client = new SecretClient(KVUri, credential);
```

### <a name="save-a-secret"></a>Salvare un segreto

Ora che l'applicazione è stata autenticata, è possibile inserire un segreto nell'insieme di credenziali delle chiavi usando il [metodo client.setSecret](/javascript/api/@azure/keyvault-secrets/secretclient?view=azure-node-latest#setsecret-string--string--setsecretoptions-). A tale scopo è necessario un nome per il segreto e in questo esempio viene usato "mySecret".  

```javascript
await client.setSecret(secretName, secretValue);
```

È possibile verificare che il segreto sia stato impostato con il comando [az keyvault secret show](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show):

```azurecli
az keyvault secret show --vault-name <your-unique-keyvault-name> --name mySecret
```

### <a name="retrieve-a-secret"></a>Recuperare un segreto

È ora possibile recuperare il valore impostato in precedenza con il [metodo client.getSecret](/javascript/api/@azure/keyvault-secrets/secretclient?view=azure-node-latest#getsecret-string--getsecretoptions-).

```javascript
const retrievedSecret = await client.getSecret(secretName);
 ```

Il segreto è ora salvato come `retrievedSecret.value`.

### <a name="delete-a-secret"></a>consente di eliminare un segreto

Eliminare infine il segreto dall'insieme di credenziali delle chiavi con il [metodo client.beginDeleteSecret](/javascript/api/@azure/keyvault-secrets/secretclient?view=azure-node-latest#begindeletesecret-string--begindeletesecretoptions-).

```javascript
await client.beginDeleteSecret(secretName)
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

```javascript
const { DefaultAzureCredential } = require("@azure/identity");
const { SecretClient } = require("@azure/keyvault-secrets");

const readline = require('readline');

function askQuestion(query) {
    const rl = readline.createInterface({
        input: process.stdin,
        output: process.stdout,
    });

    return new Promise(resolve => rl.question(query, ans => {
        rl.close();
        resolve(ans);
    }))
}

async function main() {

  const keyVaultName = process.env["KEY_VAULT_NAME"];
  const KVUri = "https://" + keyVaultName + ".vault.azure.net";

  const credential = new DefaultAzureCredential();
  const client = new SecretClient(KVUri, credential);

  const secretName = "mySecret";
  var secretValue = await askQuestion("Input the value of your secret > ");

  console.log("Creating a secret in " + keyVaultName + " called '" + secretName + "' with the value '" + secretValue + "` ...");
  await client.setSecret(secretName, secretValue);

  console.log("Done.");

  console.log("Forgetting your secret.");
  secretValue = "";
  console.log("Your secret is '" + secretValue + "'.");

  console.log("Retrieving your secret from " + keyVaultName + ".");

  const retrievedSecret = await client.getSecret(secretName);

  console.log("Your secret is '" + retrievedSecret.value + "'.");
  console.log("Deleting your secret from " + keyVaultName + " ...");

  await client.beginDeleteSecret(secretName);

  console.log("Done.");

}

main()

```

## <a name="next-steps"></a>Passaggi successivi

In questo argomento di avvio rapido è stato creato un insieme di credenziali delle chiavi, quindi è stato archiviato e recuperato un segreto. Per altre informazioni sul servizio Key Vault e su come integrarlo nelle applicazioni, continuare con gli articoli seguenti.

- Leggere una [panoramica di Azure Key Vault](../general/overview.md)
- Vedere la [Guida per gli sviluppatori per Azure Key Vault](../general/developers-guide.md)
- Esaminare le [procedure consigliate per Azure Key Vault](../general/best-practices.md)