---
title: Panoramica di Azure Security BenchmarkAzure Security Benchmark overview
description: Panoramica di Security Benchmark
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 12/16/2019
ms.author: mbaldwin
ms.custom: security-baselines
ms.openlocfilehash: c16d7247b781fea04cfa2d53b8854cff14e039c7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77616438"
---
# <a name="overview-of-azure-security-baselines"></a>Panoramica delle linee di base di sicurezza di AzureOverview of Azure Security Baselines

Le linee di base per la sicurezza di Azure consentono di rafforzare la sicurezza dei prodotti grazie a funzionalità avanzate di strumenti, monitoraggio e sicurezza e offrono un'esperienza coerente durante la protezione dell'ambiente.

Le linee di base per la sicurezza dei servizi di Azure si concentrano sulle aree di controllo incentrate sul cloud. Questi controlli sono coerenti con i benchmark di sicurezza noti, come quelli descritti dal Center for Internet Security (CIS). Le linee di base forniscono indicazioni per le aree di controllo elencate in [Azure Security Benchmark](overview.md).

Ogni raccomandazione include le seguenti informazioni:
- **ID di Azure:** l'ID di benchmark di sicurezza di Azure che corrisponde alla raccomandazione.
- **Consiglio:** seguendo subito dopo l'ID Azure, la raccomandazione fornisce una descrizione generale del controllo.
- **Linee guida**: La motivazione della raccomandazione e i collegamenti alle linee guida su come implementarla. Se la raccomandazione è supportata dal Centro sicurezza di Azure, verranno elencate anche tali informazioni.
- **Responsabilità**: Chi è responsabile dell'attuazione del controllo. Gli scenari possibili sono la responsabilità del cliente, la responsabilità di Microsoft o la responsabilità condivisa.
- **Monitoraggio del Centro sicurezza di Azure:** indica se il controllo viene monitorato dal Centro sicurezza di Azure, con collegamento al riferimento.

Tutti i consigli, inclusi i consigli che non sono applicabili a questo servizio specifico, sono inclusi nella linea di base per fornire un quadro completo di come il benchmark di sicurezza di Azure è correlato a ogni servizio. Occasionalmente possono essere presenti controlli che non sono applicabili per vari motivi, ad esempio, i controlli IaaS/compute-centric (ad esempio i controlli specifici per la gestione della configurazione del sistema operativo) potrebbero non essere applicabili ai servizi PaaS.
