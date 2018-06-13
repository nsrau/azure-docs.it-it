---
title: Servizio app di Azure stack aggiornamento 2 note sulla versione | Documenti Microsoft
description: Informazioni sulle caratteristiche di aggiornamento due per il servizio App nello Stack di Azure, i problemi noti e dove scaricare l'aggiornamento.
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
ms.reviewer: brenduns
ms.openlocfilehash: 8e1790b7d0b3a210a9142fc8580ff8ed4d64311c
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/20/2018
ms.locfileid: "34360414"
---
# <a name="app-service-on-azure-stack-update-2-release-notes"></a>Servizio app di note sulla versione di Azure Stack update 2

*Si applica a: Azure Stack integrate di sistemi Azure Stack Development Kit*

Queste note sulla versione vengono descritti i miglioramenti e correzioni in Azure App Service in Azure Stack Update 2 e i problemi noti. Problemi noti sono suddivisi in problemi direttamente correlati alla distribuzione, processo di aggiornamento e i problemi con la compilazione (post-installazione).

> [!IMPORTANT]
> Applicare l'aggiornamento 1804 al sistema Azure Stack integrato o distribuire il kit di sviluppo dello Stack di Azure più recente prima di distribuire Azure App Service 1.2.
>
>

## <a name="build-reference"></a>Compilazione di riferimento

Il servizio App sul numero di build Azure Stack Update 2 è **72.0.13698.10**

### <a name="prerequisites"></a>Prerequisiti

> [!IMPORTANT]
> Nuove distribuzioni di Azure App Service nello Stack di Azure è ora necessario un [certificato con caratteri jolly tre soggetto](azure-stack-app-service-before-you-get-started.md#get-certificates) in seguito ai miglioramenti nel modo in cui SSO per Kudu viene gestito nel servizio App di Azure. Il nuovo argomento viene  **\*. sso.appservice.\< area\>.\< DomainName\>.\< estensione\>**
>
>

Consultare il [documentazione prima di iniziare a](azure-stack-app-service-before-you-get-started.md) prima di iniziare la distribuzione.

### <a name="new-features-and-fixes"></a>Nuove funzionalità e correzioni

Servizio App di Azure in Azure Stack Update 2 include le correzioni e i miglioramenti seguenti:

- Gli aggiornamenti a **Tenant di servizio App, amministratore, i portali di funzioni e gli strumenti Kudu**. Coerentemente con versione del SDK portale di Azure dello Stack.

- Aggiornamenti al servizio di base per migliorare l'affidabilità e messaggistica abilitazione più semplice diagnosi dei problemi comuni degli errori.

- **Gli aggiornamenti per gli strumenti e Framework di applicazioni seguenti**:
  - Aggiunta di .net Framework 4.7.1
  - Aggiunto **Node. js** versioni:
    - NodeJS 6.12.3
    - NodeJS 8.9.4
    - NodeJS 8.10.0
    - NodeJS 8.11.1
  - Aggiunto **NPM** versioni:
    - 5.6.0
  - Aggiornamento .net Core componenti siano coerenti con il servizio App di Azure nel cloud pubblico.
  - Kudu aggiornato

- Lo scambio automatico della distribuzione di slot della funzionalità abilitata - [configurazione scambio automatico](https://docs.microsoft.com/azure/app-service/web-sites-staged-publishing#configure-auto-swap)

- Eseguire test in produzione abilitata - [Introduzione al Testing nell'ambiente di produzione](https://azure.microsoft.com/resources/videos/introduction-to-azure-websites-testing-in-production-with-galin-iliev/)

- Azure funzioni proxy abilitato - [lavorare con i proxy di funzioni di Azure](https://docs.microsoft.com/en-us/azure/azure-functions/functions-proxies)

- Estensione di amministrazione del servizio App UX supportano aggiunti per:
  - Rotazione segreta
  - Rotazione di certificato
  - Rotazione delle credenziali di sistema
  - Rotazione di stringa di connessione

### <a name="known-issues-post-installation"></a>Problemi noti (post-installazione)

- Thread di lavoro sono in grado di raggiungere il server di file in caso di servizio App viene distribuito in una rete virtuale esistente e il file server è disponibile nella rete privata solo.

Se si sceglie di distribuire in una rete virtuale esistente e un indirizzo IP interno per la connessione al server di file, è necessario aggiungere una regola di sicurezza in uscita, abilitare il traffico SMB tra la subnet di lavoro e il file server. A tale scopo, passare al WorkersNsg nel portale di amministrazione e aggiungere una regola di sicurezza in uscita con le proprietà seguenti:
 * Origine: qualsiasi
 * Intervallo di porte di origine: *
 * Destinazione: Indirizzi IP
 * Intervallo di indirizzi IP di destinazione: intervallo di indirizzi IP per il file server
 * Intervallo di porte di destinazione: 445
 * Protocollo: TCP
 * Azione: Consenti
 * Priorità: 700
 * Nome: Outbound_Allow_SMB445

### <a name="known-issues-for-cloud-admins-operating-azure-app-service-on-azure-stack"></a>Problemi noti per gli amministratori Cloud operativo Azure App Service nello Stack di Azure

Consultare la documentazione nel [note sulla versione di Azure Stack 1804](azure-stack-update-1804.md)

## <a name="next-steps"></a>Passaggi successivi

- Per una panoramica di servizio App di Azure, vedere [Azure App Service nella panoramica di Azure Stack](azure-stack-app-service-overview.md).
- Per ulteriori informazioni su come preparare la distribuzione di servizio App nello Stack di Azure, vedere [prima di iniziare a con il servizio App in Azure Stack](azure-stack-app-service-before-you-get-started.md).
