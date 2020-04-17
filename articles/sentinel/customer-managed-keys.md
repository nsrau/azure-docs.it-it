---
title: Configurare le chiavi gestite dal cliente in Azure Sentinel Documenti Microsoft
description: Informazioni su come configurare le chiavi gestite dal cliente (CMK) in Azure Sentinel.Learn how to set up customer-managed keys (CMK) in Azure Sentinel.
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
ms.openlocfilehash: 5eed208ed79aeab4e46ed90dd4d340a8b445be96
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81461634"
---
# <a name="set-up-azure-sentinel-customer-managed-key"></a>Configurare la chiave gestita dal cliente di Azure SentinelSet up Azure Sentinel customer-managed key


Questo articolo fornisce informazioni di base e la procedura per configurare una chiave gestita dal cliente (CMK) per Azure Sentinel.This article provides background information and steps to configure a customer-managed key (CMK) for Azure Sentinel. CMK consente la crittografia di tutti i dati salvati o inviati ad Azure Sentinel in tutte le risorse di archiviazione pertinenti con una chiave dell'insieme di credenziali delle chiavi di Azure creata o di proprietà dell'utente.

> [!NOTE]
> -   La funzionalità CMK di Azure Sentinel viene fornita solo ai clienti nuovi e l'accesso a questa funzionalità è controllato dalla registrazione delle funzionalità di Azure.The Azure Sentinel CMK capability is provided only to customers who are **new** and access to this capability is controlled by Azure feature registration.È possibile richiedere l'accesso contattando azuresentinelCMK@microsoft.come, man mano che la capacità è disponibile, le richieste in sospeso verranno approvate.
> -   La funzionalità CMK di Azure Sentinel è disponibile solo nelle aree Stati Uniti orientali, Stati Uniti occidentali 2 e Stati Uniti centro-meridionali.
> -   La funzionalità CMK è disponibile solo per i clienti che inviano 1 TB al giorno o più. Si riceveranno informazioni sui prezzi aggiuntivi quando si applica a Microsoft per eseguire il provisioning di CMK nella sottoscrizione di Azure.You will receive information about additional pricing when you apply to Microsoft to provision CMK on your Azure subscription. Ulteriori informazioni sul caricamento di [Log Analytics.](../azure-monitor/platform/customer-managed-keys.md#disclaimers)

## <a name="how-cmk-works"></a>Come funziona CMK 

La soluzione Azure Sentinel usa diverse risorse di archiviazione per la raccolta e le funzionalità dei log, tra cui Log Analytics e altre risorse di archiviazione. Come parte della configurazione CMK di Azure Sentinel, è necessario configurare le impostazioni CMK anche nelle risorse di archiviazione correlate. Verranno crittografati anche i dati salvati in risorse di archiviazione diverse da Log Analytics.Data saved in storage resources other than Log Analytics will be encrypted.

> [!NOTE]
> Se si abilita CMK in Azure Sentinel, qualsiasi funzionalità di anteprima pubblica che non supporta CMK non verrà abilitata.

## <a name="enable-cmk"></a>Abilita CMK 

Per eseguire il provisioning di CMK, attenersi alla seguente procedura: 

1.  Creare un insieme di credenziali delle chiavi di Azure e archiviare la chiave.

2.  Abilitare CMK nell'area di lavoro di Log Analytics.

3.  Registrati per Cosmos DB.

4.  Aggiungere criteri di accesso all'istanza dell'insieme di credenziali delle chiavi di Azure.Add an access policy to your Azure Key Vault instance.

5.  Abilitare CMK in Azure Sentinel.Enable CMK in Azure Sentinel.

6.  Abilitare Azure Sentinel.Enable Azure Sentinel.

### <a name="step-1-create-an-azure-key-vault-and-storing-key"></a>Passaggio 1: Creare un insieme di credenziali delle chiavi di Azure e archiviare la chiaveSTEP 1: Create an Azure Key Vault and storing key

1.  [Creare la risorsa Dell'insieme](https://docs.microsoft.com/azure-stack/user/azure-stack-key-vault-manage-portal?view=azs-1910)di credenziali delle chiavi di Azure, quindi generare o importare una chiave da usare per la crittografia dei dati.
    > [!NOTE]
    >  L'insieme di credenziali delle chiavi di Azure deve essere configurato come recuperabile per proteggere la chiave e l'accesso.

1.  [Attivare le opzioni di ripristino:](../key-vault/general/best-practices.md#turn-on-recovery-options)

    -   Assicurarsi che [l'opzione Eliminazione temporanea](../key-vault/general/overview-soft-delete.md) sia attivata.

    -   Attivare [la protezione Purge](../key-vault/general/overview-soft-delete.md#purge-protection) per evitare l'eliminazione forzata del segreto/vault anche dopo l'eliminazione temporanea.

### <a name="step-2-enable-cmk-on-your-log-analytics-workspace"></a>PASSAGGIo 2: Abilitare CMK nell'area di lavoro di Log Analytics

Seguire le istruzioni in [Configurazione chiave gestita dal cliente di Azure Monitor](../azure-monitor/platform/customer-managed-keys.md) per creare un'area di lavoro CMK che verrà usata come area di lavoro di Azure Sentinel nei passaggi seguenti.

### <a name="step-3-register-for-cosmos-db"></a>Fase 3: Registrazione per Cosmos DB

Azure Sentinel funziona con Cosmos DB come risorsa di archiviazione aggiuntiva. Assicurati di registrarti a Cosmos DB.

Seguire l'istruzione Cosmos DB per registrare il provider di risorse di Azure Cosmos DB per la sottoscrizione di Azure.Follow the Cosmos DB instruction to [Register the Azure Cosmos DB](../cosmos-db/how-to-setup-cmk.md#register-resource-provider) resource provider for your Azure subscription.

### <a name="step-4-add-an-access-policy-to-your-azure-key-vault-instance"></a>PASSAGGIo 4: Aggiungere un criterio di accesso all'istanza dell'istanza dell'istanza dell'istanza dell'istanza dell'istanza della chiave di AzureSTEP 4: Add an access policy to your Azure

Assicurarsi di aggiungere l'accesso da Cosmos DB all'istanza di Azure Key Vault.Make sure to add access from Cosmos DB to your Azure Key Vault instance. Seguire l'istruzione Cosmos DB per aggiungere criteri di [accesso all'istanza](../cosmos-db/how-to-setup-cmk.md#add-an-access-policy-to-your-azure-key-vault-instance) di Azure Key Vault con l'entità del database Cosmos di Azure.Follow the Cosmos DB instruction to add an access policy to your Azure Key Vault instance with Azure Cosmos DB principal.

### <a name="step-5-enable-cmk-in-azure-sentinel"></a>PASSAGGIo 5: Abilitare CMK in Azure SentinelSTEP 5: Enable CMK in Azure Sentinel

La funzionalità CMK di Azure Sentinel viene fornita ai nuovi clienti solo dopo aver ricevuto l'accesso direttamente dal gruppo di prodotti Azure.The Azure Sentinel CMK capability is provided to new customers only after receiving access directly from the Azure product group. Usare i contatti di Microsoft per ricevere l'approvazione del team di Azure Sentinel per abilitare CMK nella soluzione.

Dopo aver ottenuto l'approvazione, verrà chiesto di fornire le seguenti informazioni per abilitare la funzionalità CMK.

-  ID area di lavoro in cui si desidera abilitare CMK

-  URL dell'insieme di credenziali delle chiavi: copiare l'"identificatore chiave" della chiave fino all'ultima barra:  
    

    ![identificatore di chiave](./media/customer-managed-keys/key-identifier.png)

    Il team di Azure Sentinel abiliterà la funzionalità CMK di Azure Sentinel per l'area di lavoro fornita.

-  Verifica da parte del team del prodotto Azure Sentinel che è stato approvato per l'utilizzo di questa funzionalità. È necessario avere questo prima di procedere.

### <a name="step-6-enable-azure-sentinel"></a>PASSAGGIo 6: Abilitare Azure SentinelSTEP 6: Enable Azure Sentinel


Passare al portale di Azure e abilitare Controllo personale di Azure nell'area di lavoro in cui è stata configurata CMK. Per altre informazioni, vedere Onboarding di Azure Sentinel.For more information, see [Azure Sentinel Onboarding](quickstart-onboard.md).

## <a name="key-encryption-key-revocation-or-deletion"></a>Revoca o eliminazione della chiave della chiave di crittografiaKey Encryption Key revocation or deletion


Nel caso in cui un utente votchi la chiave di crittografia della chiave, eliminandola o rimuovendo l'accesso per Azure Sentinel, entro un'ora, Azure Sentinel rispetterà la modifica e si comporterà come se i dati non sono più disponibili. A questo punto, qualsiasi operazione eseguita che utilizza risorse di archiviazione persistenti, ad esempio l'inserimento di dati, le modifiche di configurazione persistenti e la creazione di eventi imprevisti, verrà impedita. I dati memorizzati in precedenza non verranno eliminati, ma rimarranno inaccessibili. I dati inaccessibili sono regolati dal criterio di conservazione dei dati e verranno eliminati in conformità a tale politica.

L'unica operazione possibile dopo la revoca o l'eliminazione della chiave di crittografia è l'eliminazione dell'account.

Se l'accesso viene ripristinato dopo la revoca, Azure Sentinel ripristinerà l'accesso ai dati entro un'ora.

Per altre informazioni sul funzionamento di Monitoraggio di Azure, vedere Revoca CMK di Monitoraggio di Azure.To understand more about how this works in Azure Monitor, see [Azure Monitor CMK revocation](../azure-monitor/platform/customer-managed-keys.md#cmk-kek-revocation).

## <a name="key-encryption-key-rotation"></a>Rotazione della chiave di crittografia


Azure Sentinel e Log Analytics supportano la rotazione delle chiavi. Quando un utente esegue la rotazione delle chiavi in Key Vault, Azure Sentinel supporta la nuova chiave entro un'ora.

Nell'insieme di credenziali delle chiavi, è possibile eseguire la rotazione delle chiavi creando una nuova versione della chiave:

![rotazione delle chiavi](./media/customer-managed-keys/key-rotation.png)

È possibile disabilitare la versione precedente della chiave dopo 24 ore o dopo che i log di controllo dell'insieme di credenziali delle chiavi di Azure non mostrano più alcuna attività che usa la versione precedente.

Se si usa la stessa chiave in Azure Sentinel e in Log Analytics, è necessario eseguire la rotazione delle chiavi, è necessario aggiornare in modo esplicito la risorsa cluster in Log Analytics con la nuova versione della chiave dell'insieme di chiavi di Azure.If you use the same key in Azure Sentinel and in Log Analytics, it is necessary to perform key rotation you must explicitly update the cluster resource in Log Analytics with the new Azure Key Vault key version. Per altre informazioni, vedere [Rotazione CMK di Monitoraggio di Azure.](../azure-monitor/platform/customer-managed-keys.md#cmk-kek-rotation)

## <a name="next-steps"></a>Passaggi successivi
In this document, you learned how to set up a customer-managed key in Azure Sentinel. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:
- Informazioni su come [ottenere visibilità sui dati e sulle potenziali minacce](quickstart-get-visibility.md).
- Iniziare a [rilevare minacce con Azure Sentinel](tutorial-detect-threats.md).
- [Utilizzare le cartelle di lavoro](tutorial-monitor-your-data.md) per monitorare i dati.

