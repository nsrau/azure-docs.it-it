---
title: Configurare le chiavi gestite dal cliente per crittografare i dati inattivi in ISE
description: Creare e gestire le proprie chiavi di crittografia per proteggere i dati inattivi per gli ambienti del servizio di integrazione (ISE) in App per la logica di AzureCreate and manage your own encryption keys to secure data at rest for integration service environments (ISEs) in Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, rarayudu, logicappspm
ms.topic: conceptual
ms.date: 03/11/2020
ms.openlocfilehash: fa39c8f65b00283044ef31dc7577a4668b3e634b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127644"
---
# <a name="set-up-customer-managed-keys-to-encrypt-data-at-rest-for-integration-service-environments-ises-in-azure-logic-apps"></a>Configurare chiavi gestite dal cliente per crittografare i dati inattivi per gli ambienti del servizio di integrazione (ISE) in App per la logica di AzureSet up customer-managed keys to encrypt data at rest for integration service environments (ISEs) in Azure Logic Apps

Le app per la logica di Azure si basano su Archiviazione di Azure per archiviare e crittografare automaticamente i [dati inattivi.](../storage/common/storage-service-encryption.md) Questa crittografia protegge i dati e consente di soddisfare gli impegni di conformità e sicurezza dell'organizzazione. Per impostazione predefinita, Archiviazione di Azure usa chiavi gestite da Microsoft per crittografare i dati. Per altre informazioni sul funzionamento della crittografia di Archiviazione di Azure, vedere Crittografia di Archiviazione di Azure per i dati inattivi e [Crittografia dei dati di Azure inattivi.For](../security/fundamentals/encryption-atrest.md)more information about how Azure Storage encryption works, see Azure Storage encryption for data at [rest.](../storage/common/storage-service-encryption.md)

