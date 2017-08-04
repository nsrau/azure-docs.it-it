---
title: Panoramica di Azure Cloud Shell (anteprima) | Microsoft Docs
description: Panoramica di Azure Cloud Shell.
services: 
documentationcenter: 
author: jluk
manager: timlt
tags: azure-resource-manager
ms.assetid: 
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 07/10/2017
ms.author: juluk
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 865c72b7525c185d047d6c9f57b642a195e56fd4
ms.contentlocale: it-it
ms.lasthandoff: 07/21/2017

---
# <a name="overview-of-azure-cloud-shell-preview"></a>Panoramica di Azure Cloud Shell (anteprima)
Azure Cloud Shell è una shell interattiva accessibile dal browser per la gestione delle risorse di Azure.

![](media/overview-pic.png)

## <a name="features"></a>Funzionalità
### <a name="browser-based-shell-experience"></a>Esperienza di shell basata su browser
Cloud Shell consente l'accesso a un'esperienza di riga di comando basata su browser realizzata pensando alle attività di gestione di Azure management. È possibile sfruttare Cloud Shell per lavorare senza i limiti di un computer locale come solo il cloud può permettere.

### <a name="pre-configured-azure-workstation"></a>Workstation Azure preconfigurata
In Cloud Shell sono preinstallati i più diffusi strumenti da riga di comando e il supporto per linguaggi per poter lavorare più velocemente.

[Vedere qui l'elenco completo di strumenti per Azure Cloud Shell.](features.md#tools)

### <a name="automatic-authentication"></a>Autenticazione automatica
Cloud Shell esegue automaticamente l'autenticazione sicura a ogni sessione per un accesso immediato alle risorse tramite l'interfaccia della riga di comando di Azure 2.0.

### <a name="connect-your-azure-file-storage"></a>Connettersi ad Archiviazione file di Azure
I computer Cloud Shell sono temporanei e quindi è necessario montare una condivisione file di Azure come `clouddrive` per rendere persistente la directory $Home.
Al primo avvio Cloud Shell chiede di creare un gruppo di risorse, un account di archiviazione e una condivisione file per conto dell'utente. Questo passaggio è occasionale e verrà automaticamente collegato per tutte le sessioni. 

#### <a name="create-new-storage"></a>Creare una nuova risorsa di archiviazione
![](media/basic-storage.png)

Viene creato per conto dell'utente un account di archiviazione con ridondanza locale, ovvero LRS, con una condivisione file di Azure contenente un'immagine del disco da 5 GB. La condivisione file viene montata come `clouddrive` per l'interazione della condivisione file stessa con l'immagine del disco utilizzata per sincronizzare e rendere persistente la directory $Home. Vengono applicati i normali costi di archiviazione.

Verranno create tre risorse per conto dell'utente:
1. Gruppo di risorse denominato: `cloud-shell-storage-<region>`
2. Account di archiviazione denominato: `cs-uniqueGuid`
3. Condivisione file denominata: `cs-<user>-<domain>-com-uniqueGuid`

> [!Note]
> Tutti i file della directory $Home, come le chiavi SSH, vengono mantenuti nell'immagine del disco utente archiviato nella condivisione file montata. Applicare le procedure consigliate quando si salvano i file nella directory $Home e nella condivisione file montata.

#### <a name="use-existing-resources"></a>Usare le risorse esistenti
![](media/advanced-storage.png)

Viene data anche un'opzione avanzata che consente di associare le risorse esistenti a Cloud Shell. Quando verrà richiesta l'installazione dell'archiviazione, fare clic su "Mostra impostazioni avanzate" per selezionare le opzioni aggiuntive. I menu a discesa vengono filtrati per le aree Cloud Shell assegnate e gli account di archiviazione ridondanti a livello locale o globale.

[Altre informazioni sull'archiviazione di Cloud Shell, l'aggiornamento delle condivisioni file e il caricamento e download di file.] (persisting-shell-storage.md)

## <a name="concepts"></a>Concetti
* Cloud Shell viene eseguito in un computer temporaneo disponibile per ogni sessione e per ogni utente
* Il timeout di Cloud Shell si verifica dopo 20 minuti senza attività interattiva
* Cloud Shell è accessibile solo con una condivisione di file collegata
* A Cloud Shell viene assegnato un computer per ogni account utente
* Vengono impostate le autorizzazioni per un normale utente Linux

[Altre informazioni su tutte le funzionalità Cloud Shell.](features.md)

## <a name="examples"></a>esempi
* Creare o modificare gli script per automatizzare la gestione di Azure
* Gestire le risorse tramite il portale di Azure e l'interfaccia della riga di comando di Azure 2.0 contemporaneamente
* Effettuare il test drive dell'interfaccia della riga di comando di Azure 2.0

[Provare tutti questi esempi nella guida introduttiva a Cloud Shell.](quickstart.md)

## <a name="pricing"></a>Prezzi
Il computer che ospita Cloud Shell è gratuito, con il prerequisito di una condivisione file di Azure montata per rendere persistente la directory $Home. Vengono applicati i normali costi di archiviazione.

## <a name="supported-browsers"></a>Browser supportati
Cloud Shell è consigliato per Chrome, Microsoft Edge e Safari. Cloud Shell è supportato per Chrome, Firefox, Safari, IE e Microsoft Edge, ma è soggetto a impostazioni specifiche del browser.

## <a name="troubleshooting"></a>Risoluzione dei problemi
1. Quando si usa una sottoscrizione di Azure Active Directory, non è possibile creare la risorsa di archiviazione a causa dell'errore 400 DisallowedOperation. Per risolvere questo problema, usare una sottoscrizione di Azure in grado di creare le risorse di archiviazione. Le sottoscrizioni di AD non sono in grado di creare le risorse di Azure.

Per le limitazioni note specifiche, vedere le [limitazioni di Cloud Shell](limitations.md).
