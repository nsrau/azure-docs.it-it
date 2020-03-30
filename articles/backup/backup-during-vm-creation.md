---
title: Abilitare il backup quando si crea una macchina virtuale di Azure
description: Descrive come abilitare il backup quando si crea una macchina virtuale di Azure con Backup di Azure.Describes how to enable backup when you create an Azure VM with Azure Backup.
ms.topic: conceptual
ms.date: 06/13/2019
ms.openlocfilehash: 0cfea6579791c4fd23c1b7acdfe722d57b5ec2fd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79247852"
---
# <a name="enable-backup-when-you-create-an-azure-vm"></a>Abilitare il backup quando si crea una macchina virtuale di Azure

Usare il servizio Backup di Azure per eseguire il backup delle macchine virtuali di Azure.Use the Azure Backup service to back up Azure virtual machines (VMs). Il backup delle macchine virtuali viene eseguito in base a una pianificazione specificata in un criterio di backup e i punti di ripristino vengono creati dai backup. I punti di ripristino vengono archiviati negli archivi servizi di ripristino.

Questo articolo descrive in dettaglio come abilitare il backup quando si crea una macchina virtuale (VM) nel portale di Azure.This article details how to enable backup when you create a virtual machine (VM) in the Azure portal.  

## <a name="before-you-start"></a>Prima di iniziare

