---
title: Risoluzione dei problemi di Microsoft Azure Stack | Documenti Microsoft
description: Azure Stack Development Kit (ASDK) informazioni sulla risoluzione.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/22/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: 6c715f07f75c9196b7cf2cc8659c6e541e1260da
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/23/2018
---
# <a name="microsoft-azure-stack-development-kit-asdk-troubleshooting"></a>Risoluzione dei problemi di Microsoft Azure Stack Development Kit (ASDK)
Questo documento fornisce informazioni sulla risoluzione dei problemi comuni per la ASDK. Se si sta verificando un problema non documentato, assicurarsi di controllare la [Forum MSDN di Azure Stack](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack) per informazioni e ulteriore assistenza.  

> [!IMPORTANT]
> Poiché il ASDK è un ambiente di valutazione, non sono supportate ufficiale offerti tramite Microsoft cliente il supporto tecnico clienti Microsoft.

Le indicazioni per la risoluzione dei problemi descritti in questa sezione sono derivate da diverse origini e potrebbero essere o non possono essere risolti al problema specifico. Esempi di codice vengono forniti "così com'è" e i risultati previsti non possono essere garantiti. In questa sezione è soggetto a modifiche frequenti e gli aggiornamenti come vengono implementati i miglioramenti al prodotto.

## <a name="deployment"></a>Distribuzione
### <a name="deployment-failure"></a>Errore di distribuzione
Se si verifica un errore durante l'installazione, è possibile riavviare la distribuzione del passaggio non riuscito con il parametro - opzione rieseguire dello script di distribuzione come nell'esempio seguente:

  ```powershell
  cd C:\CloudDeployment\Setup
  .\InstallAzureStackPOC.ps1 -Rerun
  ```

### <a name="at-the-end-of-the-deployment-the-powershell-session-is-still-open-and-doesnt-show-any-output"></a>Al termine della distribuzione, la sessione di PowerShell è ancora aperta e non visualizza alcun output
Questo comportamento è probabilmente solo il risultato del comportamento predefinito di una finestra di comando di PowerShell, quando è stato selezionato. La distribuzione di kit sviluppo ha avuto esito positivo, ma lo script è stata sospesa quando si seleziona la finestra. È possibile verificare l'installazione è stata completata cercando la parola "select" sulla barra del titolo della finestra di comando. Premere il tasto ESC per deselezionarlo e deve essere visualizzato il messaggio di completamento dopo di esso.

## <a name="virtual-machines"></a>Macchine virtuali
### <a name="default-image-and-gallery-item"></a>Elemento di raccolta e l'immagine predefinita
Prima di distribuire le macchine virtuali nello Stack di Azure, è necessario aggiungere un elemento di raccolta e l'immagine di Windows Server.

### <a name="after-restarting-my-azure-stack-host-some-vms-may-not-automatically-start"></a>Dopo il riavvio dell'host di Stack di Azure, alcune macchine virtuali non venga avviato automaticamente.
Dopo il riavvio dell'host, è possibile riscontrare servizi Azure Stack non sono immediatamente disponibili. In questo modo Azure Stack [infrastruttura di macchine virtuali](asdk-architecture.md#virtual-machine-roles) e intraprendere RPs alcune periodo di tempo per verificare la coerenza, ma verrà infine avviata automaticamente.

È anche possibile riscontrare tenant che macchine virtuali non avviare automaticamente dopo il riavvio dell'host del kit di sviluppo dello Stack di Azure. Questo è un problema noto e richiede solo pochi passaggi manuali per portarli online:

1.  Nell'host di kit di sviluppo dello Stack di Azure, avviare **gestione Cluster di Failover** dal Menu Start.
2.  Selezionare il cluster **S Cluster.azurestack.local**.
3.  Selezionare **Ruoli**.
4.  Le macchine virtuali tenant visualizzate un *salvato* stato. Quando tutte le macchine virtuali dell'infrastruttura sono in esecuzione, le macchine virtuali tenant e scegliere **avviare** per riprendere la macchina virtuale.

### <a name="i-have-deleted-some-virtual-machines-but-still-see-the-vhd-files-on-disk-is-this-behavior-expected"></a>Sono eliminati alcune macchine virtuali, ma comunque visualizzare i file di disco rigido virtuale su disco. Questo comportamento è previsto?
Sì, questo comportamento è previsto. È stato progettato in questo modo perché:

* Quando si elimina una macchina virtuale, è possibile che i dischi rigidi virtuali non vengono eliminati. I dischi sono separate le risorse nel gruppo di risorse.
* Quando un account di archiviazione Ottiene eliminato, l'eliminazione è visibile immediatamente tramite Gestione risorse di Azure, ma i dischi che potrebbe contenere ancora vengono conservati nell'archiviazione fino a quando non viene eseguito l'operazione di garbage collection.

Se viene visualizzato "orfano" i dischi rigidi virtuali, è importante sapere se fanno parte della cartella per un account di archiviazione che è stato eliminato. Se l'account di archiviazione non è stato eliminato, è normale che sono ancora presenti.

Altre informazioni sulla configurazione il recupero di soglia e su richiesta di conservazione in [gestire account di archiviazione](.\.\azure-stack-manage-storage-accounts.md).

## <a name="storage"></a>Archiviazione
### <a name="storage-reclamation"></a>Recupero di archiviazione
Potrebbe richiedere fino a 14 ore capacità recuperata da visualizzare nel portale. Recupero di spazio dipende da diversi fattori tra cui percentuale di utilizzo di file contenitore interno nell'archivio blob di blocco. Pertanto, a seconda della quantità di dati viene eliminata, non c'è garanzia sulla quantità di spazio che potrebbe essere recuperato durante l'esecuzione di garbage collection.

## <a name="next-steps"></a>Passaggi successivi
[Visitare il forum di supporto di Azure Stack](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack)

