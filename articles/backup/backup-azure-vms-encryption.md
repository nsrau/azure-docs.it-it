---
title: Eseguire il backup e il ripristino di VM di Azure crittografate con backup di Azure
description: Viene descritto come eseguire il backup e il ripristino di VM di Azure crittografate con il servizio backup di Azure.
ms.reviewer: geg
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: dacurwin
ms.openlocfilehash: ac101c6079eee908a6499f5e6ed8cc3aadffb75a
ms.sourcegitcommit: d470d4e295bf29a4acf7836ece2f10dabe8e6db2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/02/2019
ms.locfileid: "70210306"
---
# <a name="back-up-and-restore-encrypted-azure-vm"></a>Eseguire il backup e il ripristino di VM di Azure crittografate

Questo articolo descrive come eseguire il backup e il ripristino di macchine virtuali (VM) Windows o Linux di Azure con dischi crittografati tramite il servizio [backup di Azure](backup-overview.md) .

Per altre informazioni sul modo in cui backup di Azure interagisce con le VM di Azure prima di iniziare, vedere le risorse seguenti:

- [Esaminare](backup-architecture.md#architecture-direct-backup-of-azure-vms) l'architettura di backup delle VM di Azure.
- Informazioni [su](backup-azure-vms-introduction.md) Backup delle macchine virtuali di Azure e l'estensione di backup di Azure.

## <a name="encryption-support"></a>Supporto della crittografia

Backup di Azure supporta il backup di macchine virtuali di Azure con i dischi del sistema operativo/dati crittografati con crittografia dischi di Azure (ADE). ADE USA BitLocker per la crittografia delle macchine virtuali Windows e la funzionalità dm-crypt per le macchine virtuali Linux. ADE si integra con Azure Key Vault per gestire le chiavi di crittografia dei dischi e i segreti. Key Vault chiavi di crittografia della chiave (KEKs) possono essere usate per aggiungere un ulteriore livello di sicurezza, crittografando i segreti di crittografia prima di scriverli Key Vault.

Backup di Azure può eseguire il backup e il ripristino di macchine virtuali di Azure usando ADE con e senza l'app Azure AD, come riepilogato nella tabella seguente.

**Tipo di disco VM** | **ADE (BEK/dm-crypt)** | **ADE e KEK**
--- | --- | ---
**Gestito** | Sì | Sì
**Gestito**  | Sì | Yes

- Scopri di più su [Ade](../security/azure-security-disk-encryption-overview.md), [Key Vault](../key-vault/key-vault-overview.md)e [KEKs](https://blogs.msdn.microsoft.com/cclayton/2017/01/03/creating-a-key-encrypting-key-kek/).
- Leggere le [domande frequenti](../security/azure-security-disk-encryption-faq.md) sulla crittografia del disco della macchina virtuale di Azure.



### <a name="limitations"></a>Limitazioni

- È possibile eseguire il backup e il ripristino di VM crittografate nella stessa sottoscrizione e nella stessa area.
- Backup di Azure supporta macchine virtuali crittografate con chiavi autonome. Qualsiasi chiave che fa parte di un certificato usato per crittografare una macchina virtuale non è attualmente supportata.
- È possibile eseguire il backup e il ripristino di VM crittografate nella stessa sottoscrizione e nella stessa area dell'insieme di credenziali per il backup dei servizi di ripristino.
- Le macchine virtuali crittografate non possono essere ripristinate a livello di file/cartella. È necessario ripristinare l'intera macchina virtuale per ripristinare file e cartelle.
- Quando si ripristina una macchina virtuale, non è possibile usare l'opzione [Sostituisci macchina virtuale esistente](backup-azure-arm-restore-vms.md#restore-options) per le macchine virtuali crittografate. Questa opzione è supportata solo per i dischi gestiti non crittografati.




## <a name="before-you-start"></a>Prima di iniziare

Prima di iniziare, eseguire le operazioni seguenti:

1. Assicurarsi di disporre di una o più VM [Windows](../security/azure-security-disk-encryption-windows.md) o [Linux](../security/azure-security-disk-encryption-linux.md) con Ade abilitato.
2. [Esaminare la matrice di supporto per il](backup-support-matrix-iaas.md) backup delle macchine virtuali di Azure
3. [Creare](backup-azure-arm-vms-prepare.md#create-a-vault) un insieme di credenziali per il backup dei servizi di ripristino, se non è già presente.
4. Se si Abilita la crittografia per le macchine virtuali che sono già abilitate per il backup, è sufficiente fornire il backup con le autorizzazioni per accedere al Key Vault in modo che i backup possano continuare senza alcuna distorsione. [Altre](#provide-permissions) informazioni sull'assegnazione di queste autorizzazioni.

Inoltre, in alcune circostanze potrebbe essere necessario eseguire alcune operazioni:

- **Installare l'agente di macchine virtuali nella macchina virtuale**: Backup di Azure esegue il backup di macchine virtuali di Azure tramite l'installazione di un'estensione per l'agente di macchine virtuali di Azure in esecuzione nel computer. Se la macchina virtuale è stata creata da un'immagine di Azure Marketplace, l'agente è installato e in esecuzione. Se si crea una VM personalizzata o si esegue la migrazione di un computer locale, potrebbe essere necessario [installare l'agente manualmente](backup-azure-arm-vms-prepare.md#install-the-vm-agent).
- **Consenti in modo esplicito l'accesso in uscita**: In genere, non è necessario consentire in modo esplicito l'accesso alla rete in uscita per una macchina virtuale di Azure per poter comunicare con backup di Azure. Tuttavia, alcune macchine virtuali potrebbero riscontrare problemi di connessione, mostrando l'errore **ExtensionSnapshotFailedNoNetwork** durante il tentativo di connessione. In tal caso, è necessario [consentire in modo esplicito l'accesso in uscita](backup-azure-arm-vms-prepare.md#explicitly-allow-outbound-access), in modo che l'estensione di backup di Azure possa comunicare con gli indirizzi IP pubblici di Azure per il traffico di backup.



## <a name="configure-a-backup-policy"></a>Configurare un criterio di backup

1. Se non è ancora stato creato un insieme di credenziali per il backup dei servizi di ripristino, seguire [queste istruzioni](backup-azure-arm-vms-prepare.md#create-a-vault)
2. Aprire l'insieme di credenziali nel portale e selezionare **backup** nella sezione **Introduzione** .

    ![Pannello Backup](./media/backup-azure-vms-encryption/select-backup.png)

3. In **obiettivo** > di backup in**cui è in esecuzione il carico di lavoro?** selezionare **Azure**.
4. In **che cosa si vuole eseguire il backup?** selezionare **macchina** > virtuale**OK**.

      ![Pannello Scenario](./media/backup-azure-vms-encryption/select-backup-goal-one.png)

5. In **criteri** > di backup**scegliere criteri di backup**, selezionare i criteri che si desidera associare all'insieme di credenziali. Fare quindi clic su **OK**.
    - Un criterio di backup specifica quando vengono eseguiti i backup e per quanto tempo vengono archiviati.
    - I dettagli dei criteri predefiniti vengono elencati nel menu a discesa.

    ![Pannello Scenario aperto](./media/backup-azure-vms-encryption/select-backup-goal-two.png)

6. Se non si vuole usare i criteri predefiniti, selezionare **Crea nuovo**e [creare un criterio personalizzato](backup-azure-arm-vms-prepare.md#create-a-custom-policy).


7. Scegliere le macchine virtuali crittografate di cui si vuole eseguire il backup usando i criteri di selezione e fare clic su **OK**.

      ![Selezionare le macchine virtuali crittografate](./media/backup-azure-vms-encryption/selected-encrypted-vms.png)

8. Se si usa Azure Key Vault, nella pagina dell'insieme di credenziali viene visualizzato un messaggio in cui backup di Azure richiede l'accesso in sola lettura alle chiavi e ai segreti nel Key Vault.

    - Se si riceve questo messaggio, non è richiesta alcuna azione.

        ![Accedi a OK](./media/backup-azure-vms-encryption/access-ok.png)

    - Se si riceve questo messaggio, è necessario impostare le autorizzazioni come descritto nella [procedura seguente](#provide-permissions).

        ![Avviso di accesso](./media/backup-azure-vms-encryption/access-warning.png)

9. Fare clic su **Abilita backup** per distribuire il criterio di backup nell'insieme di credenziali e abilitare il backup per le macchine virtuali selezionate.


## <a name="trigger-a-backup-job"></a>Attivare un processo di backup

Il backup iniziale viene eseguito in base alla pianificazione, ma è possibile eseguirlo immediatamente come indicato di seguito:

1. Selezionare **Elementi di backup** dal menu dell'insieme di credenziali.
2. In **Elementi di backup** fare clic su **Macchine virtuali di Azure**.
3. Nell'elenco **elementi di backup** fare clic sui puntini di sospensione (...).
4. Fare clic su **Esegui backup ora**.
5. In **backup ora**usare il controllo calendario per selezionare l'ultimo giorno in cui deve essere mantenuto il punto di ripristino. Fare quindi clic su **OK**.
6. Monitorare le notifiche del portale. È possibile monitorare l'avanzamento del processo nel dashboard dell'insieme di credenziali > **Processi di Backup** > **In corso**. A seconda delle dimensioni della macchina virtuale, la creazione del backup iniziale potrebbe richiedere un po' di tempo.


## <a name="provide-permissions"></a>Fornire le autorizzazioni

La macchina virtuale di Azure richiede l'accesso in sola lettura per eseguire il backup delle chiavi e dei segreti, insieme alle macchine virtuali associate.

- Il Key Vault è associato al tenant di Azure AD della sottoscrizione di Azure. Se si è un **utente membro**, backup di Azure acquisisce l'accesso al Key Vault senza ulteriori azioni.
- Se si è un **utente Guest**, è necessario fornire le autorizzazioni per il backup di Azure per accedere all'insieme di credenziali delle chiavi.

Per impostare le autorizzazioni:

1. Nella portale di Azure selezionare **tutti i servizi**e cercare **Key Vault**.
2. Selezionare l'insieme di credenziali delle chiavi associato alla macchina virtuale crittografata di cui si sta eseguendo il backup.
3. Selezionare **criteri** > di accesso**Aggiungi nuovo**.
4. Selezionare **Seleziona entità**, quindi digitare **Gestione backup**.
5. Selezionare **Backup Management Service** > **Select**.

    ![Selezione del servizio di backup](./media/backup-azure-vms-encryption/select-backup-service.png)

6. In **Aggiungi criteri** > di accesso**Configura da modello (facoltativo)** selezionare **backup di Azure**.
    - Le autorizzazioni necessarie sono precompilate per **Autorizzazioni chiave** e **Autorizzazioni segrete**.
    - Se la macchina virtuale è crittografata solo con la chiave di ricerca di **sole**, rimuovere la selezione per le **autorizzazioni chiave** perché sono necessarie solo le autorizzazioni per i segreti.

    ![Selezione di Backup di Azure](./media/backup-azure-vms-encryption/select-backup-template.png)

6. Fare clic su **OK**. Il **servizio di gestione di backup** viene aggiunto ai criteri di **accesso**.

    ![Criteri di accesso](./media/backup-azure-vms-encryption/backup-service-access-policy.png)

7. Fare clic su **Salva** per fornire a backup di Azure le autorizzazioni.

## <a name="restore-an-encrypted-vm"></a>Ripristinare una macchina virtuale crittografata

Ripristinare le macchine virtuali crittografate come indicato di seguito:

1. [Ripristinare il disco della macchina virtuale](backup-azure-arm-restore-vms.md#restore-disks).
2. Effettua una delle seguenti operazioni:
    - Usare il modello generato durante l'operazione di ripristino per personalizzare le impostazioni della macchina virtuale e attivare la distribuzione della macchina virtuale. [Altre informazioni](backup-azure-arm-restore-vms.md#use-templates-to-customize-a-restored-vm)
    - Creare una nuova macchina virtuale dai dischi ripristinati usando PowerShell. [Altre informazioni](backup-azure-vms-automation.md#create-a-vm-from-restored-disks)
    - Per le macchine virtuali Linux, reimpostare l'estensione ADE in modo che i dischi dati siano aperti e montati. 

## <a name="next-steps"></a>Passaggi successivi

In caso di problemi, vedere gli articoli seguenti:

- [Errori comuni](backup-azure-vms-troubleshoot.md) durante il backup e il ripristino di VM di Azure crittografate.
- Problemi dell' [estensione di backup e dell'agente VM di Azure](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md) .
