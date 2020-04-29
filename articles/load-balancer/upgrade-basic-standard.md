---
title: Eseguire l'aggiornamento dal pubblico di base al Azure Load Balancer pubblico standard
description: Questo articolo illustra come aggiornare il Load Balancer pubblico di Azure dallo SKU Basic allo SKU standard
services: load-balancer
author: irenehua
ms.service: load-balancer
ms.topic: article
ms.date: 01/23/2020
ms.author: irenehua
ms.openlocfilehash: a2d6f41756d87e43ac7db9e6a8670c453920c834
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81770360"
---
# <a name="upgrade-azure-public-load-balancer"></a>Aggiornare Load Balancer pubblico di Azure
[Azure Load Balancer standard](load-balancer-overview.md) offre un set completo di funzionalità e disponibilità elevata tramite la ridondanza della zona. Per altre informazioni su Load Balancer SKU, vedere [tabella di confronto](https://docs.microsoft.com/azure/load-balancer/concepts-limitations#skus).

In un aggiornamento sono disponibili tre fasi:

1. Eseguire la migrazione della configurazione
2. Aggiungere VM ai pool back-end di Load Balancer Standard

Questo articolo illustra la migrazione della configurazione. L'aggiunta di macchine virtuali ai pool back-end può variare a seconda dell'ambiente specifico. Tuttavia, [vengono fornite](#add-vms-to-backend-pools-of-standard-load-balancer)alcune raccomandazioni generali di alto livello.

## <a name="upgrade-overview"></a>Panoramica dell'aggiornamento

È disponibile uno script Azure PowerShell che esegue le operazioni seguenti:

* Crea uno SKU standard Load Balancer nel gruppo di risorse e nella posizione specificati dall'utente.
* Copia agevolmente le configurazioni dello SKU Basic Load Balancer al Load Balancer Standard appena creato.
* Crea una regola in uscita predefinita che consente la connettività in uscita.

### <a name="caveatslimitations"></a>Caveats\Limitations

* Lo script supporta solo l'aggiornamento di Load Balancer pubblici. Per l'aggiornamento interno Load Balancer di base, fare riferimento a [Questa pagina](https://docs.microsoft.com/azure/load-balancer/upgrade-basicinternal-standard) per istruzioni.
* Il Load Balancer Standard dispone di un nuovo indirizzo pubblico. Non è possibile spostare facilmente gli indirizzi IP associati a Load Balancer di base esistenti Load Balancer Standard poiché hanno SKU differenti.
* Se il servizio di bilanciamento del carico standard viene creato in un'area diversa, non sarà possibile associare le macchine virtuali esistenti nell'area precedente alla Load Balancer Standard appena creata. Per ovviare a questa limitazione, assicurarsi di creare una nuova macchina virtuale nella nuova area.
* Se il Load Balancer non dispone di alcuna configurazione IP front-end o di un pool back-end, probabilmente si verifica un errore durante l'esecuzione dello script. Verificare che non siano vuoti.

## <a name="download-the-script"></a>Scaricare lo script

Scaricare lo script di migrazione dalla [PowerShell Gallery](https://www.powershellgallery.com/packages/AzurePublicLBUpgrade/2.0).
## <a name="use-the-script"></a>USA lo script

Sono disponibili due opzioni a seconda della configurazione e delle preferenze dell'ambiente di PowerShell locale:

* Se non si dispone di Azure AZ Modules installato o non si vuole disinstallare i moduli AZ di Azure, l'opzione migliore consiste nell'usare l' `Install-Script` opzione per eseguire lo script.
* Se è necessario proteggere i moduli di Azure AZ, la scommessa migliore consiste nel scaricare lo script ed eseguirlo direttamente.

Per determinare se Azure AZ Modules è installato, eseguire `Get-InstalledModule -Name az`. Se non vengono visualizzati i moduli AZ installati, è possibile usare il `Install-Script` metodo.

### <a name="install-using-the-install-script-method"></a>Eseguire l'installazione usando il metodo Install-script

Per usare questa opzione, non è necessario che nel computer siano installati i moduli AZ di Azure. Se sono installati, il comando seguente visualizza un errore. È possibile disinstallare i moduli di Azure AZ oppure usare l'altra opzione per scaricare lo script manualmente ed eseguirlo.
  
Eseguire lo script con il comando seguente:

`Install-Script -Name AzurePublicLBUpgrade`

Questo comando installa anche i moduli AZ richiesti.  

### <a name="install-using-the-script-directly"></a>Installare usando direttamente lo script

Se si dispone di alcuni moduli AZ di Azure installati e non è possibile disinstallarli (o non si vuole disinstallarli), è possibile scaricare manualmente lo script usando la scheda **download manuale** nel collegamento per il download dello script. Lo script viene scaricato come file nupkg non elaborato. Per installare lo script da questo file nupkg, vedere [download manuale del pacchetto](/powershell/scripting/gallery/how-to/working-with-packages/manual-download).

Per eseguire lo script:

1. Usare `Connect-AzAccount` per connettersi ad Azure.

1. Usare `Import-Module Az` per importare i moduli AZ.

1. Esaminare i parametri obbligatori:

   * **oldRgName: [String]: required** : questo è il gruppo di risorse per la Load Balancer di base esistente che si vuole aggiornare. Per trovare questo valore stringa, passare a portale di Azure, selezionare l'origine Load Balancer di base e fare clic sulla **Panoramica** per il servizio di bilanciamento del carico. Il gruppo di risorse si trova in questa pagina.
   * **oldLBName: [String]: required** . si tratta del nome del servizio di bilanciamento di base esistente che si vuole aggiornare. 
   * **newrgName: [stringa]: obbligatorio** : gruppo di risorse in cui verrà creata la Load Balancer standard. Può trattarsi di un nuovo gruppo di risorse o di uno esistente. Se si seleziona un gruppo di risorse esistente, si noti che il nome del Load Balancer deve essere univoco all'interno del gruppo di risorse. 
   * **newLocation: [String]: obbligatorio** : posizione in cui verrà creata la Load Balancer standard. È consigliabile ereditare la stessa posizione del Load Balancer di base scelto per la Load Balancer Standard per una migliore associazione con altre risorse esistenti.
   * **newLBName: [String]: required** : nome del Load Balancer standard da creare.
1. Eseguire lo script usando i parametri appropriati. Il completamento può richiedere da cinque a sette minuti.

    **Esempio**

   ```azurepowershell
   AzurePublicLBUpgrade.ps1 -oldRgName "test_publicUpgrade_rg" -oldLBName "LBForPublic" -newrgName "test_userInput3_rg" -newlocation "centralus" -newLbName "LBForUpgrade"
   ```

### <a name="add-vms-to-backend-pools-of-standard-load-balancer"></a>Aggiungere VM ai pool back-end di Load Balancer Standard

Verificare prima di tutto che lo script abbia creato un nuovo Load Balancer pubblico standard con la configurazione esatta migrata dal Load Balancer pubblico di base. È possibile verificarlo dalla portale di Azure.

Assicurarsi di inviare una piccola quantità di traffico attraverso la Load Balancer Standard come test manuale.
  
Di seguito sono riportati alcuni scenari in cui è possibile configurare le macchine virtuali per i pool back-end del Load Balancer pubblico standard appena creato e i suggerimenti per ognuno di essi:

* Lo **stato di trasferimento di macchine virtuali esistenti dai pool back-end di Load Balancer pubblici di base precedenti ai pool back-end di Load Balancer pubblici appena creati**.
    1. Per eseguire le attività di questo avvio rapido, accedere al [portale di Azure](https://portal.azure.com).
 
    1. Selezionare **tutte le risorse** nel menu a sinistra e quindi selezionare il **Load Balancer standard appena creato** dall'elenco di risorse.
   
    1. In **Impostazioni** selezionare **Pool back-end**.
   
    1. Selezionare il pool back-end che corrisponde al pool back-end del Load Balancer di base, selezionare il valore seguente: 
      - **Macchina virtuale**: elenco a discesa e selezionare le macchine virtuali dal pool back-end corrispondente del Load Balancer di base.
    1. Selezionare **Salva**.
    >[!NOTE]
    >Per le macchine virtuali con indirizzi IP pubblici, è necessario creare prima di tutto indirizzi IP standard in cui lo stesso indirizzo IP non è garantito. Dissociare le VM dagli indirizzi IP di base e associarle agli indirizzi IP standard appena creati. Sarà quindi possibile seguire le istruzioni per aggiungere macchine virtuali nel pool back-end di Load Balancer Standard. 

* **Creazione di nuove macchine virtuali da aggiungere ai pool back-end del Load Balancer pubblico standard appena creato**.
    * Altre istruzioni su come creare una macchina virtuale e associarla a Load Balancer Standard sono disponibili [qui](https://docs.microsoft.com/azure/load-balancer/quickstart-load-balancer-standard-public-portal#create-virtual-machines).

### <a name="create-an-outbound-rule-for-outbound-connection"></a>Creare una regola in uscita per la connessione in uscita

Seguire le [istruzioni](https://docs.microsoft.com/azure/load-balancer/configure-load-balancer-outbound-portal#create-outbound-rule-configuration) per creare una regola in uscita, in modo che sia possibile
* Definire la NAT in uscita da zero.
* Ridimensionare e ottimizzare il comportamento del NAT in uscita esistente.

## <a name="common-questions"></a>Domande frequenti

### <a name="are-there-any-limitations-with-the-azure-powershell-script-to-migrate-the-configuration-from-v1-to-v2"></a>Esistono limitazioni allo script Azure PowerShell per eseguire la migrazione della configurazione dalla versione V1 alla versione V2?

Sì. Vedere [avvertenze/limitazioni](#caveatslimitations).

### <a name="does-the-azure-powershell-script-also-switch-over-the-traffic-from-my-basic-load-balancer-to-the-newly-created-standard-load-balancer"></a>Lo script di Azure PowerShell passa anche il traffico dal Load Balancer di base al Load Balancer Standard appena creato?

No. Lo script Azure PowerShell esegue solo la migrazione della configurazione. La migrazione effettiva del traffico è responsabilità dell'utente e del controllo.

### <a name="i-ran-into-some-issues-with-using-this-script-how-can-i-get-help"></a>Si sono verificati alcuni problemi con l'uso di questo script. Come è possibile ottenere assistenza?
  
È possibile inviare un messaggio di slbupgradesupport@microsoft.composta elettronica a, aprire un caso di supporto con il supporto tecnico di Azure o eseguire entrambe le operazioni.

## <a name="next-steps"></a>Passaggi successivi

[Informazioni sulle Load Balancer Standard](load-balancer-overview.md)
