---
title: "Azure Defender per l'archiviazione: vantaggi e funzionalità"
description: Scopri i vantaggi e le funzionalità di Azure Defender per l'archiviazione.
author: memildin
ms.author: memildin
ms.date: 9/12/2020
ms.topic: conceptual
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 31d1bde1555f98164ccba32d4615ba51837c5ef7
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90940695"
---
# <a name="introduction-to-azure-defender-for-storage"></a>Introduzione ad Azure Defender per l'archiviazione

**Azure Defender per l'archiviazione** rileva attività potenzialmente dannose sugli account di archiviazione di Azure. I dati possono essere protetti indipendentemente dal fatto che vengano archiviati come contenitori BLOB, condivisioni file o data Lake.

Questo livello di protezione consente di risolvere le minacce *senza* che sia necessario essere un esperto di sicurezza e consente di gestire i sistemi di monitoraggio della sicurezza.


## <a name="availability"></a>Disponibilità

|Aspetto|Dettagli|
|----|:----|
|Stato versione:|Disponibile a livello generale|
|Prezzi|**Azure Defender per l'archiviazione** viene fatturato come indicato nella [pagina dei prezzi](security-center-pricing.md)|
|Tipi di archiviazione protetti|[Archiviazione BLOB](https://azure.microsoft.com/services/storage/blobs/)<br>[File di Azure](https://docs.microsoft.com/azure/storage/files/storage-files-introduction)<br>[Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction)|
|Cloud:|![Sì](./media/icons/yes-icon.png) Cloud commerciali<br>![Sì](./media/icons/yes-icon.png) US Gov<br>![No](./media/icons/no-icon.png) Gov per la Cina, altri gov|
|||


## <a name="what-kind-of-alerts-does-azure-defender-for-storage-provide"></a>Quali tipi di avvisi offre Azure Defender per l'archiviazione?

Gli avvisi di sicurezza vengono attivati quando sono presenti:

- **Attività sospetta** . ad esempio, l'accesso all'account di archiviazione è stato eseguito correttamente da un indirizzo IP noto come nodo di uscita attivo di Tor
- **Comportamento anomalo** , ad esempio modifiche nel modello di accesso a un account di archiviazione
- **Potenziale malware caricato** : l'analisi della reputazione hash indica che un file caricato contiene malware

Gli avvisi includono i dettagli dell'evento imprevisto che li ha attivati, nonché indicazioni su come individuare e correggere le minacce.

> [!TIP]
> È possibile simulare gli avvisi di archiviazione seguendo le istruzioni riportate in [questo post di Blog](https://techcommunity.microsoft.com/t5/azure-security-center/validating-atp-for-azure-storage-detections-in-azure-security/ba-p/1068131).


## <a name="what-is-hash-reputation-analysis-for-malware"></a>Che cos'è l'analisi della reputazione hash per malware?

Per determinare se un file caricato è sospetto, Azure Defender per l'archiviazione usa l'analisi della reputazione hash supportata da [Microsoft Threat Intelligence](https://go.microsoft.com/fwlink/?linkid=2128684). Gli strumenti di protezione dalle minacce non analizzano i file caricati, bensì esaminano i log di archiviazione e confrontano gli hash dei file appena caricati con quelli di virus, Trojan, spyware e ransomware noti. 

Quando si sospetta che un file contenga malware, il Centro sicurezza Visualizza un avviso e può facoltativamente inviare tramite posta elettronica al proprietario dell'archiviazione l'approvazione per eliminare il file sospetto. Per configurare questa rimozione automatica dei file che l'analisi della reputazione hash indica che contengono malware, distribuire un' [automazione del flusso di lavoro per attivare gli avvisi che contengono "malware potenziale caricato in un account di archiviazione"](https://techcommunity.microsoft.com/t5/azure-security-center/how-to-respond-to-potential-malware-uploaded-to-azure-storage/ba-p/1452005).

> [!NOTE]
> Per abilitare le funzionalità di protezione dalle minacce del Centro sicurezza, è necessario abilitare Azure Defender nella sottoscrizione che contiene i carichi di lavoro applicabili.
>
> È possibile abilitare **Azure Defender per l'archiviazione** a livello di sottoscrizione o di risorsa.



## <a name="next-steps"></a>Passaggi successivi

In questo articolo si è appreso come Azure Defender per l'archiviazione.

Per i materiali correlati, vedere gli articoli seguenti: 

- Che un avviso venga generato dal Centro sicurezza o ricevuto dal Centro sicurezza da un prodotto di sicurezza diverso, è possibile esportarlo. Per esportare gli avvisi in Azure Sentinel, in qualsiasi soluzione SIEM di terze parti o in qualsiasi altro strumento esterno, seguire le istruzioni riportate in [Esportazione di avvisi in un sistema SIEM](continuous-export.md).
- [Come abilitare Defender avanzato per l'archiviazione](https://docs.microsoft.com/azure/storage/common/storage-advanced-threat-protection)
- [Elenco di Azure Defender per gli avvisi di archiviazione](alerts-reference.md#alerts-azurestorage)
- [Funzionalità di intelligence per le minacce di Microsoft](https://go.microsoft.com/fwlink/?linkid=2128684)