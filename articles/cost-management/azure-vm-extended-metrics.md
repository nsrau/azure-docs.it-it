---
title: Aggiungere metriche estese per le macchine virtuali di Azure | Microsoft Docs
description: Questo articolo aiuta ad abilitare e configurare metriche di diagnostica estese per le macchine virtuali di Azure.
services: cost-management
keywords: 
author: bandersmsft
ms.author: banders
ms.date: 01/30/2018
ms.topic: article
ms.service: cost-management
manager: carmonm
ms.custom: 
ms.openlocfilehash: 91797aaab1dca96e78643f57776eb16d336e894b
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/01/2018
---
# <a name="add-extended-metrics-for-azure-virtual-machines"></a>Aggiungere metriche estese per le macchine virtuali di Azure

Gestione costi usa dati delle metriche di Azure dalle macchine virtuali di Azure per mostrare informazioni dettagliate sulle risorse. I dati delle metriche, chiamati anche contatori delle prestazioni, vengono usati da Gestione costi per generare report. Tuttavia, Gestione costi non raccoglie automaticamente tutti i dati delle metriche di Azure dalle macchine virtuali guest. A questo scopo, è necessario abilitare la raccolta delle metriche. Questo articolo descrive come abilitare e configurare metriche di diagnostica aggiuntive per le macchine virtuali di Azure.

Dopo aver abilitato la raccolta delle metriche, è possibile:

- Identificare quando le macchine virtuali raggiungono i limiti di memoria, dei dischi e di CPU.
- Rilevare tendenze e anomalie di utilizzo.
- Controllare i costi in base all'utilizzo.
- Ottenere consigli efficaci in termini di costi per l'ottimizzazione delle dimensioni da Gestione costi.

Ad esempio, potrebbe essere necessario monitorare le percentuali di CPU e memoria delle macchine virtuali di Azure. Le metriche delle macchine virtuali di Azure corrispondono a _Percentuale CPU [host]_ e _Percentuale memoria [guest]_.

## <a name="verify-that-metrics-are-enabled-on-vms"></a>Verificare che le metriche siano abilitate nelle macchine virtuali

1. Accedere al portale di Azure all'indirizzo http://portal.azure.com.
2. In **Macchine virtuali** selezionare una macchina virtuale e quindi fare clic su **Metriche** in **Monitoraggio**. Verrà visualizzato un elenco delle metriche disponibili.
3. Selezionare alcune metriche per visualizzare un grafico che mostra i dati corrispondenti.  
    ![Metrica di esempio - Percentuale di CPU per gli host](./media/azure-vm-extended-metrics/metric01.png)

Nell'esempio precedente è disponibile un set limitato di metriche standard per gli host, ma non sono disponibili le metriche della memoria. Le metriche della memoria fanno parte delle metriche estese. Per abilitare le metriche estese, è necessario eseguire alcuni passaggi aggiuntivi. Le informazioni seguenti descrivono come abilitarle.

## <a name="enable-extended-metrics-in-the-azure-portal"></a>Abilitare le metriche estese nel portale di Azure

Le metriche standard sono le metriche dei computer host. La metrica _Percentuale CPU [host]_ è un esempio di queste metriche. Sono disponibili anche le metriche di base per le macchine virtuali guest, chiamate metriche estese. _Percentuale memoria [guest]_ e _Memoria disponibile [guest]_ sono due esempi di metriche estese.

L'abilitazione delle metriche estese è un processo semplice. Per ogni macchina virtuale abilitare il monitoraggio a livello di guest. Quando si abilita il monitoraggio a livello di guest, nella macchina virtuale viene installato l'agente di diagnostica di Azure. Il processo seguente è lo stesso per macchine virtuali classiche e normali e macchine virtuali Windows e Linux.

### <a name="enable-guest-level-monitoring-on-existing-vms"></a>Abilitare il monitoraggio a livello di guest nelle macchine virtuali esistenti

1. In **Macchine virtuali** visualizzare l'elenco delle macchine virtuali e quindi selezionare una macchina virtuale.
2. In **Monitoraggio** selezionare **Metrica**.
3. Fare clic su **Impostazioni di diagnostica**.
4. Nella pagina Impostazioni di diagnostica fare clic su **Abilita monitoraggio a livello di guest**. Per le macchine virtuali Linux è necessario un account di archiviazione esistente. Se non si sceglie un account di archiviazione per una macchina virtuale Windows, ne viene creato uno automaticamente.  
    ![Abilita monitoraggio a livello di guest](./media/azure-vm-extended-metrics/enable-guest-monitoring.png)
5. Dopo pochi minuti, nella macchina virtuale viene installato l'agente di diagnostica di Azure. Aggiornare la pagina e l'elenco delle metriche disponibili verrà aggiornato con le metriche per i guest.  
    ![Metriche estese](./media/azure-vm-extended-metrics/extended-metrics.png)

### <a name="enable-guest-level-monitoring-on-new-vms"></a>Abilitare il monitoraggio a livello di guest nelle nuove macchine virtuali

Quando si creano nuove macchine virtuali, assicurarsi di selezionare **Diagnostica del sistema operativo guest**. Per le macchine virtuali Linux è necessario un account di archiviazione esistente. Se non si sceglie un account di archiviazione per una macchina virtuale Windows, ne viene creato uno automaticamente.

![Abilitare la diagnostica del sistema operativo guest](./media/azure-vm-extended-metrics/new-enable-diag.png)

## <a name="resource-manager-credentials"></a>Credenziali di Resource Manager

Dopo aver abilitato le metriche estese, assicurarsi che Gestione costi abbia accesso alle [credenziali di Resource Manager](activate-subs-accounts.md). Le credenziali sono necessarie perché Gestione costi possa raccogliere e visualizzare i dati sulle prestazioni per le macchine virtuali. Le credenziali vengono usate anche per creare consigli di ottimizzazione dei costi. Gestione costi necessita di almeno tre giorni di dati sulle prestazioni da un'istanza per determinare se questa sia un buon candidato per consigliare un ridimensionamento.

## <a name="enable-vm-metrics-with-a-script"></a>Abilitare le metriche delle macchine virtuali con uno script

È possibile abilitare le metriche delle macchine virtuali tramite script di Azure PowerShell. Quando sono presenti molte macchine virtuali per cui si vuole abilitare le metriche, è possibile usare uno script per automatizzare il processo. Gli script di esempio sono disponibili in GitHub nella pagina [azure-enable-diagnostics](https://github.com/Cloudyn/azure-enable-diagnostics).

## <a name="view-azure-performance-metrics"></a>Visualizzare le metriche delle prestazioni di Azure

Per visualizzare le metriche delle prestazioni nelle istanze di Azure nel portale Cloudyn, passare a **Assets** (Asset)  > **Compute** (Calcolo)  > **Instance Explorer** (Esplora istanze). Nell'elenco delle istanze di macchina virtuale espandere un'istanza e quindi espandere una risorsa per visualizzarne i dettagli.

![Instance Explorer (Esplora istanze)](./media/azure-vm-extended-metrics/instance-explorer.png)

## <a name="next-steps"></a>Passaggi successivi

- Se non è già stato abilitato l'accesso all'API di Azure Resource Manager per gli account, vedere [Activate Azure subscriptions and accounts](activate-subs-accounts.md) (Attivare gli account e le sottoscrizioni di Azure)
