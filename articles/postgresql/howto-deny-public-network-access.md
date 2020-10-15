---
title: Negare l'accesso alla rete pubblica-portale di Azure-database di Azure per PostgreSQL-server singolo
description: Informazioni su come configurare l'accesso negato alla rete pubblica usando portale di Azure per il server singolo database di Azure per PostgreSQL
author: kummanish
ms.author: manishku
ms.service: postgresql
ms.topic: how-to
ms.date: 03/10/2020
ms.openlocfilehash: 1dfc8d473d0cfe663569d2508404bf190f2da841
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90901583"
---
# <a name="deny-public-network-access-in-azure-database-for-postgresql-single-server-using-azure-portal"></a>Negare l'accesso alla rete pubblica nel server singolo database di Azure per PostgreSQL usando portale di Azure

Questo articolo descrive come configurare un singolo server di database di Azure per PostgreSQL per negare tutte le configurazioni pubbliche e consentire solo le connessioni tramite endpoint privati per migliorare ulteriormente la sicurezza della rete.

## <a name="prerequisites"></a>Prerequisiti

Per completare questa guida, è necessario:

* Un [server singolo database di Azure per PostgreSQL](quickstart-create-server-database-portal.md)

## <a name="set-deny-public-network-access"></a>Impostare Nega accesso alla rete pubblica

Seguire questa procedura per impostare PostgreSQL Single Server Deny Public Network Access:

1. Nella [portale di Azure](https://portal.azure.com/)selezionare il server singolo database di Azure per PostgreSQL.

1. Nella pagina server singolo PostgreSQL, in **Impostazioni**, fare clic su **sicurezza connessione** per aprire la pagina Configurazione sicurezza connessione.

1. In **Nega accesso alla rete pubblica**selezionare **Sì** per abilitare Nega accesso pubblico per il server singolo PostgreSQL.

    :::image type="content" source="./media/howto-deny-public-network-access/deny-public-network-access.PNG" alt-text="Server singolo database di Azure per PostgreSQL Nega accesso alla rete":::

1. Fare clic su **Salva** per salvare le modifiche.

1. Una notifica conferma che l'impostazione di sicurezza della connessione è stata abilitata correttamente.

    :::image type="content" source="./media/howto-deny-public-network-access/deny-public-network-access-success.png" alt-text="Server singolo database di Azure per PostgreSQL Nega accesso alla rete":::

## <a name="next-steps"></a>Passaggi successivi

Informazioni su [come creare avvisi per le metriche](howto-alert-on-metric.md).
