---
title: DNS personalizzato di Istanza gestita di database SQL di Azure | Microsoft Docs
description: Questo argomento descrive le opzioni di configurazione di un DNS personalizzato con un'istanza gestita di database SQL di Azure.
services: sql-database
author: srdjan-bozovic
manager: craigg
ms.service: sql-database
ms.custom: managed instance
ms.topic: article
ms.date: 03/07/2018
ms.author: srbozovi
ms.reviewer: bonova, carlrab
ms.openlocfilehash: 993ff518a1987581ad7de4797611f104080c2c2f
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/16/2018
---
# <a name="configuring-a-custom-dns-for-azure-sql-database-managed-instance"></a>Configurazione di un DNS personalizzato per Istanza gestita di database SQL di Azure

Un'istanza gestita di database SQL di Azure (anteprima) deve essere distribuita in una [rete virtuale](../virtual-network/virtual-networks-overview.md) di Azure. Esistono alcuni scenari: server collegati ad altre istanze SQL nel cloud dell'utente o un ambiente ibrido, che richiede nomi host privati che devono essere risolti dall'istanza gestita. In questo caso, è necessario configurare un DNS personalizzato all'interno di Azure. Poiché Istanza gestita usa lo stesso DNS per i meccanismi interni, la configurazione del DNS della rete virtuale deve essere compatibile con Istanza gestita. 

Per rendere una configurazione DNS personalizzata compatibile con istanza gestita, è necessario completare i passaggi seguenti: 
- Configurare un DNS personalizzato per inoltrare le richieste al servizio DNS di Azure 
- Configurare il DNS personalizzato come primario e il DNS di Azure come secondario per la rete virtuale 
- Registrare il DNS personalizzato come primario e il DNS di Azure come secondario

## <a name="configure-custom-dns-to-forward-requests-to-azure-dns"></a>Configurare un DNS personalizzato per inoltrare le richieste al servizio DNS di Azure 

Per configurare l'inoltro del DNS in Windows Server 2016, seguire questa procedura: 

1. In **Server Manager** fare clic su **Strumenti** e quindi su **DNS**. 

   ![DNS](./media/sql-database-managed-instance-custom-dns/dns.png) 

2. Fare doppio clic su **Server d'inoltro**.

   ![Server d'inoltro](./media/sql-database-managed-instance-custom-dns/forwarders.png) 

3. Fare clic su **Modifica**. 

   ![Elenco di server d'inoltro](./media/sql-database-managed-instance-custom-dns/forwarders-list.png) 

4. Immettere l'indirizzo IP dei resolver ricorsivi di Azure, ad esempio 168.63.129.16.

   ![Indirizzo IP dei resolver ricorsivi](./media/sql-database-managed-instance-custom-dns/recursive-resolvers-ip-address.png) 
 
## <a name="set-up-custom-dns-as-primary-and-azure-dns-as-secondary"></a>Impostare il DNS personalizzato come primario e il DNS di Azure come secondario 
 
La configurazione del DNS in una rete virtuale di Azure richiede l'immissione degli indirizzi IP, quindi occorre configurare la macchina virtuale di Azure che ospita il server DNS con un indirizzo IP statico usando la procedura seguente: 

1. Nel portale di Azure, aprire l'interfaccia di rete delle macchine virtuali del DNS personalizzato.

   ![interfaccia di rete](./media/sql-database-managed-instance-custom-dns/network-interface.png) 

2. Sezione Configurazioni IP. selezionare la configurazione IP 

   ![configurazione IP](./media/sql-database-managed-instance-custom-dns/ip-configuration.png) 


3. Impostare l'indirizzo IP privato come statico. Prendere nota dell'indirizzo IP (10.0.1.5 in questo screenshot) 

   ![Statica](./media/sql-database-managed-instance-custom-dns/static.png) 


## <a name="register-custom-dns-as-primary-and-azure-dns-as-secondary"></a>Registrare il DNS personalizzato come primario e il DNS di Azure come secondario 

1. Nel portale di Azure, individuare l'opzione relativa al DNS personalizzato per la propria rete virtuale.

   ![opzione relativa al DNS personalizzato](./media/sql-database-managed-instance-custom-dns/custom-dns-option.png) 

2. Passare a Personalizzato e immettere l'indirizzo IP del server DNS personalizzato, l'indirizzo IP dei resolver ricorsivi di Azure, ad esempio 168.63.129.16. 

   ![opzione relativa al DNS personalizzato](./media/sql-database-managed-instance-custom-dns/custom-dns-server-ip-address.png) 

   > [!IMPORTANT]
   > Se non si imposta il resolver ricorsivo di Azure nell'elenco DNS, l'istanza gestita entra in stato di errore. Per ripristinare una stato corretto potrebbe essere necessario creare una nuova istanza in una rete virtuale con i criteri di rete conformi, creare i dati a livello di istanza e ripristinare i database. Vedere [Configurazione della rete virtuale](sql-database-managed-instance-vnet-configuration.md).

## <a name="next-steps"></a>Passaggi successivi

- Per una panoramica, vedere [Informazioni su Istanza gestita](sql-database-managed-instance.md)
- Per un esempio di creazione di una rete virtuale e quindi di un'istanza gestita, vedere [Creazione di un'istanza gestita](sql-database-managed-instance-tutorial-portal.md).
- Per la configurazione della rete virtuale, vedere [Configurazione della rete virtuale per le istanze gestite](sql-database-managed-instance-vnet-configuration.md)
