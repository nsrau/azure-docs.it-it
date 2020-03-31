---
title: "Avvio rapido: Creare un hub di notifica di Azure con l'interfaccia della riga di comando di Azure | Microsoft Docs"
description: Questa esercitazione illustra come creare un hub di notifica di Azure con l'interfaccia della riga di comando di Azure.
services: notification-hubs
author: dbradish-microsoft
manager: barbkess
editor: sethmanheim
ms.service: notification-hubs
ms.devlang: azurecli
ms.workload: mobile
ms.topic: quickstart
ms.date: 03/17/2020
ms.author: dbradish
ms.reviewer: sethm
ms.lastreviewed: 03/18/2020
ms.openlocfilehash: 830fd33e19a10ec6472650e3d26fec677b82c3d7
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/26/2020
ms.locfileid: "80069484"
---
# <a name="quickstart-create-an-azure-notification-hub-using-the-azure-cli"></a>Guida introduttiva: Creare un hub di notifica di Azure con l'interfaccia della riga di comando di Azure

Hub di notifica di Azure offre un motore di push di facile uso e con scalabilità orizzontale che consente di inviare notifiche a qualsiasi piattaforma (iOS, Android, Windows, Kindle, Baidu e così via) da qualsiasi back-end (cloud o locale). Per altre informazioni sul servizio, vedere [Informazioni su Hub di notifica](notification-hubs-push-notification-overview.md).

In questo argomento di avvio rapido viene creato un hub di notifica con l'interfaccia della riga di comando di Azure. La prima sezione illustra la procedura per creare uno spazio dei nomi per gli hub di notifica ed eseguire query in modo da trovare informazioni sui relativi criteri di accesso. La seconda sezione descrive i passaggi per creare un hub di notifica in uno spazio dei nomi esistente.  Si apprenderà inoltre come creare criteri di accesso personalizzati.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Per Hub di notifica è necessaria la versione 2.0.67 o successiva dell'interfaccia della riga di comando di Azure. Eseguire `az --version` per trovare la versione e le librerie dipendenti installate. Per installare o eseguire l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli).

## <a name="prepare-your-environment"></a>Preparare l'ambiente

