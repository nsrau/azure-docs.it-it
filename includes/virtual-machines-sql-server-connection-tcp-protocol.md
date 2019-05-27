---
author: rothja
ms.service: virtual-machines-sql
ms.topic: include
ms.date: 10/26/2018
ms.author: jroth
ms.openlocfilehash: 8b919608dfc562d8db77619d5215a6828a53a4aa
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/23/2019
ms.locfileid: "66165494"
---
1. Durante la connessione alla macchina virtuale con Desktop remoto, cercare **Gestione configurazione**:

    ![Apertura di SQL Server Management Studio](./media/virtual-machines-sql-server-connection-tcp-protocol/sql-server-configuration-manager.png)

1. In Gestione configurazione SQL Server, nel riquadro console espandere **Configurazione di rete SQL Server**.

1. Nel riquadro console fare clic su **Protocolli per MSSQLSERVER** (nome predefinito dell'istanza). Nel riquadro dei dettagli fare clic con il pulsante destro del mouse su **TCP** e quindi scegliere **Abilita** se l'opzione non è già abilitata.

    ![Abilitazione del protocollo TCP](./media/virtual-machines-sql-server-connection-tcp-protocol/enable-tcp.png)

1. Nel riquadro della console fare clic su **Servizi di SQL Server**. Nel riquadro dei dettagli fare clic con il pulsante destro del mouse su **SQL Server (*nome istanza*)** (l'istanza predefinita è **SQL Server (MSSQLSERVER)**) e quindi scegliere **Riavvia** per arrestare e riavviare l'istanza di SQL Server.

    ![Riavvio del motore di database](./media/virtual-machines-sql-server-connection-tcp-protocol/restart-sql-server.png)

1. Chiudere Gestione configurazione SQL Server.

Per ulteriori informazioni su come abilitare i protocolli per il motore di database di SQL Server, vedere [Abilitare o disabilitare un protocollo di rete del server](https://msdn.microsoft.com/library/ms191294.aspx).
