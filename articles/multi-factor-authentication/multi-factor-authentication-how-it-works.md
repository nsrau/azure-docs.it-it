---
title: Azure Multi-Factor Authentication - Come funziona
description: "Azure multi-Factor Authentication contribuisce a salvaguardare l&quot;accesso a dati e applicazioni rispondendo alla richiesta degli utenti di poter usare un processo di accesso semplice. Offre ulteriore sicurezza richiedendo una seconda forma di autenticazione nonché un&quot;autenticazione avanzata tramite una gamma di opzioni di verifica semplice."
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
editor: curtland
ms.assetid: d14db902-9afe-4fca-b3a5-4bd54b3d8ec5
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/10/2017
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: e028130569a1e55834f68bde87f21f6f7505d41f
ms.openlocfilehash: 9132e9bde7403e2868b92cb1149904d926ff5d8e
ms.lasthandoff: 02/23/2017


---
# <a name="how-azure-multi-factor-authentication-works"></a>Come funziona Azure Multi-Factor Authentication
La sicurezza dell'autenticazione a più fattori si fonda sull'approccio basato su livelli. La manomissione di più fattori rappresenta una sfida significativa per gli autori di attacchi. Tuttavia, anche se un autore di attacco riesce a ottenere la password dell'utente, questa risulta inutile se non è in possesso del dispositivo attendibile. Se l'utente smarrisce il dispositivo, la persona che lo trova non potrà utilizzarlo a meno che non ne conosca anche la password.

![Verifica](./media/multi-factor-authentication-how-it-works/howitworks.png)

Azure multi-Factor Authentication contribuisce a salvaguardare l'accesso a dati e applicazioni rispondendo alla richiesta degli utenti di poter usare un processo di accesso semplice.  Offre ulteriore sicurezza richiedendo una seconda forma di autenticazione nonché un'autenticazione avanzata tramite una gamma di opzioni di verifica semplice.

Per informazioni dettagliate sul funzionamento, vedere il video seguente:

> [!VIDEO https://channel9.msdn.com/Events/TechEd/Europe/2014/EM-B313/player]

## <a name="methods-available-for-two-step-verification"></a>Metodi disponibili per la verifica in due passaggi
Quando un utente accede, una verifica aggiuntiva viene inviata all'utente.  Di seguito è riportato un elenco di metodi che possono essere utilizzati per la seconda verifica.

| Metodo di verifica | Descrizione |
| --- | --- |
| Chiamata telefonica |Viene effettuata una chiamata al telefono registrato dell'utente chiedendogli di verificare che sta effettuando l'accesso premendo il simbolo # o inserendo un PIN. |
| SMS |Verrà inviato un SMS al cellulare dell'utente con un codice di sei cifre.  Immettere il codice per completare il processo di verifica. |
| Notifica dell'app per dispositivi mobili |Viene inviata una richiesta allo smartphone dell'utente chiedendogli di completare la verifica selezionando Verifica dall'app per dispositivi mobili. Ciò si verifica se la notifica dell'app è stata selezionata come metodo di verifica principale.  Se l'utente riceve la richiesta quando non sta effettuando l'accesso, potrà scegliere di segnalare la frode. |
| Codice di verifica dell'app per dispositivi mobili |L'app per dispositivi mobili, che è in esecuzione sullo smartphone dell'utente, mostra un codice di verifica di 6 cifre che cambia ogni 30 secondi. L'utente individua il codice più recente e lo inserisce nella pagina di accesso per completare il processo di verifica. Ciò si verifica se il codice di verifica è stato selezionato come metodo di verifica principale. |
| Token OATH di terze parti | Azure Multi-Factor Authentication può essere configurato per accettare metodi di verifica di terze parti. |

Azure multi-Factor Authentication fornisce metodi di verifica selezionabili per cloud e server. È quindi possibile scegliere i metodi disponibili per gli utenti: telefonata, SMS, notifica dell'app o codici dell'app. Per altre informazioni, vedere i [metodi di verifica selezionabili](multi-factor-authentication-whats-next.md#selectable-verification-methods).

## <a name="next-steps"></a>Passaggi successivi

- Informazioni sulle diverse [versioni e i metodi di utilizzo per Multi-Factor Authentication](multi-factor-authentication-versions-plans.md)

- Scegliere se distribuire Azure MFA [nel cloud o in locale](multi-factor-authentication-get-started.md)
