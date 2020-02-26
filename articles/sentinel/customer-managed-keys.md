---
title: Configurare chiavi gestite dal cliente in Sentinel di Azure | Microsoft Docs
description: Informazioni su come configurare le chiavi gestite dal cliente (CMK) in Sentinel di Azure.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/30/2019
ms.author: yelevin
ms.openlocfilehash: bc2fa02925e7f5c671085eb87ca0431d3fca7691
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/25/2020
ms.locfileid: "77587958"
---
# <a name="set-up-azure-sentinel-customer-managed-key"></a>Configurare la chiave gestita dal cliente di Azure Sentinel


Questo articolo fornisce informazioni generali e procedure per configurare una chiave gestita dal cliente (CMK) per Sentinel di Azure. CMK consente la crittografia di tutti i dati salvati o inviati a Sentinel di Azure in tutte le risorse di archiviazione rilevanti con una chiave di Azure Key Vault creata o di proprietà dell'utente.

> [!NOTE]
> -   La funzionalità CMK di Azure Sentinel è disponibile solo per i clienti che **non hanno familiarità con l'** accesso a questa funzionalità è controllata dalla registrazione delle funzionalità di Azure. È possibile richiedere l'accesso contattando azuresentinelCMK@microsoft.come, quando la capacità è disponibile, le richieste in sospeso verranno approvate.
> -   La funzionalità CMK di Azure Sentinel è disponibile solo nelle aree Stati Uniti orientali, Stati Uniti occidentali 2 e Stati Uniti centro-meridionali.
> -   La funzionalità CMK è disponibile solo per i clienti che inviano 1 TB al giorno. Si riceveranno informazioni sui prezzi aggiuntivi quando si applica a Microsoft per eseguire il provisioning di CMK nella sottoscrizione di Azure. Altre informazioni su [log Analytics](../azure-monitor/platform/customer-managed-keys.md#disclaimers) addebito.

## <a name="how-cmk-works"></a>Funzionamento di CMK 

La soluzione sentinella di Azure usa alcune risorse di archiviazione per la raccolta e le funzionalità dei log, tra cui Log Analytics e altre risorse di archiviazione. Come parte della configurazione CMK di Sentinel di Azure, sarà necessario configurare anche le impostazioni di CMK nelle risorse di archiviazione correlate. Verranno crittografati anche i dati salvati nelle risorse di archiviazione diverse da Log Analytics.

> [!NOTE]
> Se si Abilita CMK in Azure Sentinel, qualsiasi funzionalità di anteprima pubblica che non supporta CMK non verrà abilitata.

## <a name="enable-cmk"></a>Abilita CMK 

Per eseguire il provisioning di CMK, seguire questa procedura: 

1.  Creare una Azure Key Vault e la chiave di archiviazione.

2.  Abilitare CMK nell'area di lavoro Log Analytics.

3.  Registrarsi per Cosmos DB.

4.  Aggiungere un criterio di accesso all'istanza di Azure Key Vault.

5.  Abilitare CMK in Sentinel di Azure.

6.  Abilitare Sentinel di Azure.

### <a name="step-1-create-an-azure-key-vault-and-storing-key"></a>PASSAGGIO 1: creare un Azure Key Vault e archiviare la chiave

1.  [Creare Azure Key Vault risorsa](https://docs.microsoft.com/azure-stack/user/azure-stack-key-vault-manage-portal?view=azs-1910), quindi generare o importare una chiave da usare per la crittografia dei dati.
    > [!NOTE]
    >  Azure Key Vault deve essere configurato come reversibile per proteggere la chiave e l'accesso.

1.  [Attiva opzioni di ripristino:](../key-vault/key-vault-best-practices.md#turn-on-recovery-options)

    -   Assicurarsi che l' [eliminazione](../key-vault/key-vault-ovw-soft-delete.md) temporanea sia attivata.

    -   Attivare la [protezione di ripulitura](../key-vault/key-vault-ovw-soft-delete.md#purge-protection) per evitare l'eliminazione forzata del segreto/insieme di credenziali anche dopo l'eliminazione temporanea.

### <a name="step-2-enable-cmk-on-your-log-analytics-workspace"></a>PASSAGGIO 2: abilitare CMK nell'area di lavoro Log Analytics

Seguire le istruzioni in [monitoraggio di Azure configurazione della chiave gestita dal cliente](../azure-monitor/platform/customer-managed-keys.md) per creare un'area di lavoro CMK che verrà usata come area di lavoro di Azure Sentinel nei passaggi seguenti.

### <a name="step-3-register-for-cosmos-db"></a>PASSAGGIO 3: registrare Cosmos DB

Azure Sentinel funziona con Cosmos DB come risorsa di archiviazione aggiuntiva. Assicurarsi di registrarsi per Cosmos DB.

Seguire le istruzioni Cosmos DB per [registrare il](../cosmos-db/how-to-setup-cmk.md#register-resource-provider) provider di risorse Azure Cosmos DB per la sottoscrizione di Azure.

### <a name="step-4-add-an-access-policy-to-your-azure-key-vault-instance"></a>PASSAGGIO 4: aggiungere un criterio di accesso all'istanza di Azure Key Vault

Assicurarsi di aggiungere l'accesso da Cosmos DB all'istanza di Azure Key Vault. Seguire le istruzioni Cosmos DB per [aggiungere un criterio di accesso all'istanza di Azure Key Vault](../cosmos-db/how-to-setup-cmk.md#add-an-access-policy-to-your-azure-key-vault-instance) con Azure Cosmos DB entità.

### <a name="step-5-enable-cmk-in-azure-sentinel"></a>PASSAGGIO 5: abilitare CMK in Sentinel di Azure

La funzionalità CMK di Sentinel di Azure viene fornita ai nuovi clienti solo dopo aver ricevuto l'accesso direttamente dal gruppo di prodotti Azure. Usare i contatti Microsoft per ricevere l'approvazione dal team di Sentinel di Azure per abilitare CMK nella soluzione.

Dopo aver ottenuto l'approvazione, verrà richiesto di fornire le informazioni seguenti per abilitare la funzionalità CMK.

-  ID area di lavoro in cui si vuole abilitare CMK

-  Key Vault URL: copiare la chiave "identificatore chiave" fino all'ultima barra:  
    

    ![identificatore di chiave](./media/customer-managed-keys/key-identifier.png)

    Il team di Sentinel di Azure Abilita la funzionalità CMK di Azure Sentinel per l'area di lavoro specificata.

-  Verifica del team del prodotto Sentinel di Azure approvato per l'uso di questa funzionalità. Questa operazione deve essere eseguita prima di procedere.

### <a name="step-6-enable-azure-sentinel"></a>PASSAGGIO 6: abilitare la sentinella di Azure


Passare alla portale di Azure e abilitare la sentinella di Azure nell'area di lavoro in cui si configura CMK. Per altre informazioni, vedere [onboarding di Sentinel di Azure](quickstart-onboard.md).

## <a name="key-encryption-key-revocation-or-deletion"></a>Revoca o eliminazione della chiave di crittografia della chiave


Nel caso in cui un utente revoca la chiave di crittografia della chiave, eliminando o rimuovendo l'accesso per Sentinel di Azure, entro un'ora, Azure Sentinel rispetterà la modifica e si comporterà come se i dati non fossero più disponibili. A questo punto, verranno impedite tutte le operazioni eseguite che usano risorse di archiviazione persistenti, ad esempio l'inserimento di dati, modifiche della configurazione persistenti e la creazione di eventi imprevisti. I dati archiviati in precedenza non verranno eliminati, ma rimarranno inaccessibili. I dati inaccessibili sono regolati dai criteri di conservazione dei dati e verranno eliminati in base a tali criteri.

L'unica operazione possibile dopo la revoca o l'eliminazione della chiave di crittografia è l'eliminazione dell'account.

Se l'accesso viene ripristinato dopo la revoca, Azure Sentinel ripristinerà l'accesso ai dati entro un'ora.

Per altre informazioni sul funzionamento di questo in monitoraggio di Azure, vedere la pagina relativa alla [revoca CMK di monitoraggio di Azure](../azure-monitor/platform/customer-managed-keys.md#cmk-kek-revocation).

## <a name="key-encryption-key-rotation"></a>Rotazione della chiave di crittografia della chiave


Sentinel di Azure e Log Analytics supportano la rotazione delle chiavi. Quando un utente esegue la rotazione delle chiavi in Key Vault, Azure Sentinel supporta la nuova chiave entro un'ora.

In Key Vault, è possibile eseguire la rotazione della chiave creando una nuova versione della chiave:

![rotazione delle chiavi](./media/customer-managed-keys/key-rotation.png)

È possibile disabilitare la versione precedente della chiave dopo 24 ore oppure, dopo che i log di controllo Azure Key Vault non visualizzano più alcuna attività che utilizza la versione precedente.

Se si usa la stessa chiave in Sentinel di Azure e in Log Analytics, è necessario eseguire la rotazione delle chiavi è necessario aggiornare in modo esplicito la risorsa cluster in Log Analytics con la nuova versione della chiave di Azure Key Vault. Per altre informazioni, vedere la pagina relativa alla [rotazione CMK di monitoraggio di Azure](../azure-monitor/platform/customer-managed-keys.md#cmk-kek-rotation).

## <a name="next-steps"></a>Passaggi successivi
In questo documento si è appreso come configurare una chiave gestita dal cliente in Sentinel di Azure. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:
- Informazioni su come [ottenere visibilità sui dati e sulle potenziali minacce](quickstart-get-visibility.md).
- Iniziare a [rilevare minacce con Azure Sentinel](tutorial-detect-threats.md).
- [Utilizzare le cartelle di lavoro](tutorial-monitor-your-data.md) di per monitorare i dati.

