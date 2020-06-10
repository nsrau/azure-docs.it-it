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
ms.date: 05/27/2020
ms.author: dbradish
ms.reviewer: sethm
ms.lastreviewed: 03/18/2020
ms.openlocfilehash: d6502985c0267fe6636c606e493533daf17f6b56
ms.sourcegitcommit: d118ad4fb2b66c759b70d4d8a18e6368760da3ad
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/02/2020
ms.locfileid: "84300013"
---
# <a name="quickstart-create-an-azure-notification-hub-using-the-azure-cli"></a>Avvio rapido: Creare un hub di notifica di Azure con l'interfaccia della riga di comando di Azure

Hub di notifica di Azure offre un motore di push di facile uso e con scalabilità orizzontale che consente di inviare notifiche a qualsiasi piattaforma (iOS, Android, Windows, Kindle, Baidu e così via) da qualsiasi back-end (cloud o locale). Per altre informazioni sul servizio, vedere [Informazioni su Hub di notifica](notification-hubs-push-notification-overview.md).

In questo argomento di avvio rapido viene creato un hub di notifica con l'interfaccia della riga di comando di Azure. La prima sezione descrive i passaggi per creare uno spazio dei nomi dell'hub di notifica.  La seconda sezione descrive i passaggi per creare un hub di notifica in uno spazio dei nomi esistente.  Si apprenderà inoltre come creare criteri di accesso personalizzati.  

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Per Hub di notifica è necessaria la versione 2.0.67 o successiva dell'interfaccia della riga di comando di Azure. Eseguire `az --version` per trovare la versione e le librerie dipendenti installate. Per installare o eseguire l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli).

## <a name="prepare-your-environment"></a>Preparare l'ambiente

1. Accedere.

   Accedere usando il comando [az login](/cli/azure/reference-index#az-login) se si usa un'installazione locale dell'interfaccia della riga di comando.

    ```azurecli
    az login
    ```

    Seguire le istruzioni visualizzate nel terminale per completare il processo di autenticazione.

2. Installare l'estensione dell'interfaccia della riga di comando di Azure.

   Quando si usano riferimenti a estensioni per l'interfaccia della riga di comando di Azure, è prima di tutto necessario installare l'estensione.  Le estensioni dell'interfaccia della riga di comando di Azure offrono l'accesso a comandi sperimentali e non definitivi che non sono stati ancora distribuiti come parte dell'interfaccia della riga di comando di base.  Per altre informazioni sulle estensioni, incluse le procedure di aggiornamento e disinstallazione, vedere [Usare le estensioni con l'interfaccia della riga di comando di Azure](/cli/azure/azure-cli-extensions-overview).

   Installare l'[estensione per Hub di notifica](/cli/azure/ext/notification-hub/notification-hub) eseguendo questo comando:

    ```azurecli
    az extension add --name notification-hub
   ```

3. Creare un gruppo di risorse.

   L'infrastruttura Hub di notifica di Azure, analogamente a tutte le risorse di Azure, deve essere distribuita in un gruppo di risorse. I gruppi di risorse consentono di organizzare e gestire le risorse di Azure correlate.

   Per questo argomento di avvio rapido, creare un gruppo di risorse denominato _spnhubrg_ nell'area _eastus_ con il comando [az group create](/cli/azure/group#az-group-create) seguente:

   ```azurecli
   az group create --name spnhubrg --location eastus
   ```

## <a name="create-a-notification-hub-namespace"></a>Creare uno spazio dei nomi per gli hub di notifica

