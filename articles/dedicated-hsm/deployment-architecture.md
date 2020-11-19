---
title: Architettura di distribuzione - HSM dedicato di Azure | Microsoft Docs
description: Considerazioni sulla progettazione di base quando si usa il modulo HSM dedicato di Azure come parte di un'architettura di applicazione
services: dedicated-hsm
author: msmbaldwin
manager: rkarlin
ms.custom: mvc, seodec18
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 02/05/2020
ms.author: mbaldwin
ms.openlocfilehash: 6a0767b077886337331f24b15715247006f3fe2c
ms.sourcegitcommit: 230d5656b525a2c6a6717525b68a10135c568d67
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/19/2020
ms.locfileid: "94888896"
---
# <a name="azure-dedicated-hsm-deployment-architecture"></a>Architettura di distribuzione del servizio HSM dedicato di Azure

HSM dedicato di Azure offre l'archiviazione di chiavi crittografiche in Azure. Soddisfa requisiti rigorosi di sicurezza. L'uso di HSM dedicato di Azure dedicato sarà utile ai clienti che:

* Devono soddisfare la certificazione FIPS 140-2 Livello 3
* Devono avere accesso esclusivo al modulo di protezione hardware
* Devono avere il controllo completo sui propri dispositivi

I moduli di protezione hardware vengono distribuiti nei data center di Microsoft e il provisioning può essere facilmente effettuato come coppia di dispositivi, come base per una soluzione a disponibilità elevata. Possono inoltre essere distribuiti in più aree, per una soluzione resiliente alle emergenze. Le aree con modulo di protezione hardware dedicato attualmente disponibile possono essere controllate usando la [pagina prodotti in base all'area](https://azure.microsoft.com/global-infrastructure/services/?products=azure-dedicated-hsm). 

Ogni regione dispone di rack HSM distribuiti in due data center indipendenti o in almeno due zone di disponibilità indipendenti. Ad esempio, il Asia orientale meridionale ha tre zone di disponibilità e gli Stati Uniti orientali 2 hanno due. In Europa, in Asia e negli Stati Uniti è disponibile un totale di otto aree che offrono il servizio HSM dedicato e questa modifica quando si aggiungono nuovi rack HSM in nuove aree geografiche. Per altre informazioni sulle aree di Azure, vedere le [informazioni ufficiali sulle aree di Azure](https://azure.microsoft.com/global-infrastructure/regions/).
Alcuni fattori di progettazione per qualsiasi soluzione basata su HSM dedicato sono posizione/latenza, disponibilità elevata e supporto per altre applicazioni distribuite.

## <a name="device-location"></a>Posizione dispositivo

La posizione ottimale per un dispositivo HSM è l'area più vicina alle applicazioni che eseguono le operazioni di crittografia. La latenza prevista all'interno dell'area è di meno di 10 millisecondi. La latenza tra più aree può essere da 5 a 10 volte superiore.

## <a name="high-availability"></a>Disponibilità elevata

Per ottenere la disponibilità elevata, un cliente deve usare due dispositivi HSM in un'area configurata con il software Thanles come coppia di disponibilità elevata. Questo tipo di distribuzione assicura la disponibilità delle chiavi se in un singolo dispositivo si verifica un problema che impedisce l'elaborazione di operazioni relative alle chiavi. Riduce inoltre notevolmente il rischio quando si eseguono interventi di manutenzione in garanzia, come la sostituzione dell'alimentatore. È importante che la progettazione tenga conto di qualsiasi tipo di errore a livello di area. Possono verificarsi errori a livello di area in caso di calamità naturali, come terremoti, uragani o inondazioni. Gli eventi di questo tipo devono essere attenuati effettuando il provisioning di dispositivi HSM in un'altra area. I dispositivi distribuiti in un'altra area possono essere combinati tramite la configurazione software di Thales. Ciò significa che la distribuzione minima per una soluzione a disponibilità elevata e resiliente alle emergenze è composta da quattro dispositivi HSM in due aree. È possibile usare la ridondanza locale e la ridondanza tra aree come baseline a cui aggiungere altre distribuzioni di dispositivi HSM per supportare la latenza, la capacità o per soddisfare altri requisiti specifici dell'applicazione.

## <a name="distributed-application-support"></a>Supporto di applicazioni distribuite

I dispositivi di tipo HSM dedicato vengono in genere distribuiti a supporto delle applicazioni che devono eseguire operazioni di archiviazione e recupero chiavi. Hanno 10 partizioni, per il supporto di applicazioni indipendenti. La posizione del dispositivo deve essere basata su una visione olistica di tutte le applicazioni che devono usare il servizio.

## <a name="next-steps"></a>Passaggi successivi

Una volta determinata l'architettura di distribuzione, la maggior parte delle attività di configurazione per implementare tale architettura verrà fornita da Thales. Questo include la configurazione del dispositivo e gli scenari di integrazione di applicazioni. Per ulteriori informazioni, utilizzare il portale di [supporto clienti di Thales](https://supportportal.gemalto.com/csm/) e scaricare le guide all'amministrazione e alla configurazione. Il sito per i partner Microsoft offre un'ampia gamma di guide all'integrazione.
Prima di eseguire il provisioning di dispositivi o di iniziare con la progettazione o distribuzione di applicazioni, è consigliabile aver ben compreso tutti i concetti chiave relativi al servizio, ad esempio disponibilità elevata e sicurezza.
Altri argomenti sui concetti:

* [Disponibilità elevata](high-availability.md)
* [Sicurezza fisica](physical-security.md)
* [Rete](networking.md)
* [Supporto](supportability.md)
* [Monitoring](monitoring.md)
