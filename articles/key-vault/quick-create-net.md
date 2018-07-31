---
title: "Guida introduttiva di Azure: Configurare un'applicazione Web di Azure per la lettura di un segreto da un insieme di credenziali delle chiavi | Microsoft Docs"
description: Questa guida introduttiva illustra come configurare un'applicazione ASP.Net Core per la lettura di un segreto da un insieme di credenziali delle chiavi
services: key-vault
author: prashanthyv
manager: sumedhb
ms.service: key-vault
ms.topic: quickstart
ms.date: 07/24/2018
ms.author: barclayn
ms.custom: mvc
ms.openlocfilehash: 07a7bc5713f093e34a775aacab27094780ac6c7e
ms.sourcegitcommit: 156364c3363f651509a17d1d61cf8480aaf72d1a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/25/2018
ms.locfileid: "39247826"
---
# <a name="quickstart-set-and-read-a-secret-from-key-vault-in-a-net-web-app"></a>Guida introduttiva: Impostare e leggere un segreto da un insieme di credenziali delle chiavi in un'app Web .NET

In questa guida introduttiva vengono presentati i passaggi necessari per ottenere un'applicazione Web di Azure per leggere le informazioni dall'insieme di credenziali delle chiavi usando le identità del servizio gestito. Si apprenderà come:

> [!div class="checklist"]
> * Creare un insieme di credenziali delle chiavi.
> * Archiviare un segreto nell'insieme di credenziali delle chiavi.
> * Recuperare un segreto dall'insieme di credenziali delle chiavi.
> * Creare un'applicazione Web di Azure.
> * [Abilitare le identità del servizio gestite](../active-directory/managed-service-identity/overview.md).
> * Concedere le autorizzazioni necessarie per l'applicazione Web per la lettura dei dati da un insieme di credenziali delle chiavi.

