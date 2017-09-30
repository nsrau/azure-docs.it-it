---
title: Backup e ripristino di macchine virtuali crittografate con Azure Backup
description: Questo articolo riguarda l'esperienza di backup e ripristino di macchine virtuali crittografate tramite Crittografia dischi di Azure.
services: backup
documentationcenter: 
author: JPallavi
manager: vijayts
editor: 
ms.assetid: 8387f186-7d7b-400a-8fc3-88a85403ea63
ms.service: backup
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 09/18/2017
ms.author: pajosh;markgal;trinadhk
ms.custom: H1Hack27Feb2017
ms.translationtype: HT
ms.sourcegitcommit: 8f9234fe1f33625685b66e1d0e0024469f54f95c
ms.openlocfilehash: 3c0c13aebbf325bc1f03a39a456c8eef1551c64a
ms.contentlocale: it-it
ms.lasthandoff: 09/20/2017

---
# <a name="how-to-back-up-and-restore-encrypted-virtual-machines-with-azure-backup"></a>Come eseguire il backup e il ripristino delle macchine virtuali crittografate con il Backup di Azure
Questo articolo illustra la procedura per eseguire il backup e ripristino di macchine virtuali con Backup di Azure. Fornisce anche informazioni sugli scenari supportati, i prerequisiti e i passaggi per la risoluzione dei problemi per i casi di errore.

## <a name="supported-scenarios"></a>Scenari supportati

 * L'operazione di backup e ripristino di macchine virtuali crittografate è supportata solo per le macchine virtuali distribuite da Resource Manager. Non è supportata per le macchine virtuali classiche. <br>
 * È supportata per le macchine virtuali Windows e Linux che usano Crittografia dischi di Azure, che usa la funzionalità BitLocker di Windows e la funzionalità DM-Crypt di Linux standard di settore per offrire la crittografia dei dischi. <br>
 * La tabella seguente illustra gli scenari supportati per le macchine virtuali crittografate solo con la chiave di crittografia BitLocker (BEK) e con la chiave di crittografia della chiave (KEK).
 
 
   |  | VM con BEK + KEK | VM solo con BEK |
   | --- | --- | --- |
   | **Macchine virtuali non gestite**  | Sì | Sì  |
   | **Macchine virtuali gestite**  | Sì | No  |

