---
title: Configurare chiavi gestite dal cliente per la crittografia dei dati inattivi in ISEs
description: Creare e gestire le proprie chiavi di crittografia per proteggere i dati inattivi per gli ambienti di Integration Services (ISEs) in app per la logica di Azure
services: logic-apps
ms.suite: integration
ms.reviewer: mijos, rarayudu, logicappspm
ms.topic: conceptual
ms.date: 11/20/2020
ms.openlocfilehash: 59c60c876058f8664b38411b562e57c2d5cdc2a8
ms.sourcegitcommit: df66dff4e34a0b7780cba503bb141d6b72335a96
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96510625"
---
# <a name="set-up-customer-managed-keys-to-encrypt-data-at-rest-for-integration-service-environments-ises-in-azure-logic-apps"></a>Configurare chiavi gestite dal cliente per la crittografia dei dati inattivi per gli ambienti di Integration Services (ISEs) in app per la logica di Azure

App per la logica di Azure si basa su archiviazione di Azure per archiviare e [crittografare automaticamente i dati](../storage/common/storage-service-encryption.md)inattivi. Questa crittografia protegge i dati e consente di soddisfare gli impegni di sicurezza e conformità dell'organizzazione. Per impostazione predefinita, archiviazione di Azure usa chiavi gestite da Microsoft per crittografare i dati. Per altre informazioni sul funzionamento della crittografia di archiviazione di Azure, vedere [crittografia di archiviazione di Azure per dati](../storage/common/storage-service-encryption.md) inattivi e [crittografia dei dati](../security/fundamentals/encryption-atrest.md)inattivi di Azure.

