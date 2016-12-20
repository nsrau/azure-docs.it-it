---
title: "Sicurezza e identità di Azure per enti pubblici | Microsoft Docs"
description: "Fornisce un confronto delle funzionalità e informazioni aggiuntive sullo sviluppo di applicazioni per Azure Government"
services: Azure-Government
cloud: gov
documentationcenter: 
author: ryansoc
manager: zakramer
editor: 
ms.assetid: e2fe7983-5870-43e9-ae01-2d45d3102c8a
ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 10/12/2016
ms.author: ryansoc
translationtype: Human Translation
ms.sourcegitcommit: cd01170c3c0a3f62024de3357d342af1f4f90c6c
ms.openlocfilehash: 27d447e8e3c336bbce2e1ca81d2c7c413b0360fc


---
# <a name="azure-government-security-identity"></a>Sicurezza e identità di Azure per enti pubblici
## <a name="key-vault"></a>Insieme di credenziali di chiave
Per informazioni dettagliate su questo servizio e su come usarlo, vedere la [documentazione su Insieme di credenziali delle chiavi di Azure](../key-vault/index.md).

### <a name="data-considerations"></a>Considerazioni sui dati
Le informazioni seguenti identificano il limite di Azure per enti pubblici per l'insieme di credenziali delle chiavi di Azure:

| Dati regolamentati o controllati consentiti | Dati regolamentati o controllati non consentiti |
| --- | --- |
| Tutti i dati crittografati con una chiave dell'insieme di credenziali delle chiavi di Azure possono contenere dati regolamentati o controllati. |I metadati dell'insieme di credenziali delle chiavi di Azure non possono contenere dati soggetti al controllo all'esportazione. Questi metadati includono tutti i dati di configurazione immessi durante la creazione e la gestione dell'insieme di credenziali delle chiavi.  Non immettere dati regolamentati o controllati nei campi relativi a nomi di gruppi di risorse, nomi di insiemi di credenziali delle chiavi e nome della sottoscrizione |

L'insieme di credenziali delle chiavi è disponibile a livello generale in Azure per enti pubblici. Come per la versione pubblica, non esistono estensioni, quindi l'insieme di credenziali delle chiavi è disponibile solo tramite PowerShell e l'interfaccia della riga di comando.

## <a name="next-steps"></a>Passaggi successivi
Per informazioni aggiuntive e aggiornamenti, sottoscrivere il <a href="https://blogs.msdn.microsoft.com/azuregov/">blog di Microsoft Azure per enti pubblici. </a>




<!--HONumber=Nov16_HO3-->


