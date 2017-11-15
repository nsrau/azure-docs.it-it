---
title: Risoluzione dei problemi di Microsoft Azure Stack | Documenti Microsoft
description: Azure Stack risoluzione dei problemi.
services: azure-stack
documentationcenter: 
author: heathl17
manager: byronr
editor: 
ms.assetid: a20bea32-3705-45e8-9168-f198cfac51af
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/11/2017
ms.author: helaw
ms.openlocfilehash: 0a8e871a3a44cb14503832d2f3a096712f8112a7
ms.sourcegitcommit: 659cc0ace5d3b996e7e8608cfa4991dcac3ea129
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/13/2017
---
# <a name="microsoft-azure-stack-troubleshooting"></a>Risoluzione dei problemi di Microsoft Azure Stack

*Si applica a: Azure Stack Development Kit*

Questo documento fornisce informazioni sulla risoluzione dei problemi comuni per lo Stack di Azure. 

Poiché il Kit di sviluppo tecnica di Azure Stack viene offerto come un ambiente di valutazione, non è previsto alcun supporto ufficiale dal supporto tecnico clienti Microsoft.  Se si sta verificando un problema non documentato, assicurarsi di controllare il [Forum MSDN di Azure Stack](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack) per ulteriore assistenza e informazioni.  

Le indicazioni per la risoluzione dei problemi descritti in questa sezione sono derivate da diverse origini e potrebbero essere o non possono essere risolti al problema specifico. Vengono forniti esempi di codice come sono e non possono essere garantiti risultati previsti. In questa sezione è soggetto a modifiche frequenti e gli aggiornamenti come vengono implementati i miglioramenti al prodotto.

## <a name="deployment"></a>Distribuzione
### <a name="deployment-failure"></a>Errore di distribuzione
Se si verifica un errore durante l'installazione, è possibile utilizzare utilizzare l'opzione rieseguire dello script di distribuzione per riavviare la distribuzione del passaggio non riuscito.  


### <a name="at-the-end-of-the-deployment-the-powershell-session-is-still-open-and-doesnt-show-any-output"></a>Al termine della distribuzione, la sessione di PowerShell è ancora aperta e non visualizza alcun output
Questo comportamento è probabilmente solo il risultato del comportamento predefinito di una finestra di comando di PowerShell, quando è stato selezionato. La distribuzione di kit di sviluppo effettivamente ha avuto esito positivo, ma lo script è stato sospeso quando si seleziona la finestra. È possibile verificare che questo è il caso cercando la parola "select" sulla barra del titolo della finestra di comando.  Premere il tasto ESC per deselezionarlo e deve essere visualizzato il messaggio di completamento dopo di esso.

## <a name="virtual-machines"></a>Macchine virtuali
### <a name="default-image-and-gallery-item"></a>Elemento di raccolta e l'immagine predefinita
Prima di distribuire le macchine virtuali nello Stack di Azure, è innanzitutto necessario aggiungere un elemento di raccolta e l'immagine di Windows Server.

### <a name="after-restarting-my-azure-stack-host-some-vms-may-not-automatically-start"></a>Dopo il riavvio dell'host di Stack di Azure, alcune macchine virtuali non venga avviato automaticamente.
Dopo il riavvio dell'host, è possibile riscontrare servizi Azure Stack non sono immediatamente disponibili.  In questo modo Azure Stack [infrastruttura di macchine virtuali](azure-stack-architecture.md#virtual-machine-roles) e RPs richiedere un po' le per verificare la coerenza, ma verrà infine avviata automaticamente.

È anche possibile riscontrare tenant che macchine virtuali non avviare automaticamente dopo il riavvio dell'host del kit di sviluppo dello Stack di Azure.  Questo è un problema noto e richiede solo pochi passaggi manuali per portarli online:

1.  Nell'host di kit di sviluppo dello Stack di Azure, avviare **gestione Cluster di Failover** dal Menu Start.
2.  Selezionare il cluster **S Cluster.azurestack.local**.
3.  Selezionare **Ruoli**.
4.  Le macchine virtuali tenant verranno visualizzate un *salvato* stato.  Quando tutte le macchine virtuali dell'infrastruttura sono in esecuzione, le macchine virtuali tenant e scegliere **avviare** per riprendere la macchina virtuale.

### <a name="i-have-deleted-some-virtual-machines-but-still-see-the-vhd-files-on-disk-is-this-behavior-expected"></a>Sono eliminati alcune macchine virtuali, ma comunque visualizzare i file di disco rigido virtuale su disco. Questo comportamento è previsto?
Sì, questo comportamento è previsto. È stato progettato in questo modo perché:

* Quando si elimina una macchina virtuale, è possibile che i dischi rigidi virtuali non vengono eliminati. I dischi sono separate le risorse nel gruppo di risorse.
* Quando un account di archiviazione Ottiene eliminato, l'eliminazione è visibile immediatamente tramite Gestione risorse di Azure (portale, PowerShell), ma i dischi che potrebbe contenere ancora vengono conservati nell'archiviazione fino a quando non viene eseguito l'operazione di garbage collection.

Se viene visualizzato "orfano" i dischi rigidi virtuali, è importante sapere se fanno parte della cartella per un account di archiviazione che è stato eliminato. Se l'account di archiviazione non è stato eliminato, è normale che sono ancora presenti.

Altre informazioni sulla configurazione il recupero di soglia e su richiesta di conservazione in [gestire account di archiviazione](azure-stack-manage-storage-accounts.md).

## <a name="storage"></a>Archiviazione
### <a name="storage-reclamation"></a>Recupero di archiviazione
Potrebbe richiedere fino a quattordici ore capacità recuperata da visualizzare nel portale. Recupero di spazio dipende da diversi fattori tra cui percentuale di utilizzo di file contenitore interno nell'archivio blob di blocco. Pertanto, a seconda della quantità di dati viene eliminata, non c'è garanzia sulla quantità di spazio che potrebbe essere recuperato durante l'esecuzione di garbage collection.

## <a name="windows-azure-pack-connector"></a>Connettore di Windows Azure Pack
* Se si modifica la password dell'account azurestackadmin dopo aver distribuito il kit di sviluppo dello Stack di Azure, è non possibile configurare non è più in modalità multi-cloud. Pertanto, non sarà possibile connettersi all'ambiente di Windows Azure Pack di destinazione.
* Dopo aver impostato la modalità multi-cloud:
    * Il dashboard di un utente è visibile solo dopo che reimpostano le impostazioni del portale. (Nel portale per gli utenti, scegliere l'icona di impostazioni del portale (icona a forma di ingranaggio nell'angolo superiore destro). In **ripristinare le impostazioni predefinite**, fare clic su **applica**.)
    * I titoli dei dashboard potrebbero non essere visualizzato. Se si verifica questo problema, è necessario aggiungerle manualmente nuovamente.
    * Alcuni riquadri non vengano visualizzate correttamente quando è possibile aggiungerli al dashboard. Per risolvere questo problema, aggiornare il browser.



