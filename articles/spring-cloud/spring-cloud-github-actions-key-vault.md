---
title: Autenticazione di Azure Spring Cloud con Key Vault in GitHub Actions
description: Come usare l'insieme di credenziali delle chiavi con il flusso di lavoro CI/CD per Azure Spring cloud con azioni di GitHub
author: MikeDodaro
ms.author: barbkess
ms.service: spring-cloud
ms.topic: how-to
ms.date: 09/08/2020
ms.custom: devx-track-java
ms.openlocfilehash: 995d10b3c7064e462500e0bec4d5d8aa010afe64
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90888785"
---
# <a name="authenticate-azure-spring-cloud-with-key-vault-in-github-actions"></a>Autenticazione di Azure Spring Cloud con Key Vault in GitHub Actions

**Questo articolo si applica a:** ✔️ Java ✔️ C#

Key Vault è un luogo sicuro per archiviare le chiavi. Gli utenti aziendali devono archiviare le credenziali per gli ambienti CI/CD nell'ambito che controllano. La chiave per ottenere le credenziali nell'insieme di credenziali delle chiavi deve essere limitata all'ambito della risorsa.  Ha accesso solo all'ambito dell'insieme di credenziali delle chiavi, non all'intero ambito di Azure. È come una chiave che può aprire solo una casella complessa e non una chiave master in grado di aprire tutte le porte di un edificio. Si tratta di un modo per ottenere una chiave con un'altra chiave, che risulta utile in un flusso di lavoro CICD. 

## <a name="generate-credential"></a>Genera credenziali
Per generare una chiave per accedere all'insieme di credenziali delle chiavi, eseguire il comando seguente nel computer locale:
```
az ad sp create-for-rbac --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP>/providers/Microsoft.KeyVault/vaults/<KEY_VAULT> --sdk-auth
```
L'ambito specificato dal `--scopes` parametro limita l'accesso alla chiave alla risorsa.  Può accedere solo alla casella Strong.

Con risultati:
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
Salvare quindi i risultati in GitHub **Secrets** come descritto in [configurare il repository GitHub ed eseguire l'autenticazione con Azure](./spring-cloud-howto-github-actions.md#set-up-github-repository-and-authenticate).

## <a name="add-access-policies-for-the-credential"></a>Aggiungere criteri di accesso per le credenziali
La credenziale creata in precedenza può ottenere solo informazioni generali sulla Key Vault, non sul contenuto archiviato.  Per ottenere i segreti archiviati nella Key Vault, è necessario impostare i criteri di accesso per le credenziali.

Passare al dashboard **Key Vault** in portale di Azure, fare clic sul menu di **controllo di accesso** , quindi aprire la scheda **assegnazioni di ruolo** . Selezionare **app** per **tipo** e `This resource` per **ambito**.  Verranno visualizzate le credenziali create nel passaggio precedente:

 ![Impostare i criteri di accesso](./media/github-actions/key-vault1.png)

Copiare il nome delle credenziali, ad esempio `azure-cli-2020-01-19-04-39-02` . Aprire il menu **criteri di accesso** e fare clic sul collegamento **+ Aggiungi criteri di accesso** .  Selezionare `Secret Management` per **modello**, quindi selezionare **entità**. Incollare il nome delle credenziali nella casella **principale** / **Select** input:

 ![Select](./media/github-actions/key-vault2.png)

 Fare clic sul pulsante **Aggiungi** nella finestra di dialogo **Aggiungi criteri di accesso** , quindi fare clic su **Salva**.

## <a name="generate-full-scope-azure-credential"></a>Genera credenziali di Azure con ambito completo
Si tratta della chiave master per aprire tutte le porte nell'edificio. La procedura è simile al passaggio precedente, ma in questo caso viene modificato l'ambito per generare la chiave master:

```
az ad sp create-for-rbac --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID> --sdk-auth
```

Anche in questo caso, i risultati sono:
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
Copiare l'intera stringa JSON.  Tornare a **Key Vault** dashboard. Aprire il menu **segreti** , quindi fare clic sul pulsante **genera/importa** . Immettere il nome del segreto, ad esempio `AZURE-CREDENTIALS-FOR-SPRING` . Incollare la stringa di credenziali JSON nella casella di immissione **valore** . È possibile notare che la casella di input valore è un campo di testo a riga singola, anziché un'area di testo a più righe.  È possibile incollare la stringa JSON completa.

 ![Credenziali dell'ambito completo](./media/github-actions/key-vault3.png)

## <a name="combine-credentials-in-github-actions"></a>Combinare le credenziali nelle azioni di GitHub
Impostare le credenziali utilizzate quando viene eseguita la pipeline CICD:

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
        keyvault: "<Your Key Vault Name>"
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
* [Azioni GitHub di Spring cloud](./spring-cloud-howto-github-actions.md)
