---
title: Spostare macchine virtuali IaaS di Azure in un'altra area di Azure usando il servizio Azure Site Recovery | Microsoft Docs
description: Usare Site Recovery per lo spostamento di macchine virtuali IaaS di Azure da un'area di Azure a un'altra.
services: site-recovery
author: rajani-janaki-ram
ms.service: site-recovery
ms.topic: tutorial
ms.date: 01/28/2019
ms.author: rajanaki
ms.custom: MVC
ms.openlocfilehash: 0f73e68fd0c01d4323e8675d3fa12f7ca1051cdb
ms.sourcegitcommit: cdf0e37450044f65c33e07aeb6d115819a2bb822
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/01/2019
ms.locfileid: "57192923"
---
# <a name="move-azure-vms-to-another-region"></a>Spostare macchine virtuali di Azure in un'altra area

Può essere opportuno spostare macchine virtuali dell'infrastruttura di Azure come servizio (IaaS) da un'area a un'altra per ottenere migliori livelli di affidabilità, disponibilità, gestione o governance. Questa esercitazione illustra come spostare macchine virtuali in un'altra area usando Azure Site Recovery. Si apprenderà come:

> [!div class="checklist"]
> * Verificare i prerequisiti
> * Preparare le VM di origine
> * Preparare l'area di destinazione
> * Copiare i dati nell'area di destinazione
> * Testare la configurazione
> * Eseguire lo spostamento
> * Rimuovere le risorse dall'area di origine


> [!IMPORTANT]
> Questo articolo descrive come spostare macchine virtuali di Azure da un'area a un'altra *così come sono*. Se l'obiettivo è quello di migliorare la disponibilità dell'infrastruttura spostando macchine virtuali nelle zone di disponibilità, vedere [Spostare macchine virtuali di Azure nelle zone di disponibilità](move-azure-vms-avset-azone.md).

## <a name="prerequisites"></a>Prerequisiti

