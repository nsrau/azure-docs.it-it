---
title: Creazione di Dynamics 365 per le risorse tecniche di coinvolgimento dei clienti | Azure Marketplace
description: Creare le risorse tecniche per un'offerta di applicazione Dynamics 365 for Customer Engagement.
services: Dynamics 365 for Customer Engagement, Azure, Marketplace, Cloud Partner Portal, AppSource
author: v-miclar
ms.service: marketplace
ms.topic: article
ms.date: 12/29/2018
ms.author: pabutler
ms.openlocfilehash: eff175264677d6b8ffb885229b5e68b306424335
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/30/2019
ms.locfileid: "64943092"
---
# <a name="create-technical-assets-for-azure-application-offer"></a>Creare le risorse tecniche per un'offerta di applicazione Azure

In genere si sviluppano soluzioni usando l'[SDK per le app Dynamics 365 for Customer Engagement](https://docs.microsoft.com/dynamics365/customer-engagement/developer/get-started-sdk).  Le soluzioni prendono varie forme, come illustrato in [Programming models for Dynamics 365 for Customer Engagement apps](https://docs.microsoft.com/dynamics365/customer-engagement/developer/programming-models) (Modelli di programmazione per app Dynamics 365 for Customer Engagement).  Scegliere la forma più idonea per i requisiti della soluzione.  Quando si sviluppa una soluzione, è necessario affrontare diversi aspetti, ad esempio opzioni di estendibilità, componenti della soluzione e compatibilità delle versioni.  Per altre informazioni, vedere [Introduction to solutions](https://docs.microsoft.com/dynamics365/customer-engagement/developer/introduction-solutions) (Introduzione alle soluzioni).

Le soluzioni Dynamics 365 pubblicate in AppSource sono per la maggior parte applicazioni gestite distribuite come file di pacchetto.


## <a name="creating-and-storing-the-package"></a>Creazione e archiviazione del pacchetto

La documentazione parallela per la creazione di offerte Dynamics 365 per Customer Engagement è disponibile nella sezione [Publish your app on AppSource](https://docs.microsoft.com/dynamics365/customer-engagement/developer/publish-app-appsource) (Pubblicare l'app in AppSource).  Gli argomenti interni seguenti illustrano in dettaglio come creare il file del pacchetto della soluzione e caricarlo in Archiviazione di Azure:

- [Passaggio 4: Create an AppSource package for your app](https://docs.microsoft.com/dynamics365/customer-engagement/developer/create-package-app-appsource) (Creare un pacchetto AppSource per l'app) - Illustra come creare un file compresso (ZIP) che rappresenta l'applicazione gestita e contiene la cartella delle risorse della soluzione, il file DLL di codice personalizzato DLL, il file di informazioni sul tipo MIME, l'icona del pacchetto AppSource, il file HTML di condizioni di licenza e il file XML dei contenuti.
- [Passaggio 5: Store your AppSource Package on Azure Storage and generate a URL with SAS key](https://docs.microsoft.com/dynamics365/customer-engagement/developer/store-appsource-package-azure-storage) (Archiviare il pacchetto AppSource in Archiviazione di Azure e generare un URL con chiave di firma di accesso condiviso) - Illustra come archiviare un file di pacchetto AppSource in un account di archiviazione BLOB di Microsoft Azure e usare una chiave di firma di accesso condiviso per condividere il file del pacchetto. Il file del pacchetto viene recuperato dal percorso di archiviazione di Azure per la certificazione e per usarlo per le versioni di valutazione di AppSource e la pubblicazione.


## <a name="next-steps"></a>Passaggi successivi

Se non è stato già fatto, [creare l'offerta Dynamics 365 for Customer Engagement](./cpp-create-offer.md).  Sarà quindi possibile [pubblicare l'offerta](./cpp-publish-offer.md).
