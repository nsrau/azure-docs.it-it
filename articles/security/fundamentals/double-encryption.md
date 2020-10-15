---
title: Crittografia doppia in Microsoft Azure
description: Questo articolo descrive il modo in cui Microsoft Azure fornisce la crittografia doppia per i dati inattivi e i dati in transito.
services: security
documentationcenter: na
author: TerryLanfear
manager: rkarlin
ms.assetid: 9dcb190e-e534-4787-bf82-8ce73bf47dba
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/13/2020
ms.author: terrylan
ms.openlocfilehash: 020e8249f57ccb1a14da798a717a00dcc3962389
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88227324"
---
# <a name="double-encryption"></a>Crittografia doppia
La crittografia doppia è la posizione in cui due o più livelli di crittografia indipendenti sono abilitati per la protezione da compromessi di qualsiasi livello di crittografia. L'uso di due livelli di crittografia attenua le minacce che derivano dalla crittografia dei dati. Ad esempio:

- Errori di configurazione nella crittografia dei dati
- Errori di implementazione nell'algoritmo di crittografia
- Compromissione di una singola chiave di crittografia

Azure offre una doppia crittografia per i dati inattivi e i dati in transito.

## <a name="data-at-rest"></a>Dati inattivi
L'approccio di Microsoft per l'abilitazione di due livelli di crittografia per i dati inattivi è:

- **Crittografia del disco con chiavi gestite dal cliente**. Fornire una chiave personalizzata per la crittografia del disco. È possibile portare le proprie chiavi all'Key Vault (BYOK – Bring Your Own Key) o generare nuove chiavi in Azure Key Vault per crittografare le risorse desiderate.
- **Crittografia dell'infrastruttura con chiavi gestite dalla piattaforma**.  Per impostazione predefinita, i dischi vengono crittografati automaticamente a riposo usando chiavi di crittografia gestite dalla piattaforma.

## <a name="data-in-transit"></a>Dati in transito
L'approccio di Microsoft per l'abilitazione di due livelli di crittografia per i dati in transito è il seguente:

- **Crittografia di transito con Transport Layer Security (TLS) 1,2 per proteggere i dati durante il viaggio tra i servizi cloud e l'utente**. Tutto il traffico in uscita da un Data Center viene crittografato in transito, anche se la destinazione del traffico è un altro controller di dominio nella stessa area. TLS 1,2 è il protocollo di sicurezza predefinito usato. Il protocollo TLS offre autenticazione avanzata, riservatezza dei messaggi e integrità (abilitando il rilevamento di manomissioni, intercettazioni e falsificazioni di messaggi), interoperabilità, flessibilità degli algoritmi e facilità di distribuzione e di utilizzo.
- **Ulteriore livello di crittografia fornito a livello di infrastruttura**. Un metodo di crittografia a livello di collegamento dati con gli standard di sicurezza di IEEE 802.1 AE MAC (noto anche come MACsec) viene applicato da punto a punto nell'hardware di rete sottostante. Ogni volta che il traffico dei clienti di Azure viene spostato tra i Data Center, al di fuori dei limiti fisici non controllati da Microsoft (o per conto di Microsoft), i pacchetti vengono crittografati e decrittografati nei dispositivi prima di essere inviati, impedendo gli attacchi fisici "Man-in-the-Middle" o snooping/intercettazioni. Poiché questa tecnologia è integrata nell'hardware di rete, fornisce la crittografia della velocità di riga nell'hardware di rete senza aumento della latenza dei collegamenti misurabile. Questa crittografia MACsec è abilitata per impostazione predefinita per tutto il traffico di Azure che si trova all'interno di un'area o tra aree e non è richiesta alcuna azione da parte dei clienti.

## <a name="next-steps"></a>Passaggi successivi
Informazioni [su come viene usata la crittografia in Azure](encryption-overview.md).
