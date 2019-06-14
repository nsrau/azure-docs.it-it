---
title: File di inclusione
description: File di inclusione
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: include
ms.date: 10/18/2018
ms.author: raynew
ms.custom: include file
ms.openlocfilehash: 74fe531c07aa871b06b5d0773f7e8fb0ade80be4
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66127697"
---
## <a name="create-a-recovery-services-vault"></a>Creare un insieme di credenziali di Servizi di ripristino
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
   - **Sottoscrizione** scegliere la sottoscrizione da usare. Se si è un membro di una sola sottoscrizione, verrà visualizzato tale nome. Se non si è certi della sottoscrizione da usare, scegliere quella predefinita (consigliato). Sono disponibili più opzioni solo se l'account aziendale o dell'istituto di istruzione è associato a più sottoscrizioni di Azure.
   - **Gruppo di risorse**: Usare un gruppo di risorse esistente oppure crearne uno nuovo. Selezionare **Usa esistente** e nell'elenco a discesa scegliere una risorsa per visualizzare l'elenco di gruppi di risorse disponibili nella sottoscrizione. Per creare un nuovo gruppo di risorse, selezionare **Crea nuovo** e inserire il nome. Per informazioni complete sui gruppi di risorse, vedere [Panoramica di Azure Resource Manager](../articles/azure-resource-manager/resource-group-overview.md).
   - **Località**: selezionare l'area geografica per l'insieme di credenziali. Se si crea un insieme di credenziali per proteggere le macchine virtuali, l'insieme di credenziali **deve** trovarsi nella stessa area delle macchine virtuali.

      > [!IMPORTANT]
      > Se non si è certi della posizione della macchina virtuale, chiudere la finestra di dialogo. Passare all'elenco di macchine virtuali nel portale. Se si dispone di macchine virtuali in più aree, creare un insieme di credenziali di Servizi di ripristino in ogni area. Creare l'insieme di credenziali nella prima posizione prima di creare l'insieme di credenziali per un'altra posizione. Non è necessario specificare gli account di archiviazione per archiviare i dati di backup. Questo aspetto viene gestito automaticamente dall'insieme di credenziali dei servizi di ripristino e dal servizio Backup di Azure.
      >
      >

5. Quando si è pronti per creare l'insieme di credenziali di Servizi di ripristino, selezionare **Crea**.

    ![Creare l'insieme di credenziali di Servizi di ripristino](./media/backup-create-rs-vault/click-create-button.png)

    La creazione dell'insieme di credenziali di Servizi di ripristino può richiedere del tempo. Monitorare le notifiche di stato nell'area **Notifiche** nell'angolo in alto a destra del portale. Dopo essere stato creato, l'insieme di credenziali, sarà visualizzabile nell'insieme di credenziali di Servizi di ripristino. Se non viene visualizzato, selezionare **Aggiorna**.

     ![Aggiornare l'elenco dell'insieme di credenziali di backup](./media/backup-create-rs-vault/refresh-button.png)
