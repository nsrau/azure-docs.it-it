---
title: Eseguire la registrazione in Gestione costi di Azure usando Cloudyn in Azure
description: Informazioni dettagliate sul processo di registrazione usato dai partner per eseguire l'onboarding dei clienti nel portale di Cloudyn.
author: bandersmsft
ms.author: banders
ms.date: 10/23/2020
ms.topic: quickstart
ms.custom: seodec18
ms.service: cost-management-billing
ms.subservice: cloudyn
ms.reviewer: benshy
ROBOTS: NOINDEX
ms.openlocfilehash: 95d8a425314ad1c968915085b35ca89aa3f62711
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/26/2020
ms.locfileid: "92543322"
---
# <a name="register-with-the-csp-partner-program-and-view-cost-data"></a>Eseguire la registrazione al programma per i partner CSP e visualizzare i dati sui costi

I partner CSP che sono utenti registrati di Cloudyn possono visualizzare e analizzare la propria spesa per il cloud in Cloudyn. [Gestione costi di Azure è disponibile in modo nativo per i partner diretti](../costs/get-started-partners.md) che hanno eseguito l'onboarding dei propri clienti a un contratto del cliente Microsoft e hanno acquistato un piano di Azure.

La registrazione consente di accedere al portale di Cloudyn. Questa guida introduttiva illustra nei dettagli il processo di registrazione necessario per creare una sottoscrizione di valutazione di Cloudyn e accedere al suo portale.

[!INCLUDE [cloudyn-note](../../../includes/cloudyn-note.md)]

## <a name="configure-indirect-csp-access-in-cloudyn"></a>Configurare l'accesso CSP indiretto in Cloudyn

Per impostazione predefinita, l'API del Centro per i partner è accessibile solo ai partner CSP diretti. Un provider CSP diretto può tuttavia configurare l'accesso per i propri clienti o partner CSP indiretti usando i gruppi di entità in Cloudyn.

Per consentire l'accesso ai clienti o partner CSP indiretti, completare la procedura seguente per segmentare i dati CSP indiretti usando i gruppi di entità Cloudyn. Assegnare poi le autorizzazioni utente appropriate ai gruppi di entità.

1. Creare un gruppo di entità con le informazioni disponibili in [Creare entità](tutorial-user-access.md#create-and-manage-entities).
2. Seguire i passaggi del video [Assigning subscriptions to Cost Entities](https://www.youtube.com/watch?v=d9uTWSdoQYo) (Assegnazione di sottoscrizioni alle entità di costo). Associare l'account del cliente CSP indiretto e le relative sottoscrizioni di Azure all'entità creata in precedenza.
3. Seguire i passaggi in [Creare un utente con accesso amministrativo](tutorial-user-access.md#create-a-user-with-admin-access) per creare un account utente con accesso amministrativo. Assicurarsi quindi che l'account utente abbia l'accesso amministrativo alle entità specifiche create in precedenza per l'account indiretto.

I partner CSP indiretti accedono al portale di Cloudyn usando gli account creati per loro.


[!INCLUDE [cost-management-create-account-view-data](../../../includes/cost-management-create-account-view-data.md)]

## <a name="next-steps"></a>Passaggi successivi

In questa guida introduttiva sono state usate le informazioni CSP per eseguire la registrazione in Cloudyn. È stato anche eseguito l'accesso al portale di Cloudyn ed è stata avviata la visualizzazione dei dati sui costi. Per altre informazioni su Cloudyn, passare all'esercitazione su Cloudyn.

> [!div class="nextstepaction"]
> [Esaminare l'utilizzo e i costi](tutorial-review-usage.md)