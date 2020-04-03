---
title: Monitoraggio e gestione della sessione di Bastion di Azure Documenti Microsoft
description: In questo articolo viene illustrato come selezionare una sessione in corso e forzare la disconnessione o eliminarla.
services: bastion
author: charwen
ms.service: bastion
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: charwen
ms.openlocfilehash: 6bf80be4868295145fa79ae29d5322181b6131d1
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/02/2020
ms.locfileid: "80619184"
---
# <a name="session-monitoring-and-management-for-azure-bastion"></a>Monitoraggio e gestione delle sessioni per Azure Bastion

Dopo aver eseguito il provisioning e la distribuzione del servizio Bastion nella rete virtuale, è possibile usarlo per connettersi senza problemi a qualsiasi macchina virtuale in questa rete virtuale. Quando gli utenti si connettono ai carichi di lavoro, È possibile usare Azure Bastion per monitorare le sessioni remote ed eseguire azioni di gestione rapida. Il monitoraggio della sessione di Bastione di Azure consente di visualizzare gli utenti connessi alle macchine virtuali. Mostra l'IP da cui l'utente si è connesso, per quanto tempo è stato connesso e quando si è connesso. L'esperienza di gestione delle sessioni consente di selezionare una sessione in corso e forzare la disconnessione o eliminare una sessione per disconnettere l'utente dalla sessione in corso.

## <a name="monitor-remote-sessions"></a><a name="monitor"></a>Monitorare le sessioni remote

1. Nel [portale di Azure](https://portal.azure.com)passare alla risorsa Bastione di Azure e selezionare Sessioni dalla pagina Bastion di Azure.In the Azure portal , navigate to your Azure Bastion resource and select **Sessions** from the Azure Bastion page.

   ![sessioni](./media/session-monitoring/sessions.png)
2. Nella pagina **Sessioni** è possibile visualizzare le sessioni remote in corso sul lato destro.

   ![visualizzare la sessione](./media/session-monitoring/view-session.png)
3. Selezionare **Aggiorna** per visualizzare l'elenco aggiornato delle sessioni remote. Quando si seleziona Aggiorna, Azure Bastion recupererà le informazioni di monitoraggio più recenti e le aggiornerà nel portale.

   ![refresh](./media/session-monitoring/refresh.png)

>[!IMPORTANT]
> Abilitare la porta 4443 per il traffico in ingresso da Gateway Manager affinché il monitoraggio della sessione funzioni.
>

## <a name="delete-or-force-disconnect-an-ongoing-remote-session"></a><a name="view"></a>Eliminare o forzare la disconnessione di una sessione remota in corso

È possibile selezionare una o più sessioni e forzarle a disconnetterle. La procedura seguente illustra come eliminare le sessioni remote:

1. Passare alla risorsa Bastione di Azure e selezionare Sessioni dalla pagina Bastion di Azure.Navigate to your Azure Bastion resource and select **Sessions** from the Azure Bastion page.

   ![navigate](./media/session-monitoring/navigate.png)
2. Dopo aver selezionato Sessioni, viene visualizzato un elenco di sessioni remote.

   ![elencare le sessioni](./media/session-monitoring/list.png)
3. Selezionare una sessione remota specifica, quindi selezionare i tre puntini di sospensione all'estremità destra della riga della sessione e infine selezionare **Elimina**.

   ![delete](./media/session-monitoring/delete.png)
4. Quando si seleziona Elimina, la sessione remota verrà disconnessa e all'utente verrà visualizzato il messaggio "Sei stato disconnesso" nella sessione remota.

   ![disconnessione](./media/session-monitoring/disconnect.png)

## <a name="next-steps"></a>Passaggi successivi

Leggi le domande frequenti sul [Bastione](bastion-faq.md).