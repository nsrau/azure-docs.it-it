---
title: Che cos'è la delega della subnet nella rete virtuale di Azure?
description: Informazioni sulla delega della subnet nella rete virtuale di Azure
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/20/2019
ms.author: kumud
ms.openlocfilehash: b33ff808b802b6848e2d5debaf515a73bf21a1bc
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74281337"
---
# <a name="what-is-subnet-delegation"></a>Che cos'è la delega della subnet?

La delega della subnet consente di designare una subnet specifica per un servizio PaaS di Azure di propria scelta che deve essere inserita nella rete virtuale. La delega della subnet fornisce il controllo completo per il cliente sulla gestione dell'integrazione dei servizi di Azure nelle reti virtuali.

Quando si delega una subnet a un servizio di Azure, si consente a tale servizio di stabilire alcune regole di configurazione di rete di base per la subnet, che consentono al servizio di Azure di gestire le istanze in modo stabile. Di conseguenza, il servizio di Azure può stabilire alcune condizioni di pre o post-distribuzione, ad esempio:
- distribuire il servizio in una subnet condivisa o dedicata.
- aggiungere al servizio un set di criteri per finalità di rete dopo la distribuzione necessari per il corretto funzionamento del servizio.

##  <a name="advantages-of-subnet-delegation"></a>Vantaggi della delega della subnet

La delega di una subnet a servizi specifici offre i vantaggi seguenti:

- consente di definire una subnet per uno o più servizi di Azure e di gestire le istanze nella subnet in base ai requisiti. Il proprietario della rete virtuale, ad esempio, può definire quanto segue per una subnet delegata per gestire meglio le risorse e l'accesso come segue:
    - filtrare i criteri di traffico con i gruppi di sicurezza di rete.
    - criteri di routing con route definite dall'utente.
    - integrazione dei servizi con le configurazioni degli endpoint di servizio.
- Aiuta a inserire i servizi per una migliore integrazione con la rete virtuale definendo le relative condizioni preliminari di distribuzione sotto forma di criteri per finalità di rete. In questo modo si garantisce che le azioni che possono influire sul funzionamento del servizio inserito possano essere bloccate in fase di inserimento.


## <a name="who-can-delegate"></a>Chi può delegare?
La delega della subnet è un esercizio che i proprietari della rete virtuale devono eseguire per definire una delle subnet per un servizio di Azure specifico. Il servizio di Azure distribuisce a sua volta le istanze in questa subnet per l'uso da parte dei carichi di lavoro dei clienti.

## <a name="impact-of-subnet-delegation-on-your-subnet"></a>Effetti della delega della subnet sulla subnet
Ogni servizio di Azure definisce il proprio modello di distribuzione, in cui è possibile definire le proprietà che eseguono o non supportano in una subnet delegata per scopi di inserimento, come indicato di seguito:
- subnet condivisa con altri servizi di Azure o VM/set di scalabilità di macchine virtuali nella stessa subnet oppure supporta solo una subnet dedicata solo con le istanze del servizio.
- supporta l'associazione NSG con la subnet delegata.
- supporta il NSG associato alla subnet delegata può essere associato anche a qualsiasi altra subnet.
- consente l'associazione della tabella di route alla subnet delegata.
- consente di associare la tabella di route associata alla subnet delegata a qualsiasi altra subnet.
- determina il numero minimo di indirizzi IP nella subnet delegata.
- stabilisce che lo spazio degli indirizzi IP nella subnet delegata deve essere dallo spazio di indirizzi IP privato (10.0.0.0/8, 192.168.0.0/16, 172.16.0.0/12).
- impone che la configurazione DNS personalizzata includa una voce DNS di Azure.

I servizi inseriti possono anche aggiungere i propri criteri come indicato di seguito:
- **Criteri di sicurezza**: raccolta di regole di sicurezza necessarie per il funzionamento di un determinato servizio.
- **Criteri di route**: raccolta di route necessaria per il funzionamento di un determinato servizio.

## <a name="what-subnet-delegation-does-not-do"></a>Quale delega della subnet non esegue

I servizi di Azure inseriti in una subnet delegata hanno ancora il set di base delle proprietà disponibili per le subnet non delegate, ad esempio:
-  I servizi di Azure possono inserire istanze nelle subnet dei clienti, ma non possono influenzare i carichi di lavoro esistenti.
-  I criteri o le route a cui si applicano questi servizi sono flessibili e possono essere sostituiti dal cliente.

## <a name="next-steps"></a>Passaggi successivi

- [Delegare una subnet](manage-subnet-delegation.md)
