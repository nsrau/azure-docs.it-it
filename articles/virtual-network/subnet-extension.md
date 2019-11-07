---
title: Estensione della subnet in Azure | Microsoft Docs
description: Informazioni sull'estensione della subnet in Azure.
services: virtual-network
documentationcenter: na
author: anupam-p
manager: narayan
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/31/2019
ms.author: anupand
ms.openlocfilehash: f718471c3f79e9a33b0e03b088f8c8d2ae0231d3
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73587511"
---
# <a name="subnet-extension"></a>Estensione subnet
La migrazione del carico di lavoro al cloud pubblico richiede un'attenta pianificazione e coordinamento. Una delle considerazioni principali può essere la possibilità di mantenere gli indirizzi IP. Ciò può essere importante soprattutto se le applicazioni hanno una dipendenza di indirizzi IP o se sono presenti requisiti di conformità per l'uso di indirizzi IP specifici. Rete virtuale di Azure risolve questo problema consentendo di creare VNet e subnet usando un intervallo di indirizzi IP di propria scelta.

Le migrazioni possono risultare difficoltose quando il requisito precedente è associato a un requisito aggiuntivo per la conservazione di alcune applicazioni locali. In una situazione di questo tipo, è necessario suddividere le applicazioni tra Azure e l'ambiente locale, senza rinumerare gli indirizzi IP su entrambi i lati. Inoltre, sarà necessario consentire alle applicazioni di comunicare come se si trovassero nella stessa rete.

Una soluzione al problema precedente è l'estensione della subnet. L'estensione di una rete consente alle applicazioni di comunicare sullo stesso dominio broadcast quando si trovano in posizioni fisiche diverse, eliminando la necessità di riprogettare la topologia di rete. 

Mentre l'estensione della rete non è una procedura consigliata in generale, i casi d'uso possono essere necessari.

- **Migrazione**in più fasi: lo scenario più comune è che si vuole eseguire una fase della migrazione. Si desidera trasferire prima alcune applicazioni e nel tempo eseguire la migrazione di altre applicazioni in Azure.
- **Latenza**: i requisiti di bassa latenza possono essere un altro motivo per mantenere alcune applicazioni in locale per assicurarsi che siano il più vicino possibile al proprio Data Center.
- **Conformità**: un altro caso di utilizzo è che potrebbero essere presenti requisiti di conformità per la conservazione di alcune applicazioni locali.
 
> [!NOTE] 
> Non è necessario estendere le subnet a meno che non sia necessario. Nei casi in cui si estendono le subnet, è consigliabile provare a impostarlo come passaggio intermedio. Con il tempo, è consigliabile provare a rinumerare le applicazioni nella rete locale ed eseguirne la migrazione in Azure.

Nella sezione successiva verrà illustrato come è possibile estendere le subnet in Azure.


## <a name="extend-your-subnet-to-azure"></a>Estendi la tua subnet ad Azure
 È possibile estendere le subnet locali in Azure usando una soluzione basata sulla rete sovrapposta di livello 3. La maggior parte delle soluzioni usa una tecnologia di sovrapposizione, ad esempio VXLAN, per estendere la rete di livello 2 usando una rete sovrapposta di livello 3. Il diagramma seguente illustra una soluzione generalizzata. In questa soluzione, la stessa subnet esiste su entrambi i lati, ovvero Azure e in locale. 

![Esempio di estensione della subnet](./media/subnet-extension/subnet-extension.png)

Gli indirizzi IP dalla subnet vengono assegnati alle macchine virtuali in Azure e in locale. In Azure e in locale è stato inserito un appliance virtuale di rete. Quando una macchina virtuale in Azure prova a comunicare con una macchina virtuale in una rete locale, l'appliance virtuale di Azure acquisisce il pacchetto, lo incapsula e lo invia tramite VPN/Express route alla rete locale. L'appliance virtuale di rete locale riceve il pacchetto, lo decapsulates e lo inoltra al destinatario previsto nella propria rete. Il traffico di ritorno usa un percorso e una logica simili.

Nell'esempio precedente, l'appliance virtuale di Azure e l'appliance virtuale locale comunicano e apprenderanno informazioni sugli indirizzi IP. Reti più complesse possono anche avere un servizio di mapping, che mantiene il mapping tra appliance virtuali e gli indirizzi IP sottostanti. Quando un appliance virtuale di virtualizzazione riceve un pacchetto, esegue una query sul servizio di mapping per trovare l'indirizzo dell'appliance virtuale di dispositivo con l'indirizzo IP di destinazione sottostante.

Nella sezione successiva sono disponibili informazioni dettagliate sulle soluzioni di estensione della subnet testate in Azure.

## <a name="next-steps"></a>Passaggi successivi 
[Estendi la tua subnet ad Azure usando le soluzioni del fornitore.](https://github.com/microsoft/Azure-LISP)