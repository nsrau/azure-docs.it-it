---
title: Che cos'è la delega della subnet nella rete virtuale di Azure?
description: Informazioni sulla delega della subnet nella rete virtuale di AzureLearn about subnet delegation in Azure virtual network
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74281337"
---
# <a name="what-is-subnet-delegation"></a>Che cos'è la delega della subnet?

La delega della subnet consente di designare una subnet specifica per un servizio Azure PaaS di propria scelta che deve essere inserito nella rete virtuale. La delega della subnet fornisce il controllo completo al cliente sulla gestione dell'integrazione dei servizi di Azure nelle reti virtuali.

Quando si delega una subnet a un servizio di Azure, si consente a tale servizio di stabilire alcune regole di configurazione di rete di base per tale subnet, che consentono al servizio di Azure di gestire le istanze in modo stabile. Di conseguenza, il servizio di Azure può stabilire alcune condizioni pre o post-distribuzione, ad esempio:As a result, the Azure service may establish some pre or post deployment conditions, such as:
- distribuire il servizio in una subnet condivisa e dedicata.
- aggiungere al servizio un set di criteri intento di rete dopo la distribuzione necessario per il corretto funzionamento del servizio.

##  <a name="advantages-of-subnet-delegation"></a>Vantaggi della delega delle subnet

La delega di una subnet a servizi specifici offre i vantaggi seguenti:Delegating a subnet to specific services provides the following advantages:

- consente di designare una subnet per uno o più servizi di Azure e di gestire le istanze nella subnet in base ai requisiti. Ad esempio, il proprietario della rete virtuale può definire quanto segue per una subnet delegata per gestire meglio le risorse e l'accesso come segue:For example, the virtual network owner can define the following for a delegated subnet to better manage resources and access as follows:
    - criteri di traffico di filtraggio di rete con i gruppi di sicurezza di rete.network filtering traffic policies with network security groups.
    - criteri di routing con route definite dall'utente.
    - l'integrazione dei servizi con le configurazioni degli endpoint del servizio.
- consente di inserire servizi per una migliore integrazione con la rete virtuale definendo le precondizioni delle distribuzioni sotto forma di criteri di finalità di rete. Ciò garantisce che tutte le azioni che possono influire sul funzionamento del servizio iniettato possano essere bloccate in PUT.


## <a name="who-can-delegate"></a>Chi può delegare?
La delega della subnet è un esercizio che i proprietari della rete virtuale devono eseguire per designare una delle subnet per un servizio di Azure specifico. Servizio di Azure distribuisce a sua volta le istanze in questa subnet per l'utilizzo da parte dei carichi di lavoro dei clienti.

## <a name="impact-of-subnet-delegation-on-your-subnet"></a>Impatto della delega della subnet sulla subnet
Ogni servizio di Azure definisce il proprio modello di distribuzione, in cui può definire le proprietà che fanno o non supportano in una subnet delegata per scopi di iniezione, ad esempio:Each Azure service defines their own deployment model, where they can define what their do or do not support in a delegated subnet for injection purposes, such as follows:
- subnet condivisa con altri servizi di Azure o VM / macchina virtuale impostata nella stessa subnet o supporta solo una subnet dedicata con solo istanze di questo servizio in esso.
- supporta l'associazione del gruppo di sicurezza di rete con la subnet delegata.
- supporta il gruppo di sicurezza di rete associato alla subnet delegata può essere associato anche a qualsiasi altra subnet.
- consente l'associazione della tabella di route con la subnet delegata.
- consente di associare la tabella di route associata alla subnet delegata a qualsiasi altra subnet.
- determina il numero minimo di indirizzi IP nella subnet delegata.
- stabilisce che lo spazio di indirizzi IP nella subnet delegata provenga da Spazio indirizzi IP privati (10.0.0.0/8, 192.168.0.0/16, 172.16.0.0/12).
- stabilisce che la configurazione DNS personalizzata dispone di una voce DNS di Azure.

I servizi iniettati possono anche aggiungere le proprie politiche come segue:
- Criteri di **sicurezza:** raccolta delle regole di sicurezza necessarie per il funzionamento di un determinato servizio.
- **Criteri percorso**: Raccolta delle route necessarie per il funzionamento di un determinato servizio.

## <a name="what-subnet-delegation-does-not-do"></a>Cosa non fa la delega della subnet

I servizi di Azure inseriti in una subnet delegata hanno ancora il set di base di proprietà disponibili per le subnet non delegate, ad esempio:The Azure services being injectedd into a delegated subnet still have the basic set of properties that are available for non-delegated subnets, such as:
-  I servizi di Azure possono inserire istanze nelle subnet dei clienti, ma non possono influire sui carichi di lavoro esistenti.
-  I criteri o i percorsi applicati da questi servizi sono flessibili e possono essere sostituiti dal cliente.

## <a name="next-steps"></a>Passaggi successivi

- [Delegare una subnet](manage-subnet-delegation.md)
