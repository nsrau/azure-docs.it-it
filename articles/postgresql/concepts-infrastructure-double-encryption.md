---
title: Crittografia doppia dell'infrastruttura-database di Azure per PostgreSQL
description: Informazioni sull'uso della crittografia a doppia infrastruttura per aggiungere un secondo livello di crittografia con chiavi gestite dal servizio.
author: kummanish
ms.author: manishku
ms.service: postgresql
ms.topic: conceptual
ms.date: 6/30/2020
ms.openlocfilehash: d54bef277d80a178c45ce918290d46c11387b7d1
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/26/2020
ms.locfileid: "88918095"
---
# <a name="azure-database-for-postgresql-infrastructure-double-encryption"></a>Crittografia doppia dell'infrastruttura del database di Azure per PostgreSQL

> [!NOTE]
> Al momento, per usare questa funzionalità è necessario richiedere l'accesso. A tale scopo, contattare AskAzureDBforPostgreSQL@service.microsoft.com .

Database di Azure per PostgreSQL usa la [crittografia di archiviazione dei dati](concepts-security.md#at-rest) inattivi per i dati usando le chiavi gestite di Microsoft. I dati, inclusi i backup, vengono crittografati su disco e questa crittografia è sempre attiva e non può essere disabilitata. La crittografia usa il modulo crittografico convalidato FIPS 140-2 e una crittografia AES 256 bit per la crittografia di archiviazione di Azure.

La crittografia doppia dell'infrastruttura aggiunge un secondo livello di crittografia usando le chiavi gestite dal servizio. Usa il modulo crittografico convalidato FIPS 140-2, ma con un algoritmo di crittografia diverso. Questo offre un livello di protezione aggiuntivo per i dati inattivi. La chiave usata nella crittografia a doppia infrastruttura viene anche gestita dal servizio database di Azure per PostgreSQL. La crittografia doppia dell'infrastruttura non è abilitata per impostazione predefinita, poiché il livello di crittografia aggiuntivo può avere un effetto sulle prestazioni.

> [!NOTE]
> Questa funzionalità è disponibile in tutte le aree di Azure in cui database di Azure per PostgreSQL supporta i piani tariffari "per utilizzo generico" e "con ottimizzazione per la memoria".

La crittografia a livello di infrastruttura offre il vantaggio di essere implementata a livello più vicino al dispositivo di archiviazione o ai cavi di rete. Database di Azure per PostgreSQL implementa i due livelli di crittografia usando le chiavi gestite dal servizio. Sebbene sia ancora tecnicamente al livello del servizio, è molto vicino all'hardware che archivia i dati inattivi. Facoltativamente, è comunque possibile abilitare la crittografia dei dati inattivi usando la [chiave gestita dal cliente](concepts-data-encryption-postgresql.md) per il server PostgreSQL sottoposta a provisioning.  

Anche l'implementazione a livello di infrastruttura supporta una varietà di chiavi. L'infrastruttura deve essere in grado di riconoscere diversi cluster di computer e reti. Di conseguenza, vengono usate chiavi diverse per ridurre al minimo il raggio di esplosione degli attacchi dell'infrastruttura e una serie di errori hardware e di rete. 

> [!NOTE]
> L'uso della crittografia a doppia infrastruttura avrà un effetto sulle prestazioni del database di Azure per il server PostgreSQL a causa del processo di crittografia aggiuntivo.

## <a name="benefits"></a>Vantaggi

La crittografia a doppia infrastruttura per database di Azure per PostgreSQL offre i vantaggi seguenti:

1. **Diversità aggiuntiva di implementazione della** crittografia: lo spostamento pianificato per la crittografia basata su hardware produrrà ulteriormente le implementazioni fornendo un'implementazione basata su hardware oltre all'implementazione basata su software.
2. **Errori di implementazione** : due livelli di crittografia a livello di infrastruttura proteggono da eventuali errori di memorizzazione nella cache o di gestione della memoria nei livelli superiori che espongono i dati in testo non crittografato. Inoltre, i due livelli assicurano anche gli errori di implementazione della crittografia in generale.

La combinazione di queste funzionalità garantisce una protezione avanzata contro le minacce e i punti deboli comuni usati per attaccare la crittografia.

## <a name="supported-scenarios-with-infrastructure-double-encryption"></a>Scenari supportati con crittografia doppia dell'infrastruttura

Le funzionalità di crittografia fornite da database di Azure per PostgreSQL possono essere usate insieme. Di seguito è riportato un riepilogo dei vari scenari che è possibile usare:

|  ##   | Crittografia predefinita | Crittografia doppia dell'infrastruttura | Crittografia dei dati tramite chiavi gestite dal cliente  |
|:------|:------------------:|:--------------------------------:|:--------------------------------------------:|
| 1     | *Sì*              | *No*                             | *No*                                         |
| 2     | *Sì*              | *Sì*                            | *No*                                         |
| 3     | *Sì*              | *No*                             | *Sì*                                        |
| 4     | *Sì*              | *Sì*                            | *Sì*                                        |
|       |                    |                                  |                                              |

> [!Important]
> - Gli scenari 2 e 4 avranno un effetto sulle prestazioni nel database di Azure per il server PostgreSQL a causa del livello aggiuntivo di crittografia dell'infrastruttura.
> - La configurazione della crittografia doppia dell'infrastruttura per database di Azure per PostgreSQL è consentita solo durante la creazione del server. Una volta eseguito il provisioning del server, non è possibile modificare la crittografia di archiviazione. Tuttavia, è comunque possibile abilitare la crittografia dei dati usando chiavi gestite dal cliente per il server creato con/senza crittografia a doppia infrastruttura.

## <a name="limitations"></a>Limitazioni

Per database di Azure per PostgreSQL, il supporto per la crittografia a doppia infrastruttura con la chiave gestita dal servizio presenta le limitazioni seguenti:

* Il supporto per questa funzionalità è limitato ai piani tariffari **per utilizzo generico** e con ottimizzazione per la **memoria** .
* È possibile creare un database di Azure per PostgreSQL con l'infrastruttura crittografia doppia abilitata nelle aree seguenti:

   * Stati Uniti orientali
   * Stati Uniti centro-meridionali
   * Stati Uniti occidentali 2
   
* Questa funzionalità è supportata solo nelle aree e nei server che supportano l'archiviazione con un massimo di 16 TB. Per l'elenco delle aree di Azure che supportano l'archiviazione con un massimo di 16 TB, vedere la [documentazione relativa all'archiviazione](concepts-pricing-tiers.md#storage).

    > [!NOTE]
    > - Tutti i **nuovi** server PostgreSQL creati nelle aree elencate sopra supportano anche la crittografia dei dati con le chiavi di gestione clienti. In questo caso, i server creati tramite il ripristino temporizzato (ripristino temporizzato) o le repliche di lettura non sono qualificati come "nuovo".
    > - Per verificare se il server di cui è stato effettuato il provisioning supporta fino a 16 TB, è possibile passare al pannello piano tariffario nel portale e verificare se il dispositivo di scorrimento archiviazione può essere spostato fino a 16 TB. Se è possibile spostare il dispositivo di scorrimento fino a 4 TB, il server potrebbe non supportare la crittografia con chiavi gestite dal cliente; Tuttavia, i dati vengono crittografati in qualsiasi momento usando chiavi gestite dal servizio. Per AskAzureDBforPostgreSQL@service.microsoft.com eventuali domande, contattare il.

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come [configurare la crittografia doppia dell'infrastruttura per database di Azure per PostgreSQL](howto-double-encryption.md).
