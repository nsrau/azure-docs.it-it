---
title: 'Backup di Azure: creare insiemi di credenziali dei servizi di ripristino'
description: Questo articolo illustra come creare insiemi di credenziali dei servizi di ripristino in cui archiviare i backup e i punti di ripristino.
ms.reviewer: sogup
author: dcurwin
manager: carmonm
keywords: Insieme di credenziali di servizi di ripristino; Backup delle macchine virtuali di Azure; Ripristino delle macchine virtuali di Azure;
ms.service: backup
ms.topic: conceptual
ms.date: 05/30/2019
ms.author: dacurwin
ms.openlocfilehash: 30e2f8812b14f27b57ef22e30aea89f19fa56e58
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/14/2019
ms.locfileid: "74074871"
---
# <a name="create-a-recovery-services-vault"></a>Creare un insieme di credenziali di Servizi di ripristino

Un insieme di credenziali dei Servizi di ripristino è un'entità che archivia i backup e i punti di ripristino che sono stati creati nel corso del tempo. L'insieme di credenziali dei servizi di ripristino contiene anche i criteri di backup associati alle macchine virtuali protette.

Per creare un insieme di credenziali dei servizi di ripristino:

1. Accedere alla sottoscrizione nel [portale di Azure](https://portal.azure.com/).

2. Nel menu a sinistra selezionare **Tutti i servizi**.

    ![Selezionare tutti i servizi](./media/backup-create-rs-vault/click-all-services.png)

3. Nella finestra di dialogo **Tutti i servizi** inserire **Servizi di ripristino**. L'elenco delle risorse filtra sulla base degli input. Nell'elenco delle risorse selezionare**Insieme di credenziali di Servizi di ripristino**.

    ![Inserire e selezionare le insiemi di credenziali di Servizi di ripristino](./media/backup-create-rs-vault/all-services.png)

    Verrà visualizzato l'elenco degli insiemi di credenziali di Servizi di ripristino nella sottoscrizione.

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
   - Altre informazioni sulla [ridondanza geografica](../storage/common/storage-redundancy-grs.md) e [locale](../storage/common/storage-redundancy-lrs.md) .

> [!NOTE]
> Per modificare il **tipo di replica di archiviazione** (con ridondanza locale o con ridondanza geografica) per un insieme di credenziali di servizi di ripristino, è necessario eseguire prima di configurare i backup nell'insieme di credenziali. Dopo aver configurato il backup, l'opzione per la modifica è disabilitata e non è possibile modificare il **tipo di replica di archiviazione**.

## <a name="next-steps"></a>Passaggi successivi

Informazioni [su](backup-azure-recovery-services-vault-overview.md) Insiemi di credenziali dei servizi di ripristino.
Informazioni [su](backup-azure-delete-vault.md) Eliminare gli insiemi di credenziali dei servizi di ripristino.
