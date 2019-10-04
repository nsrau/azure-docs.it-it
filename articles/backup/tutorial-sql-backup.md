---
title: Esercitazione - Eseguire il backup di database di SQL Server in Azure
description: Questo tutorial spiega come eseguire il backup di SQL Server in Azure.
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: tutorial
ms.date: 06/18/2019
ms.author: dacurwin
ms.openlocfilehash: 729eb0d77cee85356e359dc475f4e439b8236ebb
ms.sourcegitcommit: c662440cf854139b72c998f854a0b9adcd7158bb
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/02/2019
ms.locfileid: "68736559"
---
# <a name="back-up-a-sql-server-database-in-an-azure-vm"></a>Eseguire il backup di un database di SQL Server in una VM di Azure



Questa esercitazione illustra come eseguire il backup di un database di SQL Server in esecuzione in una VM di Azure in un insieme di credenziali di Servizi di ripristino di Backup di Azure. In questo articolo viene spiegato come:

> [!div class="checklist"]
> * Creare e configurare un insieme di credenziali.
> * Individuare i database e configurare i backup.
> * Configurare la protezione automatica per i database.
> * Eseguire un backup ad hoc.


## <a name="prerequisites"></a>Prerequisiti

Prima di eseguire il backup del database di SQL Server, verificare le condizioni seguenti:

