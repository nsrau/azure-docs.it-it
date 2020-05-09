---
title: Creare insiemi di credenziali dei servizi di ripristino
description: Questo articolo illustra come creare insiemi di credenziali dei servizi di ripristino in cui archiviare i backup e i punti di ripristino.
ms.reviewer: sogup
ms.topic: conceptual
ms.date: 05/30/2019
ms.openlocfilehash: 6ac1c7e887f80767d6ff1819476e91cb4b06bf1b
ms.sourcegitcommit: 3beb067d5dc3d8895971b1bc18304e004b8a19b3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/04/2020
ms.locfileid: "82744934"
---
# <a name="create-a-recovery-services-vault"></a>Creare un insieme di credenziali di Servizi di ripristino

Un insieme di credenziali dei Servizi di ripristino è un'entità che archivia i backup e i punti di ripristino che sono stati creati nel corso del tempo. L'insieme di credenziali dei servizi di ripristino contiene anche i criteri di backup associati alle macchine virtuali protette.

Per creare un insieme di credenziali dei servizi di ripristino:

1. Accedere alla propria sottoscrizione nel [portale di Azure](https://portal.azure.com/).

2. Nel menu a sinistra selezionare **Tutti i servizi**.

    ![Selezionare tutti i servizi](./media/backup-create-rs-vault/click-all-services.png)

3. Nella finestra di dialogo **Tutti i servizi** inserire **Servizi di ripristino**. L'elenco delle risorse filtra sulla base degli input. Nell'elenco delle risorse selezionare**Insieme di credenziali di Servizi di ripristino**.

    ![Inserire e selezionare le insiemi di credenziali di Servizi di ripristino](./media/backup-create-rs-vault/all-services.png)

    Viene visualizzato l'elenco degli insiemi di credenziali di Servizi di ripristino.

4. Nel dashboard di **Insiemi di credenziali dei Servizi di ripristino** selezionare **Aggiungi**.

    ![Aggiungere un insieme di credenziali di Servizi di ripristino](./media/backup-create-rs-vault/add-button-create-vault.png)

    Si apre la finestra di dialogo **Insieme di credenziali dei Servizi di ripristino**. Fornire i valori per **Nome**, **Sottoscrizione**, **Gruppo di risorse** e **Località**.

    ![Configurare l'insieme di credenziali di Servizi di ripristino](./media/backup-create-rs-vault/create-new-vault-dialog.png)

   - **Nome**: immettere un nome descrittivo per identificare l'insieme di credenziali. Il nome deve essere univoco nella sottoscrizione di Azure. Specificare un nome che contiene almeno due caratteri, ma non più di 50. Il nome deve iniziare con una lettera e deve contenere solo lettere, numeri e trattini.
   - **Sottoscrizione**: scegliere la sottoscrizione da usare. Se si è un membro di una sola sottoscrizione, verrà visualizzato tale nome. Se non si è certi della sottoscrizione da usare, scegliere quella predefinita (consigliato). Sono disponibili più opzioni solo se l'account aziendale o dell'istituto di istruzione è associato a più sottoscrizioni di Azure.
   - **Gruppo di risorse**: usare un gruppo di risorse esistente oppure crearne uno nuovo. Selezionare **Usa esistente** e nell'elenco a discesa scegliere una risorsa per visualizzare l'elenco di gruppi di risorse disponibili nella sottoscrizione. Per creare un nuovo gruppo di risorse, selezionare **Crea nuovo** e inserire il nome. Per informazioni complete sui gruppi di risorse, vedere [Panoramica di Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).
   - **Posizione**: selezionare l'area geografica per l'insieme di credenziali. Se si crea un insieme di credenziali per proteggere le macchine virtuali, l'insieme di credenziali **deve** trovarsi nella stessa area delle macchine virtuali.

      > [!IMPORTANT]
      > Se non si è certi della posizione della macchina virtuale, chiudere la finestra di dialogo. Passare all'elenco di macchine virtuali nel portale. Se si dispone di macchine virtuali in più aree, creare un insieme di credenziali di Servizi di ripristino in ogni area. Creare l'insieme di credenziali nella prima posizione prima di creare l'insieme di credenziali per un'altra posizione. Non è necessario specificare gli account di archiviazione per archiviare i dati di backup. Questo aspetto viene gestito automaticamente dall'insieme di credenziali dei servizi di ripristino e dal servizio Backup di Azure.
      >
      >

5. Quando si è pronti per creare l'insieme di credenziali di Servizi di ripristino, selezionare **Crea**.

    ![Creare l'insieme di credenziali di Servizi di ripristino](./media/backup-create-rs-vault/click-create-button.png)

    La creazione dell'insieme di credenziali di Servizi di ripristino può richiedere del tempo. Monitorare le notifiche di stato nell'area **Notifiche** nell'angolo in alto a destra del portale. Dopo essere stato creato, l'insieme di credenziali, sarà visualizzabile nell'insieme di credenziali di Servizi di ripristino. Se non viene visualizzato, selezionare **Aggiorna**.

     ![Aggiornare l'elenco dell'insieme di credenziali di backup](./media/backup-create-rs-vault/refresh-button.png)

## <a name="set-storage-redundancy"></a>Impostare la ridondanza di archiviazione

Backup di Azure gestisce automaticamente lo spazio di archiviazione per l'insieme di credenziali. È necessario specificare la modalità di replica dell'archiviazione.

1. Nel pannello **Insieme di credenziali dei servizi di ripristino** fare clic sul nuovo insieme di credenziali. Nella sezione **Impostazioni** fare clic su **Proprietà**.
2. In **Proprietà**, in **configurazione backup**, fare clic su **Aggiorna**.

3. Selezionare il tipo di replica di archiviazione e fare clic su **Salva**.

     ![Impostare la configurazione dell'archiviazione per il nuovo insieme di credenziali](./media/backup-try-azure-backup-in-10-mins/recovery-services-vault-backup-configuration.png)

   - Se si usa Azure come endpoint di archiviazione di backup primario, è consigliabile continuare a usare l'impostazione con **ridondanza geografica** predefinita.
   - Se non si usa Azure come endpoint di archiviazione di backup primario, scegliere l'opzione **Con ridondanza locale**, che riduce i costi di archiviazione di Azure.
   - Altre informazioni sulla ridondanza [geografica](../storage/common/storage-redundancy-grs.md) e [locale](../storage/common/storage-redundancy-lrs.md) .

> [!NOTE]
> Per modificare il **tipo di replica di archiviazione** (con ridondanza locale o con ridondanza geografica) per un insieme di credenziali di servizi di ripristino, è necessario eseguire prima di configurare i backup nell'insieme di credenziali. Dopo aver configurato il backup, l'opzione per la modifica è disabilitata e non è possibile modificare il **tipo di replica di archiviazione**.

## <a name="set-cross-region-restore"></a>Imposta ripristino tra aree

Come una delle opzioni di ripristino, Cross Region Restore (CRR) consente di ripristinare le macchine virtuali di Azure in un'area secondaria, ovvero un' [area abbinata ad Azure](https://docs.microsoft.com/azure/best-practices-availability-paired-regions). Questa opzione consente di:

- eseguire esercitazioni in caso di requisiti di controllo o di conformità
- ripristinare la macchina virtuale o il relativo disco in caso di emergenza nell'area primaria.

Per scegliere questa funzionalità, selezionare **Abilita ripristino tra aree** nel pannello **configurazione backup** .

Per questo processo, esistono implicazioni relative ai prezzi a livello di archiviazione.

>[!NOTE]
>Prima di iniziare:
>
>- Esaminare la [matrice di supporto](backup-support-matrix.md#cross-region-restore) per un elenco di tipi e aree gestiti supportati.
>- La funzionalità di ripristino tra aree (CRR) è ora visualizzata in anteprima in tutte le aree pubbliche di Azure.
>- CRR è una funzionalità di consenso esplicito a livello di insieme di credenziali GRS (disattivata per impostazione predefinita).
>- Usare il comando seguente per caricare la sottoscrizione per questa funzionalità:<br>
>  `Register-AzProviderFeature -FeatureName CrossRegionRestore -ProviderNamespace Microsoft.RecoveryServices`
>- Se si esegue l'onboarding in questa funzionalità durante l'anteprima pubblica limitata, il messaggio di posta elettronica di approvazione della verifica includerà i dettagli relativi ai criteri tariffari.
>- Dopo aver acconsentito, potrebbero essere necessarie fino a 48 ore affinché gli elementi di backup siano disponibili nelle aree secondarie.
>- Attualmente CRR è supportato solo per il tipo di gestione di backup-macchina virtuale di Azure ARM (la macchina virtuale di Azure classica non sarà supportata).  Quando i tipi di gestione aggiuntivi supportano CRR, verranno registrati **automaticamente** .

### <a name="configure-cross-region-restore"></a>Configurare il ripristino tra aree

Un insieme di credenziali creato con ridondanza GRS include l'opzione per configurare la funzionalità di ripristino tra aree. Ogni insieme di credenziali GRS avrà un banner, che sarà collegato alla documentazione. Per configurare CRR per l'insieme di credenziali, passare al pannello configurazione backup che contiene l'opzione per abilitare questa funzionalità.

 ![Banner di configurazione di backup](./media/backup-azure-arm-restore-vms/banner.png)

1. Dal portale passare a insieme di credenziali di servizi di ripristino > impostazioni > proprietà.
2. Per abilitare la funzionalità, fare clic su **Abilita ripristino tra aree in questo** insieme di credenziali.

   ![Prima di fare clic su Abilita ripristino tra aree in questo insieme di credenziali](./media/backup-azure-arm-restore-vms/backup-configuration1.png)

   ![Dopo aver fatto clic su Abilita ripristino tra aree in questo insieme di credenziali](./media/backup-azure-arm-restore-vms/backup-configuration2.png)

Informazioni su come [visualizzare gli elementi di backup nell'area secondaria](backup-azure-arm-restore-vms.md#view-backup-items-in-secondary-region).

Informazioni su come eseguire [il ripristino nell'area secondaria](backup-azure-arm-restore-vms.md#restore-in-secondary-region).

Informazioni su come [monitorare i processi di ripristino dell'area secondaria](backup-azure-arm-restore-vms.md#monitoring-secondary-region-restore-jobs).

## <a name="modifying-default-settings"></a>Modifica delle impostazioni predefinite

Si consiglia vivamente di rivedere le impostazioni predefinite per il **tipo di replica di archiviazione** e **le impostazioni di sicurezza** prima di configurare i backup nell'insieme di credenziali.

- Per impostazione predefinita, il **tipo di replica di archiviazione** è impostato su con **ridondanza geografica**. Dopo aver configurato il backup, l'opzione per la modifica è disabilitata. Per esaminare e modificare le impostazioni, seguire questa [procedura](https://docs.microsoft.com/azure/backup/backup-create-rs-vault#set-storage-redundancy) .

- Per impostazione predefinita, l' **eliminazione** temporanea è **abilitata** negli insiemi di credenziali appena creati per proteggere i dati di backup da eliminazioni accidentali o dannose. Per esaminare e modificare le impostazioni, seguire questa [procedura](https://docs.microsoft.com/azure/backup/backup-azure-security-feature-cloud#enabling-and-disabling-soft-delete) .

## <a name="next-steps"></a>Passaggi successivi

Informazioni [su](backup-azure-recovery-services-vault-overview.md) Insiemi di credenziali dei servizi di ripristino.
Informazioni [su](backup-azure-delete-vault.md) Eliminare gli insiemi di credenziali dei servizi di ripristino.
