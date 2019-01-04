---
title: Architettura di distribuzione - HSM dedicato di Azure | Microsoft Docs
description: Considerazioni sulla progettazione di base quando si usa il modulo HSM dedicato di Azure come parte di un'architettura di applicazione
services: dedicated-hsm
author: barclayn
manager: mbaldwin
ms.custom: mvc, seodec18
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: barclayn
ms.openlocfilehash: 913be9d1ee6ce92d71f5f0c7eda46a618483ce8d
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/08/2018
ms.locfileid: "53078294"
---
# <a name="azure-dedicated-hsm-deployment-architecture"></a>Architettura di distribuzione del modulo HSM dedicato di Azure

HSM dedicato di Azure offre l'archiviazione di chiavi crittografiche in Azure. Soddisfa requisiti rigorosi di sicurezza. L'uso di HSM dedicato di Azure dedicato sarà utile ai clienti che:

* Devono soddisfare la certificazione FIPS 140-2 Livello 3
* Devono avere accesso esclusivo al modulo di protezione hardware
* Devono avere il controllo completo sui propri dispositivi

I moduli di protezione hardware vengono distribuiti nei data center di Microsoft e il provisioning può essere facilmente effettuato come coppia di dispositivi, come base per una soluzione a disponibilità elevata. Possono inoltre essere distribuiti in più aree, per una soluzione resiliente alle emergenze. Le aree in cui è attualmente disponibile HSM dedicato sono:

* Stati Uniti orientali
* Stati Uniti orientali 2
* Stati Uniti occidentali
* Stati Uniti centro-meridionali
* Asia sudorientale
* Asia orientale
* Europa settentrionale
* Europa occidentale

In ognuna di queste aree, i rack di moduli di protezione hardware sono distribuiti all'interno di due data center indipendenti o in almeno due zone di disponibilità indipendenti. L'Asia sud-orientale ha tre zone di disponibilità, l'area Stati Uniti orientali 2 ne ha tre. Il servizio HSM dedicato è offerto in un totale di otto aree in Europa, Asia orientale e Stati Uniti. Per altre informazioni sulle aree di Azure, vedere le [informazioni ufficiali sulle aree di Azure](https://azure.microsoft.com/global-infrastructure/regions/).
Alcuni fattori di progettazione per qualsiasi soluzione basata su HSM dedicato sono posizione/latenza, disponibilità elevata e supporto per altre applicazioni distribuite.

## <a name="device-location"></a>Posizione dispositivo

La posizione ottimale per un dispositivo HSM è l'area più vicina alle applicazioni che eseguono le operazioni di crittografia. La latenza prevista all'interno dell'area è di meno di 10 millisecondi. La latenza tra più aree può essere da 5 a 10 volte superiore.

## <a name="high-availability"></a>Disponibilità elevata

Per ottenere la disponibilità elevata, il cliente deve usare due dispositivi HSM all'interno di un'area configurati mediante il software Gemalto come coppia a disponibilità elevata. Questo tipo di distribuzione assicura la disponibilità delle chiavi se in un singolo dispositivo si verifica un problema che impedisce l'elaborazione di operazioni relative alle chiavi. Riduce inoltre notevolmente il rischio quando si eseguono interventi di manutenzione in garanzia, come la sostituzione dell'alimentatore. È importante che la progettazione tenga conto di qualsiasi tipo di errore a livello di area. Possono verificarsi errori a livello di area in caso di calamità naturali, come terremoti, uragani o inondazioni. Gli eventi di questo tipo devono essere attenuati effettuando il provisioning di dispositivi HSM in un'altra area. I dispositivi distribuiti in un'altra area possono essere associati tra loro tramite la configurazione del software Gemalto. Ciò significa che la distribuzione minima per una soluzione a disponibilità elevata e resiliente alle emergenze è composta da quattro dispositivi HSM in due aree. È possibile usare la ridondanza locale e la ridondanza tra aree come baseline a cui aggiungere altre distribuzioni di dispositivi HSM per supportare la latenza, la capacità o per soddisfare altri requisiti specifici dell'applicazione.

## <a name="distributed-application-support"></a>Supporto di applicazioni distribuite

I dispositivi di tipo HSM dedicato vengono in genere distribuiti a supporto delle applicazioni che devono eseguire operazioni di archiviazione e recupero chiavi. Hanno 10 partizioni, per il supporto di applicazioni indipendenti. La posizione del dispositivo deve essere basata su una visione olistica di tutte le applicazioni che devono usare il servizio.

## <a name="next-steps"></a>Passaggi successivi

Una volta determinata l'architettura di distribuzione, la maggior parte delle attività di configurazione per implementare tale architettura verrà fornita da Gemalto. Questo include la configurazione del dispositivo e gli scenari di integrazione di applicazioni. Per altre informazioni, usare il portale di [assistenza clienti di Gemalto](https://supportportal.gemalto.com/csm/) e scaricare le guide all'amministrazione e alla configurazione. Il sito per i partner Microsoft offre un'ampia gamma di guide all'integrazione.
Prima di eseguire il provisioning di dispositivi o di iniziare con la progettazione o distribuzione di applicazioni, è consigliabile aver ben compreso tutti i concetti chiave relativi al servizio, ad esempio disponibilità elevata e sicurezza.
Altri argomenti sui concetti:

* [Disponibilità elevata](high-availability.md)
* [Sicurezza fisica](physical-security.md)
* [Rete](networking.md)
* [Supporto](supportability.md)
* [Monitoraggio](monitoring.md)
