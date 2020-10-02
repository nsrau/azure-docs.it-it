---
title: Azure Defender per Archiviazione - Vantaggi e funzionalità
description: Informazioni sui vantaggi e sulle funzionalità di Azure Defender per Archiviazione.
author: memildin
ms.author: memildin
ms.date: 9/22/2020
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 8b6faa6e2e8928e008370cb0eca00595712a679d
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/29/2020
ms.locfileid: "91449027"
---
# <a name="introduction-to-azure-defender-for-storage"></a>Introduzione ad Azure Defender per Archiviazione

**Azure Defender per Archiviazione** rileva le attività potenzialmente dannose negli account di archiviazione di Azure. I dati possono essere protetti indipendentemente dal fatto che siano archiviati come contenitori BLOB, condivisioni file o data lake.

Questo livello di protezione consente di affrontare le minacce *senza* dover essere esperti di sicurezza e semplifica la gestione dei sistemi di monitoraggio della sicurezza.


## <a name="availability"></a>Disponibilità

|Aspetto|Dettagli|
|----|:----|
|Stato della versione:|Disponibile a livello generale|
|Prezzi:|**Azure Defender per Archiviazione** è soggetto alle tariffe visualizzate nella [pagina dei prezzi](security-center-pricing.md)|
|Tipi di archivi protetti|[Archiviazione BLOB](https://azure.microsoft.com/services/storage/blobs/)<br>[File di Azure](https://docs.microsoft.com/azure/storage/files/storage-files-introduction)<br>[Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction)|
|Cloud:|![Sì](./media/icons/yes-icon.png) Cloud commerciali<br>![Sì](./media/icons/yes-icon.png) US Gov<br>![No](./media/icons/no-icon.png) Governo cinese, altri governi|
|||


## <a name="what-kind-of-alerts-does-azure-defender-for-storage-provide"></a>Quali tipi di avvisi vengono forniti da Azure Defender per Archiviazione?

Gli avvisi di sicurezza vengono attivati in caso di:

- **Attività sospette**: ad esempio, è stato eseguito l'accesso all'account di archiviazione da un indirizzo IP noto come nodo di uscita attivo di Tor
- **Comportamento anomalo**: ad esempio, modifiche nel modello di accesso a un account di archiviazione
- **Caricamento di potenziale malware**: l'analisi della reputazione hash indica che un file caricato contiene malware

Gli avvisi includono i dettagli dell'evento imprevisto che li ha attivati e raccomandazioni su come analizzare e risolvere le minacce.

> [!TIP]
> È possibile simulare gli avvisi relativi all'archiviazione seguendo le istruzioni riportate in [questo post di blog](https://techcommunity.microsoft.com/t5/azure-security-center/validating-atp-for-azure-storage-detections-in-azure-security/ba-p/1068131).


## <a name="what-is-hash-reputation-analysis-for-malware"></a>Cos'è l'analisi della reputazione hash per il malware?

Per determinare se un file caricato è sospetto, Azure Defender per Archiviazione usa l'analisi della reputazione hash supportata dalla funzionalità di [intelligence sulle minacce Microsoft](https://go.microsoft.com/fwlink/?linkid=2128684). Gli strumenti di protezione dalle minacce non analizzano i file caricati, bensì esaminano i log di archiviazione e confrontano gli hash dei nuovi file caricati con quelli di virus, trojan, spyware e ransomware noti. 

Se sospetta che un file contenga malware, il Centro sicurezza visualizza un avviso e può facoltativamente inviare un messaggio di posta elettronica al proprietario dell'archiviazione per chiedere l'approvazione per eliminare il file sospetto. Per configurare questa rimozione automatica dei file che l'analisi della reputazione hash indica come contenenti malware, distribuire un'[automazione del flusso di lavoro che venga attivata da avvisi che contengono "Potenziale malware caricato in un account di archiviazione"](https://techcommunity.microsoft.com/t5/azure-security-center/how-to-respond-to-potential-malware-uploaded-to-azure-storage/ba-p/1452005).

> [!NOTE]
> Per abilitare le funzionalità di protezione dalle minacce del Centro sicurezza, è necessario abilitare Azure Defender nella sottoscrizione contenente i carichi di lavoro applicabili.
>
> È possibile abilitare **Azure Defender per Archiviazione** a livello di sottoscrizione o di risorsa.



## <a name="next-steps"></a>Passaggi successivi

In questo articolo sono state fornite informazioni su Azure Defender per Archiviazione.

Per i materiali correlati, vedere gli articoli seguenti: 

- Che un avviso venga generato dal Centro sicurezza o ricevuto dal Centro sicurezza da un prodotto di sicurezza diverso, è possibile esportarlo. Per esportare gli avvisi in Azure Sentinel, in qualsiasi soluzione SIEM di terze parti o in qualsiasi altro strumento esterno, seguire le istruzioni riportate in [Esportazione di avvisi in un sistema SIEM](continuous-export.md).
- [Come abilitare Azure Defender per Archiviazione](https://docs.microsoft.com/azure/storage/common/storage-advanced-threat-protection)
- [Elenco degli avvisi di Azure Defender per Archiviazione](alerts-reference.md#alerts-azurestorage)
- [Funzionalità dell'intelligence sulle minacce di Microsoft](https://go.microsoft.com/fwlink/?linkid=2128684)