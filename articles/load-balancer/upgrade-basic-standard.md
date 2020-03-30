---
title: Eseguire l'aggiornamento dal pubblico di base allo standard pubblico - Azure Load BalancerUpgrade from Basic Public to Standard Public - Azure Load Balancer
description: Questo articolo illustra come aggiornare Azure Public Load Balancer dallo SKU di base allo SKU Standard
services: load-balancer
author: irenehua
ms.service: load-balancer
ms.topic: article
ms.date: 01/23/2020
ms.author: irenehua
ms.openlocfilehash: a4c8b029b199915cce9a417430e67675a03d327f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77659952"
---
# <a name="upgrade-azure-public-load-balancer"></a>Aggiornare il servizio di bilanciamento del carico pubblico di AzureUpgrade Azure Public Load Balancer
[Azure Standard Load Balancer](load-balancer-overview.md) offre un set completo di funzionalità e disponibilità elevata tramite la ridondanza delle zone. Per altre informazioni sullo SKU di Load Balancer, vedere la tabella di [confronto](https://docs.microsoft.com/azure/load-balancer/concepts-limitations#skus).

Ci sono tre fasi in un aggiornamento:

1. Eseguire la migrazione della configurazione
2. Aggiungere macchine virtuali ai pool back-end di Load Balancer StandardAdd VMs to backend pools of Standard Load Balancer
3. Creare una regola in uscita nel servizio di bilanciamento del carico per la connessione in uscitaCreate an outbound rule on the load balancer for outbound connection

In questo articolo viene illustrata la migrazione della configurazione. L'aggiunta di macchine virtuali ai pool back-end può variare a seconda dell'ambiente specifico. Tuttavia, [vengono fornite](#add-vms-to-backend-pools-of-standard-load-balancer)alcune raccomandazioni generali di alto livello .

## <a name="upgrade-overview"></a>Panoramica dell'aggiornamento

È disponibile uno script di Azure PowerShell che esegue le operazioni seguenti:An Azure PowerShell script is available that does the following:

* Crea un servizio di bilanciamento del carico SKU Standard nel gruppo di risorse e nel percorso specificato dall'utente.
* Copia senza problemi le configurazioni di Basic SKU Load Balancer nel nuovo servizio di bilanciamento del carico Standard.

### <a name="caveatslimitations"></a>Avvertenze - Limitazioni

* Lo script supporta solo l'aggiornamento di Public Load Balancer.Script only supports Public Load Balancer upgrade. Per l'aggiornamento di Load Balancer di base interno, creare un servizio di bilanciamento del carico interno standard se la connettività in uscita non è desiderata e creare un servizio di bilanciamento del carico interno standard e un servizio di bilanciamento del carico pubblico standard se è necessaria la connettività in uscita.
* Il servizio di bilanciamento del carico standard ha un nuovo indirizzo pubblico. È impossibile spostare senza problemi gli indirizzi IP associati a Basic Load Balancer esistenti in Load Balancer standard poiché hanno SKU diversi.
* Se il servizio di bilanciamento del carico Standard viene creato in un'area diversa, non sarà possibile associare le macchine virtuali esistenti nell'area precedente al servizio di bilanciamento del carico Standard appena creato. Per aggirare questa limitazione, assicurarsi di creare una nuova macchina virtuale nella nuova area.
* Se il servizio di bilanciamento del carico non dispone di alcuna configurazione IP front-end o pool di back-end, è probabile che si ripresenti un errore durante l'esecuzione dello script. Si prega di assicurarsi che non siano vuoti.

## <a name="download-the-script"></a>Scarica lo script

Scaricare lo script di migrazione da [PowerShell Gallery](https://www.powershellgallery.com/packages/AzurePublicLBUpgrade/1.0).
## <a name="use-the-script"></a>Utilizzare lo script

Sono disponibili due opzioni a seconda dell'installazione e delle preferenze dell'ambiente PowerShell locale:There are two options for you depending on your local PowerShell environment setup and preferences:

* Se i moduli di Azure Az non sono installati o non è consigliabile disinstallare i `Install-Script` moduli di Azure Az, l'opzione migliore consiste nell'usare l'opzione per eseguire lo script.
* Se è necessario mantenere i moduli di Azure Az, la soluzione migliore consiste nel scaricare lo script ed eseguirlo direttamente.

Per determinare se sono installati i `Get-InstalledModule -Name az`moduli di Azure Az, eseguire . Se non vedi alcun modulo Az installato, puoi `Install-Script` utilizzare il metodo .

### <a name="install-using-the-install-script-method"></a>Eseguire l'installazione utilizzando il metodo Install-Script

Per usare questa opzione, non è necessario che nel computer siano installati i moduli di Azure Az.To use this option, you must not have the Azure Az modules installed on your computer. Se sono installati, il comando seguente visualizza un errore. È possibile disinstallare i moduli di Azure Az oppure usare l'altra opzione per scaricare lo script manualmente ed eseguirlo.
  
Eseguire lo script con il comando seguente:

`Install-Script -Name AzurePublicLBUpgrade`

Questo comando installa anche i moduli Az necessari.  

### <a name="install-using-the-script-directly"></a>Eseguire l'installazione utilizzando lo script direttamente

Se sono installati alcuni moduli di Azure Az e non è possibile disinstallarli o non si desidera disinstallarli, è possibile scaricare manualmente lo script usando la scheda **Download manuale** nel collegamento per il download dello script. Lo script viene scaricato come file nupkg non elaborato. Per installare lo script da questo file nupkg, vedere [Download manuale del pacchetto](/powershell/scripting/gallery/how-to/working-with-packages/manual-download).

Per eseguire lo script:

1. Usare `Connect-AzAccount` per connettersi ad Azure.Use to connect to Azure.

1. Utilizzare `Import-Module Az` per importare i moduli Az.

1. Esaminare i parametri richiesti:

   * **oldRgName: [String]: Obbligatorio:** è il gruppo di risorse per il servizio di bilanciamento del carico di base esistente che si desidera aggiornare.oldRgName: [String]: Required – This is the resource group for your existing Basic Load Balancer you want to upgrade. Per trovare questo valore stringa, passare al portale di Azure, selezionare l'origine di Basic Load Balancer e fare clic su **Panoramica** per il servizio di bilanciamento del carico. Il gruppo di risorse si trova in tale pagina.
   * **oldLBName: [String]: Obbligatorio** – Questo è il nome del sistema di bilanciamento di base esistente che si desidera aggiornare. 
   * **newrgName: [Stringa]: Obbligatorio:** è il gruppo di risorse in cui verrà creato il servizio di bilanciamento del carico standard.newrgName: [String]: Required – This is the resource group in which the Standard Load Balancer will be created. Può essere un nuovo gruppo di risorse o uno esistente. Se si seleziona un gruppo di risorse esistente, si noti che il nome del servizio di bilanciamento del carico deve essere univoco all'interno del gruppo di risorse. 
   * **newlocation: [Stringa]: Obbligatorio** – Questa è la posizione in cui verrà creato il servizio di bilanciamento del carico standard.newlocation: [String]: Required – This is the location in which the Standard Load Balancer will be created. È consigliabile ereditare la stessa posizione del servizio di bilanciamento del carico di base scelto in Load Balancer standard per una migliore associazione con altre risorse esistenti.
   * **newLBName: [String]: Obbligatorio** – Questo è il nome per il servizio di bilanciamento del carico standard da creare.
1. Eseguire lo script utilizzando i parametri appropriati. La finitura potrebbe richiedere da cinque a sette minuti.

    **Esempio**

   ```azurepowershell
   AzurePublicLBUpgrade.ps1 -oldRgName "test_publicUpgrade_rg" -oldLBName "LBForPublic" -newrgName "test_userInput3_rg" -newlocation "centralus" -newLbName "LBForUpgrade"
   ```

### <a name="add-vms-to-backend-pools-of-standard-load-balancer"></a>Aggiungere macchine virtuali ai pool back-end di Load Balancer StandardAdd VMs to backend pools of Standard Load Balancer

Verificare innanzitutto che lo script abbia creato correttamente un nuovo servizio di bilanciamento del carico pubblico standard con la configurazione esatta migrata dal servizio di bilanciamento del carico pubblico di base. È possibile verificarlo dal portale di Azure.You can verify this from the Azure portal.

Assicurarsi di inviare una piccola quantità di traffico tramite il servizio di bilanciamento del carico Standard come test manuale.
  
Ecco alcuni scenari di come aggiungere macchine virtuali ai pool back-end del servizio di bilanciamento del carico pubblico standard appena creato e i consigli per ognuno di essi:

* **Spostamento di macchine virtuali esistenti da pool back-end di Basic Public Load Balancer precedenti a pool back-end di Standard Public Load Balancer appena creati.**
    1. Per eseguire le attività di questo avvio rapido, accedere al [portale di Azure](https://portal.azure.com).
 
    1. Selezionare **Tutte le risorse** nel menu a sinistra, quindi selezionare il servizio di **bilanciamento del carico Standard appena creato** dall'elenco delle risorse.
   
    1. In **Impostazioni** selezionare **Pool back-end**.
   
    1. Selezionare il pool back-end che corrisponde al pool back-end di Basic Load Balancer, selezionare il valore seguente: 
      - **Macchina virtuale:** è possibile eseguire l'elenco a discesa e selezionare le macchine virtuali dal pool back-end corrispondente del servizio di bilanciamento del carico di base.
    1. Selezionare **Salva**.
    >[!NOTE]
    >Per le macchine virtuali con indirizzi IP pubblici, è necessario creare prima indirizzi IP standard in cui non è garantito lo stesso indirizzo IP. Disassociare le macchine virtuali dagli indirizzi IP di base e associarle agli indirizzi IP Standard appena creati. Quindi, sarà possibile seguire le istruzioni per aggiungere macchine virtuali nel pool back-end di Load Balancer Standard.Then, you will be able to follow instructions to add VMs into backend pool of Standard Load Balancer. 

* **Creazione di nuove macchine virtuali da aggiungere ai pool back-end del nuovo servizio di bilanciamento del carico pubblico Standard appena creato.**
    * Ulteriori istruzioni su come creare una macchina virtuale e associarla a Load Balancer Standard sono disponibili [qui](https://docs.microsoft.com/azure/load-balancer/quickstart-load-balancer-standard-public-portal#create-virtual-machines).

### <a name="create-an-outbound-rule-for-outbound-connection"></a>Creare una regola in uscita per la connessione in uscitaCreate an outbound rule for outbound connection

Seguire le [istruzioni](https://docs.microsoft.com/azure/load-balancer/configure-load-balancer-outbound-portal#create-outbound-rule-configuration) per creare una regola in uscita in modo da
* Definire NAT in uscita da zero.
* Ridimensionare e ottimizzare il comportamento del NAT in uscita esistente.

## <a name="common-questions"></a>Domande frequenti

### <a name="are-there-any-limitations-with-the-azure-powershell-script-to-migrate-the-configuration-from-v1-to-v2"></a>Esistono limitazioni con lo script di Azure PowerShell per eseguire la migrazione della configurazione dalla versione 1 alla versione 2?

Sì. Vedere [Avvertenze/Limitazioni](#caveatslimitations).

### <a name="does-the-azure-powershell-script-also-switch-over-the-traffic-from-my-basic-load-balancer-to-the-newly-created-standard-load-balancer"></a>Lo script di Azure PowerShell passa anche il traffico dal servizio di bilanciamento del carico di base al servizio di bilanciamento del carico standard appena creato?

No. Lo script di Azure PowerShell esegue solo la migrazione della configurazione. La migrazione effettiva del traffico è responsabilità dell'utente e del controllo dell'utente.

### <a name="i-ran-into-some-issues-with-using-this-script-how-can-i-get-help"></a>Mi sono imbattuto in alcuni problemi con l'utilizzo di questo script. Come posso ottenere aiuto?
  
È possibile inviare slbupgradesupport@microsoft.comun messaggio di posta elettronica a , aprire un caso di supporto con il supporto di Azure o eseguire entrambe le operazione.

## <a name="next-steps"></a>Passaggi successivi

[Informazioni su Load Balancer Standard](load-balancer-overview.md)
