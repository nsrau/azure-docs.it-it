---
title: Abilitazione delle funzionalità dell'area di lavoro sinapsi
description: Questo documento descrive come un utente può abilitare le funzionalità dell'area di lavoro sinapsi in un pool SQL dedicato esistente (in precedenza SQL DW).
services: synapse-analytics
author: antvgski
manager: igorstan
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 11/25/2020
ms.author: anvang
ms.reviewer: jrasnick
ms.openlocfilehash: b105c4c9ee0bd27af11fe09a0fd756467e30bb35
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96467717"
---
# <a name="enabling-synapse-workspace-features-for-a-dedicated-sql-pool-formerly-sql-dw"></a>Abilitazione delle funzionalità dell'area di lavoro sinapsi per un pool SQL dedicato (in precedenza SQL DW)

Tutti gli utenti di SQL data warehouse possono ora accedere e usare un'istanza del pool SQL dedicato esistente (in precedenza SQL DW) tramite sinapsi studio e l'area di lavoro. Gli utenti possono usare sinapsi studio e l'area di lavoro senza effetti sull'automazione, le connessioni o gli strumenti. Questo articolo illustra come un utente di Azure sinapsi Analytics esistente può abilitare le funzionalità dell'area di lavoro sinapsi per un pool SQL dedicato esistente (in precedenza SQL DW). L'utente può espandere la soluzione di analisi esistente sfruttando le nuove funzionalità ricche di funzionalità ora disponibili tramite l'area di lavoro sinapsi e studio.   

## <a name="prerequisites"></a>Prerequisiti
Prima di abilitare le funzionalità dell'area di lavoro sinapsi nella data warehouse, è necessario assicurarsi di disporre di quanto segue:
- Diritti per creare e gestire le risorse SQL ospitate nel server logico SQL.
- Diritti per la creazione di risorse sinapsi.
- Un amministratore Azure Active Directory identificato nel server logico

## <a name="sign-in-to-the-azure-portal"></a>Accedere al portale di Azure

Accedere al [portale di Azure](https://portal.azure.com/).

## <a name="enabling-synapse-workspace-features-for-an-existing-dedicated-sql-pool-formerly-sql-dw"></a>Abilitazione delle funzionalità dell'area di lavoro sinapsi per un pool SQL dedicato esistente (in precedenza SQL DW)

Le funzionalità dell'area di lavoro sinapsi possono essere abilitate in qualsiasi pool SQL dedicato esistente (in precedenza SQL DW) in un'area supportata. Questa funzionalità è disponibile solo tramite il portale di Azure.

Seguire questa procedura per creare un'area di lavoro sinapsi per la data warehouse esistente.
1. Selezionare un pool SQL dedicato esistente (in precedenza SQL DW) e aprire la pagina panoramica.
2. Selezionare **nuova area di lavoro sinapsi** nella barra dei menu superiore o il messaggio immediatamente sotto.
3. Dopo aver esaminato l'elenco dei data warehouse resi disponibili tramite la nuova area di lavoro sinapsi nella pagina **Crea nuova area di lavoro di Azure sinapsi** . Selezionare **continua per continuare** .
4. Nella pagina Nozioni di base il pool SQL dedicato esistente (la sezione Dettagli **progetto** deve essere prepopolato con la stessa **sottoscrizione** e il **gruppo di risorse** distribuito nel server logico. In **Dettagli area di lavoro**, il nome dell' **area di lavoro** viene prepopolato con lo stesso nome e la stessa area del server logico SQL per assicurarsi che la connessione tra la nuova area di lavoro sinapsi e il server logico possa essere creata durante il processo di provisioning. Post-provisioning questa connessione deve essere mantenuta per garantire l'accesso continuo alle istanze del pool SQL dedicato (in precedenza SQL DW) tramite l'area di lavoro e studio.
5. Passare a Selezionare Data Lake Storage Gen 2.
6. Selezionare **Crea nuovo** o selezionare un **Data Lake storage Gen2** esistente prima di selezionare **Avanti: rete**.
7. Scegliere una **password di amministratore SQL** per l' **istanza senza server**. La modifica della password non comporta l'aggiornamento o la modifica delle credenziali SQL del server logico. Se si preferisce una password definita dal sistema, lasciare vuoti questi campi. Questa password può essere modificata in qualsiasi momento all'interno della nuova area di lavoro. Il nome dell'amministratore deve essere lo stesso usato nella SQL Server.
8. Selezionare le **impostazioni di rete** preferite e selezionare **Verifica + crea** per avviare il provisioning dell'area di lavoro.
9. Selezionare **goto Resource** per aprire la nuova area di lavoro.

    > [!NOTE]
    > Tutte le istanze del pool SQL dedicato (in precedenza SQL DW) ospitate nel server logico sono disponibili tramite la nuova area di lavoro.

## <a name="post-provisioning-steps"></a>Passaggi di post-provisioning
È necessario completare i passaggi seguenti per assicurarsi che le istanze del pool SQL dedicato (in precedenza SQL DW) esistenti siano accessibili tramite sinapsi Studio.
1. Nella pagina Panoramica dell'area di lavoro sinapsi selezionare **server connesso**. Il **server connesso** porta al server logico SQL connesso che ospita i data warehouse. Scegliere **server connesso** dal menu essenziale.
2. Aprire i **firewall e le reti virtuali** e assicurarsi che l'indirizzo IP del client o un intervallo di indirizzi IP predeterminato abbia accesso al server logico.
3. Aprire **Active Directory amministratore** e verificare che nel server logico sia stato impostato un amministratore di AAD.
4. Selezionare una delle istanze del pool SQL dedicato (in precedenza SQL DW) ospitate nel server logico. Nella pagina Overview (panoramica) selezionare **Launch sinapsi Studio (avvia sinapsi Studio** ) o passare a l' [accesso a sinapsi Studio](https://web.azuresynapse.net) e accedere all'area di lavoro.

5. Aprire l' **hub dati** ed espandere il pool SQL dedicato in Esplora oggetti per assicurarsi di avere accesso ed eseguire query sul data warehouse.

## <a name="next-steps"></a>Passaggi successivi
Introduzione all' [area di lavoro sinapsi e a Studio](../get-started.md).