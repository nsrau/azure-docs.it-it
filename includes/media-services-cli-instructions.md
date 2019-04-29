---
title: File di inclusione
description: File di inclusione
services: media-services
author: Juliako
ms.service: media-services
ms.topic: include
ms.date: 01/28/2019
ms.author: juliako
ms.custom: include file
ms.openlocfilehash: 8439da94c770bee313a1ae1d1da5df30683cd2ad
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61224324"
---
## <a name="cli-shell"></a>Shell dell'interfaccia della riga di comando

Per eseguire i comandi dell'interfaccia della riga di comando, è consigliabile usare [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest). **Cloud Shell** è una shell interattiva gratuita che può essere usata per eseguire la procedura di questo articolo. Gli strumenti comuni di Azure sono preinstallati e configurati in Cloud Shell per l'uso con l'account. Offre la flessibilità necessaria per scegliere l'esperienza shell più adatta al proprio modo di lavorare. Gli utenti Linux possono scegliere un'esperienza Bash, mentre gli utenti Windows possono scegliere PowerShell.

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, per questo articolo è necessario eseguire la versione 2.0 o successiva dell'interfaccia della riga di comando di Azure. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest). 

### <a name="login"></a>Login

Per iniziare a usare la shell di interfaccia della riga di comando (nel cloud o in locale), eseguire `az login` per creare una connessione ad Azure.

Se l'interfaccia della riga di comando può aprire il browser predefinito, eseguirà questa operazione e caricherà una pagina di accesso. In caso contrario, sarà necessario aprire una pagina del browser, passare a https://aka.ms/devicelogin e seguire le istruzioni nella riga di comando per immettere un codice di autorizzazione.

### <a name="specify-location-of-files"></a>Specificare il percorso dei file

Molti comandi dell'interfaccia della riga di comando di Servizi multimediali consentono di passare un parametro con un nome file. Se si usa **Cloud Shell**, è possibile caricare il file in clouddrive (tramite Bash o PowerShell). 

![Caricare file]

Se si usa un'interfaccia della riga di comando locale o **Cloud Shell**, è necessario specificare il percorso del file in base al sistema operativo o Cloud Shell (Bash o PowerShell) che si sta usando. Di seguito sono riportati alcuni esempi:

Percorso relativo verso il file (tutti i sistemi operativi)

* `@"mytestfile.json"`
* `@"../mytestfile.json"`

Percorso file assoluto nei sistemi operativi Windows e Linux/Mac

* `@ "/usr/home/mytestfile.json"`
*   `@"c:\tmp\user\mytestfile.json"`

Usare `{file}` se il comando chiede di specificare il percorso del file. Ad esempio: `az ams transform create -a amsaccount -g resourceGroup -n custom --preset .\customPreset.json`. <br/> Usare `@{file}` se il comando caricherà il file specificato. Ad esempio: `az ams account-filter create -a amsaccount -g resourceGroup -n filterName --tracks @tracks.json`.

[Caricare file]: ./media/media-services-cli/upload-download-files.png
