---
title: Servizio App di Azure per il supporto di Runtime Java Windows
description: Questo argomento fornisce l'istruzione di Runtime Java di supporto per servizio App di Azure su Windows.
author: bmitchell287
ms.author: brendm; joshuapa
ms.date: 3/22/2019
ms.topic: article
ms.service: app-service
manager: dougE
ms.openlocfilehash: ab0e9b9f272108745b629d2cb284f6c5ab52c76f
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2019
ms.locfileid: "58523041"
---
# <a name="java-runtime-statement-of-support-for-app-service-on-windows"></a>Dichiarazione di runtime Java di supporto per il servizio App in Windows

## <a name="jdk-versions-and-maintenance"></a>Versioni di JDK e manutenzione

Il pacchetto Java Development Kit (JDK) supportato di Azure è [Zulu](https://www.azul.com/downloads/azure-only/zulu/) fornito da [Azul Systems](https://www.azul.com/).

Gli aggiornamenti di versione principale verranno forniti tramite nuove opzioni di runtime in servizio App di Azure per Windows. I clienti eseguono l'aggiornamento a queste versioni più recenti di Java configurando la distribuzione del servizio app e sono responsabili dei test, oltre che di assicurare che l'aggiornamento principale risponda alle esigenze.

Ai pacchetti JDK supportati vengono automaticamente applicate patch con cadenza trimestrale, a gennaio, aprile, luglio e ottobre di ogni anno.

## <a name="security-updates"></a>Aggiornamenti della sicurezza

Le patch e le correzioni per le principali vulnerabilità della sicurezza verranno rilasciate da Azul Systems non appena disponibili. Una vulnerabilità "principale" viene definita da un punteggio di base pari o superiore a 9,0 in [NIST Common Vulnerability Scoring System, versione 2](https://nvd.nist.gov/cvss.cfm).

## <a name="deprecation-and-retirement"></a>Deprecazione e ritiro

Se un runtime Java supportato sarà ritirato, gli sviluppatori di Azure che usano il runtime interessato riceveranno un avviso di funzionalità deprecata almeno sei mesi prima che il runtime venga ritirato.

## <a name="local-development"></a>Sviluppo locale

Gli sviluppatori possono scaricare l'edizione Production di Azul Zulu Enterprise JDK per lo sviluppo locale dal [sito di download di Azul](https://www.azul.com/downloads/azure-only/zulu/).

## <a name="development-support"></a>Supporto per lo sviluppo

Il supporto tecnico per il [supportate da Azure Azul Zulu JDK](https://www.azul.com/downloads/azure-only/zulu/) è disponibile tramite Microsoft durante lo sviluppo per Azure o [Azure Stack](https://azure.microsoft.com/overview/azure-stack/) con un [qualificato piano di supporto tecnico di Azure](https://azure.microsoft.com/support/plans/).

## <a name="runtime-support"></a>Supporto di runtime

Gli sviluppatori possono [aprire un problema](/azure/azure-supportability/how-to-create-azure-support-request) relativo ai pacchetti JDK Azul Zulu tramite il supporto di Azure se hanno un [piano di supporto qualificato](https://azure.microsoft.com/support/plans/).

## <a name="next-steps"></a>Passaggi successivi

Questo argomento fornisce l'istruzione di Runtime Java di supporto per servizio App di Azure su Windows.
- Per altre informazioni sull'hosting di applicazioni web con servizio App di Azure, vedere [Panoramica del servizio App](overview.md).
- Per informazioni su Java sullo sviluppo di Azure, vedere [Azure per il centro per sviluppatori Java](https://docs.microsoft.com/java/azure/?view=azure-java-stable).
