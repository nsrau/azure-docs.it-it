---
title: Abilitare il backup quando si crea una macchina virtuale di Azure con backup di Azure
description: Viene descritto come abilitare il backup quando si crea una macchina virtuale di Azure con backup di Azure.
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 06/13/2019
ms.author: dacurwin
ms.openlocfilehash: e873980ce7aab1c5454a28e88df24bdb189c4860
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/14/2019
ms.locfileid: "74074840"
---
# <a name="enable-backup-when-you-create-an-azure-vm"></a>Abilitare il backup quando si crea una macchina virtuale di Azure

Usare il servizio backup di Azure per eseguire il backup di macchine virtuali (VM) di Azure. Viene eseguito il backup delle macchine virtuali in base a una pianificazione specificata in un criterio di backup e i punti di ripristino vengono creati dai backup. I punti di ripristino vengono archiviati negli insiemi di credenziali dei servizi di ripristino.

Questo articolo illustra come abilitare il backup quando si crea una macchina virtuale (VM) nell'portale di Azure.  

## <a name="before-you-start"></a>Prima di iniziare

- [Controllare](backup-support-matrix-iaas.md#supported-backup-actions) quali sistemi operativi sono supportati se si Abilita il backup quando si crea una macchina virtuale.

## <a name="sign-in-to-azure"></a>Accedere ad Azure

Se non è già stato effettuato l'accesso al proprio account, accedere al [portale di Azure](https://portal.azure.com).

## <a name="create-a-vm-with-backup-configured"></a>Creare una VM con il backup configurato

1. In portale di Azure fare clic su **Crea una risorsa**.

2. In Azure Marketplace fare clic su **calcolo**e quindi selezionare un'immagine di macchina virtuale.

3. Configurare la VM in base alle istruzioni di [Windows](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal) o [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/quick-create-portal) .

4. Nella scheda **gestione** , in **Abilita backup**, fare clic **su attiva**.
5. Backup di backup di Azure in un insieme di credenziali di servizi di ripristino. Se non si dispone di un insieme di credenziali esistente, fare clic su **Crea nuovo** .
6. Accettare il nome dell'insieme di credenziali suggerito o specificarne di personalizzati.
7. Specificare o creare un gruppo di risorse in cui risiederà l'insieme di credenziali. L'insieme di credenziali del gruppo di risorse può essere diverso dal gruppo di risorse VM.

    ![Abilitare il backup per una macchina virtuale](./media/backup-during-vm-creation/enable-backup.png)

8. Accettare i criteri di backup predefiniti o modificare le impostazioni.
    - Un criterio di backup specifica la frequenza con cui eseguire snapshot di backup della macchina virtuale e il tempo di conservazione delle copie di backup.
    - Il criterio predefinito esegue il backup della VM una volta al giorno.
    - È possibile personalizzare i criteri di backup per una macchina virtuale di Azure per eseguire backup giornalieri o settimanali.
    - [Altre](backup-azure-vms-introduction.md#backup-and-restore-considerations) informazioni sulle considerazioni relative al backup per le VM di Azure.
    - [Altre](backup-instant-restore-capability.md) informazioni sulla funzionalità di ripristino istantaneo.

      ![Criteri di backup predefiniti](./media/backup-during-vm-creation/daily-policy.png)

> [!NOTE]
> Il servizio backup di Azure crea un gruppo di risorse distinto (diverso dal gruppo di risorse VM) per archiviare gli snapshot, con il formato di denominazione **AzureBackupRG_geography_number** (ad esempio: AzureBackupRG_northeurope_1). I dati in questo gruppo di risorse verranno conservati per la durata in giorni, come specificato nella sezione *Mantieni snapshot Instant Recovery* del criterio di backup della macchina virtuale di Azure.  L'applicazione di un blocco a questo gruppo di risorse può causare errori di backup. <br> Questo gruppo di risorse deve essere escluso da qualsiasi restrizione relativa a nome/tag poiché i criteri di restrizione bloccano la creazione di raccolte di punti risorse in un nuovo errore causando errori di backup.

## <a name="start-a-backup-after-creating-the-vm"></a>Avviare un backup dopo la creazione della macchina virtuale

Il backup della macchina virtuale viene eseguito in base ai criteri di backup. Tuttavia, si consiglia di eseguire un backup iniziale.

Dopo aver creato la macchina virtuale, eseguire le operazioni seguenti:

1. Nelle proprietà della macchina virtuale fare clic su **backup**. Lo stato della macchina virtuale è il backup iniziale in sospeso fino a quando non viene eseguito il backup iniziale
2. Fare clic su Esegui backup **ora** per eseguire un backup su richiesta.

    ![Eseguire un backup su richiesta](./media/backup-during-vm-creation/run-backup.png)

## <a name="use-a-resource-manager-template-to-deploy-a-protected-vm"></a>Usare un modello di Gestione risorse per distribuire una macchina virtuale protetta

I passaggi precedenti illustrano come usare la portale di Azure per creare una macchina virtuale e proteggerla in un insieme di credenziali di servizi di ripristino. Per distribuire rapidamente una o più macchine virtuali e proteggerle in un insieme di credenziali di servizi di ripristino, vedere il modello [distribuire una macchina virtuale Windows e abilitare il backup](https://azure.microsoft.com/resources/templates/101-recovery-services-create-vm-and-configure-backup/).

## <a name="next-steps"></a>Passaggi successivi

Ora che la macchina virtuale è stata protetta, informazioni su come gestirla e ripristinarla.

- [Gestire e monitorare le macchine virtuali](backup-azure-manage-vms.md)
- [Ripristinare la macchina virtuale](backup-azure-arm-restore-vms.md)

Se si verificano problemi, [vedere](backup-azure-vms-troubleshoot.md) la guida alla risoluzione dei problemi.
