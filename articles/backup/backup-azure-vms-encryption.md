---
title: Backup e ripristino di macchine virtuali crittografate con Azure Backup
description: Questo articolo riguarda l&quot;esperienza di backup e ripristino di macchine virtuali crittografate tramite Crittografia dischi di Azure.
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
ms.date: 01/18/2017
ms.author: markgal; jimpark; trinadhk
translationtype: Human Translation
ms.sourcegitcommit: 152285bee4631acca7610eb5bd9167ec37296a26
ms.openlocfilehash: d1ec8112b9fc347d9de5901c4b39a3291701da45


---
# <a name="backup-and-restore-encrypted-vms-using-azure-backup"></a>Backup e ripristino di macchine virtuali crittografate con Azure Backup
Questo articolo illustra la procedura per eseguire il backup e ripristino di macchine virtuali con Backup di Azure. Fornisce anche informazioni sugli scenari supportati, i prerequisiti e i passaggi per la risoluzione dei problemi per i casi di errore.

## <a name="supported-scenarios"></a>Scenari Supportati
> [!NOTE]
> 1. L'operazione di backup e ripristino di macchine virtuali crittografate è supportata solo per le macchine virtuali distribuite da Resource Manager. Non è supportata per le macchine virtuali classiche. <br>
> 2. È supportata per le macchine virtuali Windows e Linux che usano Crittografia dischi di Azure, che si basa sulla funzionalità BitLocker di Windows e sulla funzionalità DM-Crypt di Linux standard di settore per offrire la crittografia dei dischi. <br>
> 3. Questa operazione è supportata solo per le macchine virtuali crittografate usando entrambe le chiavi di crittografia BitLocker e Key. Non è supportata per le macchine virtuali crittografate usando solo la chiave di crittografia BitLocker. <br>
> 
> 

