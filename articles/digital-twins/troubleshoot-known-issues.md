---
title: Problemi noti-gemelli digitali di Azure
description: Ottenere supporto per il riconoscimento e la mitigazione di problemi noti con i dispositivi gemelli digitali di Azure.
author: baanders
ms.author: baanders
ms.topic: troubleshooting
ms.service: digital-twins
ms.date: 07/14/2020
ms.openlocfilehash: 8fce451d9b806d2fa9a4f3d9e1c117de0aaa9fc0
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/20/2020
ms.locfileid: "86531735"
---
# <a name="known-issues-in-azure-digital-twins"></a>Problemi noti nei dispositivi gemelli digitali di Azure

Questo articolo fornisce informazioni sui problemi noti associati ai dispositivi gemelli digitali di Azure.

## <a name="managing-event-routes-in-the-azure-portal"></a>Gestione delle route degli eventi nel portale di Azure

Se è stato eseguito l'accesso al portale con un [**account Microsoft personale (MSA)**](https://account.microsoft.com/account/Account), ad esempio un *@outlook.com* account, verrà visualizzata una schermata *che informa che è necessario disporre delle autorizzazioni per visualizzare le route degli eventi* durante il tentativo di gestire le route degli eventi nel portale, indipendentemente dal livello di autorizzazione.

:::image type="content" source="media/troubleshoot-known-issues/event-route-need-permission.png" alt-text="Screenshot del portale di Azure dell'errore di autorizzazione quando si tenta di creare route di eventi in un'istanza di dispositivi gemelli digitali di Azure":::

### <a name="troubleshooting-steps"></a>Passaggi per la risoluzione dei problemi

Gli utenti che non sono attualmente in grado di gestire le route degli eventi nel portale possono comunque gestire le route degli eventi usando le API dei dispositivi gemelli digitali di Azure o CLI. Il passaggio a uno di questi strumenti per la gestione delle route degli eventi è la strategia consigliata per attenuare questo problema.

Le istruzioni per questa operazione sono reperibili in [*How-to: Manage Endpoints and routes*](how-to-manage-routes.md).

### <a name="possible-causes"></a>Possibili cause

È stato eseguito l'accesso al portale con un [account Microsoft personale (MSA)](https://account.microsoft.com/account/Account), ad esempio un *@outlook.com* account. La gestione delle route degli eventi nel portale di Azure è attualmente disponibile solo per gli utenti di Azure negli account di dominio aziendale.

## <a name="400-client-error-bad-request-in-cloud-shell"></a>"errore client 400: richiesta non valida" nella Cloud Shell

I comandi in Cloud Shell possono avere esito negativo a intermittenza con l'errore "400 errore del client: richiesta non valida per l'URL: http://localhost:50342/oauth2/token " seguita dall'analisi dello stack completo.

### <a name="troubleshooting-steps"></a>Passaggi per la risoluzione dei problemi

Per risolvere il problema, eseguire nuovamente il `az login` comando e completare i passaggi di accesso successivi.

Al termine, sarà possibile eseguire di nuovo il comando.

### <a name="possible-causes"></a>Possibili cause

Questo è il risultato di un problema noto nel Cloud Shell: il [*recupero del token da cloud Shell intermitted ha esito negativo con errore del Client 400: richiesta non valida*](https://github.com/Azure/azure-cli/issues/11749).

## <a name="next-steps"></a>Passaggi successivi

Scopri di più su sicurezza e autorizzazioni nei dispositivi gemelli digitali di Azure:
* [*Concetti: sicurezza per le soluzioni di dispositivi gemelli digitali di Azure*](concepts-security.md)