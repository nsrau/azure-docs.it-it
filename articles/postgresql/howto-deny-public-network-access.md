---
title: Negare l'accesso alla rete pubblica-portale di Azure-database di Azure per PostgreSQL-server singolo
description: Informazioni su come configurare l'accesso negato alla rete pubblica usando portale di Azure per il server singolo database di Azure per PostgreSQL
author: kummanish
ms.author: manishku
ms.service: postgresql
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: 4dff2321414721dbd415b468e59aea0ab4b3acee
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "79375122"
---
# <a name="deny-public-network-access-in-azure-database-for-postgresql-single-server-using-azure-portal"></a>Negare l'accesso alla rete pubblica nel server singolo database di Azure per PostgreSQL usando portale di Azure

Questo articolo descrive come configurare un singolo server di database di Azure per PostgreSQL per negare tutte le configurazioni pubbliche e consentire solo le connessioni tramite endpoint privati per migliorare ulteriormente la sicurezza della rete.

## <a name="prerequisites"></a>Prerequisiti

Per completare questa guida, è necessario:

* Un [server singolo database di Azure per PostgreSQL](quickstart-create-PostgreSQL Single server-server-database-using-azure-portal.md)

## <a name="set-deny-public-network-access"></a>Impostare Nega accesso alla rete pubblica

Seguire questa procedura per impostare PostgreSQL Single Server Deny Public Network Access:

1. Nella [portale di Azure](https://portal.azure.com/)selezionare il server singolo database di Azure per PostgreSQL.

1. Nella pagina server singolo PostgreSQL, in **Impostazioni**, fare clic su **sicurezza connessione** per aprire la pagina Configurazione sicurezza connessione.

1. In **Nega accesso alla rete pubblica**selezionare **Sì** per abilitare Nega accesso pubblico per il server singolo PostgreSQL.

    ![Server singolo database di Azure per PostgreSQL Nega accesso alla rete](./media/howto-deny-public-network-access/deny-public-network-access.PNG)

1. È consigliabile fare clic su **Salva** per salvare le modifiche.

1. Una notifica conferma che l'impostazione di sicurezza della connessione è stata abilitata correttamente.

    ![Il server singolo database di Azure per PostgreSQL nega l'accesso alla rete riuscito](./media/howto-deny-public-network-access/deny-public-network-access-success.png)

## <a name="next-steps"></a>Passaggi successivi

Informazioni su [come creare avvisi per le metriche](howto-alert-on-metric.md).