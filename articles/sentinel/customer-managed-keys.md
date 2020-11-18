---
title: Configurare chiavi gestite dal cliente in Azure Sentinel | Microsoft Docs
description: Informazioni su come configurare le chiavi gestite dal cliente (CMK, Customer-Managed Key) in Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/12/2020
ms.author: yelevin
ms.openlocfilehash: bd85936c86656a8ae59a04ccdf53a62bd852368f
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94655273"
---
# <a name="set-up-azure-sentinel-customer-managed-key"></a>Configurare una chiave gestita dal cliente in Azure Sentinel

Questo articolo fornisce informazioni generali e procedure per la configurazione di una chiave gestita dal cliente per Azure Sentinel. Una chiave gestita dal cliente consente la crittografia di tutti i dati salvati o inviati ad Azure Sentinel in tutte le risorse di archiviazione pertinenti con una chiave di Azure Key Vault creata o di proprietà dell'utente.

> [!NOTE]
> - La funzionalità CMK di Azure Sentinel è disponibile solo per **i nuovi clienti**.
>
> - L'accesso a questa funzionalità è controllato dalla registrazione delle funzionalità di Azure. È possibile richiedere l'accesso contattando azuresentinelCMK@microsoft.com . Le richieste in sospeso verranno approvate in base alla capacità disponibile.
>
> - La funzionalità CMK è disponibile solo per i clienti che inviano almeno 1 TB al giorno. Vengono fornite informazioni aggiuntive sui prezzi nel momento in cui si richiede a Microsoft il provisioning di CMK nella sottoscrizione di Azure. Sono disponibili altre informazioni sui [prezzi di Log Analytics](../azure-monitor/platform/manage-cost-storage.md#log-analytics-dedicated-clusters).

## <a name="how-cmk-works"></a>Funzionamento di CMK 

La soluzione di Azure Sentinel usa alcune risorse di archiviazione per la raccolta e le funzionalità dei log, tra cui Log Analytics. Nell'ambito della configurazione della soluzione CMK di Azure Sentinel è necessario configurare anche le impostazioni di CMK nelle risorse di archiviazione correlate. Vengono crittografati anche i dati salvati nelle risorse di archiviazione diverse da Log Analytics.

Sono disponibili altre informazioni su [CMK](../azure-monitor/platform/customer-managed-keys.md#customer-managed-key-overview).

> [!NOTE]
> Se si abilita CMK in Azure Sentinel, le funzionalità in anteprima pubblica che non supportano CMK non vengono abilitate.

## <a name="enable-cmk"></a>Abilitare CMK 

Per effettuare il provisioning di CMK, seguire questa procedura: 

1.  Creare un'istanza di Azure Key Vault e una chiave di archiviazione.

2.  Abilitare CMK nell'area di lavoro Log Analytics.

3.  Registrarsi a Cosmos DB.

4.  Aggiungere un criterio di accesso all'istanza di Azure Key Vault.

5.  Abilitare CMK in Azure Sentinel.

6.  Abilitare Azure Sentinel.

### <a name="step-1-create-an-azure-key-vault-and-storing-key"></a>PASSAGGIO 1: creare un'istanza di Azure Key Vault e una chiave di archiviazione

1.  [Creare una risorsa Azure Key Vault](/azure-stack/user/azure-stack-key-vault-manage-portal) e generare o importare una chiave da usare per la crittografia dei dati.
    > [!NOTE]
    >  L'istanza di Azure Key Vault deve essere configurata come recuperabile per proteggere la chiave e l'accesso.

1.  [Attivare le opzioni di ripristino:](../key-vault/general/best-practices.md#turn-on-recovery-options)

    -   Accertarsi che l'opzione [Eliminazione temporanea](../key-vault/general/soft-delete-overview.md) sia attivata.

    -   Attivare [Protezione dall'eliminazione](../key-vault/general/soft-delete-overview.md#purge-protection) per protegge dall'eliminazione forzata del segreto/insieme di credenziali anche dopo l'eliminazione temporanea.

### <a name="step-2-enable-cmk-on-your-log-analytics-workspace"></a>PASSAGGIO 2: abilitare CMK nell'area di lavoro Log Analytics

Per creare un'area di lavoro CMK da usare come area di lavoro di Azure Sentinel nei passaggi seguenti, seguire le istruzioni riportate nell'articolo relativo alla [configurazione di una chiave gestita dal cliente in Monitoraggio di Azure](../azure-monitor/platform/customer-managed-keys.md).

### <a name="step-3-register-for-cosmos-db"></a>PASSAGGIO 3: registrarsi a Cosmos DB

Azure Sentinel è compatibile con Cosmos DB come risorsa di archiviazione aggiuntiva. Accertarsi di effettuare la registrazione a Cosmos DB.

Seguire le istruzioni di Cosmos DB per [registrare il provider di risorse Azure Cosmos DB](../cosmos-db/how-to-setup-cmk.md#register-resource-provider) per la sottoscrizione di Azure.

### <a name="step-4-add-an-access-policy-to-your-azure-key-vault-instance"></a>PASSAGGIO 4: Aggiungere un criterio di accesso all'istanza di Azure Key Vault

Assicurarsi di aggiungere l'accesso da Cosmos DB all'istanza di Azure Key Vault. Seguire le istruzioni di Cosmos DB per [aggiungere un criterio di accesso all'istanza di Azure Key Vault](../cosmos-db/how-to-setup-cmk.md#add-an-access-policy-to-your-azure-key-vault-instance) con l'entità di sicurezza Azure Cosmos DB.

### <a name="step-5-enable-cmk-in-azure-sentinel"></a>PASSAGGIO 5: abilitare CMK in Azure Sentinel

La funzionalità CMK di Azure Sentinel viene fornita ai nuovi clienti solo dopo aver ricevuto l'accesso direttamente dal gruppo di prodotti Azure. Usare i contatti Microsoft per ricevere l'approvazione dal team di Azure Sentinel per abilitare CMK nella soluzione.

Dopo aver ottenuto l'approvazione, viene richiesto di fornire le informazioni seguenti per abilitare la funzionalità CMK.

-  ID area di lavoro in cui abilitare CMK

-  Key Vault URL: copiare la chiave "identificatore chiave" fino all'ultima barra:  
    

    ![Identificatore chiave](./media/customer-managed-keys/key-identifier.png)

    Il team di Azure Sentinel abilita la funzionalità CMK di Azure Sentinel per l'area di lavoro specificata.

-  Approvazione da parte del team del prodotto Azure Sentinel all'uso di questa funzionalità. Questa operazione è necessaria per poter procedere.

### <a name="step-6-enable-azure-sentinel"></a>PASSAGGIO 6: abilitare Azure Sentinel


Passare alla portale di Azure e abilitare Azure Sentinel nell'area di lavoro in cui si configura CMK. Per altre informazioni, vedere l'articolo relativo all'[onboarding di Azure Sentinel](quickstart-onboard.md).

## <a name="key-encryption-key-revocation-or-deletion"></a>Revoca o eliminazione delle chiavi di crittografia della chiave


Nel caso in cui un utente revochi la chiave di crittografia della chiave, eliminando o rimuovendo l'accesso per Azure Sentinel, entro un'ora, Azure Sentinel rispetta la modifica e si comporta come se i dati non fossero più disponibili. A questo punto, verranno impedite eventuali operazioni eseguite che usano risorse di archiviazione persistenti, ad esempio l'inserimento dati, le modifiche persistenti alla configurazione e la creazione di eventi imprevisti. I dati precedentemente archiviati non vengono eliminati, ma rimangono inaccessibili. I dati inaccessibili sono regolati dai criteri di conservazione dei dati e verranno eliminati in base a tali criteri.

L'unica operazione possibile quando la chiave di crittografia è stata revocata o eliminata è l'eliminazione dell'account.

Se l'accesso viene ripristinato dopo la revoca, Azure Sentinel ripristina l'accesso ai dati entro un'ora.

Per altre informazioni sul funzionamento di questo metodo in Monitoraggio di Azure, vedere [Revoca della chiave gestita dal cliente](../azure-monitor/platform/customer-managed-keys.md#key-revocation) in Monitoraggio di Azure.

## <a name="key-encryption-key-rotation"></a>Rotazione delle chiavi di crittografia della chiave


Azure Sentinel e Log Analytics supportano la rotazione delle chiavi. Quando un utente esegue la rotazione delle chiavi in Key Vault, Azure Sentinel supporta la nuova chiave entro un'ora.

In Key Vault è possibile eseguire la rotazione delle chiavi creando una nuova versione della chiave:

![Rotazione delle chiavi](./media/customer-managed-keys/key-rotation.png)

È possibile disabilitare la versione precedente della chiave dopo 24 ore oppure dopo che i log di controllo di Azure Key Vault smettono di visualizzare le attività che usano la versione precedente.

Se si usa la stessa chiave in Azure Sentinel e in Log Analytics, è necessario eseguire la rotazione delle chiavi e aggiornare in modo esplicito la risorsa cluster in Log Analytics con la nuova versione della chiave di Azure Key Vault. Per altre informazioni, vedere [Rotazione della chiave gestita dal cliente](../azure-monitor/platform/customer-managed-keys.md#key-rotation) in Monitoraggio di Azure.

## <a name="next-steps"></a>Passaggi successivi
In questo documento si è appreso come configurare una chiave gestita dal cliente in Azure Sentinel. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:
- Informazioni su come [ottenere visibilità sui dati e sulle potenziali minacce](quickstart-get-visibility.md).
- Iniziare a [rilevare minacce con Azure Sentinel](./tutorial-detect-threats-built-in.md).
- [Usare le cartelle di lavoro](tutorial-monitor-your-data.md) per monitorare i dati.