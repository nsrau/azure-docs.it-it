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
ms.openlocfilehash: 753d6abcdd063d5c092b0c90770396d176bc52e8
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/04/2017
---
# <a name="how-to-log-a-security-event-support-ticket"></a>Come registrare un ticket di supporto per eventi di sicurezza

1. Passare alla pagina del [supporto per editori](https://support.microsoft.com/en-us/getsupport?wf=0&tenant=ClassicCommercial&oaspworkflow=start_1.0.0.0&locale=en-us&supportregion=en-us&pesid=16230&ccsid=636450758943226673) e accedere con le proprie credenziali Microsoft.
2. Selezionare "Evento di sicurezza" come tipo di problema e scegliere tra le categorie "Evento imprevisto per la sicurezza" e "Vulnerabilità".

    ![Tipo di evento e definizione][1]

3. Dopo aver selezionato il tipo di problema e la categoria, fare clic sul pulsante **Start request** (Avvia richiesta). Specificare le informazioni seguenti per consentire una migliore comprensione del problema.

    i. Vulnerabilità e/o problema riscontrato

    ii. Per le vulnerabilità, specificare il tipo di CVE (mitre.org) o fornire il documento ottenuto con CVSS v3.0 Calculator (https://www.first.org/cvss/calculator/3.0).

    iii. Eventuale risoluzione o mitigazione Se disponibile, specificare la procedura di correzione.

    iv. Eventuale messaggio da inviare ai clienti È possibile collaborare con Microsoft per creare un messaggio appropriato, se applicabile.

4. Conferma dell'invio: dopo l'invio del problema, verrà confermata la ricezione entro un giorno lavorativo e saranno assegnate una priorità e una gravità.

    - Se è necessario comunicare con Microsoft in relazione al problema, usare il numero di conferma in tutta la corrispondenza.
    - È possibile visualizzare lo stato del problema in qualsiasi momento.

5. Operazioni successive A seconda del problema e della gravità, potranno essere eseguiti questi passaggi:

    - Microsoft comunicherà il risultato della valutazione. A seconda del risultato, potrà rimuovere l'offerta o richiederne la modifica. In questo caso, collaborerà con il venditore per ridurre al minimo l'interruzione per i clienti interessati.
    - Microsoft collaborerà con il venditore per mitigare l'impatto dell'evento imprevisto o della vulnerabilità per i clienti comuni.


[1]: ./media/azure-security-event-support-ticket/chart.png
