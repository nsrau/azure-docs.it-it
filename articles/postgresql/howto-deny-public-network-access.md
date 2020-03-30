---
title: Nega accesso alla rete pubblica - Portale di Azure - Database di Azure per PostgreSQL - Server singolo
description: Informazioni su come configurare Nega accesso di rete pubblica usando il portale di Azure per il database di Azure per PostgreSQL Singolo server
author: kummanish
ms.author: manishku
ms.service: postgresql
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: 4dff2321414721dbd415b468e59aea0ab4b3acee
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79375122"
---
# <a name="deny-public-network-access-in-azure-database-for-postgresql-single-server-using-azure-portal"></a>Nega accesso alla rete pubblica nel database di Azure per PostgreSQL Singolo server tramite il portale di AzureDeny Public Network Access in Azure Database for PostgreSQL Single server using Azure portal

In questo articolo viene descritto come configurare un database di Azure per PostgreSQL Singolo server per negare tutte le configurazioni pubbliche e consentire solo le connessioni tramite endpoint privati per migliorare ulteriormente la sicurezza di rete.

## <a name="prerequisites"></a>Prerequisiti

Per completare questa guida, è necessario:

* Un [database di Azure per PostgreSQL singolo server](quickstart-create-PostgreSQL Single server-server-database-using-azure-portal.md)

## <a name="set-deny-public-network-access"></a>Impostare Nega accesso alla rete pubblica

Attenersi alla seguente procedura per impostare PostgreSQL Server singolo Nega accesso alla rete pubblica:

1. Nel [portale](https://portal.azure.com/)di Azure selezionare il database di Azure esistente per PostgreSQL Singolo server.

1. Nella pagina PostgreSQL Server singolo, in **Impostazioni**, fare clic su **Sicurezza connessione** per aprire la pagina di configurazione della sicurezza della connessione.

1. In **Nega accesso alla rete pubblica**selezionare **Sì** per abilitare Nega accesso pubblico per il server PostgreSQL Singolo.

    ![Database di Azure per PostgreSQL Singolo server Nega accesso alla reteAzure Database for PostgreSQL Single server Deny network access](./media/howto-deny-public-network-access/deny-public-network-access.PNG)

1. Fare clic su **Salva** per salvare le modifiche.

1. Una notifica confermerà che l'impostazione di sicurezza della connessione è stata abilitata correttamente.

    ![Database di Azure per PostgreSQL Singolo server Nega accesso alla rete riuscito](./media/howto-deny-public-network-access/deny-public-network-access-success.png)

## <a name="next-steps"></a>Passaggi successivi

Informazioni su [come creare avvisi sulle metriche.](howto-alert-on-metric.md)