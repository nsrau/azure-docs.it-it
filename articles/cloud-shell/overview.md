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
ms.date: 11/02/2017
ms.author: juluk
ms.openlocfilehash: 3acea56ea414f0c43333a02274e91226db29d454
ms.sourcegitcommit: 38c9176c0c967dd641d3a87d1f9ae53636cf8260
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2017
---
# <a name="overview-of-azure-cloud-shell-preview"></a>Panoramica di Azure Cloud Shell (anteprima)
Azure Cloud Shell è una shell interattiva accessibile dal browser per la gestione delle risorse di Azure.
Offre la flessibilità necessaria per scegliere l'esperienza shell più adatta al proprio modo di lavorare.
Gli utenti Linux possono scegliere un'esperienza Bash, mentre gli utenti Windows possono scegliere PowerShell.

Avvio tramite il portale di Azure dall'icona di Cloud Shell:

![Avvio del portale](media/overview/portal-launch-icon.png)

Usare Bash o PowerShell dall'elenco a discesa del selettore della shell:

![Bash in Cloud Shell](media/overview/overview-bash-pic.png)

![PowerShell in Azure Cloud Shell](media/overview/overview-ps-pic.png)

## <a name="features"></a>Funzionalità
### <a name="browser-based-shell-experience"></a>Esperienza di shell basata su browser
Cloud Shell consente l'accesso a un'esperienza di riga di comando basata su browser realizzata pensando alle attività di gestione di Azure management.
È possibile sfruttare Cloud Shell per lavorare senza i limiti di un computer locale come solo il cloud può permettere.

### <a name="choice-of-preferred-shell-experience"></a>Scelta dell’esperienza shell preferita
Azure Cloud Shell offre la flessibilità necessaria per scegliere l'esperienza shell più adatta al proprio modo di lavorare.
Gli utenti Linux possono scegliere un'esperienza Bash, mentre gli utenti Windows possono scegliere PowerShell.

### <a name="pre-configured-azure-workstation"></a>Workstation Azure preconfigurata
In Cloud Shell sono preinstallati i più diffusi strumenti da riga di comando e il supporto per linguaggi per poter lavorare più velocemente.

Visualizzare l'elenco completo di strumenti per [l’esperienza Bash](features.md#tools) e [l’esperienza PowerShell.](features-powershell.md#tools)

### <a name="automatic-authentication"></a>Autenticazione automatica
Cloud Shell esegue automaticamente l'autenticazione sicura a ogni sessione per un accesso immediato alle risorse tramite l'interfaccia della riga di comando di Azure 2.0 o i cmdlet di Azure PowerShell.

### <a name="connect-your-azure-file-storage"></a>Connettersi ad Archiviazione file di Azure
I computer Cloud Shell sono temporanei e quindi è necessario montare una condivisione File di Azure come `clouddrive` per rendere persistente la directory $Home.
Al primo avvio Cloud Shell chiede di creare un gruppo di risorse, un account di archiviazione e una condivisione file per conto dell'utente. Questo passaggio è occasionale e verrà automaticamente collegato per tutte le sessioni. Può essere eseguito il mapping di un'unica condivisione file, che verrà utilizzato da Bash e PowerShell in Cloud Shell.

#### <a name="create-new-storage"></a>Creare una nuova risorsa di archiviazione
![](media/overview/basic-storage.png)

Può essere creato per conto dell'utente un account di archiviazione con ridondanza locale (LRS) con una condivisione File di Azure. La condivisione File di Azure verrà usata sia per gli ambienti Bash che PowerShell se si sceglie di usare entrambi. Vengono applicati i normali costi di archiviazione.

Verranno create tre risorse per conto dell'utente:
1. Gruppo di risorse denominato: `cloud-shell-storage-<region>`
2. Account di archiviazione denominato: `cs<uniqueGuid>`
3. Condivisione file denominata: `cs-<user>-<domain>-com-<uniqueGuid>`

> [!Note]
> Bash in Cloud Shell crea inoltre un'immagine del disco da 5 GB predefinita per rendere permanente `$Home`. Tutti i file della directory $Home, come le chiavi SSH, vengono mantenuti nell'immagine del disco utente archiviato nella condivisione file montata. Applicare le procedure consigliate quando si salvano i file nella directory $Home e nella condivisione file montata.

#### <a name="use-existing-resources"></a>Usare le risorse esistenti
![](media/overview/advanced-storage.png)

Viene data un'opzione avanzata per associare le risorse esistenti a Cloud Shell.
Quando verrà richiesta l'impostazione dell'archiviazione, fare clic su "Mostra impostazioni avanzate" per visualizzare le opzioni aggiuntive.
I menu a discesa vengono filtrati per le aree Cloud Shell assegnate e gli account di archiviazione ridondanti a livello locale o globale.

[Altre informazioni sull'archiviazione di Cloud Shell, l'aggiornamento delle condivisioni file e il caricamento e download di file.](persisting-shell-storage.md)

## <a name="concepts"></a>Concetti
* Cloud Shell viene eseguito in un computer temporaneo disponibile per ogni sessione e per ogni utente
* Il timeout di Cloud Shell si verifica dopo 20 minuti senza attività interattiva
* Cloud Shell è accessibile solo con una condivisione di file collegata
* Cloud Shell utilizza la stessa condivisione file sia per Bash che per PowerShell
* A Cloud Shell viene assegnato un computer per ogni account utente
* Vengono impostate le autorizzazioni per un normale utente Linux (Bash)

Ulteriori informazioni sulle funzionalità in [Bash in Cloud Shell](features.md) e [PowerShell in Cloud Shell](features-powershell.md).

## <a name="examples"></a>esempi
* Usare gli script per automatizzare la gestione delle attività di Azure
* Gestire le risorse Azure tramite il portale di Azure e gli strumenti da riga di comando di Azure contemporaneamente
* Provare i cmdlet dell’interfaccia della riga di comando di Azure 2.0 o Azure PowerShell

Provare gli esempi seguenti nella Guida introduttiva a [Bash in Cloud Shell](quickstart.md) e [PowerShell in Cloud Shell](quickstart-powershell.md).

## <a name="pricing"></a>Prezzi
Il computer che ospita Cloud Shell è gratuito, con il prerequisito di una condivisione File di Azure montata. Vengono applicati i normali costi di archiviazione.

## <a name="supported-browsers"></a>Browser supportati
Cloud Shell è consigliato per Chrome, Microsoft Edge e Safari.
Cloud Shell è supportato per Chrome, Firefox, Safari, IE e Microsoft Edge, ma è soggetto a impostazioni specifiche del browser.
