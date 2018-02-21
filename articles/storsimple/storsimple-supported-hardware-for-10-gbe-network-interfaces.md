---
title: Interfacce dell'hardware per StorSimple 10 GbE | Microsoft Docs
description: Vengono descritti i ricetrasmettitori collegabili small form factor (SFP), i cavi e gli switch supportati per le interfacce di rete 10 GbE sul dispositivo StorSimple.
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: 
ms.assetid: df8d40c7-f5ad-4f84-93eb-779fbd5f7243
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 09/21/2016
ms.author: alkohli
ms.openlocfilehash: db03b3cd668bf8e35913872ac4225de6d4d3edd1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="supported-hardware-for-the-10-gbe-network-interfaces-on-your-storsimple-device"></a>Hardware supportato per le interfacce di rete 10 GbE sul dispositivo StorSimple
## <a name="overview"></a>Panoramica
In questo articolo vengono fornite informazioni sull'hardware supplementare che funziona con il dispositivo StorSimple di Microsoft Azure.

## <a name="list-of-devices-tested-by-microsoft"></a>Elenco dei dispositivi testati da Microsoft
Microsoft ha verificato i seguenti ricetrasmettitori (SFP) collegabili small form factor, cavi e switch per assicurarsi che funzionino in modo ottimale con i dispositivi. (Le seguenti tabelle saranno aggiornate non appena verificato il nuovo hardware).

### <a name="sfp-transceivers"></a>Ricetrasmettitori SFP+ 
| Casa automobilistica | Modello |
| --- | --- |
| Cisco |SFP-10G-SR |

### <a name="cables"></a>Cavi
| Numero di serie | Assicurarsi | Modello |
| --- | --- | --- |
| 1. |Cisco |SFP-H10GB-CU1M |
| 2. |Cisco |SFP-H10GB-CU2M |
| 3. |Cisco |SFP-H10GB-CU3M |
| 4. |Tripp-Lite |N820-05M (OM3) |

### <a name="switches"></a>Switch
| Numero di serie | Assicurarsi | Modello |
| --- | --- | --- |
| 1. |Cisco |N3K-C3172PQ-10GE |
| 2. |Cisco |N3K-C3048-ZM-F |
| 3. |Cisco |N5K-C5596UP-FA |

## <a name="list-of-devices-tested-in-the-field"></a>Elenco dei dispositivi testati nel campo
Questa sezione contiene l'elenco dei dispositivi che sono stati distribuiti correttamente nel campo dai clienti di StorSimple. Non sono stati verificati da Microsoft, ma è probabile che funzionino con il dispositivo StorSimple.

| Parametro | Valore |
| --- | --- |
| Marca switch |Juniper |
| Modello switch |ex4550-32F |
| Versione sistema operativo switch |JunOS 12.3R9.4 |
| Modello pannello |Porte caricate (PIC 0) |
| Marca ricetrasmettitore |Juniper |
| Modello ricetrasmettitore |Codice 740-021308  <br></br> Codice 740-030658 |
| Versione firmware ricetrasmettitore |Rev 01 Versione 0.0 (segnalato) |
| Modello cavo |Ponticello duplex LC/LC 50/125µ, OM3, LSZH |
| Modello StorSimple |8600 |
| Versione software StorSimple |6.3.9600.17491 |

## <a name="list-of-devices-tested-by-oem-provider-mellanox"></a>Elenco dei dispositivi testati da provider OEM (Mellanox)
Mellanox ha verificato i seguenti ricetrasmettitori (SFP) collegabili small form factor, cavi e switch per assicurarsi che funzionino in modo ottimale con le interfacce di rete Mellanox come le interfacce di rete 10 GbE sul dispositivo StorSimple.

### <a name="cables-and-modules-supported-by-mellanox"></a>Cavi e moduli supportati da Mellanox
Nella tabella seguente sono elencati i cavi e i moduli supportati da Mellanox. Non sono stati verificati da Microsoft, ma è probabile che funzionino con il dispositivo StorSimple.

