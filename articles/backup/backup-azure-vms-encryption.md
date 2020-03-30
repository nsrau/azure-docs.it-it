---
title: Eseguire il backup e il ripristino delle macchine virtuali di Azure crittografateBack up and restore encrypted Azure VMs
description: Descrive come eseguire il backup e il ripristino di macchine virtuali di Azure crittografate con il servizio Backup di Azure.Describes how to back up and restore encrypted Azure VMs with the Azure Backup service.
ms.topic: conceptual
ms.date: 04/03/2019
ms.openlocfilehash: a3976cc83f749b1abe00cef3f5bf867ffbc30ab6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78206691"
---
# <a name="back-up-and-restore-encrypted-azure-vm"></a>Eseguire il backup e il ripristino della macchina virtuale di Azure crittografataBack up and restore encrypted Azure VM

Questo articolo descrive come eseguire il backup e il ripristino di macchine virtuali (VM) di Windows o Linux Azure con dischi crittografati usando il servizio Backup di Azure.This article describes how to back up and restore Windows or Linux Azure virtual machines (VMs) with encrypted disks using the [Azure Backup](backup-overview.md) service.

Per altre informazioni sull'interazione di Backup di Azure con le macchine virtuali di Azure prima di iniziare, vedere queste risorse:If you want to learn more about how Azure Backup interacts with Azure VMs before you begin, review these resources:

