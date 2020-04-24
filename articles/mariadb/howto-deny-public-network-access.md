---
title: Negare l'accesso alla rete pubblica-portale di Azure-database di Azure per MariaDB
description: Informazioni su come configurare l'accesso negato alla rete pubblica usando portale di Azure per il database di Azure per MariaDB
author: kummanish
ms.author: manishku
ms.service: mariadb
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: 87b6033e486e9009573436628d0183c8a022aced
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79375239"
---
# <a name="deny-public-network-access-in-azure-database-for-mariadb-using-azure-portal"></a>Negare l'accesso alla rete pubblica nel database di Azure per MariaDB usando portale di Azure

Questo articolo descrive come configurare un database di Azure per il server MariaDB per negare tutte le configurazioni pubbliche e consentire solo le connessioni tramite endpoint privati per migliorare ulteriormente la sicurezza della rete.

## <a name="prerequisites"></a>Prerequisiti

Per completare questa guida, è necessario:

* Un [database di Azure per MariaDB](quickstart-create-MariaDB-server-database-using-azure-portal.md)

## <a name="set-deny-public-network-access"></a>Impostare Nega accesso alla rete pubblica

Attenersi alla procedura seguente per impostare il server MariaDB per negare l'accesso alla rete pubblica:

1. Nella [portale di Azure](https://portal.azure.com/)selezionare il database di Azure per il server MariaDB esistente.

1. Nella pagina Server MariaDB, in **Impostazioni**, fare clic su **sicurezza connessione** per aprire la pagina Configurazione sicurezza connessione.

1. In Nega accesso alla rete pubblica selezionare **Sì** per abilitare Nega accesso pubblico per il server MariaDB.

    ![Accesso alla rete negato per database di Azure per MariaDB](./media/howto-deny-public-network-access/deny-public-network-access.PNG)

1. Fare clic su **Salva** per salvare le modifiche.

1. Una notifica conferma che l'impostazione di sicurezza della connessione è stata abilitata correttamente.

    ![Il database di Azure per MariaDB nega l'accesso alla rete riuscito](./media/howto-deny-public-network-access/deny-public-network-access-success.png)

## <a name="next-steps"></a>Passaggi successivi

Informazioni su [come creare avvisi per le metriche](howto-alert-metric.md).