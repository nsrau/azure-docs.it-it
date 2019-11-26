---
title: Creare & pubblicare Single Sign-On documentazione per l'applicazione
description: Linee guida per i fornitori di software indipendenti per l'integrazione con Azure Active Directory
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
ms.openlocfilehash: cb223ec8ab7b5c053136c78d3b4ca30ad4da4e18
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74232274"
---
# <a name="create-and-publish-single-sign-on-documentation-for-your-application"></a>Creare e pubblicare Single Sign-On documentazione per l'applicazione   

## <a name="documentation-on-your-site"></a>Documentazione sul sito

La facilità di adozione è un fattore significativo nelle decisioni relative al software aziendale. La documentazione chiara facile da seguire supporta i clienti nel percorso di adozione e riduce i costi di supporto. Lavorando con migliaia di fornitori di software, Microsoft ha visto cosa funziona.

È consigliabile che la documentazione nel sito includa almeno gli elementi seguenti.

* Introduzione alla funzionalità SSO

  * Protocolli supportati

  * Versione e SKU

  * Elenco dei provider di identità supportati con collegamenti alla documentazione

* Informazioni sulle licenze per l'applicazione

* Controllo degli accessi in base al ruolo per la configurazione di SSO

* Procedura di configurazione SSO

  * Elementi di configurazione dell'interfaccia utente per SAML con i valori previsti dal provider

  * Informazioni sul provider di servizi da passare ai provider di identità

* Se OIDC/OAuth

  * Elenco delle autorizzazioni necessarie per il consenso con le motivazioni aziendali

* Passaggi di test per gli utenti pilota

* Informazioni sulla risoluzione dei problemi, inclusi codici e messaggi di errore

* Meccanismi di supporto per i clienti

## <a name="documentation-on-the-microsoft-site"></a>Documentazione sul sito Microsoft

Quando l'applicazione viene elencata con la raccolta di applicazioni di Azure Active Directory, che pubblica anche l'applicazione in Azure Marketplace, Microsoft genererà la documentazione per i clienti reciproci che spiegano il processo dettagliato. È possibile vedere un esempio [qui](https://aka.ms/appstutorial). Questa documentazione viene creata in base all'invio alla raccolta ed è possibile aggiornarla facilmente se si apportano modifiche all'applicazione con l'account GitHub.

## <a name="next-steps"></a>Passaggi successivi

[Elencare l'applicazione nella raccolta di applicazioni Azure AD](https://microsoft.sharepoint.com/teams/apponboarding/Apps/SitePages/Default.aspx)