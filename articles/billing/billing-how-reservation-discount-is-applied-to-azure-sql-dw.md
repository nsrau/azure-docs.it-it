---
title: Come vengono applicati sconti prenotazione Azure SQL Data Warehouse | Microsoft Docs
description: Informazioni su come vengono applicati sconti prenotazione Azure SQL Data Warehouse per consentire un risparmio di denaro.
services: billing
author: yashesvi
manager: yashar
ms.service: billing
ms.topic: conceptual
ms.date: 04/13/2019
ms.author: banders
ms.openlocfilehash: 10e19377d31489cd19465fe6171ffb530bd58c28
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60918399"
---
# <a name="how-reservation-discounts-apply-to-azure-sql-data-warehouse"></a>Come vengono applicati sconti prenotazione Azure SQL Data Warehouse

Dopo aver acquistato capacità riservata di Azure SQL Data Warehouse, lo sconto della prenotazione viene applicato automaticamente ai data warehouse esistenti in tale area. Lo sconto di prenotazione si applica all'utilizzo emesso dal tassametro Dwu SQL Data Warehouse. Archiviazione e rete vengono applicate tariffe con pagamento a consumo.

## <a name="reservation-discount-application"></a>Applicazione dello sconto di prenotazione

Lo sconto relativo alla capacità riservata SQL Data Warehouse viene applicato all'esecuzione di data warehouse su base oraria. Se non si dispone di un warehouse distribuito per un'ora, la capacità riservata è sprecata per quell'ora. Non si applica.

Dopo l'acquisto, la prenotazione che acquista corrisponde all'utilizzo di SQL Data Warehouse generato tramite l'esecuzione di data warehouse in qualsiasi punto nel tempo. Se si arresta un data warehouse, quindi gli sconti di prenotazione applicano automaticamente alle eventuali altri warehouse corrispondente.

Per data warehouse che non vengono eseguiti per un'ora completa, la prenotazione viene applicata automaticamente alle altre istanze corrispondenti in quell'ora.

## <a name="discount-examples"></a>Esempi di sconto

Gli esempi seguenti mostrano come viene applicato lo sconto di capacità riservata SQL Data Warehouse, a seconda delle distribuzioni.

- **Esempio 1**: Si acquista 5 unità di capacità di 100 Dwu sono riservati. Si esegue un'istanza DW1500c SQL Data Warehouse per un'ora. In questo caso, l'uso viene calcolato per 15 unità di 100 Dwu utilizzo. Lo sconto di prenotazione si applica a 5 unità che è stato usato. Vengono addebitati in base alle tariffe con pagamento a consumo per i rimanenti 10 unità di 100 Dwu utilizzo usato.

- **Esempio 2:** Si acquista 5 unità di capacità di 100 Dwu sono riservati. Eseguire due istanze DW100c SQL Data Warehouse per un'ora. In questo caso, vengono generati due eventi di utilizzo per 1 unità di 100 Dwu utilizzo. Entrambi gli eventi di utilizzo ottenere sconti capacità riservata. Le restanti 3 unità di 100 Dwu riservata capacità sono inutilizzata e non si applica per utilizzi futuri.

- **Esempio 3**: Acquistare 1 unità di capacità di 100 Dwu sono riservati. Eseguire due istanze DW100c SQL Data Warehouse. Ogni esecuzione per 30 minuti. In questo caso, entrambi gli eventi di utilizzo ottenere sconti capacità riservata. Nessun utilizzo viene addebitato in base alle tariffe con pagamento a consumo.

## <a name="need-help-contact-us"></a>Richiesta di assistenza Contatti

- In caso di domande o per assistenza, [creare una richiesta di supporto](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle prenotazioni di Azure, vedere gli articoli seguenti:

- [Informazioni sulle prenotazioni di Azure](billing-save-compute-costs-reservations.md)
- [Visualizzare le transazioni di prenotazione](billing-view-reservations.md)
- [Ottenere le transazioni di prenotazione e l'utilizzo tramite API](billing-reservation-apis.md)
- [Gestire le prenotazioni](billing-manage-reserved-vm-instance.md)
