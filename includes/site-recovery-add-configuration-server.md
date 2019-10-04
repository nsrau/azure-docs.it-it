---
title: File di inclusione
description: File di inclusione
services: site-recovery
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: include
ms.date: 02/28/2019
ms.author: mayg
ms.custom: include file
ms.openlocfilehash: 7c682105113dac7c1d457489cf926210ead77993
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2019
ms.locfileid: "67179861"
---
1. Eseguire il file di installazione per l'Installazione unificata.
2. In **Before you begin** (Prima di iniziare) selezionare **Install the configuration server and process server** (Installare il server di configurazione e il server di elaborazione).

    ![Prima di iniziare](./media/site-recovery-add-configuration-server/combined-wiz1.png)

3. In **Licenza software di terze parti** fare clic su **Accetto** per scaricare e installare MySQL.

    ![Software di terze parti](./media/site-recovery-add-configuration-server/combined-wiz2.png)
4. In **Registrazione** selezionare la chiave di registrazione scaricata dall'insieme di credenziali.

    ![Registrazione](./media/site-recovery-add-configuration-server/combined-wiz3.png)
5. In **Impostazioni Internet** specificare in che modo il provider in esecuzione nel server di configurazione si connette ad Azure Site Recovery tramite Internet. Accertarsi di aver autorizzato gli URL necessari.

    - Per connettersi al proxy attualmente configurato nel computer, selezionare **Connetti ad Azure Site Recovery usando un server proxy**.
    - Per fare in modo che il provider si connetta direttamente, selezionare **Connetti direttamente ad Azure Site Recovery senza server proxy**.
    - Se per il proxy esistente è necessaria l'autenticazione o si vuole usare un proxy personalizzato per la connessione del provider, selezionare **Connect with custom proxy settings** (Connetti con le impostazioni proxy personalizzate) e specificare indirizzo, porta e credenziali.
     ![Firewall](./media/site-recovery-add-configuration-server/combined-wiz4.png)
6. In **Controllo dei prerequisiti** il programma di installazione esegue un controllo per assicurarsi che sia possibile eseguire l'installazione. Se viene visualizzato un avviso relativo al **Global time sync check** (Controllo della sincronizzazione ora globale), verificare che l'ora del clock di sistema, nelle impostazioni di **Data e ora**, corrisponda al fuso orario.

    ![Prerequisiti](./media/site-recovery-add-configuration-server/combined-wiz5.png)
7. In **MySQL Configuration** (Configurazione MySQL) creare le credenziali per l'accesso all'istanza del server MySQL che viene installata.

    ![MySQL](./media/site-recovery-add-configuration-server/combined-wiz6.png)
8. In **Dettagli dell'ambiente**, selezionare No se si replicano server fisici o macchine virtuali di Azure Stack. 
9. In **Percorso di installazione** specificare il percorso di installazione dei file binari e di archiviazione della cache. L'unità selezionata deve avere almeno 5 GB di spazio su disco disponibile, ma è consigliabile usare un'unità cache con almeno 600 GB di spazio disponibile.

    ![Percorso di installazione](./media/site-recovery-add-configuration-server/combined-wiz8.png)
10. In **Selezione della rete** selezionare prima l'interfaccia di rete usata dal server di elaborazione predefinito per il rilevamento e l'installazione push del servizio Mobility nei computer di origine e quindi selezionare la scheda di interfaccia di rete usata dal server di configurazione per la connettività con Azure. La porta 9443 è la porta predefinita per l'invio e la ricezione del traffico di replica, ma è possibile modificare il numero di porta in base ai requisiti dell'ambiente. Oltre alla porta 9443, viene aperta anche la porta 443, che viene usata da un server Web per orchestrare le operazioni di replica. Non usare la porta 443 per inviare o ricevere traffico di replica.

    ![Selezione rete](./media/site-recovery-add-configuration-server/combined-wiz9.png)


11. Esaminare le informazioni nella pagina **Riepilogo** e fare clic su **Installa**. Al termine dell'installazione verrà generata una passphrase. Sarà necessaria quando si abilita la replica, è quindi consigliabile copiarla e conservarla in un luogo sicuro.

    ![Summary](./media/site-recovery-add-configuration-server/combined-wiz10.png)

Al termine della registrazione, il server viene visualizzato nel pannello **Impostazioni** > **Server** nell'insieme di credenziali.
