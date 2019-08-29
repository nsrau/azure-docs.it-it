---
title: Ridimensionamento di SAP HANA in Azure (istanze Large) | Microsoft Docs
description: Ridimensionamento di SAP HANA in Azure (istanze Large).
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/04/2018
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f2540a0f9ea702ff620ccd4b68208cbb2e70aafd
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70101135"
---
# <a name="sizing"></a>Ridimensionamento

Il ridimensionamento per le istanze Large di HANA non è diverso dal ridimensionamento per HANA in generale. Per i sistemi esistenti e distribuiti che devono essere spostati da altri RDBMS ad HANA, SAP fornisce una serie di report che vengono eseguiti sui sistemi SAP esistenti. Se il database viene spostato in HANA, questi report controllano i dati e calcolano i requisiti di memoria per l'istanza di HANA. Per altre informazioni su come eseguire i report e su come ottenere le patch o le versioni più recenti, leggere le note SAP seguenti:

- [SAP Note #1793345 - Sizing for SAP Suite on HANA](https://launchpad.support.sap.com/#/notes/1793345) (Nota SAP 1793345: ridimensionamento della suite SAP in HANA)
- [SAP Note #1872170 - Suite on HANA and S/4 HANA sizing report](https://launchpad.support.sap.com/#/notes/1872170) (Nota SAP 1872170: report sul ridimensionamento della suite in HANA e S/4 HANA)
- [Note SAP #2121330-domande frequenti: SAP BW nel report di dimensionamento HANA](https://launchpad.support.sap.com/#/notes/2121330)
- [SAP Note #1736976 - Sizing report for BW on HANA](https://launchpad.support.sap.com/#/notes/1736976) (Nota SAP 1736976: report sul ridimensionamento per BW in HANA)
- [SAP Note #2296290 - New sizing report for BW on HANA](https://launchpad.support.sap.com/#/notes/2296290) (Nota SAP 2296290: nuovo report sul ridimensionamento per BW in HANA)

Per le implementazioni "vergini", è disponibile SAP Quick Sizer per calcolare i requisiti di memoria dell'implementazione del software SAP in HANA.

I requisiti di memoria per HANA aumentano in base al volume dei dati. È quindi opportuno conoscere l'utilizzo attuale della memoria per poter prevedere quello futuro. In base ai requisiti di memoria, è possibile associare la propria richiesta a uno degli SKU delle istanze Large di HANA.

**Passaggi successivi**
- Fare riferimento a [Richieste di Onboarding](hana-onboarding-requirements.md)