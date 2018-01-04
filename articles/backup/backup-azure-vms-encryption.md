---
title: Backup e ripristino di macchine virtuali crittografate tramite Backup di Azure
description: Questo articolo descrive il backup e il ripristino di macchine virtuali crittografate tramite Crittografia dischi di Azure.
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
ms.date: 10/13/2017
ms.author: pajosh;markgal;trinadhk; sogup
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 509e891207d1469ed244eab4512ec66420284fd5
ms.sourcegitcommit: 901a3ad293669093e3964ed3e717227946f0af96
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/21/2017
---
# <a name="back-up-and-restore-encrypted-virtual-machines-with-azure-backup"></a>Backup e ripristino di macchine virtuali crittografate con Backup di Azure
Questo articolo illustra i passaggi per eseguire il backup e il ripristino di macchine virtuali tramite Backup di Azure. L'articolo contiene anche informazioni sugli scenari supportati, sui prerequisiti e sui passaggi per la risoluzione dei problemi per i casi di errore.

## <a name="supported-scenarios"></a>Scenari supportati

 * Il backup e il ripristino di macchine virtuali crittografate sono supportati solo per le macchine virtuali che usano il modello di distribuzione Azure Resource Manager, ma non per macchine virtuali che usano il modello di distribuzione classica. <br>
 * Il backup e il ripristino di macchine virtuali crittografate sono supportati per macchine virtuali Windows e Linux che usano Crittografia dischi di Azure. Crittografia dischi usa la funzionalità BitLocker standard di settore di Windows e la funzionalità dm-crypt di Linux per fornire la crittografia dei dischi. <br>
 
 La tabella seguente illustra gli scenari supportati per le macchine virtuali crittografate solo con la chiave di crittografia BitLocker (BEK) e con la chiave di crittografia della chiave (KEK).
 
 
   |  | VM con BEK + KEK | Macchine virtuali solo con crittografia BEK |
   | --- | --- | --- |
   | **Macchine virtuali non gestite**  | Sì | Sì  |
   | **Macchine virtuali gestite**  | Sì | Sì  |

## <a name="prerequisites"></a>Prerequisiti
* La macchina virtuale è stata crittografata usando [Crittografia dischi di Azure](../security/azure-security-disk-encryption.md).

