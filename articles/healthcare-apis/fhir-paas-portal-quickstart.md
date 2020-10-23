---
title: "Avvio rapido: Distribuire l'API di Azure per FHIR con il portale di Azure"
description: Questa guida di avvio rapido illustra come distribuire l'API di Azure per FHIR e configurare le impostazioni usando il portale di Azure.
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: quickstart
ms.date: 03/15/2020
ms.author: cavoeg
ms.openlocfilehash: f3b3430ef7669022e532166848cb16d6aa66efe8
ms.sourcegitcommit: 8d8deb9a406165de5050522681b782fb2917762d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/20/2020
ms.locfileid: "92220789"
---
# <a name="quickstart-deploy-azure-api-for-fhir-using-azure-portal"></a>Avvio rapido: Distribuire l'API di Azure per FHIR con il portale di Azure

Questa guida di avvio rapido illustra come distribuire l'API di Azure per FHIR con il portale di Azure.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="create-new-resource"></a>Create new resource (Crea nuova risorsa)

Aprire il [portale di Azure](https://portal.azure.com) e fare clic su **Crea una risorsa**

![Creare una risorsa](media/quickstart-paas-portal/portal-create-resource.png)

## <a name="search-for-azure-api-for-fhir"></a>Cercare l'API di Azure per FHIR

È possibile trovare l'API di Azure per FHIR digitando "FHIR" nella casella di ricerca:

:::image type="content" source="media/quickstart-paas-portal/portal-search-healthcare-apis.png" alt-text="Cercare le API per i servizi sanitari":::

## <a name="create-azure-api-for-fhir-account"></a>Creare l'account API di Azure per FHIR

Selezionare **crea** per creare un nuovo account API di Azure per FHIR:

:::image type="content" source="media/quickstart-paas-portal/portal-create-healthcare-apis.png" alt-text="Cercare le API per i servizi sanitari":::

## <a name="enter-account-details"></a>Immettere i dettagli dell'account

Selezionare un gruppo di risorse esistente o crearne uno nuovo, scegliere un nome per l'account, infine fare clic su **Rivedi e crea**:

:::image type="content" source="media/quickstart-paas-portal/portal-new-healthcareapi-details.png" alt-text="Cercare le API per i servizi sanitari":::

Confermare la creazione e attendere la distribuzione dell'API FHIR.

## <a name="additional-settings-optional"></a>Impostazioni aggiuntive (facoltativo)

È anche possibile fare clic su **Avanti: Impostazioni aggiuntive** per visualizzare le impostazioni di autenticazione. La configurazione predefinita per l'API di Azure per FHIR consiste nell'[usare il Controllo degli accessi in base al ruolo Azure per l'assegnazione di ruoli del piano dati](configure-azure-rbac.md). Se viene usata questa modalità di configurazione, l'opzione "Autorità" per il servizio FHIR verrà impostata sul tenant di Azure Active Directory della sottoscrizione:

:::image type="content" source="media/rbac/confirm-azure-rbac-mode-create.png" alt-text="Cercare le API per i servizi sanitari":::

Si noti che la casella per l'immissione degli ID oggetto consentiti è disabilitata perché in questo caso viene usato Controllo degli accessi in base al ruolo Azure per la configurazione delle assegnazioni di ruolo.

Se si vuole configurare il servizio FHIR per usare un tenant esterno o secondario di Azure Active Directory, è possibile modificare il valore di Autorità e immettere ID oggetto per utenti e gruppi che devono essere autorizzati ad accedere al server. Per altre informazioni, vedere la guida alla [configurazione del Controllo degli accessi in base al ruolo locale](configure-local-rbac.md).

## <a name="fetch-fhir-api-capability-statement"></a>Recuperare la dichiarazione di funzionalità dell'API FHIR

Per verificare l'avvenuto provisioning del nuovo account API FHIR, recuperare una dichiarazione di funzionalità indirizzando un browser a `https://<ACCOUNT-NAME>.azurehealthcareapis.com/metadata`.

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non servono più, è possibile eliminare il gruppo di risorse, l'API di Azure per FHIR e tutte le risorse correlate. A tale scopo, selezionare il gruppo di risorse contenente l'API di Azure per FHIR, selezionare **Elimina gruppo di risorse**, quindi confermare il nome del gruppo di risorse da eliminare.

## <a name="next-steps"></a>Passaggi successivi

In questa guida di avvio rapido è stata distribuita l'API di Azure per FHIR nella sottoscrizione. Per configurare le impostazioni aggiuntive nell'API di Azure per FHIR, passare alla guida pratica sulle impostazioni aggiuntive.

>[!div class="nextstepaction"]
>[Impostazioni aggiuntive nell'API di Azure per FHIR](azure-api-for-fhir-additional-settings.md)
