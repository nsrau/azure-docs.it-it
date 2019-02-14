---
title: "Guida introduttiva: Impostare e recuperare un segreto da Azure Key Vault tramite un'app Web Node - Azure Key Vault | Microsoft Docs"
description: In questa guida introduttiva si imposta e recupera un segreto da Azure Key Vault usando un'app Web .NET
services: key-vault
author: prashanthyv
manager: sumedhb
ms.service: key-vault
ms.topic: quickstart
ms.date: 01/02/2019
ms.author: barclayn
ms.custom: mvc
ms.openlocfilehash: 8127a7a10c117edaefc4a215366990d847a165ec
ms.sourcegitcommit: de81b3fe220562a25c1aa74ff3aa9bdc214ddd65
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/13/2019
ms.locfileid: "56238284"
---
# <a name="quickstart-set-and-retrieve-a-secret-from-azure-key-vault-by-using-a-net-web-app"></a>Avvio rapido: Impostare e recuperare un segreto da Azure Key Vault tramite un'app Web .NET

In questa guida introduttiva vengono seguiti i passaggi per ottenere un'applicazione Web di Azure per leggere le informazioni da Azure Key Vault usando le identità gestite per le risorse di Azure. L'uso di Key Vault consente di mantere protette le informazioni. Si apprenderà come:

* Creare un insieme di credenziali delle chiavi.
* Archiviare un segreto nell'insieme di credenziali delle chiavi.
* Recuperare un segreto dall'insieme di credenziali delle chiavi.
* Creare un'applicazione Web di Azure.
* Abilitare un'[identità del servizio gestita](../active-directory/managed-identities-azure-resources/overview.md) per l'app Web.
* Concedere le autorizzazioni necessarie per l'applicazione Web per la lettura dei dati dall'insieme di credenziali delle chiavi.

