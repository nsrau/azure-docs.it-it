---
title: Come eseguire la migrazione da gruppi di affinità a una rete virtuale (VNet) regionale
description: Informazioni su come eseguire la migrazione da gruppi di affinità a reti virtuali regionali
services: virtual-network
documentationcenter: na
author: jimdial
manager: carmonm
editor: tysonn

ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2016
ms.author: jdial

---
# Come eseguire la migrazione da gruppi di affinità a una rete virtuale (VNet) regionale
È possibile usare un gruppo di affinità per assicurarsi che le risorse create all'interno dello stesso gruppo di affinità vengano ospitate fisicamente da server vicini l'uno all'altro, consentendo a tali risorse di comunicare più rapidamente. In passato i gruppi di affinità erano un requisito per la creazione delle reti virtuali (o VNet) In quel momento, il servizio di gestione di rete che gestiva le VNets poteva operare solo all'interno di un set di server fisici chiamato unità di scala. I miglioramenti apportati all'architettura hanno esteso l'ambito di gestione delle reti a un’area.

A seguito di tali miglioramenti, i gruppi di affinità non sono più consigliati o necessari per le reti virtuali. L'uso dei gruppi di affinità per le VNet è stato sostituito dalle regioni. Le VNet associate alle regioni sono chiamate VNet regionali.

L'uso dei gruppi di affinità inoltre non è consigliabile in generale. Oltre che come requisito per le reti virtuali, essi erano importanti anche per garantire che le risorse, ad esempio di calcolo o archiviazione, fossero posizionate l'una vicino all'altra. Con l'architettura di rete corrente di Azure, tali requisiti di posizionamento non sono più necessari. Per informazioni sui pochi casi specifici in cui è ancora opportuno usare un gruppo di affinità, vedere l'articolo relativo ai [gruppi di affinità e macchine virtuali](#Affinity-groups-and-VMs).

## Creazione e migrazione a reti virtuali regionali
Quando si creano nuove reti virtuali, usare *Regione*, disponibile come opzione nel portale di gestione. Si noti che nel file di configurazione della rete corrisponde a *Location*.

> [!IMPORTANT]
> Benché sia ancora tecnicamente possibile creare una rete virtuale associata a un gruppo di affinità, non esistono motivi vincolanti per procedere in questo modo. Molte nuove funzionalità, come ad esempio i gruppi di sicurezza di rete, sono disponibili soltanto quando si usa una VNet regionale e non per le reti virtuali associate a gruppi di affinità.
> 
> 

### Informazioni sulle VNet attualmente associate a gruppi di affinità
Le VNet attualmente associate a gruppi di affinità saranno abilitate per la migrazione a reti virtuali regionali nel prossimo futuro. Per eseguire la migrazione a una rete virtuale di area, attenersi alla seguente procedura:

1. Esportare il file di configurazione della rete. È possibile usare PowerShell o il portale di gestione. Per istruzioni sull'uso del portale di gestione, vedere [Configurare una rete virtuale usando un file di configurazione di rete](virtual-networks-using-network-configuration-file.md).
2. Modificare il file di configurazione della rete sostituendo i valori precedenti con quelli nuovi.
   
   > [!NOTE]
   > **Location** corrisponde alla regione specificata per il gruppo di affinità associato alla VNet. Ad esempio, se la VNet è associata a un gruppo di affinità posizionato negli Stati Uniti occidentali, quando si esegue la migrazione, Location deve puntare a West US.
   > 
   > 
   
    Modificare le righe seguenti nel file di configurazione della rete, sostituendo i valori esistenti con i propri:
   
    **Valore precedente:** \<VirtualNetworkSitename="VNetUSWest" AffinityGroup="VNetDemoAG"\>
   
    **Nuovo valore:** \<VirtualNetworkSitename="VNetUSWest" Location="West US"\>
3. Salvare le modifiche e [importare](virtual-networks-using-network-configuration-file.md) la configurazione di rete in Azure.

> [!NOTE]
> Questa migrazione NON provoca alcun tempo di inattività per i servizi.
> 
> 

## Gruppi di affinità e macchine virtuali
Come spiegato in precedenza, i gruppi di affinità non sono più consigliati in generale per le macchine virtuali. È opportuno usare un gruppo di affinità solo quando un set di macchine virtuali deve avere la latenza di rete assoluta più bassa tra le macchine virtuali. Inserendo le macchine virtuali in un gruppo di affinità, queste verranno posizionate tutte nello stesso cluster di elaborazione o unità di scala.

È importante notare che l'uso di un gruppo di affinità può avere due conseguenze negative:

* Il set di dimensioni delle macchine virtuali sarà limitato al set di dimensioni offerto dall'unità di scala di elaborazione.
* Esiste una maggiore probabilità di non poter allocare una nuova macchina virtuale. Ciò accade quando si esaurisce la capacità dell'unità di scala specifica per il gruppo di affinità.

### Come procedere se si dispone di una macchina virtuale in un gruppo di affinità
Le macchine virtuali attualmente incluse in un gruppo di affinità non devono essere rimosse da tale gruppo.

Quando si esegue la distribuzione, una macchina virtuale viene distribuita in una singola unità di scala. I gruppi di affinità possono limitare il set di dimensioni di macchine virtuali disponibili per una nuova distribuzione, ma qualsiasi macchina virtuale esistente che venga distribuita è già limitata al set di dimensioni disponibile nell'unità di scala in cui avviene la distribuzione. Per questo motivo, la rimozione di una macchina virtuale dal gruppo di affinità non avrà alcun effetto.

<!---HONumber=AcomDC_0810_2016-->