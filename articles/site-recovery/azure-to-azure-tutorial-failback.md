---
title: Eseguire il failback di VM IaaS di Azure replicate in un'area di Azure secondaria per il ripristino di emergenza con il servizio Azure Site Recovery.
description: Informazioni su come eseguire il failback di VM di Azure con il servizio Azure Site Recovery.
services: site-recovery
author: sideeksh
manager: rochakm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 03/07/2019
ms.author: sideeksh
ms.custom: mvc
ms.openlocfilehash: d8721f313907f0e0519dca52f5565853f1c44110
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58089702"
---
# <a name="fail-back-azure-vms-between-azure-regions"></a>Eseguire il failback di VM di Azure tra aree di Azure

Il servizio [Azure Site Recovery](site-recovery-overview.md) contribuisce all'attuazione della strategia di ripristino di emergenza gestendo e orchestrando la replica, il failover e il failback di computer locali e macchine virtuali (VM) di Azure.

Questa esercitazione illustra come eseguire il failback di una singola VM di Azure. Dopo aver eseguito il failover, si esegue il failback nell'area primaria, non appena torna disponibile. In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> 
> * Eseguire il failback della macchina virtuale secondaria
> * Riproteggere la VM primaria nell'area secondaria
> 
> [!NOTE]
> 
> Questa esercitazione illustra all'utente la procedura di failover in un'area di destinazione e di failback con una personalizzazione minima. Per altre informazioni sui vari aspetti associati al failover, come le considerazioni relative alla rete, l'automazione o la risoluzione dei problemi, vedere i documenti sulle procedure per le VM di Azure.

## <a name="prerequisites"></a>Prerequisiti

> * Verificare che lo stato della VM sia Commit del failover eseguito, che l'area primaria sia disponibile e che sia possibile crearvi nuove risorse e accedere a tali risorse.
> * Verificare che la riprotezione sia abilitata.

## <a name="fail-back-to-the-primary-region"></a>Eseguire il failback nell'area primaria

Dopo la riprotezione delle VM, è possibile eseguire il failback nell'area primaria quando si preferisce.

1. Passare all'insieme di credenziali di Servizi di ripristino. Fare clic su Elementi replicati e selezionare la VM che è stata riprotetta.

2. Verrà visualizzato quanto segue. Si noti che è simile al pannello per il failover di test e il failover dall'area primaria.
![Failback nell'area primaria](./media/site-recovery-azure-to-azure-failback/azure-to-azure-failback.png)

3. Fare clic su Failover di test per rieseguire un failover di test nell'area primaria. Scegliere il punto di ripristino e la rete virtuale per il failover di test e selezionare OK. Verrà visualizzata la VM di test creata nell'area primaria, che è accessibile e può essere esaminata.

4. Quando si è soddisfatti del failover di test, è possibile fare clic su Pulisci failover di test per eseguire la pulizia delle risorse create nell'area di origine per il failover di test.

5. In Elementi replicati selezionare la VM di cui si vuole eseguire il failover e quindi Failover.

6. In Failover selezionare un punto di ripristino in cui eseguire il failover. È possibile usare una delle opzioni seguenti.
    1. Più recente (impostazione predefinita): questa opzione determina l'elaborazione di tutti i dati nel servizio Site Recovery e offre il valore RPO (Recovery Point Objective) più basso.
    2. Elaborato più recente: questa opzione ripristina la macchina virtuale all'ultimo punto di ripristino che è stato elaborato dal servizio Site Recovery.
    3. Personalizzato: usare questa opzione per eseguire il failover in un determinato punto di ripristino. È utile per eseguire un failover di test.

7. Selezionare Arrestare la macchina virtuale prima di iniziare il failover se si vuole che Site Recovery tenti di arrestare le macchine virtuali di origine prima di attivare il failover. Il failover continua anche se l'arresto ha esito negativo. Si noti che Site Recovery non esegue la pulizia dell'origine dopo il failover.

8. Seguire lo stato di avanzamento del failover nella pagina Processi.

9. Dopo il failover, convalidare la macchina virtuale eseguendo l'accesso ad essa. Per passare a un altro punto di ripristino per la macchina virtuale, è possibile usare l'opzione Modifica punto di ripristino.

10. Quando la macchina virtuale sottoposta a failover è pronta, è possibile eseguire il commit del failover. Questa operazione elimina tutti i punti di ripristino disponibili con il servizio. L'opzione Modifica punto di ripristino non è più disponibile.

![VM nell'area primaria e in quella secondaria](./media/site-recovery-azure-to-azure-failback/azure-to-azure-failback-vm-view.png)

Se viene visualizzata la schermata precedente, è stato eseguito il failover della macchina virtuale "ContosoWin2016" da Stati Uniti centrali a Stati Uniti orientali e il failback da Stati Uniti orientali a Stati Uniti centrali.

Si noti che le VM del ripristino di emergenza rimarranno nello stato di arresto deallocato. Si tratta del comportamento previsto dalla progettazione, dato che Azure Site Recovery salva le informazioni della macchina virtuale che potrebbero essere utili in caso di failover dall'area primaria all'area secondaria in un secondo momento. Non vengono effettuati addebiti per le macchine virtuali deallocate ed è quindi consigliabile mantenere questo stato.

> [!NOTE]
> Per altri dettagli sul flusso di lavoro di riprotezione e su ciò che accade durante la riprotezione, vedere la [sezione delle procedure](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-how-to-reprotect#what-happens-during-reprotection).
