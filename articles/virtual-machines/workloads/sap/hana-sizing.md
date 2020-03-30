---
title: Ridimensionamento di SAP HANA in Azure (istanze Large) | Microsoft Docs
description: Ridimensionamento di SAP HANA in Azure (istanze Large).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/04/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 404f8318816edcc2cfd1c50ca42304ff6ec93039
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77616899"
---
# <a name="sizing"></a>Ridimensionamento

Il ridimensionamento per le istanze Large di HANA non è diverso dal ridimensionamento per HANA in generale. Per i sistemi esistenti e distribuiti che devono essere spostati da altri RDBMS ad HANA, SAP fornisce una serie di report che vengono eseguiti sui sistemi SAP esistenti. Se il database viene spostato in HANA, questi report controllano i dati e calcolano i requisiti di memoria per l'istanza di HANA. Per altre informazioni su come eseguire i report e su come ottenere le patch o le versioni più recenti, leggere le note SAP seguenti:

- [SAP Note #1793345 - Dimensionamento per SAP Suite su HANA](https://launchpad.support.sap.com/#/notes/1793345)
- [SAP Note #1872170 - Suite nel report di dimensionamento HANA e S/4 HANA](https://launchpad.support.sap.com/#/notes/1872170)
- [SAP Note #2121330 - Domande frequenti: SAP BW nel report di dimensionamento HANA](https://launchpad.support.sap.com/#/notes/2121330)
- [SAP Note #1736976 - Report di dimensionamento per BW su HANA](https://launchpad.support.sap.com/#/notes/1736976)
- [SAP Note #2296290 - Nuovo report di dimensionamento per BW su HANA](https://launchpad.support.sap.com/#/notes/2296290)

Per le implementazioni "vergini", è disponibile SAP Quick Sizer per calcolare i requisiti di memoria dell'implementazione del software SAP in HANA.

I requisiti di memoria per HANA aumentano in base al volume dei dati. È quindi opportuno conoscere l'utilizzo attuale della memoria per poter prevedere quello futuro. In base ai requisiti di memoria, è possibile associare la propria richiesta a uno degli SKU delle istanze Large di HANA.

**Passaggi successivi**
- Fare riferimento a [Richieste di Onboarding](hana-onboarding-requirements.md)