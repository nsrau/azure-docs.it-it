---
title: Aggiungere metriche estese per le macchine virtuali di Azure | Microsoft Docs
description: Questo articolo aiuta ad abilitare e configurare metriche di diagnostica estese per le macchine virtuali di Azure.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 09/27/2018
ms.topic: conceptual
ms.service: cost-management
manager: dougeby
ms.custom: ''
ms.openlocfilehash: bc3eb2721dd9fc0c4cde407a8257f6be73201a2a
ms.sourcegitcommit: 42405ab963df3101ee2a9b26e54240ffa689f140
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/28/2018
ms.locfileid: "47423358"
---
# <a name="add-extended-metrics-for-azure-virtual-machines"></a>Aggiungere metriche estese per le macchine virtuali di Azure

Cloudyn usa dati delle metriche di Azure dalle macchine virtuali di Azure per mostrare informazioni dettagliate sulle risorse. I dati delle metriche, chiamati anche contatori delle prestazioni, vengono usati da Cloudyn per generare report. Tuttavia, Cloudyn non raccoglie automaticamente tutti i dati delle metriche di Azure dalle macchine virtuali guest. A questo scopo, è necessario abilitare la raccolta delle metriche. Questo articolo descrive come abilitare e configurare metriche di diagnostica aggiuntive per le macchine virtuali di Azure.

Dopo aver abilitato la raccolta delle metriche, è possibile:

- Identificare quando le macchine virtuali raggiungono i limiti di memoria, dei dischi e di CPU.
- Rilevare tendenze e anomalie di utilizzo.
- Controllare i costi in base all'utilizzo.
- Ottenere consigli efficaci in termini di costi per l'ottimizzazione delle dimensioni da Cloudyn.

Ad esempio, potrebbe essere necessario monitorare le percentuali di CPU e memoria delle macchine virtuali di Azure. Le metriche delle macchine virtuali di Azure corrispondono a _Percentuale CPU [host]_ e _Percentuale memoria [guest]_.

> [!NOTE]
> La raccolta di dati relativi alle metriche estese è supportata solo con il monitoraggio a livello di guest di Azure. Cloudyn non è compatibile con l'estensione macchina virtuale di Log Analytics.

## <a name="determine-whether-extended-metrics-are-enabled"></a>Determinare se le metriche estese sono abilitate

1. Accedere al portale di Azure all'indirizzo http://portal.azure.com.
2. In **Macchine virtuali** selezionare una macchina virtuale e quindi fare clic su **Metriche** in **Monitoraggio**. Verrà visualizzato un elenco delle metriche disponibili.
3. Selezionare alcune metriche per visualizzare un grafico che mostra i dati corrispondenti.  
    ![Metrica di esempio - Percentuale di CPU per gli host](./media/azure-vm-extended-metrics/metric01.png)

Nell'esempio precedente è disponibile un set limitato di metriche standard per gli host, ma non sono disponibili le metriche della memoria. Le metriche della memoria fanno parte delle metriche estese. In questo caso le metriche estese non sono abilitate per la macchina virtuale. Per abilitare le metriche estese, è necessario eseguire alcuni passaggi aggiuntivi. Le informazioni seguenti descrivono come abilitarle.

## <a name="enable-extended-metrics-in-the-azure-portal"></a>Abilitare le metriche estese nel portale di Azure

Le metriche standard sono le metriche dei computer host. La metrica _Percentuale CPU [host]_ è un esempio di queste metriche. Sono disponibili anche le metriche di base per le macchine virtuali guest, chiamate metriche estese. _Percentuale memoria [guest]_ e _Memoria disponibile [guest]_ sono due esempi di metriche estese.

L'abilitazione delle metriche estese è un processo semplice. Per ogni macchina virtuale abilitare il monitoraggio a livello di guest. Quando si abilita il monitoraggio a livello di guest, nella macchina virtuale viene installato l'agente di diagnostica di Azure. Per impostazione predefinita, viene aggiunto un set di base di metriche estese. Il processo seguente è lo stesso per macchine virtuali classiche e normali e macchine virtuali Windows e Linux.

