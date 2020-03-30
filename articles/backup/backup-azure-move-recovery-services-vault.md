---
title: Come spostare gli insiemi di credenziali di Azure Backup Recovery Services
description: Istruzioni su come spostare l'insieme di credenziali dei servizi di ripristino tra sottoscrizioni e gruppi di risorse di Azure.Instructions on how to move recovery services vault across Azure subscriptions and resource groups.
ms.reviewer: sogup
ms.topic: conceptual
ms.date: 04/08/2019
ms.openlocfilehash: fed42c578da2e4f27f42e11d5ac67d698bbcd939
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77120721"
---
# <a name="move-a-recovery-services-vault-across-azure-subscriptions-and-resource-groups"></a>Spostare un insieme di credenziali di Servizi di ripristino tra sottoscrizioni e gruppi di risorse di AzureMove a Recovery Services vault across Azure Subscriptions and Resource Groups

Questo articolo illustra come spostare un insieme di credenziali di Servizi di ripristino configurato per Backup di Azure tra sottoscrizioni di Azure o in un altro gruppo di risorse nella stessa sottoscrizione. È possibile usare il portale di Azure o PowerShell per spostare un insieme di credenziali di Servizi di ripristino.

## <a name="supported-regions"></a>Aree supportate

Lo spostamento delle risorse per il vault dei servizi di recupero è supportato in Australia Orientale, Australia Sud Est, Canada Centrale, Canada Est, Asia Sud-Est, Asia orientale, Stati Uniti centrali, Stati Uniti centro-settentrionali, Stati Uniti orientali, Stati Uniti orientali2, Stati Uniti centro-meridionali, Stati Uniti centro-occidentali, Stati Uniti centrali2, Stati Uniti occidentali, India centrale, India meridionale, Giappone orientale, Giappone occidentale, Corea centrale, Corea del Sud, Europa del Nord, Europa occidentale, Sud Africa settentrionale, Sud Africa occidentale, Regno Unito Sud e Regno Unito ovest.

## <a name="unsupported-regions"></a>Aree non supportate

Francia Centrale, Francia Meridionale, Germania Nordest, Germania Centrale, US Gov Iowa, Cina Nord, Cina Nord2, Cina Est, Cina Orientale2

## <a name="prerequisites-for-moving-recovery-services-vault"></a>Prerequisiti per lo spostamento dell'insieme di credenziali dei servizi di ripristinoPrerequisites for moving Recovery Services vault

