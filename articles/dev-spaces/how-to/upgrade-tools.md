---
title: Aggiornare gli strumenti di Azure Dev Spaces
services: azure-dev-spaces
ms.date: 07/03/2018
ms.topic: conceptual
ms.custom: devx-track-azurecli
description: Informazioni su come aggiornare gli strumenti da riga di comando di Azure Dev Spaces, l'estensione di Visual Studio Code e l'estensione di Visual Studio
keywords: Docker, Kubernetes, Azure, servizio Azure Kubernetes, servizio Azure Container, contenitori
ms.openlocfilehash: aebe81d58a1bcd44f5766d368dbafa53c81ed2b8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87504331"
---
# <a name="how-to-upgrade-azure-dev-spaces-tools"></a>Aggiornare gli strumenti di Azure Dev Spaces

Se è presente una nuova versione e si sta già usando Azure Dev Spaces, potrebbe essere necessario aggiornare gli strumenti client di Azure Dev Spaces.

## <a name="update-the-azure-cli"></a>Aggiornare l'interfaccia della riga di comando di Azure

Quando si aggiorna l'interfaccia della riga di comando di Azure più recente, è anche possibile ottenere la versione più recente dell'estensione dell'interfaccia della riga di comando per Dev Spaces.

Non è necessario disinstallare la versione precedente: basta individuare solo il download all'indirizzo dell'[interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli?view=azure-cli-latest).


## <a name="update-the-dev-spaces-cli-extension-and-command-line-tools"></a>Aggiornare l'estensione dell'interfaccia della riga di comando di Dev Spaces e gli strumenti da riga di comando

Eseguire il comando seguente:

```azurecli
az aks use-dev-spaces -n <your-aks-cluster> -g <your-aks-cluster-resource-group> --update
```

## <a name="update-the-vs-code-extension"></a>Aggiornare l'estensione di Visual Studio Code

Una volta installata, l'estensione viene aggiornata automaticamente. Per usare delle nuove funzionalità potrebbe essere necessario ricaricare l'estensione. In Visual Studio Code, aprire il riquadro **Estensioni**, scegliere le estensioni **Azure Dev Spaces**, quindi scegliere **Ricarica**.

## <a name="update-visual-studio"></a>Aggiornare Visual Studio

Azure Dev Spaces fa parte del carico di lavoro Sviluppo di Azure ed è incluso in tutti gli aggiornamenti di Visual Studio.

## <a name="next-steps"></a>Passaggi successivi

Testare i nuovi strumenti mediante la creazione di un nuovo cluster. Provare le guide introduttive e le esercitazioni in [Azure Dev Spaces](../index.yml).