## <a name="prerequisites"></a>Prerequisiti
1. La macchina virtuale è stata crittografata tramite [Crittografia dischi di Azure](../security/azure-security-disk-encryption.md). 
2. È stato creato l'insieme di credenziali dei servizi di ripristino ed è stata impostata la replica di archiviazione usando i passaggi indicati nell'articolo [Preparare l'ambiente per il backup di macchine virtuali distribuite con Resource Manager](backup-azure-arm-vms-prepare.md).
3. Backup di Azure ha ottenuto le [autorizzazioni per l'accesso all'insieme di credenziali delle chiavi](#provide-permissions-to-azure-backup) contenente le chiavi e i segreti per le VM crittografate.

## <a name="backup-encrypted-vm"></a>Backup di macchine virtuali crittografate
Per impostare l'obiettivo del backup, definire i criteri, configurare gli elementi e attivare il backup, seguire questa procedura.

### <a name="configure-backup"></a>Configurare il backup
1. Se l'insieme di credenziali dei servizi di ripristino è già aperto, procedere al passaggio successivo. Se non è aperto alcun insieme di credenziali dei servizi di ripristino ma si è nel portale di Azure, nel menu dell'hub fare clic su **Esplora**.

   * Nell'elenco di risorse digitare **Servizi di ripristino**.
   * Non appena si inizia a digitare, l'elenco viene filtrato in base all'input. Quando viene visualizzata l'opzione, fare clic su **Insiemi di credenziali dei servizi di ripristino**.

      ![Creare un insieme di credenziali dei servizi di ripristino - Passaggio 1](./media/backup-azure-vms-encryption/browse-to-rs-vaults.png) <br/>

     Viene visualizzato l'elenco di insiemi di credenziali dei servizi di ripristino. Nell'elenco di insiemi di credenziali dei servizi di ripristino selezionare un insieme di credenziali.

     Viene aperto il dashboard dell'insieme di credenziali selezionato.
2. Nell'elenco di elementi che viene visualizzato nell'insieme di credenziali, fare clic su **Backup** per avviare il backup della VM crittografata.

      ![Pannello Backup aperto](./media/backup-azure-vms-encryption/select-backup.png)
3. Fare clic su **Obiettivo del backup** come primo passaggio per selezionare l'obiettivo del backup.

      ![Pannello Scenario aperto](./media/backup-azure-vms-encryption/select-backup-goal-one.png)
4. Nel primo passaggio di selezione dell'obiettivo del backup impostare **Posizione di esecuzione del carico di lavoro** su Azure ed **Elementi di cui eseguire il backup** su Macchina virtuale e quindi fare clic su **OK**.

   Si passa quindi al secondo passaggio di selezione dei criteri di backup.

   ![Pannello Scenario aperto](./media/backup-azure-vms-encryption/select-backup-goal-two.png)
5. Nel secondo passaggio di selezione dei criteri selezionare i criteri di backup da applicare all'insieme di credenziali e fare clic su **OK**.

      ![Selezionare il criterio di backup](./media/backup-azure-vms-encryption/setting-rs-backup-policy-new.png)

    I dettagli dei criteri predefiniti vengono elencati nei dettagli. Per creare un criterio, selezionare **Crea nuovo** dal menu a discesa. Dopo aver fatto clic su **OK**, il criterio di backup viene associato all'insieme di credenziali.

    Scegliere quindi le VM da associare all'insieme di credenziali.
6. Selezionare le macchine virtuali crittografate da associare al criterio specificato e fare clic su **OK**.

      ![Selezionare le macchine virtuali crittografate](./media/backup-azure-vms-encryption/selected-encrypted-vms.png)
7. In questa pagina viene visualizzato un messaggio sull'insieme di credenziali delle chiavi associato alle macchine virtuali crittografate selezionate. Il servizio di backup richiede l'accesso in sola lettura per le chiavi e i segreti nell'insieme di credenziali delle chiavi. Il servizio usa le autorizzazioni per chiavi di backup e segreti, insieme alle macchine virtuali associate. **Per consentire il funzionamento dei backup, è necessario fornire le autorizzazioni al servizio di backup per l'accesso all'insieme di credenziali delle chiavi**. È possibile fornire queste autorizzazioni seguendo la [procedura indicata nella sezione successiva](#provide-permissions-to-azure-backup).

      ![Messaggio delle macchine virtuali crittografate](./media/backup-azure-vms-encryption/encrypted-vm-warning-message.png)

      Dopo avere definito tutte le impostazioni per l'insieme di credenziali, fare clic su Abilita backup nella parte inferiore della pagina. Abilita backup consente di distribuire i criteri nell'insieme di credenziali e nelle macchine virtuali.
8. La fase successiva di preparazione è l'installazione dell'agente di macchine virtuali o la verifica che l'agente di macchine virtuali sia installato. Allo stesso scopo, usare la procedura illustrata nell'articolo [Preparare l'ambiente per il backup di macchine virtuali distribuite con Resource Manager](backup-azure-arm-vms-prepare.md).

### <a name="triggering-backup-job"></a>Attivazione del processo di backup
Per attivare il processo di backup, usare la procedura illustrata nell'articolo [Eseguire il backup di macchine virtuali di Azure in un insieme di credenziali di Servizi di ripristino](backup-azure-arm-vms.md).

### <a name="continue-backups-of-already-backed-up-vms-with-encryption-enabled"></a>Continuare i backup delle macchine virtuali per cui l'operazione è già stata eseguita con la crittografia abilitata  
Se si dispone di macchine virtuali per cui il backup è già stato eseguito nell'insieme di credenziali dei servizi di ripristino e se tali macchine sono state abilitate per la crittografia in un secondo momento, affinché le operazioni di backup continuino è necessario concedere autorizzazioni ai servizi di backup per accedere a un Key Vault. È possibile concedere queste autorizzazioni seguendo la [procedura illustrata nella sezione successiva](#provide-permissions-to-azure-backup) oppure usando la procedura di PowerShell illustrata nella sezione **Abilitare il backup** della [documentazione di PowerShell](backup-azure-vms-automation.md). 

## <a name="provide-permissions-to-azure-backup"></a>Specificare le autorizzazioni per Backup di Azure
Seguire questa procedura per fornire le autorizzazioni rilevanti a Backup di Azure per l'accesso all'insieme di credenziali delle chiavi e l'esecuzione del backup delle VM crittografate:
1. Selezionare **Altri servizi** e cercare **Insiemi di credenziali delle chiavi**.

    ![Cercare l'insieme di credenziali delle chiavi](./media/backup-azure-vms-encryption/search-key-vault.png)
    
2. Dall'elenco di insiemi di credenziali delle chiavi selezionare l'insieme di credenziali delle chiavi associato alla VM crittografata di cui deve essere eseguito il backup.

     ![Selezionare l'insieme di credenziali delle chiavi](./media/backup-azure-vms-encryption/select-key-vault.png)
     
3. Fare clic su **Criteri di accesso** e quindi su **Aggiungi nuovo**.

    ![Aggiungere un criterio di accesso](./media/backup-azure-vms-encryption/select-key-vault-access-policy.png)
    
4. Fare clic su **Selezionare un'entità** e digitare **Backup Management Service** nella barra di ricerca. 

    ![Cercare un servizio di backup](./media/backup-azure-vms-encryption/search-backup-service.png)
    
5. Selezionare **Backup Management Service** e fare clic sul pulsante Seleziona.

    ![Selezionare un servizio di backup](./media/backup-azure-vms-encryption/select-backup-service.png)
    
6. Selezionare **Backup di Azure** nell'elenco a discesa Configura dal modello. Le autorizzazioni necessarie vengono precompilate negli elenchi a discesa Autorizzazioni chiave e Autorizzazioni segrete. Se la macchina virtuale viene crittografata usando **solo la chiave BEK**, sono necessarie le autorizzazioni solo per i segreti ed è quindi necessario rimuovere la selezione per le autorizzazioni delle chiavi.

    ![Selezionare Backup di Azure](./media/backup-azure-vms-encryption/select-backup-template.png)
    
7. Fare clic su **OK**. Si noti che la voce Backup Management Service viene aggiunta ai criteri di accesso. 

    ![Criteri di accesso del servizio di backup](./media/backup-azure-vms-encryption/backup-service-access-policy.png)
    
8. Fare clic su **Salva**. Verranno fornite le autorizzazioni necessarie per Backup di Azure.

    ![Criteri di accesso del servizio di backup](./media/backup-azure-vms-encryption/save-access-policy.png)

Dopo avere fornito le autorizzazioni, è possibile procedere con l'abilitazione del backup per le VM crittografate.

## <a name="restore-encrypted-vm"></a>Ripristinare una macchina virtuale crittografata
Per ripristinare la macchina virtuale crittografata, ripristinare prima di tutto i dischi seguendo la procedura illustrata nella sezione **Ripristino dei dischi sottoposti a backup** in [Scelta di una configurazione di ripristino per la macchina virtuale](backup-azure-arm-restore-vms.md#choosing-a-vm-restore-configuration). È possibile quindi usare una delle opzioni seguenti:
* Seguire la procedura di PowerShell illustrata in [Creare una macchina virtuale da dischi ripristinati](backup-azure-vms-automation.md#create-a-vm-from-restored-disks) per creare una macchina virtuale completa dai dischi ripristinati.
* OPPURE [usare un modello generato come parte dei dischi di ripristino](backup-azure-arm-restore-vms.md#use-templates-to-customize-restore-vm) per creare macchine virtuali da dischi ripristinati. I modelli possono essere usati solo per i punti di recupero creati dopo il 26 aprile 2017.

## <a name="troubleshooting-errors"></a>Risoluzione dei problemi
| Operazione | Dettagli errore | Risoluzione |
| --- | --- | --- |
|Backup | Il servizio Backup di Azure non possiede autorizzazioni sufficienti per Key Vault per il backup di macchine virtuali crittografate. | La macchina virtuale deve essere crittografata mediante la chiave di crittografia BitLocker e la chiave di crittografia delle chiavi. In seguito, il backup deve essere abilitato.  È necessario concedere queste autorizzazioni al servizio di backup seguendo la [procedura illustrata nella sezione precedente](#provide-permissions-to-azure-backup) oppure usando la procedura di PowerShell indicata nella sezione **Abilitare la protezione** della documentazione di PowerShell in [Usare i cmdlet AzureRM.RecoveryServices.Backup per eseguire il backup delle macchine virtuali](backup-azure-vms-automation.md#back-up-azure-vms). |  
| Backup |Convalida non riuscita perché la macchina virtuale è crittografata con il solo BEK. I backup possono essere abilitati solo per le macchine virtuali crittografate con BEK e KEK. |Le macchine virtuali devono essere crittografate usando BEK e KEK. Innanzitutto decrittografare la macchina virtuale e crittografarla usando BEK e KEK. Abilitare il backup dopo che la macchina virtuale è stata crittografata usando BEK e KEK. Altre informazioni su come [decrittografare e crittografare la macchina virtuale](../security/azure-security-disk-encryption.md)  |
| Ripristino |Non è possibile ripristinare una macchina virtuale crittografata se l'insieme di credenziali delle chiavi associato alla macchina in questione non esiste. |Creare l'insieme di credenziali delle chiavi usando [Introduzione all'insieme di credenziali delle chiavi di Azure](../key-vault/key-vault-get-started.md). Se non sono presenti, per ripristinare chiavi e segreti consultare l'articolo [Restore key vault key and secret using Azure Backup](backup-azure-restore-key-secret.md) (Ripristinare chiavi e segreti dell'insieme di credenziali delle chiavi tramite Backup di Azure). |
| Ripristino |Non è possibile ripristinare una macchina virtuale crittografata se chiavi e segreti associati con la macchina virtuale in questione non esistono. |Se non sono presenti, per ripristinare chiavi e segreti consultare l'articolo [Restore key vault key and secret using Azure Backup](backup-azure-restore-key-secret.md) (Ripristinare chiavi e segreti dell'insieme di credenziali delle chiavi tramite Backup di Azure). |
| Ripristino |Il servizio Backup non ha l'autorizzazione per accedere alle risorse nella sottoscrizione. |Come indicato in precedenza, ripristinare prima di tutto i dischi seguendo la procedura illustrata nella sezione **Ripristino dei dischi sottoposti a backup** in [Scelta di una configurazione di ripristino per la macchina virtuale](backup-azure-arm-restore-vms.md#choosing-a-vm-restore-configuration). Successivamente usare PowerShell per [creare una macchina virtuale dai dischi ripristinati](backup-azure-vms-automation.md#create-a-vm-from-restored-disks). |


