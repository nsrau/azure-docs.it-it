---
title: Autenticazione di Azure Spring Cloud con l'insieme di credenziali delle chiavi nelle azioni GitHubAuthenticate Azure Spring Cloud with Key Vault in GitHub Actions
description: Come usare l'insieme di credenziali delle chiavi con il flusso di lavoro CI/CD per Azure Spring Cloud con le azioni GitHubHow to use key vault with CI/CD workflow for Azure Spring Cloud with GitHub Actions
author: MikeDodaro
ms.author: barbkess
ms.service: spring-cloud
ms.topic: how-to
ms.date: 01/20/2019
ms.openlocfilehash: 78cd5945e394219be0551bbe97afef07f18b61f7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78945477"
---
# <a name="authenticate-azure-spring-cloud-with-key-vault-in-github-actions"></a>Autenticazione di Azure Spring Cloud con l'insieme di credenziali delle chiavi nelle azioni GitHubAuthenticate Azure Spring Cloud with Key Vault in GitHub Actions
L'insieme di credenziali delle chiavi è un luogo sicuro in cui archiviare le chiavi. Gli utenti aziendali devono archiviare le credenziali per gli ambienti CI/CD nell'ambito che controllano. La chiave per ottenere le credenziali nell'insieme di credenziali delle chiavi deve essere limitata all'ambito della risorsa.  Ha accesso solo all'ambito dell'insieme di credenziali delle chiavi, non all'intero ambito di Azure.It has access to only the key vault scope, not the entire Azure scope. È come una chiave che può aprire solo una scatola forte non una chiave master che può aprire tutte le porte in un edificio. È un modo per ottenere una chiave con un'altra chiave, che è utile in un flusso di lavoro CICD. 

## <a name="generate-credential"></a>Genera credenziali
Per generare una chiave per accedere all'insieme di credenziali delle chiavi, eseguire il comando seguente sul computer locale:
```
az ad sp create-for-rbac --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP>/providers/Microsoft.KeyVault/vaults/<KEY_VAULT> --sdk-auth
```
L'ambito specificato `--scopes` dal parametro limita l'accesso chiave alla risorsa.  Può accedere solo alla scatola forte.

Con i risultati:
```
{
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
    "resourceManagerEndpointUrl": "https://management.azure.com/",
    "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
    "galleryEndpointUrl": "https://gallery.azure.com/",
    "managementEndpointUrl": "https://management.core.windows.net/"
}
```
Salvare quindi i risultati nei **segreti** GitHub come descritto in [Configurare il repository GitHub ed eseguire l'autenticazione con Azure](./spring-cloud-howto-github-actions.md#set-up-github-repository-and-authenticate).

## <a name="add-access-policies-for-the-credential"></a>Aggiungere criteri di accesso per la credenzialeAdd Access Policies for the Credential
Le credenziali create in precedenza possono ottenere solo informazioni generali sull'insieme di credenziali delle chiavi, non sul contenuto archiviato.  Per ottenere i segreti archiviati nell'insieme di credenziali delle chiavi, è necessario impostare i criteri di accesso per la credenziale.

Passare al **scope**dashboard **dell'insieme** di credenziali dei tasti nel portale di Azure, **Type** fare `This resource` clic sul menu **Controllo** di accesso, quindi aprire la scheda **Assegnazioni ruolo.** **Apps**  Dovresti vedere le credenziali create nel passaggio precedente:

 ![Impostare i criteri di accesso](./media/github-actions/key-vault1.png)

Copiare il nome della `azure-cli-2020-01-19-04-39-02`credenziale, ad esempio . Aprire il menu Criteri di **accesso,** fare clic sul collegamento Aggiungi criteri di **accesso.**  `Secret Management` Selezionare **Modello**, quindi **Principale**. Incollare il nome della credenziale nella casella di input**Selezione** **entità:**/

 ![Select](./media/github-actions/key-vault2.png)

 Fare clic sul pulsante **Aggiungi** nella finestra di dialogo **Aggiungi criteri** di accesso , quindi fare clic su **Salva**.

## <a name="generate-full-scope-azure-credential"></a>Genera credenziali di Azure con ambito completo
Questa è la chiave master per aprire tutte le porte dell'edificio. La procedura è simile al passaggio precedente, ma qui si modifica l'ambito per generare la chiave master:The procedure is similar to the previous step, but here we change the scope to generate the master key:

```
az ad sp create-for-rbac --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID> --sdk-auth
```

Anche in questo caso, i risultati:
```
{
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
    "resourceManagerEndpointUrl": "https://management.azure.com/",
    "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
    "galleryEndpointUrl": "https://gallery.azure.com/",
    "managementEndpointUrl": "https://management.core.windows.net/"
}
```
Copiare l'intera stringa JSON.  Torna al dashboard **dell'insieme di** credenziali delle chiavi. Apri il menu **Segreti,** quindi fai clic sul pulsante **Genera/Importa.** Immettere il nome `AZURE-CRENDENTIALS-FOR-SPRING`del segreto, ad esempio . Incollare la stringa delle credenziali JSON nella casella di input **Valore.** È possibile notare che la casella di input del valore è un campo di testo di una riga, anziché un'area di testo su più righe.  È possibile incollare la stringa JSON completa.

 ![Credenziale dell'ambito completo](./media/github-actions/key-vault3.png)

## <a name="combine-credentials-in-github-actions"></a>Combinare le credenziali nelle azioni GitHubCombine credentials in GitHub Actions
Impostare le credenziali utilizzate durante l'esecuzione della pipeline CICD:Set the credentials used when the CICD pipeline executes:

```
on: [push]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
    - uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}           # Strong box key you generated in the first step
    - uses: Azure/get-keyvault-secrets@v1.0
      with:
        keyvault: "zlhe-test"
        secrets: "AZURE-CREDENTIALS-FOR-SPRING"           # Master key to open all doors in the building
      id: keyvaultaction
    - uses: azure/login@v1
      with:
        creds: ${{ steps.keyvaultaction.outputs.AZURE-CREDENTIALS-FOR-SPRING }}
    - name: Azure CLI script
      uses: azure/CLI@v1
      with:
        azcliversion: 2.0.75
        inlineScript: |
          az extension add --name spring-cloud             # Spring CLI commands from here
          az spring-cloud list

```

## <a name="next-steps"></a>Passaggi successivi
* [Azioni di Spring Cloud GitHub](./spring-cloud-howto-github-actions.md)