- [Controllare](backup-support-matrix-iaas.md#supported-backup-actions) quali sistemi operativi sono supportati se si abilita il backup quando si crea una macchina virtuale.

## <a name="sign-in-to-azure"></a>Accedere ad Azure

Se non è già stato effettuato l'accesso all'account, accedere al portale di [Azure.](https://portal.azure.com)

## <a name="create-a-vm-with-backup-configured"></a>Creare una macchina virtuale con Backup configuratoCreate a VM with Backup configured

1. Nel portale di Azure fare clic su **Crea una risorsa**.

2. In Azure Marketplace fare clic su **Calcola**e quindi selezionare un'immagine di macchina virtuale.

3. Configurare la macchina virtuale in conformità con le istruzioni di Windows o [Linux.Set](https://docs.microsoft.com/azure/virtual-machines/linux/quick-create-portal) up the VM in accordance with the [Windows](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal) or Linux instructions.

4. Nella scheda **Gestione,** in **Abilita backup,** fare clic su **Attivato**.
5. Backup di Azure in un insieme di credenziali di Servizi di ripristino. Fare clic su **Crea nuovo** se non si dispone di un vault esistente.
6. Accettare il nome del vault suggerito o specificarne uno personalizzato.
7. Specificare o creare un gruppo di risorse in cui si troverà l'insieme di credenziali. L'insieme di credenziali delle risorse può essere diverso dal gruppo di risorse della macchina virtuale.

    ![Abilitare il backup per una macchina virtualeEnable backup for a VM](./media/backup-during-vm-creation/enable-backup.png)

8. Accettare il criterio di backup predefinito o modificare le impostazioni.
    - Un criterio di backup specifica la frequenza con cui eseguire gli snapshot di backup della macchina virtuale e per quanto tempo conservare le copie di backup.
    - Il criterio predefinito esegue il backup della macchina virtuale una volta al giorno.
    - È possibile personalizzare i propri criteri di backup per una macchina virtuale di Azure in modo che esedii i backup vengano eseguiti giornalmente o settimanalmente.
    - [Altre informazioni](backup-azure-vms-introduction.md#backup-and-restore-considerations) sulle considerazioni di backup per le macchine virtuali di Azure.Learn more about backup considerations for Azure VMs.
    - [Ulteriori informazioni](backup-instant-restore-capability.md) sulla funzionalità di ripristino immediato.

      ![Criterio di backup predefinito](./media/backup-during-vm-creation/daily-policy.png)

## <a name="azure-backup-resource-group-for-virtual-machines"></a>Azure Backup resource group for Virtual Machines

Il servizio di backup crea un gruppo di risorse separato (RG), diverso dal gruppo di risorse della macchina virtuale per archiviare la raccolta di punti di ripristino (RPC). RPC ospita i punti di ripristino istantanei delle macchine virtuali gestite. Il formato di denominazione predefinito del gruppo `AzureBackupRG_<Geo>_<number>`di risorse creato dal servizio Backup è: . Ad esempio: *AzureBackupRG_northeurope_1*. È ora possibile personalizzare il nome del gruppo di risorse creato da Backup di Azure.You can now customize the Resource group name created by Azure Backup.

Punti da notare:

1. È possibile utilizzare il nome predefinito del file RG o modificarlo in base ai requisiti aziendali.
2. Specificare il modello di nome RG come input durante la creazione dei criteri di backup della macchina virtuale. Il nome RG deve essere `<alpha-numeric string>* n <alpha-numeric string>`nel seguente formato: . 'n' viene sostituito con un numero intero (a partire da 1) e viene utilizzato per la scalabilità orizzontale se il primo RG è pieno. Un RG può avere un massimo di 600 RPC oggi.
              ![Scegliere il nome durante la creazione dei criteriChoose name when creating policy](./media/backup-during-vm-creation/create-policy.png)
3. Il modello deve seguire le regole di denominazione RG riportate di seguito e la lunghezza totale non deve superare la lunghezza massima consentita del nome RG.
    1. I nomi dei gruppi di risorse consentono solo caratteri alfanumerici, punti, caratteri di sottolineatura, trattini e parentesi. Non possono terminare in un periodo.
    2. I nomi dei gruppi di risorse possono contenere fino a 74 caratteri, inclusi il nome del file RG e il suffisso.
4. Il `<alpha-numeric-string>` primo è obbligatorio, mentre il secondo dopo 'n' è facoltativo. Questo vale solo se si assegna un nome personalizzato. Se non si immette alcun elemento in nessuna delle caselle di testo, viene utilizzato il nome predefinito.
5. È possibile modificare il nome del file RG modificando il criterio se e quando necessario. Se il modello di nome viene modificato, verranno creati nuovi RP nel nuovo RG. Tuttavia, i vecchi RP risiederanno ancora nel vecchio RG e non verranno spostati, poiché RP Collection non supporta lo spostamento delle risorse. Alla fine i RP verranno raccolti spazzatura come i punti scadono.
![Modifica del nome durante la modifica dei criteri](./media/backup-during-vm-creation/modify-policy.png)
6. Si consiglia di non bloccare il gruppo di risorse creato per l'utilizzo da parte del servizio di backup.

## <a name="start-a-backup-after-creating-the-vm"></a>Avviare un backup dopo la creazione della macchina virtualeStart a backup after creating the VM

Il backup della macchina virtuale verrà eseguito in base ai criteri di backup. Tuttavia, si consiglia di eseguire un backup iniziale.

Dopo aver creato la macchina virtuale, eseguire le operazioni seguenti:After the VM is created, do the following:

1. Nelle proprietà della macchina virtuale fare clic su **Backup**. Lo stato della macchina virtuale è Backup iniziale in sospeso fino all'esecuzione del backup iniziale
2. Fare clic su **Esegui backup ora** per eseguire un backup su richiesta.

    ![Eseguire un backup su richiesta](./media/backup-during-vm-creation/run-backup.png)

## <a name="use-a-resource-manager-template-to-deploy-a-protected-vm"></a>Usare un modello di Resource Manager per distribuire una macchina virtuale protettaUse a Resource Manager template to deploy a protected VM

I passaggi precedenti illustrano come usare il portale di Azure per creare una macchina virtuale e proteggerla in un insieme di credenziali di Servizi di ripristino. Per distribuire rapidamente una o più macchine virtuali e proteggerle in un insieme di credenziali di Servizi di ripristino, vedere il modello [Distribuire una macchina virtuale Windows e abilitare il backup.](https://azure.microsoft.com/resources/templates/101-recovery-services-create-vm-and-configure-backup/)

## <a name="next-steps"></a>Passaggi successivi

Dopo aver protetto la macchina virtuale, vedere come gestirla e ripristinarla.

- [Gestire e monitorare le VM](backup-azure-manage-vms.md)
- [Ripristinare la macchina virtuale](backup-azure-arm-restore-vms.md)

In caso di problemi, [consultare](backup-azure-vms-troubleshoot.md) la guida alla risoluzione dei problemi.
