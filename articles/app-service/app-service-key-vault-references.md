---
title: USA riferimenti Key Vault
description: Informazioni su come configurare app Azure servizio e funzioni di Azure per l'uso di Azure Key Vault riferimenti. Rendere Key Vault segreti disponibili per il codice dell'applicazione.
author: mattchenderson
ms.topic: article
ms.date: 10/09/2019
ms.author: mahender
ms.custom: seodec18
ms.openlocfilehash: f0d3d36d3489557f0a07e9400c59debb2dca7073
ms.sourcegitcommit: 265f1d6f3f4703daa8d0fc8a85cbd8acf0a17d30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/02/2019
ms.locfileid: "74671461"
---
# <a name="use-key-vault-references-for-app-service-and-azure-functions"></a>Usare i riferimenti Key Vault per il servizio app e funzioni di Azure

> [!NOTE] 
> I riferimenti Key Vault non sono attualmente disponibili nei piani a consumo Linux.

Questo argomento illustra come usare i segreti da Azure Key Vault all'interno dell'applicazione di Servizio app o Funzioni di Azure senza che siano richieste modifiche al codice. [Azure Key Vault](../key-vault/key-vault-overview.md) è un servizio che supporta la gestione centralizzata dei segreti con controllo completo sui criteri di accesso e sulla cronologia di controllo.

## <a name="granting-your-app-access-to-key-vault"></a>Concedere all'app l'accesso a Key Vault

Per leggere i segreti da Key Vault, è necessario avere creato un insieme di credenziali e concedere all'app l'autorizzazione per accedervi.

1. Creare un insieme di credenziali seguendo l'[avvio rapido per Key Vault](../key-vault/quick-create-cli.md).

1. Creare un'[identità gestita assegnata dal sistema](overview-managed-identity.md) per l'applicazione.

   > [!NOTE] 
   > I riferimenti a Key Vault supportano attualmente solo identità gestite assegnate dal sistema. Non è possibile usare identità assegnate dall'utente.

