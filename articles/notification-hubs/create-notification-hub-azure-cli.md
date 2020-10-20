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
ms.reviewer: thsomasu
ms.lastreviewed: 03/18/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: ebed70a99e8e906db802099e052e69ab2575d737
ms.sourcegitcommit: fbb620e0c47f49a8cf0a568ba704edefd0e30f81
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91875029"
---
# <a name="quickstart-create-an-azure-notification-hub-using-the-azure-cli"></a>Creare un hub di notifica di Azure con l'interfaccia della riga di comando di Azure

Hub di notifica di Azure offre un motore di push di facile uso e con scalabilità orizzontale che consente di inviare notifiche a qualsiasi piattaforma (iOS, Android, Windows, Kindle, Baidu e così via) da qualsiasi back-end (cloud o locale). Per altre informazioni sul servizio, vedere [Informazioni su Hub di notifica](notification-hubs-push-notification-overview.md).

In questo argomento di avvio rapido viene creato un hub di notifica con l'interfaccia della riga di comando di Azure. La prima sezione descrive la procedura per creare uno spazio dei nomi di Hub di notifica. La seconda sezione descrive i passaggi per creare un hub di notifica in uno spazio dei nomi esistente. Si apprenderà inoltre come creare criteri di accesso personalizzati.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

> [!IMPORTANT]
> Per Hub di notifica è necessaria la versione 2.0.67 o successiva dell'interfaccia della riga di comando di Azure. Eseguire [az version](/cli/azure/reference-index?#az_version) per trovare la versione e le librerie dipendenti installate. Per eseguire l'aggiornamento alla versione più recente, eseguire [az upgrade](/cli/azure/reference-index?#az_upgrade).

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

L'infrastruttura Hub di notifica di Azure, analogamente a tutte le risorse di Azure, deve essere distribuita in un gruppo di risorse.  I gruppi di risorse consentono di organizzare e gestire le risorse di Azure correlate.  Per altre informazioni sui gruppi di risorse, vedere [Informazioni su Azure Resource Manager](/azure/azure-resource-manager/management/overview).

Per questo argomento di avvio rapido, creare un gruppo di risorse denominato **spnhubrg** nell'area **eastus** con il comando [az group create](/cli/azure/group#az-group-create) seguente.

```azurecli
az group create --name spnhubrg --location eastus
```

## <a name="create-a-notification-hubs-namespace"></a>Creare uno spazio dei nomi per Hub di notifica

1. Creare uno spazio dei nomi per gli hub di notifica.

   Uno spazio dei nomi contiene uno o più hub e il nome deve essere univoco in tutte le sottoscrizioni di Azure e deve contenere almeno sei caratteri. Per verificare la disponibilità di un nome, usare il comando [az notification-hub namespace check-availability](/cli/azure/ext/notification-hub/notification-hub/namespace#ext-notification-hub-az-notification-hub-namespace-check-availability).

   ```azurecli
   az notification-hub namespace check-availability --name spnhubns
   ```

   L'interfaccia della riga di comando di Azure risponde alla richiesta di disponibilità mostrando l'output della console seguente:

   ```shell
   {
   "id": "/subscriptions/yourSubscriptionID/providers/Microsoft.NotificationHubs/checkNamespaceAvailability",
   "isAvailable": true,
   "location": null,
   "name": "spnhubns",
   "properties": false,
   "sku": null,
   "tags": null,
   "type": "Microsoft.NotificationHubs/namespaces/checkNamespaceAvailability"
   }
   ```

   Si noti la seconda riga nella risposta dell'interfaccia della riga di comando di Azure, `"isAvailable": true`. Questa riga corrisponde a `false` se il nome specificato per lo spazio dei nomi è disponibile. Dopo avere confermato la disponibilità del nome, eseguire il comando [az notification-hub namespace create](/cli/azure/ext/notification-hub/notification-hub/namespace#ext-notification-hub-az-notification-hub-namespace-create) per creare lo spazio dei nomi.  

   ```azurecli
   az notification-hub namespace create --resource-group spnhubrg --name spnhubns  --location eastus --sku Free
   ```

   Se il valore `--name` fornito al comando `az notification-hub namespace create` non è disponibile o non rispetta le [regole di denominazione e restrizioni per le risorse di Azure](../azure-resource-manager/management/resource-name-rules.md), l'interfaccia della riga di comando di Azure risponde con l'output della console seguente:

   ```shell
   #the name is not available
   The specified name is not available. For more information visit https://aka.ms/eventhubsarmexceptions.

   #the name is invalid
   The specified service namespace is invalid.
   ```

   Se il nome specificato al primo tentativo ha esito negativo, selezionare un nome diverso per lo spazio dei nomi e ripetere l'esecuzione del comando `az notification-hub namespace create`.

   > [!NOTE]
   > A partire da questo passaggio è necessario sostituire il valore del parametro `--namespace` in ogni comando dell'interfaccia della riga di comando di Azure copiato da questa guida di avvio rapido.

2. Ottenere un elenco di spazi dei nomi.

   Per visualizzare informazioni dettagliate sul nuovo spazio dei nomi, usare il comando [az notification-hub namespace list](/cli/azure/ext/notification-hub/notification-hub/namespace#ext-notification-hub-az-notification-hub-namespace-list). Il parametro `--resource-group` è facoltativo se si vogliono visualizzare tutti gli spazi dei nomi per una sottoscrizione.

   ```azurecli
   az notification-hub namespace list --resource-group spnhubrg
   ```

## <a name="create-notification-hubs"></a>Creare gli hub di notifica

1. Creare il primo hub di notifica.

   È ora possibile creare uno o più hub di notifica nel nuovo spazio dei nomi. Eseguire il comando [az notification-hub create](/cli/azure/ext/notification-hub/notification-hub#ext-notification-hub-az-notification-hub-create) per creare un hub di notifica.

   ```azurecli
   az notification-hub create --resource-group spnhubrg --namespace-name spnhubns --name spfcmtutorial1nhub --location eastus --sku Free
   ```

2. Creare un secondo hub di notifica.

   In un singolo spazio dei nomi è possibile creare più hub di notifica. Per creare un secondo hub di notifica nello stesso spazio dei nomi, eseguire di nuovo il comando `az notification-hub create` usando un nome di hub diverso.

   ```azurecli
   az notification-hub create --resource-group spnhubrg --namespace-name spnhubns --name mysecondnhub --location eastus --sku Free
   ```

3. Ottenere un elenco di hub di notifica.

   L'interfaccia della riga di comando di Azure restituisce un messaggio di esito positivo o di errore con ogni comando eseguito. Risulta comunque utile potere eseguire una query per ottenere un elenco di hub di notifica. Il comando [az notification-hub list](/cli/azure/ext/notification-hub/notification-hub#ext-notification-hub-az-notification-hub-list) è stato progettato per questa finalità.

   ```azurecli
   az notification-hub list --resource-group spnhubrg --namespace-name spnhubns --output table
   ```

## <a name="work-with-access-policies"></a>Gestire i criteri di accesso

1. Hub di notifica di Azure usa la [sicurezza basata su firma di accesso condiviso](./notification-hubs-push-notification-security.md) tramite i criteri di accesso. Quando si crea un hub di notifica, vengono creati automaticamente due criteri. Le stringhe di connessione da questi criteri sono necessarie per configurare le notifiche push. Il comando [az notification-hub authorization-rule list](/cli/azure/ext/notification-hub/notification-hub/authorization-rule#ext-notification-hub-az-notification-hub-authorization-rule-list) fornisce un elenco di nomi di criteri e dei rispettivi gruppi di risorse.

   ```azurecli
   az notification-hub authorization-rule list --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --output table
   ```

   > [!IMPORTANT]
   > Non usare il criterio _DefaultFullSharedAccessSignature_ nell'applicazione. Questo criterio deve essere usato solo nel back-end. Nell'applicazione client usare solo i criteri di accesso `Listen`.

2. Se si vogliono creare regole di autorizzazione aggiuntive con nomi significativi, è possibile creare e configurare criteri di accesso personalizzati usando il comando [az notification-hub authorization-rule create](/cli/azure/ext/notification-hub/notification-hub/authorization-rule#ext-notification-hub-az-notification-hub-authorization-rule-create). Il parametro `--rights` è un elenco delimitato da spazi delle autorizzazioni da assegnare.

   ```azurecli
   az notification-hub authorization-rule create --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --name spnhub1key --rights Listen Manage Send
   ```

3. Per ogni criterio di accesso esistono due set di chiavi e stringhe di connessione. Saranno necessarie in seguito per [configurare un hub di notifica](./configure-notification-hub-portal-pns-settings.md). Per elencare le chiavi e le stringhe di connessione per i criteri di accesso di Hub di notifica, usare il comando [az notification-hub authorization-rule list-keys](/cli/azure/ext/notification-hub/notification-hub/authorization-rule#ext-notification-hub-az-notification-hub-authorization-rule-list-keys).

   ```azurecli
   # query the keys and connection strings for DefaultListenSharedAccessSignature
   az notification-hub authorization-rule list-keys --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --name DefaultListenSharedAccessSignature --output table
   ```

   ```azurecli
   # query the keys and connection strings for a custom policy
   az notification-hub authorization-rule list-keys --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --name spnhub1key --output table
   ```

   > [!NOTE]
   > Gli [spazi dei nomi per Hub di notifica](/cli/azure/ext/notification-hub/notification-hub/namespace/authorization-rule#ext-notification-hub-az-notification-hub-namespace-authorization-rule-list-keys) e gli [hub di notifica](/cli/azure/ext/notification-hub/notification-hub/authorization-rule#ext-notification-hub-az-notification-hub-authorization-rule-list-keys) hanno criteri di accesso distinti. Quando si eseguono query per trovare chiavi e stringhe di connessione, assicurarsi di usare il riferimento corretto all'interfaccia della riga di comando di Azure.

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non sono più necessari, rimuovere il gruppo di risorse e tutte le risorse correlate tramite il comando [az group delete](/cli/azure/group):

```azurecli
az group delete --name spnhubrg
```

## <a name="next-steps"></a>Passaggi successivi

* In questa guida introduttiva è stato creato un hub di notifica. Per informazioni su come configurare l'hub con le impostazioni PNS (Platform Notification System), vedere [Configurare notifiche push in un hub di notifica](configure-notification-hub-portal-pns-settings.md)

* Informazioni sulle funzionalità complete per la gestione degli hub di notifica con l'interfaccia della riga di comando di Azure:

  [Elenco completo di informazioni di riferimento per Hub di notifica](/cli/azure/ext/notification-hub/notification-hub)

  [Elenco di informazioni di riferimento sugli spazi dei nomi di Hub di notifica](/cli/azure/ext/notification-hub/notification-hub/namespace)

  [Elenco di informazioni di riferimento sulle regole di autorizzazione di Hub di notifica](/cli/azure/ext/notification-hub/notification-hub/authorization-rule)

  [Elenco di informazioni di riferimento sulle credenziali di Hub di notifica](/cli/azure/ext/notification-hub/notification-hub/credential)
