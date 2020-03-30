---
title: Concedere a un'applicazione l'accesso ad altre risorse di AzureGrant an application access to other Azure resources
description: Questo articolo illustra come concedere all'applicazione Service Fabric abilitata per l'identità gestita l'accesso ad altre risorse di Azure che supportano l'autenticazione basata su Azure Active Directory.This article explains how to grant your managed-identity-enabled Service Fabric application access to other Azure resources supporting Azure Active Directory-based authentication.
ms.topic: article
ms.date: 12/09/2019
ms.openlocfilehash: 3b1feab1e67e993df771564a1a7c1aba4236b2c0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75614794"
---
# <a name="granting-a-service-fabric-applications-managed-identity-access-to-azure-resources-preview"></a>Concessione dell'accesso dell'identità gestita di un'applicazione Service Fabric alle risorse di Azure (anteprima)Granting a service Fabric application's managed identity access to Azure resources (preview)

Prima che l'applicazione possa usare la propria identità gestita per accedere ad altre risorse, è necessario concedere autorizzazioni a tale identità nella risorsa di Azure protetta a cui si accede. La concessione delle autorizzazioni è in genere un'azione di gestione sul "piano di controllo" del servizio di Azure proprietario della risorsa protetta instradata tramite Azure Resource Manager, che applicherà qualsiasi controllo dell'accesso basato sui ruoli applicabile.

L'esatta sequenza di passaggi dipenderà quindi dal tipo di risorsa di Azure a cui si accede, nonché dalla lingua o dal client usato per concedere le autorizzazioni. Nella parte restante dell'articolo si presuppone un'identità assegnata dall'utente assegnata all'applicazione e include diversi esempi tipici per comodità, ma non è in alcun modo un riferimento esaustivo per questo argomento. consultare la documentazione dei rispettivi servizi di Azure per istruzioni aggiornate sulla concessione delle autorizzazioni.  

