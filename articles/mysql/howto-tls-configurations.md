---
title: Configurazione TLS-portale di Azure-database di Azure per MySQL
description: Informazioni su come impostare la configurazione di TLS usando portale di Azure per il database di Azure per MySQL
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: 495fea21c4fca12cd1ec6c1ea45105178d72fe1b
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/14/2020
ms.locfileid: "79375291"
---
# <a name="configuring-tls-settings-in-azure-database-for-mysql-using-azure-portal"></a>Configurazione delle impostazioni TLS nel database di Azure per MySQL con portale di Azure

Questo articolo descrive come configurare un database di Azure per il server MySQL per applicare le connessioni per una versione minima di TLS e negare tutte le connessioni con una versione più bassa di TLS, migliorando in tal modo la sicurezza della rete.

I clienti hanno ora la possibilità di applicare la versione TLS per la connessione al database di Azure per MySQL. I clienti hanno ora la possibilità di impostare la versione minima di TLS per il server di database. Se ad esempio si imposta la versione minima di TLS su 1,0, sarà necessario consentire ai client di connettersi tramite TLS 1.0, 1.1 e 1,2. In alternativa, l'impostazione di questa opzione su 1,2 significa che è possibile consentire solo ai client che si connettono tramite TLS 1,2 e che tutte le connessioni in ingresso con TLS 1,0 e TLS 1,1 verranno rifiutate.

## <a name="prerequisites"></a>Prerequisites

Per completare questa guida, è necessario:

* Un [database di Azure per MySQL](quickstart-create-mysql-server-database-using-azure-portal.md)

## <a name="set-tls-configurations-for-azure-database-for-mysql"></a>Impostare le configurazioni TLS per database di Azure per MySQL

Per impostare la versione minima di TLS del server MySQL, seguire questa procedura:

1. Nella [portale di Azure](https://portal.azure.com/)selezionare il database di Azure per il server MySQL esistente.

1. Nella pagina server MySQL, in **Impostazioni**, fare clic su **sicurezza connessione** per aprire la pagina Configurazione sicurezza connessione.

1. Nella **versione minima di TLS**selezionare **1,2** per negare le connessioni con la versione tls inferiore a TLS 1,2 per il server MySQL.

    ![Configurazione TLS per database di Azure per MySQL](./media/howto-tls-configurations/setting-tls-value.png)

1. È consigliabile fare clic su **Salva** per salvare le modifiche.

1. Una notifica conferma che l'impostazione di sicurezza della connessione è stata abilitata correttamente.

    ![Configurazione di TLS per database di Azure per MySQL completata](./media/howto-tls-configurations/setting-tls-value-success.png)

## <a name="next-steps"></a>Passaggi successivi

Informazioni su [come creare avvisi per le metriche](howto-alert-on-metric.md).
