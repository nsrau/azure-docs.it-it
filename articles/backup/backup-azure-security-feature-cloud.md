---
title: Funzionalità di sicurezza che consentono di proteggere i carichi di lavoro cloud che usano backup di Azure
description: Informazioni su come usare le funzionalità di sicurezza in backup di Azure per rendere più sicuri i backup.
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: dacurwin
ms.openlocfilehash: b882b8ee08c38b6313558916ab46f80ce9dd5130
ms.sourcegitcommit: 2ed6e731ffc614f1691f1578ed26a67de46ed9c2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/19/2019
ms.locfileid: "71129344"
---
# <a name="security-features-to-help-protect-cloud-workloads-that-use-azure-backup"></a>Funzionalità di sicurezza che consentono di proteggere i carichi di lavoro cloud che usano backup di Azure

Le preoccupazioni riguardo ai problemi di sicurezza, come malware, ransomware e intrusioni, aumentano continuamente. Questi problemi di sicurezza possono essere costosi in termini di denaro e di dati. Per evitare tali attacchi, backup di Azure offre ora funzionalità di sicurezza che consentono di proteggere i dati di backup anche dopo l'eliminazione. Una di queste funzionalità è l'eliminazione temporanea. Con l'eliminazione temporanea, anche se un attore malintenzionato Elimina il backup di una macchina virtuale (o i dati di backup vengono accidentalmente eliminati), i dati di backup vengono conservati per 14 giorni aggiuntivi, consentendo il recupero dell'elemento di backup senza perdita di dati. Questi ulteriori 14 giorni di conservazione dei dati di backup nello stato "eliminazione temporanea" non comportano alcun costo per il cliente.

> [!NOTE]
> L'eliminazione temporanea protegge solo i dati di backup eliminati. Se una macchina virtuale viene eliminata senza un backup, la funzionalità di eliminazione temporanea non manterrà i dati. Tutte le risorse devono essere protette con backup di Azure per garantire la resilienza completa.
>

## <a name="soft-delete"></a>Eliminazione temporanea

### <a name="supported-regions"></a>Aree supportate

L'eliminazione temporanea è attualmente supportata negli Stati Uniti centro-occidentali, Asia orientale, Canada centrale, Canada orientale, Francia centrale, Francia meridionale, Corea centrale, Corea meridionale, Regno Unito meridionale, Regno Unito occidentale, Australia orientale, Australia sudorientale, Europa settentrionale, Stati Uniti occidentali, Stati Uniti occidentali, Stati Uniti centro-meridionali Asia orientale, Stati Uniti centro-settentrionali, Stati Uniti centro-meridionali, Giappone orientale, Giappone occidentale, India meridionale, India centrale, India occidentale, Stati Uniti orientali 2, Svizzera settentrionale, Svizzera occidentale e tutte le aree nazionali.

### <a name="soft-delete-for-vms"></a>Eliminazione temporanea per le macchine virtuali

1. Per eliminare i dati di backup di una macchina virtuale, è necessario arrestare il backup. Nel portale di Azure passare all'insieme di credenziali di servizi di ripristino, fare clic con il pulsante destro del mouse sull'elemento di backup e scegliere **Interrompi backup**.

   ![Screenshot degli elementi di backup portale di Azure](./media/backup-azure-security-feature-cloud/backup-stopped.png)

2. Nella finestra seguente verrà offerta la possibilità di eliminare o mantenere i dati di backup. Se si sceglie **Elimina dati di backup** e quindi **Interrompi backup**, il backup della macchina virtuale non verrà eliminato definitivamente. I dati di backup verranno invece conservati per 14 giorni nello stato di eliminazione temporanea. Se si sceglie **Elimina dati di backup** , viene inviato un avviso di eliminazione tramite posta elettronica all'ID di posta elettronica configurato, che informa l'utente che rimane un periodo di conservazione esteso per i dati di backup di 14 giorni. Viene inoltre inviato un avviso di posta elettronica il giorno 12 per informare che ci sono altri due giorni rimasti per resuscitare i dati eliminati. L'eliminazione viene posticipata fino al 15 ° giorno, quando si verifica l'eliminazione permanente e viene inviato un avviso di posta elettronica finale che informa sull'eliminazione permanente dei dati.

   ![Screenshot della schermata portale di Azure, Interrompi backup](./media/backup-azure-security-feature-cloud/delete-backup-data.png)

3. Durante questi 14 giorni, nell'insieme di credenziali di servizi di ripristino, la macchina virtuale eliminata temporaneamente verrà visualizzata con un'icona "soft-delete" rossa accanto.

   ![Screenshot di portale di Azure, VM in stato di eliminazione temporanea](./media/backup-azure-security-feature-cloud/vm-soft-delete.png)

   > [!NOTE]
   > Se nell'insieme di credenziali sono presenti elementi di backup eliminati temporaneamente, non è possibile eliminare l'insieme di credenziali in quel momento. Provare l'eliminazione dell'insieme di credenziali dopo che gli elementi di backup sono stati eliminati definitivamente e non è presente alcun elemento nello stato di eliminazione temporanea lasciato nell'insieme di credenziali.

