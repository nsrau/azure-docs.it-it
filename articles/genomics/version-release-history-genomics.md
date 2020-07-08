---
title: Cronologia delle versioni
titleSuffix: Microsoft Genomics
description: La cronologia delle versioni degli aggiornamenti al client Python di genomica di Microsoft per le correzioni e le nuove funzionalità.
services: genomics
author: grhuynh
manager: cgronlun
ms.author: grhuynh
ms.service: genomics
ms.topic: conceptual
ms.date: 01/11/2019
ms.openlocfilehash: 20475e2cde1b42790740889cf341b3a0a0afccc0
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "76991083"
---
# <a name="version-release-history"></a>Cronologia delle versioni
Il team Genomica di Microsoft aggiorna regolarmente il client Microsoft Genomics Python per apportare correzioni e nuove funzionalità. 

## <a name="latest-release"></a>Versione più recente
Il client Python corrente è la versione 0.9.0. È stato rilasciato il 6 2019 febbraio e supporta l'esecuzione di flussi di lavoro con GATK 3,5 e GATK4. Supporta l'output gVCF e può accettare un argomento facoltativo per la compressione di output.


## <a name="release-history"></a>Cronologia delle versioni 
Le nuove versioni del client Python di genomica di Microsoft vengono rilasciate una volta all'anno. Quando viene rilasciata una nuova versione del client Python di Genomica di Microsoft, viene qui aggiornato un elenco di funzionalità e correzioni. Quando vengono rilasciate delle nuove versioni, le versioni precedenti continueranno a essere supportate per almeno 90 giorni. Quando le versioni precedenti non sono più supportate, verrà indicato in questa pagina. 

### <a name="version-090"></a>Versione 0.9.0
La versione 0.9.0 include il supporto per la compressione di output. Equivale a eseguire `-bgzip` seguito da nell' `-tabix` output di VCF o gvcf. Per altre informazioni, vedere le [Domande frequenti](frequently-asked-questions-genomics.md). 

### <a name="version-081"></a>Versione 0.8.1
La versione 0.8.1 include piccole correzioni di bug.  

### <a name="version-080"></a>Versione 0.8.0
La versione 0.8.0 include il supporto per GATK4 e l'output di gVCFs.  

### <a name="version-074"></a>Versione 0.7.4
La versione 0.7.4 include il supporto per l'accettazione dei token di firma di accesso condiviso invece delle chiavi dell'account nell'input `config.txt`. Per altre informazioni, vedere la [guida introduttiva relativa all'input dei token di firma di accesso condiviso](quickstart-input-sas.md). 

### <a name="version-073"></a>Versione 0.7.3
La versione 0.7.3 include piccole correzioni di bug.

### <a name="version-072"></a>Versione 0.7.2
La versione 0.7.2 rappresenta la versione iniziale. Data di rilascio: 1 novembre 2017.
