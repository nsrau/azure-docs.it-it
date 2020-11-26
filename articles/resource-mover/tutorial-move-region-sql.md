---
title: Spostare risorse SQL di Azure tra aree con Spostamento risorse di Azure
description: Informazioni su come spostare risorse SQL di Azure in un'altra area con Spostamento risorse di Azure
author: rayne-wiselman
manager: evansma
ms.service: resource-move
ms.topic: tutorial
ms.date: 09/09/2020
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 9fe43125c83436f89bf93cbe975317efec2beb46
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/24/2020
ms.locfileid: "95542814"
---
# <a name="tutorial-move-azure-sql-database-resources-to-another-region"></a>Esercitazione: Spostare risorse del database SQL di Azure in un'altra area

Questa esercitazione illustra come spostare pool elastici e database SQL di Azure in un'area di Azure diversa, usando [Spostamento risorse di Azure](overview.md).

> [!NOTE]
> Spostamento risorse di Azure è attualmente disponibile in anteprima.

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
> * Verificare prerequisiti e requisiti.
> * Selezionare le risorse da spostare.
> * Risolvere le dipendenze delle risorse.
> * Preparare e spostare la risorsa di SQL nell'area di destinazione.
> * Preparare e spostare database e pool elastici.
> * Decidere se si vuole rimuovere lo spostamento o eseguirne il commit. 
> * Facoltativamente, rimuovere le risorse nell'area di origine dopo lo spostamento. 

