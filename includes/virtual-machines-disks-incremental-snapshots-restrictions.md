---
title: includere il file
description: File di inclusione
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/05/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 39a013f5f4b587137366147ade77f0be1b353c4c
ms.sourcegitcommit: 67bddb15f90fb7e845ca739d16ad568cbc368c06
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82204632"
---
- Non è attualmente possibile spostare gli snapshot incrementali tra le sottoscrizioni.
- Attualmente, è possibile generare solo URI SAS di un massimo di cinque snapshot di una particolare famiglia di snapshot in un determinato momento.
- Non è possibile creare uno snapshot incrementale per un disco specifico al di fuori della sottoscrizione del disco.
- È possibile creare fino a sette snapshot incrementali per ogni disco ogni cinque minuti.
- Per un singolo disco è possibile creare un totale di 200 snapshot incrementali.
- Non è possibile ottenere le modifiche tra gli snapshot effettuati prima e dopo la modifica delle dimensioni del disco padre tra i limiti di 4 TB. È necessario scaricare di nuovo la copia completa dello snapshot creato dopo il ridimensionamento. Successivamente, è possibile ottenere le modifiche tra gli snapshot creati dopo il ridimensionamento tra i limiti di 4 TB. 
