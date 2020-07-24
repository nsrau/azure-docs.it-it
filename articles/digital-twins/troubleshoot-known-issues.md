---
title: Problemi noti-gemelli digitali di Azure
description: Ottenere supporto per il riconoscimento e la mitigazione di problemi noti con i dispositivi gemelli digitali di Azure.
author: baanders
ms.author: baanders
ms.topic: troubleshooting
ms.service: digital-twins
ms.date: 07/14/2020
ms.openlocfilehash: bdde2076039a6f7687e06edef6dfd6f6f5148ce4
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87044147"
---
# <a name="known-issues-in-azure-digital-twins"></a>Problemi noti nei dispositivi gemelli digitali di Azure

Questo articolo fornisce informazioni sui problemi noti associati ai dispositivi gemelli digitali di Azure.

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