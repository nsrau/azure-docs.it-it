---
title: Creare e configurare insiemi di credenziali dei servizi di ripristino
description: Questo articolo illustra come creare e configurare insiemi di credenziali dei servizi di ripristino in cui archiviare i backup e i punti di ripristino. Informazioni su come usare il ripristino tra aree per ripristinare in un'area secondaria.
ms.topic: conceptual
ms.date: 05/30/2019
ms.custom: references_regions
ms.openlocfilehash: c7e7509f18e7511cd60099a70785b4e2c890afad
ms.sourcegitcommit: 2989396c328c70832dcadc8f435270522c113229
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/19/2020
ms.locfileid: "92171632"
---
# <a name="create-and-configure-a-recovery-services-vault"></a>Creare e configurare un insieme di credenziali di servizi di ripristino

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="set-storage-redundancy"></a>Impostare la ridondanza di archiviazione

Backup di Azure gestisce automaticamente lo spazio di archiviazione per l'insieme di credenziali. È necessario specificare la modalità di replica dell'archiviazione.

> [!NOTE]
> È necessario modificare il **tipo di replica di archiviazione** (con ridondanza locale o con ridondanza geografica) per un insieme di credenziali di servizi di ripristino prima di configurare i backup nell'insieme di credenziali. Dopo aver configurato il backup, l'opzione per la modifica è disabilitata.
>
>- Se il backup non è ancora stato configurato, [attenersi alla procedura seguente](#set-storage-redundancy) per esaminare e modificare le impostazioni.
>- Se il backup è già stato configurato e deve essere spostato da GRS a con ridondanza locale, [esaminare queste soluzioni alternative](#how-to-change-from-grs-to-lrs-after-configuring-backup).

1. Dal riquadro insiemi di credenziali dei **servizi di ripristino** selezionare il nuovo insieme di credenziali. Nella sezione **Impostazioni** selezionare  **Proprietà**.
1. In **Proprietà**, in **configurazione backup**, selezionare **Aggiorna**.

1. Selezionare il tipo di replica di archiviazione e selezionare **Salva**.

     ![Impostare la configurazione dell'archiviazione per il nuovo insieme di credenziali](./media/backup-create-rs-vault/recovery-services-vault-backup-configuration.png)

   - Se si usa Azure come endpoint di archiviazione di backup primario, è consigliabile continuare a usare l'impostazione con **ridondanza geografica** predefinita.
   - Se non si usa Azure come endpoint di archiviazione di backup primario, scegliere l'opzione **Con ridondanza locale**, che riduce i costi di archiviazione di Azure.
   - Altre informazioni sulla ridondanza [geografica](../storage/common/storage-redundancy.md#geo-redundant-storage) e [locale](../storage/common/storage-redundancy.md#locally-redundant-storage) .
   - Se è necessaria la disponibilità dei dati senza tempi di inattività in un'area, garantendo la residenza dei dati, scegliere l' [archiviazione con ridondanza della zona](../storage/common/storage-redundancy.md#zone-redundant-storage).

>[!NOTE]
>Le impostazioni di replica di archiviazione per l'insieme di credenziali non sono rilevanti per il backup della condivisione file di Azure perché la soluzione corrente è basata su snapshot e non ci sono dati trasferiti nell'insieme di credenziali. Gli snapshot vengono archiviati nello stesso account di archiviazione della condivisione file di cui è stato eseguito il backup.

## <a name="set-cross-region-restore"></a>Imposta ripristino tra aree

L'opzione di ripristino **Cross Region Restore (CRR)** consente di ripristinare i dati in un' [area associata](../best-practices-availability-paired-regions.md)secondaria di Azure.

Supporta le origini dati seguenti:

- Macchine virtuali di Azure
- Database SQL ospitati in macchine virtuali di Azure
- SAP HANA database ospitati in macchine virtuali di Azure

L'uso del ripristino tra aree consente di:

- eseguire esercitazioni in caso di requisiti di controllo o di conformità
- ripristinare i dati in caso di emergenza nell'area primaria

Quando si ripristina una macchina virtuale, è possibile ripristinare la macchina virtuale o il relativo disco. Se si esegue il ripristino da database SQL/SAP HANA ospitati in macchine virtuali di Azure, è possibile ripristinare i database o i relativi file.

Per scegliere questa funzionalità, selezionare **Abilita ripristino tra aree** dal riquadro **configurazione backup** .

Poiché questo processo è a livello di archiviazione, esistono [implicazioni](https://azure.microsoft.com/pricing/details/backup/)relative ai prezzi.

>[!NOTE]
>Prima di iniziare:
>
>- Esaminare la [matrice di supporto](backup-support-matrix.md#cross-region-restore) per un elenco di tipi e aree gestiti supportati.
>- La funzionalità di ripristino tra aree (CRR) è ora visualizzata in anteprima in tutte le aree pubbliche e nei cloud sovrani di Azure.
>- CRR è una funzionalità di consenso esplicito a livello di insieme di credenziali GRS (disattivata per impostazione predefinita).
>- Dopo aver acconsentito, potrebbero essere necessarie fino a 48 ore affinché gli elementi di backup siano disponibili nelle aree secondarie.
>- Attualmente CRR per le macchine virtuali di Azure è supportato solo per le macchine virtuali di Azure Resource Manager di Azure. Le macchine virtuali di Azure classiche non saranno supportate.  Quando i tipi di gestione aggiuntivi supportano CRR, verranno registrati **automaticamente** .
>- Il ripristino tra aree attualmente non può essere ripristinato in GRS o con ridondanza locale una volta avviata la protezione per la prima volta.

### <a name="configure-cross-region-restore"></a>Configurare il ripristino tra aree

Un insieme di credenziali creato con ridondanza GRS include l'opzione per configurare la funzionalità di ripristino tra aree. Ogni insieme di credenziali GRS avrà un banner, che sarà collegato alla documentazione. Per configurare CRR per l'insieme di credenziali, passare al riquadro Configurazione backup che contiene l'opzione per abilitare questa funzionalità.

 ![Banner di configurazione di backup](./media/backup-azure-arm-restore-vms/banner.png)

1. Dal portale passare a insieme di credenziali di servizi di ripristino > impostazioni > proprietà.
2. Per abilitare la funzionalità, selezionare **Abilita ripristino tra aree in questo** insieme di credenziali.

   ![Abilita ripristino tra aree](./media/backup-azure-arm-restore-vms/backup-configuration.png)

Per ulteriori informazioni su backup e ripristino con CRR, vedere questi articoli:

- [Ripristino tra aree per macchine virtuali di Azure](backup-azure-arm-restore-vms.md#cross-region-restore)
- [Ripristino tra aree per database SQL](restore-sql-database-azure-vm.md#cross-region-restore)
- [Ripristino tra aree per database SAP HANA](sap-hana-db-restore.md#cross-region-restore)

## <a name="set-encryption-settings"></a>Imposta impostazioni di crittografia

Per impostazione predefinita, i dati nell'insieme di credenziali di servizi di ripristino vengono crittografati usando chiavi gestite dalla piattaforma. Non è richiesta alcuna azione esplicita da parte dell'utente per abilitare questa crittografia e si applica a tutti i carichi di lavoro di cui viene eseguito il backup nell'insieme di credenziali di servizi di ripristino.  È possibile scegliere di importare la propria chiave per crittografare i dati di backup in questo insieme di credenziali. Questa operazione viene definita chiavi gestite dal cliente. Se si vuole crittografare i dati di backup usando una chiave personalizzata, è necessario specificare la chiave di crittografia prima che qualsiasi elemento sia protetto da questo insieme di credenziali. Una volta abilitata la crittografia con la chiave, non può essere invertita.

### <a name="configuring-a-vault-to-encrypt-using-customer-managed-keys"></a>Configurazione di un insieme di credenziali per la crittografia tramite chiavi gestite dal cliente

Per configurare l'insieme di credenziali per la crittografia con chiavi gestite dal cliente, è necessario seguire questi passaggi nell'ordine seguente:

1. Abilitare l'identità gestita per l'insieme di credenziali di servizi di ripristino

1. Assegnare le autorizzazioni all'insieme di credenziali per accedere alla chiave di crittografia nel Azure Key Vault

1. Abilitare l'eliminazione temporanea e ripulire la protezione sul Azure Key Vault

1. Assegnare la chiave di crittografia all'insieme di credenziali dei servizi di ripristino

Le istruzioni per ognuno di questi passaggi sono disponibili [in questo articolo](encryption-at-rest-with-cmk.md#configuring-a-vault-to-encrypt-using-customer-managed-keys).

## <a name="modifying-default-settings"></a>Modifica delle impostazioni predefinite

È consigliabile rivedere le impostazioni predefinite di **Tipo di replica di archiviazione** e **Impostazioni di sicurezza** prima di configurare i backup nell'insieme di credenziali.

- Per impostazione predefinita, il **tipo di replica di archiviazione** è impostato su con **ridondanza geografica** (GRS). Dopo aver configurato il backup, l'opzione per la modifica è disabilitata.
  - Se il backup non è ancora stato configurato, [attenersi alla procedura seguente](#set-storage-redundancy) per esaminare e modificare le impostazioni.
  - Se il backup è già stato configurato e deve essere spostato da GRS a con ridondanza locale, [esaminare queste soluzioni alternative](#how-to-change-from-grs-to-lrs-after-configuring-backup).

- Per impostazione predefinita, l' **eliminazione** temporanea è **abilitata** negli insiemi di credenziali appena creati per proteggere i dati di backup da eliminazioni accidentali o dannose. Per esaminare e modificare le impostazioni, [seguire questa procedura](./backup-azure-security-feature-cloud.md#enabling-and-disabling-soft-delete) .

### <a name="how-to-change-from-grs-to-lrs-after-configuring-backup"></a>Come passare da GRS a con ridondanza locale dopo aver configurato il backup

Prima di decidere di passare da GRS ad archiviazione con ridondanza locale (con ridondanza locale), esaminare i compromessi tra i costi più bassi e la durabilità dei dati più elevata adatta allo scenario. Se è necessario passare da GRS a con ridondanza locale, sono disponibili due opzioni. Dipendono dai requisiti aziendali per conservare i dati di backup:

- [Non è necessario mantenere i dati di backup precedenti](#dont-need-to-preserve-previous-backed-up-data)
- [È necessario mantenere i dati di backup precedenti](#must-preserve-previous-backed-up-data)

#### <a name="dont-need-to-preserve-previous-backed-up-data"></a>Non è necessario mantenere i dati di backup precedenti

Per proteggere i carichi di lavoro in un nuovo insieme di credenziali con ridondanza locale, è necessario eliminare i dati e la protezione correnti nell'insieme di credenziali di GRS e i backup configurati di nuovo.

>[!WARNING]
>L'operazione seguente è distruttiva e non può essere annullata. Tutti i dati di backup e gli elementi di backup associati al server protetto verranno eliminati definitivamente. Procedere con cautela.

Arrestare ed eliminare la protezione corrente nell'insieme di credenziali di GRS:

1. Disabilitare l'eliminazione temporanea nelle proprietà dell'insieme di credenziali di GRS. Per disabilitare l'eliminazione temporanea, seguire [questa procedura](backup-azure-security-feature-cloud.md#disabling-soft-delete-using-azure-portal) .

1. Arrestare la protezione ed eliminare i backup dall'insieme di credenziali GRS esistente. Nel menu del dashboard dell'insieme di credenziali selezionare **elementi di backup**. Gli elementi elencati di seguito che devono essere spostati nell'insieme di credenziali con ridondanza locale devono essere rimossi insieme ai relativi dati di backup. Vedere come [eliminare gli elementi protetti nel cloud](backup-azure-delete-vault.md#delete-protected-items-in-the-cloud) ed [eliminare gli elementi protetti in locale](backup-azure-delete-vault.md#delete-protected-items-on-premises).

1. Se si prevede di spostare AFS (condivisioni file di Azure), SQL Server o server SAP HANA, sarà necessario anche annullarne la registrazione. Nel menu del dashboard dell'insieme di credenziali selezionare **infrastruttura di backup**. Vedere come annullare [la registrazione di SQL Server](manage-monitor-sql-database-backup.md#unregister-a-sql-server-instance), [annullare la registrazione di un account di archiviazione associato a condivisioni file di Azure](manage-afs-backup.md#unregister-a-storage-account)e [annullare la registrazione di un'istanza di SAP Hana](sap-hana-db-manage.md#unregister-an-sap-hana-instance).

1. Dopo essere stati rimossi dall'insieme di credenziali di GRS, continuare a configurare i backup per il carico di lavoro nel nuovo insieme di credenziali con ridondanza locale.

#### <a name="must-preserve-previous-backed-up-data"></a>È necessario mantenere i dati di backup precedenti

Se è necessario mantenere i dati protetti correnti nell'insieme di credenziali di GRS e continuare la protezione in un nuovo insieme di credenziali di con ridondanza locale, sono disponibili opzioni limitate per alcuni carichi di lavoro:

- Per MARS è possibile [arrestare la protezione con Mantieni dati](backup-azure-manage-mars.md#stop-protecting-files-and-folder-backup) e registrare l'agente nel nuovo con ridondanza locale Vault.

  - Il servizio backup di Azure continuerà a mantenere tutti i punti di ripristino esistenti dell'insieme di credenziali GRS.
  - Dovrai pagare per preservare i punti di ripristino nell'insieme di credenziali di GRS.
  - Sarà possibile ripristinare i dati di cui è stato eseguito il backup solo per i punti di ripristino non scaduti nell'insieme di credenziali di GRS.
  - Sarà necessario creare una nuova replica iniziale dei dati nell'insieme di credenziali con ridondanza locale.

- Per una macchina virtuale di Azure, è possibile [arrestare la protezione con Mantieni i dati](backup-azure-manage-vms.md#stop-protecting-a-vm) per la macchina virtuale nell'insieme di credenziali GRS, spostare la macchina virtuale in un altro gruppo di risorse e quindi proteggere la macchina virtuale nell'insieme di credenziali con ridondanza locale. Vedere [linee guida e limitazioni](../azure-resource-manager/management/move-limitations/virtual-machines-move-limitations.md) per lo stato di trasferimento di una macchina virtuale in un altro gruppo di risorse.

  Una macchina virtuale può essere protetta solo in un insieme di credenziali alla volta. Tuttavia, la macchina virtuale nel nuovo gruppo di risorse può essere protetta nell'insieme di credenziali di con ridondanza locale perché è considerata una macchina virtuale diversa.

  - Il servizio backup di Azure manterrà i punti di ripristino di cui è stato eseguito il backup nell'insieme di credenziali di GRS.
  - Dovrai pagare per mantenete i punti di ripristino nell'insieme di credenziali di GRS (vedere [prezzi di backup di Azure](azure-backup-pricing.md) per informazioni dettagliate).
  - Sarà possibile ripristinare la macchina virtuale, se necessario, dall'insieme di credenziali di GRS.
  - Il primo backup nell'insieme di credenziali con ridondanza locale della macchina virtuale nella nuova risorsa sarà una replica iniziale.

## <a name="next-steps"></a>Passaggi successivi

Informazioni [su](backup-azure-recovery-services-vault-overview.md) Insiemi di credenziali dei servizi di ripristino.
Informazioni [su](backup-azure-delete-vault.md) Eliminare gli insiemi di credenziali dei servizi di ripristino.