Quando si crea un [ambiente Integration Services (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) per ospitare le app per la logica e si desidera un maggiore controllo sulle chiavi di crittografia usate da archiviazione di Azure, è possibile configurare, usare e gestire la propria chiave usando [Azure Key Vault](../key-vault/general/overview.md). Questa funzionalità è nota anche come "Bring Your Own Key" (BYOK) e la chiave è detta "chiave gestita dal cliente".

Questo argomento illustra come configurare e specificare la propria chiave di crittografia da usare quando si crea ISE usando l'API REST di app per la logica. Per la procedura generale per creare un ISE tramite l'API REST di app per la logica, vedere [creare un ambiente del servizio di integrazione (ISE) usando l'API REST di app per la logica](../logic-apps/create-integration-service-environment-rest-api.md).

## <a name="considerations"></a>Considerazioni

* Al momento, il supporto delle chiavi gestite dal cliente per ISE è disponibile solo in queste aree di Azure: Stati Uniti occidentali 2, Stati Uniti orientali e Stati Uniti centro-meridionali

* È possibile specificare una chiave gestita dal cliente *solo quando si crea ISE*, non in seguito. Non è possibile disabilitare questa chiave dopo la creazione di ISE. Attualmente non è disponibile alcun supporto per la rotazione di una chiave gestita dal cliente per un ISE.

* Per supportare le chiavi gestite dal cliente, ISE richiede l'abilitazione dell' [identità gestita assegnata dal sistema o dall'utente](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types). Questa identità consente all'ISE di autenticare l'accesso alle risorse protette, ad esempio le macchine virtuali e altri sistemi o servizi, che si trovano in o si connettono a una rete virtuale di Azure. In questo modo, non è necessario accedere con le proprie credenziali.

* Attualmente, per creare un ISE che supporti chiavi gestite dal cliente e che abbia abilitato il tipo di identità gestito, è necessario chiamare l'API REST di app per la logica usando una richiesta PUT HTTPS.

* È necessario [concedere l'accesso all'insieme di credenziali delle chiavi all'identità gestita di ISE](#identity-access-to-key-vault), ma la tempistica dipende dall'identità gestita usata.

  * **Identità gestita assegnata dal sistema**: entro *30 minuti dopo* l'invio della richiesta PUT HTTPS per la creazione di ISE, è necessario [concedere l'accesso all'insieme di credenziali delle chiavi all'identità gestita di ISE](#identity-access-to-key-vault). In caso contrario, la creazione di ISE ha esito negativo e viene ricevuto un errore di autorizzazione.

  * **Identità gestita assegnata dall'utente**: prima di inviare la richiesta HTTPS put che crea ISE, [concedere a Key Vault l'accesso all'identità gestita di ISE](#identity-access-to-key-vault).

## <a name="prerequisites"></a>Prerequisiti

* Gli stessi [prerequisiti](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#prerequisites) e [requisiti per abilitare l'accesso per ISE](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#enable-access) come quando si crea un ISE nel portale di Azure

* Un insieme di credenziali delle chiavi di Azure con le proprietà **Elimina temporaneamente** e non **Ripulisci** abilitate

  Per ulteriori informazioni sull'abilitazione di queste proprietà, vedere [Azure Key Vault Panoramica dell'eliminazione](../key-vault/general/soft-delete-overview.md) temporanea e [configurare chiavi gestite dal cliente con Azure Key Vault](../storage/common/customer-managed-keys-configure-key-vault.md). Se non si ha familiarità con [Azure Key Vault](../key-vault/general/overview.md), informazioni su come creare un insieme di credenziali delle chiavi usando [portale di Azure](../key-vault/general/quick-create-portal.md), l'interfaccia della riga di comando di [Azure](../key-vault/general/quick-create-cli.md)o [Azure PowerShell](../key-vault/general/quick-create-powershell.md).

* Nell'insieme di credenziali delle chiavi, una chiave creata con i valori delle proprietà seguenti:

  | Proprietà | Valore |
  |----------|-------|
  | **Tipo chiave** | RSA |
  | **Dimensioni della chiave RSA** | 2048 |
  | **Attivata** | Sì |
  |||

  ![Creare la chiave di crittografia gestita dal cliente](./media/customer-managed-keys-integration-service-environment/create-customer-managed-key-for-encryption.png)

  Per ulteriori informazioni, vedere [configurare chiavi gestite dal cliente con Azure Key Vault](../storage/common/customer-managed-keys-configure-key-vault.md) o il comando Azure PowerShell, [Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey).

* Uno strumento che è possibile usare per creare ISE chiamando l'API REST di app per la logica con una richiesta PUT HTTPS. Ad esempio, è possibile usare il [post](https://www.getpostman.com/downloads/), oppure è possibile compilare un'app per la logica che esegue questa attività.

<a name="enable-support-key-managed-identity"></a>

## <a name="create-ise-with-key-vault-and-managed-identity-support"></a>Creare ISE con Key Vault e il supporto di identità gestite

Per creare ISE chiamando l'API REST di app per la logica, effettuare questa richiesta HTTPS PUT:

`PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Logic/integrationServiceEnvironments/{integrationServiceEnvironmentName}?api-version=2019-05-01`

> [!IMPORTANT]
> Per la versione dell'API REST di app per la logica 2019-05-01 è necessario effettuare una richiesta PUT HTTPS per i connettori ISE.

Il completamento della distribuzione richiede in genere entro due ore. In alcuni casi, la distribuzione potrebbe richiedere fino a quattro ore. Per controllare lo stato della distribuzione, nella [portale di Azure](https://portal.azure.com)della barra degli strumenti di Azure selezionare l'icona notifiche, che consente di aprire il riquadro notifiche.

> [!NOTE]
> Se si verifica un errore di distribuzione o si elimina l'ISE, Azure potrebbe richiedere fino a un'ora prima di rilasciare le subnet. Questo ritardo indica che potrebbe essere necessario attendere prima di riusare tali subnet in un altro ISE.
>
> Se si elimina la rete virtuale, Azure impiega in genere fino a due ore per rilasciare le subnet, ma questa operazione potrebbe richiedere più tempo. 
> Quando si eliminano le reti virtuali, assicurarsi che non ci siano risorse ancora connesse. 
> Vedere [Eliminare la rete virtuale](../virtual-network/manage-virtual-network.md#delete-a-virtual-network).

### <a name="request-header"></a>Intestazione della richiesta

Nell'intestazione della richiesta includere le proprietà seguenti:

* `Content-type`: Impostare il valore della proprietà su `application/json` .

* `Authorization`: Impostare questo valore della proprietà sul bearer token per il cliente che ha accesso alla sottoscrizione o al gruppo di risorse di Azure che si vuole usare.

### <a name="request-body"></a>Corpo della richiesta

Nel corpo della richiesta, abilitare il supporto per questi elementi aggiuntivi fornendo le informazioni nella definizione ISE:

* Identità gestita usata da ISE per accedere all'insieme di credenziali delle chiavi
* L'insieme di credenziali delle chiavi e la chiave gestita dal cliente che si vuole usare

#### <a name="request-body-syntax"></a>Sintassi del corpo della richiesta

Ecco la sintassi del corpo della richiesta, che descrive le proprietà da usare quando si crea ISE:

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
      "type": <"SystemAssigned" | "UserAssigned">,
      // When type is "UserAssigned", include the following "userAssignedIdentities" object:
      "userAssignedIdentities": {
         "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{user-assigned-managed-identity-object-ID}": {
            "principalId": "{principal-ID}",
            "clientId": "{client-ID}"
         }
      }
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

Questo corpo della richiesta di esempio mostra i valori di esempio:

```json
{
   "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Logic/integrationServiceEnvironments/Fabrikam-ISE",
   "name": "Fabrikam-ISE",
   "type": "Microsoft.Logic/integrationServiceEnvironments",
   "location": "WestUS2",
   "identity": {
      "type": "UserAssigned",
      "userAssignedIdentities": {
         "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.ManagedIdentity/userAssignedIdentities/*********************************": {
            "principalId": "*********************************",
            "clientId": "*********************************"
         }
      }
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

Sebbene le tempistiche variano in base all'identità gestita usata, è necessario concedere l' [accesso all'insieme di credenziali delle chiavi all'identità gestita di ISE](#identity-access-to-key-vault).

* **Identità gestita assegnata dal sistema**: entro *30 minuti dopo* l'invio della richiesta PUT HTTPS per la creazione di ISE, è necessario aggiungere un criterio di accesso all'insieme di credenziali delle chiavi per l'identità gestita assegnata dal sistema di ISE. In caso contrario, la creazione di ISE ha esito negativo e viene ricevuto un errore di autorizzazione.

* **Identità gestita assegnata dall'utente**: prima di inviare la richiesta HTTPS put per la creazione di ISE, aggiungere un criterio di accesso all'insieme di credenziali delle chiavi per l'identità gestita assegnata dall'utente di ISE.

Per questa attività, è possibile usare il comando Azure PowerShell [set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) oppure è possibile seguire questa procedura nel portale di Azure:

1. Nella [portale di Azure](https://portal.azure.com)aprire l'insieme di credenziali delle chiavi di Azure.

1. Nel menu Key Vault selezionare criteri di **accesso**  >  **Aggiungi criteri di accesso**, ad esempio:

   ![Aggiungere i criteri di accesso per l'identità gestita assegnata dal sistema](./media/customer-managed-keys-integration-service-environment/add-ise-access-policy-key-vault.png)

1. Dopo aver aperto il riquadro **Aggiungi criteri di accesso** , seguire questa procedura:

   1. Selezionare le opzioni seguenti:

      | Impostazione | Valori |
      |---------|--------|
      | **Configura da modello (facoltativo) elenco** | Gestione chiavi |
      | **Autorizzazioni chiave** | - **Operazioni di gestione delle chiavi**: Get, List <p><p>- **Operazioni di crittografia**: chiave Unwrap, chiave a capo |
      |||

      ![Selezionare "gestione chiavi" > "autorizzazioni chiave"](./media/customer-managed-keys-integration-service-environment/select-key-permissions.png)

   1. Per **Seleziona entità** selezionare **Nessuno selezionato**. Quando si apre il riquadro **principale** , nella casella di ricerca trovare e selezionare ISE. Al termine, scegliere **Seleziona**  >  **Aggiungi**.

      ![Selezionare ISE da usare come principale](./media/customer-managed-keys-integration-service-environment/select-service-principal-ise.png)

   1. Al termine del riquadro Criteri di **accesso** selezionare **Salva**.

Per ulteriori informazioni, vedere [come eseguire l'autenticazione a Key Vault](../key-vault/general/authentication.md) e [assegnare un criterio di accesso key Vault](../key-vault/general/assign-access-policy-portal.md).

## <a name="next-steps"></a>Passaggi successivi

* Altre informazioni su [Azure Key Vault](../key-vault/general/overview.md)