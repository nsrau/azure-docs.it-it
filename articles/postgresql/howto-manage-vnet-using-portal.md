---
title: Creare e gestire gli endpoint e le regole di servizio di rete virtuale per Database di Azure per PostgreSQL tramite il portale di Azure | Microsoft Docs
description: Creare e gestire gli endpoint e le regole di servizio di rete virtuale per Database di Azure per PostgreSQL tramite il portale di Azure
services: postgresql
author: mbolz
ms.author: mbolz
ms.reviewer: jasonwhowell
ms.service: postgresql
ms.topic: conceptual
ms.date: 10/23/2018
ms.openlocfilehash: b1cdabdf3c4f5355df568e940ed78e392a9c6612
ms.sourcegitcommit: 5c00e98c0d825f7005cb0f07d62052aff0bc0ca8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/24/2018
ms.locfileid: "49957174"
---
# <a name="create-and-manage-azure-database-for-postgresql-vnet-service-endpoints-and-vnet-rules-by-using-the-azure-portal"></a>Creare e gestire gli endpoint e le regole di servizio di rete virtuale per Database di Azure per PostgreSQL tramite il portale di Azure
Gli endpoint e le regole dei servizi di rete virtuale estendono lo spazio di indirizzi privato di una rete virtuale a un server di Database di Azure per PostgreSQL. Per una panoramica degli endpoint di servizio di rete virtuale per Database di Azure per PostgreSQL, incluse le limitazioni, vedere [Usare gli endpoint e le regole di servizio di rete virtuale per Database di Azure per PostgreSQL](concepts-data-access-and-security-vnet.md). Gli endpoint di servizio di rete virtuale sono disponibili in tutte le aree supportate per Database di Azure per PostgreSQL.

> [!NOTE]
> Gli endpoint di servizio di rete virtuale sono supportati solo per i server per utilizzo generico e ottimizzati per la memoria.

## <a name="create-a-vnet-rule-and-enable-service-endpoints-in-the-azure-portal"></a>Creare una regola di rete virtuale e abilitare gli endpoint di servizio nel portale di Azure

1. Nella pagina del server PostgreSQL, in Impostazioni, fare clic su **Sicurezza connessione** per aprire la pagina Sicurezza connessione per Database di Azure per PostgreSQL. Fare quindi clic su **+ Aggiunta di una rete virtuale esistente**. Se non si dispone di una rete virtuale, è possibile fare clic su **+ Crea nuova rete virtuale** per crearne una. Vedere [Guida introduttiva: Creare una rete virtuale con il portale di Azure](../virtual-network/quick-create-portal.md).

   ![Portale di Azure: fare clic su Sicurezza connessione](./media/howto-manage-vnet-using-portal/1-connection-security.png)

2. Immettere un nome di regola di rete virtuale, selezionare la sottoscrizione, specificare il nome della rete virtuale e quello della subnet e quindi fare clic su **Abilita**. In questo modo, gli endpoint di servizio di rete virtuale nella subnet vengono abilitati automaticamente tramite il tag di servizio **Microsoft.SQL**.

   ![Portale di Azure: configurare la rete virtuale](./media/howto-manage-vnet-using-portal/2-configure-vnet.png)

    L'account deve avere le autorizzazioni necessarie per la creazione di una rete virtuale e di un endpoint di servizio.

    Gli endpoint di servizio possono essere configurati sulle reti virtuali, in modo indipendente, da un utente con accesso in scrittura alla rete virtuale.
    
    Per associare le risorse dei servizi di Azure a una rete virtuale, l'utente deve avere l'autorizzazione "Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/" per le subnet da aggiungere. Per impostazione predefinita, questa autorizzazione è inclusa nei ruoli di amministratore del servizio predefiniti e può essere modificata creando ruoli personalizzati.
    
    Altre informazioni sui [ruoli predefiniti](https://docs.microsoft.com/azure/active-directory/role-based-access-built-in-roles) e sull'assegnazione di autorizzazioni specifiche ai [ruoli personalizzati](https://docs.microsoft.com/azure/active-directory/role-based-access-control-custom-roles).
    
    Le reti virtuali e le risorse dei servizi di Azure possono essere nella stessa sottoscrizione o in sottoscrizioni diverse. Se la rete virtuale e le risorse dei servizi di Azure si trovano in sottoscrizioni diverse, le risorse devono trovarsi nello stesso tenant di Active Directory (AD).

   > [!IMPORTANT]
   > Prima configurare gli endpoint di servizio è consigliabile leggere questo articolo in cui sono riportate considerazioni e istruzioni di configurazione per gli endpoint di servizio. **Endpoint di servizio di rete virtuale:** un [endpoint di servizio di rete virtuale](../virtual-network/virtual-network-service-endpoints-overview.md) è una subnet in cui i valori delle proprietà includono uno o più nomi formali di tipi di servizi Azure. Gli endpoint dei servizi di rete virtuale usano il nome del tipo di servizio **Microsoft.Sql**, che fa riferimento al servizio di Azure denominato Database SQL. Questo tag di servizio si applica al database SQL di Azure e ai servizi di Database di Azure per PostgreSQL e MySQL. È importante tenere presente che, quando si applica il tag di servizio **Microsoft.Sql** a un endpoint di servizio di rete virtuale, viene configurato il traffico dell'endpoint per tutti i server di Database SQL di Azure, Database di Azure per PostgreSQL e Database di Azure per MySQL nella subnet. 
   > 

3. Dopo aver completato la procedura di abilitazione, fare clic su **OK**. Si noterà che gli endpoint di servizio di rete virtuale sono abilitati insieme a una regola di rete virtuale.

   ![Endpoint di servizio di rete virtuale abilitati e regola di rete virtuale creata](./media/howto-manage-vnet-using-portal/3-vnet-service-endpoints-enabled-vnet-rule-created.png)

## <a name="next-steps"></a>Passaggi successivi
- Con una procedura analoga, è possibile creare script per [abilitare gli endpoint di servizio di rete virtuale e creare una regola di rete virtuale per Database di Azure per PostgreSQL tramite l'interfaccia della riga di comando di Azure](howto-manage-vnet-using-cli.md).
- Per informazioni sulla connessione a un'istanza di Database di Azure per il server PostgreSQL, vedere [Connection libraries for Azure Database for PostgreSQL](./concepts-connection-libraries.md) (Raccolte di connessioni per Database di Azure per PostgreSQL)