- Durante lo spostamento dell'insieme di credenziali tra gruppi di risorse, i gruppi di risorse di origine e di destinazione vengono bloccati impedendo le operazioni di scrittura ed eliminazione. Per ulteriori informazioni, vedere questo [articolo](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources).
- Solo la sottoscrizione amministratore dispone delle autorizzazioni per spostare un insieme di credenziali.
- Per lo spostamento dell'insieme di credenziali tra sottoscrizioni, la sottoscrizione di destinazione deve risiedere nello stesso tenant della sottoscrizione di origine e il relativo stato deve essere abilitato.
- È necessario avere l'autorizzazione per eseguire operazioni di scrittura sul gruppo di risorse di destinazione.
- Lo spostamento dell'insieme di credenziali modifica solo il gruppo di risorse. L'insieme di credenziali dei servizi di ripristino risiederà nella stessa posizione e non potrà essere modificato.
- È possibile spostare un solo insieme di credenziali di Servizi di ripristino, per area, alla volta.
- Se una macchina virtuale non si sposta con l'insieme di credenziali di Servizi di ripristino tra sottoscrizioni o in un nuovo gruppo di risorse, i punti di ripristino della macchina virtuale corrente rimarranno intatti nell'insieme di credenziali fino alla scadenza.
- Sia che macchina virtuale venga spostata nell'insieme di credenziali o meno, è sempre possibile ripristinare la macchina virtuale dalla cronologia di backup conservata nell'insieme di credenziali.
- Crittografia disco di Azure richiede che l'insieme di credenziali delle chiavi e le macchine virtuali risiedano nella stessa area e sottoscrizione di Azure.The Azure Disk Encryption requires that the key vault and VMs reside in the same Azure region and subscription.
- Per spostare una macchina virtuale con dischi gestiti, vedere questo [articolo](https://azure.microsoft.com/blog/move-managed-disks-and-vms-now-available/).
- Le opzioni per lo spostamento delle risorse distribuite con il modello classico variano a seconda che lo spostamento avvenga all'interno di una sottoscrizione o in una nuova sottoscrizione. Per ulteriori informazioni, vedere questo [articolo](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources).
- I criteri di backup definiti per l'insieme di credenziali vengono conservati dopo che l'insieme di credenziali viene spostato tra le sottoscrizioni o in un nuovo gruppo di risorse.
- Lo spostamento dell'insieme di credenziali con File di Azure, Sincronizzazione file di Azure o SQL nelle macchine virtuali IaaS tra sottoscrizioni e gruppi di risorse non è supportato.
- Se si sposta un insieme di credenziali contenente i dati di backup della macchina virtuale tra sottoscrizioni, è necessario spostare le macchine virtuali nella stessa sottoscrizione e usare lo stesso nome del gruppo di risorse VM di destinazione (come nella sottoscrizione precedente) per continuare i backup.

> [!NOTE]
>
> Gli insiemi di credenziali di Servizi di ripristino configurati per l'uso con **Azure Site Recovery** non possono ancora essere spostati. Se sono state configurate macchine virtuali (Azure IaaS, Hyper-V, VMware) o computer fisici per il ripristino di emergenza con **Azure Site Recovery**, l'operazione di spostamento verrà bloccata. La funzionalità di spostamento di risorse per il servizio Site Recovery non è ancora disponibile.

## <a name="use-azure-portal-to-move-recovery-services-vault-to-different-resource-group"></a>Usare il portale di Azure per spostare l'insieme di credenziali di Servizi di ripristino in un gruppo di risorse diversoUse Azure portal to move Recovery Services vault to different resource group

Per spostare un insieme di credenziali di Servizi di ripristino e le risorse associate in un gruppo di risorse diverso

1. Accedere al [portale](https://portal.azure.com/)di Azure .
2. Aprire l'elenco **Insiemi di credenziali dei servizi di ripristino** e selezionare l'insieme di credenziali che si vuole spostare. Quando il dashboard dell'insieme di credenziali viene aperto, viene visualizzato come illustrato nell'immagine seguente.

   ![Insieme di credenziali di Servizi di ripristino aperto](./media/backup-azure-move-recovery-services/open-recover-service-vault.png)

   Se le **Informazioni di base** per l'insieme di credenziali non sono visualizzate, fare clic sulla freccia verso il basso. Le informazioni di base dovrebbero ora essere visualizzate.

   ![Scheda delle informazioni di base](./media/backup-azure-move-recovery-services/essentials-information-tab.png)

3. Nel menu Panoramica dell'insieme di credenziali fare clic su **cambia** accanto a **Gruppo di risorse**, per aprire il pannello **Sposta risorse**.

   ![Cambiare il gruppo di risorse](./media/backup-azure-move-recovery-services/change-resource-group.png)

4. Nel pannello **Sposta risorse** per l'insieme di credenziali selezionato è consigliabile spostare le risorse correlate facoltative selezionando la casella di controllo, come illustrato nell'immagine seguente.

   ![Spostare la sottoscrizione](./media/backup-azure-move-recovery-services/move-resource.png)

5. Per aggiungere il gruppo di risorse di destinazione, nell'elenco a discesa **Gruppo di risorse** selezionare un gruppo di risorse esistente o fare clic sull'opzione **Crea un nuovo gruppo**.

   ![Creare la risorsa](./media/backup-azure-move-recovery-services/create-a-new-resource.png)

6. Dopo aver aggiunto il gruppo di risorse, confermare l'opzione **Dichiaro di aver compreso che gli strumenti e gli script associati alle risorse spostate non funzioneranno fino a quando non li aggiornerò con i nuovi ID di risorsa** e quindi fare clic su **OK** per completare lo spostamento dell'insieme di credenziali.

   ![Messaggio di conferma](./media/backup-azure-move-recovery-services/confirmation-message.png)

## <a name="use-azure-portal-to-move-recovery-services-vault-to-a-different-subscription"></a>Usare il portale di Azure per spostare l'insieme di credenziali di Servizi di ripristino in una sottoscrizione diversaUse Azure portal to move Recovery Services vault to a different subscription

È possibile spostare un insieme di credenziali di Servizi di ripristino e le risorse associate in una sottoscrizione diversa

1. Accedere al [portale](https://portal.azure.com/)di Azure .
2. Aprire l'elenco Insiemi di credenziali dei servizi di ripristino e selezionare l'insieme di credenziali che si vuole spostare. Quando il dashboard dell'insieme di credenziali viene aperto, viene visualizzato come illustrato nell'immagine seguente.

    ![Insieme di credenziali di Servizi di ripristino aperto](./media/backup-azure-move-recovery-services/open-recover-service-vault.png)

    Se le **Informazioni di base** per l'insieme di credenziali non sono visualizzate, fare clic sulla freccia verso il basso. Le informazioni di base dovrebbero ora essere visualizzate.

    ![Scheda delle informazioni di base](./media/backup-azure-move-recovery-services/essentials-information-tab.png)

3. Nel menu Panoramica dell'insieme di credenziali fare clic su **cambia** accanto a **Sottoscrizione**, per aprire il pannello **Sposta risorse**.

   ![Cambiare la sottoscrizione](./media/backup-azure-move-recovery-services/change-resource-subscription.png)

4. Selezionare le risorse da spostare. In questo caso, è consigliabile usare l'opzione **Seleziona tutto** per selezionare tutte le risorse facoltative elencate.

   ![Spostare la risorsa](./media/backup-azure-move-recovery-services/move-resource-source-subscription.png)

5. Dall'elenco a discesa **Sottoscrizione ** selezionare la sottoscrizione di destinazione in cui si vuole spostare l'insieme di credenziali.
6. Per aggiungere il gruppo di risorse di destinazione, nell'elenco a discesa **Gruppo di risorse** selezionare un gruppo di risorse esistente o fare clic sull'opzione **Crea un nuovo gruppo**.

   ![Aggiungere la sottoscrizione](./media/backup-azure-move-recovery-services/add-subscription.png)

7. Fare clic sull'opzione **Dichiaro di aver compreso che gli strumenti e gli script associati alle risorse spostate non funzioneranno fino a quando non li aggiornerò con i nuovi ID di risorsa** per confermare e quindi fare clic su **OK**.

> [!NOTE]
> Il backup tra sottoscrizioni (l'insieme di credenziali di Servizi di ripristino e le macchine virtuali protette si trovano in sottoscrizioni diverse) non è uno scenario supportato. Neppure l'opzione di ridondanza di archiviazione può essere modificata da archiviazione con ridondanza locale ad archiviazione con ridondanza globale e viceversa durante l'operazione di spostamento dell'insieme di credenziali.
>
>

## <a name="use-powershell-to-move-recovery-services-vault"></a>Usare PowerShell per spostare l'insieme di credenziali di Servizi di ripristinoUse PowerShell to move Recovery Services vault

Per spostare un insieme di credenziali di Servizi di ripristino in un altro gruppo di risorse, usare il cmdlet `Move-AzureRMResource`. `Move-AzureRMResource` richiede il nome e il tipo della risorsa. È possibile ottenerli entrambi dal cmdlet `Get-AzureRmRecoveryServicesVault`.

```powershell
$destinationRG = "<destinationResourceGroupName>"
$vault = Get-AzureRmRecoveryServicesVault -Name <vaultname> -ResourceGroupName <vaultRGname>
Move-AzureRmResource -DestinationResourceGroupName $destinationRG -ResourceId $vault.ID
```

Per spostare le risorse in una diversa sottoscrizione, includere il parametro `-DestinationSubscriptionId`.

```powershell
Move-AzureRmResource -DestinationSubscriptionId "<destinationSubscriptionID>" -DestinationResourceGroupName $destinationRG -ResourceId $vault.ID
```

Dopo avere eseguito i cmdlet precedenti, verrà richiesto di confermare che si vuole spostare le risorse specificate. Digitare **Y** per confermare. Dopo la convalida, la risorsa viene spostata.

## <a name="use-cli-to-move-recovery-services-vault"></a>Usare l'interfaccia della riga di comando per spostare l'insieme di credenziali di Servizi di ripristinoUse CLI to

Per spostare un insieme di credenziali di Servizi di ripristino in un altro gruppo di risorse, usare il cmdlet seguente:

```azurecli
az resource move --destination-group <destinationResourceGroupName> --ids <VaultResourceID>
```

Per spostare in una nuova sottoscrizione, inserire il parametro `--destination-subscription-id`.

## <a name="post-migration"></a>Dopo la migrazione

1. Impostare/verificare i controlli di accesso per i gruppi di risorse.  
2. La funzionalità di creazione di report e monitoraggio di Backup deve essere configurata nuovamente per l'insieme di credenziali dopo il completamento dello spostamento. La configurazione precedente andrà persa durante l'operazione di spostamento.

## <a name="next-steps"></a>Passaggi successivi

È possibile spostare molti tipi diversi di risorse tra gruppi di risorse e sottoscrizioni.

Per altre informazioni, vedere [Spostare le risorse in un gruppo di risorse o una sottoscrizione nuovi](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources).
