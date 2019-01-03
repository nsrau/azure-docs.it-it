---
title: Note sulla versione 2 aggiornamento del servizio App in Azure Stack | Microsoft Docs
description: Informazioni sulle novità nell'aggiornamento 2 per il servizio App in Azure Stack, i problemi noti e dove scaricare l'aggiornamento.
services: azure-stack
documentationcenter: ''
author: apwestgarth
manager: stefsch
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/18/2018
ms.author: anwestg
ms.reviewer: sethm
ms.openlocfilehash: f40d88df7a46c73981b6f20bee0b119743c08257
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/21/2018
ms.locfileid: "53714492"
---
# <a name="app-service-on-azure-stack-update-2-release-notes"></a>Servizio App in note sulla versione update 2 di Azure Stack

*Si applica a: Azure Stack Development Kit e i sistemi integrati di Azure Stack*

Queste note sulla versione descrivono i miglioramenti e correzioni in servizio App di Azure in Azure Stack Update 2 e problemi noti. Problemi noti sono suddivisi in problemi correlati direttamente per la distribuzione, il processo di aggiornamento e i problemi con la build (post-installazione).

> [!IMPORTANT]
> Applicare l'aggiornamento 1804 per il sistema integrato Azure Stack o distribuire il kit di sviluppo di Azure Stack più recente prima della distribuzione del servizio App di Azure 1.2.
>
>

## <a name="build-reference"></a>Riferimento alla compilazione

Il servizio App sul numero di build di Azure Stack Update 2 è **72.0.13698.10**

### <a name="prerequisites"></a>Prerequisiti

> [!IMPORTANT]
> Le nuove distribuzioni del servizio App di Azure in Azure Stack è ora necessario un [certificato con caratteri jolly tre soggetto](azure-stack-app-service-before-you-get-started.md#get-certificates) grazie ai miglioramenti nel modo in cui l'accesso SSO per Kudu è ora gestito nel servizio App di Azure. Il nuovo soggetto  **\*. sso.appservice.\< area geografica\>.\< DomainName\>.\< estensione\>**
>
>

Vedere le [documentazione prima di iniziare a](azure-stack-app-service-before-you-get-started.md) prima di iniziare la distribuzione.

### <a name="new-features-and-fixes"></a>Nuove funzionalità e correzioni

Servizio App di Azure in Azure Stack Update 2 include le correzioni e i miglioramenti seguenti:

- Gli aggiornamenti **Tenant del servizio App, amministratore, i portali di funzioni e gli strumenti di Kudu**. Coerente con la versione di SDK portale di Azure Stack.

- Aggiornamenti al servizio di base per migliorare l'affidabilità e messaggistica abilitazione più semplice diagnosi dei problemi comuni degli errori.

- **Gli aggiornamenti per gli strumenti e Framework di applicazione seguenti**:
  - Aggiunta di .net Framework 4.7.1
  - Aggiunti **Node. js** versioni:
    - NodeJS 6.12.3
    - NodeJS 8.9.4
    - NodeJS 8.10.0
    - NodeJS 8.11.1
  - Aggiunti **NPM** versioni:
    - 5.6.0
  - .NET Core aggiornati i componenti siano coerenti con il servizio App di Azure nel cloud pubblico.
  - Kudu aggiornato

- Lo scambio automatico della distribuzione slot funzionalità abilitata - [configurazione scambio automatico](https://docs.microsoft.com/azure/app-service/deploy-staging-slots#configure-auto-swap)

- Test in produzione abilitata - [Introduzione al Testing nell'ambiente di produzione](https://azure.microsoft.com/resources/videos/introduction-to-azure-websites-testing-in-production-with-galin-iliev/)

- Proxy di funzioni di Azure abilitata - [lavorare con il proxy di funzioni di Azure](https://docs.microsoft.com/azure/azure-functions/functions-proxies)

- Estensione amministratore del servizio App dell'esperienza utente il supporto aggiunto per:
  - Rotazione segreta
  - Rotazione del certificato
  - Rotazione delle credenziali di sistema
  - Rotazione delle stringhe di connessione

### <a name="known-issues-post-installation"></a>Problemi noti (post-installazione)

- I ruoli di lavoro sono in grado di raggiungere il server di file quando il servizio App viene distribuito in una rete virtuale esistente e il file server è disponibile nella rete privata solo.

Se si sceglie di distribuire in una rete virtuale esistente e un indirizzo IP interno per la connessione al file server, è necessario aggiungere una regola di sicurezza in uscita, consentendo il traffico tra la subnet del ruolo di lavoro e il file server SMB. A tale scopo, passare a WorkersNsg nel portale di amministrazione e aggiungere una regola di sicurezza in uscita con le proprietà seguenti:
 * Origine: Qualsiasi
 * Intervallo di porte di origine: *
 * Destinazione: Indirizzi IP
 * Intervallo di indirizzi IP di destinazione: Intervallo di indirizzi IP per il file server
 * Intervallo di porte di destinazione: 445
 * Protocollo: TCP
 * Azione: CONSENTI
 * Priorità: 700
 * Nome: Outbound_Allow_SMB445

### <a name="known-issues-for-cloud-admins-operating-azure-app-service-on-azure-stack"></a>Problemi noti per gli amministratori Cloud funziona nel servizio App di Azure in Azure Stack

Vedere la documentazione nel [note sulla versione 1804 dello Stack di Azure](azure-stack-update-1804.md)

## <a name="next-steps"></a>Passaggi successivi

- Per una panoramica del servizio App di Azure, vedere [servizio App di Azure nella panoramica di Azure Stack](azure-stack-app-service-overview.md).
- Per altre informazioni su come preparare la distribuzione del servizio App in Azure Stack, vedere [prima di iniziare con il servizio App in Azure Stack](azure-stack-app-service-before-you-get-started.md).
