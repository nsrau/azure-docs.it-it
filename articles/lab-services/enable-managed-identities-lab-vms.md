---
title: Abilitare le identità gestite nelle macchine virtuali lab nei laboratori di Azure DevTestEnable managed identities on your lab VMs in Azure DevTest Labs
description: Questo articolo illustra come il proprietario di un lab può abilitare le identità gestite assegnate dall'utente nelle macchine virtuali del lab.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75692667"
---
# <a name="enable-user-assigned-managed-identities-on-lab-virtual-machines-in-azure-devtest-labs"></a>Abilitare le identità gestite assegnate dall'utente nelle macchine virtuali lab in Azure DevTest LabsEnable user-assigned managed identities on lab virtual machines in Azure DevTest Labs
Come proprietario di un lab, è possibile abilitare le identità gestite assegnate dall'utente nelle macchine virtuali del lab (VM) nei laboratori di Azure DevTest.As a lab owner, you can enable user-assigned managed identities on your lab virtual machines (VMs) in Azure DevTest Labs.

Un'identità gestita può essere usata per l'autenticazione a qualsiasi servizio che supporta l'autenticazione di Azure Active Directory (AD), incluso l'insieme di credenziali delle chiavi, senza passare le credenziali nel codice. Per altre informazioni sulle identità gestite, vedere [Che cos'è l'identità gestita per le risorse](../active-directory/managed-identities-azure-resources/overview.md)di Azure? .

Con questa funzionalità, gli utenti del lab possono condividere le risorse di Azure, ad esempio Il database SQL di Azure, nel contesto del lab. L'autenticazione alla risorsa viene eseguita in base all'identità stessa. Una volta configurata, ogni macchina virtuale lab esistente o appena creata verrà abilitata con questa identità. Gli utenti del lab possono accedere alle risorse una volta connessi ai propri computer.

> [!NOTE]
> È possibile aggiungere più identità gestite assegnate dall'utente da abilitare nelle macchine virtuali del lab.

## <a name="use-azure-portal"></a>Usare il portale di Azure
Per aggiungere un'identità gestita assegnata a un utente per le macchine virtuali lab, eseguire la procedura seguente:To add a user assigned managed identity for lab VMs, follow these steps:

1. [Creare un'identità gestita assegnata dall'utente nella sottoscrizioneCreate a user-assigned managed identity in your subscription](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity)
1. Passare alla pagina **Configurazione e criteri** per il lab.
2. Selezionare **Identità (anteprima)** nel menu a sinistra.
3. Selezionare la scheda **Macchina virtuale.**
4. Selezionare **Aggiungi** per selezionare un'identità esistente da un elenco a discesa precompilato. 

    ![Pulsante Aggiungi identità](./media/enable-managed-identities-lab-vms/add-identity-button.png)
5. Selezionare **un'identità gestita dall'utente** esistente dall'elenco a discesa e scegliere **OK**. 

    ![Aggiungi identità](./media/enable-managed-identities-lab-vms/add-identity.png)

## <a name="use-api"></a>Utilizzare l'API

1.  Dopo aver creato un'identità, prendere nota dell'ID risorsa dell'identità. Dovrebbe essere simile all'esempio seguente:It should look like the following sample: 

    `/subscriptions/0000000000-0000-0000-0000-00000000000000/resourceGroups/<RESOURCE GROUP NAME> /providers/Microsoft.ManagedIdentity/userAssignedIdentities/<NAME of USER IDENTITY>`.
2. Eseguire un metodo PUT HTTPS per aggiungere una nuova risorsa **ServiceRunner** al lab, come illustrato nell'esempio seguente. 

    La risorsa service runner è una risorsa proxy per gestire e controllare le identità gestite in DevTest Labs. Il nome del corridore del servizio può essere qualsiasi nome valido, ma è consigliabile usare il nome della risorsa di identità gestita.

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
Per altre informazioni sulle identità gestite, vedere [Che cos'è le identità gestite per le risorse](../active-directory/managed-identities-azure-resources/overview.md)di Azure? .







