---
title: Esercitazione su un'app Web - Configurare l'API di Azure per FHIR
description: Questa esercitazione illustra un esempio di distribuzione di una semplice applicazione Web. Questa prima esercitazione descrive i prerequisiti e la distribuzione dell'API di Azure per FHIR
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: tutorial
ms.reviewer: mihansen
ms.author: cavoeg
author: caitlinv39
ms.date: 01/03/2020
ms.custom: devx-track-javascript
ms.openlocfilehash: bf47ec0ca88d409c91b8acad25506c1cc8958e86
ms.sourcegitcommit: e71da24cc108efc2c194007f976f74dd596ab013
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/29/2020
ms.locfileid: "87422794"
---
# <a name="deploy-javascript-app-to-read-data-from-fhir-service"></a>Distribuire un'app JavaScript per leggere i dati dal servizio FHIR
In questa esercitazione verrà distribuita una piccola app JavaScript che legge i dati da un servizio FHIR. La procedura dell'esercitazione è la seguente:
1. Distribuire un server FHIR
1. Registrare un'applicazione client pubblica
1. Testare l'accesso all'applicazione
1. Creare un'applicazione Web che legga i dati di FHIR

## <a name="prerequisites"></a>Prerequisiti
Prima di iniziare questa serie di esercitazioni, è necessario avere gli elementi seguenti:
1. Una sottoscrizione di Azure.
1. Tenant di Azure Active Directory
1. [Postman](https://www.getpostman.com/) installato

> [!NOTE]
> Per questa esercitazione, il servizio FHIR, l'applicazione Azure AD e gli utenti di Azure AD sono tutti inclusi nello stesso tenant di Azure AD. Se non è questo il caso, è comunque possibile seguire l'esercitazione, ma è necessario approfondire alcuni documenti di riferimento per eseguire passaggi aggiuntivi.

## <a name="deploy-azure-api-for-fhir"></a>Distribuire l'API di Azure per FHIR
Il primo passaggio dell'esercitazione consiste nell'ottenere una configurazione corretta dell'API di Azure per FHIR.

1. Distribuire l'[API di Azure per FHIR](fhir-paas-portal-quickstart.md)
1. Una volta distribuita l'API di Azure per FHIR, configurare le impostazioni [CORS](configure-cross-origin-resource-sharing.md) passando all'API di Azure per FHIR e selezionando CORS. 
    1. Impostare **Origini** su *
    1. Impostare **Intestazioni** su *
    1. In **Metodi** scegliere **Seleziona tutto**
    1. Impostare **Età massima** su **600**

## <a name="next-steps"></a>Passaggi successivi
Ora che è stata distribuita l'API di Azure per FHIR, è possibile registrare un'applicazione client pubblica.

>[!div class="nextstepaction"]
>[Registrare un'applicazione client pubblica](tutorial-web-app-public-app-reg.md)
