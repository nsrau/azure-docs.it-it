---
title: Introduzione all'uso di Azure DevTest Labs
description: Questo articolo indica gli scenari principali per l'uso di Azure DevTest Labs e due percorsi generici per iniziare a usare il servizio all'interno dell'organizzazione.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/03/2018
ms.author: spelluru
ms.openlocfilehash: 1d00dbe23fbd724893ba2b964fc54a3ea9066dcb
ms.sourcegitcommit: 609c85e433150e7c27abd3b373d56ee9cf95179a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/03/2018
ms.locfileid: "48250835"
---
# <a name="get-started-with-using-azure-devtest-labs"></a>Introduzione all'uso di Azure DevTest Labs
Una volta deciso di esplorare DevTest Labs, esistono due percorsi generici: Modello di verifica e Distribuzione ridimensionata. 

Una **distribuzione ridimensionata** prevede settimane o mesi di revisione e pianificazione con la finalità di distribuire DevTest Labs a tutta l'azienda con l'impiego di centinaia o migliaia di sviluppatori. 

Una **distribuzione di modello di verifica** è incentrata su un grande sforzo da parte di un unico team per determinare il valore dell'organizzazione. Mentre si può essere tentati a pensare a una distribuzione ridimensionata, l'approccio tende ad avere esito negativo più spesso rispetto all'opzione del modello di verifica. Pertanto, è consigliabile iniziare gradualmente con un primo team per acquisire le conoscenze, ripetere lo stesso approccio con due o tre team aggiuntivi e quindi pianificare una distribuzione ridimensionata in base alla conoscenza acquisita. Per un modello di verifica valido, si consiglia di selezionare uno o due team e identificare i relativi scenari (ambiente di sviluppo rispetto all'ambiente di test) documentare i casi d'uso correnti e distribuire DevTest Labs. 

## <a name="scenarios"></a>Scenari
Esistono tre scenari principali per l'implementazione di DevTest Labs: 

- Desktop degli sviluppatori
- Ambienti di test
- Lab/Training/Hackathon

## <a name="developer-desktops"></a>Desktop degli sviluppatori
Gli sviluppatori hanno spesso requisiti diversi per i computer di sviluppo per progetti diversi. Con DevTest Labs, gli sviluppatori possono avere accesso alle macchine virtuali su richiesta che sono preconfigurate per soddisfare la maggior parte degli scenari. DevTest Labs offre i vantaggi seguenti:

- Le organizzazioni possono offrire ambienti di sviluppo e di test comuni per assicurare coerenza tra i team
- Gli sviluppatori possono effettuare rapidamente il provisioning dei computer di sviluppo su richiesta
- Gli sviluppatori possono effettuare il provisioning di risorse in modalità self-service senza necessità di autorizzazioni a livello di sottoscrizione
- L'IT o gli amministratori possono predefinire la topologia di rete e gli sviluppatori possono sfruttarla direttamente in modo semplice e intuitivo senza richiedere accessi speciali
- Gli sviluppatori possono personalizzare facilmente i computer di sviluppo secondo le necessità
- Gli amministratori possono controllare i costi assicurandosi che:
    - Gli sviluppatori non possano ottenere più VM del necessario per lo sviluppo
    - Le VM vengano arrestate quando non sono in uso
    - Sia consentito solo un subset di dimensioni delle istanze di macchina virtuale per i carichi di lavoro specifici

## <a name="test-environments"></a>Ambienti di test
La creazione e la gestione di ambienti di test in un'organizzazione può richiedere un notevole sforzo. Con DevTest Labs gli ambienti di test possono essere facilmente creati, aggiornati o duplicati. Ciò consente ai team di accedere a un ambiente completamente configurato quando è necessario. In questo scenario DevTest Labs offre i vantaggi seguenti:

- I tester possono provare la versione più recente dell'applicazione eseguendo rapidamente il provisioning di ambienti Windows e Linux usando modelli ed elementi riutilizzabili.
- Possono anche aumentare i test di carico eseguendo il provisioning di più agenti di test
- Gli amministratori possono connettere il lab a Visual Studio Team Services per abilitare scenari DevOps
- Gli amministratori possono controllare i costi assicurandosi che:
    - I tester non possano ottenere più macchine virtuali del necessario
    - Le VM vengano arrestate quando non sono in uso
    - Sia consentito solo un subset di dimensioni delle istanze di macchina virtuale per i carichi di lavoro specifici

## <a name="labs-workshops-trainings-and-hackathons"></a>Lab, workshop, corsi di formazione e hackathon  
Un lab in Azure DevTest Labs funge da contenitore per attività temporanee come workshop, laboratori pratici, corsi di formazione o hackathon. Il servizio consente di creare un lab in cui è possibile offrire modelli personalizzati che ogni partecipante può usare per creare ambienti identici e isolati per il training. È possibile applicare criteri per verificare che per ogni partecipante siano disponibili ambienti di training solo quando sono necessari e contengano un numero sufficiente di risorse, ad esempio macchine virtuali, per il training. Infine, è facilmente possibile condividere il lab con i partecipanti, al quale possono accedere con un clic. Dopo che la classe è stata completata, è facile eliminare il lab e tutte le risorse correlate.


## <a name="next-steps"></a>Passaggi successivi
Vedere l'articolo successivo di questa serie: [Scale up your DevTest Labs deployment](devtest-lab-guidance-scale.md) (Aumentare lo sviluppo di DevTest Labs)
