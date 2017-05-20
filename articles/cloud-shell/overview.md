---
title: Panoramica di Azure Cloud Shell (anteprima) | Microsoft Docs
description: Panoramica di Azure Cloud Shell.
services: 
documentationcenter: 
author: jluk
manager: timlt
tags: azure-resource-manager
ms.assetid: 
ms.service: 
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 05/10/2017
ms.author: juluk
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 47627bc6df93db1d92aa29350fe6e48039dc6f1b
ms.contentlocale: it-it
ms.lasthandoff: 05/10/2017

---
# <a name="overview-of-azure-cloud-shell-preview"></a>Panoramica di Azure Cloud Shell (anteprima)
Azure Cloud Shell è una shell interattiva accessibile dal browser per la gestione delle risorse di Azure.

![](media/startup.gif)

## <a name="features"></a>Funzionalità
### <a name="browser-based-shell-experience"></a>Esperienza di shell basata su browser
Cloud Shell consente l'accesso a un'esperienza di riga di comando basata su browser realizzata pensando alle attività di gestione di Azure management. È possibile sfruttare Cloud Shell per lavorare senza i limiti di un computer locale come solo il cloud può permettere.

### <a name="pre-configured-azure-workstation"></a>Workstation Azure preconfigurata
In Cloud Shell sono preinstallati i più diffusi strumenti da riga di comando e il supporto per linguaggi per poter lavorare più velocemente.

[Vedere qui l'elenco completo di strumenti per Azure Cloud Shell.](features.md#tools)

### <a name="automatic-authentication"></a>Autenticazione automatica
Cloud Shell esegue automaticamente l'autenticazione sicura a ogni sessione per un accesso immediato alle risorse tramite l'interfaccia della riga di comando di Azure 2.0.

### <a name="connect-your-azure-file-storage"></a>Connettersi ad Archiviazione file di Azure
I computer Cloud Shell sono temporanei e quindi è necessario montare una condivisione file di Azure per rendere persistente la directory $Home.
Al primo avvio Cloud Shell chiede di creare un gruppo di risorse, un account di archiviazione e una condivisione file per conto dell'utente. Questo passaggio è occasionale e verrà automaticamente collegato per tutte le sessioni. 

![](media/storage-prompt.png)

Viene creato per conto dell'utente un account di archiviazione con ridondanza locale con una condivisione file di Azure contenente un'immagine del disco da 5 GB.
Questa immagine del disco viene usata per sincronizzare e rendere persistente la directory $Home. Vengono applicati i normali costi di archiviazione.
Verranno create tre risorse per conto dell'utente:
1. Gruppo di risorse denominato: `cloud-shell-storage-<region>`
2. Account di archiviazione denominato: `cs-uniqueGuid`
3. Condivisione file denominata: `cs-<user>-<domain>-com-uniqueGuid`

[Altre informazioni su come Cloud Shell rende persistenti i file] (persisting-shell-storage.md).

## <a name="concepts"></a>Concetti
* Cloud Shell viene eseguito in un computer temporaneo disponibile per ogni sessione e per ogni utente
* Il timeout di Cloud Shell si verifica dopo 10 minuti senza attività interattiva
* Cloud Shell è accessibile solo con una condivisione di file collegata
* A Cloud Shell viene assegnato un computer per ogni account utente
* Vengono impostate le autorizzazioni per un normale utente Linux

[Altre informazioni su tutte le funzionalità Cloud Shell.](features.md)

## <a name="examples"></a>esempi
* Creare o modificare gli script per gestire le risorse di Azure da qualsiasi browser
* Gestire le risorse tramite il portale di Azure e l'interfaccia della riga di comando di Azure 2.0 contemporaneamente
* Effettuare il test drive dell'interfaccia della riga di comando di Azure 2.0

[Provare tutti questi esempi nella guida introduttiva a Cloud Shell.](quickstart.md)

## <a name="pricing"></a>Prezzi
Il computer che ospita Cloud Shell è gratuito, con il prerequisito di una condivisione file di Azure montata per rendere persistente la directory $Home. Vengono applicati i normali costi di archiviazione.

## <a name="supported-browsers"></a>Browser supportati
Cloud Shell è consigliato per Chrome, Microsoft Edge e Safari. Cloud Shell è supportato per Chrome, Firefox, Safari, IE e Microsoft Edge, ma è soggetto a impostazioni specifiche del browser.
Per le limitazioni note specifiche, vedere le [limitazioni di Cloud Shell](limitations.md).
