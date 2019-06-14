---
title: DNS personalizzato di Istanza gestita di database SQL di Azure | Microsoft Docs
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
manager: craigg
ms.date: 12/13/2018
ms.openlocfilehash: bb5890b883b6062d834b928bff28a26a3664fb64
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60700424"
---
# <a name="configuring-a-custom-dns-for-azure-sql-database-managed-instance"></a>Configurazione di un DNS personalizzato per Istanza gestita di database SQL di Azure

Istanza gestita di database SQL di Azure deve essere distribuita in una [rete virtuale](../virtual-network/virtual-networks-overview.md) di Azure. In alcuni scenari, ad esempio Posta elettronica database e server collegati ad altre istanze SQL nell'ambiente cloud o ibrido, sono richiesti nomi host privati che devono essere risolti dall'istanza gestita. In questo caso, è necessario configurare un DNS personalizzato all'interno di Azure. Poiché Istanza gestita usa lo stesso DNS per i meccanismi interni, la configurazione del DNS della rete virtuale deve essere compatibile con Istanza gestita.

   > [!IMPORTANT]
   > Usare sempre nomi di dominio completi (FQDN) per i server di posta, le istanze di SQL Server e gli altri servizi, anche se sono nella zona DNS privata. Usare ad esempio `smtp.contoso.com` per il server di posta perché il semplice `smtp` non verrà risolto correttamente.

Per rendere una configurazione DNS personalizzata compatibile con l'istanza gestita, è necessario:

- Configurare un server DNS personalizzato in modo che sia in grado di risolvere i nomi di dominio pubblico
- Inserire l'indirizzo IP DNS del resolver ricorsivo di Azure, 168.63.129.16, alla fine dell'elenco del DNS della rete virtuale

## <a name="setting-up-custom-dns-servers-configuration"></a>Definizione della configurazione dei server DNS personalizzati

1. Nel portale di Azure, individuare l'opzione relativa al DNS personalizzato per la propria rete virtuale.

   ![opzione relativa al DNS personalizzato](./media/sql-database-managed-instance-custom-dns/custom-dns-option.png)

2. Passare a Personalizzato e immettere l'indirizzo IP del server DNS personalizzato, l'indirizzo IP 168.63.129.16 del resolver ricorsivo di Azure.

   ![opzione relativa al DNS personalizzato](./media/sql-database-managed-instance-custom-dns/custom-dns-server-ip-address.png)

   > [!IMPORTANT]
   > La mancata impostazione di un resolver ricorsivo di Azure nell'elenco DNS può causare uno stato difettoso di Istanza gestita quando per qualche motivo i server DNS personalizzati non dovessero essere disponibili. Per ripristinare una stato corretto potrebbe essere necessario creare una nuova istanza in una rete virtuale con i criteri di rete conformi, creare i dati a livello di istanza e ripristinare i database. L'impostazione del resolver ricorsivo di Azure come ultima voce dell'elenco DNS garantisce la risoluzione dei nomi pubblici anche in caso di errore di tutti i server DNS personalizzati.

## <a name="next-steps"></a>Passaggi successivi

- Per una panoramica, vedere [Informazioni su Istanza gestita](sql-database-managed-instance.md)
- Per un'esercitazione che illustra come creare una nuova istanza gestita, vedere [Creare un'istanza gestita](sql-database-managed-instance-get-started.md).
- Per informazioni sulla configurazione di una rete virtuale per un'istanza gestita, vedere la [configurazione della rete virtuale per le istanze gestite](sql-database-managed-instance-connectivity-architecture.md).
