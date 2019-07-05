---
title: Riferimento a un errore per il controllo di integrità - registro contenitori di Azure
description: Codici di errore e possibili soluzioni a problemi rilevati utilizzando il comando az acr integrità verifica diagnostica nel registro contenitori di Azure
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 07/02/2019
ms.author: danlep
ms.openlocfilehash: fc29b27cbb7eea983140c59529d981ad95c27ae8
ms.sourcegitcommit: 978e1b8cac3da254f9d6309e0195c45b38c24eb5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/03/2019
ms.locfileid: "67555111"
---
# <a name="health-check-error-reference"></a>Riferimento Errore controllo integrità

Di seguito sono i dettagli sui codici di errore restituiti dal [integrità con az acr verifica][az-acr-check-health] comando. Per ogni errore, sono elencate le possibili soluzioni.

## <a name="dockercommanderror"></a>DOCKER_COMMAND_ERROR

Questo errore indica che il client Docker della riga di comando non è stato trovato. Di conseguenza, non vengono eseguiti i seguenti controlli aggiuntivi: ricerca lo stato del daemon di versione di Docker, la valutazione di Docker e l'esecuzione di Docker pull comando.

*Possibili soluzioni*: Installare il client Docker; aggiungere il percorso di Docker per le variabili di sistema.

## <a name="dockerdaemonerror"></a>DOCKER_DAEMON_ERROR

Questo errore indica che lo stato del daemon Docker non è disponibile o che non è raggiungibile tramite la CLI. Di conseguenza, le operazioni di Docker (ad esempio `docker login` e `docker pull`) non sono disponibili tramite l'interfaccia della riga di comando.

*Possibili soluzioni*: Riavviare il daemon Docker, o verificare che sia installato correttamente.

## <a name="dockerversionerror"></a>DOCKER_VERSION_ERROR

Questo errore indica che della riga di comando non è riuscito a eseguire il comando `docker --version`.

*Possibili soluzioni*: Provare a eseguire il comando manualmente, assicurarsi di avere la versione più recente dell'interfaccia della riga e provare a utilizzare il messaggio di errore.

## <a name="dockerpullerror"></a>DOCKER_PULL_ERROR

Questo errore indica che l'interfaccia della riga di comando non è in grado di estrarre un'immagine di esempio all'ambiente.

*Possibili soluzioni*: Verificare che tutti i componenti necessari per il pull di un'immagine vengano eseguiti correttamente.

## <a name="helmcommanderror"></a>HELM_COMMAND_ERROR

Questo errore indica che il client Helm non è stato trovato per l'interfaccia della riga di comando, che preclude altre operazioni di Helm.

*Possibili soluzioni*: Verificare che sia installato il client Helm, e che il relativo percorso viene aggiunto alle variabili di ambiente di sistema.

## <a name="helmversionerror"></a>HELM_VERSION_ERROR

Questo errore indica che l'interfaccia della riga di comando è riuscito a determinare la versione di Helm installata. Questa situazione può verificarsi se la versione della riga di comando di Azure (o se la versione di Helm) in uso è obsoleta.

*Possibili soluzioni*: Aggiornare alla versione più recente della riga di comando di Azure o per la versione di Helm consigliata; eseguire il comando manualmente ed esaminare il messaggio di errore.

## <a name="connectivitydnserror"></a>CONNECTIVITY_DNS_ERROR

Questo errore indica che il DNS per il server di accesso del Registro di sistema specificato è stato inviato un ping, ma non risponde, il che significa che non è disponibile. Questo può indicare problemi di connettività. In alternativa, potrebbe non essere presente nel Registro di sistema, l'utente potrebbe non avere le autorizzazioni nel Registro di sistema (per recuperare correttamente il server di accesso) o il Registro di sistema di destinazione è in un cloud diverso da quello utilizzato nel comando di Azure.

*Possibili soluzioni*: Convalidare la connettività; controllare l'ortografia del Registro di sistema e del Registro di sistema è presente. Verificare che l'utente disponga delle autorizzazioni appropriate su di esso e che il cloud del Registro di sistema sia uguale a quello usato nel comando di Azure.

