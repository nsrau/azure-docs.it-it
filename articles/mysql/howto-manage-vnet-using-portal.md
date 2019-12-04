---
title: Gestire endpoint VNet-portale di Azure-database di Azure per MySQL
description: Creare e gestire gli endpoint e le regole di servizio di rete virtuale per Database di Azure per MySQL tramite il portale di Azure
author: bolzmj
ms.author: mbolz
ms.service: mysql
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: 7479b16f2e1f14d8ebe611bf3121005af342ccb9
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74764934"
---
# <a name="create-and-manage-azure-database-for-mysql-vnet-service-endpoints-and-vnet-rules-by-using-the-azure-portal"></a>Creare e gestire gli endpoint e le regole di servizio di rete virtuale per Database di Azure per MySQL tramite il portale di Azure
Gli endpoint e le regole dei servizi di rete virtuale estendono lo spazio di indirizzi privato di una rete virtuale a un server di Database di Azure per MySQL. Per una panoramica degli endpoint di servizio di rete virtuale per Database di Azure per MySQL, incluse le limitazioni, vedere [Usare gli endpoint e le regole di servizio di rete virtuale per Database di Azure per MySQL](concepts-data-access-and-security-vnet.md). Gli endpoint di servizio di rete virtuale sono disponibili in tutte le aree supportate per Database di Azure per MySQL.

> [!NOTE]
> Gli endpoint di servizio di rete virtuale sono supportati solo per i server per utilizzo generico e ottimizzati per la memoria.
> In caso di peering di reti virtuali, se il traffico scorre attraverso un gateway di rete virtuale comune con endpoint di servizio e deve raggiungere il peer, creare una regola di elenco di controllo di accesso o di rete virtuale per consentire a Macchine virtuali di Azure all'interno della rete virtuale del gateway di accedere al server di Database di Azure per MySQL.


## <a name="create-a-vnet-rule-and-enable-service-endpoints-in-the-azure-portal"></a>Creare una regola di rete virtuale e abilitare gli endpoint di servizio nel portale di Azure

1. Nella pagina del server MySQL, in Impostazioni, fare clic su **Sicurezza connessione** per aprire la pagina Sicurezza connessione per Database di Azure per MySQL. 

2. Verificare che il controllo Consenti l'accesso a servizi di Azure sia impostato su **disattivato**.

> [!Important]
> Se si lascia il controllo impostato su ON, il server di database MySQL di Azure accetta la comunicazione da qualsiasi subnet. Lasciando il controllo impostato su SÌ, il numero di accessi potrebbe essere eccessivo dal punto di vista della sicurezza. La funzionalità dell'endpoint del servizio Rete virtuale di Microsoft Azure, in coordinamento con la funzionalità delle regole della rete virtuale di database di Azure per MySQL, può ridurre la superficie di attacco della sicurezza.

3. Fare quindi clic su **+ Aggiunta di una rete virtuale esistente**. Se non si dispone di una rete virtuale, è possibile fare clic su **+ Crea nuova rete virtuale** per crearne una. Vedere [Guida introduttiva: Creare una rete virtuale con il portale di Azure](../virtual-network/quick-create-portal.md).

   ![Portale di Azure: fare clic su Sicurezza connessione](./media/howto-manage-vnet-using-portal/1-connection-security.png)

4. Immettere un nome di regola di rete virtuale, selezionare la sottoscrizione, specificare il nome della rete virtuale e quello della subnet e quindi fare clic su **Abilita**. In questo modo, gli endpoint di servizio di rete virtuale nella subnet vengono abilitati automaticamente tramite il tag di servizio **Microsoft.SQL**.

   ![Portale di Azure: configurare la rete virtuale](./media/howto-manage-vnet-using-portal/2-configure-vnet.png)

   L'account deve avere le autorizzazioni necessarie per la creazione di una rete virtuale e di un endpoint di servizio.

   Gli endpoint di servizio possono essere configurati sulle reti virtuali, in modo indipendente, da un utente con accesso in scrittura alla rete virtuale.
    
   Per associare le risorse dei servizi di Azure a una rete virtuale, l'utente deve avere l'autorizzazione "Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/" per le subnet da aggiungere. Per impostazione predefinita, questa autorizzazione è inclusa nei ruoli di amministratore del servizio predefiniti e può essere modificata creando ruoli personalizzati.
    
   Altre informazioni sui [ruoli predefiniti](https://docs.microsoft.com/azure/active-directory/role-based-access-built-in-roles) e sull'assegnazione di autorizzazioni specifiche ai [ruoli personalizzati](https://docs.microsoft.com/azure/active-directory/role-based-access-control-custom-roles).
    
   Le reti virtuali e le risorse dei servizi di Azure possono essere nella stessa sottoscrizione o in sottoscrizioni diverse. Se la rete virtuale e le risorse dei servizi di Azure si trovano in sottoscrizioni diverse, le risorse devono trovarsi nello stesso tenant di Active Directory (AD). Verificare che il provider di risorse **Microsoft. SQL** sia registrato per entrambe le sottoscrizioni. Per altre informazioni, vedere [Resource-Manager-Registration][resource-manager-portal]

   > [!IMPORTANT]
   > Prima configurare gli endpoint di servizio è consigliabile leggere questo articolo in cui sono riportate considerazioni e istruzioni di configurazione per gli endpoint di servizio. **Endpoint servizio di rete virtuale:** un [endpoint servizio di rete virtuale](../virtual-network/virtual-network-service-endpoints-overview.md) è una subnet in cui i valori delle proprietà includono uno o più nomi formali di tipi di servizi Azure. Gli endpoint dei servizi di rete virtuale usano il nome del tipo di servizio **Microsoft.Sql**, che fa riferimento al servizio di Azure denominato Database SQL. Questo tag di servizio si applica al database SQL di Azure e ai servizi di Database di Azure per PostgreSQL e MySQL. È importante tenere presente che, quando si applica il tag di servizio **Microsoft.Sql** a un endpoint di servizio di rete virtuale, viene configurato il traffico dell'endpoint per tutti i server di Database SQL di Azure, Database di Azure per PostgreSQL e Database di Azure per MySQL nella subnet. 
   > 

5. Dopo aver completato la procedura di abilitazione, fare clic su **OK**. Si noterà che gli endpoint di servizio di rete virtuale sono abilitati insieme a una regola di rete virtuale.

   ![Endpoint di servizio di rete virtuale abilitati e regola di rete virtuale creata](./media/howto-manage-vnet-using-portal/3-vnet-service-endpoints-enabled-vnet-rule-created.png)

## <a name="next-steps"></a>Passaggi successivi
- Con una procedura analoga, è possibile creare script per [abilitare gli endpoint di servizio di rete virtuale e creare una regola di rete virtuale per Database di Azure per MySQL tramite l'interfaccia della riga di comando di Azure](howto-manage-vnet-using-cli.md).
- Per informazioni sulla connessione a un server di Database di Azure per MySQL, vedere [Connection libraries for Azure Database for MySQL](./concepts-connection-libraries.md) (Raccolte di connessioni per Database di Azure per MySQL)

<!-- Link references, to text, Within this same GitHub repo. --> 
[resource-manager-portal]: ../azure-resource-manager/resource-manager-supported-services.md