- Verificare che siano presenti macchine virtuali di Azure nell'area di Azure di origine *da* cui si vuole eseguire lo spostamento.
- Verificare che la [combinazione di area di origine e area di destinazione sia supportata](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-support-matrix#region-support) e scegliere con attenzione l'area di destinazione.
- Assicurarsi di aver compreso i [componenti e l'architettura dello scenario](azure-to-azure-architecture.md).
- Rivedere le [limitazioni e i requisiti del supporto](azure-to-azure-support-matrix.md).
- Verificare le autorizzazioni dell'account. Se è appena stato creato l'account Azure gratuito, si è *amministratori* della propria sottoscrizione. In caso contrario, rivolgersi all'amministratore per ottenere le autorizzazioni necessarie:
  -  Per abilitare la replica per una macchina virtuale e copiare i dati nella destinazione usando Site Recovery, è necessario disporre delle autorizzazioni per creare una macchina virtuale nelle risorse di Azure. Il ruolo predefinito Collaboratore Macchina virtuale ha le autorizzazioni elencate. Con le autorizzazioni, è possibile:
        - Creare una macchina virtuale nel gruppo di risorse selezionato.
        - Creare una macchina virtuale nella rete virtuale selezionata.
        - Scrivere nell'account di archiviazione selezionato.

  - È necessario anche avere le autorizzazioni per la gestione delle operazioni di Site Recovery. Il ruolo Collaboratore di Site Recovery ha tutte le autorizzazioni necessarie per gestire le operazioni di Site Recovery in un insieme di credenziali di Servizi di ripristino di Azure.

## <a name="prepare-the-source-vms"></a>Preparare le VM di origine

1. Controllare che le macchine virtuali di Azure che si prevede di spostare abbiano i certificati radice più recenti. In caso contrario, non sarà possibile abilitare la copia dei dati nell'area di destinazione a causa di vincoli di sicurezza.

    - Per le macchine virtuali Windows, installare gli aggiornamenti di Windows più recenti in modo che nella macchina virtuale siano presenti tutti i certificati radice trusted. In un ambiente non connesso, seguire i processi di aggiornamento di Windows e di aggiornamento dei certificati standard per l'organizzazione.
    - Per le macchine virtuali Linux, seguire le indicazioni fornite dal distributore di Linux per ottenere i certificati radice trusted più recenti e l'elenco di revoche di certificati.
2. Assicurarsi di non usare un proxy di autenticazione per controllare la connettività di rete per le macchine virtuali che si prevede di spostare.
3. Se la macchina virtuale che si vuole spostare non ha accesso a Internet o usa un proxy firewall per controllare l'accesso in uscita, controllare i [requisiti](azure-to-azure-tutorial-enable-replication.md#configure-outbound-network-connectivity).
4. Documentare il layout di rete di origine e tutte le risorse attualmente in uso, tra cui i servizi di bilanciamento del carico, i gruppi di sicurezza di rete e gli indirizzi IP pubblici, per la verifica.

## <a name="prepare-the-target-region"></a>Preparare l'area di destinazione

1. Nella sottoscrizione di Azure verificare se è possibile creare macchine virtuali nell'area di destinazione usata per il ripristino di emergenza. Se necessario, contattare il supporto per abilitare la quota richiesta.

2. Verificare che la sottoscrizione offra risorse sufficienti per supportare le macchine virtuali di origine. Se si usa Site Recovery per copiare i dati nella destinazione, Site Recovery sceglie la stessa dimensione o la dimensione più vicina disponibile per le macchine virtuali di destinazione.

3. Assicurarsi di creare una risorsa di destinazione per ogni componente identificato nel layout di rete di origine. Questo passaggio consente di accertarsi che nell'area di destinazione le macchine virtuali abbiano tutte le caratteristiche e le funzionalità disponibili nell'area di origine.

   Azure Site Recovery individua e crea automaticamente una rete virtuale e un account di archiviazione quando si abilita la replica per la macchina virtuale di origine. È anche possibile creare prima queste risorse e assegnarle alla macchina virtuale come parte del passaggio di abilitazione della replica, ma tutte le altre risorse devono essere create manualmente nell'area di destinazione. Per creare le risorse di rete usate più di frequente in base alla configurazione della macchina virtuale di origine, vedere i documenti seguenti:

   - [Gruppi di sicurezza di rete](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group)
   - [Servizi di bilanciamento del carico](https://docs.microsoft.com/azure/load-balancer/#step-by-step-tutorials)
   - [IP pubblico](https://docs.microsoft.com/azure/load-balancer/#step-by-step-tutorials)
    
   Per gli altri componenti di rete, vedere la [documentazione relativa alle reti di Azure](https://docs.microsoft.com/azure/#pivot=products&panel=network). 

4. Per testare la configurazione prima dello spostamento, [creare manualmente una rete non di produzione](https://docs.microsoft.com/azure/virtual-network/quick-create-portal) nell'area di destinazione. Il test della configurazione crea un'interferenza minima con l'ambiente di produzione ed è consigliabile eseguirlo.
    
## <a name="copy-data-to-the-target-region"></a>Copiare i dati nell'area di destinazione
Nella procedura seguente viene usato Azure Site Recovery per copiare i dati nell'area di destinazione.

### <a name="create-the-vault-in-any-region-except-the-source"></a>Creare l'insieme di credenziali in tutte le aree tranne l'origine

1. Accedere a [portale di Azure](https://portal.azure.com) > **Servizi di ripristino**.
2. Selezionare **Crea una risorsa** > **Strumenti di gestione** > **Backup e Site Recovery (OMS)**.
3. Per **Nome** specificare il nome descrittivo **ContosoVMVault**. Se è disponibile più di una sottoscrizione, selezionare quella appropriata.
4. Creare un gruppo di risorse **ContosoRG**.
5. Specificare un'area di Azure. Per verificare le aree supportate, vedere [Prezzi di Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/).
6. Per gli insiemi di credenziali di Servizi di ripristino, selezionare **Panoramica** > **ContosoVMVault** > **+Replica**.
7. Per **Origine** selezionare **Azure**.
8. Per **Percorso di origine** selezionare l'area di Azure di origine in cui le macchine virtuali sono attualmente in esecuzione.
9. Selezionare il modello di distribuzione Azure Resource Manager. Selezionare quindi la **sottoscrizione di origine** e il **gruppo di risorse di origine**.
10. Scegliere **OK** per salvare le impostazioni.

### <a name="enable-replication-for-azure-vms-and-start-copying-the-data"></a>Abilitare la replica per le macchine virtuali di Azure e avviare la copia dei dati

Site Recovery recupera un elenco delle macchine virtuali associate alla sottoscrizione e al gruppo di risorse.

1. Selezionare la macchina virtuale da spostare e quindi selezionare **OK**.
2. Per **Impostazioni** selezionare **Ripristino di emergenza**.
3. Per **Configura ripristino di emergenza** > **Area di destinazione** selezionare l'area di destinazione in cui si eseguirà la replica.
4. Scegliere di usare le risorse di destinazione predefinite o quelle create nella fase preliminare.
5. Selezionare **Abilita replica** per avviare il processo.

   ![Abilitare la replica](media/tutorial-migrate-azure-to-azure/settings.png)

 

## <a name="test-the-configuration"></a>Testare la configurazione


1. Passare all'insieme di credenziali. In **Impostazioni** > **Elementi replicati** selezionare la macchina virtuale da spostare nell'area di destinazione. Selezionare quindi **Failover di test**.
2. In **Failover di test** selezionare un punto di ripristino da usare per il failover:

   - **Elaborato più recente**: viene eseguito il failover della macchina virtuale al punto di recupero più recente elaborato dal servizio Site Recovery. Viene visualizzato il timestamp. Poiché non viene impiegato tempo per l'elaborazione dei dati, questa opzione offre un valore RTO (Recovery Time Objective) basso.
   - **Coerente con l'app più recente**: questa opzione esegue il failover di tutte le macchine virtuali nel punto di recupero coerente con l'app più recente. Viene visualizzato il timestamp.
   - **Personalizzato**: selezionare qualsiasi punto di recupero.

3. Selezionare la rete virtuale di Azure di destinazione in cui si vogliono spostare le VM di Azure per testare la configurazione.

   > [!IMPORTANT]
   > Per il failover di test è consigliabile usare una rete di macchine virtuali di Azure separata, non la rete di produzione nell'area di destinazione.

4. Per avviare il test dello spostamento, selezionare **OK**. Per monitorare l'avanzamento dell'operazione, selezionare la macchina virtuale per visualizzarne le **proprietà**. In alternativa, selezionare il processo **Failover di test** nell'insieme di credenziali. Selezionare quindi **Impostazioni** > **Processi** > **Processi di Site Recovery**.
5. Al termine del failover, la macchina virtuale di Azure di replica viene visualizzata nel portale di Azure in **Macchine virtuali**. Verificare che la macchina virtuale sia in esecuzione, che sia stata dimensionata in modo adeguato e che sia connessa alla rete appropriata.
6. Per eliminare la macchina virtuale creata per il test, selezionare **Pulisci failover di test** sull'elemento replicato. Da **Note** registrare e salvare eventuali osservazioni relative al test.

## <a name="perform-the-move-and-confirm"></a>Eseguire lo spostamento e confermare

1. Passare all'insieme di credenziali e in **Impostazioni** > **Elementi replicati**, selezionare la macchina virtuale e quindi **Failover**.
1. Per **Failover** selezionare **Più recente**. 
2. Selezionare **Arrestare la macchina prima di iniziare il failover**. Prima di attivare il failover, Site Recovery prova ad arrestare la macchina virtuale di origine, ma il failover continua anche se l'arresto ha esito negativo. Nella pagina **Processi** è possibile seguire lo stato del failover.
3. Al termine del processo, controllare che la macchina virtuale si trovi nell'area di Azure di destinazione come previsto.
4. In **Elementi replicati** fare clic con il pulsante destro del mouse sulla macchina virtuale e selezionare **Esegui commit**. Il processo di spostamento è così completato. Attendere il completamento del processo di commit.

## <a name="discard-the-resources-from-the-source-region"></a>Rimuovere le risorse dall'area di origine

- Passare alla macchina virtuale e selezionare **Disabilita replica**. Il processo di copia dei dati per la VM viene arrestato.

  > [!IMPORTANT]
  > Completare questo passaggio per evitare addebiti per la replica di Site Recovery dopo lo spostamento.

Se non si prevede di riutilizzare alcuna risorsa di origine, seguire questa procedura:

1. Eliminare tutte le risorse di rete pertinenti dell'area di origine elencate nel passaggio 4 di [Preparare le VM di origine](#prepare-the-source-vms).
2. Eliminare l'account di archiviazione corrispondente nell'area di origine.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è appreso come spostare le macchine virtuali di Azure in un'area di Azure diversa. È ora possibile configurare il ripristino di emergenza per le macchine virtuali spostate.

> [!div class="nextstepaction"]
> [Configurare il ripristino di emergenza dopo la migrazione](azure-to-azure-quickstart.md)

