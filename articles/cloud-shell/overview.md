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
ms.date: 02/15/2018
ms.author: juluk
ms.openlocfilehash: da415f4fb276f3d86d3ff1634a08fa11eabe6e7f
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/21/2018
---
# <a name="overview-of-azure-cloud-shell"></a>Panoramica di Azure Cloud Shell
Azure Cloud Shell è una shell interattiva accessibile dal browser per la gestione delle risorse di Azure.
Offre la flessibilità necessaria per scegliere l'esperienza shell più adatta al proprio modo di lavorare.
Gli utenti Linux possono scegliere un'esperienza Bash, mentre gli utenti Windows possono scegliere PowerShell.

Provare ad accedere dal sito Web shell.azure.com tramite questo pulsante.

[![](https://shell.azure.com/images/launchcloudshell.png "Avvio di Azure Cloud Shell")](https://shell.azure.com)

Provare ad accedere dal portale di Azure tramite l'icona Cloud Shell.

![Avvio del portale](media/overview/portal-launch-icon.png)

## <a name="features"></a>Funzionalità
### <a name="browser-based-shell-experience"></a>Esperienza di shell basata su browser
Cloud Shell consente l'accesso a un'esperienza di riga di comando basata su browser realizzata pensando alle attività di gestione di Azure management.
È possibile sfruttare Cloud Shell per lavorare senza i limiti di un computer locale come solo il cloud può permettere.

### <a name="choice-of-preferred-shell-experience"></a>Scelta dell’esperienza shell preferita
Gli utenti Linux possono usare Bash in Cloud Shell, mentre gli utenti Windows possono usare PowerShell in Cloud Shell (anteprima) scegliendo l'opzione corrispondente nel menu a discesa della shell.

![Bash in Cloud Shell](media/overview/overview-bash-pic.png)

![PowerShell in Cloud Shell (anteprima)](media/overview/overview-ps-pic.png)

### <a name="authenticated-and-configured-azure-workstation"></a>Workstation Azure autenticata e configurata
Cloud Shell è un servizio Microsoft ed è quindi dotato dei più comuni strumenti da riga di comando e del supporto per varie lingue. Cloud Shell esegue anche l'autenticazione automatica sicura per un accesso immediato alle risorse tramite l'interfaccia della riga di comando di Azure 2.0 o i cmdlet di Azure PowerShell.

Visualizzare l'elenco completo di strumenti per [l'esperienza Bash](features.md#tools) e [l'esperienza PowerShell (anteprima).](features-powershell.md#tools)

### <a name="multiple-access-points"></a>Più punti di accesso
Cloud Shell è uno strumento flessibile che può essere usato da:
* [portal.azure.com](https://portal.azure.com)
* [shell.azure.com](https://shell.azure.com)
* [Documentazione "Prova" dell'interfaccia della riga di comando di Azure 2.0](https://docs.microsoft.com/cli/azure/overview?view=azure-cli-latest)
* [App per dispositivi mobili di Azure](https://azure.microsoft.com/features/azure-portal/mobile-app/)
* [Estensione Azure Account per Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account)

### <a name="connect-your-microsoft-azure-files-storage"></a>Connettersi all'archiviazione di File di Microsoft Azure
I computer Cloud Shell sono temporanei ed è quindi necessario montare una condivisione di File di Azure come `clouddrive` per rendere permanenti i file.

Al primo avvio, Cloud Shell chiede di creare un gruppo di risorse, un account di archiviazione e una condivisione di File di Azure per conto dell'utente. Questo passaggio è occasionale e verrà automaticamente collegato per tutte le sessioni. Può essere eseguito il mapping di un'unica condivisione file, che verrà utilizzato da Bash e PowerShell in Cloud Shell (anteprima).

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
> I menu a discesa sono filtrati in base all'area Cloud Shell assegnata in precedenza e agli account di archiviazione con ridondanza locale, geografica o della zona.

[Altre informazioni sull'archiviazione di Cloud Shell, l'aggiornamento delle condivisioni file di Azure e il caricamento/download di file.](persisting-shell-storage.md)

## <a name="concepts"></a>Concetti
* Cloud Shell viene eseguito in un host temporaneo disponibile per ogni sessione e per ogni utente
* Il timeout di Cloud Shell si verifica dopo 20 minuti senza attività interattiva
* Cloud Shell richiede il montaggio di una condivisione file di Azure
* Cloud Shell usa la stessa condivisione file di Azure sia per Bash che per PowerShell
* A Cloud Shell viene assegnato un computer per ogni account utente
* Bash rende permanente $Home usando un'immagine da 5 GB contenuta nella condivisione file
* Vengono impostate le autorizzazioni per un normale utente Linux in Bash

Ulteriori informazioni sulle funzionalità in [Bash in Cloud Shell](features.md) e [PowerShell in Cloud Shell (anteprima)](features-powershell.md).

## <a name="pricing"></a>Prezzi
Il computer che ospita Cloud Shell è gratuito, con il prerequisito di una condivisione File di Azure montata. Vengono applicati i normali costi di archiviazione.

## <a name="next-steps"></a>Passaggi successivi
[Avvio rapido di Bash in Cloud Shell](quickstart.md) <br>
[Guida introduttiva a PowerShell in Cloud Shell (anteprima)](quickstart-powershell.md)