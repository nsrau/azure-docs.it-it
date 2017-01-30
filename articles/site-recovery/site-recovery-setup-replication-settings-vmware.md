---
title: Configurare le impostazioni di replica per Azure Site Recovery| Documentazione Microsoft
description: Descrive come distribuire Site Recovery per orchestrare la replica, il failover e il ripristino di macchine virtuali Hyper-V nei cloud VMM in Azure.
services: site-recovery
documentationcenter: 
author: sujayt
manager: rochakm
editor: rayne-wiselman
ms.assetid: 8e7d868e-00f3-4e8b-9a9e-f23365abf6ac
ms.service: site-recovery
ms.workload: backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 01/19/2017
ms.author: sutalasi
translationtype: Human Translation
ms.sourcegitcommit: 74d51269cdd55e39eaa2963fec2b409decb47d0a
ms.openlocfilehash: a279fdc35c62c76e6f0858d52c64240032669f15


---
# <a name="manage-replication-policy-for-vmware-to-azure"></a>Gestire i criteri di replica per VMware in Azure


## <a name="create-a-new-replication-policy"></a>Creare nuovi criteri di replica

1. Fare clic su "Gestisci -> Infrastruttura di Site Recovery" nel menu a sinistra. 
2. Selezionare "Criteri di replica" nella sezione "For VMware and Physical machines" (Per VMware e computer fisici).
3. Fare clic su "+Criteri di replica" nella parte superiore.

    ![Creare criteri di replica](./media/site-recovery-setup-replication-settings-vmware/createpolicy.png)

4. Immettere il nome del criterio.

5. In Soglia RPO specificare il limite del valore RPO. Quando la replica continua supera questo limite, verranno generati avvisi.
6. In Conservazione del punto di ripristino specificare la durata in ore dell'intervallo di conservazione per ogni punto di ripristino. I computer protetti possono essere ripristinati in qualsiasi punto all'interno di un intervallo. 

    > [!NOTE] 
    > Per i computer replicati in Archiviazione Premium è supportata la conservazione fino a 24 ore, mentre per i computer replicati in Archiviazione Standard è supportata la conservazione per 72 ore.
    
    > [!NOTE] 
    > Verrà creato automaticamente un criterio di replica per il failback.

7. In Frequenza snapshot coerenti con l'app specificare la frequenza, in minuti, con cui verranno creati punti di ripristino contenenti snapshot coerenti con l'applicazione.

8. Fare clic su "OK". La creazione del criterio dovrebbe richiedere approssimativamente da 30 secondi a 1 minuto.

![Creare criteri di replica](./media/site-recovery-setup-replication-settings-vmware/Creating-Policy.png)

## <a name="associate-configuration-server-with-replication-policy"></a>Associare il server di configurazione ai criteri di replica
1. Fare clic sul criterio di replica a cui si vuole associare il server di configurazione.
2. Fare clic su "Associa" nella parte superiore.
![Creare criteri di replica](./media/site-recovery-setup-replication-settings-vmware/Associate-CS-1.PNG)

3. Selezionare il server di configurazione nell'elenco di server.
4. Fare clic su OK. L'associazione del server di configurazione dovrebbe richiedere approssimativamente da 1 a 2 minuti.

![Creare criteri di replica](./media/site-recovery-setup-replication-settings-vmware/Associate-CS-2.png)

## <a name="edit-replication-policy"></a>Modificare i criteri di replica
1. Fare clic sul criterio per cui si vogliono modificare le impostazioni di replica.
![Creare criteri di replica](./media/site-recovery-setup-replication-settings-vmware/Select-Policy.png)

2. Fare clic su "Modifica impostazioni" nella parte superiore.
![Creare criteri di replica](./media/site-recovery-setup-replication-settings-vmware/Edit-Policy.png)

3. Modificare le impostazioni in base alle esigenze.
4. Fare clic su "Salva" nella parte superiore. Il salvataggio del criterio dovrebbe richiedere approssimativamente da 2 a 5 minuti, a seconda del numero di VM che usano il criterio di replica.

![Creare criteri di replica](./media/site-recovery-setup-replication-settings-vmware/Save-Policy.png)

## <a name="dissociate-configuration-server-from-replication-policy"></a>Annullare l'associazione del server di configurazione ai criteri di replica
1. Fare clic sul criterio di replica associato al server di configurazione.
2. Fare clic su "Annulla associazione" nella parte superiore.
3. Selezionare il server di configurazione nell'elenco di server.
4. Fare clic su OK. L'annullamento dell'associazione del server di configurazione dovrebbe richiedere approssimativamente da 1 a 2 minuti.
    
    > [!NOTE] 
    > Non è possibile annullare l'associazione di un server di configurazione se i criteri vengono usati da almeno un elemento replicato. Prima di annullare l'associazione del server di configurazione, verificare che nessun elemento replicato usi i criteri.

## <a name="delete-replication-policy"></a>Eliminare criteri di replica 

1. Fare clic sul criterio di replica da eliminare.
2. Fare clic su Elimina. L'eliminazione del criterio dovrebbe richiedere approssimativamente da 30 secondi a 1 minuto.

    > [!NOTE] 
    > Non è possibile eliminare criteri di replica a cui è associato almeno 1 server di configurazione. Prima di eliminare i criteri, verificare che nessun elemento replicato li usi ed eliminare tutti server di configurazione associati.


<!--HONumber=Jan17_HO4-->


