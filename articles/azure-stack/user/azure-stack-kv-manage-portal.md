---
title: Gestire Key Vault in Azure Stack tramite il portale | Microsoft Docs
description: Informazioni su come gestire Key Vault in Azure Stack tramite il portale
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: D4300668-461F-45F6-BF3B-33B502C39D17
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/12/2018
ms.author: sethm
ms.openlocfilehash: 51c04a567ff953c4e84930e3feae448f78627683
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/12/2018
ms.locfileid: "44713936"
---
# <a name="manage-key-vault-in-azure-stack-by-using-the-portal"></a>Gestire Key Vault in Azure Stack tramite il portale

È possibile gestire Key Vault in Azure Stack tramite il portale di Azure Stack. Questo articolo consente di iniziare a creare e gestire un insieme di credenziali delle chiavi in Azure Stack.

## <a name="prerequisites"></a>Prerequisiti

È necessario sottoscrivere un'offerta che include il servizio Azure Key Vault.

## <a name="create-a-key-vault"></a>Creare un insieme di credenziali delle chiavi

1. Accedi per il [portale per gli utenti](https://portal.local.azurestack.external).

2. Dal dashboard, selezionare **+ crea una risorsa** > **sicurezza e identità** > **Key Vault**.

    ![Schermata di Key Vault](media/azure-stack-kv-manage-portal/image1.png)

3. Nel **creazione Key Vault** riquadro, assegnare un **nome** dell'insieme di credenziali. I nomi dell'insieme di credenziali possono contenere solo caratteri alfanumerici e caratteri speciali trattini (-). Essi non devono iniziare con un numero.

4. Scegliere una **sottoscrizione** dall'elenco delle sottoscrizioni disponibili. Nell'elenco a discesa vengono visualizzate tutte le sottoscrizioni che offrono il servizio Key Vault.

5. Selezionare un gruppo esistente in **Gruppo di risorse** oppure creare un nuovo gruppo.

6. Selezionare il **tariffario**.
    >[!NOTE]
    > Chiave di insiemi di credenziali di supporto di Azure Stack Development Kit **Standard** solo gli SKU.

7. Scegliere una delle esistente **criteri di accesso** o crearne uno nuovo. Un criterio di accesso consente di concedere le autorizzazioni per un utente, applicazione o un gruppo di sicurezza eseguire operazioni con questo insieme di credenziali.

8. Facoltativamente, scegliere un' **criteri di accesso avanzati** per abilitare l'accesso alle funzionalità. Ad esempio: macchine virtuali (VM) per la distribuzione, Resource Manager per la distribuzione del modello e l'accesso a crittografia dischi di Azure per la crittografia del volume.

9. Dopo aver configurato le impostazioni, selezionare **OK**, quindi selezionare **crea**. Verrà avviata la distribuzione di insieme di credenziali delle chiavi.

## <a name="manage-keys-and-secrets"></a>Gestire chiavi e segreti

Dopo aver creato un insieme di credenziali, usare la procedura seguente per creare e gestire chiavi e segreti nell'insieme di credenziali.

### <a name="create-a-key"></a>Creare una chiave

1. Accedi per il [portale per gli utenti](https://portal.local.azurestack.external).

2. Dal dashboard, selezionare **tutte le risorse**, selezionare l'insieme di credenziali delle chiavi creato in precedenza e quindi selezionare la **chiavi** riquadro.

3. Nel **tasti** riquadro, selezionare **Add**.

4. Nel **creare una chiave** riquadro, dall'elenco dei **opzioni**, scegliere il metodo che si desidera utilizzare per creare una chiave. È possibile **genera** una nuova chiave **caricare** esistente della chiave oppure usare **Restore Backup** per selezionare un backup di una chiave.

5. Immettere un **nome** relative alla chiave. Il nome della chiave può contenere solo caratteri alfanumerici e trattini (-) carattere speciale.

6. Facoltativamente, configurare il **impostare la data di attivazione** e **impostare la data di scadenza** i valori per la chiave.

7. Selezionare **Create** per avviare la distribuzione.

Dopo che la chiave viene creata correttamente, è possibile selezionarlo in **chiavi** e visualizzare o modificare le relative proprietà. La sezione properties contiene il **identificatore di chiave**, ovvero un URI Uniform Resource Identifier () usata dalle applicazioni esterne per accedere a questa chiave. Per limitare le operazioni su questa chiave, configurare le impostazioni in **operazioni consentite**.

![Chiave URI](media/azure-stack-kv-manage-portal/image4.png)

### <a name="create-a-secret"></a>Creare un segreto

1. Accedi per il [portale per gli utenti](https://portal.local.azurestack.external).
2. Dal dashboard, selezionare **tutte le risorse**, selezionare l'insieme di credenziali delle chiavi creato in precedenza e quindi selezionare la **segreti** riquadro.

3. Sotto **segreti**, selezionare **Add**.

4. Sotto **creare un segreto**, nell'elenco dei **opzioni di caricamento**, scegliere un'opzione in base al quale si desidera creare un segreto. È possibile creare una chiave privata **manualmente** se si immette un valore per la chiave privata o caricare un **certificato** dal computer locale.

5. Immettere un **nome** per la chiave privata. Il nome del segreto può contenere solo caratteri alfanumerici e trattini (-) carattere speciale.

6. Facoltativamente, specificare il **tipo di contenuto**, configurare i valori per **impostare la data di attivazione** e **impostare la data di scadenza** per la chiave privata.

7. Selezionare **Create** per avviare la distribuzione.

Dopo aver creata la chiave privata, è possibile selezionarlo in **segreti** e visualizzare o modificare le relative proprietà. Il **identificatore di segreto** è un URI che le applicazioni esterne è possono usare per accedere a questo segreto.

![Segreto URI](media/azure-stack-kv-manage-portal/image5.png)

## <a name="next-steps"></a>Passaggi successivi

* [Distribuire una macchina virtuale per il recupero della password archiviata in Key Vault](azure-stack-kv-deploy-vm-with-secret.md)
* [Distribuire una macchina virtuale con certificato archiviato in Key Vault](azure-stack-kv-push-secret-into-vm.md)
