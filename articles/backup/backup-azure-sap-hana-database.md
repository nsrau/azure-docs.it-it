---
title: Eseguire il backup di un database SAP HANA in Azure con Backup di Azure
description: Questo articolo illustra come eseguire il backup di un database SAP HANA in macchine virtuali di Azure con il servizio Backup di Azure.
ms.topic: conceptual
ms.date: 11/12/2019
ms.openlocfilehash: 61a1dae356209126af47837ec7e8940c394b77a5
ms.sourcegitcommit: ac7ae29773faaa6b1f7836868565517cd48561b2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/25/2020
ms.locfileid: "88827053"
---
# <a name="back-up-sap-hana-databases-in-azure-vms"></a>Eseguire il backup di database SAP HANA nelle VM di Azure

I database SAP HANA sono carichi di lavoro critici che richiedono un obiettivo del punto di ripristino (RPO) basso e la conservazione a lungo termine. È possibile eseguire il backup di database SAP HANA in esecuzione nelle macchine virtuali (VM) di Azure usando [Backup di Azure](backup-overview.md).

Questo articolo illustra come eseguire il backup di database SAP HANA in esecuzione nelle VM di Azure in un insieme di credenziali di Servizi di ripristino di Backup di Azure.

In questo articolo si apprenderà come:
> [!div class="checklist"]
>
> * Creare e configurare un insieme di credenziali
> * Individuare i database
> * Configurare i backup
> * Eseguire un processo di backup su richiesta

>[!NOTE]
>A partire dal 1° agosto 2020, il backup di SAP HANA per RHEL (7.4, 7.6, 7.7 e 8.1) è disponibile a livello generale.

>[!NOTE]
>L'**eliminazione temporanea per SQL Server nella macchina virtuale di Azure e l'eliminazione temporanea per SAP HANA nei carichi di lavoro delle macchine virtuali di Azure** ora sono disponibili in anteprima.<br>
>Per iscriverti all'anteprima, scrivici all'indirizzo [AskAzureBackupTeam@microsoft.com](mailto:AskAzureBackupTeam@microsoft.com) .

## <a name="prerequisites"></a>Prerequisiti

