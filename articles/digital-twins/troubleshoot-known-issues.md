---
title: Problemi noti-gemelli digitali di Azure
description: Ottenere supporto per il riconoscimento e la mitigazione di problemi noti con i dispositivi gemelli digitali di Azure.
author: baanders
ms.author: baanders
ms.topic: troubleshooting
ms.service: digital-twins
ms.date: 07/14/2020
ms.openlocfilehash: 0c008061d2d4fafa96eda934d5026c92839a0bdb
ms.sourcegitcommit: d18a59b2efff67934650f6ad3a2e1fe9f8269f21
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/20/2020
ms.locfileid: "88661487"
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

## <a name="missing-role-assignment-after-scripted-setup"></a>Assegnazione di ruolo mancante dopo l'installazione tramite script

Alcuni utenti potrebbero riscontrare problemi con la parte dell'assegnazione di ruolo di [*procedura: configurare un'istanza e l'autenticazione (con script)*](how-to-set-up-instance-scripted.md). Lo script non indica errori, ma il ruolo *proprietario (anteprima) di Azure Digital Twins* non è stato assegnato correttamente all'utente e questo avrà un effetto sulla possibilità di creare altre risorse.

Per determinare se l'assegnazione di ruolo è stata configurata correttamente dopo l'esecuzione dello script, seguire le istruzioni riportate nella sezione [*verificare l'assegnazione del ruolo utente*](how-to-set-up-instance-scripted.md#verify-user-role-assignment) dell'articolo di installazione. Se l'utente non viene visualizzato con questo ruolo, questo problema ha effetto.

### <a name="troubleshooting-steps"></a>Passaggi per la risoluzione dei problemi

Per risolverlo, è possibile configurare manualmente l'assegnazione di ruolo usando l'interfaccia della riga di comando o portale di Azure. 

Seguire queste istruzioni:
* [CLI](how-to-set-up-instance-cli.md#set-up-user-access-permissions)
* [portal](how-to-set-up-instance-portal.md#set-up-user-access-permissions)

### <a name="possible-causes"></a>Possibili cause

Per gli utenti che hanno effettuato l'accesso con un [account Microsoft personale (MSA)](https://account.microsoft.com/account), l'ID principale dell'utente che identifica l'utente in comandi come questo può essere diverso dall'indirizzo di posta elettronica di accesso dell'utente, rendendo difficile l'individuazione e l'utilizzo da parte dello script per assegnare il ruolo in modo appropriato.

## <a name="issue-with-interactive-browser-authentication"></a>Problemi con l'autenticazione interattiva del browser

Quando si scrive il codice di autenticazione nelle applicazioni dei dispositivi gemelli digitali di Azure usando la versione più recente (versione **1.2.0**) della **libreria [Azure. Identity](https://docs.microsoft.com/dotnet/api/azure.identity?view=azure-dotnet) **, è possibile che si verifichino problemi con il metodo [InteractiveBrowserCredential](https://docs.microsoft.com/dotnet/api/azure.identity.interactivebrowsercredential?view=azure-dotnet) .

Il metodo interessato viene usato negli articoli seguenti: 
* [*Esercitazione: Scrivere il codice di un'app client*](tutorial-code.md)
* [*Procedura: scrivere codice di autenticazione dell'app*](how-to-authenticate-client.md)
* [*Procedura: usare le API e gli SDK di dispositivi digitali gemelli di Azure*](how-to-use-apis-sdks.md)

Il problema include una risposta di errore "Azure. Identity. AuthenticationFailedException" quando si tenta di eseguire l'autenticazione in una finestra del browser. È possibile che la finestra del browser non venga avviata completamente o che venga eseguita correttamente l'autenticazione dell'utente, mentre l'applicazione client ha ancora esito negativo con l'errore.

### <a name="troubleshooting-steps"></a>Passaggi per la risoluzione dei problemi

Per risolvere il comportamento, fare in modo che le applicazioni usino in modo esplicito Azure. Identity versione **1.1.1**. Con questa versione della libreria, il browser dovrebbe caricare e autenticarsi come previsto.

>[!NOTE]
> Non è sufficiente aggiungere la libreria senza specificare alcuna versione, perché continuerà a essere impostata automaticamente sulla versione più recente di **1.2.0**. È necessario specificare la versione **1.1.1** in modo esplicito.

### <a name="possible-causes"></a>Possibili cause

Questo problema è correlato a un problema aperto con la versione più recente di Azure. Identity Library (versione **1.2.0**): [*non è possibile eseguire l'autenticazione quando si usa InteractiveBrowserCredential*](https://github.com/Azure/azure-sdk-for-net/issues/13940).

Questo problema verrà visualizzato se si usa la versione **1.2.0** nell'applicazione Digital gemelli di Azure o se si aggiunge la libreria al progetto senza specificare una versione (come per impostazione predefinita la versione più recente).

## <a name="next-steps"></a>Passaggi successivi

Scopri di più su sicurezza e autorizzazioni nei dispositivi gemelli digitali di Azure:
* [*Concetti: sicurezza per le soluzioni di dispositivi gemelli digitali di Azure*](concepts-security.md)