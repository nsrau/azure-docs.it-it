---
title: Sostituzione di componenti hardware StorSimple | Microsoft Docs
description: "Viene descritto come sostituire in modo sicuro i PCM, la batteria, i moduli controller, i controller EBOD, le unità disco e chassis di un dispositivo StorSimple."
services: storsimple
documentationcenter: 
author: alkohli
manager: timlt
editor: 
ms.assetid: e8087ba7-0b66-4f59-8988-e53aad52ee21
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 02/27/2017
ms.author: alkohli
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: f2d197264f066a23468db80686444bd24f6d796f
ms.openlocfilehash: ae37f49916445a9486457af61aa9bf8bc1d7eb87
ms.lasthandoff: 02/28/2017


---
# <a name="replace-a-hardware-component-on-your-storsimple-8000-series-device"></a>Sostituire un componente hardware sul dispositivo StorSimple serie 8000

## <a name="overview"></a>Panoramica
Le esercitazioni di sostituzione dei componenti hardware descrivono i componenti hardware del dispositivo Microsoft Azure StorSimple serie 8000 e i passaggi necessari per rimuovere e sostituire i componenti. In questo articolo vengono descritte le icone di sicurezza, vengono forniti dei collegamenti alle esercitazioni dettagliate e vengono elencati i componenti che possono essere sostituiti.