## <a name="granting-access-to-azure-storage"></a>Concessione dell'accesso ad Archiviazione di AzureGranting access to Azure Storage
È possibile usare l'identità gestita dell'applicazione Service Fabric (in questo caso assegnata dall'utente) per recuperare i dati da un BLOB di archiviazione di Azure.You can use the Service Fabric application's managed identity (user-assigned in this case) to retrieve the data from an Azure storage blob. Concedere all'identità le autorizzazioni necessarie nel portale di Azure con i passaggi seguenti:Grant the identity the required permissions in the Azure portal with the following steps:

1. Passare all'account di archiviazione
2. Fare clic sul collegamento Controllo di accesso (IAM) nel pannello di sinistra.
3. (opzionale) Controllare l'accesso esistente: selezionare Identità gestita assegnata dal sistema o dall'utente nel controllo 'Trova'; selezionare l'identità appropriata dall'elenco dei risultati risultante
4. Fare clic su Aggiungi assegnazione ruolo nella parte superiore della pagina per aggiungere una nuova assegnazione di ruolo per l'identità dell'applicazione.
In Ruolo scegliere Lettore dei dati del BLOB di archiviazione dall'elenco a discesa.
5. Nell'elenco a discesa successivo, `User assigned managed identity`in Assegna accesso a, scegliere .
6. Assicurarsi quindi che la sottoscrizione appropriata sia presente nell'elenco a discesa Sottoscrizione e quindi impostare Gruppo di risorse su Tutti i gruppi di risorse.
7. In Seleziona scegliere l'UAI corrispondente all'applicazione Service Fabric e quindi fare clic su Salva.Under Select, choose the UAI corresponding to the Service Fabric application and then click Save.

Il supporto per le identità gestite di Service Fabric assegnate dal sistema non include l'integrazione nel portale di Azure. Se l'applicazione utilizza un'identità assegnata dal sistema, sarà necessario innanzitutto trovare l'ID client `Azure AD user, group, or service principal` dell'identità dell'applicazione e quindi ripetere i passaggi precedenti, ma selezionando l'opzione nel controllo Find.

## <a name="granting-access-to-azure-key-vault"></a>Concessione dell'accesso all'insieme di credenziali delle chiavi di AzureGranting access to Azure Key Vault
Analogamente, con l'accesso all'archiviazione, è possibile sfruttare l'identità gestita di un'applicazione Service Fabric per accedere a un insieme di credenziali delle chiavi di Azure.Similarly with accessing storage, you can leverage the managed identity of a Service Fabric application to access an Azure key vault. I passaggi per concedere l'accesso nel portale di Azure sono simili a quelli elencati in precedenza e non verranno ripetuti qui. Fare riferimento all'immagine seguente per le differenze.

![Criteri di accesso dell'insieme di credenziali delle chiavi](../key-vault/media/vs-secure-secret-appsettings/add-keyvault-access-policy.png)

Nell'esempio seguente viene illustrata la concessione dell'accesso a un insieme di credenziali tramite una distribuzione di modelli. aggiungere i frammenti di codice sottostanti come un'altra voce sotto l'elemento `resources` del modello. Nell'esempio viene illustrata la concessione dell'accesso sia per i tipi di identità assegnati dall'utente che per quelli assegnati dal sistema, rispettivamente: scegliere quella applicabile.

```json
    # under 'variables':
  "variables": {
        "userAssignedIdentityResourceId" : "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities/', parameters('userAssignedIdentityName'))]",
    }
    # under 'resources':
    {
        "type": "Microsoft.KeyVault/vaults/accessPolicies",
        "name": "[concat(parameters('keyVaultName'), '/add')]",
        "apiVersion": "2018-02-14",
        "properties": {
            "accessPolicies": [
                {
                    "tenantId": "[reference(variables('userAssignedIdentityResourceId'), '2018-11-30').tenantId]",
                    "objectId": "[reference(variables('userAssignedIdentityResourceId'), '2018-11-30').principalId]",
                    "dependsOn": [
                        "[variables('userAssignedIdentityResourceId')]"
                    ],
                    "permissions": {
                        "keys":         ["get", "list"],
                        "secrets":      ["get", "list"],
                        "certificates": ["get", "list"]
                    }
                }
            ]
        }
    },
```
E per le identità gestite assegnate dal sistema:
```json
    # under 'variables':
  "variables": {
        "sfAppSystemAssignedIdentityResourceId": "[concat(resourceId('Microsoft.ServiceFabric/clusters/applications/', parameters('clusterName'), parameters('applicationName')), '/providers/Microsoft.ManagedIdentity/Identities/default')]"
    }
    # under 'resources':
    {
        "type": "Microsoft.KeyVault/vaults/accessPolicies",
        "name": "[concat(parameters('keyVaultName'), '/add')]",
        "apiVersion": "2018-02-14",
        "properties": {
            "accessPolicies": [
            {
                    "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'))]",
                    "tenantId": "[reference(variables('sfAppSystemAssignedIdentityResourceId'), '2018-11-30').tenantId]",
                    "objectId": "[reference(variables('sfAppSystemAssignedIdentityResourceId'), '2018-11-30').principalId]",
                    "dependsOn": [
                        "[variables('sfAppSystemAssignedIdentityResourceId')]"
                    ],
                    "permissions": {
                        "secrets": [
                            "get",
                            "list"
                        ],
                        "certificates": 
                        [
                            "get", 
                            "list"
                        ]
                    }
            },
        ]
        }
    }
```

Per ulteriori dettagli, vedere [Vaults - Update Access Policy](https://docs.microsoft.com/rest/api/keyvault/vaults/updateaccesspolicy).

## <a name="next-steps"></a>Passaggi successivi
* [Distribuire un'applicazione Azure Service Fabric con un'identità gestita assegnata dal sistemaDeploy an Azure Service Fabric application with a system-assigned managed identity](./how-to-deploy-service-fabric-application-system-assigned-managed-identity.md)
* [Distribuire un'applicazione Azure Service Fabric con un'identità gestita assegnata dall'utenteDeploy an Azure Service Fabric application with a user-assigned managed identity](./how-to-deploy-service-fabric-application-user-assigned-managed-identity.md)