Prima di continuare, leggere i [concetti di base di Key Vault](key-vault-whatis.md#basic-concepts).

>[!NOTE]
>Key Vault è un repository centrale per archiviare i segreti a livello di codice. Per farlo, tuttavia, le applicazioni e gli utenti devono prima autenticarsi in Key Vault, ovvero devono presentare un segreto. Per seguire le procedure consigliate per la sicurezza, questo primo segreto deve essere ruotato periodicamente. 
>
>Con le [identità del servizio gestite per le risorse di Azure](../active-directory/managed-identities-azure-resources/overview.md), alle applicazioni in esecuzione in Azure viene assegnata un'identità gestita automaticamente da Azure. Ciò aiuta a risolvere il *problema di introduzione del segreto*, in modo tale che gli utenti e le applicazioni possano seguire le procedure consigliate senza doversi preoccupare della rotazione del primo segreto.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Prerequisiti

* In Windows:
  * [Visual Studio 2017 versione 15.7.3 o successiva](https://www.microsoft.com/net/download/windows) con i carichi di lavoro seguenti:
    * Sviluppo Web e ASP.NET
    * Sviluppo multipiattaforma .NET Core
  * [.NET Core 2.1 SDK o versione successiva](https://www.microsoft.com/net/download/windows)

* Su Mac:
  * Vedere [Novità di Visual Studio per Mac](https://visualstudio.microsoft.com/vs/mac/).

* Tutte le piattaforme:
  * Git ([download](https://git-scm.com/downloads)).
  * Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.
  * [Interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) versione 2.0.4 o versioni successive. È disponibile per Windows, Mac e Linux.

## <a name="log-in-to-azure"></a>Accedere ad Azure

Per accedere ad Azure tramite l'interfaccia della riga di comando di Azure, immettere:

```azurecli
az login
```

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Creare un gruppo di risorse con il comando [az group create](/cli/azure/group#az-group-create). Un gruppo di risorse di Azure è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite.

Selezionare il nome di un gruppo di risorse e compilare il segnaposto.
L'esempio seguente crea un gruppo di risorse nell'area Stati Uniti orientali:

```azurecli
# To list locations: az account list-locations --output table
az group create --name "<YourResourceGroupName>" --location "East US"
```

Il gruppo di risorse appena creato viene usato in tutto l'articolo.

## <a name="create-a-key-vault"></a>Creare un insieme di credenziali delle chiavi

Viene ora creato un insieme di credenziali delle chiavi nel gruppo di risorse creato nel passaggio precedente. Specificare le informazioni seguenti:

* Nome dell'insieme di credenziali delle chiavi: Il nome deve essere una stringa di 3-24 caratteri costituiti esclusivamente da 0-9, a-z, A-Z e un trattino (-).
* Nome del gruppo di risorse.
* Percorso: **Stati Uniti orientali**.

```azurecli
az keyvault create --name "<YourKeyVaultName>" --resource-group "<YourResourceGroupName>" --location "East US"
```

A questo punto, l'account Azure è l'unico autorizzato a eseguire qualsiasi operazione su questo nuovo insieme di credenziali.

## <a name="add-a-secret-to-the-key-vault"></a>Aggiungere un segreto all'insieme di credenziali delle chiavi

Verrà aggiunto un segreto per illustrare il funzionamento di questa operazione. Si potrebbe archiviare una stringa di connessione SQL o qualsiasi altra informazione che è necessario conservare in modo sicuro, rendendola però disponibile per l'applicazione.

Digitare i comandi seguenti per creare un segreto nell'insieme di credenziali delle chiavi denominato **AppSecret**. Questo segreto archivierà il valore **MySecret**.

```azurecli
az keyvault secret set --vault-name "<YourKeyVaultName>" --name "AppSecret" --value "MySecret"
```

Per visualizzare il valore contenuto nel segreto come testo normale:

```azurecli
az keyvault secret show --name "AppSecret" --vault-name "<YourKeyVaultName>"
```

Questo comando mostra le informazioni del segreto, incluso l'URI. Dopo aver completato questi passaggi si avrà un URI per un segreto in un insieme di credenziali delle chiavi. Prendere nota di queste informazioni. Saranno necessarie in un passaggio successivo.

## <a name="clone-the-repo"></a>Clonare il repository

Clonare il repository per creare una copia locale in cui è possibile modificare l'origine. Eseguire il comando seguente:

```
git clone https://github.com/Azure-Samples/key-vault-dotnet-core-quickstart.git
```

## <a name="open-and-edit-the-solution"></a>Aprire e modificare la soluzione

Modificare il file program.cs per eseguire l'esempio con il nome dell'insieme di credenziali delle chiavi specifico:

1. Passare alla cartella key-vault-dotnet-core-quickstart.
2. Aprire il file key-vault-dotnet-core-quickstart.sln in Visual Studio 2017.
3. Aprire il file Program.cs e aggiornare il segnaposto *YourKeyVaultName* con il nome dell'insieme di credenziali delle chiavi creato in precedenza.

Questa soluzione usa le librerie NuGet [AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) e [KeyVault](https://www.nuget.org/packages/Microsoft.Azure.KeyVault).

## <a name="run-the-app"></a>Esecuzione dell'app

Dal menu principale di Visual Studio 2017 selezionare **Debug** > **Avvia** senza eseguire debug. Quando viene visualizzato il browser, passare alla pagina **Informazioni su**. Viene visualizzato il valore per **AppSecret**.

## <a name="publish-the-web-application-to-azure"></a>Pubblicare l'applicazione Web in Azure

Pubblicare questa app in Azure per visualizzarla come un'app Web e per verificare che venga recuperato il valore del segreto:

1. In Visual Studio selezionare il progetto **key-vault-dotnet-core-quickstart**.
2. Selezionare **Pubblica** > **Avvia**.
3. Creare un nuovo **servizio app** e quindi selezionare **Pubblica**.
4. Modificare il nome dell'app in **keyvaultdotnetcorequickstart**.
5. Selezionare **Create**.

>[!VIDEO https://sec.ch9.ms/ch9/e93d/a6ac417f-2e63-4125-a37a-8f34bf0fe93d/KeyVault_high.mp4]

## <a name="enable-a-managed-identity-for-the-web-app"></a>Abilitare un'identità gestita per l'app Web

Azure Key Vault consente di archiviare in modo sicuro le credenziali e altre chiavi e segreti, ma è necessario autenticare il codice in Key Vault per recuperarle. [La panoramica delle identità gestite per le risorse Azure](../active-directory/managed-identities-azure-resources/overview.md) consente di risolvere il problema in maniera più semplice, assegnando ai servizi di Azure un'identità gestita automaticamente in Azure Active Directory (Azure AD). È possibile usare questa identità per l'autenticazione a qualsiasi servizio che supporti l'autenticazione di Azure AD, incluso Key Vault, senza inserire le credenziali nel codice.

Nell'interfaccia della riga di comando di Azure eseguire il comando assign-identity per creare l'identità per l'applicazione:

   ```azurecli
   az webapp identity assign --name "keyvaultdotnetcorequickstart" --resource-group "<YourResourceGroupName>"
   ```

>[!NOTE]
>Il comando in questa procedura equivale a passare al portale e impostare **Identità/Assegnato dal sistema** su **Attivato** nelle proprietà dell'applicazione Web.

## <a name="assign-permissions-to-your-application-to-read-secrets-from-key-vault"></a>Assegnare le autorizzazioni all'applicazione per la lettura dei segreti da Key Vault

Prendere nota dell'output quando si pubblica l'applicazione in Azure. Deve essere nel formato seguente:
        
        {
          "principalId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
          "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
          "type": "SystemAssigned"
        }
        
Eseguire quindi questo comando usando il nome dell'insieme di credenziali delle chiavi e il valore **PrincipalId**:

```azurecli

az keyvault set-policy --name '<YourKeyVaultName>' --object-id <PrincipalId> --secret-permissions get list

```

A questo punto, quando si esegue l'applicazione viene visualizzato il valore del segreto recuperato. Nel comando precedente si concedono all'identità del servizio gestita del servizio app le autorizzazioni per eseguire le operazioni **get** e **list** nell'insieme di credenziali delle chiavi.

## <a name="clean-up-resources"></a>Pulire le risorse
Eliminare il gruppo di risorse, la macchina virtuale e tutte le risorse correlate quando non servono più. A tale scopo, selezionare il gruppo di risorse per la VM e selezionare **Elimina**.

Eliminare l'insieme di credenziali delle chiavi usando il comando [az keyvault delete](https://docs.microsoft.com/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-delete):

```azurecli
az keyvault delete --name
                   [--resource-group]
                   [--subscription]
```

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Altre informazioni su Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis)
