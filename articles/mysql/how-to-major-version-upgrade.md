---
title: Aggiornamento della versione principale-portale di Azure-database di Azure per MySQL-server singolo
description: Questo articolo descrive come è possibile aggiornare la versione principale per database di Azure per MySQL-server singolo con portale di Azure
author: ambhatna
ms.author: ambhatna
ms.service: mysql
ms.topic: how-to
ms.date: 11/16/2020
ms.openlocfilehash: 4dd4729589e429cb1b028b183fdfd144617d1d1b
ms.sourcegitcommit: 03c0a713f602e671b278f5a6101c54c75d87658d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/19/2020
ms.locfileid: "94920645"
---
# <a name="major-version-upgrade-in-azure-database-for-mysql-single-server-using-the-azure-portal"></a>Aggiornamento della versione principale nel server singolo database di Azure per MySQL con il portale di Azure

> [!IMPORTANT]
> L'aggiornamento della versione principale per il server singolo di database di Azure per MySQL è in anteprima pubblica.

Questo articolo descrive come aggiornare la versione principale di MySQL sul posto nel database di Azure per il singolo server MySQL.

Questa funzionalità consentirà ai clienti di eseguire aggiornamenti sul posto dei propri server MySQL 5,6 a MySQL 5,7 con un clic del pulsante senza lo spostamento dei dati o la necessità di modificare le stringhe di connessione dell'applicazione.

> [!Note]
> * L'aggiornamento della versione principale è disponibile solo per l'aggiornamento della versione principale da MySQL 5,6 a MySQL 5,7<br>
> * L'aggiornamento della versione principale non è ancora supportato nel server di replica.

## <a name="prerequisites"></a>Prerequisiti
Per completare questa guida, è necessario:
- Un [singolo server di database di Azure per MySQL](quickstart-create-mysql-server-database-using-azure-portal.md)

## <a name="perform-major-version-upgrade-from-mysql-56-to-mysql-57"></a>Eseguire l'aggiornamento della versione principale da MySQL 5,6 a MySQL 5,7

Seguire questa procedura per eseguire l'aggiornamento principale della versione per il database di Azure del server MySQL 5,6

> [!IMPORTANT]
> È consigliabile eseguire prima l'aggiornamento su una copia ripristinata del server anziché aggiornare direttamente la produzione. Vedere [come eseguire il ripristino temporizzato](howto-restore-server-portal.md#point-in-time-restore).

1. Nella [portale di Azure](https://portal.azure.com/)selezionare il server di database di Azure per MySQL 5,6 esistente.

2. Nella pagina **Panoramica** fare clic sul pulsante **Aggiorna** sulla barra degli strumenti.

3. Nella sezione **aggiornamento** selezionare **OK** per aggiornare il server di database di Azure per MySQL 5,6 al server 5,7.

    :::image type="content" source="./media/how-to-major-version-upgrade-portal/upgrade.png" alt-text="Database di Azure per MySQL-Panoramica-aggiornamento":::

4. Una notifica conferma che l'aggiornamento ha avuto esito positivo.

## <a name="next-steps"></a>Passaggi successivi

Informazioni sui [criteri di controllo delle versioni di database di Azure per MySQL](concepts-version-policy.md).