---
title: Abilitare le identità gestite nelle macchine virtuali del Lab in Azure DevTest Labs
description: Questo articolo illustra come un proprietario del Lab può abilitare le identità gestite assegnate dall'utente nelle macchine virtuali del Lab.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/03/2020
ms.author: spelluru
ms.openlocfilehash: 5d70f83babcf53249f581230e72326d99a0533d6
ms.sourcegitcommit: 2f8ff235b1456ccfd527e07d55149e0c0f0647cc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/07/2020
ms.locfileid: "75692667"
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
2. Selezionare **Identity (anteprima)** nel menu a sinistra.
3. Selezionare la scheda **macchina virtuale** .
4. Selezionare **Aggiungi** per selezionare un'identità esistente da un elenco a discesa pre-popolato. 

    ![Pulsante Aggiungi identità](./media/enable-managed-identities-lab-vms/add-identity-button.png)
5. Selezionare un' **identità gestita dall'utente** esistente dall'elenco a discesa e fare clic su **OK**. 

    ![Aggiungi identità](./media/enable-managed-identities-lab-vms/add-identity.png)

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







