---
title: Usare le revisioni per apportare modifiche non di rilievo in modo sicuro in Gestione API di Azure | Microsoft Docs
description: Seguire le procedure di questa esercitazione per apprendere come apportare modifiche non di rilievo usando le revisioni in Gestione API.
services: api-management
documentationcenter: 
author: juliako
manager: cfowler
editor: 
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.custom: mvc
ms.topic: tutorial
ms.date: 11/19/2017
ms.author: apimpm
ms.openlocfilehash: 50d7ac17faebb34f1a1f9a3259aa0196950391d9
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/04/2017
---
# <a name="use-revisions-to-make-non-breaking-changes-safely"></a>Usare le revisioni per apportare modifiche non di rilievo in modo sicuro
Se l'API è pronta e inizia a essere usata dagli sviluppatori, è in genere necessario fare attenzione alle modifiche apportate a tale API e contemporaneamente non interrompere i chiamanti dell'API. È anche utile far conoscere agli sviluppatori le modifiche apportate. A tal fine, in Gestione API di Azure sono disponibili le **revisioni**. Per altre informazioni, vedere [Versions & revisions](https://blogs.msdn.microsoft.com/apimanagement/2017/09/14/versions-revisions/) (Versioni e revisioni) e [API Versioning with Azure API Management](https://blogs.msdn.microsoft.com/apimanagement/2017/09/13/api-versioning-with-azure-api-management/) (Controllo delle versioni delle API con Gestione API di Azure).

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Aggiungere una nuova revisione
> * Apportare modifiche non di rilievo alla revisione
> * Rendere la revisione corrente e aggiungere una voce del registro modifiche
> * Esplorare il portale per sviluppatori per visualizzare le modifiche e il registro modifiche

![Registro modifiche nel portale per sviluppatori](media/api-management-getstarted-revise-api/azure_portal.PNG)

## <a name="prerequisites"></a>Prerequisiti

+ Completare la guida introduttiva seguente: [Creare un'istanza di Gestione API di Azure](get-started-create-service-instance.md).
+ Completare anche l'esercitazione seguente: [Importare e pubblicare la prima API](import-and-publish.md).

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="add-a-new-revision"></a>Aggiungere una nuova revisione

1. Selezionare la pagina **API**.
2. Selezionare **Conference API** (API Conferenza) nell'elenco di API oppure l'API a cui si vuole aggiungere le revisioni.
3. Fare clic sulla scheda **Revisioni** dal menu nella parte superiore della pagina.
4. Selezionare **+ Aggiungi revisione**.

    > [!TIP]
    > È anche possibile scegliere **Aggiungi revisione** nel menu di scelta rapida (**...**) dell'API.
    
    ![Menu Revisioni nella parte superiore dello schermo](media/api-management-getstarted-revise-api/TopMenu.PNG)

5. Fornire una descrizione per la nuova revisione, per facilitare la comprensione dell'uso.
6. Selezionare **Crea**
7. La nuova revisione è stata creata.

    > [!NOTE]
    > L'API originale resta nella **Revisione 1**. Si tratta della revisione che gli utenti continueranno a chiamare fino a quando non si imposta come corrente una revisione diversa.

## <a name="make-non-breaking-changes-to-your-revision"></a>Apportare modifiche non di rilievo alla revisione

1. Selezionare **Conference API** (API Conferenza) nell'elenco di API.
2. Selezionare la scheda **Progettazione** nella parte superiore della schermata.
3. Si noti che il **selettore di revisione** (sopra alla scheda Progettazione) mostra la revisione corrente come **Revisione 2**.

    > [!TIP]
    > Usare il selettore di revisione per alternare le revisioni su cui si intende lavorare.

4. Selezionare **+ Aggiungi operazione**.
5. Impostare la nuova operazione su **POST**e il nome e il nome visualizzato dell'operazione su **test**
6. **Salvare** la nuova operazione.
7. È stata ora apportata una modifica alla **Revisione 2**. Usare il **selettore di revisione** nella parte superiore della pagina per tornare alla **Revisione 1**.
8. Si noti che la nuova operazione non viene visualizzata nella **Revisione 1**. 

## <a name="make-your-revision-current-and-add-a-change-log-entry"></a>Rendere la revisione corrente e aggiungere una voce del registro modifiche
1. Selezionare la scheda **Revisioni** dal menu nella parte superiore della pagina.

    ![Il menu Revisione nella schermata di revisione.](media/api-management-getstarted-revise-api/RevisionsMenu.PNG)
1. Aprire il menu di scelta rapida (**...**) per la **Revisione 2**.
2. Selezionare **Make Current** (Imposta come corrente). Selezionare **Post to Public Change Log for this API**(Postare nel registro modifiche pubblico per questa API) per pubblicare delle note sulla modifica.
3. Selezionare **Post to Public Change Log for this API**(Postare nel registro modifiche pubblico per questa API).
4. Fornire una descrizione della modifica comprensibile agli sviluppatori, ad esempio **Test delle revisioni. Aggiunta una nuova operazione "test".**
5. La **Revisione 2** è ora quella corrente.

## <a name="browse-the-developer-portal-to-see-changes-and-change-log"></a>Esplorare il portale per sviluppatori per visualizzare le modifiche e il registro modifiche
1. Nel portale di Azure selezionare **API**
2. Selezionare **Portale per sviluppatori** dal menu principale.
3. Selezionare **API**, quindi **Conference API** (API Conferenza).
4. Si noti che è ora disponibile la nuova operazione **test**.
5. Selezionare **API Change History** (Cronologia delle modifiche all'API) al di sotto del nome dell'API.
6. Si noti che il registro modifiche è ora visualizzato in questo elenco.

    ![Portale per sviluppatori](media/api-management-getstarted-revise-api/developer_portal.PNG)

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è appreso come:

> [!div class="checklist"]
> * Aggiungere una nuova revisione
> * Apportare modifiche non di rilievo alla revisione
> * Rendere la revisione corrente e aggiungere una voce del registro modifiche
> * Esplorare il portale per sviluppatori per visualizzare le modifiche e il registro modifiche

Passare all'esercitazione successiva:

> [!div class="nextstepaction"]
> [Pubblicare più versioni dell'API](api-management-get-started-publish-versions.md)