4. Per ripristinare la macchina virtuale eliminata temporaneamente, è prima necessario annullarne l'eliminazione. Per annullare l'eliminazione, scegliere la macchina virtuale eliminata temporaneamente, quindi fare clic sull'opzione **Annulla eliminazione**.

   ![Screenshot della portale di Azure, annullamento dell'eliminazione della macchina virtuale](./media/backup-azure-security-feature-cloud/choose-undelete.png)

   Verrà visualizzata una finestra in cui viene visualizzato un avviso che indica che se si sceglie Annulla eliminazione, tutti i punti di ripristino per la macchina virtuale verranno annullati e resi disponibili per l'esecuzione di un'operazione di ripristino. La macchina virtuale verrà mantenuta in uno stato "Arresta protezione dati con Mantieni dati" con i backup sospesi e i dati di backup mantenuti per sempre senza alcun criterio di backup valido.

   ![Screenshot di portale di Azure, confermare l'annullamento dell'eliminazione della macchina virtuale](./media/backup-azure-security-feature-cloud/undelete-vm.png)

   A questo punto, è anche possibile ripristinare la macchina virtuale selezionando **Ripristina macchina virtuale** dal punto di ripristino scelto.  

   ![Screenshot dell'opzione di portale di Azure, ripristino macchina virtuale](./media/backup-azure-security-feature-cloud/restore-vm.png)

   > [!NOTE]
   > Il Garbage Collector viene eseguito e puliti i punti di ripristino scaduti solo dopo che l'utente esegue l'operazione di **ripresa del backup** .

5. Al termine del processo di annullamento dell'eliminazione, lo stato tornerà a "Interrompi backup con Mantieni dati", quindi è possibile scegliere **Riprendi backup**. L'operazione di **ripresa del backup** riporta l'elemento di backup nello stato attivo, associato a un criterio di backup selezionato dall'utente che definisce le pianificazioni di backup e conservazione.

   ![Screenshot del portale di Azure, Riprendi l'opzione backup](./media/backup-azure-security-feature-cloud/resume-backup.png)

Questo diagramma di flusso Mostra i diversi passaggi e Stati di un elemento di backup:

![Ciclo di vita dell'elemento di backup eliminato temporaneamente](./media/backup-azure-security-feature-cloud/lifecycle.png)

Per ulteriori informazioni, vedere la sezione [domande frequenti](backup-azure-security-feature-cloud.md#frequently-asked-questions) più avanti.

## <a name="other-security-features"></a>Altre funzionalità di sicurezza

### <a name="storage-side-encryption"></a>Crittografia lato archiviazione

Archiviazione di Azure crittografa automaticamente i dati in modo permanente nel cloud. La crittografia protegge i dati e ti aiuta a soddisfare gli impegni di sicurezza e conformità dell'organizzazione. I dati in archiviazione di Azure vengono crittografati e decrittografati in modo trasparente usando la crittografia AES a 256 bit, una delle crittografie a blocchi più solide disponibili ed è conforme a FIPS 140-2. La crittografia di archiviazione di Azure è simile alla crittografia BitLocker per Windows. Backup di Azure crittografa automaticamente i dati prima di archiviarli. Il servizio Archiviazione di Azure decrittografa i dati prima di recuperarli.  

In Azure, i dati in transito tra archiviazione di Azure e l'insieme di credenziali sono protetti da HTTPS. Questi dati rimangono all'interno della rete backbone di Azure.

Per altre informazioni, vedere [crittografia di archiviazione di Azure per dati](https://docs.microsoft.com/en-in/azure/storage/common/storage-service-encryption)inattivi.

### <a name="vm-encryption"></a>Crittografia VM

È possibile eseguire il backup e il ripristino di macchine virtuali di Azure (VM) Windows o Linux con dischi crittografati tramite il servizio backup di Azure. Per istruzioni, vedere backup [e ripristino di macchine virtuali crittografate con backup di Azure](https://docs.microsoft.com/en-us/azure/backup/backup-azure-vms-encryption).

### <a name="protection-of-azure-backup-recovery-points"></a>Protezione dei punti di ripristino di backup di Azure

Gli account di archiviazione usati dagli insiemi di credenziali dei servizi di ripristino sono isolati e non è possibile accedervi dagli utenti per eventuali scopi dannosi. L'accesso è consentito solo tramite le operazioni di gestione di backup di Azure, ad esempio il ripristino. Queste operazioni di gestione sono controllate tramite il controllo degli accessi in base al ruolo (RBAC).

Per altre informazioni, vedere [usare il controllo degli accessi in base al ruolo per gestire i punti di ripristino di backup di Azure](https://docs.microsoft.com/en-us/azure/backup/backup-rbac-rs-vault).

## <a name="frequently-asked-questions"></a>Domande frequenti

### <a name="soft-delete"></a>Eliminazione temporanea

#### <a name="do-i-need-to-enable-the-soft-delete-feature-on-every-vault"></a>È necessario abilitare la funzionalità di eliminazione temporanea in ogni insieme di credenziali?

No, viene compilata e abilitata per impostazione predefinita per tutti gli insiemi di credenziali dei servizi di ripristino.

#### <a name="can-i-configure-the-number-of-days-for-which-my-data-will-be-retained-in-soft-deleted-state-after-delete-operation-is-complete"></a>È possibile configurare il numero di giorni per cui i dati verranno conservati nello stato di eliminazione temporanea dopo il completamento dell'operazione di eliminazione?

No, viene fissato a 14 giorni di conservazione aggiuntiva dopo l'operazione di eliminazione.
 
#### <a name="do-i-need-to-pay-the-cost-for-this-additional-14-day-retention"></a>È necessario pagare il costo per questa conservazione aggiuntiva di 14 giorni?

No, questo periodo di conservazione aggiuntivo di 14 giorni è gratuito come parte della funzionalità di eliminazione temporanea.
 
#### <a name="can-i-perform-a-restore-operation-when-my-data-is-in-soft-delete-state"></a>È possibile eseguire un'operazione di ripristino quando i dati sono in stato di eliminazione temporanea?

No, è necessario annullare l'eliminazione della risorsa eliminata temporaneamente per poter eseguire il ripristino. L'operazione di annullamento dell'eliminazione riporterà la risorsa nello **stato di arresto della protezione con Mantieni dati** in cui è possibile eseguire il ripristino in qualsiasi momento. Il Garbage Collector rimane in pausa in questo stato.
 
#### <a name="will-my-snapshots-follow-the-same-lifecycle-as-my-recovery-points-in-the-vault"></a>Gli snapshot seguiranno lo stesso ciclo di vita dei punti di ripristino nell'insieme di credenziali?

Sì.
 
#### <a name="how-can-i-trigger-the-scheduled-backups-again-for-a-soft-deleted-resource"></a>Come è possibile attivare di nuovo i backup pianificati per una risorsa eliminata temporaneamente?

L'annullamento dell'eliminazione seguito dall'operazione di ripresa proteggerà di nuovo la risorsa. L'operazione di ripresa associa un criterio di backup per attivare i backup pianificati con il periodo di memorizzazione selezionato. Inoltre, il Garbage Collector viene eseguito non appena l'operazione di ripresa viene completata. Se si desidera eseguire un ripristino da un punto di ripristino che supera la data di scadenza, è consigliabile eseguire l'operazione prima di attivare l'operazione di ripresa.
 
#### <a name="can-i-delete-my-vault-if-there-are-soft-deleted-items-in-the-vault"></a>È possibile eliminare l'insieme di credenziali se sono presenti elementi eliminati temporaneamente nell'insieme di credenziali?

Non è possibile eliminare l'insieme di credenziali di servizi di ripristino se sono presenti elementi di backup nello stato di eliminazione temporanea nell'insieme di credenziali. Gli elementi eliminati temporaneamente vengono eliminati definitivamente dopo 14 giorni dall'operazione di eliminazione. È possibile eliminare l'insieme di credenziali solo dopo che tutti gli elementi eliminati temporaneamente sono stati eliminati.  

#### <a name="can-i-delete-the-data-earlier-than-the-14-days-soft-delete-period-after-deletion"></a>È possibile eliminare i dati prima del periodo di eliminazione temporanea di 14 giorni dopo l'eliminazione?

No. Non è possibile forzare l'eliminazione degli elementi eliminati temporaneamente, che vengono eliminati automaticamente dopo 14 giorni. Questa funzionalità di sicurezza è abilitata per salvaguardare i dati di backup da eliminazioni accidentali o dannose.  È necessario attendere 14 giorni prima di eseguire qualsiasi altra azione nella macchina virtuale.  Gli elementi eliminati temporaneamente non verranno addebitati.  Se è necessario proteggere nuovamente le VM contrassegnate per l'eliminazione temporanea entro 14 giorni in un nuovo insieme di credenziali, contattare il supporto tecnico Microsoft.

#### <a name="can-soft-delete-operations-be-performed-in-powershell-or-cli"></a>È possibile eseguire operazioni di eliminazione temporanea in PowerShell o nell'interfaccia della riga di comando?

No, il supporto per PowerShell o l'interfaccia della riga di comando non è attualmente disponibile.

#### <a name="is-soft-delete-supported-for-other-cloud-workloads-like-sql-server-in-azure-vms-and-sap-hana-in-azure-vms"></a>L'eliminazione temporanea è supportata per altri carichi di lavoro cloud, ad esempio SQL Server in macchine virtuali di Azure e SAP HANA in macchine virtuali di Azure?

No. L'eliminazione temporanea è attualmente supportata solo per le macchine virtuali di Azure.

## <a name="next-steps"></a>Passaggi successivi

* Vedere informazioni sui [controlli di sicurezza per backup di Azure](backup-security-controls.md).