## <a name="pre-requisites"></a>Prerequisiti
1. La macchina virtuale è stata crittografata tramite [Crittografia dischi di Azure](../security/azure-security-disk-encryption.md). Sono state usate sia la chiave di crittografia BitLocker che la chiave di crittografia Key.
2. È stato creato l'insieme di credenziali dei servizi di ripristino ed è stata impostata la replica di archiviazione usando i passaggi indicati nell'articolo [Preparare l'ambiente per il backup di macchine virtuali distribuite con Resource Manager](backup-azure-arm-vms-prepare.md).

## <a name="backup-encrypted-vm"></a>Backup di macchine virtuali crittografate
Per impostare l'obiettivo del backup, definire i criteri, configurare gli elementi e attivare il backup, usare la procedura seguente.

### <a name="configure-backup"></a>Configurare il backup
1. Se l'insieme di credenziali dei servizi di ripristino è già aperto, procedere al passaggio successivo. Se non è aperto alcun insieme di credenziali dei servizi di ripristino ma si è nel portale di Azure, nel menu dell'hub fare clic su **Esplora**.
   
   * Nell'elenco di risorse digitare **Servizi di ripristino**.
   * Non appena si inizia a digitare, l'elenco viene filtrato in base all'input. Quando viene visualizzata l'opzione, fare clic su **Insiemi di credenziali dei servizi di ripristino**.
     
      ![Creare un insieme di credenziali dei servizi di ripristino - Passaggio 1](./media/backup-azure-vms-encryption/browse-to-rs-vaults.png) <br/>
     
     Viene visualizzato l'elenco di insiemi di credenziali dei servizi di ripristino. Nell'elenco di insiemi di credenziali dei servizi di ripristino selezionare un insieme di credenziali.
     
     Viene aperto il dashboard dell'insieme di credenziali selezionato.
2. Nell'elenco di elementi che viene visualizzato in insieme di credenziali, fare clic su **Backup** per aprire il pannello Backup.
   
      ![Pannello Backup aperto](./media/backup-azure-vms-encryption/select-backup.png) 
3. Nel pannello Backup fare clic su **Obiettivo del backup** per aprire il pannello corrispondente.
   
      ![Pannello Scenario aperto](./media/backup-azure-vms-encryption/select-backup-goal-one.png) 
4. Nel pannello Obiettivo del backup impostare **Posizione di esecuzione del carico di lavoro** su Azure ed **Elementi di cui eseguire il backup** su Macchina virtuale e quindi fare clic su **OK**.
   
   Il pannello Obiettivo di backup si chiude e viene visualizzato il pannello Criterio di backup.
   
   ![Pannello Scenario aperto](./media/backup-azure-vms-encryption/select-backup-goal-two.png) 
5. Nel pannello Criterio di backup selezionare il criterio di backup che si vuole applicare all'insieme di credenziali e fare clic su **OK**.
   
      ![Selezionare il criterio di backup](./media/backup-azure-vms-encryption/setting-rs-backup-policy-new.png) 
   
    I dettagli dei criteri predefiniti vengono elencati nei dettagli. Per creare un criterio, selezionare **Crea nuovo** dal menu a discesa. Dopo aver fatto clic su **OK**, il criterio di backup viene associato all'insieme di credenziali.
   
    Scegliere quindi le VM da associare all'insieme di credenziali.
6. Selezionare le macchine virtuali crittografate da associare al criterio specificato e fare clic su **OK**.
   
      ![Selezionare le macchine virtuali crittografate](./media/backup-azure-vms-encryption/selected-encrypted-vms.png)
7. In questa pagina viene visualizzato un messaggio sull'insieme di credenziali delle chiavi associato alle macchine virtuali crittografate selezionate. Il servizio di backup richiede l'accesso in sola lettura per le chiavi e i segreti nell'insieme di credenziali delle chiavi. Il servizio usa le autorizzazioni per chiavi di backup e segreti, insieme alle macchine virtuali associate. 
   
      ![Messaggio delle macchine virtuali crittografate](./media/backup-azure-vms-encryption/encrypted-vm-message.png)
   
      Dopo aver definito tutte le impostazioni per l'insieme di credenziali, nel pannello Backup fare clic su Abilita backup nella parte inferiore della pagina. Abilita backup consente di distribuire il criterio nell'insieme di credenziali e nelle macchine virtuali.
8. La fase successiva di preparazione è l'installazione dell'agente di macchine virtuali o la verifica che l'agente di macchine virtuali sia installato. Allo stesso scopo, usare la procedura illustrata nell'articolo [Preparare l'ambiente per il backup di macchine virtuali distribuite con Resource Manager](backup-azure-arm-vms-prepare.md). 

### <a name="triggering-backup-job"></a>Attivazione del processo di backup
Per attivare il processo di backup, usare la procedura illustrata nell'articolo [Eseguire il backup di macchine virtuali di Azure in un insieme di credenziali di Servizi di ripristino](backup-azure-arm-vms.md).

## <a name="restore-encrypted-vm"></a>Ripristinare una macchina virtuale crittografata
Per ripristinare la macchina virtuale crittografata, ripristinare prima di tutto i dischi seguendo la procedura illustrata nella sezione **Ripristino dei dischi sottoposti a backup** in [Scelta di una configurazione di ripristino per la macchina virtuale](backup-azure-arm-restore-vms.md#choosing-a-vm-restore-configuration). Seguire quindi la procedura di PowerShell illustrata in [Creare una macchina virtuale da dischi ripristinati](backup-azure-vms-automation.md#create-a-vm-from-restored-disks) per creare una macchina virtuale completa dai dischi ripristinati.

## <a name="troubleshooting-errors"></a>Risoluzione dei problemi
| Operazione | Dettagli errore | Risoluzione |
| --- | --- | --- |
| Backup |Convalida non riuscita perché la macchina virtuale è crittografata con il solo BEK. I backup possono essere abilitati solo per le macchine virtuali crittografate con BEK e KEK. |Le macchine virtuali devono essere crittografate usando BEK e KEK. In seguito, il backup deve essere abilitato. |
| Ripristino |Non è possibile ripristinare una macchina virtuale crittografata se l'insieme di credenziali delle chiavi associato alla macchina in questione non esiste. |Creare l'insieme di credenziali delle chiavi usando [Introduzione all'insieme di credenziali delle chiavi di Azure](../key-vault/key-vault-get-started.md). Se non sono presenti, per ripristinare chiavi e segreti consultare l'articolo [Restore key vault key and secret using Azure Backup](backup-azure-restore-key-secret.md) (Ripristinare chiavi e segreti dell'insieme di credenziali delle chiavi tramite Backup di Azure). |
| Ripristino |Non è possibile ripristinare una macchina virtuale crittografata se chiavi e segreti associati con la macchina virtuale in questione non esistono. |Se non sono presenti, per ripristinare chiavi e segreti consultare l'articolo [Restore key vault key and secret using Azure Backup](backup-azure-restore-key-secret.md) (Ripristinare chiavi e segreti dell'insieme di credenziali delle chiavi tramite Backup di Azure). |
| Ripristino |Il servizio Backup non ha l'autorizzazione per accedere alle risorse nella sottoscrizione. |Come indicato in precedenza, ripristinare prima di tutto i dischi seguendo la procedura illustrata nella sezione **Ripristino dei dischi sottoposti a backup** in [Scelta di una configurazione di ripristino per la macchina virtuale](backup-azure-arm-restore-vms.md#choosing-a-vm-restore-configuration). Successivamente usare PowerShell per [creare una macchina virtuale dai dischi ripristinati](backup-azure-vms-automation.md#create-a-vm-from-restored-disks). 




<!--HONumber=Jan17_HO3-->


