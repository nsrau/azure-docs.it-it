---
title: Panoramica di Azure Cloud Shell | Microsoft Docs
description: Panoramica di Azure Cloud Shell.
services: ''
documentationcenter: ''
author: jluk
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 09/04/2018
ms.author: damaerte
ms.openlocfilehash: 5608b3e0f9b98db62d22245de5a864f757f48799
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/02/2019
ms.locfileid: "57243976"
---
# <a name="overview-of-azure-cloud-shell"></a>Panoramica di Azure Cloud Shell
Azure Cloud Shell è una shell interattiva accessibile dal browser per la gestione delle risorse di Azure.
Offre la flessibilità necessaria per scegliere l'esperienza shell più adatta al proprio modo di lavorare.
Gli utenti Linux possono scegliere un'esperienza Bash, mentre gli utenti Windows possono scegliere PowerShell.

Provare dal sito Web shell.azure.com facendo clic sul pulsante seguente.

[![Incorporare l'avvio](https://shell.azure.com/images/launchcloudshell.png "Avvio di Azure Cloud Shell")](https://shell.azure.com)

Provare ad accedere dal portale di Azure tramite l'icona Cloud Shell.

![Avvio del portale](media/overview/portal-launch-icon.png)

## <a name="features"></a>Funzionalità

### <a name="browser-based-shell-experience"></a>Esperienza di shell basata su browser
Cloud Shell consente l'accesso a un'esperienza di riga di comando basata su browser realizzata pensando alle attività di gestione di Azure management.
È possibile sfruttare Cloud Shell per lavorare senza i limiti di un computer locale come solo il cloud può permettere.

### <a name="choice-of-preferred-shell-experience"></a>Scelta dell’esperienza shell preferita
Gli utenti possono scegliere tra Bash o PowerShell dal menu a discesa della shell.

![Bash in Cloud Shell](media/overview/overview-bash-pic.png)

![PowerShell in Azure Cloud Shell](media/overview/overview-ps-pic.png)

### <a name="authenticated-and-configured-azure-workstation"></a>Workstation Azure autenticata e configurata
Cloud Shell è un servizio Microsoft ed è quindi dotato dei più comuni strumenti da riga di comando e del supporto per varie lingue. Cloud Shell esegue anche l'autenticazione automatica sicura per un accesso immediato alle risorse tramite l'interfaccia della riga di comando di Azure o i cmdlet di Azure PowerShell.

Vedere l'[elenco completo degli strumenti installati in Cloud Shell](features.md#tools).

### <a name="integrated-cloud-shell-editor"></a>Editor Cloud Shell integrato
Cloud Shell offre un editor di testo con interfaccia grafica integrata basato sull'editor open source Monaco. Creare e modificare i file di configurazione eseguendo `code .` per una distribuzione semplice tramite l'interfaccia della riga di comando di Azure o Azure PowerShell.

[Altre informazioni sull'editor Cloud Shell](using-cloud-shell-editor.md).

### <a name="integrated-with-docsmicrosoftcom"></a>Integrato con docs.microsoft.com

È possibile usare Cloud Shell direttamente dalla documentazione ospitata in [docs.microsoft.com](https://docs.microsoft.com). È integrato nella documentazione di [Microsoft Learn](https://docs.microsoft.com/learn/), [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) e [dell'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure) - Fare clic sul pulsante "Prova" in un frammento di codice per aprire l'esperienza immersiva di shell. 

### <a name="multiple-access-points"></a>Più punti di accesso
Cloud Shell è uno strumento flessibile che può essere usato da:
* [portal.azure.com](https://portal.azure.com)
* [shell.azure.com](https://shell.azure.com)
* [Documentazione dell'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure)
* [Documentazione di Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)
* [App per dispositivi mobili di Azure](https://azure.microsoft.com/features/azure-portal/mobile-app/)
* [Estensione Azure Account per Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account)

### <a name="connect-your-microsoft-azure-files-storage"></a>Connettersi all'archiviazione di File di Microsoft Azure
I computer Cloud Shell sono temporanei ed è quindi necessario montare una condivisione di File di Azure nuova o esistente come `clouddrive` per rendere permanenti i file.

Al primo avvio, Cloud Shell chiede di creare un gruppo di risorse, un account di archiviazione e una condivisione di File di Azure per conto dell'utente. Questo passaggio è occasionale e verrà automaticamente collegato per tutte le sessioni. Può essere eseguito il mapping di un'unica condivisione file, che verrà utilizzato da Bash e PowerShell in Cloud Shell.

Continuare a leggere per informazioni su come montare un [account di archiviazione nuovo o esistente](persisting-shell-storage.md).

## <a name="concepts"></a>Concetti
* Cloud Shell viene eseguito in un host temporaneo disponibile per ogni sessione e per ogni utente
* Il timeout di Cloud Shell si verifica dopo 20 minuti senza attività interattiva
* Cloud Shell richiede il montaggio di una condivisione file di Azure
* Cloud Shell usa la stessa condivisione file di Azure sia per Bash che per PowerShell
* A Cloud Shell viene assegnato un computer per ogni account utente
* Cloud Shell rende permanente $HOME usando un'immagine da 5 GB contenuta nella condivisione file
* Vengono impostate le autorizzazioni per un normale utente Linux in Bash

Ulteriori informazioni sulle funzionalità in [Bash in Cloud Shell](features.md) e [PowerShell in Cloud Shell](features-powershell.md).

## <a name="pricing"></a>Prezzi
Il computer che ospita Cloud Shell è gratuito, con il prerequisito di una condivisione File di Azure montata. Vengono applicati i normali costi di archiviazione.

## <a name="next-steps"></a>Passaggi successivi
[Avvio rapido di Bash in Cloud Shell](quickstart.md) <br>
[Guida introduttiva a PowerShell in Cloud Shell](quickstart-powershell.md)
