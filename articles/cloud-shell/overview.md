---
title: Panoramica di Azure Cloud Shell | Microsoft Docs
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
ms.date: 12/13/2017
ms.author: juluk
ms.openlocfilehash: 129b43db85a0962005352e0f1e6ad2ad3be2c7d5
ms.sourcegitcommit: 3fca41d1c978d4b9165666bb2a9a1fe2a13aabb6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/15/2017
---
# <a name="overview-of-azure-cloud-shell"></a>Panoramica di Azure Cloud Shell
Azure Cloud Shell è una shell interattiva accessibile dal browser per la gestione delle risorse di Azure.
Offre la flessibilità necessaria per scegliere l'esperienza shell più adatta al proprio modo di lavorare.
Gli utenti Linux possono scegliere un'esperienza Bash, mentre gli utenti Windows possono scegliere PowerShell.

Provare a da shell.azure.com tramite questo pulsante.

[![](https://shell.azure.com/images/launchcloudshell.png "Avviare Shell di Cloud di Azure")](https://shell.azure.com)

Provare dal portale di Azure utilizzando l'icona della Shell di Cloud.

![Avvio del portale](media/overview/portal-launch-icon.png)

## <a name="features"></a>Funzionalità
### <a name="browser-based-shell-experience"></a>Esperienza di shell basata su browser
Cloud Shell consente l'accesso a un'esperienza di riga di comando basata su browser realizzata pensando alle attività di gestione di Azure management.
È possibile sfruttare Cloud Shell per lavorare senza i limiti di un computer locale come solo il cloud può permettere.

### <a name="choice-of-preferred-shell-experience"></a>Scelta dell’esperienza shell preferita
Gli utenti di Linux possono usare Bash nella Shell di Cloud, anche se gli utenti di Windows è possono usare PowerShell nella Shell di Cloud (anteprima) nell'elenco a discesa della shell.

![Bash in Cloud Shell](media/overview/overview-bash-pic.png)

![PowerShell in Cloud Shell (anteprima)](media/overview/overview-ps-pic.png)

### <a name="authenticated-and-configured-azure-workstation"></a>Workstation Azure autenticata e configurata
Shell cloud gestita da Microsoft che viene quindi con i comuni strumenti da riga di comando e supporto del linguaggio. Shell cloud anche in modo sicuro autentica automaticamente per l'accesso immediato alle risorse tramite i cmdlet di Azure 2.0 CLI o Azure PowerShell.

Visualizzare l'elenco completo di strumenti per [l'esperienza Bash](features.md#tools) e [l'esperienza PowerShell (anteprima).](features-powershell.md#tools)

### <a name="multiple-access-points"></a>Più punti di accesso
Cloud Shell è uno strumento flessibile che può essere usato da:
* [Portal.Azure.com](https://portal.azure.com)
* [Shell.Azure.com](https://shell.azure.com)
* [Documentazione "Prova" dell'interfaccia della riga di comando di Azure 2.0](https://docs.microsoft.com/cli/azure/overview?view=azure-cli-latest)
* [App per dispositivi mobili di Azure](https://azure.microsoft.com/features/azure-portal/mobile-app/)
* [Estensione di Account di Azure di codice di Visual Studio](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account)

### <a name="connect-your-microsoft-azure-files-storage"></a>Connettersi all'archiviazione di File di Microsoft Azure
Cloud Shell macchine sono temporanee e richiedono una condivisione di file di Azure deve essere montato come `clouddrive` per rendere persistenti i file.

Primo avvio che della Shell Cloud richiesto per creare una risorsa gruppo di account di archiviazione e file di Azure condividono per conto dell'utente. Questo passaggio è occasionale e verrà automaticamente collegato per tutte le sessioni. Può essere eseguito il mapping di un'unica condivisione file, che verrà utilizzato da Bash e PowerShell in Cloud Shell (anteprima).

#### <a name="create-new-storage"></a>Creare una nuova risorsa di archiviazione
![](media/overview/basic-storage.png)

Può essere creato per conto dell'utente un account di archiviazione con ridondanza locale (LRS) con una condivisione File di Azure. La condivisione File di Azure verrà usata sia per gli ambienti Bash che PowerShell se si sceglie di usare entrambi. Vengono applicati i normali costi di archiviazione.

Verranno create tre risorse per conto dell'utente:
1. Gruppo di risorse denominato: `cloud-shell-storage-<region>`
2. Account di archiviazione denominato: `cs<uniqueGuid>`
3. Condivisione file denominata: `cs-<user>-<domain>-com-<uniqueGuid>`

> [!Note]
> Bash in Cloud Shell crea inoltre un'immagine del disco da 5 GB predefinita per rendere permanente `$Home`. Tutti i file della directory $Home, come le chiavi SSH, vengono mantenuti nell'immagine del disco utente archiviata nella condivisione file di Azure montata. Applicare le procedure consigliate quando si salvano i file nella directory $Home e nella condivisione file di Azure montata.

#### <a name="use-existing-resources"></a>Usare le risorse esistenti
![](media/overview/advanced-storage.png)

Viene data un'opzione avanzata per associare le risorse esistenti a Cloud Shell.
Quando verrà richiesta l'impostazione dell'archiviazione, fare clic su "Mostra impostazioni avanzate" per visualizzare le opzioni aggiuntive.

> [!Note]
> Menu a discesa vengono filtrati per paese Cloud Shell pre-assegnato e gli account di archiviazione con ridondanza locale/archiviazione con ridondanza geografica.

[Altre informazioni sull'archiviazione di Cloud Shell, l'aggiornamento delle condivisioni file di Azure e il caricamento/download di file.](persisting-shell-storage.md)

## <a name="concepts"></a>Concetti
* Cloud Shell viene eseguito in un host temporaneo disponibile per ogni sessione e per ogni utente
* Il timeout di Cloud Shell si verifica dopo 20 minuti senza attività interattiva
* Cloud Shell richiede il montaggio di una condivisione file di Azure
* Cloud Shell usa la stessa condivisione file di Azure sia per Bash che per PowerShell
* A Cloud Shell viene assegnato un computer per ogni account utente
* Bash persiste $Home usando un'immagine di 5 GB contenuta nella condivisione di file
* Vengono impostate le autorizzazioni per un normale utente Linux in Bash

Ulteriori informazioni sulle funzionalità in [Bash in Cloud Shell](features.md) e [PowerShell in Cloud Shell (anteprima)](features-powershell.md).

## <a name="pricing"></a>Prezzi
Il computer che ospita Cloud Shell è gratuito, con il prerequisito di una condivisione File di Azure montata. Vengono applicati i normali costi di archiviazione.

## <a name="next-steps"></a>Passaggi successivi
[Avvio rapido di Bash in Cloud Shell](quickstart.md) <br>
[Guida introduttiva a PowerShell in Cloud Shell (anteprima)](quickstart-powershell.md)