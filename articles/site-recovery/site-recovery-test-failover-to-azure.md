---
title: Failover di test in Azure in Site Recovery | Documentazione Microsoft
description: Azure Site Recovery coordina la replica, il failover e il ripristino di macchine virtuali e server fisici. Informazioni sul failover in Azure o in un centro dati secondario.
services: site-recovery
documentationcenter: 
author: prateek9us
manager: gauravd
editor: 
ms.assetid: 44813a48-c680-4581-a92e-cecc57cc3b1e
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 1/09/2017
ms.author: pratshar
translationtype: Human Translation
ms.sourcegitcommit: f430d81889ecf7f3210358556b99d7d88b0e7a76
ms.openlocfilehash: 8d848385863aeea43dbad3991e7776e399ac3994


---
# <a name="test--failover-to-azure-in-site-recovery"></a>Failover di test in Azure in Site Recovery

Questo articolo contiene informazioni e istruzioni per eseguire un failover di test o un'esercitazione sul ripristino di emergenza di macchine virtuali e server fisici protetti con Site Recovery, usando Azure come sito di ripristino. 

Per inviare commenti o domande, è possibile usare la parte inferiore di questo articolo oppure il [forum sui Servizi di ripristino di Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

Il failover di test viene eseguito per convalidare la strategia di replica o eseguire un'esercitazione sul ripristino di emergenza senza perdita di dati o tempi di inattività. Un failover di test non ha alcun impatto sulla replica in corso o sull'ambiente di produzione. Il failover di test può essere eseguito su una macchina virtuale o un [piano di ripristino](site-recovery-create-recovery-plans.md). Quando si attiva un failover di test, è necessario specificare la rete a cui si connetteranno le macchine virtuali di test. Dopo aver attivato un failover di test, è possibile tenere traccia dello stato di avanzamento nella pagina **Processi**.  


## <a name="supported-scenarios"></a>Scenari supportati
Il failover di test è supportato in tutti gli scenari di distribuzione tranne la [distribuzione legacy da sito VMware in Azure](site-recovery-vmware-to-azure-classic-legacy.md). Il failover di test, inoltre, non è supportato quando la macchina virtuale è stata sottoposta a failover in Azure.  


## <a name="run-a-test-failover"></a>Eseguire un failover di test
Questa procedura descrive come eseguire un failover di test per un piano di ripristino. In alternativa, è anche possibile eseguire il failover di test per un singolo computer usando l'opzione appropriata su di esso. 

1. Selezionare **Piani di ripristino** > *nome_pianodiripristino*. Fare clic su **Failover di test**.
1. Selezionare un **punto di ripristino** in cui eseguire il failover. È possibile usare una delle opzioni seguenti.
    1.  **Latest processed** (Punto di ripristino elaborato più recente): con questa opzione viene eseguito il failover di tutte le macchine virtuali del piano di ripristino nell'ultimo punto di ripristino elaborato dal servizio Site Recovery. Quando si esegue il failover di test di una macchina virtuale, viene visualizzato anche il timestamp del punto di ripristino elaborato più recente. Se si esegue il failover di un piano di ripristino, per ottenere tali informazioni è possibile passare alla singola macchina virtuale ed esaminare il riquadro **Latest Recovery Points** (Punti di ripristino più recenti). Dato che non viene impiegato tempo per elaborare dati non elaborati, questa opzione offre un failover con valore RTO (Recovery Time Objective) basso. 
    1.    **Punto di ripristino coerente con l'app più recente**: con questa opzione viene eseguito il failover di tutte le macchine virtuali del piano di ripristino nell'ultimo punto di ripristino coerente con l'applicazione elaborato dal servizio Site Recovery. Quando si esegue il failover di test di una macchina virtuale, viene visualizzato anche il timestamp del punto di ripristino coerente con l'app più recente. Se si esegue il failover di un piano di ripristino, per ottenere tali informazioni è possibile passare alla singola macchina virtuale ed esaminare il riquadro **Latest Recovery Points** (Punti di ripristino più recenti). 
    1.    **Più recente**: con questa opzione vengono prima elaborati tutti i dati inviati al servizio Site Recovery per creare un punto di ripristino per ogni macchina virtuale e quindi viene eseguito il failover in tale punto di ripristino. Questa opzione offre il valore RPO (Recovery Point Objective) più basso, perché la macchina virtuale creata dopo il failover avrà tutti i dati che sono stati replicati nel servizio Site Recovery all'attivazione del failover. 
    1.  **Personalizzato**: in caso di failover di test di una macchina virtuale, è possibile usare questa opzione per eseguire il failover in un determinato punto di ripristino.
1. Selezionare una **rete virtuale di Azure**: specificare una rete virtuale di Azure in cui verranno create le macchine virtuali di test. Site Recovery tenta di creare le macchine virtuali di test in una subnet con lo stesso nome e con lo stesso IP specificati nelle impostazioni **Calcolo e rete** della macchina virtuale. Se nella rete virtuale di Azure specificata per il failover di test non è disponibile una subnet con lo stesso nome, la macchina virtuale di test verrà creata nella prima subnet in ordine alfabetico. Se nella subnet non è disponibile lo stesso IP, la macchina virtuale riceverà un altro indirizzo IP disponibile nella subnet. Per altri dettagli, vedere [questa sezione](#creating-a-network-for-test-failover).
1. Se si esegue il failover in Azure ed è abilitata la crittografia dei dati, in **Chiave di crittografia** selezionare il certificato rilasciato quando è stata abilitata la crittografia dei dati durante l'installazione del provider. Se nella macchina virtuale non è stata abilitata la crittografia, è possibile ignorare questo passaggio.
1. Tenere traccia dello stato di avanzamento del failover nella scheda **Processi** . Nel portale di Azure dovrebbe anche essere possibile vedere la macchina di replica di test.
1. Per avviare una connessione RDP nella macchina virtuale, è necessario [aggiungere un indirizzo IP pubblico](site-recovery-monitoring-and-troubleshooting.md#adding-a-public-ip-on-a-resource-manager-virtual-machine) nell'interfaccia di rete della macchina virtuale sottoposta a failover. In caso di failover in una macchina virtuale della versione classica, è necessario [aggiungere un endpoint](../virtual-machines/virtual-machines-windows-classic-setup-endpoints.md) sulla porta 3389.
1. Al termine, fare clic su **Cleanup test failover** (Pulizia failover di test) nel piano di ripristino. Fare clic su **Note** per registrare e salvare eventuali osservazioni associate al failover di test. Verranno eliminate le macchine virtuali create durante il failover di test. 


> [!TIP]
> Site Recovery tenta di creare le macchine virtuali di test in una subnet con lo stesso nome e con lo stesso IP specificati nelle impostazioni **Calcolo e rete** della macchina virtuale. Se nella rete virtuale di Azure specificata per il failover di test non è disponibile una subnet con lo stesso nome, la macchina virtuale di test verrà creata nella prima subnet in ordine alfabetico. Se nella subnet non è disponibile lo stesso IP, la macchina virtuale riceverà un altro indirizzo IP disponibile nella subnet. 
>
> 


## <a name="creating-a-network-for-test-failover"></a>Creazione di una rete per il failover di test 
Durante un failover di test, è consigliabile scegliere una rete isolata dalla rete del sito di ripristino di produzione specificata nelle impostazioni **Calcolo e rete** della macchina virtuale. Quando si crea una rete virtuale di Azure, per impostazione predefinita è isolata dalle altre reti. La rete dovrà simulare la rete di produzione:

1. La rete di test dovrà avere lo stesso numero di subnet della rete di produzione, con lo stesso nome delle subnet nella rete di produzione.
1. La rete di test dovrà usare lo stesso intervallo IP della rete di produzione.
1. Aggiornare il DNS della rete di test con l'IP specificato come IP di destinazione per la macchina virtuale DNS nelle impostazioni **Calcolo e rete**. Per altri dettagli, vedere la sezione [Considerazioni sul failover di test per Active Directory](site-recovery-active-directory.md#test-failover-considerations) . 


## <a name="test-failover-to-a-production-network-on-recovery-site"></a>Failover di test in una rete di produzione nel sito di ripristino 
Durante un failover di test, è consigliabile scegliere una rete diversa rispetto alla rete del sito di ripristino di produzione specificata nelle impostazioni **Calcolo e rete** della macchina virtuale. Se tuttavia si vuole comunque convalidare la connettività di rete end-to-end in una macchina virtuale sottoposta a failover, tenere presente quanto segue:

1. Verificare che la macchina virtuale primaria sia arrestata quando si esegue il failover di test. In caso contrario, due macchine virtuali con la stessa identità saranno in esecuzione contemporaneamente nella stessa rete e ciò può causare conseguenze indesiderate. 
1. Tutte le modifiche apportate alle macchine virtuali del failover di test andranno perse con la pulizia di tali macchine virtuali. Le modifiche non verranno replicate nella macchina virtuale primaria.
1. Questa modalità di esecuzione dei test determina un tempo di inattività dell'applicazione di produzione. È consigliabile chiedere agli utenti di non usare l'applicazione mentre è in corso l'esercitazione sul ripristino di emergenza.  



## <a name="prepare-active-directory-and-dns"></a>Preparare Active Directory e DNS
Per eseguire un failover di test per testare l'applicazione, è necessaria una copia dell'ambiente Active Directory di produzione nell'ambiente di test. Per altri dettagli, vedere la sezione [Considerazioni sul failover di test per Active Directory](site-recovery-active-directory.md#test-failover-considerations) . 

## <a name="next-steps"></a>Passaggi successivi
Dopo l'esito positivo di un failover di test, si può provare a eseguire un [failover](site-recovery-failover.md).




<!--HONumber=Jan17_HO4-->


