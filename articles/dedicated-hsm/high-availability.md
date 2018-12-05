---
title: Disponibilità elevata con HSM dedicato di Azure | Microsoft Docs
description: HSM dedicato di Azure offre capacità di archiviazione chiavi in Azure che soddisfano la certificazione FIPS 140-2 Livello 3
services: dedicated-hsm
author: barclayn
manager: mbaldwin
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/19/2018
ms.author: barclayn
ms.openlocfilehash: eb3c6ea64b55d59f261927925d4d4ae87546bf95
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/26/2018
ms.locfileid: "52319240"
---
# <a name="azure-dedicated-hsm-high-availability"></a>Disponibilità elevata con HSM dedicato di Azure

HSM dedicato di Azure si basa sui data center a disponibilità elevata di Microsoft. Tuttavia qualsiasi data center a disponibilità elevata è vulnerabile agli errori locali e in casi estremi agli errori a livello di area. Microsoft distribuisce i dispositivi HSM in diversi data center di un'area per garantire che, in caso di provisioning di più dispositivi, tali dispositivi non condividano lo stesso rack. Si può ottenere un livello di disponibilità elevata maggiore associando questi moduli di protezione hardware tra più data center di un'area. È anche possibile associare i dispositivi fra aree per fronteggiare il failover di area in una situazione di ripristino di emergenza. Con questa configurazione a disponibilità elevata a più livelli qualsiasi errore di dispositivo verrà trattato automaticamente per mantenere le applicazioni in funzione. Tutti i data center dispongono anche di componenti e dispositivi di riserva in loco in modo che qualsiasi dispositivo guasto possa essere sostituito tempestivamente.

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
