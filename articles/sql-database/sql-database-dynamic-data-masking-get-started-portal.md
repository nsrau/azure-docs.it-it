---
title: 'Portale di Azure: Maschera dati dinamica del database SQL di Azure | Documentazione Microsoft'
description: Introduzione alla funzione Maschera dati dinamica del database SQL nel portale di Azure
services: sql-database
documentationcenter: 
author: ronitr
manager: jhubbard
editor: 
ms.assetid: "2"
ms.service: sql-database
ms.custom: security
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: Inactive
ms.date: 11/22/2016
ms.author: ronitr
ms.openlocfilehash: aae91ef276625b13119c449636c9accc61e0a302
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/31/2017
---
# <a name="get-started-with-sql-database-dynamic-data-masking-with-the-azure-portal"></a>Introduzione alla funzione Maschera dati dinamica del database SQL nel portale di Azure

Questo articolo illustra come implementare la funzione [Maschera dati dinamica](sql-database-dynamic-data-masking-get-started.md) con il portale di Azure. È anche possibile implementare tale funzione tramite [cmdlet del database SQL di Azure](https://msdn.microsoft.com/library/azure/mt574084.aspx) o l'[API REST](https://msdn.microsoft.com/library/dn505719.aspx).


## <a name="set-up-dynamic-data-masking-for-your-database-using-the-azure-portal"></a>Configurare il mascheramento dei dati dinamici per il database tramite il portale di Azure
1. Avviare il portale di Azure all'indirizzo [https://portal.azure.com](https://portal.azure.com).
2. Accedere alla pagina di configurazione del database che include i dati sensibili a cui si desidera applicare la maschera.
3. Fare clic sul riquadro **Maschera dati dinamica**. Verrà aperta la pagina di configurazione **Maschera dati dinamica**.
   
   * In alternativa, è possibile scorrere verso il basso fino alla sezione **Operazioni** e fare clic su **Mascheramento dei dati dinamici**.
     
     ![Riquadro di spostamento](./media/sql-database-dynamic-data-masking-get-started/4_ddm_settings_tile.png)<br/><br/>
4. Nella pagina di configurazione **Maschera dati dinamica** potrebbero essere visualizzate alcune colonne del database che il motore di raccomandazioni ha contrassegnato per l'applicazione della maschera. Per accettare i suggerimenti, è sufficiente fare clic su **Aggiungi maschera** per una o più colonne e verrà creata una maschera in base al tipo predefinito per questa colonna. È possibile modificare la funzione maschera facendo clic sulla regola di maschera e modificando il formato maschera del campo su un formato diverso a propria scelta. Assicurarsi di salvare le impostazioni facendo clic su **Salva** .
   
    ![Riquadro di spostamento](./media/sql-database-dynamic-data-masking-get-started/5_ddm_recommendations.png)<br/><br/>
5. Per aggiungere una maschera a una colonna del database, nella parte superiore della pagina di configurazione **Maschera dati dinamica** fare clic su **Aggiungi maschera** per aprire la pagina di configurazione **Aggiungi regola di maschera**.
   
    ![Riquadro di spostamento](./media/sql-database-dynamic-data-masking-get-started/6_ddm_add_mask.png)<br/><br/>
6. Selezionare lo **schema**, la **tabella** e la **colonna** per definire i campi designati a cui verrà applicata la maschera.
7. Scegliere un **Formato maschera del campo** dall'elenco di categorie maschera dei dati sensibili.
   
    ![Riquadro di spostamento](./media/sql-database-dynamic-data-masking-get-started/7_ddm_mask_field_format.png)<br/><br/>        
8. Fare clic su **Salva** nella pagina delle regole di maschera dei dati per aggiornare il set di regole di maschera nei criteri della maschera dati dinamica.
9. Digitare gli utenti SQL o le identità AAD da escludere dalla maschera e che hanno accesso ai dati sensibili senza maschera. Deve trattarsi di un elenco di utenti separati da punto e virgola. Gli utenti con privilegi di amministratore dispongono sempre dell'accesso ai dati originali senza maschera.
   
    ![Riquadro di spostamento](./media/sql-database-dynamic-data-masking-get-started/8_ddm_excluded_users.png)
   
   > [!TIP]
   > Per fare in modo che il livello dell'applicazione consenta la visualizzazione dei dati sensibili per gli utenti dell'applicazione con privilegi, aggiungere l'utente SQL o l'identità AAD usata dall'applicazione per eseguire query nel database. È altamente consigliabile che l'elenco contenga un numero limitato di utenti con privilegi per ridurre al minimo l'esposizione dei dati sensibili.
   > 
   > 
10. Fare clic su **Salva** nella pagina di configurazione della maschera dati per salvare il criterio di maschera nuovo o aggiornato.


## <a name="next-steps"></a>Passaggi successivi

* Per una panoramica, vedere l'articolo su [Maschera dati dinamica](sql-database-dynamic-data-masking-get-started.md).
* È anche possibile implementare tale funzione tramite [cmdlet del database SQL di Azure](https://msdn.microsoft.com/library/azure/mt574084.aspx) o l'[API REST](https://msdn.microsoft.com/library/dn505719.aspx).