## <a name="connectivityforbiddenerror"></a>CONNECTIVITY_FORBIDDEN_ERROR

Questo errore indica che l'endpoint di richiesta per il Registro di sistema specificata ha risposto con uno stato 403 HTTP non è consentito. Questo errore indica che gli utenti autorizzati ad accedere al Registro di sistema, probabilmente a causa di una configurazione di rete virtuale.

*Possibili soluzioni*: Rimuovere le regole di rete virtuale oppure aggiungere l'indirizzo IP client corrente all'elenco consentiti.

## <a name="connectivitychallengeerror"></a>CONNECTIVITY_CHALLENGE_ERROR

Questo errore indica che l'endpoint di richiesta di verifica del Registro di sistema di destinazione non ha emesso una richiesta di verifica.

*Possibili soluzioni*: Riprovare dopo qualche minuto. Se l'errore persiste, aprire un problema in https://aka.ms/acr/issues.

## <a name="connectivityaadloginerror"></a>CONNECTIVITY_AAD_LOGIN_ERROR

Questo errore indica che l'endpoint di richiesta di verifica del Registro di sistema di destinazione rilasciato una sfida, ma il Registro di sistema non supporta l'autenticazione di Azure Active Directory.

*Possibili soluzioni*: Provare un modo diverso per l'autenticazione, ad esempio, con le credenziali di amministratore. Se gli utenti devono eseguire l'autenticazione con Azure Active Directory, aprire un problema in https://aka.ms/acr/issues.

## <a name="connectivityrefreshtokenerror"></a>CONNECTIVITY_REFRESH_TOKEN_ERROR

Questo errore indica che il server di accesso del Registro di sistema non ha risposto con un token di aggiornamento, in modo che è stato negato l'accesso al Registro di sistema di destinazione. Questo errore può verificarsi se l'utente non ha le autorizzazioni appropriate nel Registro di sistema o se le credenziali utente per il comando di Azure non sono aggiornate.

*Possibili soluzioni*: Verificare se l'utente disponga delle autorizzazioni appropriate nel Registro di sistema. eseguire `az login` per aggiornare le autorizzazioni, i token e le credenziali.

## <a name="connectivityaccesstokenerror"></a>CONNECTIVITY_ACCESS_TOKEN_ERROR

Questo errore indica che il server di accesso del Registro di sistema non ha risposto con un token di accesso, in modo che è stato negato l'accesso al Registro di sistema di destinazione. Questo errore può verificarsi se l'utente non ha le autorizzazioni appropriate nel Registro di sistema o se le credenziali utente per il comando di Azure non sono aggiornate.

*Possibili soluzioni*: Verificare se l'utente disponga delle autorizzazioni appropriate nel Registro di sistema. eseguire `az login` per aggiornare le autorizzazioni, i token e le credenziali.

## <a name="loginservererror"></a>LOGIN_SERVER_ERROR

Questo errore indica che l'interfaccia della riga di comando è riuscito a trovare il server di accesso del Registro di sistema specificato e per il cloud corrente è stato trovato alcun suffisso predefinito. Questo errore può verificarsi se il Registro di sistema non esiste, se l'utente non dispone delle autorizzazioni appropriate nel Registro di sistema, se non corrispondono a cloud del Registro di sistema e il cloud di Azure dell'interfaccia della riga corrente o se la versione della riga di comando di Azure è obsoleta.

*Possibili soluzioni*: Verificare che l'ortografia sia corretta e che esista il Registro di sistema; Verificare che l'utente abbia le autorizzazioni appropriate nel Registro di sistema e i cloud del Registro di sistema e l'ambiente della riga di comando corrispondenti. aggiornamento della riga di comando di Azure alla versione più recente.

## <a name="next-steps"></a>Passaggi successivi

Per le opzioni controllare l'integrità di un registro di sistema, vedere [controlla l'integrità di un registro contenitori di Azure](container-registry-check-health.md).

Vedere le [domande frequenti su](container-registry-faq.md) per domande frequenti e altri problemi noti sul registro contenitori di Azure.





<!-- LINKS - internal -->
[az-acr-check-health]: /cli/azure/acr#az-acr-check-health
