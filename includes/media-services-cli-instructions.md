---
title: File di inclusione
description: File di inclusione
services: media-services
author: Juliako
ms.service: media-services
ms.topic: include
ms.date: 01/25/2019
ms.author: juliako
ms.custom: include file
ms.openlocfilehash: b335cf996de41f4eea15af1899a0c6654c2f679f
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/28/2019
ms.locfileid: "55104984"
---
## <a name="open-cli-shell"></a>Aprire la shell interfaccia della riga di comando

Per eseguire i comandi dell'interfaccia della riga di comando, è consigliabile usare [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest). **Cloud Shell** è una shell interattiva gratuita che può essere usata per eseguire la procedura di questo articolo. Gli strumenti comuni di Azure sono preinstallati e configurati in Cloud Shell per l'uso con l'account. È sufficiente selezionare il pulsante Copia per copiare il codice, incollarlo in Cloud Shell e quindi premere Invio per eseguirlo. Esistono alcuni modi per aprire Cloud Shell:

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, per questo articolo è necessario eseguire la versione 2.0 o successiva dell'interfaccia della riga di comando di Azure. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest). 

### <a name="login"></a>Login

Per iniziare a usare la shell di interfaccia della riga di comando (nel cloud o in locale), eseguire `az login` per creare una connessione ad Azure.

Se l'interfaccia della riga di comando può aprire il browser predefinito, eseguirà questa operazione e caricherà una pagina di accesso. In caso contrario, sarà necessario aprire una pagina del browser, passare a https://aka.ms/devicelogin e seguire le istruzioni nella riga di comando per immettere un codice di autorizzazione.

### <a name="specify-location-of-files"></a>Specificare il percorso dei file

Molti comandi dell'interfaccia della riga di comando di Servizi multimediali consentono di passare un parametro con un nome file. 

Se si usa **Azure Cloud Shell**, caricare il file in **Azure Cloud Shell**. È possibile trovare il pulsante di caricamento/download di file nella parte superiore della finestra della shell. Fare quindi riferimento al file come segue: `@{FileName}.` 

![Caricare file]

Se si usa l'interfaccia della riga di comando di Azure in locale, specificare il percorso completo del file. Ad esempio: `@c:\tracks.json`.


[Caricare file]: ./media/media-services-cli/upload-download-files.png