Tenere presente che il monitoraggio a livello di guest sia di Azure che di Linux richiede un account di archiviazione. Quando si abilita il monitoraggio a livello di guest, se non si sceglie un account di archiviazione esistente, ne viene creato uno automaticamente.

### <a name="enable-guest-level-monitoring-on-existing-vms"></a>Abilitare il monitoraggio a livello di guest nelle macchine virtuali esistenti

1. In **Macchine virtuali** visualizzare l'elenco delle macchine virtuali e quindi selezionare una macchina virtuale.
2. Selezionare **Impostazioni di diagnostica** in **Monitoraggio**.
3. Nella pagina Impostazioni di diagnostica fare clic su **Abilita monitoraggio a livello di guest**.  
    ![Abilita monitoraggio a livello di guest](./media/azure-vm-extended-metrics/enable-guest-monitoring.png)
4. Dopo pochi minuti, nella macchina virtuale viene installato l'agente di diagnostica di Azure. Viene aggiunto un set di base di metriche. Aggiornare la pagina. I contatori delle prestazioni aggiunti vengono visualizzati nella scheda Panoramica.
5. In Monitoraggio selezionare **Metrica**.
6. Nel grafico delle metriche selezionare **Guest (versione classica)** in **Metric Namespace** (Spazio dei nomi delle metriche).
7. Nell'elenco Metrica è possibile visualizzare tutti i contatori delle prestazioni disponibili per la macchina virtuale guest.  
    ![Metriche estese](./media/azure-vm-extended-metrics/extended-metrics.png)

### <a name="enable-guest-level-monitoring-on-new-vms"></a>Abilitare il monitoraggio a livello di guest nelle nuove macchine virtuali

Quando si creano nuove macchine virtuali, nella scheda Gestione selezionare **Sì** per **Diagnostica sistema operativo guest**.

![Abilitare la diagnostica del sistema operativo guest](./media/azure-vm-extended-metrics/new-enable-diag.png)

## <a name="resource-manager-credentials"></a>Credenziali di Resource Manager

Dopo aver abilitato le metriche estese, assicurarsi che Cloudyn abbia accesso alle [credenziali di Resource Manager](activate-subs-accounts.md). Le credenziali sono necessarie perché Cloudyn possa raccogliere e visualizzare i dati sulle prestazioni per le macchine virtuali. Le credenziali vengono usate anche per creare consigli di ottimizzazione dei costi. Cloudyn necessita di almeno tre giorni di dati sulle prestazioni da un'istanza per determinare se questa sia un buon candidato per consigliare un ridimensionamento.

## <a name="enable-vm-metrics-with-a-script"></a>Abilitare le metriche delle macchine virtuali con uno script

È possibile abilitare le metriche delle macchine virtuali tramite script di Azure PowerShell. Quando sono presenti molte macchine virtuali per cui si vuole abilitare le metriche, è possibile usare uno script per automatizzare il processo. Gli script di esempio sono disponibili in GitHub nella pagina [azure-enable-diagnostics](https://github.com/Cloudyn/azure-enable-diagnostics).

## <a name="view-azure-performance-metrics"></a>Visualizzare le metriche delle prestazioni di Azure

Per visualizzare le metriche delle prestazioni nelle istanze di Azure nel portale Cloudyn, passare a **Assets** (Asset)  > **Compute** (Calcolo)  > **Instance Explorer** (Esplora istanze). Nell'elenco delle istanze di macchina virtuale espandere un'istanza e quindi espandere una risorsa per visualizzarne i dettagli.

![Instance Explorer (Esplora istanze)](./media/azure-vm-extended-metrics/instance-explorer.png)

## <a name="next-steps"></a>Passaggi successivi

- Se non è già stato abilitato l'accesso all'API di Azure Resource Manager per gli account, vedere [Attivare sottoscrizioni e account di Azure](activate-subs-accounts.md).