Prima di continuare, leggere i [concetti di base](key-vault-whatis.md#basic-concepts), in particolare in relazione all'[identità del servizio gestita](../active-directory/managed-service-identity/overview.md)

## <a name="prerequisites"></a>Prerequisiti

* In Windows:
  * [Visual Studio 2017 versione 15.7.3 o successiva](https://www.microsoft.com/net/download/windows) con i carichi di lavoro seguenti:
    * Sviluppo Web e ASP.NET
    * Sviluppo multipiattaforma .NET Core
  * [.NET Core 2.1 SDK o versione successiva](https://www.microsoft.com/net/download/windows)

* Su Mac:
  * https://visualstudio.microsoft.com/vs/mac/

* Tutte le piattaforme:
  * Scaricare GIT da [qui](https://git-scm.com/downloads).
  * Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.
  * [Interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) È necessaria l'interfaccia della riga di comando di Azure versione 2.0.4 o successiva. È disponibile per Windows, Mac e Linux

## <a name="login-to-azure"></a>Accedere ad Azure

   Per accedere ad Azure tramite l'interfaccia della riga di comando, è possibile digitare:

```azurecli
az login
```

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Creare un gruppo di risorse con il comando [az group create](/cli/azure/group#az_group_create). Un gruppo di risorse di Azure è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite.

Selezionare il nome di un gruppo di risorse e compilare il segnaposto.
L'esempio seguente crea un gruppo di risorse denominato *<YourResourceGroupName>* nella località *eastus*.

```azurecli
# To list locations: az account list-locations --output table
az group create --name "<YourResourceGroupName>" --location "East US"
```

In tutto l'articolo viene usato il gruppo di risorse appena creato.

## <a name="create-an-azure-key-vault"></a>Creare un Azure Key Vault

Verrà di seguito creato un insieme di credenziali delle chiavi nel gruppo di risorse creato nel passaggio precedente. Specificare le informazioni seguenti:

* Nome dell'insieme di credenziali: **selezionare il nome dell'insieme di credenziali delle chiavi**. Il nome di Key Vault deve essere una stringa composta da un minimo di 3 a un massimo di 24 caratteri e contenente solo (0-9, a-z, A-Z e -).
* Nome del gruppo di risorse: **selezionare il nome di un gruppo di risorse**.
* Località: **Stati Uniti orientali**.

```azurecli
az keyvault create --name "<YourKeyVaultName>" --resource-group "<YourResourceGroupName>" --location "East US"
```

A questo punto, l'account Azure è l'unico autorizzato a eseguire qualsiasi operazione su questo nuovo insieme di credenziali.

## <a name="add-a-secret-to-key-vault"></a>Aggiungere un segreto all'istanza di Key Vault

Verrà aggiunto un segreto per illustrare il funzionamento di questa operazione. Si potrebbe archiviare una stringa di connessione SQL o qualsiasi altra informazione che è necessario conservare in modo sicuro, rendendola però disponibile per l'applicazione. In questa esercitazione la password sarà denominata **AppSecret** e archivierà il valore **MySecret**.

Digitare i comandi seguenti per creare un segreto nell'insieme di credenziali delle chiavi chiamato **AppSecret** che archivierà il valore **MySecret**:

```azurecli
az keyvault secret set --vault-name "<YourKeyVaultName>" --name "AppSecret" --value "MySecret"
```

Per visualizzare il valore contenuto nel segreto come testo normale:

```azurecli
az keyvault secret show --name "AppSecret" --vault-name "<YourKeyVaultName>"
```

Questo comando mostra le informazioni del segreto, incluso l'URI. Dopo aver completato questi passaggi sarà disponibili un URI per un segreto in Azure Key Vault. Prendere nota di queste informazioni. Saranno necessarie in un passaggio successivo.

## <a name="clone-the-repo"></a>Clonare il repository

Clonare il repository per creare una copia locale e modificare l'origine eseguendo il comando seguente:

```
git clone https://github.com/Azure-Samples/key-vault-dotnet-core-quickstart.git
```

## <a name="open-and-edit-the-solution"></a>Aprire e modificare la soluzione

Modificare il file program.cs per poter eseguire l'esempio con il nome dell'insieme di credenziali delle chiavi specifico.

1. Passare alla cartella key-vault-dotnet-core-quickstart
2. Aprire il file key-vault-dotnet-core-quickstart.sln in Visual Studio 2017
3. Aprire il file Program.cs e aggiornare il segnaposto <YourKeyVaultName> con il nome dell'insieme di credenziali delle chiavi creato in precedenza.

Questa soluzione usa le librerie NuGet [AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) e [KeyVault](https://www.nuget.org/packages/Microsoft.Azure.KeyVault)

## <a name="run-the-app"></a>Esecuzione dell'app

Dal menu principale di Visual Studio 2017 scegliere Debug > Avvia senza eseguire debug. Quando viene visualizzato il browser, passare alla pagina About. Viene visualizzato il valore per AppSecret.

## <a name="publish-the-web-application-to-azure"></a>Pubblicare l'applicazione Web in Azure

L'app verrà pubblicata in Azure per visualizzarla come un'app Web e anche per verificare che venga recuperato il valore del segreto

1. In Visual Studio selezionare il progetto **key-vault-dotnet-core-quickstart**.
2. Selezionare **Publish** (Pubblica), quindi **Start** (Inizia).
3. Creare un nuovo **servizio app** e selezionare **Publish**.
4. Modificare il nome dell'app in "keyvaultdotnetcorequickstart"
5. Selezionare **Crea**.

>[!VIDEO https://sec.ch9.ms/ch9/e93d/a6ac417f-2e63-4125-a37a-8f34bf0fe93d/KeyVault_high.mp4]

## <a name="enable-managed-service-identities-msi"></a>Abilitare le identità del servizio gestite

Azure Key Vault consente di archiviare in modo sicuro le credenziali e altre chiavi e segreti, ma è necessario autenticare il codice in Azure Key Vault per recuperarli. Identità del servizio gestita semplifica questa attività, assegnando ai servizi di Azure un'identità gestita automaticamente in Azure Active Directory (Azure AD). È possibile usare questa identità per l'autenticazione a qualsiasi servizio che supporti l'autenticazione di Azure AD, incluso Key Vault, senza inserire le credenziali nel codice.

1. Tornare all'interfaccia della riga di comando di Azure
2. Eseguire il comando assign-identity per creare l'identità per l'applicazione:

```azurecli
az webapp identity assign --name "keyvaultdotnetcorequickstart" --resource-group "<YourResourceGroupName>"
```

>[!NOTE]
>Questo comando equivale a passare al portale e impostare **Identità del servizio gestito** su **Attivato** nelle proprietà dell'applicazione Web.

## <a name="assign-permissions-to-your-application-to-read-secrets-from-key-vault"></a>Assegnare le autorizzazioni all'applicazione per la lettura dei segreti da Key Vault

Prendere nota dell'output quando si [pubblica l'applicazione in Azure][]. Deve essere nel formato seguente:
        
        {
          "principalId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
          "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
          "type": "SystemAssigned"
        }
        
Eseguire quindi questo comando usando il nome dell'insieme di credenziali delle chiavi e il valore PrincipalId copiato in precedenza:

```azurecli

az keyvault set-policy --name '<YourKeyVaultName>' --object-id <PrincipalId> --secret-permissions get

```

## <a name="next-steps"></a>Passaggi successivi

* [Home page di Azure Key Vault](https://azure.microsoft.com/services/key-vault/)
* [Documentazione su Azure Key Vault](https://docs.microsoft.com/azure/key-vault/)
* [Azure SDK per .NET](https://github.com/Azure/azure-sdk-for-net)
* [Informazioni di riferimento sulle API REST](https://docs.microsoft.com/rest/api/keyvault/)
