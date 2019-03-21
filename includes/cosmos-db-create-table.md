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
ms.openlocfilehash: a91c42ca32fb356b418dcd412c0690b01ff85789
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57908137"
---
È ora possibile usare lo strumento Esplora dati nel portale di Azure per creare un database e una tabella. 

1. Fare clic su **Esplora dati** > **New Table** (Nuova tabella). 
    
    Viene visualizzata l'area **Add Table** (Aggiungi tabella) all'estrema destra. Per vederla potrebbe essere necessario scorrere la schermata.

    ![Esplora dati nel portale di Azure](./media/cosmos-db-create-table/azure-cosmosdb-data-explorer.png)

2. Nella pagina **Add Table** (Aggiungi tabella) immettere le impostazioni per la nuova tabella.

    Impostazione|Valore consigliato|DESCRIZIONE
    ---|---|---
    ID tabella|sample-table|ID della nuova tabella. I nomi delle tabelle presentano gli stessi requisiti relativi ai caratteri degli ID di database. I nomi dei database devono avere una lunghezza compresa tra 1 e 255 caratteri e non possono contenere `/ \ # ?` o spazi finali.
    Velocità effettiva|400 UR/s|Modificare la velocità effettiva in 400 unità richiesta al secondo (UR/sec). Se si vuole ridurre la latenza, è possibile aumentare la velocità effettiva in un secondo momento.

3. Fare clic su **OK**.

4. In Esplora dati verranno visualizzati il nuovo database e la nuova tabella.

   ![Esplora dati nel portale di Azure, con il nuovo database e la nuova raccolta](./media/cosmos-db-create-table/azure-cosmos-db-new-table.png)