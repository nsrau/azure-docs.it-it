---
title: Esporta ed elimina i tuoi dati
titleSuffix: Azure Machine Learning Studio (classic)
description: I dati del prodotto archiviati da Azure Machine Learning Studio (classico) sono disponibili per l'esportazione e l'eliminazione tramite il portale di Azure e anche tramite le API REST autenticate. I dati di telemetria sono accessibili tramite il portale Privacy di Azure. Questo articolo illustra i passaggi da eseguire.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 05/25/2018
ms.openlocfilehash: a0cc200f69be2362806886aae79ece52c833a43a
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73492969"
---
# <a name="export-and-delete-in-product-user-data-from-azure-machine-learning-studio-classic"></a>Esportare ed eliminare dati utente del prodotto dal Azure Machine Learning Studio (versione classica)

È possibile eliminare o esportare i dati del prodotto archiviati da Azure Machine Learning Studio (classico) usando il portale di Azure, l'interfaccia di studio (classica), PowerShell e le API REST autenticate. Questo articolo descrive come esportare o eliminare i dati. 

I dati di telemetria sono accessibili tramite il portale Privacy di Azure. 

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="what-kinds-of-user-data-does-studio-classic-collect"></a>Quali tipi di dati utente vengono raccolti da studio (classico)?

Per questo servizio, i dati utente sono costituiti da informazioni sugli utenti autorizzati ad accedere alle aree di lavoro e record di telemetria relativi alle interazioni dell'utente con il servizio.

Esistono due tipi di dati utente in Machine Learning Studio (classico):
- **Dati dell'account personale:** ID account e indirizzi di posta elettronica associati a un account.
- **Dati dei clienti:** dati caricati per l'analisi.

## <a name="studio-classic-account-types-and-how-data-is-stored"></a>Tipi di account Studio (classico) e modalità di archiviazione dei dati

Esistono tre tipi di account Machine Learning Studio (classico). Il tipo di account di cui si dispone determina la modalità di archiviazione dei dati e la procedura per eliminarli o esportarli.

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

## <a name="delete"></a>Eliminare i dati dell'area di lavoro in studio (versione classica) 

### <a name="delete-individual-assets"></a>Eliminare singole risorse

Gli utenti possono eliminare le risorse in un'area di lavoro selezionandole e quindi selezionando il pulsante di eliminazione.

![Elimina asset in Machine Learning Studio (versione classica)](./media/export-delete-personal-data-dsr/delete-studio-asset.png)

### <a name="delete-an-entire-workspace"></a>Eliminare un'intera area di lavoro

Gli utenti possono anche eliminare la propria intera area di lavoro:
- Area di lavoro a pagamento: eliminare tramite il portale di Azure.
- Area di lavoro gratuita: usare il pulsante di eliminazione nel riquadro **Impostazioni**.

![Eliminare un'area di lavoro gratuita in Machine Learning Studio (versione classica)](./media/export-delete-personal-data-dsr/delete-studio-data-workspace.png)
 
## <a name="export-studio-classic-data-with-powershell"></a>Esportare i dati di studio (classico) con PowerShell
Usare PowerShell per esportare tutte le informazioni in un formato portatile dalla versione classica di Azure Machine Learning Studio usando i comandi. Per informazioni, vedere l'articolo [relativo al modulo PowerShell per Azure Machine Learning Studio (classico)](powershell-module.md) .

## <a name="next-steps"></a>Passaggi successivi

Per la documentazione relativa ai servizi Web e alla fatturazione del piano di impegno, vedere informazioni di [riferimento sull'API REST Azure Machine Learning Studio (classica)](https://docs.microsoft.com/rest/api/machinelearning/). 
