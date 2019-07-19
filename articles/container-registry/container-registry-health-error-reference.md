---
title: Riferimento di errore per il controllo integrità-Azure Container Registry
description: Codici di errore e possibili soluzioni per i problemi rilevati mediante l'esecuzione del comando AZ ACR check-Health Diagnostic in Azure Container Registry
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 07/02/2019
ms.author: danlep
ms.openlocfilehash: 672d446fa8dc27612c7b046cac109bfa4ca5fec5
ms.sourcegitcommit: f5075cffb60128360a9e2e0a538a29652b409af9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/18/2019
ms.locfileid: "68309383"
---
# <a name="health-check-error-reference"></a>Riferimento errore controllo integrità

Di seguito sono riportati i dettagli sui codici di errore restituiti dal comando [AZ ACR check-Health][az-acr-check-health] . Per ogni errore, sono elencate le possibili soluzioni.

## <a name="dockercommanderror"></a>DOCKER_COMMAND_ERROR

Questo errore indica che non è stato possibile trovare il client Docker per l'interfaccia della riga di comando. Di conseguenza, non vengono eseguiti i seguenti controlli aggiuntivi: ricerca della versione di Docker, valutazione dello stato del daemon Docker ed esecuzione di un comando Docker pull.

*Possibili soluzioni*: Installare il client Docker; aggiungere il percorso di Docker alle variabili di sistema.

## <a name="dockerdaemonerror"></a>DOCKER_DAEMON_ERROR

Questo errore indica che lo stato del daemon Docker non è disponibile o che non è stato possibile raggiungerlo tramite l'interfaccia della riga di comando. Di conseguenza, le operazioni di Docker, ad `docker login` esempio `docker pull`e, non sono disponibili tramite l'interfaccia della riga di comando.

*Possibili soluzioni*: Riavviare il daemon Docker oppure verificare che sia installato correttamente.

## <a name="dockerversionerror"></a>DOCKER_VERSION_ERROR

Questo errore indica che l'interfaccia della riga di comando non è `docker --version`stata in grado di eseguire il comando.

*Possibili soluzioni*: Provare a eseguire il comando manualmente, verificare di avere la versione più recente dell'interfaccia della riga di comando ed esaminare il messaggio di errore.

## <a name="dockerpullerror"></a>DOCKER_PULL_ERROR

Questo errore indica che l'interfaccia della riga di comando non è stata in grado di eseguire il pull di un'immagine di esempio nell'ambiente.

*Possibili soluzioni*: Verificare che tutti i componenti necessari per eseguire il pull di un'immagine vengano eseguiti correttamente.

## <a name="helmcommanderror"></a>HELM_COMMAND_ERROR

Questo errore indica che il client Helm non è stato trovato dall'interfaccia della riga di comando, che impedisce altre operazioni Helm.

*Possibili soluzioni*: Verificare che il client Helm sia installato e che il relativo percorso venga aggiunto alle variabili di ambiente di sistema.

## <a name="helmversionerror"></a>HELM_VERSION_ERROR

Questo errore indica che l'interfaccia della riga di comando non è stata in grado di determinare la versione di Helm installata. Questo problema può verificarsi se la versione dell'interfaccia della riga di comando di Azure (o se la versione Helm) utilizzata è obsoleta.

*Possibili soluzioni*: Eseguire l'aggiornamento alla versione più recente dell'interfaccia della riga di comando di Azure o alla versione consigliata. eseguire il comando manualmente ed esaminare il messaggio di errore.

## <a name="connectivitydnserror"></a>CONNECTIVITY_DNS_ERROR

Questo errore indica che il DNS per il server di accesso del registro di sistema specificato è stato sottoposto a ping, ma non ha risposto, il che significa che non è disponibile. Questo può indicare alcuni problemi di connettività. In alternativa, il registro di sistema potrebbe non esistere, l'utente potrebbe non disporre delle autorizzazioni per il registro di sistema (per recuperare il server di accesso in modo corretto) o il registro di sistema di destinazione si trova in un cloud diverso da quello usato nell'interfaccia della riga di comando di Azure.

*Possibili soluzioni*: Convalida connettività; verificare l'ortografia del registro di sistema e il registro di sistema esistente. Verificare che l'utente disponga delle autorizzazioni appropriate e che il cloud del registro di sistema corrisponda a quello usato nell'interfaccia della riga di comando di Azure.