1. Creare uno spazio dei nomi per gli hub di notifica.

   Uno spazio dei nomi contiene uno o più hub e **il nome deve essere univoco in tutte le sottoscrizioni di Azure e deve contenere almeno sei caratteri**.  Per verificare la disponibilità di un nome, usare il comando [az notification-hub namespace check-availability](/cli/azure/ext/notification-hub/notification-hub/namespace#ext-notification-hub-az-notification-hub-namespace-check-availability).

   ```azurecli
   az notification-hub namespace check-availability --name spnhubns
   ```

   L'interfaccia della riga di comando di Azure risponderà alla richiesta di disponibilità mostrando l'output seguente della console:

   ```output
   {
   "id": "/subscriptions/yourSubscriptionID/providers/Microsoft.NotificationHubs/checkNamespaceAvailability",
   "isAvailiable": true,
   "location": null,
   "name": "spnhubns",
   "properties": false,
   "sku": null,
   "tags": null,
   "type": "Microsoft.NotificationHubs/namespaces/checkNamespaceAvailability"
   }
   ```

   Si noti la seconda riga nella risposta dell'interfaccia della riga di comando di Azure, `"isAvailable": true`.  Questa riga corrisponderà a `false` se il nome specificato per lo spazio dei nomi è disponibile.  Dopo avere confermato la disponibilità del nome, eseguire il comando [az notification-hub namespace create](/cli/azure/ext/notification-hub/notification-hub/namespace#ext-notification-hub-az-notification-hub-namespace-create) per creare lo spazio dei nomi.  

   ```azurecli
   az notification-hub namespace create --resource-group spnhubrg --name spnhubns  --location eastus --sku Free
   ```

   Se il valore `--name` specificato al comando `az notification-hub namespace create` non è disponibile o non rispetta le [Regole di denominazione e restrizioni per le risorse di Azure](/azure/azure-resource-manager/management/resource-name-rules), l'interfaccia della riga di comando di Azure risponderà con l'output seguente della console:

   ```output
   #the name is not available
   The specified name is not available. For more information visit https://aka.ms/eventhubsarmexceptions.

   #the name is invalied
   The specified service namespace is invalid.
   ```

   Se il nome specificato al primo tentativo ha esito negativo, selezionare un nome diverso per lo spazio dei nomi e ripetere l'esecuzione del comando `az notification-hub namespace create`.

   > [!NOTE]
   > A partire da questo passaggio sarà necessario sostituire il valore del parametro `--namespace` in ogni comando dell'interfaccia della riga di comando di Azure copiato da questo avvio rapido.

2. Ottenere un elenco di spazi dei nomi.

   Per visualizzare informazioni dettagliate sul nuovo spazio dei nomi, usare il comando [az notification-hub namespace list](/azure/ext/notification-hub/notification-hub/namespace?view=azure-cli-latest#ext-notification-hub-az-notification-hub-namespace-list).  Il parametro `--resource-group` è facoltativo se si vogliono visualizzare tutti gli spazi dei nomi per una sottoscrizione.

   ```azurecli
   az notification-hub namespace list --resource-group spnhubrg
   ```

## <a name="create-notification-hubs"></a>Creare gli hub di notifica

1. Creare il primo hub di notifica.

   È ora possibile creare uno o più hub di notifica nel nuovo spazio dei nomi.  Eseguire il comando [az notification-hub create](/cli/azure/ext/notification-hub/notification-hub#ext-notification-hub-az-notification-hub-create) per creare un hub di notifica.

   ```azurecli
   az notification-hub create --resource-group spnhubrg --namespace-name spnhubns --name spfcmtutorial1nhub --location eastus --sku Free
   ```

2. Creare un secondo hub di notifica.

   In un singolo spazio dei nomi è possibile creare più hub di notifica.  Per creare un secondo hub di notifica nello stesso spazio dei nomi, eseguire di nuovo il comando `az notification-hub create` usando un nome di hub diverso.

   ```azurecli
   az notification-hub create --resource-group spnhubrg --namespace-name spnhubns --name mysecondnhub --location eastus --sku Free
   ```

3. Ottenere un elenco di hub di notifica.

   L'interfaccia della riga di comando di Azure restituisce un messaggio di esito positivo o di errore con ogni comando eseguito. Risulta comunque utile potere eseguire una query per ottenere un elenco di hub di notifica.  Il comando [az notification-hub list](/azure/ext/notification-hub/notification-hub?view=azure-cli-latest#ext-notification-hub-az-notification-hub-list) è stato progettato per questa finalità.

   ```azurecli
   az notification-hub list --resource-group spnhubrg --namespace-name spnhubns --output table
   ```

## <a name="work-with-notification-hub-access-policies"></a>Usare i criteri di accesso per gli hub di notifica

1. Elencare i criteri di accesso per un hub di notifica.

   Hub di notifica di Azure usa la [sicurezza basata su firma di accesso condiviso](/azure/notification-hubs/notification-hubs-push-notification-security) tramite i criteri di accesso.  Quando si crea un hub di notifica, vengono creati automaticamente due criteri.  Le stringhe di connessione da questi criteri sono necessarie per configurare le notifiche push.  Il comando [az notification-hub authorization-rule list](/cli/azure/ext/notification-hub/notification-hub/authorization-rule#ext-notification-hub-az-notification-hub-authorization-rule-list) fornisce un elenco di nomi di criteri e dei rispettivi gruppi di risorse.

   ```azurecli
   az notification-hub authorization-rule list --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --output table
   ```

   > [!IMPORTANT]
   > Non usare il criterio _DefaultFullSharedAccessSignature_ nell'applicazione. Deve essere usato solo nel back-end.  Nell'applicazione client usare solo i criteri di accesso `Listen`.

2. Creare una nuova regola di autorizzazione per un hub di notifica.

   Se si vogliono creare regole di autorizzazione aggiuntive con nomi significativi, è possibile creare e configurare criteri di accesso personalizzati usando il comando [az notification-hub authorization-rule create](/cli/azure/ext/notification-hub/notification-hub/authorization-rule#ext-notification-hub-az-notification-hub-authorization-rule-create).  Il parametro `--rights` è un elenco delimitato da spazi delle autorizzazioni da assegnare.

   ```azurecli
   az notification-hub authorization-rule create --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --name spnhub1key --rights Listen Manage Send
   ```

3. Elencare le chiavi e le stringhe di connessione per i criteri di accesso degli hub di notifica

   Per ogni criterio di accesso esistono due set di chiavi e stringhe di connessione.  Saranno necessarie in seguito per [configurare un hub di notifica](/azure/notification-hubs/configure-notification-hub-portal-pns-settings).  Per elencare le chiavi e le stringhe di connessione per i criteri di accesso degli hub di notifica, usare il comando [az notification-hub authorization-rule list-keys](/cli/azure/ext/notification-hub/notification-hub/authorization-rule#ext-notification-hub-az-notification-hub-authorization-rule-list-keys).

   ```azurecli
   #query the keys and connection strings for DefaultListenSharedAccessSignature
   az notification-hub authorization-rule list-keys --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --name DefaultListenSharedAccessSignature --output table
   ```

   ```azurecli
   #query the keys and connection strings for a custom policy
   az notification-hub authorization-rule list-keys --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --name spnhub1key --output table
   ```

   > [!NOTE]
   > Gli [spazi dei nomi per gli hub di notifica](/cli/azure/ext/notification-hub/notification-hub/namespace/authorization-rule#ext-notification-hub-az-notification-hub-namespace-authorization-rule-list-keys) e gli [hub di notifica](/cli/azure/ext/notification-hub/notification-hub/authorization-rule#ext-notification-hub-az-notification-hub-authorization-rule-list-keys) hanno criteri di accesso distinti.  Quando si eseguono query per trovare chiavi e stringhe di connessione, assicurarsi di usare il riferimento corretto all'interfaccia della riga di comando di Azure.

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non sono più necessari, rimuovere il gruppo di risorse e tutte le risorse correlate tramite il comando [az group delete](/cli/azure/group).

```azurecli
az group delete --name spnhubrg
```

## <a name="next-steps"></a>Passaggi successivi

* In questa guida introduttiva è stato creato un hub di notifica. Per informazioni su come configurare l'hub con le impostazioni PNS (Platform Notification System), vedere [Configurare notifiche push in un hub di notifica](configure-notification-hub-portal-pns-settings.md)

* Informazioni sulle funzionalità complete per la gestione degli hub di notifica con l'interfaccia della riga di comando di Azure.

  [Elenco completo di informazioni di riferimento per Hub di notifica](/cli/azure/ext/notification-hub/notification-hub)

  [Elenco di informazioni di riferimento sugli spazi dei nomi di Hub di notifica](/cli/azure/ext/notification-hub/notification-hub/namespace)

  [Elenco di informazioni di riferimento sulle regole di autorizzazione di Hub di notifica](/cli/azure/ext/notification-hub/notification-hub/authorization-rule)

  [Elenco di informazioni di riferimento sulle credenziali di Hub di notifica](/cli/azure/ext/notification-hub/notification-hub/credential)
