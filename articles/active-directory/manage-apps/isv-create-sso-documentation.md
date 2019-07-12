---
title: Creare e pubblicare single sign-on di documentazione per l'applicazione
description: Linee guida per fornitori di software indipendenti per l'integrazione con Azure Active Directory
services: active-directory
author: barbaraselden
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 05/22/2019
ms.author: baselden
ms.reviewer: jeeds
ms.collection: M365-identity-device-management
ms.openlocfilehash: 53221343ac606b6076cc9cc3cff6e0f96c1a3ac3
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/08/2019
ms.locfileid: "67659593"
---
# <a name="create-and-publish-single-sign-on-documentation-for-your-application"></a>Creare e pubblicare single sign-on di documentazione per l'applicazione   

## <a name="documentation-on-your-site"></a>Documentazione nel sito

Facilità di adozione è un fattore significativo nelle decisioni di software aziendale. Documentazione di deselezionare facili da seguire supporta i clienti nella loro processo decisionale l'adozione e riduce i costi di supporto. Lavora con migliaia di fornitori di software, Microsoft ha rilevato che cosa funziona.

È consigliabile che la documentazione nel sito come minimo includere gli elementi seguenti.

* Introduzione alla funzionalità SSO

  * Protocolli supportati

  * SKU e versione

  * Elenco di provider di identità supportati con collegamenti alla documentazione

* Informazioni sulle licenze per l'applicazione

* Controllo degli accessi basata sui ruoli per configurare l'accesso SSO

* Passaggi di configurazione di SSO

  * Elementi di configurazione dell'interfaccia utente per SAML con i valori previsti dal provider

  * Informazioni sul provider di servizi da passare al provider di identità

* Se OIDC/OAuth

  * Elenco delle autorizzazioni necessarie per il consenso mediante motivazioni aziendali

* Passaggi di test per gli utenti pilota

* Informazioni sulla risoluzione dei problemi, inclusi i messaggi e codici di errore

* Supportare i meccanismi per i clienti

## <a name="documentation-on-the-microsoft-site"></a>Documentazione sul sito Microsoft

Quando si Elenca la tua applicazione con raccolta di Azure Active Directory dell'applicazione, viene pubblicata l'applicazione in Azure Marketplace, Microsoft genera la documentazione per i nostri clienti reciproche che spiega la procedura dettagliata. È possibile vedere un esempio [qui](https://aka.ms/appstutorial). Questa documentazione viene creata in base l'invio in gallery ed è possibile aggiornare facilmente se si apportano modifiche all'applicazione usando il proprio account GitHub.

## <a name="next-steps"></a>Fasi successive

[Includi la tua applicazione nella raccolta di applicazioni AD Azure](https://microsoft.sharepoint.com/teams/apponboarding/Apps/SitePages/Default.aspx)