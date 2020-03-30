---
title: DNS personalizzato dell'istanza gestita
description: Questo argomento descrive le opzioni di configurazione di un DNS personalizzato con un'istanza gestita di database SQL di Azure.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, carlrab
ms.date: 07/17/2019
ms.openlocfilehash: 9c98176413f858d236902c332553d47f749aa3c0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80247079"
---
# <a name="configuring-a-custom-dns-for-azure-sql-database-managed-instance"></a>Configurazione di un DNS personalizzato per Istanza gestita di database SQL di Azure

Istanza gestita di database SQL di Azure deve essere distribuita in una [rete virtuale](../virtual-network/virtual-networks-overview.md) di Azure. In alcuni scenari, ad esempio Posta elettronica database e server collegati ad altre istanze SQL nell'ambiente cloud o ibrido, sono richiesti nomi host privati che devono essere risolti dall'istanza gestita. In questo caso, è necessario configurare un DNS personalizzato all'interno di Azure. 

Poiché managed instance utilizza lo stesso DNS per il proprio funzionamento interno, configurare il server DNS personalizzato in modo che possa risolvere i nomi di dominio pubblici.

> [!IMPORTANT]
> Utilizzare sempre un nome di dominio completo (FQDN) per il server di posta, l'istanza di SQL Server e per altri servizi, anche se si trovano all'interno della zona DNS privata. Ad esempio, `smtp.contoso.com` utilizzare per `smtp` il server di posta perché non si risolverà correttamente. La creazione di un server collegato o di una replica che fa riferimento a macchine virtuali SQL all'interno della stessa rete virtuale richiede anche un nome di dominio completo e un suffisso DNS predefinito. Ad esempio: `SQLVM.internal.cloudapp.net`. Per ulteriori informazioni, vedere [Risoluzione dei](https://docs.microsoft.com/azure/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances#name-resolution-that-uses-your-own-dns-server)nomi che utilizza il proprio server DNS .

> [!IMPORTANT]
> L'aggiornamento dei server DNS della rete virtuale non influirà immediatamente sull'istanza gestita. La configurazione DNS dell'istanza gestita viene aggiornata dopo la scadenza del lease DHCP o dopo l'aggiornamento della piattaforma, a seconda dell'evento che si verifica per primo. **Gli utenti sono invitati a impostare la configurazione DNS della rete virtuale prima di creare la prima istanza gestita.**

## <a name="next-steps"></a>Passaggi successivi

- Per una panoramica, vedere [Che cos'è un'istanza gestitaFor](sql-database-managed-instance.md) an overview, see What is a Managed Instance
- Per un'esercitazione che illustra come creare una nuova istanza gestita, vedere [Creare un'istanza gestita](sql-database-managed-instance-get-started.md).
- Per informazioni sulla configurazione di una rete virtuale per un'istanza gestita, vedere la [configurazione della rete virtuale per le istanze gestite](sql-database-managed-instance-connectivity-architecture.md).
