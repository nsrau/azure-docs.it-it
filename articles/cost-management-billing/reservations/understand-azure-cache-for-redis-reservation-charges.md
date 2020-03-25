---
title: Informazioni su come viene applicato lo sconto della prenotazione ad Azure Cache for Redis | Microsoft Docs
description: Informazioni su come viene applicato lo sconto della prenotazione alle istanze di Azure Cache for Redis.
author: yegu-ms
manager: maiye
ms.service: cache
ms.topic: conceptual
ms.date: 01/22/2020
ms.author: yegu
ms.openlocfilehash: 4ad28bd65fe9bc240e963f103ca5bd658e90fdfd
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/24/2020
ms.locfileid: "77529620"
---
# <a name="how-the-reservation-discount-is-applied-to-azure-cache-for-redis"></a>Come viene applicato lo sconto della prenotazione ad Azure Cache for Redis

Dopo avere acquistato capacità riservata per Azure Cache for Redis, lo sconto viene automaticamente applicato alle istanze della cache che corrispondono agli attributi e alla quantità della prenotazione. La prenotazione copre solo i costi di calcolo di Azure Cache for Redis. Per l'archiviazione e la rete vengono addebitate le tariffe normali. La capacità riservata è disponibile solo per le cache del [livello Premium](/azure/azure-cache-for-redis/cache-premium-tier-intro).

## <a name="how-reservation-discount-is-applied"></a>Come viene applicato lo sconto per la prenotazione

Uno sconto per la prenotazione si basa sul principio ***use-it-or-lose-it***, ovvero se non viene usato va perso. Quindi, se non si hanno risorse corrispondenti per un'ora, si perderà la quantità di prenotazione per quell'ora. Non è possibile riportare le ore prenotate inutilizzate.

Quando si arresta una risorsa, lo sconto per la prenotazione si applica automaticamente a un'altra risorsa corrispondente nell'ambito specificato. Se non si trovano risorse corrispondenti nell'ambito specificato, le ore prenotate vanno perse.

## <a name="discount-applied-to-azure-cache-for-redis"></a>Sconto applicato ad Azure Cache for Redis

Lo sconto sulla capacità riservata di Azure Cache for Redis viene applicato alle cache su base oraria. La prenotazione acquistata viene confrontata con l'utilizzo delle risorse di calcolo generato dalle cache in esecuzione. Per le cache che non vengono eseguite per un'ora intera, la prenotazione viene automaticamente applicata ad altre cache che corrispondono ai relativi attributi. Lo sconto può essere applicato a cache in esecuzione simultaneamente. Se nessuna cache in esecuzione per un'intera ora corrisponde agli attributi della prenotazione, non è possibile ottenere il vantaggio completo dello sconto per questa ora.

Gli esempi seguenti mostrano come viene applicato lo sconto per la capacità riservata di Azure Cache for Redis a seconda del numero di cache acquistate e dei tempi di esecuzione.

* **Esempio 1**: Si acquista una capacità riservata di Azure Cache for Redis per una cache di 6 GB. Se si esegue una cache di 13 GB che corrisponde al resto degli attributi della prenotazione, viene addebitato il prezzo a consumo per 7 GB di Azure Cache for Redis per l'utilizzo di risorse delle calcolo e si ottiene lo sconto della prenotazione per un'ora di utilizzo di risorse di calcolo della cache di 6 GB.

Per gli altri esempi, si presuppone che la capacità riservata acquistata per Azure Cache for Redis sia per una cache di 26 GB e che gli altri attributi della prenotazione corrispondano alla cache in esecuzione.

* **Esempio 2:** Si eseguono due cache di 13 GB per un'ora. Lo sconto della prenotazione di 26 GB viene applicato all'utilizzo di risorse di calcolo di entrambe le cache.

* **Esempio 3**: Si esegue una cache di 26 GB dalle 13:00 alle 13:30. Si esegue un'altra cache di 26 GB dalle 13:30 alle 14.00. A entrambi i database viene applicato lo sconto sulla prenotazione.

* **Esempio 4**: Si esegue una cache di 26 GB dalle 13:00 alle 13:45. Si esegue un'altra cache di 26 GB dalle 13:30 alle 14.00. Viene addebitata la tariffa con pagamento in base al consumo per la sovrapposizione di 15 minuti. Lo sconto sulla prenotazione viene applicato all'utilizzo delle risorse di calcolo per il resto del tempo.

Per informazioni sull'applicazione delle prenotazioni di Azure nei report sull'utilizzo per la fatturazione, vedere [Informazioni sull'utilizzo delle prenotazioni di Azure](/azure/billing/billing-understand-reserved-instance-usage-ea).

## <a name="need-help-contact-us"></a>Richiesta di assistenza Contatti
In caso di domande o per assistenza, [creare una richiesta di supporto](https://go.microsoft.com/fwlink/?linkid=2083458).
