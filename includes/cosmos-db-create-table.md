---
title: File di inclusione
description: File di inclusione
services: cosmos-db
author: SnehaGunda
ms.service: cosmos-db
ms.topic: include
ms.date: 04/13/2018
ms.author: sngun
ms.custom: include file
ms.openlocfilehash: 5743d785afb87aef6b3a89af6dc8eb18f66b164d
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/05/2020
ms.locfileid: "68854677"
---
È ora possibile usare lo strumento Esplora dati nel portale di Azure per creare un database e una tabella. 

1. Selezionare su **Esplora dati** > **Nuova tabella**. 
    
    Viene visualizzata l'area **Add Table** (Aggiungi tabella) all'estrema destra. Per vederla potrebbe essere necessario scorrere la schermata.

    ![Esplora dati nel portale di Azure](./media/cosmos-db-create-table/azure-cosmosdb-data-explorer.png)

2. Nella pagina **Add Table** (Aggiungi tabella) immettere le impostazioni per la nuova tabella.

    Impostazione|Valore consigliato|Descrizione
    ---|---|---
    ID tabella|sample-table|ID della nuova tabella. I nomi delle tabelle presentano gli stessi requisiti relativi ai caratteri degli ID di database. I nomi dei database devono avere una lunghezza compresa tra 1 e 255 caratteri e non possono contenere `/ \ # ?` o spazi finali.
    Velocità effettiva|400 UR/s|Modificare la velocità effettiva in 400 unità richiesta al secondo (UR/sec). Se si vuole ridurre la latenza, è possibile aumentare la velocità effettiva in un secondo momento.

3. Selezionare **OK**.

4. In Esplora dati verranno visualizzati il nuovo database e la nuova tabella.

   ![Esplora dati nel portale di Azure, con il nuovo database e la nuova raccolta](./media/cosmos-db-create-table/azure-cosmos-db-new-table.png)