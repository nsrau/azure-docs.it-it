---
title: File di inclusione
description: File di inclusione
services: backup
author: markgalioto
ms.service: backup
ms.topic: include
ms.date: 5/14/2018
ms.author: markgal
ms.custom: include file
ms.openlocfilehash: 5590da80a1c217e7902e8e010688e40f5624898c
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2018
ms.locfileid: "38730499"
---
## <a name="create-a-recovery-services-vault"></a>Creare un insieme di credenziali di Servizi di ripristino
Un insieme di credenziali dei servizi di ripristino è un'entità che archivia i backup e i punti di ripristino che sono stati creati nel corso del tempo. L'insieme di credenziali dei servizi di ripristino contiene anche i criteri di backup associati alle macchine virtuali protette.

Per creare un insieme di credenziali dei servizi di ripristino:

1. Accedere alla propria sottoscrizione nel [portale di Azure](https://portal.azure.com/).
2. Nel menu a sinistra selezionare **Tutti i servizi**.

    ![Scegliere l'opzione Tutti i servizi nel menu principale](./media/backup-create-rs-vault/click-all-services.png) <br/>

3. Nella finestra di dialogo Tutti i servizi digitare *Servizi di ripristino*. Non appena si inizia a digitare, l'elenco delle risorse viene filtrato in base all'input. Quando viene visualizzata, selezionare l'opzione **Insiemi di credenziali di Servizi di ripristino**.

    ![Digitare Servizi di ripristino nella finestra di dialogo Tutti i servizi](./media/backup-create-rs-vault/all-services.png) <br/>

    Viene visualizzato l'elenco degli insiemi di credenziali di Servizi di ripristino.
4. Nel menu di **Insiemi di credenziali dei servizi di ripristino** selezionare **Aggiungi**.

    ![Creare un insieme di credenziali dei servizi di ripristino - Passaggio 2](./media/backup-create-rs-vault/add-button-create-vault.png)

    Verrà aperto il menu **Insiemi di credenziali dei servizi di ripristino**, in cui viene richiesto di specificare le informazioni relative a **Nome**, **Sottoscrizione**, **Gruppo di risorse** e **Località**.

    ![Riquadro "Insiemi di credenziali dei servizi di ripristino"](./media/backup-create-rs-vault/create-new-vault-dialog.png)
5. Nel campo **Nome**digitare un nome descrittivo per identificare l'insieme di credenziali. Il nome deve essere univoco nella sottoscrizione di Azure. Digitare un nome che contiene almeno due caratteri, ma non più di 50. Il nome deve iniziare con una lettera e può contenere solo lettere, numeri e trattini.
6. In **Sottoscrizione** scegliere la sottoscrizione da usare. Se si è un membro di una sola sottoscrizione, verrà visualizzato tale nome. Se non si è certi della sottoscrizione da usare, usare quella predefinita (o suggerita). Sono disponibili più opzioni solo se l'account aziendale o dell'istituto di istruzione è associato a più sottoscrizioni di Azure.
7. Per **Gruppo di risorse** è possibile usare un gruppo di risorse esistente oppure crearne uno nuovo. Selezionare **Usa esistente** e fare clic sul menu a discesa per visualizzare l'elenco di gruppi di risorse disponibili nella sottoscrizione. Per creare un nuovo gruppo di risorse, selezionare **Crea nuovo** e digitare il nome. Per informazioni complete sui gruppi di risorse, vedere [Panoramica di Azure Resource Manager](../articles/azure-resource-manager/resource-group-overview.md).
8. In **Posizione** selezionare l'area geografica per l'insieme di credenziali. Se si sta creando un insieme di credenziali per proteggere le macchine virtuali, l'insieme di credenziali *deve* essere nella stessa area delle macchine virtuali.

   > [!IMPORTANT]
   > Se non si è certi della località in cui si trova la VM, chiudere la finestra di dialogo per la creazione dell'insieme di credenziali e passare all'elenco delle macchine virtuali nel portale. Se si hanno macchine virtuali in più aree, creare un insieme di credenziali di Servizi di ripristino in ogni area. Creare l'insieme di credenziali nella prima località prima di passare a quella successiva. Non è necessario specificare account di archiviazione per archiviare i dati di backup. Questo aspetto viene gestito automaticamente dall'insieme di credenziali dei servizi di ripristino e dal servizio Backup di Azure.
   >
   >

9. Quando si è pronti per creare l'insieme di credenziali di Servizi di ripristino, fare clic su **Crea**.

    ![Elenco degli insiemi di credenziali per il backup](./media/backup-create-rs-vault/click-create-button.png)

    La creazione dell'insieme di credenziali dei servizi di ripristino può richiedere alcuni minuti. Monitorare le notifiche di stato nella sezione Notifiche, ovvero l'area superiore destra del portale. Al termine della creazione, l'insieme di credenziali verrà visualizzato nell'elenco degli insiemi di credenziali dei servizi di ripristino. Se l'insieme di credenziali ancora non viene visualizzato, fare clic su **Aggiorna**.

     ![Elenco degli insiemi di credenziali per il backup](./media/backup-create-rs-vault/refresh-button.png)
