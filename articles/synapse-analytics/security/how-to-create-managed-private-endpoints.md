---
title: Creare un endpoint privato gestito per connettersi ai risultati dell'origine dati
description: In questo articolo viene illustrato come creare un endpoint privato gestito per le origini dati da un'area di lavoro di Azure sinapsi.
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: security
ms.date: 04/15/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: fae958542f1bc391f285104d80d1e19131470abe
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87065473"
---
# <a name="create-a-managed-private-endpoint-to-your-data-source-preview"></a>Creare un endpoint privato gestito per l'origine dati (anteprima)

In questo articolo viene illustrato come creare un endpoint privato gestito per l'origine dati in Azure. Per altre informazioni, vedere [endpoint privati gestiti](./synapse-workspace-managed-private-endpoints.md) .

## <a name="step-1-open-your-azure-synapse-workspace-in-azure-portal"></a>Passaggio 1: aprire l'area di lavoro della sinapsi di Azure in portale di Azure

È possibile creare un endpoint privato gestito per l'origine dati da Azure sinapsi Studio. Selezionare la scheda **Panoramica** in portale di Azure e selezionare **Avvia sinapsi Studio**.
![Avviare Azure sinapsi Studio](./media/how-to-create-managed-private-endpoints/managed-private-endpoint-1.png)

## <a name="step-2-navigate-to-the-managed-virtual-networks-tab-in-synapse-studio"></a>Passaggio 2: passare alla scheda reti virtuali gestite in sinapsi Studio

In Azure sinapsi Studio selezionare la scheda **Gestisci** dal pannello di navigazione sinistro. Selezionare **reti virtuali gestite** e quindi selezionare **+ nuovo**.
![Creare un nuovo endpoint privato gestito](./media/how-to-create-managed-private-endpoints/managed-private-endpoint-2.png)

## <a name="step-3-select-the-data-source-type"></a>Passaggio 3: selezionare il tipo di origine dati

Consente di selezionare il tipo di origine dati. In questo caso, l'origine dati di destinazione è un account ADLS Gen2. Selezionare **Continua**.
![Selezionare un tipo di origine dati di destinazione](./media/how-to-create-managed-private-endpoints/managed-private-endpoint-3.png)

## <a name="step-4-enter-information-about-the-data-source"></a>Passaggio 4: immettere le informazioni sull'origine dati

Nella finestra successiva immettere le informazioni sull'origine dati. In questo esempio viene creato un endpoint privato gestito per un account ADLS Gen2. Immettere un **nome** per l'endpoint privato gestito. Specificare una **sottoscrizione di Azure** e un **nome dell'account di archiviazione**. Selezionare **Crea**.
![Immettere i dettagli dell'origine dati di destinazione](./media/how-to-create-managed-private-endpoints/managed-private-endpoint-4.png)

## <a name="step-5-verify-that-your-managed-private-endpoint-was-successfully-created"></a>Passaggio 5: verificare che l'endpoint privato gestito sia stato creato correttamente

Dopo aver inviato la richiesta, lo stato verrà visualizzato. Per verificare che sia stata creata correttamente la creazione dell'endpoint privato gestito, controllare lo *stato di provisioning*. Potrebbe essere necessario attendere 1 minuto e selezionare **Aggiorna** per aggiornare lo stato di provisioning. È possibile osservare che l'endpoint privato gestito per l'account ADLS Gen2 è stato creato correttamente.

È anche possibile vedere che lo *stato di approvazione* è *in sospeso*. Il proprietario della risorsa di destinazione può approvare o negare la richiesta di connessione all'endpoint privato. Se il proprietario approva la richiesta di connessione all'endpoint privato, viene stabilito un collegamento privato. Se negato, non viene stabilito alcun collegamento privato.
![Stato della richiesta di creazione dell'endpoint privato gestito](./media/how-to-create-managed-private-endpoints/managed-private-endpoint-5.png)

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sugli [endpoint privati gestiti](./synapse-workspace-managed-private-endpoints.md)