* È stato creato un insieme di credenziali di Servizi di ripristino e la replica di archiviazione è stata impostata seguendo i passaggi descritti in [Preparare l'ambiente per il backup](backup-azure-arm-vms-prepare.md).

* Per eseguire il backup, sono state concesse le [autorizzazioni per accedere a Key Vault](#provide-permissions-to-azure-backup) che contiene chiavi e segreti per le macchine virtuali crittografate.

## <a name="backup-encrypted-vm"></a>Backup di una macchina virtuale crittografata
Per impostare l'obiettivo di un backup, definire i criteri, configurare gli elementi e attivare un backup, seguire questa procedura.

### <a name="configure-backup"></a>Configurare il backup
1. Se l'insieme di credenziali di Servizi di ripristino è già aperto, procedere al passaggio successivo. Se non è presente un insieme di credenziali di Servizi di ripristino aperto, ma si opera nel portale di Azure, nel menu **Hub** selezionare **Sfoglia**.

   a. Nell'elenco di risorse digitare **servizi di ripristino**.

   b. Non appena si inizia a digitare, l'elenco viene filtrato in base all'input. Quando viene visualizzato **Insiemi di credenziali dei servizi di ripristino**, selezionare questa opzione.

      ![Insieme di credenziali dei servizi di ripristino](./media/backup-azure-vms-encryption/browse-to-rs-vaults.png) <br/>

    c. Viene visualizzato l'elenco di insiemi di credenziali dei servizi di ripristino. Selezionare un insieme di credenziali nell'elenco.

     Viene aperto il dashboard dell'insieme di credenziali selezionato.
2. Nell'elenco di elementi visualizzato sotto l'insieme di credenziali, selezionare **Backup** per avviare il backup della macchina virtuale crittografata.

      ![Pannello Backup](./media/backup-azure-vms-encryption/select-backup.png)
3. Nel riquadro **Backup** selezionare **Obiettivo del backup**.

      ![Pannello Scenario](./media/backup-azure-vms-encryption/select-backup-goal-one.png)
4. In **Posizione di esecuzione del carico di lavoro** selezionare **Azure**. In **Elementi di cui eseguire il backup** selezionare **Macchina virtuale**. Selezionare **OK**.

   ![Pannello Scenario aperto](./media/backup-azure-vms-encryption/select-backup-goal-two.png)
5. In **Scegliere i criteri di backup** selezionare i criteri da applicare all'insieme di credenziali. Selezionare **OK**.

      ![Selezionare il criterio di backup](./media/backup-azure-vms-encryption/setting-rs-backup-policy-new.png)

    Vengono elencati i dettagli dei criteri predefiniti. Per creare un criterio, selezionare **Crea nuovo** nell'elenco a discesa. Dopo aver selezionato **OK**, i criteri di backup vengono associati con l'insieme di credenziali.

6. Scegliere le macchine virtuali crittografate da associare con i criteri specificati e quindi selezionare **OK**.

      ![Selezionare le macchine virtuali crittografate](./media/backup-azure-vms-encryption/selected-encrypted-vms.png)
7. In questa pagina viene visualizzato un messaggio sull'insieme di credenziali delle chiavi associato alle macchine virtuali crittografate selezionate. Il servizio Backup richiede l'accesso in sola lettura per le chiavi e i segreti in Key Vault. Il servizio usa queste autorizzazioni per eseguire il backup di chiavi e segreti, nonché delle macchine virtuali associate.<br>
Se si è un **utente membro**, processo di Backup di abilitare facilmente acquisirà l'accesso all'insieme di credenziali chiave di backup crittografato di macchine virtuali senza richiedere alcun intervento dell'utente.

   ![Messaggio delle macchine virtuali crittografate](./media/backup-azure-vms-encryption/member-user-encrypted-vm-warning-message.png)

   Per un **utente Guest**, è necessario fornire autorizzazioni al servizio di backup per l'insieme di credenziali chiave per i backup funzionino accedere. È possibile fornire queste autorizzazioni seguendo il [i passaggi indicati nella sezione seguente](#provide-permissions-to-backup)

   ![Messaggio delle macchine virtuali crittografate](./media/backup-azure-vms-encryption/guest-user-encrypted-vm-warning-message.png)
 
    Dopo avere definito tutte le impostazioni per l'insieme di credenziali, selezionare **Abilita backup** nella parte inferiore della pagina. **Abilitare il Backup** consente di distribuire i criteri per l'insieme di credenziali e le macchine virtuali.
  
8. La fase successiva di preparazione è l'installazione dell'agente di macchine virtuali o la verifica che l'agente di macchine virtuali sia installato. Per eseguire la stessa operazione, seguire la procedura descritta in [Preparare l'ambiente per il backup](backup-azure-arm-vms-prepare.md).

### <a name="trigger-a-backup-job"></a>Attivare un processo di backup
Seguire la procedura descritta in [Backup di macchine virtuali di Azure in un insieme di credenziali di Servizi di ripristino](backup-azure-arm-vms.md) per attivare un processo di backup.

### <a name="continue-backups-of-already-backed-up-vms-with-encryption-enabled"></a>Continuare i backup delle macchine virtuali per cui l'operazione è già stata eseguita con la crittografia abilitata  
Se sono presenti macchine virtuali per cui è già stato eseguito il backup in un insieme di credenziali di Servizi di ripristino e abilitate per la crittografia in un secondo momento, per continuare l'esecuzione dei backup è necessario concedere al servizio Backup le autorizzazioni per accedere a Key Vault. Per concedere queste autorizzazioni, seguire la [procedura descritta nella sezione seguente](#provide-permissions-to-azure-backup). In alternativa è possibile seguire la procedura di PowerShell nella sezione per l'abilitazione del backup nella [documentazione di PowerShell](backup-azure-vms-automation.md). 

## <a name="provide-permissions-to-backup"></a>Concedere le autorizzazioni al servizio Backup
Seguire questa procedura per concedere le autorizzazioni rilevanti al servizio Backup per l'accesso a Key Vault e l'esecuzione del backup delle macchine virtuali crittografate.
1. Selezionare **Altri servizi** e cercare **Insiemi di credenziali delle chiavi**.

    ![Key Vault](./media/backup-azure-vms-encryption/search-key-vault.png)
    
2. Nell'elenco di Key Vault selezionare l'insieme associato alla macchina virtuale crittografata di cui deve essere eseguito il backup.

     ![Selezione di Key Vault](./media/backup-azure-vms-encryption/select-key-vault.png)
     
3. Selezionare **Criteri di accesso** e quindi selezionare **Aggiungi nuovo**.

    ![Aggiungi nuovo](./media/backup-azure-vms-encryption/select-key-vault-access-policy.png)
    
4. Selezionare **Selezionare un'entità** e quindi digitare **servizio di gestione backup** nella casella di ricerca. 

    ![Ricerca del servizio di backup](./media/backup-azure-vms-encryption/search-backup-service.png)
    
5. Selezionare **Backup Management Service** (Servizio di gestione backup) e quindi selezionare **Seleziona**.

    ![Selezione del servizio di backup](./media/backup-azure-vms-encryption/select-backup-service.png)
    
6. In **Configura dal modello (facoltativo)** selezionare **Backup di Azure**. Le autorizzazioni necessarie sono precompilate per **Autorizzazioni chiave** e **Autorizzazioni segrete**. Se la macchina virtuale viene crittografata usando **solo la chiave BEK**, sono necessarie le autorizzazioni solo per i segreti ed è quindi necessario rimuovere la selezione per **Autorizzazioni chiave**.

    ![Selezione di Backup di Azure](./media/backup-azure-vms-encryption/select-backup-template.png)
    
7. Selezionare **OK**. Si noti che la voce **Backup Management Service** (Servizio di gestione backup) è stata aggiunta in **Criteri di accesso**. 

    ![Criteri di accesso](./media/backup-azure-vms-encryption/backup-service-access-policy.png)
    
8. Selezionare **Salva** per concedere le autorizzazioni necessarie al servizio Backup.

    ![Criteri di accesso per il backup](./media/backup-azure-vms-encryption/save-access-policy.png)

Dopo avere concesso le autorizzazioni, è possibile procedere con l'abilitazione del backup per le macchine virtuali crittografate.

## <a name="restore-an-encrypted-vm"></a>Ripristinare una macchina virtuale crittografata
Per ripristinare una macchina virtuale crittografata, ripristinare prima i dischi seguendo la procedura descritta nella sezione "Ripristinare i dischi di cui è stato eseguito il backup" in [Scelta di una configurazione di ripristino per la macchina virtuale](backup-azure-arm-restore-vms.md#choose-a-vm-restore-configuration). È possibile quindi usare una delle opzioni seguenti:

* Seguire la procedura di PowerShell descritta in [Creare una macchina virtuale da dischi ripristinati](backup-azure-vms-automation.md#create-a-vm-from-restored-disks) per creare una macchina virtuale completa da dischi ripristinati.
* In alternativa, [usare modelli per personalizzare una macchina virtuale ripristinata](backup-azure-arm-restore-vms.md#use-templates-to-customize-a-restored-vm) per creare macchine virtuali da dischi ripristinati. I modelli possono essere usati solo per i punti di recupero creati dopo il 26 aprile 2017.

## <a name="troubleshooting-errors"></a>Risoluzione dei problemi
| Operazione | Dettagli errore | Risoluzione |
| --- | --- | --- |
|Backup | Il servizio Backup non dispone delle autorizzazioni sufficienti per Accedere a Key Vault per il backup di macchine virtuali crittografate. | Al servizio Backup devono essere concesse queste autorizzazioni seguendo la [procedura descritta nella sezione precedente](#provide-permissions-to-azure-backup). Oppure è possibile seguire la procedura di PowerShell nella sezione per abilitare la protezione della documentazione di PowerShell [Usare i cmdlet AzureRM.RecoveryServices.Backup per eseguire il backup di macchine virtuali](backup-azure-vms-automation.md#back-up-azure-vms). |  
| Restore |Non è possibile ripristinare questa macchina virtuale crittografata perché il Key Vault associato alla macchina non esiste. |Creare un Key Vault come descritto in [Introduzione all'insieme di credenziali delle chiavi di Azure](../key-vault/key-vault-get-started.md). Vedere [Ripristinare la chiave dell'insieme di credenziali delle chiavi e il segreto per le macchine virtuali crittografate con Backup di Azure](backup-azure-restore-key-secret.md) per ripristinare una chiave e un segreto, se non sono presenti. |
| Restore |Non è possibile ripristinare questa macchina virtuale crittografata perché la chiave e il segreto associati non esistono. |Vedere [Ripristinare la chiave dell'insieme di credenziali delle chiavi e il segreto per le macchine virtuali crittografate con Backup di Azure](backup-azure-restore-key-secret.md) per ripristinare una chiave e un segreto, se non sono presenti. |
| Restore |Il servizio Backup non dispone dell'autorizzazione per accedere alle risorse nella sottoscrizione dell'utente. |Come accennato in precedenza, ripristinare prima i dischi seguendo la procedura descritta nella sezione "Ripristinare i dischi di cui è stato eseguito il backup" in [Scegliere una configurazione di ripristino per la macchina virtuale](backup-azure-arm-restore-vms.md#choose-a-vm-restore-configuration). Usare quindi PowerShell per [creare una macchina virtuale da dischi ripristinati](backup-azure-vms-automation.md#create-a-vm-from-restored-disks). |
