---
title: Matrice di compatibilità del sistema operativo per SAP HANA in istanze Large | Microsoft Docs
description: La matrice di compatibilità illustra la compatibilità delle diverse versioni del sistema operativo con i diversi tipi di hardware (istanze Large)
services: virtual-machines-linux
documentationcenter: ''
author: sasarava
manager: hrushib
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 08/21/2020
ms.author: sasarava
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 18e97aea5c556c4f8e6ff1fb1b91a82da6de1b59
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/22/2020
ms.locfileid: "88749354"
---
# <a name="compatible-operating-systems-for-hana-large-instances"></a>Sistemi operativi compatibili per HANA in istanze Large

## <a name="hana-large-instance-type-i"></a>HANA in istanze Large tipo I     
  | Sistema operativo | Disponibilità        | SKU                                                          |
  |------------------|---------------------|---------------------------------------------------------------|
  | SLES 12 SP2      | Non più disponibile | S72, S72m, S96, S144, S144m, S192, S192m, S192xm              |
  | SLES 12 SP3      | Disponibile           | S72, S72m, S96, S144, S144m, S192, S192m, S192xm              |
  | SLES 12 SP4      | Disponibile           | S72, S72m, S96, S144, S144m, S192, S192m, S192xm, S224, S224m |
  | SLES 12 SP5      | Disponibile           | S72, S72m, S96, S144, S144m, S192, S192m, S192xm, S224, S224m |
  | SLES 15 SP1      | Disponibile           | S72, S72m, S96, S144, S144m, S192, S192m, S192xm, S224, S224m |
  | RHEL 7.6         | Disponibile           | S72, S72m, S96, S144, S144m, S192, S192m, S192xm, S224, S224m |

  
### <a name="persistent-memory-skus"></a>SKU di memoria persistente
  | Sistema operativo | Disponibilità | SKU                             |
  |------------------|--------------|----------------------------------|
  | SLES 12 SP4      | Disponibile    | S224oo, S224om, S224ooo, S224oom |
  
## <a name="hana-large-instance-type-ii"></a>HANA in istanze Large tipo II     
  |  Sistema operativo       | Disponibilità        | SKU                                                              |
  |-------------------------|---------------------|-------------------------------------------------------------------|
  | SLES 12 SP2             | Non più disponibile | S384, S384m, S384xm, S384xxm, S576m, S576xm, S768m, S768xm, S960m |
  | SLES 12 SP3             | Disponibile           | S384, S384m, S384xm, S384xxm, S576m, S576xm, S768m, S768xm, S960m |
  | SLES 12 SP4             | Disponibile           | S384, S384m, S384xm, S384xxm, S576m, S576xm, S768m, S768xm, S960m |
  | SLES 12 SP5             | Disponibile           | S384, S384m, S384xm, S384xxm, S576m, S576xm, S768m, S768xm, S896m, S960m |
  | SLES 15 SP1             | Disponibile           | S384, S384m, S384xm, S384xxm, S576m, S576xm, S768m, S768xm, S896m, S960m |
  
## <a name="related-documents"></a>Documenti correlati

- Altre informazioni sugli [SKU disponibili](hana-available-skus.md)
- Informazioni sull'[aggiornamento del sistema operativo](os-upgrade-hana-large-instance.md)
  

  
  
