---
title: Eseguire l'aggiornamento da interno di base a interno standard - Azure Load BalancerUpgrade from Basic Internal to Standard Internal - Azure Load Balancer
description: Questo articolo illustra come aggiornare Azure Internal Load Balancer dallo SKU Basic allo SKU Standard
services: load-balancer
author: irenehua
ms.service: load-balancer
ms.topic: article
ms.date: 02/23/2020
ms.author: irenehua
ms.openlocfilehash: 239dc0f3133a5adf59a23d333131c91d3a655597
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2020
ms.locfileid: "81770380"
---
# <a name="upgrade-azure-internal-load-balancer--no-outbound-connection-required"></a>Aggiornare il servizio di bilanciamento del carico interno di Azure - Nessuna connessione in uscita richiestaUpgrade Azure Internal Load Balancer - No Outbound Connection Required
[Azure Standard Load Balancer](load-balancer-overview.md) offre un set completo di funzionalità e disponibilità elevata tramite la ridondanza delle zone. Per altre informazioni sullo SKU di Load Balancer, vedere la tabella di [confronto](https://docs.microsoft.com/azure/load-balancer/concepts-limitations#skus).

In questo articolo viene presentato uno script di PowerShell che crea un servizio di bilanciamento del carico Standard con la stessa configurazione del servizio di bilanciamento del carico di base e la migrazione del traffico da Basic Load Balancer a Load Balancer Standard.This article introduces a PowerShell script which creates a Standard Load Balancer with the same configuration as the Basic Load Balancer along with migrating traffic from Basic Load Balancer to Standard Load Balancer.

## <a name="upgrade-overview"></a>Panoramica dell'aggiornamento

È disponibile uno script di Azure PowerShell che esegue le operazioni seguenti:An Azure PowerShell script is available that does the following:

* Crea un servizio di bilanciamento del carico SKU interno standard nel percorso specificato. Si noti che non verrà fornita alcuna [connessione in uscita](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections) da Standard Internal Load Balancer.
* Copia senza problemi le configurazioni di Basic SKU Load Balancer nel nuovo servizio di bilanciamento del carico Standard.
* Spostare senza problemi gli indirizzi IP privati da Basic Load Balancer al nuovo servizio di bilanciamento del carico Standard.
* Spostare senza problemi le macchine virtuali dal pool back-end del servizio di bilanciamento del carico di base al pool back-end del servizio di bilanciamento del carico Standard

### <a name="caveatslimitations"></a>Avvertenze - Limitazioni

* Lo script supporta solo l'aggiornamento del servizio di bilanciamento del carico interno in cui non è necessaria alcuna connessione in uscita. Se è stata richiesta la [connessione in uscita](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections) per alcune delle macchine virtuali, fare riferimento a questa [pagina](upgrade-InternalBasic-To-PublicStandard.md) per istruzioni. 
* Se il servizio di bilanciamento del carico Standard viene creato in un'area diversa, non sarà possibile associare le macchine virtuali esistenti nell'area precedente al servizio di bilanciamento del carico Standard appena creato. Per aggirare questa limitazione, assicurarsi di creare una nuova macchina virtuale nella nuova area.
* Se il servizio di bilanciamento del carico non dispone di alcuna configurazione IP front-end o pool di back-end, è probabile che si ripresenti un errore durante l'esecuzione dello script. Assicurarsi che non siano vuoti.

## <a name="download-the-script"></a>Scarica lo script

Scaricare lo script di migrazione da [PowerShell Gallery](https://www.powershellgallery.com/packages/AzureILBUpgrade/2.0).
## <a name="use-the-script"></a>Utilizzare lo script

Sono disponibili due opzioni a seconda dell'installazione e delle preferenze dell'ambiente PowerShell locale:There are two options for you depending on your local PowerShell environment setup and preferences:

* Se i moduli di Azure Az non sono installati o non è consigliabile disinstallare i `Install-Script` moduli di Azure Az, l'opzione migliore consiste nell'usare l'opzione per eseguire lo script.
* Se è necessario mantenere i moduli di Azure Az, la soluzione migliore consiste nel scaricare lo script ed eseguirlo direttamente.

Per determinare se sono installati i `Get-InstalledModule -Name az`moduli di Azure Az, eseguire . Se non vedi alcun modulo Az installato, puoi `Install-Script` utilizzare il metodo .

### <a name="install-using-the-install-script-method"></a>Eseguire l'installazione utilizzando il metodo Install-Script

Per usare questa opzione, non è necessario che nel computer siano installati i moduli di Azure Az.To use this option, you must not have the Azure Az modules installed on your computer. Se sono installati, il comando seguente visualizza un errore. È possibile disinstallare i moduli di Azure Az oppure usare l'altra opzione per scaricare lo script manualmente ed eseguirlo.
  
Eseguire lo script con il comando seguente:

`Install-Script -Name AzureILBUpgrade`

Questo comando installa anche i moduli Az necessari.  

### <a name="install-using-the-script-directly"></a>Eseguire l'installazione utilizzando lo script direttamente

Se sono installati alcuni moduli di Azure Az e non è possibile disinstallarli o non si desidera disinstallarli, è possibile scaricare manualmente lo script usando la scheda **Download manuale** nel collegamento per il download dello script. Lo script viene scaricato come file nupkg non elaborato. Per installare lo script da questo file nupkg, vedere [Download manuale del pacchetto](/powershell/scripting/gallery/how-to/working-with-packages/manual-download).

Per eseguire lo script:

1. Usare `Connect-AzAccount` per connettersi ad Azure.Use to connect to Azure.

1. Utilizzare `Import-Module Az` per importare i moduli Az.

1. Esaminare i parametri richiesti:

   * **rgName: [Stringa]: Obbligatorio: gruppo** di risorse per il servizio di bilanciamento del carico di base esistente e il nuovo servizio di bilanciamento del carico standard. Per trovare questo valore stringa, passare al portale di Azure, selezionare l'origine di Basic Load Balancer e fare clic su **Panoramica** per il servizio di bilanciamento del carico. Il gruppo di risorse si trova in tale pagina.
   * **oldLBName: [String]: Obbligatorio** – Questo è il nome del sistema di bilanciamento di base esistente che si desidera aggiornare. 
   * **newlocation: [Stringa]: Obbligatorio** – Questa è la posizione in cui verrà creato il servizio di bilanciamento del carico standard.newlocation: [String]: Required – This is the location in which the Standard Load Balancer will be created. È consigliabile ereditare la stessa posizione del servizio di bilanciamento del carico di base scelto in Load Balancer standard per una migliore associazione con altre risorse esistenti.
   * **newLBName: [String]: Obbligatorio** – Questo è il nome per il servizio di bilanciamento del carico standard da creare.
1. Eseguire lo script utilizzando i parametri appropriati. La finitura potrebbe richiedere da cinque a sette minuti.

    **Esempio**

   ```azurepowershell
   AzureILBUpgrade.ps1 -rgName "test_InternalUpgrade_rg" -oldLBName "LBForInternal" -newlocation "centralus" -newLbName "LBForUpgrade"
   ```

## <a name="common-questions"></a>Domande frequenti

### <a name="are-there-any-limitations-with-the-azure-powershell-script-to-migrate-the-configuration-from-v1-to-v2"></a>Esistono limitazioni con lo script di Azure PowerShell per eseguire la migrazione della configurazione dalla versione 1 alla versione 2?

Sì. Vedere [Avvertenze/Limitazioni](#caveatslimitations).

### <a name="does-the-azure-powershell-script-also-switch-over-the-traffic-from-my-basic-load-balancer-to-the-newly-created-standard-load-balancer"></a>Lo script di Azure PowerShell passa anche il traffico dal servizio di bilanciamento del carico di base al servizio di bilanciamento del carico standard appena creato?

Sì, migra il traffico. Se si desidera eseguire la migrazione del traffico personalmente, usare [questo script](https://www.powershellgallery.com/packages/AzureILBUpgrade/1.0) che non sposta automaticamente le macchine virtuali.

### <a name="i-ran-into-some-issues-with-using-this-script-how-can-i-get-help"></a>Mi sono imbattuto in alcuni problemi con l'utilizzo di questo script. Come posso ottenere aiuto?
  
È possibile inviare slbupgradesupport@microsoft.comun messaggio di posta elettronica a , aprire un caso di supporto con il supporto di Azure o eseguire entrambe le operazione.

## <a name="next-steps"></a>Passaggi successivi

[Informazioni su Load Balancer Standard](load-balancer-overview.md)
