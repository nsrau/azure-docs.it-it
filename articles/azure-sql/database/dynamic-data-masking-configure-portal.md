---
title: 'Portale di Azure: maschera dati dinamica'
description: Come iniziare a usare la maschera dati dinamica del database SQL di Azure nell'portale di Azure
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: DavidTrigano
ms.author: datrigan
ms.reviewer: vanto
ms.date: 04/28/2020
ms.openlocfilehash: 3f8c296860fc36772a85039e27c86a72614c2a50
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92791053"
---
# <a name="get-started-with-sql-database-dynamic-data-masking-with-the-azure-portal"></a>Introduzione alla funzione Maschera dati dinamica del database SQL nel portale di Azure
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Questo articolo illustra come implementare la funzione [Maschera dati dinamica](dynamic-data-masking-overview.md) con il portale di Azure. È anche possibile implementare tale funzione tramite [cmdlet del database SQL di Azure](/powershell/module/az.sql/) o l'[API REST](/rest/api/sql/).

> [!NOTE]
> Non è possibile impostare questa funzionalità usando il portale per le sinapsi di Azure (usare PowerShell o l'API REST) o SQL Istanza gestita. Per altre informazioni, vedere [Dynamic Data Masking](/sql/relational-databases/security/dynamic-data-masking).

## <a name="set-up-dynamic-data-masking-for-your-database-using-the-azure-portal"></a>Configurare il mascheramento dei dati dinamici per il database tramite il portale di Azure

1. Avviare il portale di Azure all'indirizzo [https://portal.azure.com](https://portal.azure.com) .
2. Accedere alla pagina di configurazione del database che include i dati sensibili a cui si desidera applicare la maschera.
3. Fare clic sul pannello **Dynamic Data Masking** nella sezione **sicurezza** del database.

   ![Screenshot che mostra la sezione sicurezza con Dynamic Data Masking evidenziato.](./media/dynamic-data-masking-configure-portal/4_ddm_settings_tile.png)

4. Nella pagina di configurazione **Maschera dati dinamica** potrebbero essere visualizzate alcune colonne del database che il motore di raccomandazioni ha contrassegnato per l'applicazione della maschera. Per accettare i suggerimenti, è sufficiente fare clic su **Aggiungi maschera** per una o più colonne e verrà creata una maschera in base al tipo predefinito per questa colonna. È possibile modificare la funzione maschera facendo clic sulla regola di maschera e modificando il formato maschera del campo su un formato diverso a propria scelta. Assicurarsi di salvare le impostazioni facendo clic su **Salva** .

    ![Screenshot che mostra la pagina di configurazione Dynamic Data Masking.](./media/dynamic-data-masking-configure-portal/5_ddm_recommendations.png)

5. Per aggiungere una maschera a una colonna del database, nella parte superiore della pagina di configurazione **Maschera dati dinamica** fare clic su **Aggiungi maschera** per aprire la pagina di configurazione **Aggiungi regola di maschera** .

    ![Screenshot che mostra la pagina di configurazione Aggiungi regola di maschera.](./media/dynamic-data-masking-configure-portal/6_ddm_add_mask.png)

6. Selezionare lo **schema** , la **tabella** e la **colonna** per definire i campi designati a cui verrà applicata la maschera.
7. **Selezionare la modalità di maschera** dall'elenco di categorie di maschera dati sensibili.

    ![Screenshot che mostra le categorie di maschera dati sensibili nella sezione selezionare la modalità di maschera.](./media/dynamic-data-masking-configure-portal/7_ddm_mask_field_format.png)

8. Fare clic su **Aggiungi** nella pagina regola maschera dati per aggiornare il set di regole di maschera nel criterio maschera dati dinamica.
9. Digitare le identità SQL Users o Azure Active Directory (Azure AD) che devono essere escluse dalla maschera e avere accesso ai dati sensibili senza mascheramento. Deve trattarsi di un elenco di utenti separati da punto e virgola. Gli utenti con privilegi di amministratore dispongono sempre dell'accesso ai dati originali senza maschera.

    ![Riquadro di spostamento](./media/dynamic-data-masking-configure-portal/8_ddm_excluded_users.png)

    > [!TIP]
    > Per fare in modo che il livello dell'applicazione possa visualizzare dati sensibili per gli utenti con privilegi di applicazione, aggiungere l'utente SQL o l'identità Azure AD utilizzata dall'applicazione per eseguire una query sul database. È altamente consigliabile che l'elenco contenga un numero limitato di utenti con privilegi per ridurre al minimo l'esposizione dei dati sensibili.

10. Fare clic su **Salva** nella pagina di configurazione della maschera dati per salvare il criterio di maschera nuovo o aggiornato.

## <a name="next-steps"></a>Passaggi successivi

- Per una panoramica, vedere l'articolo su [Maschera dati dinamica](dynamic-data-masking-overview.md).
- È anche possibile implementare tale funzione tramite [cmdlet del database SQL di Azure](/powershell/module/az.sql/) o l'[API REST](/rest/api/sql/).