---
title: Disponibilità elevata - HSM dedicato di Azure | Microsoft Docs
description: Esempio di disponibilità elevata con HSM dedicato di Azure e considerazioni di base
services: dedicated-hsm
author: barclayn
manager: barbkess
ms.custom: mvc, seodec18
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/27/2019
ms.author: barclayn
ms.openlocfilehash: 784a6821502a0e02a74d619047604ffe47df7e97
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60914180"
---
# <a name="azure-dedicated-hsm-high-availability"></a>Disponibilità elevata con HSM dedicato di Azure

HSM dedicato di Azure si basa sui data center a disponibilità elevata di Microsoft. Tuttavia qualsiasi data center a disponibilità elevata è vulnerabile agli errori locali e in casi estremi agli errori a livello di area. Microsoft distribuisce i dispositivi HSM in diversi data center di un'area per garantire che, in caso di provisioning di più dispositivi, tali dispositivi non condividano lo stesso rack. Un ulteriore livello di disponibilità elevata può essere ottenuto accoppiando i Data Center in un'area usando la funzionalità a disponibilità elevata gruppo Gemalto questi moduli di protezione hardware. È anche possibile associare i dispositivi fra aree per fronteggiare il failover di area in una situazione di ripristino di emergenza. Con questa configurazione a disponibilità elevata a più livelli qualsiasi errore di dispositivo verrà trattato automaticamente per mantenere le applicazioni in funzione. Tutti i data center dispongono anche di componenti e dispositivi di riserva in loco in modo che qualsiasi dispositivo guasto possa essere sostituito tempestivamente.

## <a name="high-availability-example"></a>Esempio di disponibilità elevata

Le informazioni su come configurare i dispositivi HSM per la disponibilità elevata a livello di software sono disponibili in "Gemalto Luna Network HSM Administration Guide" (Guida all'amministrazione di Gemalto Luna Network HSM). Il documento è disponibile nel [portale di assistenza clienti di Gemalto](https://supportportal.gemalto.com/csm/).

Il diagramma seguente illustra un'architettura a disponibilità elevata. Essa usa vari dispositivi nell'area e vari dispositivi associati in un'area separata. Questa architettura impiega un minimo di quattro dispositivi HSM e componenti di rete virtuale.

![Diagramma di disponibilità elevata](media/high-availability/high-availability.png)

## <a name="next-steps"></a>Passaggi successivi

Prima di eseguire il provisioning di dispositivi o di iniziare la progettazione o distribuzione di applicazioni, è consigliabile aver ben compreso tutti i concetti chiave relativi al servizio.
Altri argomenti sui concetti:

* [Architettura di distribuzione](deployment-architecture.md)
* [Sicurezza fisica](physical-security.md)
* [Rete](networking.md)
* [Supporto](supportability.md)
* [Monitoraggio](monitoring.md)

Per informazioni dettagliate su come configurare i dispositivi HSM per la disponibilità elevata, fare riferimento al portale di supporto clienti di Gemalto per le guide all'amministrazione e vedere la sezione 6.
