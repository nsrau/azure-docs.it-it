---
title: Come viene applicato uno sconto per la prenotazione a un server singolo di Database di Azure per PostgreSQL
description: Come viene applicato uno sconto per la prenotazione a un server singolo di Database di Azure per PostgreSQL
author: kummanish
ms.author: manishku
ms.service: cost-management-billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/17/2019
ms.openlocfilehash: 2f1843690861a463c634f523e82facffc72b6743
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/15/2020
ms.locfileid: "75995507"
---
# <a name="how-a-reservation-discount-is-applied-to-azure-database-for-postgresql-single-server"></a>Come viene applicato uno sconto per la prenotazione a un server singolo di Database di Azure per PostgreSQL

Dopo avere acquistato capacità riservata per un server singolo di Database di Azure per PostgreSQL, lo sconto per la prenotazione viene automaticamente applicato ai database di server singoli di Database di Azure per PostgreSQL che corrispondono agli attributi e alla quantità della prenotazione. Una prenotazione copre solo i costi di calcolo di un server singolo di Database di Azure per PostgreSQL. Per l'archiviazione e la rete vengono addebitate le tariffe normali. 

## <a name="how-reservation-discount-is-applied"></a>Come viene applicato lo sconto per la prenotazione

Uno sconto per la prenotazione si basa sul principio ***use-it-or-lose-it***, ovvero se non viene usato va perso. Quindi, se non si hanno risorse corrispondenti per un'ora, si perderà la quantità di prenotazione per quell'ora. Non è possibile riportare le ore prenotate inutilizzate.</br>

Quando si arresta una risorsa, lo sconto per la prenotazione si applica automaticamente a un'altra risorsa corrispondente nell'ambito specificato. Se non si trovano risorse corrispondenti nell'ambito specificato, le ore prenotate vanno perse.

## <a name="discount-applied-to-azure-database-for-postgresql-single-server"></a>Sconto applicato a un server singolo di Database di Azure per PostgreSQL

Lo sconto per la capacità riservata di un server singolo di Database di Azure per PostgreSQL viene applicato all'esecuzione del server singolo PostgreSQL su base oraria. La prenotazione acquistata viene confrontata con l'utilizzo delle risorse di calcolo generato dal server singolo di Database di Azure per PostgreSQL in esecuzione. Per i server singoli PostgreSQL che non vengono eseguiti per l'intera ora, la prenotazione viene automaticamente applicata ad altri server singoli di Database di Azure per PostgreSQL che corrispondono agli attributi della prenotazione. Lo sconto può essere applicato a server singoli di Database di Azure per PostgreSQL che vengono eseguiti simultaneamente. Se nessun server singolo PostgreSQL in esecuzione per l'intera ora corrisponde agli attributi della prenotazione, non si ottiene il vantaggio completo dello sconto per la prenotazione per tale ora.

Gli esempi seguenti mostrano come viene applicato lo sconto per la capacità riservata di server singoli di Database di Azure per PostgreSQL a seconda del numero di core acquistati e di quando vengono eseguiti.

* **Esempio 1**: si acquista una capacità riservata per un singolo server di database di Azure per PostgreSQL per 8 vCore. Se si esegue un server singolo di Database di Azure per PostgreSQL da 16 vCore che corrisponde al resto degli attributi della prenotazione, viene addebitato il prezzo a consumo per 8 vCore dell'utilizzo di risorse di calcolo del server singolo PostgreSQL e si ottiene lo sconto per la prenotazione per un'ora di utilizzo di risorse di calcolo del server singolo PostgreSQL da 8 vCore.</br>

Per il resto di questi esempi, si supponga che la capacità riservata del server di Database di Azure per PostgreSQL che si acquista sia per un server singolo di Database di Azure per PostgreSQL da 16 vCore e il resto degli attributi di prenotazione corrisponda ai server singoli PostgreSQL in esecuzione.

* **Esempio 2**: eseguire due server di database di Azure per PostgreSQL singoli con 8 vCore ogni per un'ora. Lo sconto per la prenotazione da 16 vCore viene applicato per l'utilizzo di risorse di calcolo per i due server singoli di Database di Azure per PostgreSQL da 8 vCore.

* **Esempio 3**: 1 16 eseguire il server singolo del database di Azure per PostgreSQL di Azure vCore da 1 a 1:30 PM. Si esegue un altro server singolo di Database di Azure per PostgreSQL da 16 vCore dalle 13.30 alle 14. A entrambi i database viene applicato lo sconto sulla prenotazione.

* **Esempio 4**: 1 16 eseguire il server singolo database di Azure per PostgreSQL di Azure vCore da 1 a 1:45 PM. Si esegue un altro server singolo di Database di Azure per PostgreSQL da 16 vCore dalle 13.30 alle 14. Viene addebitata la tariffa con pagamento in base al consumo per la sovrapposizione di 15 minuti. Lo sconto sulla prenotazione viene applicato all'utilizzo delle risorse di calcolo per il resto del tempo.

Per informazioni sull'applicazione delle prenotazioni di Azure nei report sull'utilizzo per la fatturazione, vedere [Informazioni sull'utilizzo delle prenotazioni di Azure](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage-ea).

## <a name="need-help-contact-us"></a>Opzioni per Contattaci
In caso di domande o per assistenza, [creare una richiesta di supporto](https://go.microsoft.com/fwlink/?linkid=2083458).
