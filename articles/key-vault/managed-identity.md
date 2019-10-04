---
title: Usare un'identità gestita assegnata dal sistema per accedere a Azure Key Vault
description: Informazioni su come creare un'identità gestita per le applicazioni del servizio app e su come usarla per accedere a Azure Key Vault
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: 6c7a9fdb5ed60023a82984fd5be5b424c634e679
ms.sourcegitcommit: a19f4b35a0123256e76f2789cd5083921ac73daf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/02/2019
ms.locfileid: "71720257"
---
# <a name="provide-key-vault-authentication-with-a-managed-identity"></a>Fornire Key Vault autenticazione con un'identità gestita

Un'identità gestita da Azure Active Directory consente all'app di accedere facilmente ad altre risorse protette da Azure AD. L'identità viene gestita dalla piattaforma Azure e non è necessario eseguire il provisioning o ruotare alcun segreto. Per altre informazioni, vedere [Identità gestite per le risorse di Azure](../active-directory/managed-identities-azure-resources/overview.md). 

Questo articolo illustra come creare un'identità gestita per un'applicazione del servizio app e usarla per accedere a Azure Key Vault. Per le applicazioni ospitate nelle macchine virtuali di Azure, vedere [usare un'identità gestita assegnata dal sistema VM Windows per accedere a Azure Key Vault](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-nonaad.md).


[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Prerequisiti 

Per completare questa guida, è necessario disporre delle risorse riportate di seguito. 

- Insieme di credenziali delle chiavi. È possibile usare un insieme di credenziali delle chiavi esistente o crearne uno nuovo attenendosi alla procedura descritta in una delle guide introduttive seguenti:
   - [Creare un Key Vault con l'interfaccia della riga di comando di Azure](quick-create-cli.md)
   - [Creare un insieme di credenziali delle chiavi con Azure PowerShell](quick-create-powershell.md)
   - [Creare un insieme di credenziali delle chiavi con l'portale di Azure](quick-create-portal.md).
- Un'applicazione del servizio app esistente a cui concedere l'accesso all'insieme di credenziali delle chiavi. È possibile crearne rapidamente uno attenendosi alla procedura descritta nella [documentazione del servizio app](../app-service/overview.md).
- [Interfaccia](/cli/azure/install-azure-cli?view=azure-cli-latest) della riga di comando di Azure o [Azure PowerShell](/powershell/azure/overview). In alternativa, è possibile usare la [portale di Azure](http://portal.azure.com).


## <a name="adding-a-system-assigned-identity"></a>Aggiunta di un'identità assegnata dal sistema 

In primo luogo, è necessario aggiungere un'identità assegnata dal sistema a un'applicazione. 
 
### <a name="azure-portal"></a>Portale di Azure 

Per configurare un'identità gestita nel portale, è prima necessario creare un'applicazione come di consueto e quindi abilitare la funzionalità. 

1. Se si usa un'app per le funzioni, passare a **Funzionalità della piattaforma**. Per altri tipi di app, scorrere verso il basso fino al gruppo **Impostazioni** nel riquadro di spostamento a sinistra. 

1. Selezionare **Identità gestita**. 

1. All'interno della scheda **Assegnata dal sistema** impostare **Stato** su **Attivato**. Fare clic su **Salva**. 

    ![](./media/managed-identity-system-assigned.png)

### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

Questa Guida introduttiva richiede l'interfaccia della riga di comando di Azure versione 2.0.4 o successiva. Eseguire `az --version` per trovare la versione corrente. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli?view=azure-cli-latest). 

Per accedere con l'interfaccia della riga di comando di Azure, usare il comando [AZ login](/cli/azure/reference-index?view=azure-cli-latest#az-login) :

```azurecli-interactive
az login
```

Per altre informazioni sulle opzioni di accesso con l'interfaccia della riga di comando di Azure, vedere [accedere con l'interfaccia](/cli/azure/authenticate-azure-cli?view=azure-cli-latest)della riga di comando. 

Per creare l'identità per l'applicazione, usare l'interfaccia della riga di comando di Azure [AZ webapp Identity Assign](/cli/azure/webapp/identity?view=azure-cli-latest#az-webapp-identity-assign) oppure il comando [AZ functionapp Identity Assign](/cli/azure/functionapp/identity?view=azure-cli-latest#az-functionapp-identity-assign) :


```azurecli-interactive
az webapp identity assign --name myApp --resource-group myResourceGroup
```

```azurecli-interactive
az functionapp identity assign --name myApp --resource-group myResourceGroup
```

Prendere nota dell'oggetto `PrincipalId`, che sarà necessario nella sezione successiva.

```json
{
  "principalId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "type": "SystemAssigned"
}
```
## <a name="grant-your-app-access-to-key-vault"></a>Concedere all'app l'accesso a Key Vault 

### <a name="azure-portal"></a>Portale di Azure

1.  Passare a Key Vault risorsa. 

1.  Selezionare **criteri di accesso** e fare clic su **Aggiungi criteri di accesso**. 

1.  In **autorizzazioni segrete**selezionare **Get, List**. 

1.  Scegliere **Seleziona entità**e nel campo di ricerca immettere il nome dell'app.  Selezionare l'app nell'elenco dei risultati e fare clic su **Seleziona**. 

1.  Fare clic su **Aggiungi** per completare l'aggiunta dei nuovi criteri di accesso.

    ![](./media/managed-identity-access-policy.png)

### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

Per concedere all'applicazione l'accesso all'insieme di credenziali delle chiavi, usare l'interfaccia della riga di comando di Azure [AZ Key Vault set-Policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) Command, specificando il parametro **ObjectID** con il **PrincipalId** annotato in precedenza.

```azurecli-interactive
az keyvault set-policy --name myKeyVault --object-id <PrincipalId> --secret-permissions get list 
```

## <a name="next-steps"></a>Passaggi successivi

- sicurezza Key Vault [Azure: Gestione delle identità e degli accessi @ no__t-0
- [Fornire Key Vault autenticazione con un criterio di controllo di accesso](key-vault-group-permissions-for-apps.md)
- [Informazioni su chiavi, segreti e certificati](about-keys-secrets-and-certificates.md)
- [Proteggere l'](key-vault-secure-your-key-vault.md)insieme di credenziali delle chiavi.
- [Guida per gli sviluppatori di Azure Key Vault](key-vault-developers-guide.md)
- Esaminare le [procedure consigliate per Azure Key Vault](key-vault-best-practices.md)