---
title: Monitoraggio e gestione delle sessioni di Azure Bastion | Microsoft Docs
description: In questo articolo viene illustrato come selezionare una sessione in corso e forzare la disconnessione o eliminarla.
services: bastion
author: charwen
ms.service: bastion
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: charwen
ms.openlocfilehash: 6bf80be4868295145fa79ae29d5322181b6131d1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80619184"
---
# <a name="session-monitoring-and-management-for-azure-bastion"></a>Monitoraggio e gestione delle sessioni per Azure Bastion

Una volta eseguito il provisioning e la distribuzione del servizio Bastion nella rete virtuale, è possibile usarlo per connettersi facilmente a qualsiasi macchina virtuale in questa rete virtuale. Poiché gli utenti si connettono ai carichi di lavoro, è possibile usare Azure Bastion per monitorare le sessioni remote e intraprendere azioni di gestione rapide. Azure Bastion Session Monitoring consente di visualizzare gli utenti connessi alle macchine virtuali. Viene visualizzato l'indirizzo IP da cui l'utente si è connesso, la durata della connessione e la connessione. L'esperienza di gestione delle sessioni consente di selezionare una sessione in corso e di forzare la disconnessione o l'eliminazione di una sessione per disconnettere l'utente dalla sessione in corso.

## <a name="monitor-remote-sessions"></a><a name="monitor"></a>Monitorare le sessioni remote

1. Nella [portale di Azure](https://portal.azure.com)passare alla risorsa di Azure Bastion e selezionare **sessioni** dalla pagina del Bastion di Azure.

   ![sessioni](./media/session-monitoring/sessions.png)
2. Nella pagina **sessioni** è possibile visualizzare le sessioni remote in corso sul lato destro.

   ![Visualizza sessione](./media/session-monitoring/view-session.png)
3. Selezionare **Aggiorna** per visualizzare l'elenco aggiornato delle sessioni remote. Quando si seleziona Aggiorna, Azure Bastion recupera le informazioni di monitoraggio più recenti e le aggiorna nel portale.

   ![refresh](./media/session-monitoring/refresh.png)

>[!IMPORTANT]
> Abilitare la porta 4443 per il traffico in ingresso da Gestione gateway per il corretto funzionamento del monitoraggio della sessione.
>

## <a name="delete-or-force-disconnect-an-ongoing-remote-session"></a><a name="view"></a>Eliminare o forzare la disconnessione di una sessione remota in corso

È possibile selezionare un set di sessioni e disconnetterle in modo forzato. Nei passaggi seguenti viene illustrato come eliminare sessioni remote:

1. Passare alla risorsa di Azure Bastion e selezionare **sessioni** dalla pagina del Bastion di Azure.

   ![navigate](./media/session-monitoring/navigate.png)
2. Dopo aver selezionato le sessioni, viene visualizzato un elenco di sessioni remote.

   ![elencare le sessioni](./media/session-monitoring/list.png)
3. Selezionare una sessione remota specifica, quindi selezionare i tre puntini di sospensione nella parte destra della riga della sessione e quindi selezionare **Elimina**.

   ![Elimina](./media/session-monitoring/delete.png)
4. Quando si seleziona Elimina, la sessione remota verrà disconnessa e all'utente verrà visualizzato un messaggio "disconnesso" nella sessione remota.

   ![disconnessione](./media/session-monitoring/disconnect.png)

## <a name="next-steps"></a>Passaggi successivi

Leggere le [domande frequenti su Bastion](bastion-faq.md).