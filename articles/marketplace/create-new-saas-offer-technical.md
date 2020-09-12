---
title: Come aggiungere i dettagli tecnici per l'offerta SaaS nel centro per i partner Microsoft
description: Informazioni su come fornire i dettagli tecnici per l'offerta SaaS (Software as a Service) per Microsoft Commercial Marketplace.
author: mingshen-ms
ms.author: mingshen
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 09/02/2020
ms.openlocfilehash: 78a81a2d392363ac45979fd28138f838653a2358
ms.sourcegitcommit: 3246e278d094f0ae435c2393ebf278914ec7b97b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/02/2020
ms.locfileid: "89380974"
---
# <a name="how-to-add-technical-details-for-your-saas-offer"></a>Come aggiungere i dettagli tecnici per l'offerta SaaS

Questo articolo descrive come immettere i dettagli tecnici che consentono a Microsoft Commercial Marketplace di connettersi alla soluzione. Questa connessione consente di effettuare il provisioning dell'offerta per il cliente se sceglie di acquisirla e gestirla. Per ulteriori informazioni su queste impostazioni, vedere la pagina relativa alle [informazioni tecniche](plan-saas-offer.md#technical-information).

> [!NOTE]
> Se si sceglie di elaborare le transazioni in modo indipendente, questa opzione non verrà visualizzata. È invece possibile passare a [come commercializzare l'offerta SaaS](create-new-saas-offer-marketing.md).

## <a name="technical-configuration"></a>Configurazione tecnica

Nella scheda **configurazione tecnica** verranno definiti i dettagli tecnici usati dal Marketplace commerciale per comunicare con la soluzione o l'applicazione SaaS. 

- **URL della pagina di destinazione** (obbligatorio): definire l'URL del sito Web SaaS (ad esempio, `https://contoso.com/signup` ) che i clienti potranno atterrare dopo aver acquisito l'offerta dal Marketplace commerciale e aver attivato il processo di configurazione dalla sottoscrizione Saas appena creata.

  > [!IMPORTANT]
  > La pagina di destinazione deve essere in esecuzione 24/7. Questo è l'unico modo per ricevere notifiche sui nuovi acquisti delle offerte SaaS effettuate nel Marketplace commerciale o sulle richieste di configurazione di una sottoscrizione attiva di un'offerta.

- **Webhook di connessione** (obbligatorio): per tutti gli eventi asincroni che Microsoft deve inviare all'utente (ad esempio, la sottoscrizione Saas è stata annullata), è necessario specificare un URL del webhook di connessione. Questo URL verrà chiamato per notificare l'evento.

  > [!IMPORTANT]
  > Il webhook dovrebbe essere attivo e in esecuzione 24/7 poiché questo è l'unico modo per ricevere una notifica sugli aggiornamenti delle sottoscrizioni SaaS dei clienti acquistate tramite il Marketplace commerciale.

- **Azure Active Directory ID tenant** (obbligatorio): per trovare l'ID tenant per l'app Azure Active Directory (Azure ad), passare al pannello [registrazioni app](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) in Azure Active Directory. Nella colonna **nome visualizzato** selezionare l'app. Cercare quindi il numero di **ID della directory (tenant)** elencato (ad esempio, `50c464d3-4930-494c-963c-1e951d15360e` ).

- **Azure Active Directory ID applicazione** (obbligatorio): per trovare l' [ID applicazione](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in), passare al pannello [registrazioni app](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) in Azure Active Directory. Nella colonna **nome visualizzato** selezionare l'app. Cercare quindi il numero ID dell'applicazione (client) elencato (ad esempio, `50c464d3-4930-494c-963c-1e951d15360e` ).

Selezionare **Salva bozza** prima di continuare con la scheda successiva: Panoramica del piano.

## <a name="next-steps"></a>Passaggi successivi

- [Come creare piani per l'offerta SaaS](create-new-saas-offer-plans.md).
