---
title: File di inclusione
description: File di inclusione
services: backup
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: include
ms.date: 10/18/2018
ms.author: dacurwin
ms.custom: include file
ms.openlocfilehash: c111752dcca03781dfb2a11fb100dafc4bad90f7
ms.sourcegitcommit: 4313e0d13714559d67d51770b2b9b92e4b0cc629
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/27/2020
ms.locfileid: "91400955"
---
## <a name="create-a-recovery-services-vault"></a>Creare un insieme di credenziali di Servizi di ripristino

Un insieme di credenziali di Servizi di ripristino è un'entità di gestione che archivia i punti di ripristino creati nel tempo e fornisce un'interfaccia per l'esecuzione di operazioni correlate al backup, come l'esecuzione di backup su richiesta, l'esecuzione di ripristini e la creazione di criteri di backup.

Seguire questa procedura per creare un insieme di credenziali di Servizi di ripristino.

1. Accedere alla propria sottoscrizione nel [portale di Azure](https://portal.azure.com/).

1. Nel menu a sinistra selezionare **Tutti i servizi**.

    ![Selezionare tutti i servizi](./media/backup-create-rs-vault/click-all-services.png)

1. Nella finestra di dialogo **Tutti i servizi** inserire *Servizi di ripristino*. L'elenco delle risorse filtra sulla base degli input. Nell'elenco delle risorse selezionare**Insieme di credenziali di Servizi di ripristino**.

    ![Inserire e selezionare le insiemi di credenziali di Servizi di ripristino](./media/backup-create-rs-vault/all-services.png)

    Viene visualizzato l'elenco degli insiemi di credenziali di Servizi di ripristino.

1. Nel dashboard di **Insiemi di credenziali dei Servizi di ripristino** selezionare **Aggiungi**.

    ![Aggiungere un insieme di credenziali di Servizi di ripristino](./media/backup-create-rs-vault/add-button-create-vault.png)

    Si apre la finestra di dialogo **Insieme di credenziali dei Servizi di ripristino**. Fornire i valori per **Nome**, **Sottoscrizione**, **Gruppo di risorse** e **Località**.

    ![Configurare l'insieme di credenziali di Servizi di ripristino](./media/backup-create-rs-vault/create-new-vault-dialog.png)

   - **Name**: immettere un nome descrittivo per identificare l'insieme di credenziali. Il nome deve essere univoco nella sottoscrizione di Azure. Specificare un nome con un minimo di 2 caratteri e un massimo di 50 caratteri. Il nome deve iniziare con una lettera e deve contenere solo lettere, numeri e trattini.
   - **Sottoscrizione** scegliere la sottoscrizione da usare. Se si è un membro di una sola sottoscrizione, verrà visualizzato tale nome. Se non si è certi della sottoscrizione da usare, scegliere quella predefinita (consigliato). Sono disponibili più opzioni solo se l'account aziendale o dell'istituto di istruzione è associato a più sottoscrizioni di Azure.
   - **Gruppo di risorse**: Usare un gruppo di risorse esistente oppure crearne uno nuovo. Selezionare **Usa esistente** e nell'elenco a discesa scegliere una risorsa per visualizzare l'elenco di gruppi di risorse disponibili nella sottoscrizione. Per creare un nuovo gruppo di risorse, selezionare **Crea nuovo** e inserire il nome. Per altre informazioni sui gruppi di risorse, vedere [Panoramica di Azure Resource Manager](../articles/azure-resource-manager/management/overview.md).
   - **Località**: selezionare l'area geografica per l'insieme di credenziali. Per proteggere qualsiasi origine dati con un insieme di credenziali, quest'ultimo *deve* trovarsi nella stessa area dell'origine dati.

      > [!IMPORTANT]
      > In caso di dubbi sulla posizione dell'origine dati, chiudere la finestra di dialogo. Passare all'elenco delle risorse nel portale. Se si hanno origini dati in più aree, creare un insieme di credenziali di Servizi di ripristino per ogni area. Creare l'insieme di credenziali nella prima località prima che nell'altra. Non è necessario specificare gli account di archiviazione per archiviare i dati di backup. Questo aspetto viene gestito automaticamente dall'insieme di credenziali di Servizi di ripristino e da Backup di Azure.
      >
      >

1. Dopo avere specificato i valori, selezionare **Rivedi e crea**.

    ![Screenshot del pulsante Rivedi e crea nel processo di creazione di un insieme di credenziali di Servizi di ripristino.](./media/backup-create-rs-vault/review-and-create.png)

1. Quando si è pronti per creare l'insieme di credenziali di Servizi di ripristino, selezionare **Crea**.

    ![Creare l'insieme di credenziali di Servizi di ripristino](./media/backup-create-rs-vault/click-create-button.png)

    La creazione dell'insieme di credenziali di Servizi di ripristino può richiedere del tempo. Monitorare le notifiche di stato nell'area **Notifiche** nell'angolo in alto a destra del portale. Dopo essere stato creato, l'insieme di credenziali, sarà visualizzabile nell'insieme di credenziali di Servizi di ripristino. Se non viene visualizzato, selezionare **Aggiorna**.

     ![Aggiornare l'elenco dell'insieme di credenziali di backup](./media/backup-create-rs-vault/refresh-button.png)

>[!IMPORTANT]
> È consigliabile rivedere le impostazioni predefinite di **Tipo di replica di archiviazione** e **Impostazioni di sicurezza** prima di configurare i backup nell'insieme di credenziali. Per altre informazioni, vedere la sezione [Impostare la ridondanza di archiviazione](https://docs.microsoft.com/azure/backup/backup-create-rs-vault#set-storage-redundancy).
