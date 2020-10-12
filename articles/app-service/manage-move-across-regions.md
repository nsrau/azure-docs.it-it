---
title: Spostare un'app in un'altra area
description: Informazioni su come spostare le risorse del servizio app da un'area a un'altra.
ms.topic: how-to
ms.date: 02/27/2020
ms.custom: subject-moving-resources
ms.openlocfilehash: 87a2da31802a8b6858e875c23ef1dbd2d6d006bc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86524855"
---
# <a name="move-an-app-service-resource-to-another-region"></a>Spostare una risorsa del servizio app in un'altra area

Questo articolo descrive come spostare le risorse del servizio app in un'area di Azure diversa. È possibile spostare le risorse in un'altra area per diversi motivi. Ad esempio, per sfruttare i vantaggi di una nuova area di Azure, per distribuire le funzionalità o i servizi disponibili solo in aree specifiche, per soddisfare i requisiti di governance e criteri interni oppure in risposta ai requisiti della pianificazione della capacità.

Le risorse del servizio app sono specifiche dell'area e non possono essere spostate tra le aree. È necessario creare una copia delle risorse esistenti del servizio app nell'area di destinazione, quindi spostare il contenuto nella nuova app. Se l'app di origine usa un dominio personalizzato, è possibile [eseguirne la migrazione alla nuova app nell'area di destinazione](manage-custom-dns-migrate-domain.md) al termine dell'operazione.

Per semplificare la copia dell'app, è possibile [clonare una singola app del servizio app](app-service-web-app-cloning.md) in un piano di servizio app in un'altra area, ma presenta alcune [limitazioni](app-service-web-app-cloning.md#current-restrictions), in particolare in quanto non supporta le app Linux.

## <a name="prerequisites"></a>Prerequisiti

- Assicurarsi che l'app del servizio app si trovi nell'area di Azure da cui si vuole eseguire lo spostamento.
- Assicurarsi che l'area di destinazione supporti il servizio app e qualsiasi servizio correlato, di cui si desidera spostare le risorse.
<!-- - Domain bindings, certificates, and managed identities can't replicated using the **Export template** method. You must create them manually. -->

## <a name="prepare"></a>Preparazione

Identificare tutte le risorse del servizio app attualmente in uso. Ad esempio:

- App del servizio app
- [Piani del servizio app](overview-hosting-plans.md)
- [Slot di distribuzione](deploy-staging-slots.md)
- [Domini personalizzati acquistati in Azure](manage-custom-dns-buy-domain.md)
- [Certificati SSL](configure-ssl-certificate.md)
- [Integrazione della rete virtuale di Azure](web-sites-integrate-with-vnet.md)
- [Connessioni ibride](app-service-hybrid-connections.md).
- [Identità gestite](overview-managed-identity.md)
- [Impostazioni di backup](manage-backup.md)

Alcune risorse, ad esempio i certificati importati o le connessioni ibride, contengono l'integrazione con altri servizi di Azure. Per informazioni su come spostare tali risorse tra le aree, vedere la documentazione relativa ai rispettivi servizi.

## <a name="move"></a>Spostamento

1. [Creare un backup dell'app di origine](manage-backup.md).
1. [Creare un'app in un nuovo piano di servizio app nell'area di destinazione](app-service-plan-manage.md#create-an-app-service-plan).
2. [Ripristinare il backup nell'app di destinazione](web-sites-restore.md)
2. Se si usa un dominio personalizzato, [associarlo preventivamente all'app di destinazione](manage-custom-dns-migrate-domain.md#bind-the-domain-name-preemptively) con `awverify.` e [abilitare il dominio nell'app di destinazione](manage-custom-dns-migrate-domain.md#enable-the-domain-for-your-app).
3. Configurare tutto il resto nell'app di destinazione in modo che corrisponda all'app di origine e verificare la configurazione.
4. Quando si è pronti per il dominio personalizzato in modo che punti all'app di destinazione, [modificare il mapping del nome di dominio](manage-custom-dns-migrate-domain.md#remap-the-active-dns-name).

<!-- 1. Login to the [Azure portal](https://portal.azure.com) > **Resource Groups**.
2. Locate the Resource Group that contains the source App Service resources and click on it.
3. Select > **Settings** > **Export template**.
4. Choose **Deploy** in the **Export template** blade.
5. Click **TEMPLATE** > **Edit template** to open the template in the online editor.
6. Click inside the online editor and type Ctrl+F (or ⌘+F on a Mac) and type `"identity": {` to find any managed identity definition. The following is an example if you have a user-assigned managed identity.
    ```json
    "identity": {
        "type": "UserAssigned",
        "userAssignedIdentities": {
            "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/<group-name>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<identity-name>": {
                "principalId": "00000000-0000-0000-0000-000000000000",
                "clientId": "00000000-0000-0000-0000-000000000000"
            }
        }
    },
    ```
6. Click inside the online editor and type Ctrl+F (or ⌘+F on a Mac) and type `"Microsoft.Web/sites/hostNameBindings` to find all hostname bindings. The following is an example if you have a user-assigned managed identity.
    ```json
    {
        "type": "Microsoft.Web/sites/hostNameBindings",
        "apiVersion": "2018-11-01",
        "name": "[concat(parameters('sites_webapp_name'), '/', parameters('sites_webapp_name'), '.azurewebsites.net')]",
        "location": "West Europe",
        "dependsOn": [
            "[resourceId('Microsoft.Web/sites', parameters('sites_webapp_name'))]"
        ],
        "properties": {
            "siteName": "<app-name>",
            "hostNameType": "Verified"
        }
    },
    ```
6. Click inside the online editor and type Ctrl+F (or ⌘+F on a Mac) and type `"Microsoft.Web/certificates` to find all hostname bindings. The following is an example if you have a user-assigned managed identity.
    ```json
    {
        "type": "Microsoft.Web/certificates",
        "apiVersion": "2018-11-01",
        "name": "[parameters('certificates_test2_cephaslin_com_name')]",
        "location": "West Europe",
        "properties": {
            "hostNames": [
                "[parameters('certificates_test2_cephaslin_com_name')]"
            ],
            "password": "[parameters('certificates_test2_cephaslin_com_password')]"
        }
    },
    ```
7. Delete the entire JSON block. Click **Save** in the online editor.
8. Click **BASICS** > **Create new** to create a new resource group. Type the group name and click **OK**.
9. In **BASICS** > **Location**, select the region you want.   -->

## <a name="clean-up-source-resources"></a>Pulire le risorse di origine

Eliminare l'app di origine e il piano di servizio app. [Un piano di servizio app nel livello non gratuito comporta un addebito, anche se non è in esecuzione alcuna app.](app-service-plan-manage.md#delete-an-app-service-plan)

## <a name="next-steps"></a>Passaggi successivi

[Clonazione di app del servizio app di Azure con PowerShell](app-service-web-app-cloning.md)