> [!IMPORTANT]
> Prima di tentare di rimuovere o sostituire qualsiasi componente di StorSimple, leggere le [convenzioni di sicurezza](#safety-icon-conventions) e altre [precauzioni di sicurezza](storsimple-safety.md).
> 
> 

### <a name="safety-icon-conventions"></a>Convenzioni di sicurezza
Nella tabella seguente vengono descritte le icone di sicurezza utilizzate in queste esercitazioni. Prestare particolare attenzione a queste icone di sicurezza quando si eseguono i passaggi per rimuovere e sostituire i componenti del dispositivo.

| Icona | Text | Informazioni aggiuntive |
|:--- |:--- |:--- |
| ![Icona di avviso](./media/storsimple-hardware-component-replacement/Warning.png) |**PERICOLO!** |Indica una situazione di pericolo che, se non viene evitato, comporterà morte o gravi ferite. Questa parola deve essere limitata a situazioni più estreme. |
| ![Icona di avviso](./media/storsimple-hardware-component-replacement/Warning.png) |**AVVISO!** |Indica una situazione di pericolo che, se non viene evitata, può comportare morte o gravi ferite. |
| ![Icona di attenzione](./media/storsimple-hardware-component-replacement/Caution.png) |**ATTENZIONE!** |Indica una situazione di pericolo che, se non viene evitato, comporterà ferite lievi o limitate. |
| ![Icona di notifica](./media/storsimple-hardware-component-replacement/NoticeIcon.png) |**NOTIFICA:** |Indica le informazioni considerate importanti, ma non correlate al rischio. |
| ![Icona di scossa elettrica](./media/storsimple-hardware-component-replacement/Electric.png) |**Pericolo di scosse elettriche** |Indica alta tensione. |
| ![Icona peso elevato](./media/storsimple-hardware-component-replacement/Weight.png) |**Pesante** | |
| ![Nessuna icona di parti riparabili dall'utente](./media/storsimple-hardware-component-replacement/NoUserServiceableParts.png) |**Nessuna parte riparabile dall'utente** |Non accedere a meno che non si sia stati adeguatamente formati. |
| ![Icona di istruzioni di lettura](./media/storsimple-hardware-component-replacement/ReadInstructions.png) |**Leggere prima tutte le istruzioni** | |
| ![Icona suggerimento di pericolo](./media/storsimple-hardware-component-replacement/TipHazard.png) |**Suggerimento di pericolo** | |

### <a name="before-you-begin"></a>Prima di iniziare
Acquisire familiarità con le informazioni di sicurezza relative al dispositivo e alle icone di sicurezza utilizzate in questa esercitazione. Andare a [Installazione sicura e funzionamento del dispositivo StorSimple](storsimple-safety.md) per informazioni più dettagliate. Rivedere le [precauzioni di sicurezza](storsimple-safety.md#handling-precautions) prima di gestire il dispositivo StorSimple. 

Prima di tentare di sostituire un componente, considerare le seguenti informazioni.

![Warning Icon](./media/storsimple-hardware-component-replacement/Warning.png) ![Electrical Shock Icon](./media/storsimple-hardware-component-replacement/Electric.png) **AVVISO!** 

* Collegarsi a terra correttamente usando un elettrostatica o passepartout antistatiche durante la gestione di moduli e i componenti del dispositivo StorSimple.
* Non toccare nessun circuito. Utilizzare maniglie e guide specificate durante la gestione di componenti che possono avere circuiti esposti..

![Warning Icon](./media/storsimple-hardware-component-replacement/Warning.png) ![Notice Icon](./media/storsimple-hardware-component-replacement/NoticeIcon.png) **NOTIFICA:**

Quando si sostituisce un modulo, **non lasciare MAI un alloggiamento vuoto nella sporgenza dell’enclosure**. Procurarsi un ricambio o un modulo vuoto prima di rimuovere la parte del problema.

## <a name="hardware-component-replacement-procedures"></a>Procedure di sostituzione di componenti hardware
Il dispositivo Microsoft Azure StorSimple serie 8000 è costituito da diversi moduli plug-in negli chassis primari e/o EBOD. Il modello 8100 ha un solo chassis principale, mentre il 8600 è un dispositivo a chassis doppio con un chassis principale e uno EBOD.

Nelle tabelle seguenti vengono riepilogati i componenti hardware principali nel dispositivo. Fare clic sul collegamento nella colonna della **Procedura di sostituzione** per passare all'esercitazione associata.

| Componenti | # Presente | Modulo plug-in? | Procedura di sostituzione |
|:--- |:--- |:--- |:--- |
| Chassis |1 |No |[Sostituire lo chassis sul dispositivo StorSimple](storsimple-chassis-replacement.md) |
| Controller primari |2 |Sì |[Sostituire un modulo controller nel dispositivo StorSimple](storsimple-controller-replacement.md) |
| Power and Cooling Modules (PCM) da&764;W |2 |Sì |[Sostituire un PCM sul dispositivo StorSimple](storsimple-power-cooling-module-replacement.md) |
| Batteria di backup |2 |Sì |[Sostituire il modulo batteria di backup nel dispositivo StorSimple](storsimple-battery-replacement.md) |
| Unità disco |12 |Sì |[Sostituire un'unità disco del dispositivo StorSimple](storsimple-disk-drive-replacement.md) |

**Tabella 1** Componenti Hardware nel chassis principale

Lo chassis principale e lo chassis EBOD sono diversi nei moduli I/O. Inoltre, i PCM hanno diverse potenze elettriche. I PCM nello chassis principale sono di 764 W, mentre quelli nello chassis EBOD sono di 580 W. I PCM nello chassis principale contengono anche un modulo batteria di backup.

| Componenti | # Presente | Modulo plug-in? | Procedura di sostituzione |
|:--- |:--- |:--- |:--- |
| Chassis |1 |No |[Sostituire lo chassis sul dispositivo StorSimple](storsimple-chassis-replacement.md) |
| Controller EBOD |2 |Sì |[Sostituire un controller EBOD sul dispositivo StorSimple](storsimple-ebod-controller-replacement.md) |
| Power and Cooling Modules (PCM) da&580;W |2 |Sì |[Sostituire un PCM sul dispositivo StorSimple](storsimple-power-cooling-module-replacement.md) |
| Unità disco |12 |Sì |[Sostituire un'unità disco del dispositivo StorSimple](storsimple-disk-drive-replacement.md) |

**Tabella 2** componenti Hardware nell'enclosure EBOD

I moduli plug-in nel dispositivo sono evidenziati nei seguenti diagrammi anteriori e posteriori. È possibile utilizzare questi diagrammi per determinare la posizione dei diversi moduli plug-in se è necessaria una sostituzione. Il diagramma anteriore mostra le unità disco e i diagrammi posteriori dello chassis EBOD e dello chassis principale mostrano i moduli plug-in.

![Piano anteriore del dispositivo con unità disco](./media/storsimple-hardware-component-replacement/IC741028.png)

**Figura 1** Parte anteriore del dispositivo

| Etichetta | Description |
|:--- |:--- |
| 0 - 11 |Unità di dischi (totale pari a 12) |

Sia lo chassis principale sia quello EBOD hanno moduli unità carrier. Lo chassis ospita dodici unità disco da 3,5" disposte in un formato 3 da 4.

![Backplane dei moduli dello chassis principale del dispositivo](./media/storsimple-hardware-component-replacement/IC740994.png)

**Figura 2** Pannello posteriore dello chassis principale

| Etichetta | Descrizione |
|:--- |:--- |
| 1 |PCM 0 |
| 2 |PCM 1 |
| 3 |Controller 0 |
| 4 |Controller 1 |

![Piano posteriore dei moduli plug-in dell'enclosure EBOD del dispositivo](./media/storsimple-hardware-component-replacement/IC769599.png)

**Figura 3** pannello posteriore dell’enclosure EBOD

| Etichetta | Descrizione |
|:--- |:--- |
| 1 |PCM 0 |
| 2 |PCM 1 |
| 3 |Controller 0 EBOD |
| 4 |Controller 1 EBOD |

## <a name="field-replaceable-units"></a>Unità sostituibile sul campo 
Le seguenti unità sostituibili sul campo (FRU) sono disponibili per il dispositivo StorSimple:

* Chassis (incluso il pannello operativo integrato)
* PCM con AC da&764; W
* PCM con AC da&580; W
* Unità disco rigido con modulo unità carrier
* Modulo controller
* Modulo controller EBOD
* Modulo batteria di backup
* Kit per il montaggio su rack

[Contattare il supporto Microsoft](storsimple-contact-microsoft-support.md) per ordinare una di queste unità sostitutive.

## <a name="next-steps"></a>Passaggi successivi
Rivedere tutte le [informazioni sulla sicurezza](storsimple-safety.md) prima di tentare di sostituire un componente hardware StorSimple.


