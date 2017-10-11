---
title: Usare le revisioni per aggiornare l'API con Gestione API di Azure | Microsoft Docs
description: Seguire le procedure di questa esercitazione per apprendere come apportare modifiche non di rilievo usando le revisioni in Gestione API.
services: api-management
documentationcenter: 
author: mattfarm
manager: anneta
editor: 
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 08/18/2017
ms.author: apimpm
ms.translationtype: HT
ms.sourcegitcommit: 212b163c49fdd133b0ed2d99b33035fcc391aec6
ms.openlocfilehash: 0d67166a16ae4d640080ad83e7625e594b0dc246
ms.contentlocale: it-it
ms.lasthandoff: 09/21/2017

---

# <a name="make-non-breaking-changes-safely-using-revisions"></a>Apportare modifiche non di rilievo usando le revisioni in modo sicuro
L'esercitazione descrive come apportare modifiche all'API in modo sicuro e come comunicare agli sviluppatori le modifiche apportate.

## <a name="prerequisites"></a>Prerequisiti
Per completare questa esercitazione è necessario creare un servizio Gestione API e disporre di un'API esistente che sia possibile modificare al posto dell'API Conference (API Conferenza).

## <a name="about-revisions"></a>Informazioni sulle revisioni
Se l'API è pronta e inizia a essere usata dagli sviluppatori, è in genere necessario fare attenzione a che le modifiche apportate successivamente a tale API non compromettano il lavoro dei chiamanti. È anche utile far conoscere agli sviluppatori le modifiche apportate. A tal fine, in Gestione API di Azure sono disponibili le **revisioni**.

## <a name="walkthrough"></a>Procedura dettagliata
In questa procedura dettagliata viene aggiunta una nuova revisione e a questa un'operazione, quindi la revisione viene resa corrente creando una voce del registro modifiche.

## <a name="add-a-new-revision"></a>Aggiungere una nuova revisione
1. Aprire la pagina delle **API** nel servizio di Gestione API nel portale di Azure.
2. Selezionare **API conferenza** nell'elenco di API, quindi selezionare la scheda **Revisioni** dal menu nella parte superiore della pagina.
3. Selezionare **+ Aggiungi revisione**.

    > [!TIP]
    > È anche possibile scegliere **Aggiungi revisione** nel menu di scelta rapida (**... **) dell'API.
![Menu Revisioni nella parte superiore dello schermo](media/api-management-getstarted-revise-api/TopMenu.PNG)

4. Fornire una descrizione per la nuova revisione, per facilitare la comprensione dell'uso.
5. Selezionare **Crea**
6. La nuova revisione è stata creata.

    > [!NOTE]
    > L'API originale resta nella **Revisione 1**. Si tratta della revisione che gli utenti continueranno a chiamare fino a quando non si imposta come corrente una revisione diversa.

## <a name="make-non-breaking-changes-to-your-revision"></a>Apportare modifiche non di rilievo alla revisione
1. Selezionare la scheda **Progettazione** nella parte superiore della schermata.
2. Si noti che il **selettore di revisione** (sopra alla scheda Progettazione) mostra la revisione corrente come **Revisione 2**.

    > [!TIP]
    > Usare il selettore di revisione per alternare le revisioni su cui si intende lavorare.

3. Selezionare **+ Aggiungi operazione**.
4. Impostare la nuova operazione su **POST**e il nome e il nome visualizzato dell'operazione su **Commenti e suggerimenti**
5. **Salvare** la nuova operazione.
6. È stata ora apportata una modifica alla **Revisione 2**. Usare il **selettore di revisione** nella parte superiore della pagina per tornare alla **Revisione 1**.
7. Si noti che la nuova operazione non viene visualizzata nella **Revisione 1**. 

## <a name="make-your-revision-current-and-add-a-change-log-entry"></a>Rendere la revisione corrente e aggiungere una voce del registro modifiche
1. Selezionare la scheda **Revisioni** dal menu nella parte superiore della pagina.
![Il menu Revisione nella schermata di revisione.](media/api-management-getstarted-revise-api/RevisionsMenu.PNG)
2. Aprire il menu di scelta rapida (**... **) per la **Revisione 2**.
3. Selezionare **Make Current** (Imposta come corrente).
![Rendere la revisione corrente e inserire un post nel registro modifiche](media/api-management-getstarted-revise-api/MakeCurrent.PNG)
4. Selezionare **Post to Public Change Log for this API**(Postare nel registro modifiche pubblico per questa API).
5. Fornire una descrizione della modifica comprensibile agli sviluppatori, ad esempio **"Aggiunta nuova operazione per commenti e suggerimenti".**
6. La **Revisione 2** è ora quella corrente.

## <a name="browse-the-developer-portal-to-see-changes-and-change-log"></a>Esplorare il portale per sviluppatori per visualizzare le modifiche e il registro modifiche
1. Selezionare **Portale per sviluppatori** dal menu principale.
2. Selezionare **API**, quindi **Conference API** (API Conferenza).
3. Si noti che è ora disponibile la nuova operazione **Commenti e suggerimenti**.
4. Selezionare **API Change History** (Cronologia delle modifiche all'API) al di sotto del nome dell'API.
5. Si noti che il registro modifiche è ora visualizzato in questo elenco.
![Registro modifiche nel portale per sviluppatori](media/api-management-getstarted-revise-api/ChangeLogDevPortal.PNG)

## <a name="next-steps"></a>Passaggi successivi
[Pubblicare versioni dell'API con Gestione API di Azure](#api-management-getstarted-publish-versions.md)
