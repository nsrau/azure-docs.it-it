---
title: Come registrare un ticket di supporto per eventi di sicurezza - Azure | Microsoft Docs
description: Informazioni per i venditori di Azure Marketplace che hanno identificato un potenziale evento di sicurezza, per la registrazione di un ticket appropriato.
services: security
documentationcenter: na
author: DavidBosland
manager: lakoch
editor: v-dabosl
ms.assetid: f1ffde66-98f0-4c3e-ad94-fee1f97cae03
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/13/2017
ms.author: v-dabosl
ms.openlocfilehash: 243b6b7846719d3f848073e3aa973c9406ca59c7
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60586609"
---
# <a name="how-to-log-a-security-event-support-ticket"></a>Come registrare un ticket di supporto per eventi di sicurezza

1. Passare alla pagina del [supporto per editori](https://support.microsoft.com/en-us/getsupport?wf=0&tenant=ClassicCommercial&oaspworkflow=start_1.0.0.0&locale=en-us&supportregion=en-us&pesid=16230&ccsid=636450758943226673) e accedere con le proprie credenziali Microsoft.
2. Selezionare "Evento di sicurezza" come tipo di problema e scegliere tra le categorie "Evento imprevisto per la sicurezza" e "Vulnerabilità".

    ![Tipo di evento e definizione][1]

3. Dopo aver selezionato il tipo di problema e la categoria, fare clic sul pulsante **Start request** (Avvia richiesta). Specificare le informazioni seguenti per consentire una migliore comprensione del problema.

    i. Vulnerabilità e/o problema riscontrato

    ii. Per le vulnerabilità, specificare CVE (mitre.org) o compilato out CVSS3 v3 calculator (https://www.first.org/cvss/calculator/3.0).

    iii. Eventuale risoluzione o mitigazione Se disponibile, specificare la procedura di correzione.

    iv. Eventuale messaggio da inviare ai clienti È possibile collaborare con Microsoft per creare un messaggio appropriato, se applicabile.

4. Conferma dell'invio: dopo l'invio del problema, verrà confermata la ricezione entro un giorno lavorativo e saranno assegnate una priorità e una gravità.

    - Se è necessario comunicare con Microsoft in relazione al problema, usare il numero di conferma in tutta la corrispondenza.
    - È possibile visualizzare lo stato del problema in qualsiasi momento.

5. Operazioni successive A seconda del problema e della gravità, potranno essere eseguiti questi passaggi:

    - Microsoft comunicherà il risultato della valutazione. A seconda del risultato, potrà rimuovere l'offerta o richiederne la modifica. In questo caso, collaborerà con il venditore per ridurre al minimo l'interruzione per i clienti interessati.
    - Microsoft collaborerà con il venditore per mitigare l'impatto dell'evento imprevisto o della vulnerabilità per i clienti comuni.


[1]: ./media/azure-security-event-support-ticket/chart.png
