---
title: Gestire l'insieme di credenziali chiave nello Stack di Azure tramite il portale | Documenti Microsoft
description: Informazioni su come gestire l'insieme di credenziali chiave nello Stack di Azure tramite il portale
services: azure-stack
documentationcenter: 
author: SnehaGunda
manager: byronr
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/10/2017
ms.author: sngun
ms.openlocfilehash: d263cbcc81be37eaedfdb771436fd13ef25362f8
ms.sourcegitcommit: 963e0a2171c32903617d883bb1130c7c9189d730
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/20/2017
---
# <a name="manage-key-vault-in-azure-stack-by-using-the-portal"></a>Gestire l'insieme di credenziali chiave nello Stack di Azure tramite il portale

È possibile gestire l'insieme di credenziali chiave nello Stack di Azure tramite il portale di Azure Stack. In questo articolo consente di iniziare a creare e gestire un insieme di credenziali chiave nello Stack di Azure. 

## <a name="prerequisites"></a>Prerequisiti  

È necessario sottoscrivere un'offerta che include il servizio insieme credenziali chiavi Azure.
 
## <a name="create-a-key-vault"></a>Creare un insieme di credenziali delle chiavi 

1. Accedi al [portale per gli utenti](https://portal.local.azurestack.external).  

2. Dal dashboard, selezionare **New** > **sicurezza + identità** > **insieme di credenziali chiave**.  

    ![Schermata di insieme di credenziali chiave](media/azure-stack-kv-manage-portal/image1.png)  

3. Nel **Crea insieme di credenziali chiave** riquadro, assegnare un **nome** per l'insieme di credenziali. I nomi di archivio possono contenere solo caratteri alfanumerici e caratteri speciali trattino (-). Essi non devono iniziare con un numero.  

4. Scegliere un **sottoscrizione** dall'elenco delle sottoscrizioni disponibili. Tutte le sottoscrizioni che offrono il servizio insieme di credenziali chiave vengono visualizzate nell'elenco a discesa.  

5. Selezionare un oggetto esistente **gruppo di risorse** o crearne uno nuovo.  

6. Selezionare il **tariffario**.  
    >[!NOTE]
    > Chiave di insiemi di credenziali per il supporto di Azure Stack Development Kit **Standard** solo SKU.

7. Scegliere una delle esistente **criteri di accesso** o crearne uno nuovo. Un criterio di accesso consente di concedere le autorizzazioni per un utente, applicazione o un gruppo di sicurezza eseguire operazioni con questo insieme di credenziali.  

8. Facoltativamente, scegliere un **criteri di accesso avanzato** per abilitare le funzionalità, ad esempio l'accesso alle macchine virtuali (VM) per la distribuzione, accedere a Gestione risorse per la distribuzione dei modelli e l'accesso a Azure crittografia del disco per la crittografia del volume. 
  
9.  Dopo aver configurato le impostazioni, selezionare **OK**, quindi selezionare **crea**. Verrà avviata la distribuzione della chiave dell'insieme di credenziali. 

## <a name="manage-keys-and-secrets"></a>Gestire le chiavi e segreti

Dopo aver creato un insieme di credenziali, utilizzare la procedura seguente per creare e gestire le chiavi e segreti nell'insieme di credenziali.

### <a name="create-a-key"></a>Creare una chiave

1. Accedi al [portale per gli utenti](https://portal.local.azurestack.external).  

2. Dal dashboard, selezionare **tutte le risorse**, selezionare l'insieme di credenziali chiave creato in precedenza e quindi selezionare il **chiavi** riquadro.  

3. Nel **chiavi** riquadro, selezionare **Aggiungi**. 

4. Nel **creare una chiave** riquadro, dall'elenco di **opzioni**, scegliere il metodo che si desidera utilizzare per creare una chiave. È possibile **genera** una nuova chiave, **caricare** esistente della chiave, o utilizzare **ripristinare Backup** per selezionare un backup di una chiave.  

5. Immettere un **nome** per la chiave. Il nome della chiave può contenere solo caratteri alfanumerici e il trattino (-) di un carattere speciale.  

6. Facoltativamente, configurare il **impostare la data di attivazione** e **impostare la data di scadenza** i valori della chiave.  

7. Selezionare **crea** per avviare la distribuzione.  

Dopo la chiave viene creata correttamente, è possibile selezionare in **chiavi** e consente di visualizzare o modificare le relative proprietà. Nella sezione delle proprietà contiene il **identificatore chiave**, ovvero un identificatore URI (Uniform Resource) per cui le applicazioni esterne possono accedere a questa chiave. Per limitare le operazioni su questa chiave, configurare le impostazioni in **operazioni consentite**.

![Chiave URI](media/azure-stack-kv-manage-portal/image4.png)  

### <a name="create-a-secret"></a>Creare un segreto 

1. Accedi al [portale per gli utenti](https://portal.local.azurestack.external).  
2. Dal dashboard, selezionare **tutte le risorse**, selezionare l'insieme di credenziali chiave creato in precedenza e quindi selezionare il **segreti** riquadro.  

3. In **segreti**selezionare **Aggiungi**.  

4. In **creare un segreto**, dall'elenco di **opzioni di caricamento**, scegliere un'opzione mediante il quale si desidera creare una chiave privata. È possibile creare un segreto **manualmente** se si immette un valore per la chiave privata o caricare un **certificato** dal computer locale.  

5. Immettere un **nome** per la chiave privata. Il nome del segreto può contenere solo caratteri alfanumerici e il trattino (-) di un carattere speciale.  

6. Facoltativamente, specificare il **tipo di contenuto**e configurare i valori per **impostare la data di attivazione** e **impostare la data di scadenza** per la chiave privata.  

7. Selezionare **crea** per avviare la distribuzione.  

Dopo il segreto viene creato correttamente, è possibile selezionare in **segreti** e consente di visualizzare o modificare le relative proprietà. Nella sezione delle proprietà contiene un **identificatore di segreto**, ovvero un URI per cui le applicazioni esterne possono accedere il segreto. 

![Segreto URI](media/azure-stack-kv-manage-portal/image5.png) 


## <a name="next-steps"></a>Passaggi successivi
* [Distribuire una macchina virtuale per il recupero della password archiviata nell'insieme di credenziali chiave](azure-stack-kv-deploy-vm-with-secret.md) 
* [Distribuire una macchina virtuale con certificato archiviato nell'insieme di credenziali chiave](azure-stack-kv-push-secret-into-vm.md)     


