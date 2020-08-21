---
title: Abilitare le identità gestite nelle macchine virtuali del Lab in Azure DevTest Labs
description: Questo articolo illustra come un proprietario del Lab può abilitare le identità gestite assegnate dall'utente nelle macchine virtuali del Lab.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: b4bf2900acebaeecd5cbc4cb65635aee6de87dda
ms.sourcegitcommit: 6fc156ceedd0fbbb2eec1e9f5e3c6d0915f65b8e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/21/2020
ms.locfileid: "88717634"
---
# <a name="enable-user-assigned-managed-identities-on-lab-virtual-machines-in-azure-devtest-labs"></a>Abilitare le identità gestite assegnate dall'utente nelle macchine virtuali del Lab in Azure DevTest Labs
In qualità di proprietario del Lab, è possibile abilitare le identità gestite assegnate dall'utente nelle macchine virtuali (VM) Lab in Azure DevTest Labs.

Un'identità gestita può essere usata per eseguire l'autenticazione a qualsiasi servizio che supporti l'autenticazione Azure Active Directory (AD), incluso Key Vault, senza passare alcuna credenziale nel codice. Per altre informazioni sulle identità gestite, vedere informazioni sulle [identità gestite per le risorse di Azure](../active-directory/managed-identities-azure-resources/overview.md).

Con questa funzionalità, gli utenti del Lab possono condividere risorse di Azure come il database SQL di Azure nel contesto del Lab. L'autenticazione alla risorsa viene curata dall'identità stessa. Una volta configurata, ogni macchina virtuale lab esistente/appena creata verrà abilitata con questa identità. Gli utenti del Lab possono accedere alle risorse dopo aver eseguito l'accesso ai propri computer.

> [!NOTE]
> È possibile aggiungere più identità gestite assegnate dall'utente da abilitare nelle macchine virtuali del Lab.

## <a name="use-azure-portal"></a>Usare il portale di Azure
Per aggiungere un'identità gestita assegnata dall'utente per le macchine virtuali del Lab, attenersi alla procedura seguente:

1. [Creare un'identità gestita assegnata dall'utente nella sottoscrizione](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity)
1. Passare alla pagina **configurazione e criteri** per il Lab.
1. Selezionare **Identity (anteprima)** nel menu a sinistra.
1. Selezionare la scheda **macchina virtuale** .
1. Selezionare **Aggiungi** per selezionare un'identità esistente da un elenco a discesa pre-popolato. 

    > [!div class="mx-imgBorder"]
    > ![Pulsante Aggiungi identità](./media/enable-managed-identities-lab-vms/add-identity-button.png)
1. Selezionare un' **identità gestita dall'utente** esistente dall'elenco a discesa e fare clic su **OK**. 

    > [!div class="mx-imgBorder"]
    > ![Aggiungi identità](./media/enable-managed-identities-lab-vms/add-identity.png)

## <a name="use-api"></a>USA API

1.  Dopo aver creato un'identità, prendere nota dell'ID risorsa dell'identità. Dovrebbe essere simile all'esempio seguente: 

    `/subscriptions/0000000000-0000-0000-0000-00000000000000/resourceGroups/<RESOURCE GROUP NAME> /providers/Microsoft.ManagedIdentity/userAssignedIdentities/<NAME of USER IDENTITY>`.
2. Eseguire un metodo PUT HTTPS per aggiungere una nuova risorsa **ServiceRunner** al Lab, come illustrato nell'esempio seguente. 

    La risorsa di Service Runner è una risorsa proxy per gestire e controllare le identità gestite in DevTest Labs. Il nome del Runner del servizio può essere qualsiasi nome valido, ma è consigliabile usare il nome della risorsa di identità gestita.

    ```json
    {
        "identity": {
            "type": "userAssigned",
            "userAssignedIdentities": { 
                "[userAssignedIdentityResourceId]": {}
            }
            },
        "location": "southeastasia",
        "properties": {
            "identityUsageType": "VirtualMachine"
        }
    }
    ```

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sulle identità gestite, vedere informazioni sulle [identità gestite per le risorse di Azure](../active-directory/managed-identities-azure-resources/overview.md).







