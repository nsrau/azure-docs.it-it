---
title: Azure Service Fabric-concedere a un'applicazione Service Fabric l'accesso ad altre risorse di Azure | Microsoft Docs
description: Questo articolo illustra come concedere l'accesso alle applicazioni Service Fabric abilitato per l'identità gestita ad altre risorse di Azure che supportano l'autenticazione basata su Azure Active Directory.
services: service-fabric
author: athinanthny
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.date: 08/08/2019
ms.author: atsenthi
ms.openlocfilehash: 1facd85563a54de2b703bbc8c192976db92c30f7
ms.sourcegitcommit: 55e0c33b84f2579b7aad48a420a21141854bc9e3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/19/2019
ms.locfileid: "69624898"
---
# <a name="granting-a-service-fabric-applications-managed-identity-access-to-azure-resources-preview"></a>Concessione dell'accesso alle identità gestite di un'applicazione Service Fabric alle risorse di Azure (anteprima)

Prima che l'applicazione possa usare la propria identità gestita per accedere ad altre risorse, le autorizzazioni devono essere concesse a tale identità nella risorsa di Azure protetta a cui si accede. La concessione di autorizzazioni è in genere un'azione di gestione sul ' piano di controllo ' del servizio di Azure proprietario della risorsa protetta indirizzata tramite Azure Resource Manager, che imporrà qualsiasi controllo dell'accesso in base al ruolo applicabile.

La sequenza esatta di passaggi dipenderà quindi dal tipo di risorsa di Azure a cui si accede, nonché dal linguaggio/client utilizzato per concedere le autorizzazioni. Il resto dell'articolo presuppone che un'identità assegnata dall'utente sia assegnata all'applicazione e includa diversi esempi tipici per praticità, ma non è in alcun modo un riferimento esaustivo per questo argomento. per istruzioni aggiornate sulla concessione di autorizzazioni, vedere la documentazione dei rispettivi servizi di Azure.  

## <a name="granting-access-to-azure-storage"></a>Concessione dell'accesso ad archiviazione di Azure
È possibile usare l'identità gestita dell'applicazione Service Fabric, in questo caso assegnata dall'utente, per recuperare i dati da un BLOB di archiviazione di Azure. Concedere all'identità le autorizzazioni necessarie nel portale di Azure con i passaggi seguenti:

1. Passare all'account di archiviazione
2. Fare clic sul collegamento controllo di accesso (IAM) nel pannello di sinistra.
3. opzionale Verifica accesso esistente: selezionare identità gestita di sistema o assegnata dall'utente nel controllo ' trova '; Selezionare l'identità appropriata dall'elenco dei risultati risultante
4. Fare clic su + Aggiungi assegnazione ruolo nella parte superiore della pagina per aggiungere una nuova assegnazione di ruolo per l'identità dell'applicazione.
Nell'elenco a discesa ruolo selezionare lettore dati BLOB di archiviazione.
5. Nell'elenco a discesa successivo, in assegna accesso a, `User assigned managed identity`scegliere.
6. Assicurarsi quindi che la sottoscrizione appropriata sia elencata nell'elenco a discesa sottoscrizione e quindi impostare gruppo di risorse su tutti i gruppi di risorse.
7. In Seleziona scegliere il UAI corrispondente all'applicazione Service Fabric e quindi fare clic su Salva.

Il supporto per le identità gestite Service Fabric assegnate dal sistema non include l'integrazione nel portale di Azure; Se l'applicazione usa un'identità assegnata dal sistema, sarà necessario trovare prima l'ID client dell'identità dell'applicazione, quindi ripetere i passaggi precedenti, ma selezionando l' `Azure AD user, group, or service principal` opzione nel controllo trova.

## <a name="granting-access-to-azure-key-vault"></a>Concessione dell'accesso a Azure Key Vault
Analogamente all'accesso allo spazio di archiviazione, è possibile sfruttare l'identità gestita di un'applicazione Service Fabric per accedere a un insieme di credenziali delle chiavi di Azure. I passaggi per concedere l'accesso all'portale di Azure sono simili a quelli elencati in precedenza e non verranno ripetuti qui. Per le differenze, vedere l'immagine seguente.

![Criteri di accesso dell'insieme di credenziali delle chiavi](../key-vault/media/vs-secure-secret-appsettings/add-keyvault-access-policy.png)

Nell'esempio seguente viene illustrato come concedere l'accesso a un insieme di credenziali tramite una distribuzione modello. aggiungere il frammento di codice seguente come altra `resources` voce sotto l'elemento del modello.

```json
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

Per altri dettagli, vedere insiemi di credenziali [-aggiornare i criteri di accesso](https://docs.microsoft.com/rest/api/keyvault/vaults/updateaccesspolicy).

## <a name="next-steps"></a>Passaggi successivi

* [Distribuire un'applicazione Service Fabric di Azure con un'identità gestita assegnata dal sistema](./how-to-deploy-service-fabric-application-system-assigned-managed-identity.md)

* [Distribuire un'applicazione Service Fabric di Azure con un'identità gestita assegnata dall'utente](./how-to-deploy-service-fabric-application-user-assigned-managed-identity.md)

## <a name="related-articles"></a>Articoli correlati

* Esaminare il [supporto di identità gestite](./concepts-managed-identity.md) in Azure Service Fabric

* [Distribuire un nuovo](./configure-new-azure-service-fabric-enable-managed-identity.md) Azure Service Fabric cluster con supporto di identità gestite 

* [Abilitare l'identità gestita](./configure-existing-cluster-enable-managed-identity-token-service.md) in un cluster di Service Fabric Azure esistente

* Utilizzare un'identità gestita dell'applicazione Service Fabric [dal codice sorgente](./how-to-managed-identity-service-fabric-app-code.md)

* Vedere l'elenco dei [servizi di Azure che supportano l'autenticazione Azure ad](../active-directory/managed-identities-azure-resources/services-support-msi.md)
