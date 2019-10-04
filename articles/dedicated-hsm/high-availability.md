---
title: Disponibilità elevata - HSM dedicato di Azure | Microsoft Docs
description: Esempio di disponibilità elevata con HSM dedicato di Azure e considerazioni di base
services: dedicated-hsm
author: msmbaldwin
manager: rkarlin
ms.custom: mvc, seodec18
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/27/2019
ms.author: mbaldwin
ms.openlocfilehash: 536ef62acad900090924598edfa45450b2a8c951
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/11/2019
ms.locfileid: "70882257"
---
# <a name="azure-dedicated-hsm-high-availability"></a>Disponibilità elevata con HSM dedicato di Azure

HSM dedicato di Azure si basa sui data center a disponibilità elevata di Microsoft. Tuttavia qualsiasi data center a disponibilità elevata è vulnerabile agli errori locali e in casi estremi agli errori a livello di area. Microsoft distribuisce i dispositivi HSM in diversi data center di un'area per garantire che, in caso di provisioning di più dispositivi, tali dispositivi non condividano lo stesso rack. È possibile ottenere un ulteriore livello di disponibilità elevata abbinando questi HSM tra i Data Center di un'area usando la funzionalità gruppo a disponibilità elevata di Gemalto. È anche possibile associare i dispositivi fra aree per fronteggiare il failover di area in una situazione di ripristino di emergenza. Con questa configurazione a disponibilità elevata a più livelli qualsiasi errore di dispositivo verrà trattato automaticamente per mantenere le applicazioni in funzione. Tutti i data center dispongono anche di componenti e dispositivi di riserva in loco in modo che qualsiasi dispositivo guasto possa essere sostituito tempestivamente.

## <a name="high-availability-example"></a>Esempio di disponibilità elevata

Le informazioni su come configurare i dispositivi HSM per la disponibilità elevata a livello di software sono disponibili in "Gemalto Luna Network HSM Administration Guide" (Guida all'amministrazione di Gemalto Luna Network HSM). Questo documento è disponibile nella pagina del modulo di protezione [hardware Gemalto](https://safenet.gemalto.com/data-encryption/hardware-security-modules-hsms/safenet-network-hsm/).

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
