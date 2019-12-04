---
title: Connetti a Excel online
description: Gestire i dati con le API REST di Excel Online e App per la logica di Azure
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 08/23/2018
tags: connectors
ms.openlocfilehash: a4356b4f88df3fc457917be563a79e9054a1638e
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74789793"
---
# <a name="manage-excel-online-data-with-azure-logic-apps"></a>Gestire i dati di Excel Online con App per la logica di Azure

Con App per la logica di Azure e il connettore Excel Online, è possibile creare attività e flussi di lavoro automatizzati basati sui dati in Excel Online (Business o OneDrive). Questo connettore offre azioni che consentono di eseguire attività sui dati e gestire i fogli di calcolo, ad esempio:

* Creare nuovi fogli di lavoro e tabelle.
* Ottenere e gestire fogli di lavoro, tabelle e righe.
* Aggiungere singole righe e colonne chiave.

È quindi possibile usare gli output generati da queste azioni con azioni per altri servizi. Se ad esempio si usa un'azione che crea fogli di lavoro con frequenza settimanale, è possibile usare un'altra azione che invia un messaggio di posta elettronica di conferma usando il connettore Office 365 Outlook.

Se non si ha familiarità con App per la logica, consultare [Informazioni su App per la logica di Azure](../logic-apps/logic-apps-overview.md).

> [!NOTE]
> I connettori [Excel Online (Business)](/connectors/excelonlinebusiness/) ed [Excel Online (OneDrive)](/connectors/excelonline/) interagiscono con App per la logica di Azure e si distinguono dal [connettore Excel per PowerApps](/connectors/excel/).

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, [iscriversi per creare un account Azure gratuito](https://azure.microsoft.com/free/).

* Un [account di Office 365](https://www.office.com/) per l'account aziendale o l'account Microsoft personale.

  I dati di Excel possono essere presenti come file con valori delimitati da virgole (CSV) in una cartella di archiviazione, ad esempio OneDrive. 
  Lo stesso file con estensione csv può essere usato anche con il [connettore File flat](../logic-apps/logic-apps-enterprise-integration-flatfile.md).

* Conoscenza di base di [come creare le app per la logica](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* L'app per la logica in cui si vuole accedere ai dati di Excel Online. Questo connettore offre solo azioni, quindi per avviare l'app per la logica, usare un trigger distinto, ad esempio il trigger **Ricorrenza**.

## <a name="add-excel-action"></a>Aggiungere un'azione di Excel

1. Nel [portale di Azure](https://portal.azure.com) aprire l'app per la logica in Progettazione app per la logica, se ancora chiusa.

1. Nel trigger scegliere **Nuovo passaggio**.

1. Nella casella di ricerca digitare "excel" come filtro. Nell'elenco delle azioni scegliere l'azione desiderata.

   > [!NOTE]
   > La finestra di progettazione dell'app per la logica non può caricare tabelle con 100 o più colonne. Se possibile, ridurre il numero di colonne nella tabella selezionata in modo che la finestra di progettazione possa caricare la tabella.

1. Se richiesto, accedere all'account Office 365.

   Le credenziali autorizzano l'app per la logica alla creazione di una connessione a Excel Online e all'accesso ai dati.

1. Continuare specificando i dettagli necessari per l'azione selezionata e creando il flusso di lavoro dell'app per la logica.

## <a name="connector-reference"></a>Informazioni di riferimento sui connettori

Per informazioni tecniche, ad esempio trigger, azioni e limiti, come descritto nei file OpenAPI (in precedenza spavalderia) del connettore, vedere le pagine di riferimento del connettore seguenti:

* [Excel Online (Business)](/connectors/excelonlinebusiness/)
* [Excel Online (OneDrive)](/connectors/excelonline/)

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su altri [connettori di App per la logica](../connectors/apis-list.md)