## <a name="connectivityforbiddenerror"></a>CONNECTIVITY_FORBIDDEN_ERROR

Questo errore indica che l'endpoint di richiesta per il registro di sistema specificato ha risposto con uno stato HTTP non consentito 403. Questo errore indica che gli utenti non hanno accesso al registro di sistema, probabilmente a causa di una configurazione di rete virtuale.

*Possibili soluzioni*: Rimuovere le regole della rete virtuale o aggiungere l'indirizzo IP del client corrente all'elenco degli indirizzi consentiti.

## <a name="connectivitychallengeerror"></a>CONNECTIVITY_CHALLENGE_ERROR

Questo errore indica che l'endpoint di verifica del registro di sistema di destinazione non ha emesso una richiesta di verifica.

*Possibili soluzioni*: Riprovare tra qualche minuto. Se l'errore persiste, aprire un problema in https://aka.ms/acr/issues.

## <a name="connectivityaadloginerror"></a>CONNECTIVITY_AAD_LOGIN_ERROR

Questo errore indica che l'endpoint di verifica del registro di sistema di destinazione ha emesso una richiesta, ma il registro di sistema non supporta l'autenticazione Azure Active Directory.

*Possibili soluzioni*: Provare con un altro modo per eseguire l'autenticazione, ad esempio con le credenziali di amministratore. Se gli utenti devono eseguire l'autenticazione con Azure Active Directory, aprire un problema https://aka.ms/acr/issues in.

## <a name="connectivityrefreshtokenerror"></a>CONNECTIVITY_REFRESH_TOKEN_ERROR

Questo errore indica che il server di accesso del registro di sistema non ha risposto con un token di aggiornamento, pertanto l'accesso al registro di sistema di destinazione è stato negato. Questo errore può verificarsi se l'utente non ha le autorizzazioni corrette per il registro di sistema o se le credenziali utente per l'interfaccia della riga di comando di Azure non sono aggiornate.

*Possibili soluzioni*: Verificare che l'utente disponga delle autorizzazioni appropriate per il registro di sistema. eseguire `az login` per aggiornare autorizzazioni, token e credenziali.

## <a name="connectivityaccesstokenerror"></a>CONNECTIVITY_ACCESS_TOKEN_ERROR

Questo errore indica che il server di accesso del registro di sistema non ha risposto con un token di accesso, in modo che l'accesso al registro di destinazione sia stato negato. Questo errore può verificarsi se l'utente non ha le autorizzazioni corrette per il registro di sistema o se le credenziali utente per l'interfaccia della riga di comando di Azure non sono aggiornate.

*Possibili soluzioni*: Verificare che l'utente disponga delle autorizzazioni appropriate per il registro di sistema. eseguire `az login` per aggiornare autorizzazioni, token e credenziali.

## <a name="loginservererror"></a>LOGIN_SERVER_ERROR

Questo errore indica che l'interfaccia della riga di comando non è stata in grado di trovare il server di accesso del registro di sistema specificato e non è stato trovato alcun suffisso predefinito per il Cloud corrente. Questo errore può verificarsi se il registro di sistema non esiste, se l'utente non ha le autorizzazioni corrette per il registro di sistema, se il cloud del registro di sistema e il cloud dell'interfaccia della riga di comando di Azure corrente non corrispondono o se la versione dell'interfaccia della riga di comando di Azure è obsoleta.

*Possibili soluzioni*: Verificare che l'ortografia sia corretta e che il registro esista. Verificare che l'utente disponga delle autorizzazioni corrette per il registro di sistema e che i cloud del registro di sistema e l'ambiente dell'interfaccia della riga di comando corrispondano; aggiornare l'interfaccia della riga di comando di Azure alla versione più recente.

## <a name="next-steps"></a>Passaggi successivi

Per le opzioni per verificare l'integrità di un registro, vedere [verificare l'integrità di un registro contenitori di Azure](container-registry-check-health.md).

Vedere le domande [frequenti](container-registry-faq.md) sulle domande frequenti e altri problemi noti relativi ad Azure container Registry.





<!-- LINKS - internal -->
[az-acr-check-health]: /cli/azure/acr#az-acr-check-health
