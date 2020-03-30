---
title: Panoramica di Azure Cloud Shell | Microsoft Docs
description: Panoramica di Azure Cloud Shell.
services: ''
documentationcenter: ''
author: maertendMSFT
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 09/03/2019
ms.author: damaerte
ms.openlocfilehash: 513c3da8031774f5f111ee357b5a3c43e1d09d95
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75832482"
---
# <a name="overview-of-azure-cloud-shell"></a>Panoramica di Azure Cloud Shell
Azure Cloud Shell è una shell interattiva, autenticata e accessibile tramite browser per la gestione delle risorse di Azure.
Offre la flessibilità necessaria per scegliere l'esperienza shell più adatta al proprio modo di lavorare, ovvero Bash o PowerShell.

Provare dal sito Web shell.azure.com facendo clic sul pulsante seguente.

[![Lancio dell'incorporamento](https://shell.azure.com/images/launchcloudshell.png "Avviare Azure Cloud Shell")](https://shell.azure.com)

Provare ad accedere dal portale di Azure tramite l'icona Cloud Shell.

![Avvio del portale](media/overview/portal-launch-icon.png)

## <a name="features"></a>Funzionalità

### <a name="browser-based-shell-experience"></a>Esperienza di shell basata su browser
Cloud Shell consente l'accesso a un'esperienza di riga di comando basata su browser realizzata pensando alle attività di gestione di Azure management.
È possibile sfruttare Cloud Shell per lavorare senza i limiti di un computer locale come solo il cloud può permettere.

### <a name="choice-of-preferred-shell-experience"></a>Scelta dell’esperienza shell preferita
Gli utenti possono scegliere tra Bash o PowerShell.Users can choose between Bash or PowerShell.
1. Selezionare **Cloud Shell**.

    ![Icona di Cloud Shell](media/overview/overview-cloudshell-icon.png)

2. Selezionare **Bash** o **PowerShell**.

    ![Scegliere Bash o PowerShell](media/overview/overview-choices.png)

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
I computer Cloud Shell sono temporanei, ma i file vengono resi persistenti in `clouddrive`due modi: tramite un'immagine disco e tramite una condivisione file montata denominata .  Al primo avvio, Cloud Shell chiede di creare un gruppo di risorse, un account di archiviazione e una condivisione di File di Azure per conto dell'utente. Questo passaggio è occasionale e verrà automaticamente collegato per tutte le sessioni. Può essere eseguito il mapping di un'unica condivisione file, che verrà utilizzato da Bash e PowerShell in Cloud Shell.

Ulteriori informazioni su come montare un [account di archiviazione nuovo o esistente](persisting-shell-storage.md) o sui meccanismi di [persistenza usati in Cloud Shell](persisting-shell-storage.md#how-cloud-shell-storage-works).

> [!NOTE]
> Il firewall di archiviazione di Azure non è supportato per gli account di archiviazione della shell cloud.

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