1. Creare [criteri di accesso in Key Vault](../key-vault/key-vault-secure-your-key-vault.md#key-vault-access-policies) per l'identità di applicazione creata in precedenza. Abilitare l'autorizzazione per il segreto "Get" in questi criteri. Non configurare l'"applicazione autorizzata" o le impostazioni di `applicationId`, poiché questa operazione non è compatibile con un'identità gestita.

    > [!NOTE]
    > I riferimenti Key Vault non sono attualmente in grado di risolvere i segreti archiviati in un insieme di credenziali delle chiavi con [restrizioni di rete](../key-vault/key-vault-overview-vnet-service-endpoints.md).

## <a name="reference-syntax"></a>Sintassi del riferimento

Un riferimento a Key Vault viene espresso nel formato `@Microsoft.KeyVault({referenceString})`, in cui sostituire `{referenceString}` con una delle opzioni seguenti:

> [!div class="mx-tdBreakAll"]
> | Stringa di riferimento                                                            | Description                                                                                                                                                                                 |
> |-----------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
> | SecretUri=_secretUri_                                                       | **SecretUri** deve essere l'URI del piano dati completo di un segreto in Key Vault, inclusa una versione, ad esempio, https://myvault.vault.azure.net/secrets/mysecret/ec96f02080254f109c51a1f14cdb1931  |
> | VaultName=_vaultName_;SecretName=_secretName_;SecretVersion=_secretVersion_ | **VaultName** deve essere il nome della risorsa Key Vault. **SecretName** deve essere il nome del segreto di destinazione. **SecretVersion** deve essere la versione del segreto da usare. |

Un riferimento completo con la versione, ad esempio, sarà simile al seguente:

```
@Microsoft.KeyVault(SecretUri=https://myvault.vault.azure.net/secrets/mysecret/ec96f02080254f109c51a1f14cdb1931)
```
In alternativa:

```
@Microsoft.KeyVault(VaultName=myvault;SecretName=mysecret;SecretVersion=ec96f02080254f109c51a1f14cdb1931)
```


## <a name="source-application-settings-from-key-vault"></a>Impostazioni dell'applicazione di origine da Key Vault

Key Vault riferimenti possono essere utilizzati come valori per [le impostazioni dell'applicazione](configure-common.md#configure-app-settings), consentendo di tenere segreti in Key Vault invece che nella configurazione del sito. Le impostazioni dell'applicazione vengono crittografate in modo sicuro, ma se sono necessarie le funzionalità di gestione dei segreti, è opportuno entrare in Key Vault.

Per usare un riferimento a Key Vault per un'impostazione di applicazione, impostare il riferimento come valore dell'impostazione. L'app può fare riferimento al segreto tramite la relativa chiave come di consueto. Non sono necessarie modifiche al codice.

> [!TIP]
> La maggior parte delle impostazioni di applicazione che usano riferimenti a Key Vault deve essere contrassegnata come impostazioni slot, perché è consigliabile usare insiemi di credenziali separati per ogni ambiente.

### <a name="azure-resource-manager-deployment"></a>Distribuzione Azure Resource Manager

Durante l'automazione delle distribuzioni di risorse tramite modelli di Azure Resource Manager, potrebbe essere necessario disporre in sequenza le dipendenze in un determinato ordine per usufruire di questa funzionalità. Si noti che sarà necessario definire le impostazioni di applicazione come risorsa propria, invece di usare una proprietà `siteConfig` nella definizione del sito. Questo avviene perché il sito deve essere definito per primo, in modo che l'identità assegnata dal sistema venga creata insieme al sito e possa essere usata nei criteri di accesso.

Uno pseudo-modello di esempio per un'app per le funzioni potrebbe essere simile al seguente:

```json
{
    //...
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[variables('storageAccountName')]",
            //...
        },
        {
            "type": "Microsoft.Insights/components",
            "name": "[variables('appInsightsName')]",
            //...
        },
        {
            "type": "Microsoft.Web/sites",
            "name": "[variables('functionAppName')]",
            "identity": {
                "type": "SystemAssigned"
            },
            //...
            "resources": [
                {
                    "type": "config",
                    "name": "appsettings",
                    //...
                    "dependsOn": [
                        "[resourceId('Microsoft.Web/sites', variables('functionAppName'))]",
                        "[resourceId('Microsoft.KeyVault/vaults/', variables('keyVaultName'))]",
                        "[resourceId('Microsoft.KeyVault/vaults/secrets', variables('keyVaultName'), variables('storageConnectionStringName'))]",
                        "[resourceId('Microsoft.KeyVault/vaults/secrets', variables('keyVaultName'), variables('appInsightsKeyName'))]"
                    ],
                    "properties": {
                        "AzureWebJobsStorage": "[concat('@Microsoft.KeyVault(SecretUri=', reference(variables('storageConnectionStringResourceId')).secretUriWithVersion, ')')]",
                        "WEBSITE_CONTENTAZUREFILECONNECTIONSTRING": "[concat('@Microsoft.KeyVault(SecretUri=', reference(variables('storageConnectionStringResourceId')).secretUriWithVersion, ')')]",
                        "APPINSIGHTS_INSTRUMENTATIONKEY": "[concat('@Microsoft.KeyVault(SecretUri=', reference(variables('appInsightsKeyResourceId')).secretUriWithVersion, ')')]",
                        "WEBSITE_ENABLE_SYNC_UPDATE_SITE": "true"
                        //...
                    }
                },
                {
                    "type": "sourcecontrols",
                    "name": "web",
                    //...
                    "dependsOn": [
                        "[resourceId('Microsoft.Web/sites', variables('functionAppName'))]",
                        "[resourceId('Microsoft.Web/sites/config', variables('functionAppName'), 'appsettings')]"
                    ],
                }
            ]
        },
        {
            "type": "Microsoft.KeyVault/vaults",
            "name": "[variables('keyVaultName')]",
            //...
            "dependsOn": [
                "[resourceId('Microsoft.Web/sites', variables('functionAppName'))]"
            ],
            "properties": {
                //...
                "accessPolicies": [
                    {
                        "tenantId": "[reference(concat('Microsoft.Web/sites/',  variables('functionAppName'), '/providers/Microsoft.ManagedIdentity/Identities/default'), '2015-08-31-PREVIEW').tenantId]",
                        "objectId": "[reference(concat('Microsoft.Web/sites/',  variables('functionAppName'), '/providers/Microsoft.ManagedIdentity/Identities/default'), '2015-08-31-PREVIEW').principalId]",
                        "permissions": {
                            "secrets": [ "get" ]
                        }
                    }
                ]
            },
            "resources": [
                {
                    "type": "secrets",
                    "name": "[variables('storageConnectionStringName')]",
                    //...
                    "dependsOn": [
                        "[resourceId('Microsoft.KeyVault/vaults/', variables('keyVaultName'))]",
                        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
                    ],
                    "properties": {
                        "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountResourceId'),'2015-05-01-preview').key1)]"
                    }
                },
                {
                    "type": "secrets",
                    "name": "[variables('appInsightsKeyName')]",
                    //...
                    "dependsOn": [
                        "[resourceId('Microsoft.KeyVault/vaults/', variables('keyVaultName'))]",
                        "[resourceId('Microsoft.Insights/components', variables('appInsightsName'))]"
                    ],
                    "properties": {
                        "value": "[reference(resourceId('microsoft.insights/components/', variables('appInsightsName')), '2015-05-01').InstrumentationKey]"
                    }
                }
            ]
        }
    ]
}
```

> [!NOTE] 
> In questo esempio, la distribuzione del controllo del codice sorgente dipende dalle impostazioni dell'applicazione. Si tratta in genere di un comportamento non sicuro, dato che l'aggiornamento delle impostazioni dell'applicazione ha un comportamento asincrono. Tuttavia, dato che è stata inclusa l'impostazione dell'applicazione `WEBSITE_ENABLE_SYNC_UPDATE_SITE`, l'aggiornamento è sincrono. Questo significa che la distribuzione del controllo del codice sorgente inizierà solo dopo l'aggiornamento completo delle impostazioni dell'applicazione.

## <a name="troubleshooting-key-vault-references"></a>Risoluzione dei problemi relativi ai riferimenti a Key Vault

Se un riferimento non viene risolto correttamente, verrà utilizzato il valore di riferimento. Ciò significa che, per le impostazioni dell'applicazione, viene creata una variabile di ambiente il cui valore ha la sintassi `@Microsoft.KeyVault(...)`. Questa operazione può causare la generazione di errori da parte dell'applicazione, perché era previsto un segreto di una determinata struttura.

In genere, ciò è dovuto a un errore di configurazione dei [criteri di accesso key Vault](#granting-your-app-access-to-key-vault). Tuttavia, potrebbe anche essere dovuto a un segreto non più esistente o a un errore di sintassi nel riferimento stesso.

Se la sintassi è corretta, è possibile visualizzare altre cause per l'errore controllando lo stato di risoluzione corrente nel portale. Passare a impostazioni applicazione e selezionare "modifica" per il riferimento in questione. Sotto la configurazione delle impostazioni verranno visualizzate le informazioni sullo stato, inclusi gli eventuali errori. L'assenza di questi significa che la sintassi di riferimento non è valida.

Per ottenere altre informazioni, è anche possibile usare uno dei rilevatori predefiniti.

### <a name="using-the-detector-for-app-service"></a>Uso del detector per il servizio app

1. Nel portale passare all'app.
2. Selezionare **diagnostica e Risolvi i problemi**.
3. Scegliere **disponibilità e prestazioni** e selezionare **app Web in basso.**
4. Individuare **Key Vault Application Settings Diagnostics** e fare clic su **altre informazioni**.


### <a name="using-the-detector-for-azure-functions"></a>Uso del detector per funzioni di Azure

1. Nel portale passare all'app.
2. Passare a **funzionalità della piattaforma.**
3. Selezionare **diagnostica e Risolvi i problemi**.
4. Scegliere **disponibilità e prestazioni** e selezionare l'app per le **funzioni inattiva o segnalazione errori.**
5. Fare clic su **Key Vault impostazioni applicazione diagnostica.**
