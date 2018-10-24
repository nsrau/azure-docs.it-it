---
title: 'Risoluzione dei problemi: errori di autenticazione frequenti| Microsoft Docs'
description: Offre assistenza relativamente agli errori di autenticazione frequenti quando si usano API del portale Cloud Partner.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: 82a5ef86d1ca35cddb05cb4e126e64cc3759bcc0
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/05/2018
ms.locfileid: "48807267"
---
<a name="troubleshooting-common-authentication-errors"></a>Risoluzione dei problemi: errori di autenticazione frequenti
------------------------------------------

Questo articolo offre assistenza relativamente agli errori di autenticazione frequenti quando si usano API del portale Cloud Partner.

### <a name="unauthorized-error"></a>Errore non autorizzato

Se si visualizzano costantemente errori `401 unauthorized`, verificare di disporre di un token di accesso valido.  Se non è ancora stato fatto, creare un'applicazione di base Azure Active Directory (Azure AD) e un'entità servizio come illustrato in [Usare il portale per creare un'applicazione Azure Active Directory e un'entità servizio che possano accedere alle risorse](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal). Usare quindi l'applicazione o una semplice richiesta HTTP POST per verificare l'accesso.  Verranno inclusi l'ID tenant, l'ID applicazione, l'ID oggetto e la chiave privata per ottenere il token di accesso, come illustrato nell'immagine seguente:

![Risoluzione dei problemi: errore 401](./media/cloud-partner-portal-api-troubleshooting-authentication-errors/troubleshooting-401-error.jpg)


### <a name="forbidden-error"></a>Errore non consentito

Se si visualizza un errore `403 forbidden`, assicurarsi che sia stata aggiunta l'entità servizio corretta all'account di pubblicazione nel portale Cloud Partner.
Seguire la procedura nella pagina [Prerequisiti](./cloud-partner-portal-api-prerequisites.md) per aggiungere l'entità servizio al portale.

Se è stata aggiunta l'entità servizio corretta, verificare tutte le altre informazioni. Prestare particolare attenzione all'ID oggetto immesso nel portale. Sono presenti due ID oggetto nella pagina di registrazione dell'app Azure Active Directory ed è necessario utilizzare l'ID oggetto locale. È possibile trovare il valore corretto visitando la pagina **Registrazioni app** per l'app e facendo clic sul nome dell'app nella sezione **Applicazione gestita nella directory locale**. Ciò consente di visualizzare le proprietà locali per l'app, in cui è possibile trovare l'ID oggetto corretto nella pagina **Proprietà**, come illustrato nella figura seguente. Inoltre, garantisce l'uso dell'ID editore corretto quando si aggiunge l'entità servizio e si effettua la chiamata API.

![Risoluzione dei problemi: errore 403](./media/cloud-partner-portal-api-troubleshooting-authentication-errors/troubleshooting-403-error.jpg)
