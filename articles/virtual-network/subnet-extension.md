---
title: Estensione della subnet in Azure . Documenti Microsoft
description: Informazioni sull'estensione della subnet in Azure.Learn about subnet extension in Azure.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "73587511"
---
# <a name="subnet-extension"></a>Estensione della subnet
La migrazione del carico di lavoro al cloud pubblico richiede un'attenta pianificazione e coordinamento. Una delle considerazioni chiave può essere la possibilità di mantenere i tuoi indirizzi IP. Che può essere importante soprattutto se le applicazioni hanno dipendenza di indirizzo IP o si dispone di requisiti di conformità per utilizzare indirizzi IP specifici. Rete virtuale di Azure risolve questo problema automaticamente consentendo di creare rete virtuale e subnet utilizzando un intervallo di indirizzi IP di propria scelta.

Le migrazioni possono diventare un po' impegnative quando il requisito precedente è associato a un requisito aggiuntivo per mantenere alcune applicazioni in locale. In questo caso, ad esempio una situazione, è necessario dividere le applicazioni tra Azure e locale, senza rinumerare gli indirizzi IP su entrambi i lati. Inoltre, è necessario consentire alle applicazioni di comunicare come se si trovassero nella stessa rete.

Una soluzione al problema precedente è l'estensione della subnet. L'estensione di una rete consente alle applicazioni di comunicare sullo stesso dominio di trasmissione quando esistono in posizioni fisiche diverse, eliminando la necessità di riprogettare la topologia di rete. 

Anche se l'estensione della rete non è una buona pratica in generale, i casi d'uso riportati di seguito possono renderla necessaria.

- **Migrazione in più fasi:** lo scenario più comune è quello di voler eseguire una fase della migrazione. Si vuole portare alcune applicazioni prima e nel tempo eseguire la migrazione di resto delle applicazioni in Azure.You want to bring a few applications first and over time migrate rest of the applications to Azure.
- **Latenza :** I requisiti di latenza bassa possono essere un altro motivo per mantenere alcune applicazioni in locale per garantire che siano il più vicino possibile al data center.
- **Conformità**: Un altro caso d'uso è che si potrebbero avere requisiti di conformità per mantenere alcune delle applicazioni in locale.
 
> [!NOTE] 
> È consigliabile non estendere le subnet a meno che non sia necessario. Nei casi in cui si estendono le subnet, è consigliabile provare a renderlo un passaggio intermedio. Con il tempo, è consigliabile provare a rinumerare le applicazioni nella rete locale ed eseguirne la migrazione in Azure.With time, you should try re-num-number applications in your on-premises network and migrate them to Azure.

In the next section, we'll discuss how you can extend your subnets into Azure.


## <a name="extend-your-subnet-to-azure"></a>Estendere la subnet ad AzureExtend your subnet to Azure
 È possibile estendere le subnet locali ad Azure usando una soluzione basata su rete overlay di livello 3.You can extend your on-premises subnets to Azure using a layer-3 overlay network based solution. La maggior parte delle soluzioni utilizza una tecnologia di overlay come VXLAN per estendere la rete layer-2 utilizzando una rete overlay di livello 3. Il diagramma seguente mostra una soluzione generalizzata. In questa soluzione, la stessa subnet esiste su entrambi i lati, ovvero Azure e locale. 

![Esempio di estensione subnetSubnet Extension Example](./media/subnet-extension/subnet-extension.png)

Gli indirizzi IP della subnet vengono assegnati alle macchine virtuali in Azure e in locale. Sia Azure che in locale hanno un'nVA inserita nelle reti. Quando una macchina virtuale in Azure tenta di comunicare con una macchina virtuale nella rete locale, l'infrastruttura NVA di Azure acquisisce il pacchetto, lo incapsula e lo invia tramite VPN/Express Route alla rete locale. L'nVA locale riceve il pacchetto, lo decapsula e lo inoltra al destinatario desiderato nella rete. Il traffico restituito utilizza un percorso e una logica simili.

Nell'esempio precedente, l'infrastruttura di rete di Azure e l'infrastruttura di rete locale comunicano e apprendono gli indirizzi IP uno dietro l'altro. Reti più complesse possono anche avere un servizio di mappatura, che mantiene il mapping tra le nVA e gli indirizzi IP dietro di loro. Quando un'nVA riceve un pacchetto, interroga il servizio di mapping per individuare l'indirizzo dell'nVA che contiene l'indirizzo IP di destinazione.

In the next section, you'll find details on subnet extension solutions we've tested on Azure.

## <a name="next-steps"></a>Passaggi successivi 
[Estendere la subnet ad Azure usando le soluzioni dei fornitori.](https://github.com/microsoft/Azure-LISP)