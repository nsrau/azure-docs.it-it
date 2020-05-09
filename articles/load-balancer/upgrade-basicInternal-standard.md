---
title: Aggiornamento da Basic interno a Azure Load Balancer interno standard
description: Questo articolo illustra come aggiornare Load Balancer interni di Azure dallo SKU Basic allo SKU standard
services: load-balancer
author: irenehua
ms.service: load-balancer
ms.topic: article
ms.date: 02/23/2020
ms.author: irenehua
ms.openlocfilehash: 960897abca67bf2a43c4c056b8dfa8cce0119faa
ms.sourcegitcommit: f57297af0ea729ab76081c98da2243d6b1f6fa63
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/06/2020
ms.locfileid: "82871593"
---
# <a name="upgrade-azure-internal-load-balancer--no-outbound-connection-required"></a>Aggiornare Load Balancer interno di Azure-nessuna connessione in uscita necessaria
[Azure Load Balancer standard](load-balancer-overview.md) offre un set completo di funzionalità e disponibilità elevata tramite la ridondanza della zona. Per altre informazioni su Load Balancer SKU, vedere [tabella di confronto](https://docs.microsoft.com/azure/load-balancer/skus#skus).

Questo articolo presenta uno script di PowerShell che consente di creare un Load Balancer Standard con la stessa configurazione del Load Balancer di base insieme alla migrazione del traffico da Load Balancer di base a Load Balancer Standard.

## <a name="upgrade-overview"></a>Panoramica dell'aggiornamento

È disponibile uno script Azure PowerShell che esegue le operazioni seguenti:

* Crea uno SKU interno standard Load Balancer nel percorso specificato. Si noti che nessuna [connessione in uscita](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections) non verrà fornita dal Load Balancer interno standard.
* Copia agevolmente le configurazioni dello SKU Basic Load Balancer al Load Balancer Standard appena creato.
* Spostare facilmente gli indirizzi IP privati dal Load Balancer Basic al Load Balancer Standard appena creato.
* Spostare facilmente le VM dal pool back-end del Load Balancer di base al pool back-end della Load Balancer Standard

### <a name="caveatslimitations"></a>Caveats\Limitations

* Lo script supporta solo l'aggiornamento interno del Load Balancer in cui non è richiesta alcuna connessione in uscita. Se è necessaria una [connessione in uscita](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections) per alcune macchine virtuali, fare riferimento a questa [pagina](upgrade-InternalBasic-To-PublicStandard.md) per istruzioni. 
* Il Load Balancer di base deve trovarsi nello stesso gruppo di risorse delle macchine virtuali e delle schede di rete di back-end.
* Se il servizio di bilanciamento del carico standard viene creato in un'area diversa, non sarà possibile associare le macchine virtuali esistenti nell'area precedente alla Load Balancer Standard appena creata. Per ovviare a questa limitazione, assicurarsi di creare una nuova macchina virtuale nella nuova area.
* Se il Load Balancer non dispone di alcuna configurazione IP front-end o di un pool back-end, probabilmente si verifica un errore durante l'esecuzione dello script. Verificare che non siano vuoti.

## <a name="change-ip-allocation-method-to-static-for-frontend-ip-configuration-ignore-this-step-if-its-already-static"></a>Modificare il metodo di allocazione IP in statico per la configurazione IP front-end (ignorare questo passaggio se è già statico)

1. Selezionare **tutti i servizi** nel menu a sinistra, selezionare **tutte le risorse**e quindi selezionare il Load Balancer di base dall'elenco delle risorse.

2. In **Impostazioni**selezionare **configurazione IP**front-end e selezionare la prima configurazione IP front-end. 

3. Per **assegnazione**selezionare **static**

4. Ripetere il passaggio 3 per tutte le configurazioni IP front-end del Load Balancer di base.


## <a name="download-the-script"></a>Scaricare lo script

Scaricare lo script di migrazione dalla [PowerShell Gallery](https://www.powershellgallery.com/packages/AzureILBUpgrade/3.0).
## <a name="use-the-script"></a>USA lo script

Sono disponibili due opzioni a seconda della configurazione e delle preferenze dell'ambiente di PowerShell locale:

* Se non si dispone di Azure AZ Modules installato o non si vuole disinstallare i moduli AZ di Azure, l'opzione migliore consiste nell'usare l' `Install-Script` opzione per eseguire lo script.
* Se è necessario proteggere i moduli di Azure AZ, la scommessa migliore consiste nel scaricare lo script ed eseguirlo direttamente.

Per determinare se Azure AZ Modules è installato, eseguire `Get-InstalledModule -Name az`. Se non vengono visualizzati i moduli AZ installati, è possibile usare il `Install-Script` metodo.

### <a name="install-using-the-install-script-method"></a>Eseguire l'installazione usando il metodo Install-script

Per usare questa opzione, non è necessario che nel computer siano installati i moduli AZ di Azure. Se sono installati, il comando seguente visualizza un errore. È possibile disinstallare i moduli di Azure AZ oppure usare l'altra opzione per scaricare lo script manualmente ed eseguirlo.
  
Eseguire lo script con il comando seguente:

`Install-Script -Name AzureILBUpgrade`

Questo comando installa anche i moduli AZ richiesti.  

### <a name="install-using-the-script-directly"></a>Installare usando direttamente lo script

Se si dispone di alcuni moduli AZ di Azure installati e non è possibile disinstallarli (o non si vuole disinstallarli), è possibile scaricare manualmente lo script usando la scheda **download manuale** nel collegamento per il download dello script. Lo script viene scaricato come file nupkg non elaborato. Per installare lo script da questo file nupkg, vedere [download manuale del pacchetto](/powershell/scripting/gallery/how-to/working-with-packages/manual-download).

Per eseguire lo script:

1. Usare `Connect-AzAccount` per connettersi ad Azure.

1. Usare `Import-Module Az` per importare i moduli AZ.

1. Esaminare i parametri obbligatori:

   * **RgName: [String]: required** : questo è il gruppo di risorse per la Load Balancer di base esistente e la nuova Load Balancer standard. Per trovare questo valore stringa, passare a portale di Azure, selezionare l'origine Load Balancer di base e fare clic sulla **Panoramica** per il servizio di bilanciamento del carico. Il gruppo di risorse si trova in questa pagina.
   * **oldLBName: [String]: required** . si tratta del nome del servizio di bilanciamento di base esistente che si vuole aggiornare. 
   * **newLocation: [String]: obbligatorio** : posizione in cui verrà creata la Load Balancer standard. È consigliabile ereditare la stessa posizione del Load Balancer di base scelto per la Load Balancer Standard per una migliore associazione con altre risorse esistenti.
   * **newLBName: [String]: required** : nome del Load Balancer standard da creare.
1. Eseguire lo script usando i parametri appropriati. Il completamento può richiedere da cinque a sette minuti.

    **Esempio**

   ```azurepowershell
   AzureILBUpgrade.ps1 -rgName "test_InternalUpgrade_rg" -oldLBName "LBForInternal" -newlocation "centralus" -newLbName "LBForUpgrade"
   ```

## <a name="common-questions"></a>Domande frequenti

### <a name="are-there-any-limitations-with-the-azure-powershell-script-to-migrate-the-configuration-from-v1-to-v2"></a>Esistono limitazioni allo script Azure PowerShell per eseguire la migrazione della configurazione dalla versione V1 alla versione V2?

Sì. Vedere [avvertenze/limitazioni](#caveatslimitations).

### <a name="does-the-azure-powershell-script-also-switch-over-the-traffic-from-my-basic-load-balancer-to-the-newly-created-standard-load-balancer"></a>Lo script di Azure PowerShell passa anche il traffico dal Load Balancer di base al Load Balancer Standard appena creato?

Sì, esegue la migrazione del traffico. Se si vuole eseguire la migrazione personale del traffico, usare [questo script](https://www.powershellgallery.com/packages/AzureILBUpgrade/1.0) che non sposta le VM.

### <a name="i-ran-into-some-issues-with-using-this-script-how-can-i-get-help"></a>Si sono verificati alcuni problemi con l'uso di questo script. Come è possibile ottenere assistenza?
  
È possibile inviare un messaggio di slbupgradesupport@microsoft.composta elettronica a, aprire un caso di supporto con il supporto tecnico di Azure o eseguire entrambe le operazioni.

## <a name="next-steps"></a>Passaggi successivi

[Informazioni sulle Load Balancer Standard](load-balancer-overview.md)
