---
title: Nega accesso alla rete pubblica - Portale di Azure - Database di Azure per MariaDB
description: Informazioni su come configurare Nega accesso di rete pubblica usando il portale di Azure per il database di Azure per MariaDB
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
# <a name="deny-public-network-access-in-azure-database-for-mariadb-using-azure-portal"></a>Nega accesso alla rete pubblica nel database di Azure per MariaDB tramite il portale di AzureDeny Public Network Access in Azure Database for MariaDB using Azure portal

In questo articolo viene descritto come configurare un database di Azure per il server MariaDB per negare tutte le configurazioni pubbliche e consentire solo le connessioni tramite endpoint privati per migliorare ulteriormente la sicurezza di rete.

## <a name="prerequisites"></a>Prerequisiti

Per completare questa guida, è necessario:

* Un [database di Azure per MariaDBAn Azure Database for MariaDB](quickstart-create-MariaDB-server-database-using-azure-portal.md)

## <a name="set-deny-public-network-access"></a>Impostare Nega accesso alla rete pubblica

Attenersi alla seguente procedura per impostare il server MariaDB Nega accesso alla rete pubblica:

1. Nel [portale](https://portal.azure.com/)di Azure selezionare il database di Azure esistente per il server MariaDB.In the Azure portal , select your existing Azure Database for MariaDB server.

1. Nella pagina Server MariaDB, in **Impostazioni**, fare clic su **Protezione connessione** per aprire la pagina di configurazione della protezione della connessione.

1. In Nega accesso alla rete pubblica selezionare **Sì** per abilitare nega accesso pubblico per il server MariaDB.

    ![Database di Azure per l'accesso alla rete MariaDB DenyAzure Database for MariaDB Deny network access](./media/howto-deny-public-network-access/deny-public-network-access.PNG)

1. Fare clic su **Salva** per salvare le modifiche.

1. Una notifica confermerà che l'impostazione di sicurezza della connessione è stata abilitata correttamente.

    ![Database di Azure per l'accesso alla rete negatale MariaDB riuscitoAzure Database for MariaDB Deny network access success](./media/howto-deny-public-network-access/deny-public-network-access-success.png)

## <a name="next-steps"></a>Passaggi successivi

Informazioni su [come creare avvisi sulle metriche.](howto-alert-metric.md)