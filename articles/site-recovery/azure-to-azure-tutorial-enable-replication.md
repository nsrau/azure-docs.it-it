---
title: Esercitazione per configurare il ripristino di emergenza delle macchine virtuali di Azure con Azure Site Recovery
description: Questa esercitazione descrive come configurare il ripristino di emergenza per macchine virtuali di Azure in un'area di Azure diversa usando il servizio Site Recovery.
ms.topic: tutorial
ms.date: 11/03/2020
ms.custom: mvc
ms.openlocfilehash: 90527ad39055e438e4970ad4686f204f72d20cd2
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/05/2020
ms.locfileid: "93394101"
---
# <a name="tutorial-set-up-disaster-recovery-for-azure-vms"></a>Esercitazione: Configurare il ripristino di emergenza per le macchine virtuali di Azure

Questa esercitazione illustra come configurare il ripristino di emergenza per le macchine virtuali di Azure usando [Azure Site Recovery](site-recovery-overview.md). In questo articolo vengono illustrate le operazioni seguenti:

> [!div class="checklist"]
> * Verificare le impostazioni e le autorizzazioni di Azure
> * Preparare le macchine virtuali da replicare
> * Creare un insieme di credenziali di Servizi di ripristino
> * Abilitare la replica delle macchine virtuali

Quando si abilita la replica di una macchina virtuale per il ripristino di emergenza, l'estensione del servizio di mobilità di Site Recovery viene installata nella macchina virtuale e registrata con Azure Site Recovery. Durante la replica, le scritture su disco della macchina virtuale vengono inviate a un account di archiviazione della cache nell'area di origine. I dati vengono inviati da questo account all'area di destinazione e vengono generati i punti di ripristino dai dati. Quando si esegue il failover di una macchina virtuale durante il ripristino di emergenza, viene usato un punto di ripristino per ripristinare la macchina virtuale nell'area di destinazione.

> [!NOTE]
> Le esercitazioni forniscono istruzioni con le impostazioni predefinite più semplici. Per configurare il ripristino di emergenza delle macchine virtuali di Azure con impostazioni personalizzate, vedere [questo articolo](azure-to-azure-how-to-enable-replication.md).

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare questa esercitazione, è necessario:

