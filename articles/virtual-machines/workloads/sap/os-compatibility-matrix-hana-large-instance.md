---
title: Matrice di compatibilità del sistema operativo per SAP HANA (istanze large) | Microsoft Docs
description: La matrice di compatibilità rappresenta la compatibilità di versioni diverse del sistema operativo con diversi tipi di hardware (istanze large)
services: virtual-machines-linux
documentationcenter: ''
author: sasarava
manager: hrushib
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/03/2020
ms.author: sasarava
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: aa19433ef6446932da3509694ccccd08538b964f
ms.sourcegitcommit: bc792d0525d83f00d2329bea054ac45b2495315d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/06/2020
ms.locfileid: "78675648"
---
# <a name="compatible-operating-systems-for-hana-large-instances"></a>Sistemi operativi compatibili per le istanze large di HANA

## <a name="hana-large-instance-type-i"></a>Tipo di istanza large di HANA I     
  | Sistema operativo | Disponibilità        | SKU                                                          |
  |------------------|---------------------|---------------------------------------------------------------|
  | SLES 12 SP2      | Non più disponibile | S72, S72m, S96, S144, S144m, S192, S192m, S192xm              |
  | SLES 12 SP3      | Disponibile           | S72, S72m, S96, S144, S144m, S192, S192m, S192xm              |
  | SLES 12 SP4      | Disponibile           | S72, S72m, S96, S144, S144m, S192, S192m, S192xm, S224, S224m |
  
### <a name="persistent-memory-skus"></a>SKU di memoria permanenti
  | Sistema operativo | Disponibilità | SKU                             |
  |------------------|--------------|----------------------------------|
  | SLES 12 SP4      | Disponibile    | S224oo, S224om, S224ooo, S224oom |
  
## <a name="hana-large-instance-type-ii"></a>Tipo di istanza large di HANA II     
  |  Sistema operativo       | Disponibilità        | SKU                                                              |
  |-------------------------|---------------------|-------------------------------------------------------------------|
  | SLES 12 SP2             | Non più disponibile | S384, S384m, S384xm, S384xxm, S576m, S576xm, S768m, S768xm, S960m |
  | SLES 12 SP3             | Disponibile           | S384, S384m, S384xm, S384xxm, S576m, S576xm, S768m, S768xm, S960m |
  
## <a name="related-documents"></a>Documenti correlati

- Per ulteriori informazioni sugli [SKU disponibili](hana-available-skus.md)
- Per conoscere [l'aggiornamento del sistema operativo](os-upgrade-hana-large-instance.md)
  

  
  
