---
title: Creare un'offerta di applicazione Power BI - Azure Marketplace | Microsoft Docs
description: Come creare un'offerta di app Power BI per Microsoft AppSource Marketplace.
services: Azure, AppSource, Marketplace, Cloud Partner Portal, Power BI
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
ms.date: 01/29/2019
ms.author: pbutlerm
ms.openlocfilehash: e0e1311276b858f1ac16fe6f17ccad49dd9901c9
ms.sourcegitcommit: de32e8825542b91f02da9e5d899d29bcc2c37f28
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/02/2019
ms.locfileid: "55665932"
---
# <a name="create-a-power-bi-application-offer"></a>Creare un'offerta di applicazione Power BI

Questa sezione indica i passaggi da seguire per creare una nuova offerta di app Power BI per [AppSource](https://appsource.microsoft.com). Ogni offerta viene visualizzata come entità a sé stante in AppSource.  Quando si crea una nuova offerta nel [portale Cloud Partner](https://cloudpartner.azure.com/), è necessario specificare quattro gruppi di asset per l'offerta.

|   Gruppo di asset      | Descrizione                                                                         |
| ----------------   | ----------------                                                                    |
| Impostazioni dell'offerta     | Identificatori principali e nome dell'offerta                                      |
| Informazioni tecniche     | URL del programma di installazione usato per installare l'app nell'area di lavoro di Power BI del client. Per altre informazioni su come generare questo URL, vedere la [documentazione relativa alle app Power BI](https://go.microsoft.com/fwlink/?linkid=2028636).   |
| Dettagli da visualizzare nella vetrina | Contiene gli asset di marketing, di carattere legale e di gestione dei clienti potenziali. Gli asset di marketing includono la descrizione dell'offerta e i logo.  Gli asset di carattere legale includono l'informativa sulla privacy, le condizioni per l'utilizzo e altra documentazione legale.  I criteri di gestione dei clienti potenziali consentono di specificare come gestire i clienti potenziali dal portale AppSource per gli utenti finali. |
| Contatti           | Contiene informazioni sui criteri e sui contatti di supporto                                     |
|    |     |


## <a name="new-offer-form"></a>Modulo New Offer (Nuova offerta)

Dopo l'accesso al portale Cloud Partner, fare clic su **+ New Offer** (Nuova offerta) sulla barra dei menu a sinistra.  Nel menu risultante fare clic su **Power BI Apps** (App Power BI) per visualizzare il modulo **New Offer** (Nuova offerta) e iniziare a definire gli asset per una nuova offerta di app.

![Voce di menu dell'offerta di Power BI](./media/new-offer-menu.png)

> [!WARNING] 
> Se l'opzione **Power BI Apps** (App Power BI) non viene visualizzata o non è abilitata, l'account non ha l'autorizzazione per creare questo tipo di offerta. Verificare di aver soddisfatto tutti i [prerequisiti](./cpp-prerequisites.md) per questo tipo di offerta, tra cui la registrazione per un account sviluppatore.


## <a name="next-steps"></a>Passaggi successivi

Gli articoli successivi di questa sezione illustrano le schede disponibili nella pagina **New Offer** (Nuova offerta) per il tipo di offerta relativo a un'app Power BI. Ogni articolo illustra come usare la scheda descritta per definire i gruppi di asset e i servizi di supporto per la nuova offerta di app.

-  [Scheda Offer Settings (Impostazioni dell'offerta)](./cpp-offer-settings-tab.md)
-  [Scheda Technical info (Informazioni tecniche)](./cpp-technical-info-tab.md)
-  [Scheda Storefront details (Dettagli vetrina)](./cpp-storefront-details-tab.md)
-  [Scheda Contacts (Contatti)](./cpp-contacts-tab.md)
