---
title: Servizio App in Azure Stack Update uno | Documenti Microsoft
description: Informazioni sulle caratteristiche di aggiornamento per il servizio App nello Stack di Azure, i problemi noti e dove scaricare l'aggiornamento.
services: azure-stack
documentationcenter: 
author: apwestgarth
manager: stefsch
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/08/2018
ms.author: anwestg
ms.reviewer: brenduns
ms.openlocfilehash: 0c33c8fdefbb27ba8414e58bed1b42ee7aaba88a
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/16/2018
---
# <a name="app-service-on-azure-stack-update-one-release-notes"></a>Servizio app di Azure stack aggiornare uno note sulla versione

*Si applica a: Azure Stack integrate di sistemi Azure Stack Development Kit*

Queste note sulla versione vengono descritti i miglioramenti e correzioni in Azure App Service in Azure Stack Update 1 e i problemi noti. Problemi noti sono suddivisi in problemi direttamente correlati alla distribuzione, processo di aggiornamento e i problemi con la compilazione (post-installazione).

> [!IMPORTANT]
> Applicare l'aggiornamento 1802 al sistema Azure Stack integrato o distribuire il kit di sviluppo dello Stack di Azure più recente prima di distribuire il servizio App di Azure.
>
>

## <a name="build-reference"></a>Compilazione di riferimento

Il servizio App sul numero di build Azure Stack Update 1 è **69.0.13698.9**

### <a name="prerequisites"></a>Prerequisiti

> [!IMPORTANT]
> Servizio App di Azure nello Stack di Azure richiede ora un [certificato con caratteri jolly tre soggetto](azure-stack-app-service-before-you-get-started.md#get-certificates) in seguito ai miglioramenti nel modo in cui SSO per Kudu viene gestito nel servizio App di Azure.  È il nuovo argomento * * *. sso.appservice.<region>. <domainname>.<extension>**
>
>

Consultare il [documentazione prima di iniziare a](azure-stack-app-service-before-you-get-started.md) prima di iniziare la distribuzione.

### <a name="new-features-and-fixes"></a>Nuove funzionalità e correzioni

Servizio App di Azure in Azure Stack Update 1 include gli aggiornamenti e i miglioramenti seguenti:

- **Elevata disponibilità del servizio App di Azure** -i carichi di lavoro di The Azure Stack 1802 update è attivato per essere distribuite tra più domini di errore.  Pertanto infrastruttura del servizio App è in grado di essere a tolleranza di errore in quanto verrà distribuito nei domini di errore.  Per impostazione predefinita tutte le nuove distribuzioni di servizio App di Azure avrà questa funzionalità tuttavia per le distribuzioni completate, prima di Azure Stack 1802 aggiornamento viene applicato consultare il [documentazione di dominio di errore di servizio App](azure-stack-app-service-fault-domain-update.md)

- **Distribuire nella rete virtuale esistente** -i clienti possono ora distribuire App servizio nello Stack di Azure all'interno di una rete virtuale esistente.  Distribuzione in una rete virtuale esistente consente ai clienti per la connessione al Server SQL e File Server, necessari per il servizio App di Azure, tramite le porte private.  Durante la distribuzione ai clienti è possono scegliere di distribuire in una rete virtuale esistente, tuttavia [necessario creare subnet per l'utilizzo dal servizio App](azure-stack-app-service-before-you-get-started.md#virtual-network) prima della distribuzione.

- Gli aggiornamenti a **Tenant di servizio App, amministratore, i portali di funzioni e gli strumenti Kudu**.  Coerentemente con versione del SDK portale di Azure dello Stack.

- **Gli aggiornamenti per gli strumenti e Framework di applicazioni seguenti**:
    - Aggiunto **.Net Core 2.0** supportano
    - Aggiunto **Node. js** versioni:
        - 6.11.2
        - 6.11.5
        - 7.10.1
        - 8.0.0
        - 8.1.4
        - 8.4.0
        - 8.5.0
        - 8.7.0
        - 8.8.1
        - 8.9.0
    - Aggiunto **NPM** versioni:
        - 3.10.10
        - 4.2.0
        - 5.0.0
        - 5.0.3
        - 5.3.0
        - 5.4.2
        - 5.5.1
    - Aggiunto **PHP** aggiornamenti:
        - 5.6.32
        - 7.0.26 (x86 e x64)
        - 7.1.12 (x86 e x64)
    - Aggiornato **Git per Windows** a v 2.14.1
    - Aggiornato **Mercurial** a v4.5.0

  - Aggiunta del supporto per **solo HTTPS** funzionalità all'interno di funzionalità dominio personalizzato nel portale Tenant di servizio App. 

  - Convalida aggiuntiva della connessione di archiviazione nel selettore di archiviazione personalizzati per le funzioni di Azure 

#### <a name="fixes"></a>Correzioni

- Quando si crea un pacchetto di distribuzione non in linea, i clienti non riceveranno più un accesso negato quando si apre la cartella dal programma di installazione di servizio App

- Risolvere i problemi quando si utilizza la funzionalità di domini personalizzati nel portale Tenant di servizio App.

- Evitare che i clienti che usano i nomi degli amministratori riservato durante l'installazione

- Abilitata la distribuzione di servizio App con **appartenenti a un dominio** file server di

- Miglioramenti apportati al recupero della radice dello Stack di Azure nello script e l'ora convalidare il certificato radice nel programma di installazione del servizio App.

- Stato errato fisso restituito in Azure Resource Manager quando una sottoscrizione viene eliminato tale risorse contenute nello spazio dei nomi Microsoft.

### <a name="known-issues-with-the-deployment-process"></a>Problemi noti con il processo di distribuzione

- Sono presenti problemi noti per la distribuzione del servizio App di Azure in Azure Stack Update 1.

### <a name="known-issues-with-the-update-process"></a>Problemi noti con il processo di aggiornamento

- Sono presenti problemi noti per l'aggiornamento del servizio App di Azure in Azure Stack Update 1.

### <a name="known-issues-post-installation"></a>Problemi noti (post-installazione)

- Sono presenti problemi noti per l'installazione del servizio App di Azure in Azure Stack Update 1.

### <a name="known-issues-for-cloud-admins-operating-azure-app-service-on-azure-stack"></a>Problemi noti per gli amministratori Cloud operativo Azure App Service nello Stack di Azure

Consultare la documentazione nel [note sulla versione di Azure Stack 1802](azure-stack-update-1802.md)

## <a name="see-also"></a>Vedere anche 

- Per una panoramica di servizio App di Azure, vedere [Azure App Service nella panoramica di Azure Stack](azure-stack-app-service-overview.md).
- Per ulteriori informazioni su come preparare la distribuzione di servizio App nello Stack di Azure, vedere [prima di iniziare a con il servizio App in Azure Stack](azure-stack-app-service-before-you-get-started.md).