- [Esaminare le aree supportate](azure-to-azure-support-matrix.md#region-support). È possibile configurare il ripristino di emergenza per le macchine virtuali di Azure tra due località diverse nella stessa area geografica.
- Sono necessarie una o più macchine virtuali di Azure. Verificare che siano supportate le macchine virtuali [Windows](azure-to-azure-support-matrix.md#windows) o [Linux](azure-to-azure-support-matrix.md#replicated-machines---linux-file-systemguest-storage).
- Verificare i requisiti di [calcolo](azure-to-azure-support-matrix.md#replicated-machines---compute-settings), [archiviazione](azure-to-azure-support-matrix.md#replicated-machines---storage) e [rete](azure-to-azure-support-matrix.md#replicated-machines---networking) della macchina virtuale.
- Questa esercitazione presuppone che le macchine virtuali non siano crittografate. Se si vuole configurare il ripristino di emergenza per le macchine virtuali crittografate, [vedere questo articolo](azure-to-azure-how-to-enable-replication-ade-vms.md).

## <a name="check-azure-settings"></a>Verificare le impostazioni di Azure

Verificare le autorizzazioni e le impostazioni nell'area di destinazione.

### <a name="check-permissions"></a>Verificare le autorizzazioni

L'account Azure deve avere le autorizzazioni per creare un insieme di credenziali di Servizi di ripristino e per creare le macchine virtuali nell'area di destinazione.

- Se è stata appena creata una sottoscrizione gratuita di Azure, si è l'amministratore dell'account e non sono necessari ulteriori interventi da parte dell'utente.
- In caso contrario, rivolgersi all'amministratore per ottenere le autorizzazioni necessarie.
    - **Creare un insieme di credenziali:** Autorizzazioni di amministratore o proprietario per la sottoscrizione. 
    - **Gestire le operazioni di Site Recovery nell'insieme di credenziali**: Ruolo di Azure predefinito *Collaboratore di Site Recovery*.
    - **Creare le macchine virtuali di Azure nell'area di destinazione**: Ruolo predefinito *Collaboratore Macchina virtuale* oppure le autorizzazioni specifiche per:
        - Creare una macchina virtuale nella rete virtuale selezionata.
        - Scrivere in un account di archiviazione di Azure.
        - Scrivere su un disco gestito di Azure.

### <a name="verify-target-settings"></a>Verificare le impostazioni di destinazione

Durante il ripristino dell'individuazione, quando si esegue il failover dall'area di origine, vengono create le macchine virtuali nell'area di destinazione. 

Verificare che la sottoscrizione disponga di un numero sufficiente di risorse nell'area di destinazione. È necessario poter creare le macchine virtuali con dimensioni corrispondenti a quelle delle macchine virtuali nell'area di origine. Quando si configura il ripristino di emergenza, Site Recovery sceglie le stesse dimensioni o le dimensioni più vicine possibile per la macchina virtuale di destinazione.


## <a name="prepare-vms"></a>Preparare le macchine virtuali

Assicurarsi che nelle macchine virtuali siano disponibili la connettività in uscita e i certificati radice più recenti. 


### <a name="set-up-vm-connectivity"></a>Configurare la connettività delle macchine virtuali

Le macchine virtuali di cui si vuole eseguire la replica necessitano della connettività di rete in uscita.

> [!NOTE]
> Site Recovery non supporta l'uso di un proxy di autenticazione per controllare la connettività di rete.

#### <a name="outbound-connectivity-for-urls"></a>Connettività in uscita per gli URL

Se si usa un proxy firewall basato su URL per controllare la connettività in uscita, consentire l'accesso a questi URL:

| **Nome**                  | **Commerciale**                               | **Enti pubblici**                                 | **Descrizione** |
| ------------------------- | -------------------------------------------- | ---------------------------------------------- | ----------- |
| Archiviazione                   | `*.blob.core.windows.net`                  | `*.blob.core.usgovcloudapi.net`              | Consente la scrittura di dati dalla macchina virtuale nell'account di archiviazione della cache all'area di origine. |
| Azure Active Directory    | `login.microsoftonline.com`                | `login.microsoftonline.us`                   | Fornisce l'autenticazione e l'autorizzazione per gli URL del servizio Site Recovery. |
| Replica               | `*.hypervrecoverymanager.windowsazure.com` | `*.hypervrecoverymanager.windowsazure.com`   | Consente alla macchina virtuale di comunicare con il servizio Site Recovery. |
| Bus di servizio               | `*.servicebus.windows.net`                 | `*.servicebus.usgovcloudapi.net`             | Consente alla macchina virtuale di scrivere i dati di diagnostica e monitoraggio di Site Recovery. |

#### <a name="outbound-connectivity-for-ip-address-ranges"></a>Connettività in uscita per gli intervalli di indirizzi IP

Se si usano i gruppi di sicurezza di rete per controllare la connettività, creare regole dei gruppi di sicurezza di rete basate sul tag del servizio che consentano la connettività HTTPS in uscita alla porta 443 per questi [tag del servizio](../virtual-network/service-tags-overview.md#available-service-tags) (gruppi di indirizzi IP):

**Tag** | **Consentito** 
--- | ---
Tag Storage  |Consente la scrittura di dati dalla macchina virtuale nell'account di archiviazione della cache.   
Tag Azure AD | Consente l'accesso a tutti gli indirizzi IP corrispondenti ad Azure AD.   
Tag EventsHub | Consente l'accesso al monitoraggio di Site Recovery.  
Tag AzureSiteRecovery | Consente l'accesso al servizio Site Recovery in qualsiasi area.   
Tag GuestAndHybridManagement | Usare questo tag se si vuole aggiornare automaticamente l'agente di mobilità di Site Recovery in esecuzione nelle macchine virtuali abilitate per la replica.

[Altre informazioni](azure-to-azure-about-networking.md#outbound-connectivity-using-service-tags) sui tag necessari e sugli esempi di assegnazione dei tag.

### <a name="verify-vm-certificates"></a>Verificare i certificati delle macchine virtuali

Controllare che nelle macchine virtuali siano presenti i certificati radice più recenti. In caso contrario, non sarà possibile registrare la macchina virtuale in Site Recovery a causa di vincoli di sicurezza.

- **Macchine virtuali Windows**: installare tutti gli aggiornamenti di Windows più recenti nella macchina virtuale in modo che tutti i certificati radice trusted siano presenti nel computer. In un ambiente non connesso, seguire i processi standard per Windows Update e per gli aggiornamenti dei certificati.
- **Macchine virtuali Linux**: seguire le indicazioni fornite dal distributore di Linux per ottenere i certificati radice trusted e l'elenco di revoche di certificati più recenti.

## <a name="create-a-recovery-services-vault"></a>Creare un insieme di credenziali di Servizi di ripristino

Creare un insieme di credenziali di Servizi di ripristino in qualsiasi area, tranne che nell'area di origine da cui verranno replicate le macchine virtuali.

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Nella casella di ricerca digitare *ripristino*. In **Servizi** fare clic su **Insiemi di credenziali di Servizi di ripristino**.

    ![Ricerca di insiemi di credenziali di Servizi di ripristino](./media/azure-to-azure-tutorial-enable-replication/search.png)

3. In **Insiemi di credenziali di Servizi di ripristino** selezionare **Aggiungi**.
4. In **Crea insieme di credenziali di Servizi di ripristino** > **Informazioni di base** selezionare la sottoscrizione in cui creare l'insieme di credenziali.
5. In **Gruppo di risorse** selezionare un gruppo di risorse esistente per l'insieme di credenziali o crearne uno nuovo.
6. In **Nome dell'insieme di credenziali** specificare un nome descrittivo per identificare l'insieme di credenziali.
7. In **Area** selezionare l'area di Azure in cui verrà collocato l'insieme di credenziali. [Verificare le aree supportate](https://azure.microsoft.com/pricing/details/site-recovery/).
8. Selezionare **Rivedi e crea**.

   ![Impostazioni dell'insieme di credenziali nella pagina per la creazione di un nuovo insieme di credenziali](./media/azure-to-azure-tutorial-enable-replication/vault-basics.png)

9. In **Rivedi e crea** selezionare **Crea**.

10. Viene avviata la distribuzione dell'insieme di credenziali. Seguire lo stato di avanzamento nelle notifiche.
11. Una volta distribuito l'insieme di credenziali, selezionare **Aggiungi al dashboard** per salvarlo e farvi riferimento rapidamente. Selezionare **Vai alla risorsa** per aprire il nuovo insieme di credenziali. 
    
    ![Pulsanti per l'apertura dell'insieme di credenziali dopo la distribuzione e l'aggiunta al dashboard](./media/azure-to-azure-tutorial-enable-replication/vault-deploy.png)

### <a name="enable-site-recovery"></a>Abilitare Site Recovery

Nelle impostazioni dell'insieme di credenziali selezionare **Abilita Site Recovery**.

![Selezione per abilitare Site Recovery nell'insieme di credenziali](./media/azure-to-azure-tutorial-enable-replication/enable-site-recovery.png)

## <a name="enable-replication"></a>Abilitare la replica

Selezionare le impostazioni di origine e abilitare la replica delle macchina virtuali. 

### <a name="select-source-settings"></a>Selezionare le impostazioni di origine

1. Nella pagina Insieme di credenziali > **Site Recovery** selezionare **Abilita replica** in **Macchine virtuali di Azure**.

    ![Selezione per abilitare la replica per le macchine virtuali di Azure](./media/azure-to-azure-tutorial-enable-replication/enable-replication.png)

2. In **Origine**> **Località di origine** selezionare l'area di Azure di origine in cui le VM sono attualmente in esecuzione.
3. Nel **modello di distribuzione delle macchine virtuali di Azure** lasciare l'impostazione **Resource Manager** predefinita.
4. In **Sottoscrizione di origine** selezionare la sottoscrizione in cui sono in esecuzione le macchine virtuali. È possibile selezionare qualsiasi sottoscrizione che si trovi nello stesso tenant di Azure Active Directory dell'insieme di credenziali.
5. In **Gruppo di risorse di origine** selezionare il gruppo di risorse che contiene le macchine virtuali.
6. In **Ripristino di emergenza tra zone di disponibilità** lasciare l'impostazione predefinita **No**.

     ![Impostare l'origine](./media/azure-to-azure-tutorial-enable-replication/source.png)

7. Selezionare **Avanti**.

### <a name="select-the-vms"></a>Selezionare le macchine virtuali

Site Recovery recupera le macchine virtuali associate alla sottoscrizione e al gruppo di risorse selezionati.

1. In **Macchine virtuali** selezionare le macchine virtuali da abilitare per il ripristino di emergenza.

     ![Pagina per selezionare le macchine virtuali per la replica](./media/azure-to-azure-tutorial-enable-replication/select-vm.png)

2. Selezionare **Avanti**.

### <a name="review-replication-settings"></a>Rivedere le impostazioni di replica

1. In **Impostazioni di replica** rivedere le impostazioni. Site Recovery crea le impostazioni predefinite e i criteri per l'area di destinazione. Ai fini di questa esercitazione verranno usate le impostazioni predefinite.
2. Selezionare **Abilita replica**.

    ![Pagina per personalizzare le impostazioni e abilitare la replica](./media/azure-to-azure-tutorial-enable-replication/enable-vm-replication.png)   

3. Monitorare lo stato di avanzamento della replica nelle notifiche.

     ![Monitorare lo stato di avanzamento nelle notifiche](./media/azure-to-azure-tutorial-enable-replication/notification.png) ![Monitorare la notifica di operazione di replica riuscita](./media/azure-to-azure-tutorial-enable-replication/notification-success.png)

4. Le macchine virtuali abilitate vengono visualizzate nella pagina Insieme di credenziali > **Elementi replicati**.

    ![Macchina virtuale nella pagina elementi replicati](./media/azure-to-azure-tutorial-enable-replication/replicated-items.png)


## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato abilitato il ripristino di emergenza per una macchina virtuale di Azure. A questo punto, eseguire un'esercitazione per verificare che il failover funzioni come previsto.

> [!div class="nextstepaction"]
> [Eseguire un'esercitazione sul ripristino di emergenza](azure-to-azure-tutorial-dr-drill.md)