1. Identificare o [creare](backup-sql-server-database-azure-vms.md#create-a-recovery-services-vault) un insieme di credenziali di Servizi di ripristino nella stessa area o nelle stesse impostazioni locali della macchina virtuale che ospita l'istanza di SQL Server.
2. [Controllare le autorizzazioni VM](backup-azure-sql-database.md#set-vm-permissions) necessarie per eseguire il backup dei database SQL.
3. Verificare che la VM abbia [connettività di rete](backup-sql-server-database-azure-vms.md#establish-network-connectivity).
4. Verificare che i database di SQL Server siano denominati in base alle [linee guida per la denominazione](#verify-database-naming-guidelines-for-azure-backup) per Backup di Backup.
5. Verificare che non siano abilitate altre soluzioni di backup per il database. Disabilitare tutti gli altri backup di SQL Server prima di configurare questo scenario. È possibile abilitare Backup di Azure per una VM di Azure e Backup di Azure per un database SQL Server in esecuzione nella VM senza alcun conflitto.


### <a name="establish-network-connectivity"></a>Stabilire la connettività di rete

Per tutte le operazioni, la macchina virtuale SQL Server richiede la connettività agli indirizzi IP pubblici di Azure. Le operazioni della macchina virtuale, ad esempio individuazione dei database, configurazione e pianificazione dei backup, ripristino dei punti di ripristino e così via, non riescono in assenza di connettività agli indirizzi IP pubblici. Stabilire la connettività con una di queste opzioni:

- **Consentire gli intervalli IP del data center di Azure**: consentire gli [intervalli IP](https://www.microsoft.com/download/details.aspx?id=41653) nel download. Per accedere al gruppo di sicurezza di rete, usare il cmdlet **Set-AzureNetworkSecurityRule**.
- **Distribuire un server proxy HTTP per il routing del traffico:** quando si esegue il backup di un database SQL Server in una macchina virtuale di Azure, l'estensione di backup nella macchina virtuale usa le API HTTPS per inviare i comandi di gestione a Backup di Azure e i dati ad Archiviazione di Azure. L'estensione di backup usa anche Azure Active Directory (Azure AD) per l'autenticazione. Eseguire il routing del traffico di estensione per il backup di questi tre servizi attraverso il proxy HTTP. Le estensioni sono l'unico componente configurato per l'accesso a Internet pubblico.

Ogni opzione presenta vantaggi e svantaggi

**Opzione** | **Vantaggi** | **Svantaggi**
--- | --- | ---
Consentire gli intervalli di indirizzi IP | Senza costi aggiuntivi. | Complessità di gestione a causa della variazione degli intervalli IP nel tempo. <br/><br/> Possibilità di accesso a tutto l'ambiente Azure, non solo al servizio di Archiviazione di Azure.
Usare un proxy HTTP   | Possibilità di controllo granulare nel proxy sugli URL di archiviazione. <br/><br/> Singolo punto di accesso Internet alle VM. <br/><br/> Non è soggetto alle modifiche degli indirizzi IP di Azure. | Costi aggiuntivi per l'esecuzione di una macchina virtuale con il software proxy.

### <a name="set-vm-permissions"></a>Impostare le autorizzazioni della VM

Backup di Azure esegue una serie di operazioni quando si configura il backup per un database SQL Server:

- Aggiunge l'estensione **AzureBackupWindowsWorkload**.
- Per individuare i database nella macchina virtuale, Backup di Azure crea l'account **NT SERVICE\AzureWLBackupPluginSvc**. Questo account viene usato per il backup e il ripristino e richiede le autorizzazioni sysadmin SQL.
- Backup di Azure sfrutta l'account **NT AUTHORITY\SYSTEM** per l'individuazione o l'interrogazione dei database, quindi questo account deve essere un account di accesso pubblico in SQL.

Se la VM SQL Server non è stata creata da Azure Marketplace, è possibile ricevere un errore **UserErrorSQLNoSysadminMembership**. In tal caso [seguire queste istruzioni](backup-azure-sql-database.md#set-vm-permissions).

### <a name="verify-database-naming-guidelines-for-azure-backup"></a>Verificare le linee guida per la denominazione dei database per Backup di Azure

Evitare quanto segue per i nomi dei database:

  * Spazi finali/iniziali
  * Punto esclamativo (!) finale
  * Parentesi quadra chiusa ']'
  * Nomi di database iniziano con "F:\"

È previsto l'uso di alias per i caratteri non supportati dalle tabelle di Azure, ma è consigliabile evitare tali caratteri. [Altre informazioni](https://docs.microsoft.com/rest/api/storageservices/Understanding-the-Table-Service-Data-Model?redirectedfrom=MSDN)


[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="discover-sql-server-databases"></a>Individuare i database SQL Server

Individuare i database in esecuzione nella macchina virtuale.

1. Nel [portale di Azure](https://portal.azure.com) aprire l'insieme di credenziali di Servizi di ripristino usato per il backup del database.

2. Nel dashboard di **Insiemi di credenziali dei Servizi di ripristino** selezionare **Backup**.

   ![Selezionare Backup per aprire il menu Obiettivo di backup](./media/backup-azure-sql-database/open-backup-menu.png)

3. Nel menu **Obiettivo di backup** impostare **Posizione di esecuzione del carico di lavoro** su **Azure** (valore predefinito).

4. In **Elementi di cui eseguire il backup** selezionare **SQL Server in una macchina virtuale di Azure**.

    ![Selezionare SQL Server in macchine virtuali di Azure per il backup](./media/backup-azure-sql-database/choose-sql-database-backup-goal.png)

5. In **Obiettivo di backup** > **Individuare i database nelle macchine virtuali** selezionare **Avvia individuazione** per cercare le macchine virtuali non protette nella sottoscrizione. Questa operazione può richiedere del tempo, a seconda del numero di macchine virtuali non protette nella sottoscrizione.

   - Le VM non protette verranno visualizzate nell'elenco dopo l'individuazione, elencate per nome e gruppo di risorse.
   - Se una VM non è elencata come previsto, verificare se ne è già stato eseguito il backup in un insieme di credenziali.
   - Più macchine virtuali possono avere lo stesso nome, ma in questo caso appartengono a gruppi di risorse differenti.

     ![Il backup è in sospeso durante la ricerca dei database nelle macchine virtuali](./media/backup-azure-sql-database/discovering-sql-databases.png)

6. Nell'elenco delle macchine virtuali selezionare la VM nella quale è in esecuzione il database SQL Server e fare clic su **Individua database**.

7. Tenere traccia dell'individuazione dei database nell'area **Notifiche**. A seconda del numero di database presenti nella macchina virtuale, il completamento del processo può richiedere del tempo. Quando i database selezionati sono stati individuati, viene visualizzato un messaggio di conferma.

    ![Messaggio di distribuzione riuscita](./media/backup-azure-sql-database/notifications-db-discovered.png)

8. Backup di Azure individua tutti i database SQL Server presenti nella macchina virtuale. Durante l'individuazione vengono eseguite queste operazioni in background:

    - Backup di Azure registra la VM con l'insieme di credenziali per il backup del carico di lavoro. Tutti i database presenti nella VM registrata possono essere sottoposti a backup solo in questo insieme di credenziali.
    - Backup di Azure installa l'estensione **AzureBackupWindowsWorkload** nella macchina virtuale. Nel database SQL non viene installato alcun agente.
    - Backup di Azure crea l'account del servizio **NT Service\AzureWLBackupPluginSvc** nella macchina virtuale.
      - Tutte le operazioni di backup e ripristino usano l'account del servizio.
      - Per **NT Service\AzureWLBackupPluginSvc** sono necessarie le autorizzazioni sysadmin SQL. Tutte le macchine virtuali SQL Server create in Azure Marketplace vengono fornite con **SqlIaaSExtension** installato. L'estensione **AzureBackupWindowsWorkload** utilizza l'estensione **SQLIaaSExtension** per ottenere automaticamente le autorizzazioni richieste.
    - Se la VM non è stata creata dal Marketplace, la VM non ha **SqlIaaSExtension** installata e l'operazione di individuazione ha esito negativo con il messaggio di errore **UserErrorSQLNoSysAdminMembership**. Seguire le [istruzioni](backup-azure-sql-database.md#set-vm-permissions) per risolvere il problema.

        ![Selezionare la macchina virtuale e il database](./media/backup-azure-sql-database/registration-errors.png)

## <a name="configure-backup"></a>Configurare il backup  

Configurare il backup come segue:

1. In **Obiettivo di backup** selezionare **Configura backup**.

   ![Selezionare Configurare il backup](./media/backup-azure-sql-database/backup-goal-configure-backup.png)

2. Fare clic su **Configura backup**. Viene visualizzato il pannello **Seleziona elementi per backup**. Vi sono elencati tutti i gruppi di disponibilità registrati e i server SQL autonomi. Espandere la freccia di espansione a sinistra della riga per visualizzare tutti i database non protetti in tale istanza o gruppo di disponibilità Always On.  

    ![Visualizzazione di tutte le istanze di SQL Server con i database autonomi](./media/backup-azure-sql-database/list-of-sql-databases.png)

3. Selezionare tutti i database da proteggere e fare clic su **OK**.

   ![Protezione del database](./media/backup-azure-sql-database/select-database-to-protect.png)

   Per ottimizzare i carichi di backup, Backup di Azure imposta il numero massimo di database in un processo di backup su 50.

    
     * In alternativa, è possibile abilitare la protezione automatica sull'intera istanza o sul gruppo di disponibilità Always On selezionando l'opzione **ON** nell'elenco a discesa corrispondente nella colonna **AUTOPROTECT**. La funzionalità di protezione automatica non solo abilita la protezione per tutti i database esistenti in un'unica operazione, ma protegge anche automaticamente tutti i nuovi database che verranno aggiunti in futuro a tale istanza o gruppo di disponibilità.  

4. Fare clic su **OK** per aprire il pannello **Criteri di backup**.

    ![Abilitare la protezione automatica per il gruppo di disponibilità AlwaysOn](./media/backup-azure-sql-database/enable-auto-protection.png)

5. In **Scegliere i criteri di backup** selezionare un criterio e quindi fare clic su **OK**.

   - Selezionare i criteri predefiniti: HourlyLogBackup.
   - Scegliere un criterio di backup creato in precedenza per SQL.
   - Definire un nuovo criterio basato sull'obiettivo del punto di ripristino (RPO) e sull'intervallo di conservazione.

     ![Selezionare il criterio di backup](./media/backup-azure-sql-database/select-backup-policy.png)

6. Nel menu **Backup** selezionare **Abilita backup**.

    ![Abilitare i criteri di backup scelti](./media/backup-azure-sql-database/enable-backup-button.png)

7. È possibile monitorare l'avanzamento della configurazione nell'area delle **notifiche** del portale.

    ![Area delle notifiche](./media/backup-azure-sql-database/notifications-area.png)

### <a name="create-a-backup-policy"></a>Creare un criterio di backup

Un criterio di backup definisce quando vengono acquisiti i backup e per quanto tempo vengono conservati.

- Un criterio viene creato a livello di insieme di credenziali.
- Più insiemi di credenziali possono usare gli stessi criteri di backup, ma è necessario applicare i criteri di backup a ogni insieme di credenziali.
- Quando si crea un criterio di backup, il backup completo giornaliero è l'impostazione predefinita.
- È possibile aggiungere un backup differenziale, ma solo se si configura l'esecuzione settimanale del backup completo.
- [Informazioni](backup-architecture.md#sql-server-backup-types) sui vari tipi criteri di backup.

Per creare un criterio di backup:

1. Nell'insieme di credenziali fare clic su **Criteri di Backup** > **Aggiungi**.
2. Nel menu **Aggiungi** fare clic su **SQL Server nella macchina virtuale di Azure**. Ciò definisce il tipo di criterio.

   ![Scegliere un tipo per il nuovo criterio di backup](./media/backup-azure-sql-database/policy-type-details.png)

3. In **Nome criterio** immettere un nome per il nuovo criterio.
4. Nel criterio **Backup completo** selezionare una **frequenza di backup** scegliendo **Giornaliero** o **Settimanale**.

   - Per **Giornaliero**, scegliere l'ora e il fuso orario per l'inizio del processo di backup.
   - È necessario eseguire un backup completo, in quanto non è possibile disattivare l'opzione **Backup completo**.
   - Fare clic su **Backup completo** per visualizzare il criterio.
   - Se si sceglie di eseguire backup completi giornalieri, non è possibile creare backup differenziali.
   - Per **Settimanale**, selezionare il giorno della settimana, l'ora e il fuso orario per l'inizio del processo di backup.

     ![Nuovi campi di criteri di backup](./media/backup-azure-sql-database/full-backup-policy.png)  

5. Per **Intervallo di conservazione**, per impostazione predefinita sono selezionate tutte le opzioni. Deselezionare gli eventuali limiti di intervallo di conservazione che non si vogliono usare e impostare gli intervalli da usare.

    - Il periodo di conservazione minimo di qualsiasi tipo di backup (completo/differenziale/del log) è di 7 giorni.
    - I punti di recupero vengono contrassegnati per la conservazione, in base al relativo intervallo. Ad esempio, se si seleziona un backup completo giornaliero, viene attivato solo un backup completo ogni giorno.
    - Il backup del giorno specifico viene contrassegnato e conservato in base all'intervallo di conservazione settimanale e alla conservazione settimanale.
    - L'intervallo di conservazione mensile e annuale si comporta allo stesso modo.

   ![Impostazione dell'intervallo di conservazione](./media/backup-azure-sql-database/retention-range-interval.png)

6. Nel menu del **criterio Backup completo** selezionare **OK** per accettare le impostazioni.
7. Per aggiungere un criterio di backup differenziale, selezionare **Backup differenziale**.

   ![Impostazioni dell'intervallo di conservazione](./media/backup-azure-sql-database/retention-range-interval.png)
   ![Aprire il menu del criterio di backup differenziale](./media/backup-azure-sql-database/backup-policy-menu-choices.png)

8. Nel **criterio Backup differenziale** selezionare **Abilita** per accedere alle opzioni di frequenza e conservazione.

    - Al massimo, è possibile attivare un backup differenziale al giorno.
    - I backup differenziali possono essere conservati al massimo per 180 giorni. Se è necessario conservarli più a lungo, usare i backup completi.

9. Selezionare **OK** per salvare il criterio e tornare al menu principale **Criteri di backup**.

10. Per aggiungere un criterio di backup del log transazionale, selezionare **Backup del log**.
11. In **Backup del log** selezionare **Abilita** e quindi impostare le opzioni di frequenza e conservazione. I backup del log possono verificarsi ogni 15 minuti ed essere conservati per un massimo di 35 giorni.
12. Selezionare **OK** per salvare il criterio e tornare al menu principale **Criteri di backup**.

    ![Modificare i criteri di backup del log](./media/backup-azure-sql-database/log-backup-policy-editor.png)

13. Nel menu **Criterio di backup**, scegliere se abilitare la **compressione dei backup SQL**.
    - La compressione è disabilitata per impostazione predefinita.
    - Nel back-end Backup di Azure usa la compressione di backup nativa di SQL.

14. Dopo aver completato le modifiche ai criteri di backup, selezionare **OK**.

## <a name="run-an-ad-hoc-backup"></a>Eseguire un backup ad hoc

1. Nell'insieme di credenziali di Servizi di ripristino scegliere Elementi di backup.
2. Fare clic su "SQL Server in una macchina virtuale di Azure".
3. Fare clic con il pulsante destro del mouse su un database e scegliere "Esegui backup ora".
4. Scegliere un tipo di backup (completo/differenziale/del log/solo copia completa) e la compressione (Abilita/Disabilita)
5. Scegliere OK per iniziare il backup.
6. Monitorare il processo di backup passando all'insieme di credenziali di Servizi di ripristino e scegliendo "Processi di backup".


## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato usato il portale di Azure per eseguire le operazioni seguenti:

> [!div class="checklist"]
> * Creare e configurare un insieme di credenziali.
> * Individuare i database e configurare i backup.
> * Configurare la protezione automatica per i database.
> * Eseguire un backup ad hoc.

Proseguire con l'esercitazione successiva sul ripristino di una macchina virtuale di Azure da disco.

> [!div class="nextstepaction"]
> [Ripristinare il backup di database SQL Server in macchine virtuali di Azure](./restore-sql-database-azure-vm.md)
 

