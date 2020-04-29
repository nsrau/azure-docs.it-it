---
title: Archiviare il pacchetto di AppSource in una risorsa di archiviazione di Azure e generare un URL con la chiave di firma di accesso condiviso
description: Illustrare in dettaglio i passaggi necessari per caricare e proteggere un pacchetto di AppSource.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/18/2018
ms.author: dsindona
ms.openlocfilehash: 57bc370fd160b8b3d6d7941ea28cd460c99f3d96
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80285368"
---
<a name="store-your-appsource-package-to-azure-storage-and-generate-a-url-with-sas-key"></a>Archiviare il pacchetto di AppSource in una risorsa di archiviazione di Azure e generare un URL con la chiave di firma di accesso condiviso
=============================================================================

Per garantire la protezione dei file, tutti i partner devono archiviare il file del pacchetto di AppSource in un account di archiviazione BLOB di Azure e usare una chiave di firma di accesso condiviso per condividerlo. Si recupererà il file del pacchetto dal percorso di archiviazione di Azure per la certificazione e per usarlo per le versioni di valutazione di AppSource.

Seguire la procedura seguente per caricare il pacchetto nell'archivio BLOB:

1. Passare a <https://azure.microsoft.com> e creare un account di valutazione o di fatturazione gratuito.

2. Accedi al [portale di Azure](https://portal.azure.com/).

3. Creare un nuovo account di archiviazione facendo clic su **+ Nuovo** e andando all'account **Dati e archiviazione**.

   ![Pannello dei dati e di archiviazione nel portale di Microsoft Azure](media/CRMScreenShot7.png)

4. Immettere un **Nome** e il nome del **Gruppo di risorse**, quindi fare clic sul pulsante **Crea**.

   ![Creare un account di archiviazione nel portale di Microsoft Azure](media/CRMScreenShot8.png)

5. Passare al gruppo di risorse appena creato e creare un nuovo contenitore BLOB.

   ![Caricare il pacchetto come BLOB usando il portale di Microsoft Azure](media/CRMScreenShot9.png)

6. Se non è già stato fatto, scaricare e installare Microsoft [Azure Storage Explorer](https://storageexplorer.com/).

7. Aprire Storage Explorer e fare clic sull'icona per connettersi all'account di archiviazione di Azure.

8. Passare al contenitore di BLOB creato e fare clic su **Carica** per aggiungere il file zip del pacchetto.

   ![Caricare il pacchetto usando Microsoft Storage Explorer](media/CRMScreenShot10.png)

9. Fare clic sul file con il pulsante destro del mouse, quindi selezionare **Ottieni la firma di accesso condiviso**.

   ![Ottenere la firma di accesso condiviso di un file di Azure](media/CRMScreenShot11.png)

10. Modificare la **Scadenza** per rendere attiva la firma di accesso condiviso per un mese, quindi fare clic su **Crea**.

    ![Modificare la data di scadenza della firma di accesso condiviso di un file di Azure](media/CRMScreenShot12.png)

11. Copiare il campo URL e salvarlo per usarlo in seguito. Immettere questo URL quando si crea l'offerta associata. 

    ![Copiare l'URL della firma di accesso condiviso di un file di Azure](media/CRMScreenShot13.png)