Fare riferimento alle sezioni [Prerequisiti](tutorial-backup-sap-hana-db.md#prerequisites) e [Funzionalità dello script di pre-registrazione](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does) per configurare il database per il backup.

### <a name="establish-network-connectivity"></a>Stabilire la connettività di rete

Per tutte le operazioni, un database SAP HANA in esecuzione su una macchina virtuale di Azure richiede la connettività al servizio Backup di Azure, ad Archiviazione di Azure e ad Azure Active Directory. Questa operazione può essere eseguita usando endpoint privati oppure consentendo l'accesso agli indirizzi IP pubblici o FQDN richiesti. Non consentire una connettività corretta ai servizi di Azure necessari può causare errori nelle operazioni quali l'individuazione del database, la configurazione del backup, l'esecuzione di backup e il ripristino dei dati.

La tabella seguente elenca le varie alternative che è possibile usare per stabilire la connettività:

| **Opzione**                        | **Vantaggi**                                               | **Svantaggi**                                            |
| --------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| Endpoint privati                 | Backup su indirizzi IP privati all'interno della rete virtuale  <br><br>   Offerta di un controllo granulare lato rete e lato insieme di credenziali | [Costi](https://azure.microsoft.com/pricing/details/private-link/) standard per endpoint privati |
| Tag del servizio NSG                  | Gestione semplificata perché le modifiche degli intervalli vengono unite automaticamente   <br><br>   Nessun costo aggiuntivo | Utilizzabile solo con i gruppi di sicurezza di rete  <br><br>    Accesso consentito all'intero servizio |
| Tag FQDN di Firewall di Azure          | Gestione semplificata perché i FQDN necessari vengono gestiti automaticamente | Utilizzabile solo con Firewall di Azure                         |
| Accesso consentito a FQDN/IP del servizio | Nessun costo aggiuntivo   <br><br>  Funziona con tutti i firewall e le appliance di sicurezza di rete | È possibile che sia necessario accedere a un ampio set di indirizzi IP o FQDN   |
| Usare un proxy HTTP                 | Singolo punto di accesso Internet alle VM                       | Costi aggiuntivi per l'esecuzione di una VM con il software proxy         |

Altre informazioni sull'uso di queste opzioni sono condivise di seguito:

#### <a name="private-endpoints"></a>Endpoint privati

Gli endpoint privati consentono di connettersi in modo sicuro dai server all'interno di una rete virtuale nell'insieme di credenziali di Servizi di ripristino. L'endpoint privato usa un indirizzo IP dallo spazio indirizzi della rete virtuale per l'insieme di credenziali. Il traffico di rete tra le risorse all'interno della rete virtuale e l'insieme di credenziali si sposta attraverso la rete virtuale e un collegamento privato nella rete backbone Microsoft. In questo modo si elimina l'esposizione dalla rete Internet pubblica. Altre informazioni sugli endpoint privati per Backup di Azure sono disponibili [qui](./private-endpoints.md).

#### <a name="nsg-tags"></a>Tag NSG

Se si usano gruppi di sicurezza di rete (NSG), usare il tag del servizio *AzureBackup* per consentire l'accesso in uscita a Backup di Azure. Oltre al tag di Backup di Azure, è necessario consentire la connettività per l'autenticazione e il trasferimento dei dati creando [regole NSG](../virtual-network/security-overview.md#service-tags) simili per *Azure AD* e *Archiviazione di Azure*.  I passaggi seguenti descrivono il processo di creazione di una regola per il tag di Backup di Azure:

1. In **Tutti i servizi**, passare a **Gruppi di sicurezza di rete** e selezionare il gruppo di sicurezza di rete.

1. In **Impostazioni** selezionare **Regole di sicurezza in uscita**.

1. Selezionare **Aggiungi**. Immettere tutti i dettagli necessari per la creazione di una nuova regola, come descritto nelle [impostazioni delle regole di sicurezza](../virtual-network/manage-network-security-group.md#security-rule-settings). Assicurarsi che l'opzione **Destinazione** sia impostata su *Tag del servizio* e che l'opzione **Tag del servizio di destinazione** sia impostata su *AzureBackup*.

1. Fare clic su **Aggiungi** per salvare la regola di sicurezza in uscita appena creata.

È possibile creare in modo analogo le regole di sicurezza NSG in uscita NSG per Archiviazione di Azure e Azure AD. Per altre informazioni sui tag di servizio, vedere [questo articolo](../virtual-network/service-tags-overview.md).

#### <a name="azure-firewall-tags"></a>Tag del Firewall di Azure

Se si usa Firewall di Azure, creare una regola dell'applicazione usando il tag [FQDN di Firewall di Azure](../firewall/fqdn-tags.md) *AzureBackup*. In questo modo si consente l'accesso in uscita a Backup di Azure.

#### <a name="allow-access-to-service-ip-ranges"></a>Consentire l'accesso agli intervalli IP del servizio

Se si decide di consentire l'accesso agli IP del servizio, fare riferimento agli intervalli IP nel file JSON disponibile [qui](https://www.microsoft.com/download/confirmation.aspx?id=56519). È necessario consentire l'accesso agli indirizzi IP corrispondenti a Backup di Azure, Archiviazione di Azure e Azure Active Directory.

#### <a name="allow-access-to-service-fqdns"></a>Accesso consentito a FQDN del servizio

È anche possibile usare gli FQDN seguenti per consentire l'accesso ai servizi richiesti dai server:

| Service    | Nomi di dominio a cui accedere                             |
| -------------- | ------------------------------------------------------------ |
| Backup di Azure  | `*.backup.windowsazure.com`                             |
| Archiviazione di Azure | `*.blob.core.windows.net` <br><br> `*.queue.core.windows.net` |
| Azure AD      | Consentire l'accesso ai nomi di dominio completo (FQDN) nelle sezioni 56 e 59 come indicato in [questo articolo](/office365/enterprise/urls-and-ip-address-ranges#microsoft-365-common-and-office-online) |

#### <a name="use-an-http-proxy-server-to-route-traffic"></a>Usare un server proxy HTTP per instradare il traffico

Quando si esegue il backup di un database SAP HANA in una macchina virtuale di Azure, l'estensione di backup nella VM usa le API HTTPS per inviare i comandi di gestione a Backup di Azure e i dati ad Archiviazione di Azure. L'estensione di backup usa anche Azure AD per l'autenticazione. Eseguire il routing del traffico di estensione per il backup di questi tre servizi attraverso il proxy HTTP. Usare l'elenco di indirizzi IP e FQDN indicati in precedenza per consentire l'accesso ai servizi necessari. I server proxy autenticati non sono supportati.

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="discover-the-databases"></a>Individuare i database

1. Nell'insieme di credenziali, in **Attività iniziali**, fare clic su **Backup**. In **Posizione di esecuzione del carico di lavoro** selezionare **SAP HANA in una macchina virtuale di Azure**.
2. Fare clic su **Avvia individuazione**. Viene avviata l'individuazione delle macchine virtuali Linux non protette nell'area dell'insieme di credenziali.

   * Le VM non protette verranno visualizzate nel portale dopo l'individuazione, elencate per nome e gruppo di risorse.
   * Se una VM non è elencata come previsto, verificare se ne è già stato eseguito il backup in un insieme di credenziali.
   * Più macchine virtuali possono avere lo stesso nome, ma in questo caso appartengono a gruppi di risorse differenti.

3. In **Seleziona macchine virtuali** fare clic sul collegamento per scaricare lo script che fornisce le autorizzazioni per il servizio Backup di Azure per accedere alle macchine virtuali SAP HANA per l'individuazione del database.
4. Eseguire lo script in ogni macchina virtuale che ospita i database SAP HANA di cui eseguire il backup.
5. Dopo aver eseguito lo script nelle macchine virtuali, selezionarle in **Seleziona macchine virtuali**. Quindi fare clic su **Individua database**.
6. Backup di Azure individua tutti i database SAP HANA presenti nella macchina virtuale. Durante l'individuazione, Backup di Azure registra la VM con l'insieme di credenziali e installa l'estensione. Nel database non vengono installati agenti.

    ![Individuare i database SAP HANA](./media/backup-azure-sap-hana-database/hana-discover.png)

## <a name="configure-backup"></a>Configurare il backup  

Ora abilitare il backup.

1. Nel passaggio 2 fare clic su **Configura backup**.

    ![Configurazione di backup](./media/backup-azure-sap-hana-database/configure-backup.png)
2. In **Seleziona gli elementi per il backup** selezionare tutti i database da proteggere e fare clic su **OK**.

    ![Seleziona elementi per backup](./media/backup-azure-sap-hana-database/select-items.png)
3. In **Criteri di backup** > **Scegliere i criteri di backup** creare un nuovo criterio di backup per i database, seguendo le istruzioni seguenti.

    ![Scegliere i criteri di backup](./media/backup-azure-sap-hana-database/backup-policy.png)
4. Dopo aver creato il criterio, nel menu **Backup** fare clic su **Abilita backup**.

    ![Abilita backup](./media/backup-azure-sap-hana-database/enable-backup.png)
5. Per tenere traccia dello stato di avanzamento della configurazione di backup, vedere l'area **Notifiche** del portale.

### <a name="create-a-backup-policy"></a>Creare un criterio di backup

Un criterio di backup definisce quando vengono acquisiti i backup e per quanto tempo vengono conservati.

* Un criterio viene creato a livello di insieme di credenziali.
* Più insiemi di credenziali possono usare gli stessi criteri di backup, ma è necessario applicare i criteri di backup a ogni insieme di credenziali.

>[!NOTE]
>Backup di Azure non si adatta automaticamente al cambiamento dell'ora legale per il backup di un database SAP HANA in esecuzione in una VM di Azure.
>
>Modificare manualmente i criteri in base alle esigenze.

Specificare le impostazioni del criterio come segue:

1. In **Nome criterio** immettere un nome per il nuovo criterio.

   ![Immettere il nome dei criteri](./media/backup-azure-sap-hana-database/policy-name.png)
2. Nel criterio **Backup completo** selezionare una **frequenza di backup** scegliendo **Giornaliero** o **Settimanale**.
   * **Giornaliera**: Scegliere l'ora e il fuso orario per l'inizio del processo di backup.
       * È necessario eseguire un backup completo. Non è possibile disattivare questa opzione.
       * Fare clic su **Backup completo** per visualizzare il criterio.
       * Se si sceglie di eseguire backup completi giornalieri, non è possibile creare backup differenziali.
   * **Settimanale**: selezionare il giorno della settimana, l'ora e il fuso orario per l'inizio del processo di backup.

   ![Selezionare una frequenza di backup](./media/backup-azure-sap-hana-database/backup-frequency.png)

3. In **Intervallo conservazione** configurare le impostazioni di conservazione per il backup completo.
    * Per impostazione predefinita, sono selezionate tutte le opzioni. Deselezionare gli eventuali limiti dell'intervallo di conservazione che non si vogliono usare e impostare quelli da usare.
    * Il periodo di conservazione minimo di qualsiasi tipo di backup (completo/differenziale/del log) è di sette giorni.
    * I punti di recupero vengono contrassegnati per la conservazione, in base al relativo intervallo. Ad esempio, se si seleziona un backup completo giornaliero, viene attivato solo un backup completo ogni giorno.
    * Il backup di un giorno specifico viene contrassegnato e conservato in base all'intervallo e all'impostazione di conservazione settimanale.
    * L'intervallo di conservazione mensile e annuale si comporta allo stesso modo.

4. Nel menu **Criteri di backup completo** fare clic su **OK** per accettare le impostazioni.
5. Selezionare **Backup differenziale** per aggiungere un criterio differenziale.
6. Nel **criterio Backup differenziale** selezionare **Abilita** per accedere alle opzioni di frequenza e conservazione.
    * Al massimo, è possibile attivare un backup differenziale al giorno.
    * I backup differenziali possono essere conservati al massimo per 180 giorni. Se è necessario conservarli più a lungo, usare i backup completi.

    ![Criteri di backup differenziale](./media/backup-azure-sap-hana-database/differential-backup-policy.png)

    > [!NOTE]
    > I backup incrementali non sono attualmente supportati.

7. Fare clic su **OK** per salvare il criterio e tornare nel menu principale **Criteri di backup**.
8. Selezionare **Backup del log** per aggiungere un criterio per i backup del log delle transazioni.
    * In **Backup del log** selezionare **Abilita**.  Questa operazione non può essere disabilitata, dal momento che SAP HANA gestisce tutti i backup del log.
    * Impostare la frequenza e i controlli di conservazione.

    > [!NOTE]
    > I backup del log iniziano a fluire solo dopo il corretto completamento di un backup completo.

9. Fare clic su **OK** per salvare il criterio e tornare nel menu principale **Criteri di backup**.
10. Dopo aver completato la definizione dei criteri di backup, fare clic su **OK**.

> [!NOTE]
> Ogni backup del log viene concatenato al backup completo precedente per formare una catena di recupero. Questo backup completo verrà mantenuto fino alla scadenza della conservazione dell'ultimo backup del log. Questo potrebbe significare che il backup completo viene mantenuto per un periodo aggiuntivo per assicurarsi che tutti i log possano essere ripristinati. Si supponga che l'utente disponga di log di un backup completo settimanale, giornaliero differenziale e ogni 2 ore. Vengono tutti conservati per 30 giorni. Tuttavia, la versione settimanale completa può essere effettivamente eliminata o eliminata solo dopo i successivi backup completi, ovvero dopo 30 + 7 giorni. Per esempio, un backup completo settimanale si verifica il 16 novembre. In base ai criteri di conservazione, deve essere mantenuto fino al 16 dicembre. L'ultimo backup del log per questa versione completa si verifica prima del successivo completo in programma, il 22 novembre. Questo log è disponibile fino al 22 dicembre e fino a quel momento non sarà possibile eliminare il backup completo del 16 novembre. Il backup completo del 16 novembre, quindi, viene mantenuto fino al 22 dicembre.

## <a name="run-an-on-demand-backup"></a>Eseguire un backup su richiesta

I backup vengono eseguiti in base alla pianificazione dei criteri. È possibile eseguire un backup su richiesta nel modo seguente:

1. Selezionare **Elementi di backup** dal menu dell'insieme di credenziali.
2. In **Elementi di backup** selezionare la macchina virtuale che esegue il database SAP HANA, quindi fare clic su **Esegui backup**.
3. In **backup ora**scegliere il tipo di backup che si desidera eseguire. Fare quindi clic su **OK**. Questo backup verrà mantenuto in base ai criteri associati a questo elemento di backup.
4. Monitorare le notifiche del portale. È possibile monitorare l'avanzamento del processo nel dashboard dell'insieme di credenziali > **Processi di Backup** > **In corso**. A seconda delle dimensioni del database, la creazione del backup iniziale potrebbe richiedere un po' di tempo.

## <a name="run-sap-hana-studio-backup-on-a-database-with-azure-backup-enabled"></a>Eseguire il backup di SAP HANA Studio in un database con Backup di Azure abilitato

Per eseguire il backup locale (tramite HANA Studio) di un database di cui è stato eseguito il backup con Backup di Azure, procedere come indicato di seguito:

1. Attendere il completamento di tutti i backup del log o completi per il database. Verificare lo stato in SAP HANA Studio/Cockpit.
1. Disabilitare i backup del log e impostare il catalogo di backup sul file system per il database pertinente.
1. A tal fine, fare doppio clic su **systemdb** > **Configurazione** > **Seleziona database** > **Filtro (log)** .
1. Impostare **enable_auto_log_backup** su **No**.
1. Impostare **log_backup_using_backint** su **False**.
1. Impostare **catalog_backup_using_backint** su **false**.
1. Eseguire un backup completo su richiesta del database.
1. Attendere il completamento del backup completo e del catalogo.
1. Ripristinare le impostazioni precedenti a quelle per Azure:
    * Impostare **enable_auto_log_backup** su **Yes**.
    * Impostare **log_backup_using_backint** su **True**.
    * Impostare **catalog_backup_using_backint** su **true**.

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su come [ripristinare i database SAP HANA in esecuzione nelle VM di Azure](./sap-hana-db-restore.md)
* Informazioni su come [gestire i database SAP HANA di cui è stato eseguito il backup con Backup di Azure](./sap-hana-db-manage.md)
