---
title: Procedure consigliate di Azure Stack convalida | Microsoft Docs
description: Questo articolo descrive le procedure consigliate per l'uso come un servizio di convalida.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/11/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 03/11/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 568817c6e25952f15a396e5748d314187345945b
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57769047"
---
# <a name="create-an-oem-package"></a>Creare un pacchetto dell'OEM

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Il pacchetto di estensione OEM di Azure Stack è il meccanismo dal quale OEM contenuto specifico viene aggiunto all'infrastruttura di Azure Stack, per l'uso nella distribuzione, nonché processi operativi, ad esempio update, l'espansione e sostituzione di campo.

## <a name="creating-the-package"></a>Creazione del pacchetto

Dopo aver creato e convalidato, il pacchetto di estensione OEM può essere usato in VaaS.  Prima di continuare, assicurarsi di aver completato i passaggi per la [creazione di un pacchetto di OEM](https://microsoft.sharepoint.com/:w:/r/teams/cloudsolutions/Sacramento/_layouts/15/Doc.aspx?sourcedoc=%7BD7406069-7661-419C-B3B1-B6A727AB3972%7D&file=Azure%20Stack%20OEM%20Extension%20Package.docx&action=default&mobileredirect=true). Il pacchetto viene quindi inviato a Microsoft insieme ai risultati del test VaaS per la firma del flusso di lavoro di convalida del pacchetto. La procedura seguente illustra come aggregare i file generati in un unico file zip che VaaS possono utilizzare.

1. Identificare il contenuto seguente per il pacchetto:
    - Un file zip contenente i contenuti del pacchetto
    - Un file manifesto denominato `oemMetadata.xml`, che deve essere identica nel contenuto del file Metadata. XML nella radice del contenuto del pacchetto.

2. Selezionare i file di contenuto e creare un file zip dal contenuto:

    ![Contenuto del file zip](media/vaas-create-oem-package-1.png) ![Comprimi elemento contenuto](media/vaas-create-oem-package-2.png)

3. Rinominare il file risultante è sufficientemente descrittiva per l'utente per facilitarne l'identificazione.

## <a name="verifying-the-contents"></a>Verifica per determinare se il contenuto

Per convalidare la struttura del file zip, esaminarlo e non verificare che sia presente alcuna sottocartella. Il TLD con il contenuto compresso. Di seguito è riportata una struttura di pacchetto valido.
> [!IMPORTANT]
> Comprimere la cartella padre anziché il contenuto causerà l'esito negativo di firma del pacchetto.

![Contenuto del pacchetto compresso in modo corretto](media/vaas-create-oem-package-3.png)

Il file zip può essere caricato in VaaS e firmato da Microsoft nel flusso di lavoro di convalida del pacchetto.

## <a name="next-steps"></a>Passaggi successivi

- [Convalidare un pacchetto dell'OEM](azure-stack-vaas-validate-oem-package.md)
