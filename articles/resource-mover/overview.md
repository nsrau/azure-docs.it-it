---
title: Che cos'è Spostamento risorse di Azure?
description: Informazioni su Spostamento risorse di Azure
author: rayne-wiselman
manager: evansma
ms.service: azure-migrate
ms.topic: overview
ms.date: 09/09/2020
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 621c5e7b0061ccd76fd0109552107915b943511f
ms.sourcegitcommit: 3be3537ead3388a6810410dfbfe19fc210f89fec
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/10/2020
ms.locfileid: "89653337"
---
# <a name="what-is-azure-resource-mover"></a>Che cos'è Spostamento risorse di Azure?

Questo articolo offre una panoramica del servizio Spostamento risorse di Azure. Spostamento risorse consente di spostare risorse di Azure tra aree di Azure.

È possibile spostare le risorse in aree di Azure diverse per:

- **Allinearsi all'introduzione di una nuova area**: spostare le risorse in un'area di Azure appena introdotta che non era disponibile in precedenza.
- **Usufruire di servizi/funzionalità**: spostare le risorse per sfruttare servizi o funzionalità disponibili in un'area specifica.
- **Rispondere a sviluppi aziendali**: spostare le risorse in un'area in risposta a modifiche aziendali, ad esempio fusioni o acquisizioni.
- **Allinearsi per prossimità**: spostare le risorse in un'area in cui si trova l'azienda.
- **Soddisfare requisiti relativi ai dati**: spostare le risorse per garantire l'allineamento ai requisiti di residenza dei dati o alle esigenze di classificazione dei dati.
- **Rispondere a requisiti di distribuzione**: spostare le risorse distribuite per errore o spostarle in risposta a esigenze di capacità.
- **Rispondere alla rimozione di autorizzazioni**: spostare le risorse a causa della rimozione delle autorizzazioni per un'area.

> [!IMPORTANT]
> Spostamento risorse di Azure è attualmente disponibile in anteprima pubblica.

## <a name="why-use-resource-mover"></a>Perché usare Spostamento risorse?

Spostamento risorse offre quanto segue:

- Un singolo hub per lo spostamento di risorse tra aree.
- Riduzione dei tempi richiesti e della complessità degli spostamenti. Tutto il necessario in un'unica posizione.
- Un'esperienza semplice e coerente per spostare diversi tipi di risorse di Azure.
- Un modo semplice per identificare le dipendenze tra le risorse da spostare. È quindi possibile spostare contemporaneamente le risorse correlate in modo che dopo lo spostamento tutto funzioni come previsto nell'area di destinazione.
- Pulizia automatica delle risorse nell'area di origine, se si preferisce eliminarle dopo lo spostamento.
- Test È possibile provare a eseguire uno spostamento e quindi annullarlo se non si vuole procedere a uno spostamento completo.

## <a name="move-across-regions"></a>Spostarsi tra aree

Per spostare le risorse tra le aree, selezionare le risorse da spostare. Spostamento risorse convalida tali risorse e risolve tutte le dipendenze presenti in altre risorse. Se sono presenti dipendenze, è possibile procedere in due modi:
- Spostare nell'area di destinazione le risorse dipendenti.
- Non spostare le risorse dipendenti, ma usare invece risorse equivalenti nell'area di destinazione.

Dopo che sono state risolte tutte le dipendenze, Spostamento risorse illustra un semplice processo di spostamento.

1. Avviare uno spostamento iniziale.
2. Dopo lo spostamento iniziale, è possibile decidere se eseguirne il commit per completarlo o se rimuoverlo.
3. Al termine dello spostamento, è possibile decidere se eliminare le risorse nel percorso di origine.

È possibile spostare le risorse tra aree nell'hub Spostamento risorse o a partire da un gruppo di risorse. [Altre informazioni](select-move-tool.md) 

## <a name="what-resources-can-i-move-across-regions"></a>Quali risorse è possibile spostare tra aree?

Tramite Spostamento risorse è attualmente possibile spostare tra aree le risorse seguenti:

- Macchine virtuali di Azure e dischi associati
- Schede di interfaccia di rete
- Set di disponibilità 
- Reti virtuali di Azure 
- Indirizzi IP pubblici
- Gruppi di sicurezza di rete:
- Servizi di bilanciamento del carico interni e pubblici 
- Database SQL di Azure e pool elastici


## <a name="next-steps"></a>Passaggi successivi

[Leggere le informazioni](about-move-process.md) relative ai componenti di Spostamento risorse e al processo di spostamento.
