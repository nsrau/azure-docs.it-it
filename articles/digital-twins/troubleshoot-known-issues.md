---
title: Problemi noti-gemelli digitali di Azure
description: Ottenere supporto per il riconoscimento e la mitigazione di problemi noti con i dispositivi gemelli digitali di Azure.
author: baanders
ms.author: baanders
ms.topic: troubleshooting
ms.service: digital-twins
ms.date: 07/14/2020
ms.openlocfilehash: 8bcbe395f78d3e4e9a6f7f615edc61eaa04347cf
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/21/2020
ms.locfileid: "92311660"
---
# <a name="known-issues-in-azure-digital-twins"></a>Problemi noti nei dispositivi gemelli digitali di Azure

Questo articolo fornisce informazioni sui problemi noti associati ai dispositivi gemelli digitali di Azure.

## <a name="400-client-error-bad-request-in-cloud-shell"></a>"errore client 400: richiesta non valida" nella Cloud Shell

I comandi in Cloud Shell in esecuzione a *https://shell.azure.com* possono avere esito negativo a intermittenza con l'errore "400 errore del client: richiesta non valida per l'URL: http://localhost:50342/oauth2/token ", seguita da analisi dello stack completo.

Per i dispositivi gemelli digitali di Azure in particolare, questo influisca sui gruppi di comandi seguenti:
* `az dt route`
* `az dt model`
* `az dt twin`

### <a name="troubleshooting-steps"></a>Passaggi per la risoluzione dei problemi

Per risolvere il problema, eseguire nuovamente il `az login` comando in cloud Shell e completare i passaggi di accesso successivi. Successivamente, dovrebbe essere possibile eseguire di nuovo il comando.

In alternativa, è possibile aprire il riquadro Cloud Shell nel portale di Azure e completare il lavoro Cloud Shell da questa posizione:

:::image type="content" source="media/includes/portal-cloud-shell.png" alt-text="Visualizzazione della portale di Azure con l'icona ' Cloud Shell ' evidenziata e la Cloud Shell visualizzata nella parte inferiore della finestra del portale":::

Infine, un'altra soluzione consiste nell' [installare l'interfaccia della](/cli/azure/install-azure-cli?view=azure-cli-latest) riga di comando di Azure nel computer, in modo da poter eseguire i comandi dell'interfaccia della riga di comando Questo problema non viene riscontrato nell'interfaccia della riga di comando locale.

### <a name="possible-causes"></a>Possibili cause

Questo è il risultato di un problema noto nel Cloud Shell: il [*recupero del token da cloud Shell intermitted ha esito negativo con errore del Client 400: richiesta non valida*](https://github.com/Azure/azure-cli/issues/11749).

Si presenta un problema con i token di autenticazione dell'istanza di Azure Digital gemelli e con l'autenticazione basata su [identità gestita](../active-directory/managed-identities-azure-resources/overview.md) predefinita di cloud Shell. Il passaggio per la risoluzione dei problemi di esecuzione `az login` consente di passare dall'autenticazione di identità gestita, in modo da eseguire il debug di questo problema.

Questo non influisce sui comandi di Azure Digital gemelli dei `az dt` `az dt endpoint` gruppi di comandi o, perché usano un tipo diverso di token di autenticazione (basato su ARM), che non ha un problema con l'autenticazione di identità gestita del cloud Shell.

## <a name="missing-role-assignment-after-scripted-setup"></a>Assegnazione di ruolo mancante dopo l'installazione tramite script

Alcuni utenti potrebbero riscontrare problemi con la parte dell'assegnazione di ruolo di [*procedura: configurare un'istanza e l'autenticazione (con script)*](how-to-set-up-instance-scripted.md). Lo script non indica errori, ma il ruolo *proprietario (anteprima) di Azure Digital Twins* non è stato assegnato correttamente all'utente e questo problema influirà sulla capacità di creare altre risorse.

Per determinare se l'assegnazione di ruolo è stata configurata correttamente dopo l'esecuzione dello script, seguire le istruzioni riportate nella sezione [*verificare l'assegnazione del ruolo utente*](how-to-set-up-instance-scripted.md#verify-user-role-assignment) dell'articolo di installazione. Se l'utente non viene visualizzato con questo ruolo, questo problema ha effetto.

### <a name="troubleshooting-steps"></a>Passaggi per la risoluzione dei problemi

Per risolverlo, è possibile configurare manualmente l'assegnazione di ruolo usando l'interfaccia della riga di comando o portale di Azure. 

Seguire queste istruzioni:
* [CLI](how-to-set-up-instance-cli.md#set-up-user-access-permissions)
* [portal](how-to-set-up-instance-portal.md#set-up-user-access-permissions)

### <a name="possible-causes"></a>Possibili cause

Per gli utenti che hanno effettuato l'accesso con un [account Microsoft personale (MSA)](https://account.microsoft.com/account), l'ID principale dell'utente che identifica l'utente in comandi come questo può essere diverso dal messaggio di posta elettronica di accesso dell'utente, rendendo difficile l'individuazione e l'utilizzo da parte dello script per assegnare il ruolo in modo appropriato.

## <a name="issue-with-interactive-browser-authentication"></a>Problemi con l'autenticazione interattiva del browser

Quando si scrive il codice di autenticazione nelle applicazioni dei dispositivi gemelli digitali di Azure usando la versione **1.2.0** della **libreria [Azure. Identity](/dotnet/api/azure.identity?view=azure-dotnet) **, è possibile che si verifichino problemi con il metodo [InteractiveBrowserCredential](/dotnet/api/azure.identity.interactivebrowsercredential?view=azure-dotnet) .

Questa non è la versione più recente della libreria. La versione più recente è la **1.2.2**.

Il metodo interessato viene usato negli articoli seguenti: 
* [*Esercitazione: Scrivere il codice di un'app client*](tutorial-code.md)
* [*Procedura: scrivere codice di autenticazione dell'app*](how-to-authenticate-client.md)
* [*Procedura: usare le API e gli SDK di dispositivi digitali gemelli di Azure*](how-to-use-apis-sdks.md)

Il problema include una risposta di errore "Azure. Identity. AuthenticationFailedException" quando si tenta di eseguire l'autenticazione in una finestra del browser. È possibile che la finestra del browser non venga avviata completamente o che venga eseguita correttamente l'autenticazione dell'utente, mentre l'applicazione client ha ancora esito negativo con l'errore.

### <a name="troubleshooting-steps"></a>Passaggi per la risoluzione dei problemi

Per risolvere il, aggiornare le applicazioni in modo da usare la `Azure.Identity` versione **1.2.2**. Con questa versione della libreria, il browser dovrebbe caricare e autenticarsi come previsto.

### <a name="possible-causes"></a>Possibili cause

Questo problema è correlato a un problema aperto con la versione più recente della `Azure.Identity` libreria (versione **1.2.0**): [*non è possibile eseguire l'autenticazione quando si usa InteractiveBrowserCredential*](https://github.com/Azure/azure-sdk-for-net/issues/13940).

Questo problema verrà visualizzato se si usa la versione **1.2.0** nell'applicazione Digital gemelli di Azure o se si aggiunge la libreria al progetto senza specificare una versione (come per impostazione predefinita la versione più recente).

## <a name="next-steps"></a>Passaggi successivi

Scopri di più su sicurezza e autorizzazioni nei dispositivi gemelli digitali di Azure:
* [*Concetti: sicurezza per le soluzioni di dispositivi gemelli digitali di Azure*](concepts-security.md)