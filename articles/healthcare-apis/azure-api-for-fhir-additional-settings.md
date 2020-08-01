---
title: Impostazioni aggiuntive per l'API di Azure per FHIR
description: Panoramica delle impostazioni aggiuntive che è possibile impostare per l'API di Azure per FHIR
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.reviewer: mihansen
ms.author: cavoeg
author: CaitlinV39
ms.date: 11/22/2019
ms.openlocfilehash: 64bdc60f8f9e291c4dc084b23f8ec5054cdc528f
ms.sourcegitcommit: cee72954f4467096b01ba287d30074751bcb7ff4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2020
ms.locfileid: "87447258"
---
# <a name="additional-settings-for-azure-api-for-fhir"></a>Impostazioni aggiuntive per l'API di Azure per FHIR

In questa guida dettagliata si esamineranno le impostazioni aggiuntive che è possibile impostare nell'API di Azure per FHIR. Sono disponibili altre pagine che analizzano ulteriormente i dettagli.

## <a name="configure-database-settings"></a>Configurare le impostazioni del database

API di Azure per FHIR usa il database per archiviare i dati. Le prestazioni del database sottostante dipendono dal numero di unità richiesta (UR) selezionate durante il provisioning del servizio o nelle impostazioni del database dopo il provisioning del servizio.

È necessario effettuare il provisioning della velocità effettiva per garantire che le risorse di sistema sufficienti siano sempre disponibili per il database. Il numero di ur necessarie per l'applicazione dipende dalle operazioni eseguite. Le operazioni possono variare da semplici letture e scritture a query più complesse.

Per ulteriori informazioni su come modificare le impostazioni predefinite, vedere [Configure database settings](configure-database.md).

## <a name="access-control"></a>Controllo di accesso

L'API di Azure per FHIR consente solo agli utenti autorizzati di accedere all'API FHIR. È possibile configurare gli utenti autorizzati tramite due meccanismi diversi. La modalità principale e consigliata per configurare il controllo degli accessi è l'uso del [controllo degli accessi in base al ruolo di Azure (RBAC di Azure)](https://docs.microsoft.com/azure/role-based-access-control/), accessibile tramite il **Pannello controllo di accesso (IAM)** . Il controllo degli accessi in base al ruolo di Azure funziona solo se si vuole proteggere l'accesso al piano dati tramite il tenant Azure Active Directory associato alla sottoscrizione. Se si vuole usare un tenant diverso, l'API di Azure per FHIR offre un meccanismo di controllo di accesso del piano dati FHIR locale. Le opzioni di configurazione non sono più ricche quando si usa il meccanismo RBAC locale. Per informazioni dettagliate, scegliere una delle opzioni seguenti:

* [Azure RBAC per il piano dati FHIR](configure-azure-rbac.md). Si tratta dell'opzione preferita quando si usa il tenant Azure Active Directory associato alla sottoscrizione.
* [Controllo di accesso del piano dati FHIR locale](configure-local-rbac.md). Usare questa opzione solo quando è necessario usare un tenant di Azure Active Directory esterno per il controllo di accesso al piano dati. 

## <a name="enable-diagnostic-logging"></a>Abilitare la registrazione diagnostica
È possibile abilitare la registrazione diagnostica come parte della configurazione per poter monitorare il servizio e avere rapporti accurati ai fini della conformità. Per informazioni dettagliate su come configurare la registrazione diagnostica, vedere la [Guida alle](enable-diagnostic-logging.md) procedure per la configurazione della registrazione diagnostica, insieme ad alcune query di esempio. 

## <a name="use-custom-headers-to-add-data-to-audit-logs"></a>Usare intestazioni personalizzate per aggiungere dati ai log di controllo
Nell'API di Azure per FHIR, può essere necessario includere informazioni aggiuntive nei log, che provengono dal sistema chiamante. Per includere queste informazioni, è possibile utilizzare intestazioni personalizzate.

È possibile usare le intestazioni personalizzate per acquisire diversi tipi di informazioni. Ad esempio:

* Informazioni su identità o autorizzazioni
* Origine del chiamante
* Organizzazione di origine
* Dettagli del sistema client (cartella clinica elettronica, portale del paziente)

Per aggiungere questi dati ai log di controllo, vedere la Guida alle procedure per l' [uso di intestazioni HTTP personalizzate per aggiungere dati a log di controllo](use-custom-headers.md) .

## <a name="next-steps"></a>Passaggi successivi

Questa guida illustra come configurare impostazioni aggiuntive per l'API di Azure per FHIR.

Vedere quindi la serie di esercitazioni per creare un'applicazione Web che legge i dati di FHIR.

>[!div class="nextstepaction"]
>[Distribuire l'applicazione JavaScript](tutorial-web-app-fhir-server.md)