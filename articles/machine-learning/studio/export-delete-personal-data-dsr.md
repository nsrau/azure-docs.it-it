---
title: Esportare ed eliminare i dati da Machine Learning Studio
titleSuffix: Azure Machine Learning Studio
description: I dati interni al prodotto archiviati da Azure Machine Learning Studio sono disponibili per l'esportazione e l'eliminazione tramite il portale di Azure e anche tramite API REST autenticate. I dati di telemetria sono accessibili tramite il portale Privacy di Azure. Questo articolo illustra i passaggi da eseguire.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: ericlicoding
ms.author: amlstudiodocs
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 05/25/2018
ms.openlocfilehash: d2276eb11ce904b485440a6ce91981846a980dbc
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/31/2019
ms.locfileid: "55462189"
---
# <a name="export-and-delete-in-product-user-data-from-azure-machine-learning-studio"></a>Esportare ed eliminare i dati utente interni al prodotto da Azure Machine Learning Studio

È possibile eliminare o esportare i dati interni al prodotto archiviati da Azure Machine Learning Studio usando il portale di Azure, l'interfaccia di Machine Learning Studio, PowerShell e API REST autenticate. Questo articolo spiega come procedere. 

I dati di telemetria sono accessibili tramite il portale Privacy di Azure. 

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="what-kinds-of-user-data-does-studio-collect"></a>Quali tipi di dati utente raccoglie Machine Learning Studio?

Per questo servizio, i dati utente sono costituiti da informazioni sugli utenti autorizzati ad accedere alle aree di lavoro e record di telemetria relativi alle interazioni dell'utente con il servizio.

Esistono due tipi di dati utente in Machine Learning Studio:
- **Dati dell'account personale:** ID account e indirizzi di posta elettronica associati a un account.
- **Dati dei clienti:** dati caricati per l'analisi.

## <a name="studio-account-types-and-how-data-is-stored"></a>Tipi di account di Machine Learning Studio e modalità di archiviazione

Esistono tre tipi di account in Machine Learning Studio. Il tipo di account di cui si dispone determina la modalità di archiviazione dei dati e la procedura per eliminarli o esportarli.

- Un'**area di lavoro guest** è un account gratuito e anonimo. Si accede senza fornire credenziali, ad esempio un indirizzo di posta elettronica o una password.
    -  Alla scadenza dell'area di lavoro guest, i dati vengono eliminati.
    - Gli utenti guest possono esportare i dati dei clienti tramite l'interfaccia utente, le API REST o il pacchetto di PowerShell.
- Un'**area di lavoro gratuita** è un account gratuito a cui si accede con le credenziali dell'account Microsoft, un indirizzo di posta elettronica e una password.
    - È possibile esportare ed eliminare dati personali e dei clienti, che sono soggetti a richieste DSR (Data Subject Rights, Diritti del soggetto dei dati).
    - È possibile esportare i dati dei clienti tramite l'interfaccia utente, le API REST o il pacchetto di PowerShell.
    - Per le aree di lavoro gratuite che non usano account di Azure AD, i dati di telemetria possono essere esportati tramite il portale Privacy.
    - Quando si elimina l'area di lavoro, si eliminano tutti i dati personali dei clienti.
- Un'**area di lavoro standard** è un account a pagamento a cui si accede inserendo credenziali di accesso.
    - È possibile esportare ed eliminare dati personali e dei clienti, che sono soggetti a richieste DSR.
    - È possibile accedere ai dati tramite il portale Privacy di Azure
    - È possibile esportare i dati personali e dei clienti tramite l'interfaccia utente, le API REST o il pacchetto di PowerShell
    - È possibile eliminare i dati nel portale di Azure.

## <a name="delete-workspace-data-in-studio"></a>Eliminare i dati dell'area di lavoro in Machine Learning Studio 

### <a name="delete-individual-assets"></a>Eliminare singole risorse

Gli utenti possono eliminare le risorse in un'area di lavoro selezionandole e quindi selezionando il pulsante di eliminazione.

![Eliminare risorse in Machine Learning Studio](./media/export-delete-personal-data-dsr/delete-studio-asset.png)

### <a name="delete-an-entire-workspace"></a>Eliminare un'intera area di lavoro

Gli utenti possono anche eliminare la propria intera area di lavoro:
- Aggiungi area di lavoro: Eliminare il ripristino tramite il portale di Azure.
- Area di lavoro gratuita: usare il pulsante di eliminazione nel riquadro **Impostazioni**.

![Eliminare un'area di lavoro gratuita in Machine Learning Studio](./media/export-delete-personal-data-dsr/delete-studio-data-workspace.png)
 
## <a name="export-studio-data-with-powershell"></a>Esportare i dati di Machine Learning Studio con PowerShell
Usare i comandi di PowerShell per esportare tutte le informazioni in un formato portabile da Azure Machine Learning Studio. Per informazioni, vedere [Modulo PowerShell per Microsoft Azure Machine Learning](powershell-module.md).

## <a name="next-steps"></a>Passaggi successivi

Per la documentazione sulla fatturazione dei servizi Web e del piano di impegno, vedere le [informazioni di riferimento sull'API REST di Azure Machine Learning](https://docs.microsoft.com/rest/api/machinelearning/). 
