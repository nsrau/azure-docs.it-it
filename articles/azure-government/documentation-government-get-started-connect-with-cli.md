---
title: Connettersi ad Azure per enti pubblici con l&quot;interfaccia della riga di comando di Azure | Documentazione Microsoft
description: Informazioni sulla gestione della sottoscrizione in Azure per enti pubblici tramite la connessione all&quot;interfaccia della riga di comando di Azure
services: azure-government
cloud: gov
documentationcenter: 
author: zakramer
manager: liki
ms.assetid: c7cbe993-375e-4aed-9aa5-2f4b2111f71c
ms.service: azure-government
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 02/13/2017
ms.author: zakramer
translationtype: Human Translation
ms.sourcegitcommit: 6b29e98aa3458a76e186234c2e9800294902bea9
ms.openlocfilehash: 6e070870a93d2636f076b339dd401358735a9bd0
ms.lasthandoff: 02/21/2017


---


# <a name="connect-to-azure-government-with-azure-cli"></a>Connettersi ad Azure per enti pubblici con l'interfaccia della riga di comando di Azure

Per usare l'interfaccia della riga di comando di Azure, è necessario connettersi ad Azure per enti pubblici invece della versione pubblica di Azure. L'interfaccia della riga di comando di Azure consente di gestire una sottoscrizione di grandi dimensioni tramite script o di accedere alle funzionalità attualmente non disponibili nel portale di Azure. Se è stata usata l'interfaccia della riga di comando di Azure nella versione pubblica di Azure, è essenzialmente la stessa cosa.  Le differenze in Azure per enti pubblici sono le seguenti:

Esistono diversi modi per [installare l'interfaccia della riga di comando di Azure](https://docs.microsoft.com/en-us/azure/xplat-cli-install). Se Node è già installato, il modo più semplice consiste nell'installare il pacchetto npm:

Per installare l'interfaccia della riga di comando da un pacchetto npm, verificare che siano state caricate e installate le [versioni più recenti di Node.js e npm](https://nodejs.org/en/download/package-manager/). Eseguire quindi il comando **npm install** per installare il pacchetto dell'interfaccia della riga di comando di Azure:

```bash
npm install -g azure-cli
```

Nelle distribuzioni di Linux potrebbe essere necessario usare **sudo** per la corretta esecuzione del comando **npm**, come segue:

```bash
sudo npm install -g azure-cli
```

> [!NOTE]
> Se è necessario installare o aggiornare Node.js e npm nella distribuzione o nel sistema operativo Linux, è consigliabile installare la versione più recente di Node.js LTS (4.x). Se si usa una versione precedente, potrebbero verificarsi errori di installazione.


Dopo aver installato l'interfaccia della riga di comando di Azure, è necessario accedere ad Azure per enti pubblici:

```
azure login --username your-user-name@your-gov-tenant.onmicrosoft.com  --environment AzureUSGovernment
```

Ad accesso avvenuto, è possibile eseguire normalmente i comandi dell'interfaccia della riga di comando di Azure:

```
azure webapp list my-resource-group
```
