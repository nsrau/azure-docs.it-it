---
title: Spostare un insieme di credenziali di Servizi di ripristino tra sottoscrizioni di Azure o in un altro gruppo di risorse
description: Istruzioni per spostare l'insieme di credenziali di Servizi di ripristino tra gruppi di risorse e sottoscrizioni di Azure.
services: backup
author: sogup
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: sogup
ms.openlocfilehash: f4ab983fbebe9c0219e70fa7bd5742cf1c3a0491
ms.sourcegitcommit: 43b85f28abcacf30c59ae64725eecaa3b7eb561a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/09/2019
ms.locfileid: "59361969"
---
# <a name="move-a-recovery-services-vault-across-azure-subscriptions-and-resource-groups-limited-public-preview"></a>Spostare un insieme di credenziali di Servizi di ripristino tra sottoscrizioni di Azure e gruppi di risorse (anteprima pubblica limitata)

Questo articolo illustra come spostare un insieme di credenziali di Servizi di ripristino configurato per Backup di Azure tra sottoscrizioni di Azure o in un altro gruppo di risorse nella stessa sottoscrizione. È possibile usare il portale di Azure o PowerShell per spostare un insieme di credenziali di Servizi di ripristino.

> [!NOTE]
> Per spostare un insieme di credenziali di servizi di ripristino e le risorse associate al gruppo di risorse diverso, è necessario innanzitutto [registrare la sottoscrizione di origine](#register-the-source-subscription-to-move-your-recovery-services-vault).

## <a name="supported-geos"></a>Aree geografiche supportate

Lo spostamento di risorse per insieme di credenziali di servizi di ripristino è supportato in Australia orientale, Australia sud orientale, Canada centrale, Canada orientale, Asia sud-orientale, Asia orientale, Stati Uniti centrali, Settentrionali, Stati Uniti orientali, Stati Uniti orientali 2, South central US, West Central US, centro-occidentali US2, Stati Uniti occidentali, India centrale, India meridionale, Giappone orientale, Giappone occidentale, Corea centrale, Corea meridionale, Europa settentrionale, Europa occidentale, Sudafrica settentrionale, Sudafrica occidentale, Regno Unito meridionale, Regno Unito occidentale, Emirati Arabi Uniti centrali ed Emirati Arabi Uniti settentrionali.

## <a name="prerequisites-for-moving-a-vault"></a>Prerequisiti per lo spostamento di un insieme di credenziali

- Durante lo spostamento tra gruppi di risorse, sia il gruppo di risorse di origine che il gruppo di risorse di destinazione sono bloccati durante l'operazione. Fino al completamento dello spostamento, le operazioni di scrittura ed eliminazione sono bloccate nei gruppi di risorse.
- Solo l'amministratore della sottoscrizione ha le autorizzazioni per spostare un insieme di credenziali.
- Quando si sposta un insieme di credenziali tra sottoscrizioni, la sottoscrizione di destinazione deve esistere in uno stato abilitato e deve essere nello stesso tenant della sottoscrizione di origine.
- È necessario avere l'autorizzazione per eseguire operazioni di scrittura sul gruppo di risorse di destinazione.
- Non è possibile modificare il percorso dell'insieme di credenziali di Servizi di ripristino. Lo spostamento dell'insieme di credenziali modifica solo il gruppo di risorse. Il nuovo gruppo di risorse può essere in un percorso diverso, ma ciò non modifica il percorso dell'insieme di credenziali.
- È attualmente possibile spostare un solo insieme di credenziali di Servizi di ripristino per ogni area contemporaneamente.
- Se una macchina virtuale non viene spostata con l'insieme di credenziali di Servizi di ripristino tra le sottoscrizioni o in un nuovo gruppo di risorse, i punti di recupero correnti della macchina virtuale rimangono invariati nell'insieme di credenziali finché non scadono.
- Sia che macchina virtuale venga spostata nell'insieme di credenziali o meno, è sempre possibile ripristinare la macchina virtuale dalla cronologia di backup conservata nell'insieme di credenziali.
- Crittografia dischi di Azure richiede che l'insieme di credenziali delle chiavi e le macchine virtuali si trovino nella stessa area e sottoscrizione di Azure.
- Per spostare una macchina virtuale con dischi gestiti, vedere questo [articolo](https://azure.microsoft.com/blog/move-managed-disks-and-vms-now-available/).
- Le opzioni per lo spostamento delle risorse distribuite con il modello classico variano a seconda che lo spostamento avvenga all'interno di una sottoscrizione o in una nuova sottoscrizione. Per altre informazioni, vedere questo [articolo](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources#classic-deployment-limitations).
- I criteri di backup definiti per l'insieme di credenziali vengono conservati dopo che l'insieme di credenziali viene spostato tra le sottoscrizioni o in un nuovo gruppo di risorse.
- Non è attualmente è possibile spostare insiemi di credenziali contenenti File di Azure, Sincronizzazione file di Azure o SQL in macchine virtuali IaaS tra sottoscrizioni e gruppi di risorse.
- Se si sposta un insieme di credenziali contenente i dati di backup di macchine virtuali tra sottoscrizioni diverse, è necessario spostare le macchine virtuali nella stessa sottoscrizione e usare lo stesso gruppo di risorse di destinazione per continuare l'esecuzione dei backup.<br>

> [!NOTE]
>
> Gli insiemi di credenziali di Servizi di ripristino configurati per l'uso con **Azure Site Recovery** non possono ancora essere spostati. Se sono state configurate macchine virtuali (Azure IaaS, Hyper-V, VMware) o computer fisici per il ripristino di emergenza con **Azure Site Recovery**, l'operazione di spostamento verrà bloccata. La funzionalità di spostamento di risorse per il servizio Site Recovery non è ancora disponibile.

## <a name="register-the-source-subscription-to-move-your-recovery-services-vault"></a>Registrare la sottoscrizione di origine per spostare l'insieme di credenziali di Servizi di ripristino

Per registrare la sottoscrizione di origine per **spostare** l'insieme di credenziali di Servizi di ripristino, eseguire i cmdlet seguenti dal terminale di PowerShell:

1. Accedere con l'account Azure

   ```
   Connect-AzureRmAccount
   ```

2. Selezionare la sottoscrizione da registrare

   ```
   Get-AzureRmSubscription –SubscriptionName "Subscription Name" | Select-AzureRmSubscription
   ```
3. Registrare questa sottoscrizione

   ```
   Register-AzureRmProviderFeature -ProviderNamespace Microsoft.RecoveryServices -FeatureName RecoveryServicesResourceMove
   ```

4. Eseguire il comando

   ```
   Register-AzureRmResourceProvider -ProviderNamespace Microsoft.RecoveryServices
   ```

Prima di iniziare con l'operazione di spostamento tramite il portale di Azure o PowerShell, attendere che la sottoscrizione venga inserita nell'elenco elementi consentiti (30 minuti).

## <a name="use-azure-portal-to-move-a-recovery-services-vault-to-different-resource-group"></a>Usare il portale di Azure per spostare un insieme di credenziali di Servizi di ripristino in un gruppo di risorse diverso

Per spostare un insieme di credenziali di Servizi di ripristino e le risorse associate in un gruppo di risorse diverso

1. Accedere al [portale di Azure](https://portal.azure.com/).
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


## <a name="use-azure-portal-to-move-a-recovery-services-vault-to-a-different-subscription"></a>Usare il portale di Azure per spostare un insieme di credenziali di Servizi di ripristino in una sottoscrizione diversa

È possibile spostare un insieme di credenziali di Servizi di ripristino e le risorse associate in una sottoscrizione diversa

1. Accedere al [portale di Azure](https://portal.azure.com/).
2. Aprire l'elenco Insiemi di credenziali dei servizi di ripristino e selezionare l'insieme di credenziali che si vuole spostare. Quando il dashboard dell'insieme di credenziali viene aperto, viene visualizzato come illustrato nell'immagine seguente.

    ![Insieme di credenziali di Servizi di ripristino aperto](./media/backup-azure-move-recovery-services/open-recover-service-vault.png)

    Se le **Informazioni di base** per l'insieme di credenziali non sono visualizzate, fare clic sulla freccia verso il basso. Le informazioni di base dovrebbero ora essere visualizzate.

    ![Scheda delle informazioni di base](./media/backup-azure-move-recovery-services/essentials-information-tab.png)

3. Nel menu Panoramica dell'insieme di credenziali fare clic su **cambia** accanto a **Sottoscrizione**, per aprire il pannello **Sposta risorse**.

   ![Cambiare la sottoscrizione](./media/backup-azure-move-recovery-services/change-resource-subscription.png)

4. Selezionare le risorse da spostare. In questo caso, è consigliabile usare l'opzione **Seleziona tutto** per selezionare tutte le risorse facoltative elencate.

   ![Spostare la risorsa](./media/backup-azure-move-recovery-services/move-resource-source-subscription.png)

5. Dall'elenco a discesa **Sottoscrizione**  selezionare la sottoscrizione di destinazione in cui si vuole spostare l'insieme di credenziali.
6. Per aggiungere il gruppo di risorse di destinazione, nell'elenco a discesa **Gruppo di risorse** selezionare un gruppo di risorse esistente o fare clic sull'opzione **Crea un nuovo gruppo**.

   ![Aggiungere la sottoscrizione](./media/backup-azure-move-recovery-services/add-subscription.png)

7. Fare clic sull'opzione **Dichiaro di aver compreso che gli strumenti e gli script associati alle risorse spostate non funzioneranno fino a quando non li aggiornerò con i nuovi ID di risorsa** per confermare e quindi fare clic su **OK**.

> [!NOTE]
> Il backup tra sottoscrizioni (l'insieme di credenziali di Servizi di ripristino e le macchine virtuali protette si trovano in sottoscrizioni diverse) non è uno scenario supportato. Neppure l'opzione di ridondanza di archiviazione può essere modificata da archiviazione con ridondanza locale ad archiviazione con ridondanza globale e viceversa durante l'operazione di spostamento dell'insieme di credenziali.
>
>

## <a name="use-powershell-to-move-a-vault"></a>Usare PowerShell per spostare un insieme di credenziali

Per spostare un insieme di credenziali di Servizi di ripristino in un altro gruppo di risorse, usare il cmdlet `Move-AzureRMResource`. `Move-AzureRMResource` richiede il nome della risorsa e il tipo di risorsa. È possibile ottenerli entrambi dal cmdlet `Get-AzureRmRecoveryServicesVault`.

```
$destinationRG = "<destinationResourceGroupName>"
$vault = Get-AzureRmRecoveryServicesVault -Name <vaultname> -ResourceGroupName <vaultRGname>
Move-AzureRmResource -DestinationResourceGroupName $destinationRG -ResourceId $vault.ID
```

Per spostare le risorse in una diversa sottoscrizione, includere il parametro `-DestinationSubscriptionId`.

```
Move-AzureRmResource -DestinationSubscriptionId "<destinationSubscriptionID>" -DestinationResourceGroupName $destinationRG -ResourceId $vault.ID
```

Dopo avere eseguito i cmdlet precedenti, verrà richiesto di confermare che si vuole spostare le risorse specificate. Digitare **Y** per confermare. Dopo la convalida, la risorsa viene spostata.

## <a name="use-cli-to-move-a-vault"></a>Usare l'interfaccia della riga di comando spostare un insieme di credenziali

Per spostare un insieme di credenziali di Servizi di ripristino in un altro gruppo di risorse, usare il cmdlet seguente:

```
az resource move --destination-group <destinationResourceGroupName> --ids <VaultResourceID>
```

Per spostare in una nuova sottoscrizione, inserire il parametro `--destination-subscription-id`.

## <a name="post-migration"></a>Dopo la migrazione

1. È necessario impostare/verificare i controlli di accesso per i gruppi di risorse.  
2. La funzionalità di creazione di report e monitoraggio di Backup deve essere configurata nuovamente per l'insieme di credenziali dopo il completamento dello spostamento. La configurazione precedente andrà persa durante l'operazione di spostamento.



## <a name="next-steps"></a>Passaggi successivi

È possibile spostare molti tipi diversi di risorse tra gruppi di risorse e sottoscrizioni.

Per altre informazioni, vedere [Spostare le risorse in un gruppo di risorse o una sottoscrizione nuovi](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources).