| Numero di serie | speed | Modello | DESCRIZIONE | Assicurarsi |
| --- | --- | --- | --- | --- |
| 1. |10 GbE |CAB-SFP-SFP-1M |Cavo in rame passivo SFP+ 10 Gb/s 1 m |Arista |
| 2. |10 GbE |CAB-SFP-SFP-2M |Cavo in rame passivo SFP+ 10 Gb/s 2 m |Arista |
| 3. |10 GbE |CAB-SFP-SFP-3M |Cavo in rame passivo SFP+ 10 Gb/s 3 m |Arista |
| 4. |10 GbE |CAB-SFP-SFP-5M |Cavo in rame passivo SFP+ 10 Gb/s 5 m |Arista |
| 5. |10 GbE |Cisco SFP-H10GBCU1M |Cavo SFP+ Cisco |Cisco |
| 6. |10 GbE |Cisco SFP-H10GBCU3M |Cavo SFP+ Cisco |Cisco |
| 7. |10 GbE |Cisco SFP-H10GBCU5M |Cavo SFP+ Cisco |Cisco |
| 8. |10 GbE |HP J9281B X242 10G |Cavo in rame a collegamento diretto da SFP+ a SFP+ 1 m |HP |
| 9. |10 GbE |455883-B21 HP BLc |10 Gb SR SFP+ Opt |HP |
| 10. |10 GbE |455886-B21 HP BLc |10 Gb LR SFP+ Opt |HP |
| 11. |10 GbE |487649-B21 HP BLc |Cavo in rame SFP+ 10 GbE 0,5 m |HP |
| 12. |10 GbE |487652-B21 HP BLc |Cavo in rame SFP+ 10 GbE 1 m |HP |
| 13. |10 GbE |487655-B21 HP BLc |Cavo in rame SFP+ 10 GbE 3 m |HP |
| 14. |10 GbE |487658-B21 HP BLc |Cavo in rame SFP+ 10 GbE 7 m |HP |
| 15. |10 GbE |537963-B21 HP BLc |Cavo in rame SFP+ 10 GbE 5 m |HP |
| 16. |10 GbE |AP784A HP |Cavo SFP+ in rame passivo serie C 3 m |HP |
| 17. |10 GbE |AP785A HP |Cavo SFP+ in rame passivo serie C 5 m |HP |
| 18. |10 GbE |AP818A HP |Cavo SFP+ in rame attivo serie B 1 m |HP |
| 19. |10 GbE |AP819A HP |Cavo SFP+ in rame attivo serie B 3 m |HP |
| 20. |10 GbE |J9150A HP |Ricetrasmettitore LC SR SFP+ X132 10 G  |HP |
| 21. |10 GbE |J9151A HP |Ricetrasmettitore LC SR SFP+ X132 10 G |HP |
| 22. |10 GbE |J9283B HP |Cavo DAC X242 10G SFP+ SFP+ 3 m |HP |
| 23. |10 GbE |J9285B HP |Cavo DAC X242 10G SFP+ SFP+ 7 m |HP |
| 24. |10 GbE |JD095B HP |Cavo DAC X240 10G SFP+ SFP+ 0,65 m |HP |
| 25. |10 GbE |JD096B HP |Cavo DAC X240 10G SFP+ SFP+ 1,2 m  |HP |
| 26. |10 GbE |JD097B HP |Cavo DAD X240 10G SFP+ SFP+ 3 m  |HP |
| 27. |10 GbE |MAM1Q00A-QSA Mellanox |Da QSFP a SFP + adattatore |Mellanox Technologies |
| 28. |10 GbE |MC2309124-006 Mt |1 cavo in rame passivo da SFP+ a QSFP da 10 Gb/s 24 awg 7 m |Mellanox Technologies |
| 29. |10 GbE |MC2309124-007 Mt |1 cavo in rame passivo da SFP+ a QSFP da 10 Gb/s 24 awg 7 m |Mellanox Technologies |
| 30. |10 GbE |MC2309130-003 Mt |1 cavo in rame passivo da SFP+ a QSFP da 10 Gb/s 30 awg 3 m |Mellanox Technologies |
| 31. |10 GbE |MC2309130-00A Mt |1 cavo in rame passivo da SFP+ a QSFP da 10 Gb/s 30 awg 0,5 m |Mellanox Technologies |
| 32. |10 GbE |MC3309124-005 Mt |1 cavo in rame passivo SFP+ da 10 Gb/s 24 awg 5 m |Mellanox Technologies |
| 33. |10 GbE |MC3309124-007 Mt |1 cavo in rame passivo SFP+ da 10 Gb/s 24 awg 7 m |Mellanox Technologies |
| 34. |10 GbE |MC3309130-003 Mt |1 cavo in rame passivo SFP+ da 10 Gb/s 30 awg 3 m |Mellanox Technologies |
| 35. |10 GbE |MC3309130-00A Mt |1 cavo in rame passivo SFP+ da 10 Gb/s 30 awg 0,5 m |Mellanox Technologies |

### <a name="switches-supported-by-mellanox"></a>Switch supportati da Mellanox
Nella tabella seguente sono elencati gli switch supportati da Mellanox. Non sono stati verificati da Microsoft, ma è probabile che funzionino con il dispositivo StorSimple.

| Numero di serie | speed | Modello | DESCRIZIONE | Assicurarsi |
| --- | --- | --- | --- | --- |
| 1. |10GbE |516733-B21 |Switch blade HP ProCurve 6120XG 10GbE Ethernet |HP |
| 2. |10GbE |538113-B21 |Modulo pass-through (PTM) HP 10GbE |HP |
| 3. |10GbE |EN4093 |Modulo switch scalabile IBM PureFlex System Fabric EN4093 10 Gigabit |IBM |
| 4. |1GbE |3020 |Switch blade Cisco Catalyst 3020 1GbE |Cisco |
| 5. |1GbE |3020 X |Switch blade Cisco Catalyst 3020X 1GbE |Cisco |
| 6. |1GbE |438030-B21 |Modulo switch HP 1GbEe - Switch blade GbE2c Layer 2/3 Ethernet |HP |
| 7. |1GbE |6120G |Switch blade HP ProCurve 6120G/XG 1GbE |HP |

## <a name="next-steps"></a>Passaggi successivi
[Ulteriori informazioni sui componenti hardware di StorSimple](storsimple-monitor-hardware-status.md)

