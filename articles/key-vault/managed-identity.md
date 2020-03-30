---
title: Usare un'identità gestita assegnata dal sistema per accedere all'insieme di credenziali delle chiavi di AzureUse an system-assigned managed identity to access Azure Key Vault
description: Informazioni su come creare un'identità gestita per le applicazioni del servizio app e su come usarla per accedere a Archiviazione delle chiavi di AzureLearn how to create a managed identity for App Service applications and how to use it to access Azure Key Vault
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: 36a4871339401629300eedd77b6441aed10aabf3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79270953"
---
# <a name="provide-key-vault-authentication-with-a-managed-identity"></a>Fornire l'autenticazione dell'insieme di credenziali delle chiavi con un'identità gestitaProvide Key Vault authentication with a managed

Un'identità gestita da Azure Active Directory consente all'app di accedere facilmente ad altre risorse protette da Azure AD. L'identità viene gestita dalla piattaforma Azure e non è necessario eseguire il provisioning o ruotare alcun segreto. Per altre informazioni, vedere [Identità gestite per le risorse di Azure](../active-directory/managed-identities-azure-resources/overview.md). 

Questo articolo illustra come creare un'identità gestita per un'applicazione del servizio app e usarla per accedere all'insieme di credenziali delle chiavi di Azure.This article shows you how to create a managed identity for an App Service application and use it to access Azure Key Vault. Per le applicazioni ospitate nelle macchine virtuali di Azure, vedere [Usare un'identità gestita assegnata dal sistema di macchine virtuali Windows per accedere a Archiviazione delle chiavi di Azure.For](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-nonaad.md)applications hosted in Azure VMs, see Use a Windows VM system-assigned managed identity to access Azure Key Vault .


[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Prerequisiti 

Per completare questa guida, è necessario disporre delle risorse seguenti. 

- Un insieme di credenziali delle chiavi. È possibile usare un insieme di credenziali delle chiavi esistente o crearne uno nuovo seguendo le procedure illustrate in una di queste guide di avvio rapido:
   - [Creare un insieme di credenziali delle chiavi con l'interfaccia della riga di comando di Azure](quick-create-cli.md)
   - [Creare un insieme di credenziali delle chiavi con Azure PowerShell](quick-create-powershell.md)
   - [Creare un insieme di credenziali delle chiavi con il portale di Azure](quick-create-portal.md).
- Applicazione del servizio app esistente a cui concedere l'accesso all'insieme di credenziali delle chiavi. È possibile crearne rapidamente uno seguendo la procedura descritta nella [documentazione del servizio app.](../app-service/overview.md)
- [Interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli?view=azure-cli-latest) o Azure [PowerShell](/powershell/azure/overview). In alternativa, è possibile usare il [portale di Azure](https://portal.azure.com).


## <a name="adding-a-system-assigned-identity"></a>Aggiunta di un'identità assegnata dal sistema 

Innanzitutto, è necessario aggiungere un'identità assegnata dal sistema a un'applicazione. 
 
### <a name="azure-portal"></a>Portale di Azure 

Per configurare un'identità gestita nel portale, è prima necessario creare un'applicazione come di consueto e quindi abilitare la funzionalità. 

1. Se si usa un'app per le funzioni, passare a **Funzionalità della piattaforma**. Per altri tipi di app, scorrere verso il basso fino al gruppo **Impostazioni** nel riquadro di spostamento a sinistra. 

1. Selezionare **Identità gestita**. 

1. All'interno della scheda **Assegnata dal sistema** impostare **Stato** su **Attivato**. Fare clic su **Salva**. 

    ![](./media/managed-identity-system-assigned.png)

### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

Questa guida introduttiva richiede l'interfaccia della riga di comando di Azure versione 2.0.4 o successiva. Eseguire `az --version` per trovare la versione corrente. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli?view=azure-cli-latest). 

Per accedere con l'interfaccia della riga di comando di Azure, usare il comando az login:To sign in with Azure CLI, use the [az login command:](/cli/azure/reference-index?view=azure-cli-latest#az-login)

```azurecli-interactive
az login
```

Per altre informazioni sulle opzioni di accesso con l'interfaccia della riga di comando di Azure, vedere [Accedere con l'interfaccia della riga di comando](/cli/azure/authenticate-azure-cli?view=azure-cli-latest)di Azure.For more information on login options with the Azure CLI, see Sign in with Azure CLI . 

Per creare l'identità per questa applicazione, usare il comando azure CLI [az webapp identity assign](/cli/azure/webapp/identity?view=azure-cli-latest#az-webapp-identity-assign) o az [functionapp identity assign](/cli/azure/functionapp/identity?view=azure-cli-latest#az-functionapp-identity-assign) command:


```azurecli-interactive
az webapp identity assign --name myApp --resource-group myResourceGroup
```

```azurecli-interactive
az functionapp identity assign --name myApp --resource-group myResourceGroup
```

Prendere nota `PrincipalId`della , che sarà necessaria nella sezione successiva.

```json
{
  "principalId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "type": "SystemAssigned"
}
```
## <a name="grant-your-app-access-to-key-vault"></a>Concedere all'app l'accesso all'Insieme di credenziali delle chiavi 

### <a name="azure-portal"></a>Portale di Azure

1.  Passare alla risorsa Key Vault.Navigate to Key Vault resource. 

1.  Selezionare **Criteri di accesso** e fare clic su Aggiungi criteri di **accesso**. 

1.  In **Autorizzazioni segrete**selezionare **Ottieni, Elenca**. 

1.  Scegliere **Seleziona entità**e nel campo di ricerca immettere il nome dell'app.  Selezionare l'app nell'elenco dei risultati e fare clic su **Seleziona**. 

1.  Fare clic su **Aggiungi** per completare l'aggiunta dei nuovi criteri di accesso.

    ![](./media/managed-identity-access-policy.png)

### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

Per concedere all'applicazione l'accesso all'insieme di credenziali delle chiavi, usare il comando [keyvault set-policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) dell'interfaccia della riga di comando di Azure, che fornisce al parametro **ObjectId** il **principalId** annotato in precedenza.

```azurecli-interactive
az keyvault set-policy --name myKeyVault --object-id <PrincipalId> --secret-permissions get list 
```

## <a name="next-steps"></a>Passaggi successivi

- [Sicurezza dell'insieme di credenziali delle chiavi di Azure: gestione di identità e accessiAzure Key Vault security: Identity and access management](overview-security.md#identity-and-access-management)
- [Fornire un'autenticazione di Key Vault con un criterio di controllo di accesso](key-vault-group-permissions-for-apps.md)
- [Informazioni su chiavi, segreti e certificati](about-keys-secrets-and-certificates.md)
- [Proteggere l'insieme di credenziali delle chiavi](key-vault-secure-your-key-vault.md).
- [Guida per gli sviluppatori di Azure Key VaultAzure Key Vault developer's guide](key-vault-developers-guide.md)
- Esaminare le [procedure consigliate per Azure Key Vault](key-vault-best-practices.md)