---
title: Risoluzione dei problemi di Microsoft Azure Stack | Microsoft Docs
description: Azure Stack risoluzione dei problemi.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: a20bea32-3705-45e8-9168-f198cfac51af
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/09/2018
ms.author: jeffgilb
ms.reviewer: unknown
ms.openlocfilehash: 53bb89daee47d5f380786246070cf5cddb69b731
ms.sourcegitcommit: aa988666476c05787afc84db94cfa50bc6852520
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/10/2018
ms.locfileid: "37929559"
---
# <a name="microsoft-azure-stack-troubleshooting"></a>Risoluzione dei problemi di Microsoft Azure Stack

Questo documento fornisce informazioni sulla risoluzione dei problemi comuni per Azure Stack. 

> [!NOTE]
> Poiché Azure Stack Technical Development Kit (ASDK) viene offerto come un ambiente di valutazione, non è previsto alcun supporto ufficiale dal supporto tecnico clienti Microsoft. Se si sta verificando un problema, assicurarsi di controllare la [forum di MSDN su Azure Stack](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack) per ottenere assistenza e informazioni.  

I consigli per la risoluzione dei problemi descritti in questa sezione derivano da origini diverse e possono o potrebbero non risolvere il problema specifico. Esempi di codice vengono forniti come sono e non è possibile garantire i risultati previsti. In questa sezione è soggetta a modifiche frequenti e gli aggiornamenti come vengono implementati i miglioramenti al prodotto.

## <a name="deployment"></a>Distribuzione
### <a name="deployment-failure"></a>Errore di distribuzione
Se si verifica un errore durante l'installazione, è possibile riavviare la distribuzione del passaggio non riuscito con l'opzione - opzione riesecuzione dello script di distribuzione.  

### <a name="at-the-end-of-asdk-deployment-the-powershell-session-is-still-open-and-doesnt-show-any-output"></a>Al termine della distribuzione ASDK, la sessione di PowerShell è ancora aperta e non visualizza alcun output.
Questo comportamento è probabile che solo il risultato del comportamento predefinito di una finestra di comando di PowerShell, quando è stata selezionata. La distribuzione di kit di sviluppo ha avuto esito positivo, ma lo script è stato sospeso quando si seleziona la finestra. È possibile verificare il programma di installazione è stata completata cercando la parola "seleziona" sulla barra del titolo della finestra di comando.  Premere il tasto ESC per deselezionarlo, e deve essere visualizzato il messaggio di completamento dopo di esso.

## <a name="virtual-machines"></a>Macchine virtuali
### <a name="default-image-and-gallery-item"></a>Elemento di raccolta e di immagine predefinito
Un elemento di raccolta e l'immagine di Windows Server deve essere aggiunto prima di distribuire le macchine virtuali in Azure Stack.

### <a name="after-restarting-my-azure-stack-host-some-vms-may-not-automatically-start"></a>Dopo il riavvio dell'host di Azure Stack, alcune macchine virtuali non venga avviato automaticamente.
Dopo il riavvio dell'host, è possibile riscontrare i servizi di Azure Stack non sono immediatamente disponibili.  In questo modo Azure Stack [VM dell'infrastruttura](..\azure-stack\asdk\asdk-architecture.md#virtual-machine-roles) e richiedono alcuni minuti per verificare la coerenza dei provider di risorse, ma alla fine verranno avviato automaticamente.

È anche possibile notare tenant che le macchine virtuali non vengono avviati automaticamente dopo un riavvio dell'host Azure Stack development kit. Questo è un problema noto e richiede solo pochi passaggi manuali per portarli online:

1.  Nell'host Azure Stack development kit, avviare **gestione Cluster di Failover** dal Menu Start.
2.  Selezionare il cluster **S-Cluster.azurestack.local**.
3.  Selezionare **Ruoli**.
4.  Le macchine virtuali tenant vengono visualizzati una *salvato* dello stato. Quando tutte le macchine virtuali dell'infrastruttura sono in esecuzione, fare doppio clic su macchine virtuali tenant e selezionare **avviare** per riprendere la macchina virtuale.

### <a name="i-have-deleted-some-virtual-machines-but-still-see-the-vhd-files-on-disk-is-this-behavior-expected"></a>Sono eliminati alcune macchine virtuali, ma ancora visualizzati i file di disco rigido virtuale su disco. Questo comportamento è previsto?
Sì, questo comportamento è previsto. È stato progettato in questo modo perché:

* Quando si elimina una VM, dischi rigidi virtuali non vengono eliminati. I dischi sono risorse separate nel gruppo di risorse.
* Quando un account di archiviazione eliminato, l'eliminazione è visibile immediatamente tramite Azure Resource Manager, ma può contenere dischi ancora vengono conservati nell'archiviazione fino a quando non viene eseguita l'operazione di garbage collection.

Se viene visualizzato "orfani" i dischi rigidi virtuali, è importante sapere se fanno parte della cartella per un account di archiviazione che è stato eliminato. Se l'account di archiviazione non è stato eliminato, è normale che sono ancora presenti.

Altre informazioni sulla configurazione di conservazione soglia e on demand vengono recuperati per primi nel [gestire gli account di archiviazione](azure-stack-manage-storage-accounts.md).

## <a name="storage"></a>Archiviazione
### <a name="storage-reclamation"></a>Recupero di archiviazione
Potrebbero occorrere fino a 14 ore per la capacità recuperata da visualizzare nel portale. Recupero di spazio dipende da diversi fattori tra cui percentuale di utilizzo di file interno del contenitore nell'archivio blob in blocchi. Pertanto, a seconda della quantità di dati viene eliminata, non c'è garanzia per la quantità di spazio che può essere recuperata quando garbage collector viene eseguito.

