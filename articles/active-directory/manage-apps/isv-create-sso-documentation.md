---
title: Creare & pubblicare la documentazione Single Sign-On per l'applicazioneCreate a publish single sign-on documentation for your application
description: Linee guida per i fornitori di software indipendenti sull'integrazione con Azure Active Directory
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74232274"
---
# <a name="create-and-publish-single-sign-on-documentation-for-your-application"></a>Creare e pubblicare la documentazione Single Sign-On per l'applicazione   

## <a name="documentation-on-your-site"></a>Documentazione sul tuo sito

La facilità di adozione è un fattore significativo nelle decisioni relative al software aziendale. Una chiara documentazione facile da seguire supporta i clienti nel loro percorso di adozione e riduce i costi di supporto. Lavorando con migliaia di fornitori di software, Microsoft ha visto cosa funziona.

Ti consigliamo di documentare almeno la tua documentazione sul tuo sito di includere i seguenti elementi.

* Introduzione alla funzionalità SSO

  * Protocolli supportati

  * Versione e SKU

  * Elenco dei provider di identità supportati con collegamenti alla documentazioneSupported Identity Providers list with documentation links

* Informazioni sulle licenze per l'applicazione

* Controllo degli accessi in base al ruolo per la configurazione di SSORole-based access control for configuring SSO

* Passaggi di configurazione SSO

  * Elementi di configurazione dell'interfaccia utente per SAML con i valori previsti dal provider

  * Informazioni sul provider di servizi da passare ai provider di identità

* Se OIDC/OAuth

  * Elenco delle autorizzazioni necessarie per il consenso con motivazioni aziendali

* Passaggi di test per gli utenti pilotaTesting steps for pilot users

* Informazioni sulla risoluzione dei problemi, inclusi i codici di errore e i messaggi

* Meccanismi di supporto per i clienti

## <a name="documentation-on-the-microsoft-site"></a>Documentazione sul sito Microsoft

Quando si elenca l'applicazione con la raccolta di applicazioni di Azure Active Directory, che pubblica anche l'applicazione in Azure Marketplace, Microsoft genererà la documentazione per i clienti comuni che spiegano il processo passo-passo. Potete vedere un esempio [qui](https://aka.ms/appstutorial). Questa documentazione viene creata in base all'invio alla raccolta ed è possibile aggiornarla facilmente se si apportano modifiche all'applicazione usando l'account GitHub.This documentation is created based on your submission to the gallery, and you can easily update it if you make changes to your application using your GitHub account.

## <a name="next-steps"></a>Passaggi successivi

[Elencare l'applicazione nella raccolta di applicazioni di Azure ADList your application in the Azure AD Application Gallery](https://microsoft.sharepoint.com/teams/apponboarding/Apps/SitePages/Default.aspx)