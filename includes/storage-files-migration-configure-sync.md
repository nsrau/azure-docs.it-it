---
title: Configurazione di Sincronizzazione file di Azure
description: Configurare Sincronizzazione file di Azure. Un blocco di testo comune, condiviso tra i documenti di migrazione.
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 2/20/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 53d7af6b43ff24ab12501570385162759f4c7633
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78209584"
---
Questo passaggio associa tutte le risorse e le cartelle configurate in Windows Server durante i passaggi precedenti.

* Accedi al [portale di Azure](https://portal.azure.com).
* Individuare la risorsa del servizio di sincronizzazione archiviazione.
* Creare un nuovo *gruppo di sincronizzazione* all'interno della risorsa del servizio di sincronizzazione archiviazione per ogni condivisione file di Azure. In Sincronizzazione file di Azure terminologia, la condivisione file di Azure diventerà un *endpoint cloud* nella topologia di sincronizzazione che si sta descrivendo con la creazione di un gruppo di sincronizzazione. Quando si crea il gruppo di sincronizzazione, assegnargli un nome familiare, in modo da identificare il set di file sincronizzati qui. Assicurarsi di fare riferimento alla condivisione file di Azure con un nome corrispondente.
* Una volta creato il gruppo di sincronizzazione, viene visualizzata una riga nell'elenco dei gruppi di sincronizzazione. Fare clic sul nome (un collegamento) per visualizzare il contenuto del gruppo di sincronizzazione. La condivisione file di Azure sarà visualizzata in "endpoint cloud".
* Individuare il pulsante di comando per *+ Aggiungi endpoint server*. La cartella nel server locale di cui è stato effettuato il provisioning diventerà il percorso dell' *endpoint server*.
