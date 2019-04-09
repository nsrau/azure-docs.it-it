---
title: Eseguire il backup e ripristino di macchine virtuali di Azure crittografate con Backup di Azure
description: Viene descritto come eseguire il backup e ripristino di macchine virtuali di Azure crittografate con il servizio Backup di Azure.
services: backup
author: geetha
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 4/3/2019
ms.author: geetha
ms.openlocfilehash: 99117c96f79dd7d0da388a0e793908f6ffb8ed27
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2019
ms.locfileid: "59266445"
---
# <a name="back-up-and-restore-encrypted-azure-vm"></a>Eseguire il backup e ripristino della macchina virtuale crittografata di Azure

Questo articolo descrive come eseguire il backup e ripristino di macchine virtuali (VM) Windows o Linux di Azure con dischi crittografati usando la [Backup di Azure](backup-overview.md) servizio.

Per altre informazioni sulla modalità di interazione di Backup di Azure con macchine virtuali di Azure prima di iniziare, consultare queste risorse:

- [Revisione](backup-architecture.md#architecture-direct-backup-of-azure-vms) l'architettura del backup delle macchine Virtuali di Azure.
- [Informazioni su](backup-azure-vms-introduction.md) backup delle macchine Virtuali di Azure e l'estensione di Backup di Azure.

## <a name="encryption-support"></a>Supporto della crittografia

Backup di Azure supporta il backup delle macchine virtuali di Azure con i relativi dischi del sistema operativo/dati crittografati con crittografia dischi di Azure (ADE). ADE utilizza BitLocker per la crittografia di macchine virtuali Windows e la funzionalità dm-crypt per le macchine virtuali Linux. ADE si integra con Azure Key Vault per gestire i segreti e chiavi di crittografia del disco. Le chiavi di crittografia chiave di Key Vault (scambio) utilizzabile per aggiungere un ulteriore livello di sicurezza, la crittografia dei segreti di crittografia prima di scriverli in Key Vault.

Backup di Azure può eseguire il backup e ripristino di macchine virtuali di Azure con ADE con e senza l'app di Azure AD, come riepilogato nella tabella seguente.

**Tipo di disco della macchina virtuale** | **ADE (BEK/dm-crypt)** | **ADE e KEK**
--- | --- | --- 
**Unmanaged Disks** | Sì | Sì
**Gestito**  | Sì | Sì

- Altre informazioni sulle [ADE](../security/azure-security-disk-encryption-overview.md), [Key Vault](../key-vault/key-vault-overview.md), e [scambio](https://blogs.msdn.microsoft.com/cclayton/2017/01/03/creating-a-key-encrypting-key-kek/).
- Leggere il [domande frequenti su](../security/azure-security-disk-encryption-faq.md) per crittografia dischi di macchina virtuale di Azure.



### <a name="limitations"></a>Limitazioni

- È possibile eseguire il backup e ripristino di macchine virtuali crittografate all'interno della stessa sottoscrizione e area.
- Backup di Azure supporta le macchine virtuali crittografate con chiavi autonomo. Qualsiasi chiave che fa parte di un certificato usato per crittografare una macchina virtuale non è attualmente supportata.
- È possibile eseguire il backup e ripristino di macchine virtuali crittografate all'interno della stessa sottoscrizione e area geografica come insieme di credenziali di Backup di servizi di ripristino.
- Le macchine virtuali crittografate non possono essere ripristinate a livello di file/cartella. È necessario ripristinare l'intera macchina virtuale per ripristinare file e cartelle.
- Quando si ripristina una macchina virtuale, non è possibile usare la [sostituire macchina virtuale esistente](backup-azure-arm-restore-vms.md#restore-options) opzione per le macchine virtuali crittografate. Questa opzione è supportata solo per i dischi gestiti non crittografati.




## <a name="before-you-start"></a>Prima di iniziare

Prima di iniziare, eseguire le operazioni seguenti:

1. Assicurarsi di disporre di uno o più [Windows](../security/azure-security-disk-encryption-windows.md) oppure [Linux](../security/azure-security-disk-encryption-linux.md) macchine virtuali con ADE abilitata.
2. [Esaminare la matrice di supporto](backup-support-matrix-iaas.md) per il backup di macchine Virtuali di Azure
3. [Creare](backup-azure-arm-vms-prepare.md#create-a-vault) un insieme di credenziali di Backup di servizi di ripristino se non hai uno.
4. Se si abilita la crittografia per le macchine virtuali che sono già abilitate per il backup, è sufficiente fornire Backup con le autorizzazioni per accedere a Key Vault in modo che i backup possono continuare senza interruzioni. [Altre informazioni](#provide-permissions) sull'assegnazione di queste autorizzazioni.

Inoltre, esistono un paio di cose che potrebbe essere necessario eseguire operazioni in alcune circostanze:

- **Installare l'agente di macchine Virtuali nella VM**: Backup di Azure esegue il backup di macchine virtuali di Azure tramite l'installazione di un'estensione per l'agente di macchine virtuali di Azure in esecuzione nel computer. Se la VM è stata creata da un'immagine di Azure marketplace, l'agente è installato e in esecuzione. Se si crea una macchina virtuale personalizzata o si esegue la migrazione di una macchina locale, potrebbe essere necessario [installare manualmente l'agente](backup-azure-arm-vms-prepare.md#install-the-vm-agent).
- **Consentire l'accesso in uscita in modo esplicito**: In genere, non è necessario consentire in modo esplicito l'accesso alla rete in uscita per una VM di Azure per poter comunicare con Backup di Azure. Tuttavia, alcune macchine virtuali potrebbero riscontrare problemi di connessione, che mostra le **ExtensionSnapshotFailedNoNetwork** errore quando provano a connettersi. In questo caso, dovrebbe [consentire in modo esplicito l'accesso in uscita](backup-azure-arm-vms-prepare.md#explicitly-allow-outbound-access), in modo che l'estensione di Backup di Azure può comunicare con Azure indirizzi IP pubblici per il traffico di backup.



## <a name="configure-a-backup-policy"></a>Configurare un criterio di backup

1. Se è stato ancora creato un archivio di backup di servizi di ripristino, seguire [queste istruzioni](backup-azure-arm-vms-prepare.md#create-a-vault)
2. Aprire l'insieme di credenziali nel portale e selezionare **Backup** nel **introduttiva** sezione.

    ![Pannello Backup](./media/backup-azure-vms-encryption/select-backup.png)

3. Nelle **obiettivo del Backup** > **in cui viene eseguito il carico di lavoro?** selezionare **Azure**.
4. Nelle **ciò che si desidera eseguire il backup?** selezionate **macchina virtuale** > **OK**.

      ![Pannello Scenario](./media/backup-azure-vms-encryption/select-backup-goal-one.png)

5. Nelle **criteri di Backup** > **scegliere Criteri di backup**, selezionare il criterio che si desidera associare l'insieme di credenziali. Fare quindi clic su **OK**.
    - Un criterio di backup specifica quando i backup vengono eseguiti e quanto tempo vengono archiviati.
    - I dettagli dei criteri predefiniti vengono elencati nel menu a discesa.

    ![Pannello Scenario aperto](./media/backup-azure-vms-encryption/select-backup-goal-two.png)

6. Se non si desidera usare i criteri predefiniti, selezionare **Crea nuovo**, e [creare un criterio personalizzato](backup-azure-arm-vms-prepare.md#create-a-custom-policy).


7. Scegliere le macchine virtuali crittografate si desidera eseguire il backup usando i criteri di selezionati e selezionare **OK**.

      ![Selezionare le macchine virtuali crittografate](./media/backup-azure-vms-encryption/selected-encrypted-vms.png)

8. Se si usa Azure Key Vault, nella pagina dell'insieme di credenziali, viene visualizzato un messaggio che Backup di Azure richiede l'accesso di sola lettura alle chiavi e segreti nell'insieme di credenziali chiave di.

    - Se si riceve questo messaggio, è necessaria alcuna azione.
    
        ![Accesso OK](./media/backup-azure-vms-encryption/access-ok.png)
        
    - Se si riceve questo messaggio, è necessario impostare le autorizzazioni come descritto nel [procedura riportata di seguito](#provide-permissions).
    
        ![Avviso di accesso](./media/backup-azure-vms-encryption/access-warning.png)

9. Fare clic su **Abilita Backup** per distribuire il criterio di backup nell'insieme di credenziali e abilitare il backup per macchine virtuali selezionate. 


## <a name="trigger-a-backup-job"></a>Attivare un processo di backup

Verrà eseguito il backup iniziale in base alla pianificazione, ma è possibile eseguirlo immediatamente come indicato di seguito:

1. Selezionare **Elementi di backup** dal menu dell'insieme di credenziali.
2. In **Elementi di backup** fare clic su **Macchine virtuali di Azure**.
3. Nel **elementi di Backup** fare clic sui puntini di sospensione (...).
4. Fare clic su **Esegui backup ora**.
5. Nelle **Esegui Backup ora**, usare il controllo calendario per selezionare l'ultimo giorno che deve essere mantenuto il punto di ripristino. Fare quindi clic su **OK**.
6. Monitorare le notifiche del portale. È possibile monitorare l'avanzamento del processo nel dashboard dell'insieme di credenziali > **Processi di Backup** > **In corso**. A seconda delle dimensioni della macchina virtuale, la creazione del backup iniziale potrebbe richiedere un po' di tempo.


## <a name="provide-permissions"></a>Fornire le autorizzazioni

Macchina virtuale di Azure deve avere accesso di sola lettura per eseguire il backup di chiavi e segreti, insieme a macchine virtuali associate.

- Insieme di credenziali delle chiavi è associato il tenant di Azure AD della sottoscrizione di Azure. Se non ha un **utente membro**, Backup di Azure acquisisce l'accesso all'insieme di credenziali chiave senza eseguire ulteriori azioni.
- Se non ha un **utente Guest**, è necessario fornire le autorizzazioni per accedere a insieme di credenziali di Backup di Azure.

Per impostare le autorizzazioni:

1. Nel portale di Azure, selezionare **tutti i servizi**e cercare **insiemi di credenziali della chiave**.
2. Selezionare l'insieme di credenziali chiave associati alla macchina virtuale crittografata esegue il backup.
3. Selezionare **criteri di accesso** > **Aggiungi nuovo**.
4. Selezionare **selezionare un'entità**, quindi digitare **Gestione Backup**. 
5. Selezionare **servizio di gestione di Backup** > **selezionare**.

    ![Selezione del servizio di backup](./media/backup-azure-vms-encryption/select-backup-service.png)

6. Nelle **criteri di accesso Add** > **Configura dal modello (facoltativo)**, selezionare **Backup di Azure**.
    - Le autorizzazioni necessarie sono precompilate per **Autorizzazioni chiave** e **Autorizzazioni segrete**.
    - Se la macchina virtuale viene crittografata usando **solo con BEK**, rimuovere la selezione per **autorizzazioni chiave** poiché è necessario solo le autorizzazioni per i segreti. 

    ![Selezione di Backup di Azure](./media/backup-azure-vms-encryption/select-backup-template.png)

6. Fare clic su **OK**. **Servizio di gestione di backup** viene aggiunto alla **i criteri di accesso**. 

    ![Criteri di accesso](./media/backup-azure-vms-encryption/backup-service-access-policy.png)

7. Fare clic su **salvare** per fornire Backup di Azure con le autorizzazioni.

## <a name="restore-an-encrypted-vm"></a>Ripristinare una macchina virtuale crittografata

Le macchine virtuali crittografate è ripristinare come indicato di seguito:

1. [Ripristinare il disco della macchina virtuale](backup-azure-arm-restore-vms.md#restore-disks).
2. Quindi eseguire una delle operazioni seguenti:
    - Usare il modello che viene generato durante l'operazione di ripristino per personalizzare le impostazioni della macchina virtuale e attivare la distribuzione della macchina virtuale. [Altre informazioni](backup-azure-arm-restore-vms.md#use-templates-to-customize-a-restored-vm)
    - Creare una nuova macchina virtuale da dischi ripristinati tramite Powershell. [Altre informazioni](backup-azure-vms-automation.md#create-a-vm-from-restored-disks)

## <a name="next-steps"></a>Passaggi successivi

Se si verificano problemi, consultare

- [Errori comuni](backup-azure-vms-troubleshoot.md#troubleshoot-backup-of-encrypted-vms) quando backup e ripristino crittografati macchine virtuali di Azure.
- [Generale](backup-azure-vms-troubleshoot.md) problemi di macchine Virtuali di Azure.
- [Estensione agente/backup VM Azure](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md) problemi.
