---
title: Nega accesso alla rete pubblica - Portale di Azure - Database di Azure per MySQLDeny Public Network Access - Azure portal - Azure Database for MySQL
description: Informazioni su come configurare Nega accesso di rete pubblica usando il portale di Azure per il database di Azure per MySQL
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: b5f93f3a3583900810ca75f925c6a88df9102652
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79374953"
---
# <a name="deny-public-network-access-in-azure-database-for-mysql-using-azure-portal"></a>Nega accesso alla rete pubblica nel database di Azure per MySQL tramite il portale di AzureDeny Public Network Access in Azure Database for MySQL using Azure portal

In questo articolo viene descritto come configurare un database di Azure per il server MySQL per negare tutte le configurazioni pubbliche e consentire solo le connessioni tramite endpoint privati per migliorare ulteriormente la sicurezza di rete.

## <a name="prerequisites"></a>Prerequisiti

Per completare questa guida, è necessario:

* Un [database di Azure per MySQLAn Azure Database for MySQL](quickstart-create-mysql-server-database-using-azure-portal.md)

## <a name="set-deny-public-network-access"></a>Impostare Nega accesso alla rete pubblica

Attenersi alla seguente procedura per impostare il server MySQL Nega accesso alla rete pubblica:

1. Nel [portale](https://portal.azure.com/)di Azure selezionare il database di Azure esistente per il server MySQL.In the Azure portal , select your existing Azure Database for MySQL server.

1. Nella pagina Server MySQL, in **Impostazioni**, fare clic su **Sicurezza connessione** per aprire la pagina di configurazione della sicurezza della connessione.

1. In **Nega accesso alla rete pubblica**selezionare **Sì** per abilitare Nega accesso pubblico per il server MySQL.

    ![Database di Azure per MySQL Negare l'accesso alla reteAzure Database for MySQL Deny network access](./media/howto-deny-public-network-access/setting-deny-public-network-access.PNG)

1. Fare clic su **Salva** per salvare le modifiche.

1. Una notifica confermerà che l'impostazione di sicurezza della connessione è stata abilitata correttamente.

    ![Database di Azure per MySQL Negare l'accesso alla rete riuscitoAzure Database for MySQL Deny network access success](./media/howto-deny-public-network-access/setting-deny-public-network-access-success.png)

## <a name="next-steps"></a>Passaggi successivi

Informazioni su [come creare avvisi sulle metriche.](howto-alert-on-metric.md)