1. Accedere.

   Accedere usando il comando [az login](/cli/azure/reference-index#az-login) se si usa un'installazione locale dell'interfaccia della riga di comando.

    ```azurecli-interactive
    az login
    ```

    Seguire le istruzioni visualizzate nel terminale per completare il processo di autenticazione.

2. Installare l'estensione dell'interfaccia della riga di comando di Azure.

   Per eseguire i comandi dell'interfaccia della riga di comando di Azure per gli hub di notifica, installare l'[estensione per Hub di notifica](/cli/azure/ext/notification-hub/notification-hub).  

    ```azurecli-interactive
    az extension add --name notification-hub
   ```

3. Creare un gruppo di risorse.

   Gli hub di notifica di Azure, analogamente a tutte le risorse di Azure, devono essere distribuiti in un gruppo di risorse. I gruppi di risorse consentono di organizzare e gestire le risorse di Azure correlate.

   Per questo argomento di avvio rapido, creare un gruppo di risorse denominato *spnhubrg* nell'area *eastus* con il comando [az group create](/cli/azure/group#az-group-create) seguente:

   ```azurecli-interactive
   az group create --name spnhubrg --location eastus
   ```

## <a name="create-a-notification-hub-namespace"></a>Creare uno spazio dei nomi per gli hub di notifica

1. Creare uno spazio dei nomi per gli hub di notifica

   Uno spazio dei nomi contiene uno o più hub e il nome deve essere univoco in tutte le sottoscrizioni di Azure.  Per verificare la disponibilità dello spazio dei nomi del servizio specificato, usare il comando [az notification-hub namespace check-availability](/cli/azure/ext/notification-hub/notification-hub/namespace#ext-notification-hub-az-notification-hub-namespace-check-availability).  Eseguire il comando [az notification-hub namespace create](/cli/azure/ext/notification-hub/notification-hub/namespace#ext-notification-hub-az-notification-hub-namespace-create) per creare uno spazio dei nomi.  

   ```azurecli-interactive
   #check availability
   az notification-hub namespace check-availability --name spnhubns

   #create the namespace
   az notification-hub namespace create --resource-group spnhubrg --name spnhubns  --location eastus --sku Free
   ```

2. Elencare le chiavi e le stringhe di connessione per i criteri di accesso dello spazio dei nomi.

   Per i nuovi spazi dei nomi viene automaticamente creato un criterio di accesso denominato **RootManageSharedAccessKey**.  Ogni criterio di accesso include due set di chiavi e stringhe di connessione.  Per elencare le chiavi e le stringhe di connessione per lo spazio dei nomi, eseguire il comando [az notification-hub namespace authorization-rule list-keys](/cli/azure/ext/notification-hub/notification-hub/authorization-rule#ext-notification-hub-az-notification-hub-authorization-rule-list-keys).

   ```azurecli-interactive
   az notification-hub namespace authorization-rule list-keys --resource-group spnhubrg --namespace-name spnhubns --name RootManageSharedAccessKey
   ```

## <a name="create-notification-hubs"></a>Creare gli hub di notifica

1. Creare il primo hub di notifica.

   È ora possibile creare un hub di notifica nel nuovo spazio dei nomi.  Eseguire il comando [az notification-hub create](/cli/azure/ext/notification-hub/notification-hub#ext-notification-hub-az-notification-hub-create) per creare un hub di notifica.

   ```azurecli-interactive
   az notification-hub create --resource-group spnhubrg --namespace-name spnhubns --name spfcmtutorial1nhub --location eastus --sku Free
   ```

2. Creare un secondo hub di notifica.

   In un singolo spazio dei nomi è possibile creare più hub di notifica.  Per creare un secondo hub di notifica nello stesso spazio dei nomi, eseguire di nuovo il comando `az notification-hub create` usando un nome di hub diverso.

   ```azurecli-interactive
   az notification-hub create --resource-group spnhubrg --namespace-name spnhubns --name mysecondnhub --location eastus --sku Free
   ```

## <a name="work-with-access-policies"></a>Gestire i criteri di accesso

1. Creare una nuova regola di autorizzazione per un hub di notifica.

   Per ogni nuovo hub di notifica viene automaticamente creato un criterio di accesso.  Per creare e personalizzare i propri criteri di accesso, usare il comando [az notification-hub authorization-rule create](/cli/azure/ext/notification-hub/notification-hub/authorization-rule#ext-notification-hub-az-notification-hub-authorization-rule-create).

   ```azurecli-interactive
   az notification-hub authorization-rule create --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --name spnhub1key --rights Listen Send
   ```

2. Elencare i criteri di accesso per un hub di notifica.

   Per eseguire una query e verificare quali criteri di accesso sono disponibili per un hub di notifica, usare il comando [az notification-hub authorization-rule list](/cli/azure/ext/notification-hub/notification-hub/authorization-rule#ext-notification-hub-az-notification-hub-authorization-rule-list).

   ```azurecli-interactive
   az notification-hub authorization-rule list --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --output table
   ```

   > [!IMPORTANT]
   > Non usare il criterio **DefaultFullSharedAccessSignature** nell'applicazione. Deve essere usato solo nel back-end.  Nell'applicazione client usare solo i criteri di accesso **Listen**.

3. Elencare le chiavi e le stringhe di connessione per i criteri di accesso degli hub di notifica

   Per ogni criterio di accesso esistono due set di chiavi e stringhe di connessione.  Sono necessarie in un secondo momento per gestire le notifiche push.  Per elencare le chiavi e le stringhe di connessione per i criteri di accesso degli hub di notifica, usare il comando [az notification-hub authorization-rule list-keys](/cli/azure/ext/notification-hub/notification-hub/authorization-rule#ext-notification-hub-az-notification-hub-authorization-rule-list-keys).

   ```azurecli-interactive
   #query the keys and connection strings for DefaultListenSharedAccessSignature
   az notification-hub authorization-rule list-keys --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --name DefaultListenSharedAccessSignature --output json

   #query the keys and connection strings for the custom policy
   az notification-hub authorization-rule list-keys --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --name spnhub1key --output table
   ```

   > [!NOTE]
   > Gli [spazi dei nomi per gli hub di notifica](/cli/azure/ext/notification-hub/notification-hub/namespace/authorization-rule#ext-notification-hub-az-notification-hub-namespace-authorization-rule-list-keys) e gli [hub di notifica](/cli/azure/ext/notification-hub/notification-hub/authorization-rule#ext-notification-hub-az-notification-hub-authorization-rule-list-keys) hanno criteri di accesso distinti.  Quando si eseguono query per trovare chiavi e stringhe di connessione, assicurarsi di usare il riferimento corretto all'interfaccia della riga di comando di Azure.

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non sono più necessari, rimuovere il gruppo di risorse e tutte le risorse correlate tramite il comando [az group delete](/cli/azure/group).

```azurecli-interactive
az group delete --name spnhubrg
```

## <a name="see-also"></a>Vedere anche

Informazioni sulle funzionalità complete per la gestione degli hub di notifica con l'interfaccia della riga di comando di Azure.

* [Elenco di riferimento completo dell'interfaccia della riga di comando di Azure per Hub di notifica](/cli/azure/ext/notification-hub/notification-hub)
* [Elenco di riferimento dell'interfaccia della riga di comando di Azure per gli spazi dei nomi di Hub di notifica](/cli/azure/ext/notification-hub/notification-hub/namespace)
* [Elenco di riferimento dell'interfaccia della riga di comando di Azure per le regole di autorizzazione per Hub di notifica](/cli/azure/ext/notification-hub/notification-hub/authorization-rule)
* [Elenco di riferimento dell'interfaccia della riga di comando di Azure per le credenziali di Hub di notifica](/cli/azure/ext/notification-hub/notification-hub/credential)
