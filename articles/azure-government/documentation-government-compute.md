---
title: Calcolo di Azure per enti pubblici | Documentazione Microsoft
description: "Fornisce un confronto delle funzionalità e informazioni aggiuntive sullo sviluppo di applicazioni per Azure Government"
services: azure-government
cloud: gov
documentationcenter: 
author: ryansoc
manager: zakramer
ms.assetid: fb11f60c-5a70-46a9-82a0-abb2a4f4239b
ms.service: azure-government
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 3/13/2017
ms.author: ryansoc
translationtype: Human Translation
ms.sourcegitcommit: a087df444c5c88ee1dbcf8eb18abf883549a9024
ms.openlocfilehash: 146fcd9788bab55e4abd9e70dcfe25741f041d34
ms.lasthandoff: 03/15/2017


---
# <a name="azure-government-compute"></a>Calcolo di Azure per enti pubblici
## <a name="virtual-machines"></a>Macchine virtuali
Per informazioni dettagliate su questo servizio e come usarlo, vedere [Dimensioni delle macchine virtuali in Azure](../virtual-machines/virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

### <a name="variations"></a>Varianti
I prodotti (VM) sono disponibili in Azure per enti pubblici:

| Prodotti (VM) | US Gov VA | US Gov IA | Dipartimento della difesa Stati Uniti orientali | Dipartimento della difesa Stati Uniti occidentali
| --- | --- | --- |--- |--- |
| A0-A7 |S |S |S |S |S |
| Av2 |S |S |S |S |S |
| Serie D |S |N |N |N |
| Serie Dv2 |S |S |S |S |
| Serie DS |S |N |N |N |
| Serie DSv2 |S |N |S |S |
| Serie F |S |S |S |S |
| Serie FS |S |N |S |S |
| Serie G |S |N |N |N |
| Serie GS |S |N |N |N |

### <a name="data-considerations"></a>Considerazioni sui dati
Le informazioni seguenti identificano il limite di Azure per enti pubblici per Macchine virtuali di Azure:

| Dati regolamentati o controllati consentiti | Dati regolamentati o controllati non consentiti |
| --- | --- |
| I dati immessi, archiviati ed elaborati in una VM possono contenere dati soggetti al controllo all'esportazione. File binari in esecuzione in Macchine virtuali di Azure. Autenticatori statici, ad esempio le password e i PIN delle smart card per l'accesso ai componenti della piattaforma Azure. Chiavi private o i certificati usati per gestire i componenti della piattaforma Azure. Stringhe di connessione SQL.  Altri segreti di sicurezza o informazioni, ad esempio chiavi di crittografia, chiavi master, chiavi di archiviazione e certificati archiviati nei servizi di Azure. |I metadati non possono contenere dati soggetti al controllo all'esportazione. Questi metadati includono tutti i dati di configurazione immessi durante la creazione e la gestione della macchina virtuale di Azure.  Non immettere dati regolamentati o controllati nei campi relativi a nomi di ruoli tenant, gruppi di risorse, nomi delle distribuzioni, nomi delle risorse e tag delle risorse |

## <a name="next-steps"></a>Passaggi successivi
Per informazioni aggiuntive e aggiornamenti, sottoscrivere il <a href="https://blogs.msdn.microsoft.com/azuregov/">blog di Microsoft Azure per enti pubblici. </a>