Quando si crea un ambiente del servizio di [integrazione (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) per l'hosting delle app per la logica e si vuole un maggiore controllo sulle chiavi di crittografia usate da Archiviazione di Azure, è possibile configurare, usare e gestire la propria chiave usando Archiviazione delle chiavi di [Azure.](../key-vault/key-vault-overview.md) Questa funzionalità è nota anche come "Bring Your Own Key" (BYOK) e la chiave viene definita "chiave gestita dal cliente".

Questo argomento illustra come configurare e specificare una chiave di crittografia personalizzata da usare quando si crea ISE usando l'API REST delle app per la logica. Per la procedura generale per creare un'API REST di ISE tramite app per la logica, vedere Creare un ambiente del [servizio di integrazione (ISE) usando l'API REST delle app per la logica.](../logic-apps/create-integration-service-environment-rest-api.md)

## <a name="considerations"></a>Considerazioni

* Al momento, il supporto delle chiavi gestite dal cliente per un ISE è disponibile solo nelle aree di Azure: Stati Uniti occidentali 2, Stati Uniti orientali e Stati Uniti centro-meridionali

* È possibile specificare una chiave gestita dal cliente *solo quando si crea ISE*, non successivamente. Non è possibile disattivare questa chiave dopo la creazione di ISE. Attualmente non è disponibile alcun supporto per la rotazione di una chiave gestita dal cliente per un ISE.

* Per supportare le chiavi gestite dal cliente, ISE richiede [l'abilitazione dell'identità gestita assegnata dal sistema.](../active-directory/managed-identities-azure-resources/overview.md#how-does-the-managed-identities-for-azure-resources-work) Questa identità consente a ISE di autenticare l'accesso alle risorse in altri tenant di Azure Active Directory (Azure AD) in modo che non sia necessario accedere con le credenziali.

* Attualmente, per creare un ISE che supporta le chiavi gestite dal cliente e la cui identità assegnata dal sistema è abilitata, è necessario chiamare l'API REST App per la logica usando una richiesta HTTPS PUT.

* Entro *30 minuti* dall'invio della richiesta HTTPS PUT che crea ISE, è necessario [concedere l'accesso all'insieme di credenziali delle chiavi per l'identità assegnata dal sistema dell'ISE.](#identity-access-to-key-vault) In caso contrario, la creazione di ISE non riesce e genera un errore di autorizzazione.

## <a name="prerequisites"></a>Prerequisiti

* Gli stessi [prerequisiti](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#prerequisites) e [requisiti per abilitare l'accesso per ISE](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#enable-access) come quando si crea un ISE nel portale di Azure

* Un insieme di credenziali delle chiavi di Azure con le proprietà **Soft Delete** e Do Not Purge abilitateAn Azure key vault that has the Soft Delete and **Do Not Purge** properties enabled

  Per altre informazioni sull'abilitazione di queste proprietà, vedere [Panoramica dell'eliminazione temporanea](../key-vault/key-vault-ovw-soft-delete.md) di Archiviazione delle chiavi di Azure e [Configurare le chiavi gestite dal cliente con L'insieme](../storage/common/storage-encryption-keys-portal.md)delle chiavi di Azure.For more information about enabling these properties, see Azure Key Vault soft-delete overview and Configure customer-managed keys with Azure Key Vault . Se non si ha familiarità con l'insieme di credenziali delle chiavi di Azure, vedere [come creare un insieme](../key-vault/quick-create-portal.md#create-a-vault) di credenziali delle chiavi usando il portale di Azure o il comando [New-AzKeyVault](https://docs.microsoft.com/powershell/module/az.keyvault/new-azkeyvault)di Azure.

* Nell'insieme di credenziali delle chiavi, una chiave creata con i seguenti valori di proprietà:

  | Proprietà | valore |
  |----------|-------|
  | **Tipo chiave** | RSA |
  | **Dimensione chiave RSA** | 2048 |
  | **Abilitato** | Sì |
  |||

  ![Creare la chiave di crittografia gestita dal clienteCreate your customer-managed encryption key](./media/customer-managed-keys-integration-service-environment/create-customer-managed-key-for-encryption.png)

  Per altre informazioni, vedere [Configurare le chiavi gestite dal cliente con](../storage/common/storage-encryption-keys-portal.md) L'insieme di credenziali delle chiavi di Azure o il comando di Azure PowerShell, [Add-AzKeyVaultKey](https://docs.microsoft.com/powershell/module/az.keyvault/Add-AzKeyVaultKey).

* Strumento che è possibile usare per creare ISE chiamando l'API REST app per la logica con una richiesta HTTPS PUT. Ad esempio, è possibile usare [Postman](https://www.getpostman.com/downloads/)oppure creare un'app per la logica che esegue questa attività.

<a name="enable-support-key-system-identity"></a>

## <a name="create-ise-with-key-vault-and-managed-identity-support"></a>Creare ISE con l'insieme di credenziali delle chiavi e il supporto delle identità gestiteCreate ISE with key vault and managed identity support

Per creare ISE chiamando l'API REST app per la logica, effettuare questa richiesta HTTPS PUT:To create your ISE by calling the Logic Apps REST API, make this HTTPS PUT request:

`PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Logic/integrationServiceEnvironments/{integrationServiceEnvironmentName}?api-version=2019-05-01`

> [!IMPORTANT]
> La versione dell'API REST App per la logica 2019-05-01 richiede di effettuare la propria richiesta HTTP PUT per i connettori ISE.

Il completamento della distribuzione richiede in genere entro due ore. In alcuni casi, la distribuzione potrebbe richiedere fino a quattro ore. Per controllare lo stato della distribuzione, nel portale di [Azure](https://portal.azure.com), sulla barra degli strumenti di Azure, selezionare l'icona delle notifiche, che apre il riquadro delle notifiche.

> [!NOTE]
> Se si verifica un errore di distribuzione o si elimina l'ISE, Azure potrebbe richiedere fino a un'ora prima di rilasciare le subnet. Questo ritardo significa che potrebbe essere necessario attendere prima di riutilizzare tali subnet in un altro ISE.
>
> Se si elimina la rete virtuale, Azure richiede in genere fino a due ore prima di rilasciare le subnet, ma questa operazione potrebbe richiedere più tempo. 
> Quando si eliminano reti virtuali, assicurarsi che nessuna risorsa sia ancora connessa. 
> Vedere [Eliminare rete virtuale](../virtual-network/manage-virtual-network.md#delete-a-virtual-network).

### <a name="request-header"></a>Intestazione della richiesta

Nell'intestazione della richiesta includere queste proprietà:In the request header, include these properties:

* `Content-type`: imposta il `application/json`valore di questa proprietà su .

* `Authorization`: impostare il valore di questa proprietà sul token di connessione per il cliente che ha accesso alla sottoscrizione o al gruppo di risorse di Azure che si vuole usare.

### <a name="request-body"></a>Corpo della richiesta

Nel corpo della richiesta abilitare il supporto per questi elementi aggiuntivi fornendo le relative informazioni nella definizione ISE:

* Identità gestita assegnata dal sistema utilizzata da ISE per accedere all'insieme di credenziali delle chiavi
* L'insieme di credenziali delle chiavi e la chiave gestita dal cliente che si desidera utilizzare

#### <a name="request-body-syntax"></a>Sintassi del corpo della richiesta

Di seguito è riportata la sintassi del corpo della richiesta, che descrive le proprietà da utilizzare quando si crea ISE:

```json
{
   "id": "/subscriptions/{Azure-subscription-ID/resourceGroups/{Azure-resource-group}/providers/Microsoft.Logic/integrationServiceEnvironments/{ISE-name}",
   "name": "{ISE-name}",
   "type": "Microsoft.Logic/integrationServiceEnvironments",
   "location": "{Azure-region}",
   "sku": {
      "name": "Premium",
      "capacity": 1
   },
   "identity": {
      "type": "SystemAssigned"
   },
   "properties": {
      "networkConfiguration": {
         "accessEndpoint": {
            // Your ISE can use the "External" or "Internal" endpoint. This example uses "External".
            "type": "External"
         },
         "subnets": [
            {
               "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Network/virtualNetworks/{virtual-network-name}/subnets/{subnet-1}",
            },
            {
               "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Network/virtualNetworks/{virtual-network-name}/subnets/{subnet-2}",
            },
            {
               "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Network/virtualNetworks/{virtual-network-name}/subnets/{subnet-3}",
            },
            {
               "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Network/virtualNetworks/{virtual-network-name}/subnets/{subnet-4}",
            }
         ]
      },
      "encryptionConfiguration": {
         "encryptionKeyReference": {
            "keyVault": {
               "id": "subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.KeyVault/vaults/{key-vault-name}",
            },
            "keyName": "{customer-managed-key-name}",
            "keyVersion": "{key-version-number}"
         }
      }
   }
}
```

#### <a name="request-body-example"></a>Esempio di corpo della richiesta

Questo corpo della richiesta di esempio mostra i valori di esempio:This example request body shows the sample values:

```json
{
   "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Logic/integrationServiceEnvironments/Fabrikam-ISE",
   "name": "Fabrikam-ISE",
   "type": "Microsoft.Logic/integrationServiceEnvironments",
   "location": "WestUS2",
   "identity": {
      "type": "SystemAssigned"
   },
   "sku": {
      "name": "Premium",
      "capacity": 1
   },
   "properties": {
      "networkConfiguration": {
         "accessEndpoint": {
            // Your ISE can use the "External" or "Internal" endpoint. This example uses "External".
            "type": "External"
         },
         "subnets": [
            {
               "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Network/virtualNetworks/Fabrikam-VNET/subnets/subnet-1",
            },
            {
               "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Network/virtualNetworks/Fabrikam-VNET/subnets/subnet-2",
            },
            {
               "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Network/virtualNetworks/Fabrikam-VNET/subnets/subnet-3",
            },
            {
               "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Network/virtualNetworks/Fabrikam-VNET/subnets/subnet-4",
            }
         ]
      },
      "encryptionConfiguration": {
         "encryptionKeyReference": {
            "keyVault": {
               "id": "subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.KeyVault/vaults/FabrikamKeyVault",
            },
            "keyName": "Fabrikam-Encryption-Key",
            "keyVersion": "********************"
         }
      }
   }
}
```

<a name="identity-access-to-key-vault"></a>

## <a name="grant-access-to-your-key-vault"></a>Concedere l'accesso all'insieme di credenziali delle chiavi

Entro *30 minuti* dall'invio della richiesta HTTP PUT per creare ISE, è necessario aggiungere un criterio di accesso all'insieme di credenziali delle chiavi per l'identità assegnata dal sistema di ISE. In caso contrario, la creazione per ISE non riesce e viene visualizzato un errore di autorizzazioni. 

Per questa attività, è possibile usare il comando Azure PowerShell Set-AzKeyVaultAccessPolicy oppure eseguire questi passaggi nel portale di Azure:For this task, you can use either the Azure PowerShell [Set-AzKeyVaultAccessPolicy](https://docs.microsoft.com/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) command, you can follow these steps in the Azure portal:

1. Nel portale di Azure aprire l'insieme di credenziali delle chiavi di Azure.In the [Azure portal,](https://portal.azure.com)open your Azure key vault.

1. Nel menu dell'insieme di credenziali delle chiavi selezionare **Criteri** > di accesso**Aggiungi criteri**di accesso , ad esempio:

   ![Aggiungere criteri di accesso per l'identità gestita assegnata dal sistemaAdd access policy for system-assigned managed identity](./media/customer-managed-keys-integration-service-environment/add-ise-access-policy-key-vault.png)

1. Dopo aver aperto il riquadro **Aggiungi criteri di accesso,** attenersi alla seguente procedura:

   1. Selezionare queste opzioni:

      | Impostazione | Valori |
      |---------|--------|
      | **Configura da elenco di modelli (facoltativo)** | Gestione chiavi |
      | **Autorizzazioni chiave** | - **Operazioni di gestione delle**chiavi : Get, List <p><p>- **Operazioni di crittografia**: Unwrap Key, Wrap Key |
      |||

      ![Selezionare "Gestione chiavi" > "Autorizzazioni chiave"](./media/customer-managed-keys-integration-service-environment/select-key-permissions.png)

   1. Per **Seleziona entità**, **selezionare Nessuno selezionato**. Dopo l'apertura del riquadro **Principale,** nella casella di ricerca individuare e selezionare ISE. Al termine, scegliere **Seleziona** > **Aggiungi**.

      ![Selezionare l'ISE da utilizzare come principale](./media/customer-managed-keys-integration-service-environment/select-service-principal-ise.png)

   1. Al termine del riquadro Criteri di **accesso** selezionare **Salva**.

Per ulteriori informazioni, consultate Fornire l'autenticazione dell'insieme di credenziali delle [chiavi con un'identità gestita.](../key-vault/managed-identity.md#grant-your-app-access-to-key-vault)

## <a name="next-steps"></a>Passaggi successivi

* Altre informazioni su [Azure Key Vault](../key-vault/key-vault-overview.md)