---
title: Nessun gruppo di connettori funzionante trovato per un'applicazione Proxy di applicazione | Microsoft Docs
description: Risoluzione dei problemi riscontrati quando non esiste alcun connettore funzionante in un gruppo di connettori per l'applicazione con Proxy di applicazione di Azure AD
services: active-directory
documentationcenter: 
author: ajamess
manager: mtillman
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: 7aad43be4dc411215a4b54c21a16c4cbb949c7ec
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/11/2017
---
# <a name="no-working-connector-group-found-for-an-application-proxy-application"></a>Nessun gruppo di connettori funzionante trovato per un'applicazione Proxy di applicazione

Questo articolo fornisce istruzioni sulla risoluzione di problemi comuni riscontrati quando non viene rilevato un connettore per un'applicazione Proxy di applicazione integrata con Azure Active Directory.

## <a name="overview-of-steps"></a>Panoramica dei passaggi
Se nella propria applicazione non è presente alcun connettore funzionante in un gruppo di connettori, il problema può essere risolto in diversi modi:

-   Se il gruppo non contiene connettori, è possibile:

    -   Scaricare un nuovo connettore sul server locale corretto e assegnarlo a questo gruppo

    -   Spostare un connettore attivo nel gruppo

-   Se il gruppo non contiene connettori attivi, è possibile:

    -   Identificare il motivo per cui il connettore è inattivo e risolvere il problema

    -   Spostare un connettore attivo nel gruppo

Per sapere quale di questi è il problema riscontrato, aprire il menu "Proxy di applicazione" nell'applicazione, quindi cercare il messaggio di avviso del gruppo di connettori. Specifica che il gruppo deve contenere almeno un connettore (se il gruppo non ne contiene alcuno) o che non vi sono connettori attivi (anche se sono inclusi probabilmente connettori inattivi).

   ![Selezione del gruppo di connettori nel portale di Azure](./media/application-proxy-connectivity-no-working-connector/no-active-connector.png)

Per informazioni dettagliate su ognuna di queste opzioni, vedere la sezione corrispondente riportata di seguito. Ognuno di questi passaggi presuppone che si inizi dalla pagina di gestione del connettore. Se si sta analizzando il messaggio di errore sopra riportato, è possibile passare a questa pagina facendo clic sul messaggio di avviso. In alternativa, passare a **Azure Active Directory**, fare clic su **Applicazioni aziendali** e quindi su **Proxy di applicazione.**

   ![Gestione del gruppo di connettori nel portale di Azure](./media/application-proxy-connectivity-no-working-connector/app-proxy.png)

## <a name="download-a-new-connector"></a>Scaricare un nuovo connettore

Per scaricare un nuovo connettore, usare il pulsante "Scaricare il connettore" nella parte superiore della pagina.

Si noti che il connettore deve essere installato su un computer che abbia una linea di visuale diretta all'applicazione back-end e si trova in genere sullo stesso server dell'applicazione. Dopo essere stato scaricato, il connettore viene visualizzato in questo menu. Fare clic sul connettore e usare l'elenco a discesa "Gruppo di connettori" per assicurarsi che appartenga al gruppo corretto. Salvare la modifica.

   ![Scaricare il connettore dal portale di Azure](./media/application-proxy-connectivity-no-working-connector/download-connector.png)
   
## <a name="move-an-active-connector"></a>Spostare un connettore attivo

Se si dispone di un connettore attivo che deve appartenere a questo gruppo e ha una linea di visuale sull'applicazione back-end, è possibile spostare il connettore nel gruppo assegnato. A tale scopo, fare clic sul connettore. Nel campo "Gruppo di connettori", usare l'elenco a discesa per selezionare il gruppo corretto e fare clic su Salva.

## <a name="resolve-an-inactive-connector"></a>Risolvere un connettore inattivo

Se il gruppo include solo connettori inattivi, sono probabilmente in un computer che non dispone di tutte le porte necessarie sbloccate.

Vedere il documento di risoluzione dei problemi relativi alle porte per informazioni dettagliate sull'analisi di questo problema.

## <a name="next-steps"></a>Passaggi successivi
[Comprendere i connettori del proxy applicazione di Azure AD](application-proxy-understand-connectors.md)