- [Esaminare](backup-architecture.md#architecture-built-in-azure-vm-backup) l'architettura di backup della macchina virtuale di Azure.Review the Azure VM backup architecture.
- [Ulteriori informazioni su](backup-azure-vms-introduction.md) Backup della macchina virtuale di Azure e estensione Di Backup di Azure.Azure VM backup, and the Azure Backup extension.

## <a name="encryption-support"></a>Supporto della crittografia

Backup di Azure supporta il backup delle macchine virtuali di Azure con i dischi os/dati crittografati con Crittografia disco di Azure.Azure Backup supports backup of Azure VMs that have their OS/data disks encrypted with Azure Disk Encryption (ADE). ADE utilizza BitLocker per la crittografia delle macchine virtuali Windows e la funzionalità dm-crypt per le macchine virtuali Linux. ADE si integra con l'insieme di credenziali delle chiavi di Azure per gestire le chiavi e i segreti di crittografia del disco. Le chiavi chiave di crittografia delle chiavi (KEK, Vault Key Encryption Keys) possono essere utilizzate per aggiungere un ulteriore livello di sicurezza, crittografando i segreti di crittografia prima di scriverli in Key Vault.

Backup di Azure può eseguire il backup e il ripristino delle macchine virtuali di Azure usando ADE con e senza l'app Azure AD, come riepilogato nella tabella seguente.

**Tipo di disco VM** | **ADE (BEK/dm-crypt)** | **ADE e KEK**
--- | --- | ---
**Non gestito** | Sì | Sì
**Gestito**  | Sì | Sì

- Ulteriori informazioni su [ADE,](../security/azure-security-disk-encryption-overview.md) [Key Vault](../key-vault/key-vault-overview.md)e [KEK](https://docs.microsoft.com/azure/virtual-machine-scale-sets/disk-encryption-key-vault#set-up-a-key-encryption-key-kek).
- Leggere le [domande frequenti sulla](../security/azure-security-disk-encryption-faq.md) crittografia del disco della macchina virtuale di Azure.Read the FAQ for Azure VM disk encryption.

### <a name="limitations"></a>Limitazioni

- È possibile eseguire il backup e il ripristino di macchine virtuali crittografate all'interno della stessa sottoscrizione e area.
- Backup di Azure supporta le macchine virtuali crittografate usando chiavi autonome. Qualsiasi chiave che fa parte di un certificato usato per crittografare una macchina virtuale non è attualmente supportata.
- È possibile eseguire il backup e il ripristino di macchine virtuali crittografate all'interno della stessa sottoscrizione e area dell'insieme di credenziali di backup dei servizi di ripristino.
- Le macchine virtuali crittografate non possono essere ripristinate a livello di file/cartella. È necessario ripristinare l'intera macchina virtuale per ripristinare file e cartelle.
- Quando si ripristina una macchina virtuale, non è possibile usare l'opzione [Sostituisci macchina virtuale esistente](backup-azure-arm-restore-vms.md#restore-options) per le macchine virtuali crittografate. Questa opzione è supportata solo per i dischi gestiti non crittografati.

## <a name="before-you-start"></a>Prima di iniziare

Prima di iniziare, eseguire le operazioni seguenti:

1. Assicurarsi di avere una o più macchine virtuali [Windows](../security/azure-security-disk-encryption-windows.md) o [Linux](../virtual-machines/linux/disk-encryption-overview.md) con ADE abilitato.
2. [Esaminare la matrice di supporto](backup-support-matrix-iaas.md) per il backup della macchina virtuale di AzureReview the support matrix for Azure VM backup
3. [Creare](backup-azure-arm-vms-prepare.md#create-a-vault) un insieme di credenziali di backup dei servizi di ripristino, se non è disponibile.
4. Se si abilita la crittografia per le macchine virtuali già abilitate per il backup, è sufficiente fornire a Backup le autorizzazioni per accedere all'insieme di credenziali delle chiavi in modo che i backup possano continuare senza interruzioni. [Ulteriori informazioni](#provide-permissions) sull'assegnazione di queste autorizzazioni.

Inoltre, ci sono un paio di cose che potrebbe essere necessario fare in alcune circostanze:

- **Installare l'agente di macchine virtuali nella macchina virtuale:** Backup di Azure esegue il backup delle macchine virtuali di Azure installando un'estensione dell'agente di macchine virtuali di Azure in esecuzione nel computer. Se la macchina virtuale è stata creata da un'immagine del marketplace di Azure, l'agente viene installato e in esecuzione. Se si crea una macchina virtuale personalizzata o si esegue la migrazione di un computer locale, potrebbe essere necessario [installare l'agente manualmente.](backup-azure-arm-vms-prepare.md#install-the-vm-agent)

## <a name="configure-a-backup-policy"></a>Configurare un criterio di backup

1. Se non è ancora stato creato un vault di backup di Servizi di ripristino, seguire [queste istruzioni](backup-azure-arm-vms-prepare.md#create-a-vault)
2. Aprire l'insieme di credenziali nel portale e selezionare **Backup** nella sezione **Guida introduttiva.**

    ![Pannello Backup](./media/backup-azure-vms-encryption/select-backup.png)

3. In **Obiettivo di backup** > Dove è in esecuzione il carico di lavoro? selezionare **Azure.In**Backup goal**Where is your workload running?** select Azure .
4. In **Cosa si desidera eseguire il backup selezionare** Macchina **Virtual machine** > virtuale**OK**.

      ![Pannello Scenario](./media/backup-azure-vms-encryption/select-backup-goal-one.png)

5. In **Criteri di** > **backup Scegliere**i criteri di backup selezionare il criterio che si desidera associare all'insieme di credenziali. Fare quindi clic su **OK**.
    - Un criterio di backup specifica quando vengono eseguiti i backup e per quanto tempo vengono archiviati.
    - I dettagli dei criteri predefiniti vengono elencati nel menu a discesa.

    ![Pannello Scenario aperto](./media/backup-azure-vms-encryption/select-backup-goal-two.png)

6. Se non si desidera utilizzare il criterio predefinito, selezionare **Crea nuovo**e creare un [criterio personalizzato.](backup-azure-arm-vms-prepare.md#create-a-custom-policy)

7. Scegliere le macchine virtuali crittografate di cui eseguire il backup utilizzando il criterio di selezione e selezionare **OK**.

      ![Selezionare le macchine virtuali crittografate](./media/backup-azure-vms-encryption/selected-encrypted-vms.png)

8. Se si usa L'insieme di credenziali delle chiavi di Azure, nella pagina dell'insieme di credenziali viene visualizzato un messaggio che indica che Azure Backup richiede l'accesso in sola lettura alle chiavi e ai segreti nell'insieme di credenziali delle chiavi.

    - Se viene visualizzato questo messaggio, non è necessaria alcuna azione.

        ![Accesso OK](./media/backup-azure-vms-encryption/access-ok.png)

    - Se viene visualizzato questo messaggio, è necessario impostare le autorizzazioni come descritto nella [procedura riportata di seguito.](#provide-permissions)

        ![Avviso di accesso](./media/backup-azure-vms-encryption/access-warning.png)

9. Fare clic su **Abilita backup** per distribuire i criteri di backup nell'insieme di credenziali e abilitare il backup per le macchine virtuali selezionate.

## <a name="trigger-a-backup-job"></a>Attivare un processo di backup

Il backup iniziale verrà eseguito in base alla pianificazione, ma è possibile eseguirlo immediatamente come segue:

1. Selezionare **Elementi di backup** dal menu dell'insieme di credenziali.
2. In **Elementi di backup**fare clic su Macchina virtuale di **Azure**.
3. Nell'elenco **Elementi di backup** fare clic sui puntini di sospensione (...).
4. Fare clic su **Esegui backup ora**.
5. In **Backup ora**, utilizzare il controllo calendario per selezionare l'ultimo giorno in cui deve essere mantenuto il punto di ripristino. Fare quindi clic su **OK**.
6. Monitorare le notifiche del portale. È possibile monitorare l'avanzamento del processo nel dashboard dell'insieme di credenziali > **processi** > di backup**in corso.** A seconda delle dimensioni della macchina virtuale, la creazione del backup iniziale potrebbe richiedere un po' di tempo.

## <a name="provide-permissions"></a>Fornire autorizzazioni

La macchina virtuale di Azure richiede l'accesso in sola lettura per eseguire il backup delle chiavi e dei segreti, insieme alle macchine virtuali associate.

- L'insieme di credenziali delle chiavi è associato al tenant di Azure AD della sottoscrizione di Azure.Your Key Vault is associated with the Azure AD tenant of the Azure subscription. Se si è un **utente membro,** Backup di Azure acquisisce l'accesso all'insieme di credenziali delle chiavi senza ulteriori azioni.
- Se si è un **utente Guest**, è necessario fornire le autorizzazioni per Backup di Azure per accedere all'insieme di credenziali delle chiavi.

Per impostare le autorizzazioni:

1. Nel portale di Azure selezionare **Tutti i servizi**e cercare Gli **insiemi**di credenziali delle chiavi .
2. Selezionare l'insieme di credenziali delle chiavi associato alla macchina virtuale crittografata di cui si sta esegue il backup.
3. Selezionare **Criteri di** > accesso**Aggiungi nuovo**.
4. Selezionare **Seleziona entità**, quindi digitare **Gestione backup**.
5. Selezionare **Backup Management Service** > **Select**.

    ![Selezione del servizio di backup](./media/backup-azure-vms-encryption/select-backup-service.png)

6. In **Aggiungi criteri di** > accesso**Configura dal modello (facoltativo)** selezionare **Backup di Azure.**
    - Le autorizzazioni necessarie sono precompilate per **Autorizzazioni chiave** e **Autorizzazioni segrete**.
    - Se la macchina virtuale è crittografata **solo tramite BEK**, rimuovere la selezione per **Autorizzazioni chiave** poiché sono necessarie solo le autorizzazioni per i segreti.

    ![Selezione di Backup di Azure](./media/backup-azure-vms-encryption/select-backup-template.png)

7. Fare clic su **OK**. **Il servizio di gestione dei backup** viene aggiunto ai criteri di **accesso**.

    ![Criteri di accesso](./media/backup-azure-vms-encryption/backup-service-access-policy.png)

8. Fare clic su **Salva** per fornire a Backup di Azure le autorizzazioni.

## <a name="restore-an-encrypted-vm"></a>Ripristinare una macchina virtuale crittografata

Le macchine virtuali crittografate vengono ripristinate come segue:You restore encrypted VMs as follows:

1. [Ripristinare il disco della macchina virtuale](backup-azure-arm-restore-vms.md#restore-disks).
2. Ricreare l'istanza della macchina virtuale eseguendo una delle operazioni seguenti:
    1. Usare il modello generato durante l'operazione di ripristino per personalizzare le impostazioni della macchina virtuale e attivare la distribuzione della macchina virtuale. [Scopri di più](backup-azure-arm-restore-vms.md#use-templates-to-customize-a-restored-vm).
    2. Creare una nuova macchina virtuale dai dischi ripristinati usando PowerShell.Create a new VM from the restored disks using PowerShell. [Scopri di più](backup-azure-vms-automation.md#create-a-vm-from-restored-disks).
3. Per le macchine virtuali Linux, reinstallare l'estensione ADE in modo che i dischi dati siano aperti e installati.

## <a name="next-steps"></a>Passaggi successivi

In caso di problemi, consulta questi articoli:

- [Errori comuni](backup-azure-vms-troubleshoot.md) durante il backup e il ripristino di macchine virtuali di Azure crittografate.
- [Problemi relativi all'agente vm Azure/estensione di backup.](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md)
