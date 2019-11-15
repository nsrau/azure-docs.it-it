---
title: Come viene applicato uno sconto per la prenotazione a Database di Azure per MySQL
description: Come viene applicato uno sconto per la prenotazione a Database di Azure per MySQL
author: kummanish
ms.author: manishku
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/17/2019
ms.openlocfilehash: 905a0c77919e448a7c3387ca8fa35a8150780534
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/05/2019
ms.locfileid: "73608153"
---
# <a name="how-a-reservation-discount-is-applied-to-azure-database-for-mysql"></a>Come viene applicato uno sconto per la prenotazione a Database di Azure per MySQL

Dopo avere acquistato capacità riservata per Database di Azure per MySQL, lo sconto per la prenotazione viene automaticamente applicato ai server MySQL che corrispondono agli attributi e alla quantità della prenotazione. Una prenotazione copre solo i costi di calcolo di Database di Azure per MySQL. Per l'archiviazione e la rete vengono addebitate le tariffe normali. 

## <a name="how-reservation-discount-is-applied"></a>Come viene applicato lo sconto per la prenotazione

Uno sconto per la prenotazione si basa sul principio ***use-it-or-lose-it***, ovvero se non viene usato va perso. Quindi, se non si hanno risorse corrispondenti per un'ora, si perderà la quantità di prenotazione per quell'ora. Non è possibile riportare le ore prenotate inutilizzate.</br>

Quando si arresta una risorsa, lo sconto per la prenotazione si applica automaticamente a un'altra risorsa corrispondente nell'ambito specificato. Se non si trovano risorse idonee nell'ambito specificato, le ore prenotate vanno perse.

## <a name="discount-applied-to-azure-database-for-mysql"></a>Sconto applicato a Database di Azure per MySQL

Lo sconto per la capacità riservata di Database di Azure per MySQL viene applicato all'esecuzione di server MySQL su base oraria. La prenotazione acquistata viene confrontata con l'utilizzo delle risorse di calcolo generato dai server di Database di Azure per MySQL in esecuzione. Per i server MySQL che non vengono eseguiti per un'ora intera, la prenotazione viene automaticamente applicata all'altra istanza di Database di Azure per MySQL che corrisponde agli attributi della prenotazione. Lo sconto può essere applicato a server di Database di Azure per MySQL che vengono eseguiti simultaneamente. Se nessun server MySQL in esecuzione per l'ora intera corrisponde agli attributi della prenotazione, non è possibile ottenere il vantaggio completo dello sconto per la prenotazione per tale ora.

Gli esempi seguenti mostrano come viene applicato lo sconto per la capacità riservata di Database di Azure per MySQL a seconda del numero di core acquistati e dei tempi di esecuzione.

* **Esempio 1**: si acquista una capacità riservata per Database di Azure per MySQL per 8 vCore. Se si esegue un server di Database di Azure per MySQL da 16 vCore che corrisponde al resto degli attributi della prenotazione, viene addebitato il prezzo a consumo per 8 vCore dell'utilizzo di risorse di calcolo del server MySQL e si ottiene lo sconto per la prenotazione per un'ora di utilizzo di risorse di calcolo del server MySQL da 8 vCore.</br>

Per gli altri esempi, si presuppone che la capacità riservata di Database di Azure per MySQL acquistata sia per un'istanza di Database di Azure per MySQL da 16 vCore e che gli altri attributi della prenotazione corrispondano ai server MySQL in esecuzione.

* **Esempio 2:** si eseguono due server di Database di Azure per MySQL ciascuno con 8 vCore per un'ora. Lo sconto per la prenotazione da 16 vCore viene applicato per l'utilizzo di risorse di calcolo per i due server di Database di Azure per MySQL da 8 vCore.

* **Esempio 3**: si esegue un server di Database di Azure per MySQL da 16 vCore dalle 13 alle 13.30. Si esegue un altro server di Database di Azure per MySQL da 16 vCore dalle 13.30 alle 14. A entrambi i database viene applicato lo sconto sulla prenotazione.

* **Esempio 4**: si esegue un server di Database di Azure per MySQL da 16 vCore dalle 13 alle 13.45. Si esegue un altro server di Database di Azure per MySQL da 16 vCore dalle 13.30 alle 14. Viene addebitata la tariffa con pagamento in base al consumo per la sovrapposizione di 15 minuti. Lo sconto sulla prenotazione viene applicato all'utilizzo delle risorse di calcolo per il resto del tempo.

Per informazioni sull'applicazione delle prenotazioni di Azure nei report sull'utilizzo per la fatturazione, vedere [Informazioni sull'utilizzo delle prenotazioni di Azure](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage-ea).

## <a name="need-help-contact-us"></a>Richiesta di assistenza Contatti
In caso di domande o per assistenza, [creare una richiesta di supporto](https://go.microsoft.com/fwlink/?linkid=2083458).
