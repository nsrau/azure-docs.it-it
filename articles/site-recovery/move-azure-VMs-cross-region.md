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
ms.openlocfilehash: bbede01844f20c0240b154fd319b818a43463131
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/07/2019
ms.locfileid: "55824564"
---
# <a name="move-azure-vms-to-another-region"></a>Spostare macchine virtuali di Azure in un'altra area

Esistono vari scenari in cui può essere opportuno spostare le macchine virtuali IaaS di Azure esistenti da un'area a un'altra come descritto, per aumentare l'affidabilità e la disponibilità delle VM esistenti, per migliorare la gestibilità, per motivi di governance e così via. 

Questa esercitazione illustra come spostare VM di Azure in un'altra area con Azure Site Recovery. In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * [Verificare i prerequisiti](#verify-prerequisites)
> * [Preparare le VM di origine](#prepare-the-source-vms)
> * [Preparare l'area di destinazione](#prepare-the-target-region)
> * [Copiare i dati nell'area di destinazione](#copy-data-to-the-target-region)
> * [Testare la configurazione](#test-the-configuration)
> * [Eseguire lo spostamento ](#perform-the-move-to-the-target-region-and-confirm)
> * [Rimuovere la risorsa nell'area di origine ](#discard-the-resource-in-the-source-region)

> [!IMPORTANT]
> Questo documento descrive come spostare le VM di Azure da un'area a un'altra allo stato attuale. Se il requisito è migliorare la disponibilità dell'infrastruttura spostando le VM in zone di disponibilità, vedere questa esercitazione.

## <a name="verify-prerequisites"></a>Verificare i prerequisiti

- Verificare la presenza di VM di Azure nell'area di Azure da cui si vuole eseguire lo spostamento.
- Verificare se la [combinazione di area di origine-area di destinazione scelta è supportata](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-support-matrix#region-support) e prendere una decisione basata su informazioni aggiornate in merito all'area di destinazione.
- Assicurarsi di aver compreso i [componenti e l'architettura dello scenario](azure-to-azure-architecture.md).
- Rivedere le [limitazioni e i requisiti del supporto](azure-to-azure-support-matrix.md).
- Verificare le autorizzazioni dell'account. Se è appena stato creato l'account gratuito di Azure, si è amministratori della propria sottoscrizione. Se non si è l'amministratore della propria sottoscrizione, rivolgersi all'amministratore per l'assegnazione delle autorizzazioni necessarie. Per abilitare la replica per una VM e infine copiare i dati nella destinazione con Azure Site Recovery, è necessario quanto segue:

    1. Autorizzazioni per creare una macchina virtuale nelle risorse di Azure. Al ruolo predefinito 'Collaboratore Macchina virtuale' sono assegnate le autorizzazioni seguenti, che includono:
        - Autorizzazione per la creazione di una macchina virtuale nel gruppo di risorse selezionato
        - Autorizzazione per la creazione di una macchina virtuale nella rete virtuale selezionata
        - Autorizzazione per la scrittura nell'account di archiviazione selezionato

    2. È necessario anche avere l'autorizzazione per la gestione delle operazioni di Azure Site Recovery. Il ruolo 'Collaboratore di Site Recovery' ha tutte le autorizzazioni necessarie per la gestione delle operazioni di Site Recovery in un insieme di credenziali dei servizi di ripristino.

## <a name="prepare-the-source-vms"></a>Preparare le VM di origine

1. Controllare che nelle VM di Azure da spostare siano presenti tutti i certificati radice più recenti. In caso contrario, a causa dei vincoli di sicurezza non è possibile abilitare la copia dei dati nell'area di destinazione.

    - Per le macchine virtuali di Windows, installare tutti gli aggiornamenti di Windows più recenti nella macchina virtuale in modo che tutti i certificati radice trusted siano presenti nel computer. In un ambiente non connesso, seguire i processi di aggiornamento di Windows e di aggiornamento dei certificati standard per l'organizzazione.
    - Per le macchine virtuali Linux, seguire le indicazioni fornite dal distributore di Linux per ottenere i certificati radice trusted più recenti e l'elenco di revoche di certificati nella macchina virtuale.
2. Assicurarsi di non usare un proxy di autenticazione per controllare la connettività di rete per le macchine virtuali da spostare.
3. Se la VM che si sta provando a spostare non ha accesso a Internet e usa un proxy firewall per controllare l'accesso in uscita, controllare i requisiti [qui](azure-to-azure-tutorial-enable-replication.md#configure-outbound-network-connectivity).
4. Identificare e annotare il layout di rete di origine e tutte le risorse attualmente in uso, tra cui i servizi di bilanciamento del carico, i gruppi di sicurezza di rete, l'indirizzo IP pubblico e così via per la verifica.

## <a name="prepare-the-target-region"></a>Preparare l'area di destinazione

1. Verificare che la sottoscrizione di Azure in uso consenta la creazione di macchine virtuali nell'area di destinazione usata per il ripristino di emergenza. Se necessario, contattare il supporto per abilitare la quota richiesta.

2. Assicurarsi che la sottoscrizione disponga di risorse sufficienti per supportare macchine virtuali con dimensioni corrispondenti alle macchine virtuali di origine. Se si usa Site Recovery per copiare i dati nella destinazione, Site Recovery sceglie per la VM di destinazione la stessa dimensione o la dimensione più vicina possibile.

3. Assicurarsi di creare una risorsa di destinazione per ogni componente identificato nel layout di rete di origine. Questo aspetto è importante affinché le VM abbiano tutte le caratteristiche e le funzionalità presenti nell'origine, dopo il cutover nell'area di destinazione.

    > [!NOTE]
    > Azure Site Recovery individua e crea automaticamente una rete virtuale e un account di archiviazione quando si abilita la replica per la VM di origine. In alternativa, è possibile creare preventivamente queste risorse e assegnarle alla VM nel passaggio di abilitazione della replica. Tutte le altre risorse devono invece essere create manualmente nell'area di destinazione, come indicato di seguito.

     Per creare le risorse di rete usate più di frequente che sono pertinenti per il caso specifico in base alla configurazione della VM di origine, vedere i documenti seguenti:

    - [Gruppi di sicurezza di rete](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group)
    - [Servizi di bilanciamento del carico](https://docs.microsoft.com/azure/load-balancer/#step-by-step-tutorials)
    - [IP pubblico](https://docs.microsoft.com/azure/load-balancer/#step-by-step-tutorials)
    
    Per qualsiasi altro componente di rete, vedere la [documentazione](https://docs.microsoft.com/azure/#pivot=products&panel=network) relativa alle reti. 

4. Se si vuole testare la configurazione prima di eseguire il cutover finale nell'area di destinazione, [creare manualmente una rete non di produzione](https://docs.microsoft.com/azure/virtual-network/quick-create-portal) nell'area di destinazione. Questo passaggio creerà un'interferenza minima con l'ambiente di produzione ed è consigliato.
    
## <a name="copy-data-to-the-target-region"></a>Copiare i dati nell'area di destinazione
La procedura seguente illustra come usare Azure Site Recovery per copiare i dati nell'area di destinazione.

### <a name="create-the-vault-in-any-region-except-the-source-region"></a>Creare l'insieme di credenziali in tutte le aree, ad eccezione dell'area di origine.

1. Accedere a [portale di Azure](https://portal.azure.com) > **Servizi di ripristino**.
2. Fare clic su **Crea una risorsa** > **Strumenti di gestione** > **Backup e Site Recovery (OMS)**.
3. In **Nome** specificare il nome descrittivo **ContosoVMVault**. Se si hanno più sottoscrizioni, selezionare quella appropriata.
4. Creare un gruppo di risorse **ContosoRG**.
5. Specificare un'area di Azure. Per verificare le aree supportate, vedere la sezione relativa alla disponibilità a livello geografico in [Prezzi di Azure Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/).
6. In Insiemi di credenziali dei servizi di ripristino fare clic su **Panoramica** > **ContosoVMVault** > **+Replica**.
7. In **Source** (Origine) selezionare **Azure**.
8. In **Percorso di origine** selezionare l'area di Azure di origine in cui le VM sono attualmente in esecuzione.
9. Selezionare il modello di distribuzione Resource Manager. Selezionare quindi la **sottoscrizione di origine** e il **gruppo di risorse di origine**.
10. Fare clic su **OK** per salvare le impostazioni.

### <a name="enable-replication-for-azure-vms-and-start-copying-the-data"></a>Abilitare la replica per le VM di Azure e avviare la copia dei dati

Site Recovery recupera un elenco delle macchine virtuali associate alla sottoscrizione e al gruppo di risorse.

1. Nel passaggio successivo selezionare la macchina virtuale che si vuole spostare. Fare quindi clic su **OK**.
3. In **Impostazioni** fare clic su **Ripristino di emergenza**.
4. In **Configure disaster recovery** (Configura ripristino di emergenza)  >  **Area di destinazione** selezionare l'area di destinazione in cui si vuole eseguire la replica.
5. Si può scegliere di procedere con le risorse di destinazione predefinite oppure usare quelle già create.
6. Fare clic su **Abilitazione della replica**. Verrà avviato un processo per abilitare la replica per la macchina virtuale.

    ![abilitare la replica](media/tutorial-migrate-azure-to-azure/settings.png)

 

## <a name="test-the-configuration"></a>Testare la configurazione


1. Passare all'insieme di credenziali e in **Impostazioni** > **Elementi replicati** fare clic sulla macchina virtuale che si intende spostare nell'area di destinazione e quindi sull'icona **+Failover di Test**.
2. In **Failover di test** selezionare un punto di ripristino in cui eseguire il failover:

   - **Elaborato più recente**: viene eseguito il failover della macchina virtuale al punto di recupero più recente elaborato dal servizio Site Recovery. Viene visualizzato il timestamp. Con questa opzione, non viene impiegato alcun tempo di elaborazione dati e viene quindi fornito un valore RTO (Recovery Time Objective) basso.
   - **Coerente con l'app più recente**: questa opzione esegue il failover di tutte le macchine virtuali al più recente punto di recupero coerente con l'app. Viene visualizzato il timestamp.
   - **Personalizzato**: selezionare qualsiasi punto di recupero.

3. Selezionare la rete virtuale di Azure di destinazione in cui si vogliono spostare le VM di Azure per testare la configurazione. 

> [!IMPORTANT]
> Per il failover di test è consigliabile usare una rete di macchine virtuali di Azure separata, non la rete di produzione dell'area di destinazione in cui si vogliono spostare infine le VM.

4. Per avviare il test dello spostamento, fare clic su **OK**. Per verificare lo stato dell'operazione, fare clic sulla VM per visualizzare le rispettive proprietà. In alternativa, è possibile fare clic sul processo **Failover di test** nel nome dell'insieme di credenziali, quindi su **Impostazioni** > **Processi** > **Site Recovery jobs** (Processi di Site Recovery).
5. Al termine del failover, la macchina virtuale di Azure di replica viene visualizzata nel portale di Azure in **Macchine virtuali**. Verificare che la macchina virtuale sia in esecuzione, che sia stata dimensionata in modo adeguato e che sia connessa alla rete appropriata.
6. Se si vuole eliminare la VM creata nell'ambito del test dello spostamento, fare clic su **Pulisci failover di test** per l'elemento replicato. In **Note** registrare e salvare eventuali osservazioni associate al test.

## <a name="perform-the-move-to-the-target-region-and-confirm"></a>Eseguire lo spostamento nell'area di destinazione e confermare

1.  Passare all'insieme di credenziali e in **Impostazioni** > **Elementi replicati** fare clic sulla macchina virtuale e quindi su **Failover**.
2. In **Failover** selezionare **Più recente**. 
3. Selezionare **Arrestare la macchina prima di iniziare il failover**. Site Recovery proverà ad arrestare la macchina virtuale di origine prima di attivare il failover. Il failover continua anche se l'arresto ha esito negativo. Nella pagina **Processi** è possibile seguire lo stato del failover. 
4. Al termine del processo, controllare che la VM si trovi nell'area di Azure di destinazione come previsto.
5. In **Elementi replicati** fare clic con il pulsante destro del mouse su Macchina virtuale > **Esegui commit**. Questa operazione completa il processo di spostamento nell'area di destinazione. Attendere la fine del processo di commit.

## <a name="discard-the-resource-in-the-source-region"></a>Rimuovere la risorsa nell'area di origine 

1. Passare alla VM.  Fare clic su **Disabilita replica**.  Il processo di copia dei dati per la VM viene arrestato.  

> [!IMPORTANT]
> Il passaggio riportato sopra è importante per evitare addebiti per la replica di Site Recovery dopo lo spostamento.

Se non si prevede di riusare alcuna risorsa di origine, procedere con il successivo set di passaggi.

1. Procedere all'eliminazione di tutte le risorse di rete pertinenti dell'area di origine annotate nel passaggio 4 di [Preparare le VM di origine](#prepare-the-source-vms). 
2. Eliminare l'account di archiviazione corrispondente nell'area di origine.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stata spostata una macchina virtuale di Azure in un'area diversa di Azure. Ora è possibile configurare il ripristino di emergenza per le macchine virtuali spostate.

> [!div class="nextstepaction"]
> [Configurare il ripristino di emergenza dopo la migrazione](azure-to-azure-quickstart.md)

