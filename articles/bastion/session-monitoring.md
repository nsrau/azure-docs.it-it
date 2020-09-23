---
title: Monitoraggio e gestione delle sessioni di Azure Bastion | Microsoft Docs
description: In questo articolo viene illustrato come selezionare una sessione in corso e forzare la disconnessione o eliminarla.
services: bastion
author: charwen
ms.service: bastion
ms.topic: how-to
ms.date: 05/21/2020
ms.author: charwen
ms.openlocfilehash: beb4f8fc5b9e22bc3e2bdd4732c8321dfcd70780
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90980729"
---
# <a name="session-monitoring-and-management-for-azure-bastion"></a>Monitoraggio e gestione delle sessioni per Azure Bastion

Una volta eseguiti il provisioning e la distribuzione del servizio Bastion nella rete virtuale, è possibile usarlo per connettersi facilmente a qualsiasi macchina virtuale nella rete. Quando gli utenti si connettono ai carichi di lavoro, è possibile usare Azure Bastion per monitorare le sessioni remote e intraprendere azioni di gestione rapide. Il monitoraggio delle sessioni di Azure Bastion consente di visualizzare quali utenti sono connessi alle singole macchine virtuali. Il servizio mostra l'indirizzo IP da cui l'utente si è connesso, la durata della connessione e l'orario. L'esperienza di gestione delle sessioni consente di selezionare una sessione in corso ed eliminarla oppure forzare la disconnessione dell'utente.

## <a name="monitor-remote-sessions"></a><a name="monitor"></a>Monitorare le sessioni remote

1. Nel [portale di Azure](https://portal.azure.com) passare alla risorsa di Azure Bastion e selezionare **Sessioni** nella pagina di Azure Bastion.

   ![Screenshot mostra le impostazioni del menu portale di Azure con le sessioni selezionate.](./media/session-monitoring/sessions.png)
2. Sul lato destro della pagina **Sessioni** sono visibili le sessioni remote in corso.

   ![visualizzazione sessione](./media/session-monitoring/view-session.png)
3. Selezionare **Aggiorna** per vedere l'elenco aggiornato delle sessioni remote. Quando si seleziona Aggiorna, Azure Bastion recupera le informazioni di monitoraggio più recenti e le aggiorna nel portale.

   ![Screenshot mostra una sessione che usa Azure Bastion con l'opzione di aggiornamento evidenziata.](./media/session-monitoring/refresh.png)


## <a name="delete-or-force-disconnect-an-ongoing-remote-session"></a><a name="view"></a>Eliminare o forzare la disconnessione di una sessione remota in corso

È possibile selezionare un set di sessioni e disconnetterle in modo forzato. I passaggi seguenti illustrano come eliminare sessioni remote:

1. Passare alla risorsa di Azure Bastion e selezionare **Sessioni** nella pagina Azure Bastion.

   ![Screenshot mostra la portale di Azure con le sessioni selezionate in impostazioni.](./media/session-monitoring/navigate.png)
2. Dopo aver selezionato Sessioni, compare un elenco delle sessioni remote.

   ![elenco sessioni](./media/session-monitoring/list.png)
3. Selezionare una specifica sessione remota, quindi selezionare i tre puntini di sospensione a destra della riga della sessione e selezionare **Elimina**.

   ![Screenshot mostra la portale di Azure la visualizzazione di una sessione con l'icona di eliminazione selezionata.](./media/session-monitoring/delete.png)
4. Quando si seleziona Elimina, la sessione remota verrà disconnessa e all'utente verrà mostrato un messaggio "Sei stato disconnesso" nella sessione remota.

   ![Screenshot mostra un messaggio che verifica che l'utente sia stato disconnesso.](./media/session-monitoring/disconnect.png)

## <a name="next-steps"></a>Passaggi successivi

Leggere le [domande frequenti su Bastion](bastion-faq.md).
