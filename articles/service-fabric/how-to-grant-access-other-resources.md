---
title: Concedere a un'applicazione l'accesso ad altre risorse di Azure
description: Questo articolo illustra come concedere l'accesso alle applicazioni Service Fabric abilitato per l'identità gestita ad altre risorse di Azure che supportano l'autenticazione basata su Azure Active Directory.
ms.topic: article
ms.date: 12/09/2019
ms.openlocfilehash: 638176ca2665146a64fb7a6bd83253eba3434a7f
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2020
ms.locfileid: "86258963"
---
# <a name="granting-a-service-fabric-applications-managed-identity-access-to-azure-resources-preview"></a>Concessione dell'accesso alle identità gestite di un'applicazione Service Fabric alle risorse di Azure (anteprima)

Prima che l'applicazione possa usare la propria identità gestita per accedere ad altre risorse, le autorizzazioni devono essere concesse a tale identità nella risorsa di Azure protetta a cui si accede. La concessione di autorizzazioni è in genere un'azione di gestione sul ' piano di controllo ' del servizio di Azure proprietario della risorsa protetta indirizzata tramite Azure Resource Manager, che imporrà qualsiasi controllo dell'accesso in base al ruolo applicabile.

La sequenza esatta di passaggi dipenderà quindi dal tipo di risorsa di Azure a cui si accede, nonché dal linguaggio/client utilizzato per concedere le autorizzazioni. Il resto dell'articolo presuppone che un'identità assegnata dall'utente sia assegnata all'applicazione e includa diversi esempi tipici per praticità, ma non è in alcun modo un riferimento esaustivo per questo argomento. per istruzioni aggiornate sulla concessione di autorizzazioni, vedere la documentazione dei rispettivi servizi di Azure.  

## <a name="granting-access-to-azure-storage"></a>Concessione dell'accesso ad archiviazione di Azure
È possibile usare l'identità gestita dell'applicazione Service Fabric, in questo caso assegnata dall'utente, per recuperare i dati da un BLOB di archiviazione di Azure. Concedere all'identità le autorizzazioni necessarie nel portale di Azure con i passaggi seguenti:

1. Passare all'account di archiviazione
2. Fare clic sul collegamento Controllo di accesso (IAM) nel pannello di sinistra.
3. opzionale Verifica accesso esistente: selezionare identità gestita di sistema o assegnata dall'utente nel controllo ' trova '; Selezionare l'identità appropriata dall'elenco dei risultati risultante
4. Fare clic su + Aggiungi assegnazione ruolo nella parte superiore della pagina per aggiungere una nuova assegnazione di ruolo per l'identità dell'applicazione.
In Ruolo scegliere Lettore dei dati del BLOB di archiviazione dall'elenco a discesa.
5. Nell'elenco a discesa successivo, in assegna accesso a, scegliere `User assigned managed identity` .
6. Assicurarsi quindi che la sottoscrizione appropriata sia presente nell'elenco a discesa Sottoscrizione e quindi impostare Gruppo di risorse su Tutti i gruppi di risorse.
7. In Seleziona scegliere il UAI corrispondente all'applicazione Service Fabric e quindi fare clic su Salva.

Il supporto per le identità gestite Service Fabric assegnate dal sistema non include l'integrazione nel portale di Azure; Se l'applicazione usa un'identità assegnata dal sistema, sarà necessario trovare prima l'ID client dell'identità dell'applicazione, quindi ripetere i passaggi precedenti, ma selezionando l' `Azure AD user, group, or service principal` opzione nel controllo trova.

## <a name="granting-access-to-azure-key-vault"></a>Concessione dell'accesso a Azure Key Vault
Analogamente all'accesso allo spazio di archiviazione, è possibile sfruttare l'identità gestita di un'applicazione Service Fabric per accedere a un insieme di credenziali delle chiavi di Azure. I passaggi per concedere l'accesso all'portale di Azure sono simili a quelli elencati in precedenza e non verranno ripetuti qui. Per le differenze, vedere l'immagine seguente.

![Criteri di accesso dell'insieme di credenziali delle chiavi](../key-vault/media/vs-secure-secret-appsettings/add-keyvault-access-policy.png)

Nell'esempio seguente viene illustrato come concedere l'accesso a un insieme di credenziali tramite una distribuzione modello. aggiungere i frammenti di codice riportati di seguito come un'altra voce sotto l' `resources` elemento del modello. Nell'esempio viene illustrata la concessione dell'accesso per i tipi di identità assegnati dall'utente e di sistema, rispettivamente, scegliere quella appropriata.

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

Per altri dettagli, vedere insiemi di credenziali [-aggiornare i criteri di accesso](/rest/api/keyvault/vaults/updateaccesspolicy).

## <a name="next-steps"></a>Passaggi successivi
* [Distribuire un'applicazione Service Fabric di Azure con un'identità gestita assegnata dal sistema](./how-to-deploy-service-fabric-application-system-assigned-managed-identity.md)
* [Distribuire un'applicazione Service Fabric di Azure con un'identità gestita assegnata dall'utente](./how-to-deploy-service-fabric-application-user-assigned-managed-identity.md)
