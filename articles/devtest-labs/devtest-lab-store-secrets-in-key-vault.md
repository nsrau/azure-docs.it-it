---
title: Archiviare segreti in un insieme di credenziali delle chiavi in Azure DevTest Labs | Microsoft Docs
description: Informazioni su come archiviare i segreti in Azure Key Vault e usarle durante la creazione di una macchina virtuale, di una formula o di un ambiente.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 5714279ef183cb930d643575466dae3d6cb69bba
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85481647"
---
# <a name="store-secrets-in-a-key-vault-in-azure-devtest-labs"></a>Archiviare segreti in un insieme di credenziali delle chiavi in Azure DevTest Labs
Potrebbe essere necessario inserire un segreto complesso quando si usa Azure DevTest Labs: password per la macchina virtuale Windows, la chiave pubblica SSH per la macchina virtuale Linux o il token di accesso personale per clonare il repository Git tramite un artefatto. I segreti sono in genere lunghi e contengono caratteri casuali. Pertanto, l'immissione può essere difficile e poco pratica specialmente se si usa lo stesso segreto più volte.

Per risolvere questo problema e mantenere i segreti in un luogo sicuro, DevTest Labs supporta l'archiviazione dei segreti in un [insieme di credenziali delle chiavi di Azure](../key-vault/general/overview.md). Quando un utente salva un segreto per la prima volta, il servizio DevTest Labs crea automaticamente un insieme di credenziali delle chiavi nello stesso gruppo di risorse che contiene il lab e archivia il segreto nell'insieme di credenziali delle chiavi. DevTest Labs crea un insieme di credenziali delle chiavi separato per ogni utente. 

Si noti che l'utente Lab dovrà prima creare una macchina virtuale Lab prima di poter creare un segreto nell'insieme di credenziali delle chiavi. Questo perché il servizio DevTest lab deve associare l'utente del Lab a un documento utente valido prima di poter creare e archiviare i segreti nell'insieme di credenziali delle chiavi. 


## <a name="save-a-secret-in-azure-key-vault"></a>Salvare un segreto in Azure Key Vault
Per salvare il segreto in Azure Key Vault procedere come segue:

1. Selezionare **Segreti personali** nel menu a sinistra.
2. Immettere un **nome** per il segreto. Il nome verrà visualizzato nell'elenco a discesa durante la creazione di una macchina virtuale, una formula o un ambiente. 
3. Immettere il segreto come **valore**.

    ![Archiviare un segreto](media/devtest-lab-store-secrets-in-key-vault/store-secret.png)

## <a name="use-a-secret-from-azure-key-vault"></a>Usare un segreto da Azure Key Vault
Quando è necessario inserire un segreto per creare una macchina virtuale, una formula o un ambiente, è possibile immettere manualmente il segreto o selezionarne uno salvato dall'insieme di credenziali delle chiavi. Per usare un segreto archiviato nell'insieme di credenziali delle chiavi, eseguire le operazioni seguenti:

1. Selezionare **Usa un segreto salvato**. 
2. Selezionare il segreto dall'elenco a discesa **Seleziona un segreto**. 

    ![Usare un segreto nella macchina virtuale](media/devtest-lab-store-secrets-in-key-vault/secret-store-pick-a-secret.png)

## <a name="use-a-secret-in-an-azure-resource-manager-template"></a>Usare un segreto nel modello di Azure Resource Manager
È possibile specificare il nome del segreto in un modello di Azure Resource Manager che viene usato per creare una macchina virtuale, come illustrato nell'esempio seguente:

![Usare il segreto nella formula o nell'ambiente](media/devtest-lab-store-secrets-in-key-vault/secret-store-arm-template.png)

## <a name="next-steps"></a>Passaggi successivi

- [Creare una macchina virtuale usando il segreto](devtest-lab-add-vm.md) 
- [Creare una formula usando il segreto](devtest-lab-manage-formulas.md)
- [Creare un ambiente usando il segreto](devtest-lab-create-environment-from-arm.md)