> [!NOTE]
> Le esercitazioni illustrano il percorso più rapido per provare uno scenario e prevedono l'uso delle opzioni predefinite. 

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/pricing/free-trial/) prima di iniziare. Accedere quindi al [portale di Azure](https://portal.azure.com).

## <a name="prerequisites"></a>Prerequisiti

-  Verificare di avere accesso come *Proprietario* nella sottoscrizione che contiene le risorse da spostare.
    - La prima volta che si aggiunge una risorsa per una coppia di origine e destinazione specifica in una sottoscrizione di Azure, Spostamento risorse crea un'[identità gestita assegnata dal sistema](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types) (nota in precedenza come identità del servizio gestita) che viene considerata attendibile dalla sottoscrizione.
    - Per creare l'identità e assegnarle il ruolo richiesto (Collaboratore o Amministratore Accesso utenti nella sottoscrizione di origine), l'account usato per aggiungere le risorse deve avere le autorizzazioni di *Proprietario* nella sottoscrizione. [Altre informazioni](../role-based-access-control/rbac-and-directory-admin-roles.md#azure-roles) sui ruoli di Azure.
- La quota disponibile nella sottoscrizione deve essere sufficiente per creare le risorse che si intende spostare nell'area di destinazione. Se non è disponibile alcuna quota, [richiedere limiti aggiuntivi](../azure-resource-manager/management/azure-subscription-service-limits.md).
- Verificare i prezzi e gli addebiti associati all'area di destinazione in cui si intende spostare le risorse. Per facilitare l'operazione, usare il [calcolatore dei prezzi](https://azure.microsoft.com/pricing/calculator/).
    

## <a name="check-sql-requirements"></a>Controllare i requisiti di SQL

1. [Controllare](support-matrix-move-region-sql.md) quali funzionalità di database/pool elastici sono supportate per lo spostamento in un'altra area.
2. Nell'area di destinazione creare un server di destinazione per ogni server di origine. [Altre informazioni](../azure-sql/database/active-geo-replication-security-configure.md#how-to-configure-logins-and-users)
4. Se i database sono crittografati con la tecnologia TDE (Transparent Data Encryption) e si usa una chiave di crittografia personale in Azure Key Vault, vedere [come](../key-vault/general/move-region.md) spostare gli insiemi di credenziali delle chiavi in un'altra area.
5. Se la sincronizzazione dei dati SQL è abilitata, lo spostamento dei database membro è supportato. Dopo lo spostamento è necessario configurare la sincronizzazione dei dati SQL per il nuovo database di destinazione.
6. Rimuovere le impostazioni di Sicurezza dei dati avanzata prima dello spostamento. Dopo lo spostamento [configurare le impostazioni](../azure-sql/database/azure-defender-for-sql.md) a livello di SQL Server nell'area di destinazione.
7. Se il controllo è abilitato, i criteri vengono reimpostati sull'impostazione predefinita dopo lo spostamento. [Configurare di nuovo il controllo](../azure-sql/database/auditing-overview.md) dopo lo spostamento.
7. I criteri di conservazione backup per il database di origine vengono trasferiti al database di destinazione. [Altre informazioni](../azure-sql/database/long-term-backup-retention-configure.md) sulla modifica delle impostazioni dopo lo spostamento.
8. Rimuovere le regole del firewall a livello di server prima dello spostamento. Le regole del firewall a livello di database vengono copiate dal server di origine a quello di destinazione durante lo spostamento. Dopo lo spostamento [configurare le regole del firewall](../azure-sql/database/firewall-create-server-level-portal-quickstart.md) per SQL Server nell'area di destinazione.
9. Rimuovere le impostazioni di ottimizzazione automatica prima dello spostamento. [Configurare di nuovo l'ottimizzazione automatica](../azure-sql/database/automatic-tuning-enable.md) dopo lo spostamento.
10. Rimuovere le impostazioni degli avvisi di database prima dello spostamento. [Reimpostarli](../azure-sql/database/alerts-insights-configure-portal.md) dopo lo spostamento.
    
## <a name="select-resources"></a>Selezionare le risorse

Selezionare le risorse da spostare.

- È possibile selezionare tutti i tipi di risorse supportati in qualsiasi gruppo di risorse nell'area di origine selezionata.
- Le risorse vengono spostate in un'area di destinazione nella stessa sottoscrizione dell'area di origine. Se si vuole cambiare la sottoscrizione, è possibile eseguire questa operazione dopo lo spostamento delle risorse.

1. Nel portale di Azure cercare *spostamento risorse*. In **Servizi** selezionare quindi **Spostamento risorse di Azure**.

     ![Risultati della ricerca di spostamento risorse nel portale di Azure](./media/tutorial-move-region-sql/search.png)

2. In **Panoramica** fare clic su **Attività iniziali**.

    ![Pulsante per aggiungere le risorse da spostare in un'altra area](./media/tutorial-move-region-sql/get-started.png)

3. In **Sposta risorse** > **Origine e destinazione** selezionare l'area e la sottoscrizione di origine.
4. In **Destinazione** selezionare l'area in cui spostare le risorse. Quindi fare clic su **Next**.

    ![Pagina per la selezione dell'area di origine e di destinazione](./media/tutorial-move-region-sql/source-target.png)

6. In **Risorse da spostare** fare clic su **Selezionare le risorse**.
7. In **Selezionare le risorse** selezionare le risorse. È possibile aggiungere solo le risorse supportate per lo spostamento. Fare quindi clic su **Done**.

    ![Pagina per selezionare le risorse di SQL da spostare](./media/tutorial-move-region-sql/select-resources.png)

8. In **Risorse da spostare** fare clic su **Avanti**.

9. In **Rivedi e aggiungi** verificare le impostazioni relative a origine e destinazione. Verificare di aver compreso che i metadati relativi allo spostamento verranno archiviati in un gruppo di risorse creato a questo scopo nell'area dei metadati.


    ![Pagina per rivedere le impostazioni e procedere allo spostamento](./media/tutorial-move-region-sql/review.png)

10. Per iniziare ad aggiungere le risorse, fare clic su **Continua**.
11. Dopo il completamento del processo di aggiunta fare clic su **Aggiunta di risorse per lo spostamento** nell'icona di notifica.
12. Dopo aver fatto clic sulla notifica, esaminare le risorse nella pagina **Tra aree**.


> [!NOTE]
> 
> - La risorsa di SQL Server si trova ora nello stato *Assegnazione manuale in sospeso*.
> - Altre risorse aggiunte si trovano nello stato *Preparazione in sospeso*.
> - Il metodo per rimuovere una risorsa da una raccolta di spostamento dipende da punto in cui ci si trova nel processo di spostamento. [Altre informazioni](remove-move-resources.md)

## <a name="resolve-dependencies"></a>Risolvere gli errori relativi alle risorse di Azure non trovate


1. Se in **Tra regioni** per le risorse viene visualizzato un messaggio *Convalida dipendenze* nella colonna **Problemi**, fare clic sul pulsante **Convalida dipendenze**. Viene avviato il processo di convalida.
2. Se vengono trovate dipendenze, fare clic su **Aggiungi dipendenze**.

    ![Pulsante per aggiungere le dipendenze](./media/tutorial-move-region-sql/add-dependencies.png)
   
3. In **Aggiungi dipendenze** selezionare le risorse dipendenti > **Aggiungi dipendenze**. Monitorare lo stato di avanzamento nelle notifiche.

4. Se necessario, aggiungere altre dipendenze e convalidare di nuovo le dipendenze. 

5. Nella pagina **Tra aree** verificare che le risorse si trovino ora in uno stato *Preparazione in sospeso* e che non vengano segnalati problemi.

    ![Pagina che mostra le risorse nello stato Preparazione in sospeso](./media/tutorial-move-region-sql/prepare-pending.png)



## <a name="move-the-sql-server"></a>Spostare la risorsa di SQL Server

Assegnare una risorsa di SQL Server di destinazione nell'area di destinazione ed eseguire il commit dello spostamento.

### <a name="assign-a-target-sql-server"></a>Assegnare una risorsa di SQL Server di destinazione

1. In **Tra aree** fare clic su **Risorsa non assegnata** per la risorsa di SQL Server nella colonna **Configurazione della destinazione**.
2. Selezionare una risorsa di SQL Server esistente nell'area di destinazione. 
    
    ![Voce che mostra lo stato della risorsa di SQL Server impostato su Commit spostamento in sospeso](./media/tutorial-move-region-sql/sql-server-commit-move-pending.png) 

    
> [!NOTE]
> Lo stato della risorsa di SQL Server di origine passa a *Commit spostamento in sospeso*. 

### <a name="commit-the-sql-server-move"></a>Eseguire il commit dello spostamento della risorsa di SQL Server

1. In **Tra aree** selezionare la risorsa di SQL Server e quindi fare clic su **Commit spostamento**.
2. In **Commit delle risorse** fare clic su **Commit**.

    ![Pagina per eseguire il commit dello spostamento della risorsa di SQL Server](./media/tutorial-move-region-sql/commit-sql-server.png)

3. Tenere traccia dello stato di avanzamento dello spostamento nella barra delle notifiche.

> [!NOTE]
> Dopo il commit la risorsa di SQL Server si trova nello stato *Eliminazione origine in sospeso*.


## <a name="prepare-resources-to-move"></a>Preparare le risorse da spostare

Dopo aver spostato la risorsa di SQL Server di origine è possibile preparare lo spostamento delle altre risorse.

## <a name="prepare-an-elastic-pool"></a>Preparare un pool elastico

1. In **Tra aree** selezionare il pool elastico di origine (demo-test1-elasticpool nella procedura dettagliata) e quindi fare clic su **Prepara**.

    ![Pulsante per preparare le risorse](./media/tutorial-move-region-sql/prepare-elastic.png)

2. In **Prepara risorse** fare clic su **Prepara**.
3. Quando le notifiche indicano la riuscita del processo di preparazione, fare clic su **Aggiorna**.

> [!NOTE]
> Il pool elastico si trova ora nello stato *Avvio spostamento in sospeso*.

## <a name="prepare-a-single-database"></a>Preparare un database singolo

1. In **Tra aree** selezionare il database singolo (non in un pool elastico) e quindi fare clic su **Prepara**.

    ![Pulsante per preparare le risorse selezionate](./media/tutorial-move-region-sql/prepare-db.png)

2. In **Prepara risorse** fare clic su **Prepara**.
3. Quando le notifiche indicano la riuscita del processo di preparazione, fare clic su **Aggiorna**.

> [!NOTE]
> Il database si trova ora nello stato *Avvio spostamento in sospeso* ed è stato creato nell'area di destinazione.


## <a name="move-the-pool-and-prepare-pool-databases"></a>Spostare il pool e preparare i database del pool

Per preparare i database in un pool elastico, il pool elastico deve trovarsi nello stato *Commit spostamento in sospeso*. Per passare a questo stato, avviare lo spostamento per il pool.

#### <a name="initiate-move---elastic-pool"></a>Avvia spostamento - pool elastico

1. In **Tra aree** selezionare il pool elastico di origine (demo-test1-elasticpool nella procedura dettagliata) e quindi fare clic su **Avvia spostamento**.
2. In **Sposta risorse** fare clic su **Avvia spostamento**.

    
    ![Pulsante per avviare lo spostamento del pool elastico](./media/tutorial-move-region-sql/initiate-elastic.png)

1. Tenere traccia dello stato di avanzamento dello spostamento nella barra delle notifiche.
1. Quando le notifiche indicano la riuscita dello spostamento, fare clic su **Aggiorna**.

> [!NOTE]
> Il pool elastico si trova ora nello stato *Commit spostamento in sospeso*.

#### <a name="prepare-database"></a>Preparare il database

1. In **Tra aree** selezionare il database (demo-test2-sqldb nella procedura dettagliata) e quindi fare clic su **Prepara**.
2. In **Prepara risorse** fare clic su **Prepara**.

    ![Pulsante per preparare il database nel pool elastico](./media/tutorial-move-region-sql/prepare-database-elastic.png) 

Durante la preparazione il database di destinazione viene creato nell'area di destinazione e viene avviata la replica dei dati. Dopo la preparazione il database si trova nello stato *Avvio spostamento in sospeso*. 

![Pulsante per preparare il database selezionato nel pool elastico](./media/tutorial-move-region-sql/initiate-move-pending.png) 

## <a name="move-databases"></a>Spostare database

Avviare lo spostamento dei database.
1. In **Tra aree** selezionare le risorse con lo stato **Avvio spostamento in sospeso**. Fare quindi clic su **Avvia spostamento**.
2. In **Sposta risorse** fare clic su **Avvia spostamento**.

    ![Pagina per avviare lo spostamento](./media/tutorial-move-region-sql/initiate-move.png)

3. Tenere traccia dello stato di avanzamento dello spostamento nella barra delle notifiche.

> [!NOTE]
> I database si trovano ora nello stato *Commit spostamento in sospeso*.


## <a name="discard-or-commit"></a>Rimozione o commit?

Dopo lo spostamento iniziale è possibile decidere se si vuole rimuovere lo spostamento o eseguirne il commit. 

- **Rimozione**: è possibile che si voglia eliminare uno spostamento se si sta eseguendo un test e non si vuole realmente spostare la risorsa di origine. In seguito alla rimozione dello spostamento, la risorsa torna allo stato **Avvio spostamento in sospeso**.
- **Commit**: il commit consente di completare lo spostamento nell'area di destinazione. Dopo il commit una risorsa di origine si troverà nello stato **Eliminazione origine in sospeso** e sarà possibile decidere se eliminarla.


## <a name="discard-the-move"></a>Rimuovere lo spostamento 

È possibile rimuovere lo spostamento come descritto di seguito:

1. In **Tra aree** selezionare le risorse con lo stato **Commit spostamento in sospeso** e fare clic su **Rimuovi spostamento**.
2. In **Rimuovi spostamento** fare clic su **Rimuovi**.
3. Tenere traccia dello stato di avanzamento dello spostamento nella barra delle notifiche.


> [!NOTE]
> - Dopo la rimozione le risorse si trovano nello stato *Avvio spostamento in sospeso*.
> - Se è presente un solo pool elastico, la rimozione continua e il pool elastico creato nell'area di destinazione viene eliminato.
> - Se è presente un pool elastico con database associati nello stato *Commit spostamento in sospeso*, non è possibile rimuovere il pool elastico.
> - Se si rimuove un database SQL, le risorse dell'area di destinazione non vengono eliminate. 

Se si vuole riavviare lo spostamento dopo la rimozione, selezionare il database SQL o il pool elastico e avviare di nuovo lo spostamento.


## <a name="commit-the-move"></a>Eseguire il commit dello spostamento

Per completare lo spostamento di database e pool elastici, seguire questa procedura:


1. Controllare che la risorsa di SQL Server si trovi nello stato *Eliminazione origine in sospeso*.
2. Aggiornare le stringhe di connessione del database impostandole sull'area di destinazione prima di eseguire il commit.
3. In **Tra aree** selezionare le risorse SQL e quindi fare clic su **Commit spostamento**.
4. In **Commit delle risorse** fare clic su **Commit**.

    ![Commit spostamento](./media/tutorial-move-region-sql/commit-sql-resources.png)

5. Tenere traccia dello stato di avanzamento del commit nella barra delle notifiche.


> [!NOTE]
> Durante il processo di commit i database SQL potrebbero risultare inattivi per qualche minuto.
> I database e i pool elastici di cui è stato eseguito il commit si trovano nello stato *Eliminazione origine in sospeso*.
> Dopo il commit aggiornare le impostazioni correlate al database, incluse le regole del firewall, i criteri e gli avvisi, nel database di destinazione.


## <a name="delete-source-resources-after-commit"></a>Eliminare le risorse di origine dopo il commit

Facoltativamente, dopo lo spostamento è possibile eliminare le risorse nell'area di origine. 

1. In **Tra aree** fare clic sul nome di ogni risorsa di origine da eliminare.
2. Nella pagina delle proprietà di ogni risorsa selezionare **Elimina**.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione:

> [!div class="checklist"]
> * I database SQL di Azure sono stati spostati in un'altra area di Azure.
> * I pool elastici SQL di Azure sono stati spostati in un'altra area.

Provare ora a spostare le macchine virtuali di Azure in un'altra area.

> [!div class="nextstepaction"]
> [Spostare macchine virtuali di Azure](./tutorial-move-region-virtual-machines.md)