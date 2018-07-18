---
title: Aggiungere utenti a un account di Azure Data Lake Analytics
description: Informazioni su come aggiungere correttamente gli utenti al proprio account di Data Lake Analytics
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
manager: kfile
editor: jasonwhowell
ms.assetid: db35f16e-1565-4873-a851-bd987accdc58
ms.topic: conceptual
ms.date: 05/24/2018
ms.openlocfilehash: 58b78c7d9769069f36c9f01c2a7650878a6c5ec9
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/04/2018
ms.locfileid: "34716789"
---
# <a name="adding-a-user-in-the-azure-portal"></a>Aggiunta di un utente nel portale di Azure

## <a name="start-the-add-user-wizard"></a>Avviare l'Aggiunta guidata utente
1. Aprire Azure Data Lake Analytics tramite https://portal.azure.com.
2. Fare clic su **Aggiunta guidata utente**.
3. Nel passaggio **Selezionare l'utente** trovare l'utente che si desidera aggiungere. Fare clic su **Seleziona**.
4. Nel passaggio **Selezionare il ruolo** scegliere **Sviluppatore di Data Lake Analytics**. Questo ruolo dispone del set minimo di autorizzazioni necessarie per inviare, monitorare o gestire i processi U-SQL. Assegnare questo ruolo se il gruppo non deve gestire i servizi di Azure.
5. Nel passaggio **Selezionare le autorizzazioni catalogo** selezionare tutti i database aggiuntivi necessari a cui l'utente dovrà accedere. L'accesso in lettura e scrittura al database master è necessario per inviare i processi. Al termine, fare clic su **OK**.
6. Nel passaggio finale denominato **Assegnare le autorizzazioni selezionate** rivedere le modifiche che la procedura guidata apporterà. Fare clic su **OK**.


## <a name="configure-acls-for-data-folders"></a>Configurare gli ACL per le cartelle di dati
Concedere "R-X" o "RWX", in base alle esigenze, alle cartelle che contengono dati di input e output.


## <a name="optionally-add-the-user-to-the-azure-data-lake-store-role-reader-role"></a>Facoltativamente aggiungere il ruolo **Lettore** di Azure Data Lake Store all'utente.
1.  Individuare l'account di Azure Data Lake Store.
2.  Fare clic su **Utenti**.
3. Fare clic su **Aggiungi**.
4.  Selezionare un ruolo Controllo degli accessi in base al ruolo di Azure per assegnare questo gruppo.
5.  Assegnare il ruolo Lettore. Questo ruolo dispone del set minimo di autorizzazioni necessarie per esplorare o gestire i dati archiviati in Azure Data Lake Store. Assegnare questo ruolo se il gruppo non deve gestire i servizi di Azure.
6.  Digitare il nome del gruppo.
7.  Fare clic su **OK**.

## <a name="adding-a-user-using-powershell"></a>Aggiunta di un utente tramite PowerShell

1. Seguire le istruzioni in questa guida: [Come installare e configurare Azure PowerShell](https://azure.microsoft.com/documentation/articles/powershell-install-configure/).
2. Scaricare lo script di PowerShell [Add-AdlaJobUser.ps1](https://github.com/Azure/AzureDataLake/blob/master/Samples/PowerShell/ADLAUsers/Add-AdlaJobUser.ps1).
3. Eseguire lo script di PowerShell. 

Il comando di esempio per concedere all'utente l'accesso per inviare processi, visualizzare nuovi metadati del processo e i metadati precedenti è:

`Add-AdlaJobUser.ps1 -Account myadlsaccount -EntityToAdd 546e153e-0ecf-417b-ab7f-aa01ce4a7bff -EntityType User -FullReplication`


## <a name="next-steps"></a>Passaggi successivi

* [Panoramica di Azure Data Lake Analytics](data-lake-analytics-overview.md)
* [Introduzione a Data Lake Analytics con il portale di Azure](data-lake-analytics-get-started-portal.md)
* [Gestire Azure Data Lake Analytics tramite Azure PowerShell](data-lake-analytics-manage-use-powershell.md)

