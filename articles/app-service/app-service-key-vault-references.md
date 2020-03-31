---
title: Usare i riferimenti dell'insieme di credenziali delle chiaviUse Key Vault references
description: Informazioni su come configurare il servizio app di Azure e le funzioni di Azure per usare i riferimenti dell'insieme di chiavi di Azure.Learn how to set up Azure App Service and Azure Functions to use Azure Key Vault references. Rendere i segreti dell'insieme di credenziali delle chiavi disponibili per il codice dell'applicazione.
author: mattchenderson
ms.topic: article
ms.date: 10/09/2019
ms.author: mahender
ms.custom: seodec18
ms.openlocfilehash: 7fdb7c980a278e2dcd4b64a4b70de50721d0b72a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79280339"
---
# <a name="use-key-vault-references-for-app-service-and-azure-functions"></a>Usare i riferimenti dell'insieme di credenziali delle chiavi per il servizio app e le funzioni di AzureUse Key Vault references for App Service and Azure Functions

Questo argomento illustra come usare i segreti da Azure Key Vault all'interno dell'applicazione di Servizio app o Funzioni di Azure senza che siano richieste modifiche al codice. [Azure Key Vault](../key-vault/key-vault-overview.md) è un servizio che supporta la gestione centralizzata dei segreti con controllo completo sui criteri di accesso e sulla cronologia di controllo.

## <a name="granting-your-app-access-to-key-vault"></a>Concedere all'app l'accesso a Key Vault

Per leggere i segreti da Key Vault, è necessario avere creato un insieme di credenziali e concedere all'app l'autorizzazione per accedervi.

1. Creare un insieme di credenziali seguendo l'[avvio rapido per Key Vault](../key-vault/quick-create-cli.md).

1. Creare un'[identità gestita assegnata dal sistema](overview-managed-identity.md) per l'applicazione.

   > [!NOTE] 
   > I riferimenti a Key Vault supportano attualmente solo identità gestite assegnate dal sistema. Non è possibile usare identità assegnate dall'utente.

1. Creare [criteri di accesso in Key Vault](../key-vault/key-vault-secure-your-key-vault.md#key-vault-access-policies) per l'identità di applicazione creata in precedenza. Abilitare l'autorizzazione per il segreto "Get" in questi criteri. Non configurare l'"applicazione autorizzata" o le impostazioni di `applicationId`, poiché questa operazione non è compatibile con un'identità gestita.

    > [!NOTE]
    > I riferimenti dell'insieme di credenziali delle chiavi non sono attualmente in grado di risolvere i segreti archiviati in un insieme di credenziali delle chiavi con restrizioni di [rete.](../key-vault/key-vault-overview-vnet-service-endpoints.md)

## <a name="reference-syntax"></a>Sintassi del riferimento

Un riferimento a Key Vault viene espresso nel formato `@Microsoft.KeyVault({referenceString})`, in cui sostituire `{referenceString}` con una delle opzioni seguenti:

> [!div class="mx-tdBreakAll"]
> | Stringa di riferimento                                                            | Descrizione                                                                                                                                                                                 |
> |-----------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
> | SecretUri=_secretUri_                                                       | **SecretUri** deve essere l'URI del piano dati completo di un segreto in Key Vault, inclusa una versione, ad esempio, https://myvault.vault.azure.net/secrets/mysecret/ec96f02080254f109c51a1f14cdb1931  |
> | VaultName=_vaultName_;SecretName=_secretName_;SecretVersion=_secretVersion_ | **VaultName** deve essere il nome della risorsa Key Vault. **SecretName** deve essere il nome del segreto di destinazione. **SecretVersion** deve essere la versione del segreto da usare. |

Ad esempio, un riferimento completo con Version sarà simile al seguente:For example, a complete reference with Version would look like the following:

```
@Microsoft.KeyVault(SecretUri=https://myvault.vault.azure.net/secrets/mysecret/ec96f02080254f109c51a1f14cdb1931)
```
In alternativa:

```
@Microsoft.KeyVault(VaultName=myvault;SecretName=mysecret;SecretVersion=ec96f02080254f109c51a1f14cdb1931)
```


## <a name="source-application-settings-from-key-vault"></a>Impostazioni dell'applicazione di origine da Key Vault

I riferimenti dell'insieme di credenziali delle chiavi possono essere utilizzati come valori per [Impostazioni applicazione](configure-common.md#configure-app-settings), consentendo di mantenere segreti nell'insieme di credenziali delle chiavi anziché nella configurazione del sito. Le impostazioni dell'applicazione vengono crittografate in modo sicuro inattivi, ma se sono necessarie funzionalità di gestione segreta, devono accedere all'insieme di credenziali delle chiavi.

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

## <a name="troubleshooting-key-vault-references"></a>Risoluzione dei problemi relativi ai riferimenti dell'insieme di credenziali delle

Se un riferimento non viene risolto correttamente, verrà utilizzato il valore di riferimento. Ciò significa che per le impostazioni dell'applicazione, `@Microsoft.KeyVault(...)` verrebbe creata una variabile di ambiente il cui valore ha la sintassi. Ciò può causare l'applicazione generare errori, come si aspettava un segreto di una certa struttura.

In genere, ciò è dovuto a una configurazione errata dei criteri di [accesso dell'insieme](#granting-your-app-access-to-key-vault)di credenziali delle chiavi. Tuttavia, potrebbe anche essere dovuto a un segreto non più esistente o un errore di sintassi nel riferimento stesso.

Se la sintassi è corretta, è possibile visualizzare altre cause di errore controllando lo stato di risoluzione corrente nel portale. Passare a Impostazioni applicazione e selezionare "Modifica" per il riferimento in questione. Sotto la configurazione dell'impostazione, dovrebbero essere visualizzate informazioni sullo stato, inclusi eventuali errori. L'assenza di questi implica che la sintassi di riferimento non è valida.

È inoltre possibile utilizzare uno dei rilevatori incorporati per ottenere informazioni aggiuntive.

### <a name="using-the-detector-for-app-service"></a>Uso del rilevatore per il servizio appUsing the detector for App Service

1. Nel portale passare all'app.
2. Selezionare **Diagnostica e risolvi i problemi**.
3. Scegliere **Disponibilità e prestazioni** e selezionare App Web **inattivo.**
4. Trovare **Key Vault Application Settings Diagnostics** e fare clic su Ulteriori **informazioni**.


### <a name="using-the-detector-for-azure-functions"></a>Uso del rilevatore per Funzioni di AzureUsing the detector for Azure Functions

1. Nel portale passare all'app.
2. Passare a **Funzionalità della piattaforma.**
3. Selezionare **Diagnostica e risolvi i problemi**.
4. Scegliere **Disponibilità e prestazioni** e selezionare App per le funzioni **inattivo o la segnalazione di errori.**
5. Fare clic su **Diagnostica impostazioni applicazione Vault chiave.**
