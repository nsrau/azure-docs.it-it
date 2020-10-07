---
author: msmbaldwin
ms.service: key-vault
ms.topic: include
ms.date: 09/03/2020
ms.author: msmbaldwin
ms.openlocfilehash: 9440a33858cb70389e86621ae76be9c08ec56d61
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/05/2020
ms.locfileid: "89482131"
---
1. Assicurarsi di avere [un account Azure con una sottoscrizione attiva](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

1. Installare [Python 2.7 o versione successiva oppure 3.5.3 o versione successiva](https://www.python.org/downloads).

1. Installare l'[interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli).

1. Seguire le istruzioni riportate in [Configurare l'autenticazione per lo sviluppo locale](/azure/developer/python/configure-local-development-environment?tabs=cmd#configure-authentication), con cui si crea un'entità servizio locale e la si rende disponibile al client Azure Key Vault per Python tramite variabili di ambiente. 

    Quando si esegue il codice direttamente in Azure, un'entità servizio separata non è necessaria se l'app usa l'identità gestita.

1. In un terminale o un prompt dei comandi creare una cartella di progetto appropriata e quindi creare e attivare un ambiente virtuale Python come descritto in [Usare ambienti virtuali Python](/azure/developer/python/configure-local-development-environment?tabs=cmd#use-python-virtual-environments)

1. Installare la libreria di identità di Azure Active Directory:

    ```terminal
    pip install azure.identity
    ```
