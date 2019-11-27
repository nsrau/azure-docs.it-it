---
title: Riferimento di errore per i controlli di integrità
description: Codici di errore e possibili soluzioni per i problemi rilevati mediante l'esecuzione del comando AZ ACR check-Health Diagnostic in Azure Container Registry
ms.topic: article
ms.date: 07/02/2019
ms.openlocfilehash: a921d17ad7d01b134f5bfa33a1d9a768d3ea94df
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/24/2019
ms.locfileid: "74455026"
---
# <a name="health-check-error-reference"></a>Riferimento errore controllo integrità

Di seguito sono riportati i dettagli sui codici di errore restituiti dal comando [AZ ACR check-Health][az-acr-check-health] . Per ogni errore, sono elencate le possibili soluzioni.

## <a name="docker_command_error"></a>DOCKER_COMMAND_ERROR

Questo errore indica che non è stato possibile trovare il client Docker per l'interfaccia della riga di comando. Di conseguenza, non vengono eseguiti i seguenti controlli aggiuntivi: ricerca della versione di Docker, valutazione dello stato del daemon Docker ed esecuzione di un comando Docker pull.

*Possibili soluzioni*: installare il client Docker; aggiungere il percorso di Docker alle variabili di sistema.

## <a name="docker_daemon_error"></a>DOCKER_DAEMON_ERROR

Questo errore indica che lo stato del daemon Docker non è disponibile o che non è stato possibile raggiungerlo tramite l'interfaccia della riga di comando. Di conseguenza, le operazioni di Docker, ad esempio `docker login` e `docker pull`, non sono disponibili tramite l'interfaccia della riga di comando.

*Possibili soluzioni*: riavviare il daemon Docker o verificare che sia installato correttamente.

## <a name="docker_version_error"></a>DOCKER_VERSION_ERROR

Questo errore indica che l'interfaccia della riga di comando non è stata in grado di eseguire il comando `docker --version`.

*Possibili soluzioni*: provare a eseguire il comando manualmente, verificare di avere la versione più recente dell'interfaccia della riga di comando ed esaminare il messaggio di errore.

## <a name="docker_pull_error"></a>DOCKER_PULL_ERROR

Questo errore indica che l'interfaccia della riga di comando non è stata in grado di eseguire il pull di un'immagine di esempio nell'ambiente.

*Possibili soluzioni*: verificare che tutti i componenti necessari per eseguire il pull di un'immagine vengano eseguiti correttamente.

## <a name="helm_command_error"></a>HELM_COMMAND_ERROR

Questo errore indica che il client Helm non è stato trovato dall'interfaccia della riga di comando, che impedisce altre operazioni Helm.

*Possibili soluzioni*: verificare che il client Helm sia installato e che il relativo percorso venga aggiunto alle variabili di ambiente del sistema.

## <a name="helm_version_error"></a>HELM_VERSION_ERROR

Questo errore indica che l'interfaccia della riga di comando non è stata in grado di determinare la versione di Helm installata. Questo problema può verificarsi se la versione dell'interfaccia della riga di comando di Azure (o se la versione Helm) utilizzata è obsoleta.

*Possibili soluzioni*: eseguire l'aggiornamento alla versione più recente dell'interfaccia della riga di comando di Azure o alla versione consigliata. eseguire il comando manualmente ed esaminare il messaggio di errore.

## <a name="connectivity_dns_error"></a>CONNECTIVITY_DNS_ERROR

Questo errore indica che il DNS per il server di accesso del registro di sistema specificato è stato sottoposto a ping, ma non ha risposto, il che significa che non è disponibile. Questo può indicare alcuni problemi di connettività. In alternativa, il registro di sistema potrebbe non esistere, l'utente potrebbe non disporre delle autorizzazioni per il registro di sistema (per recuperare il server di accesso in modo corretto) o il registro di sistema di destinazione si trova in un cloud diverso da quello usato nell'interfaccia della riga di comando di Azure.

*Possibili soluzioni*: convalidare la connettività; verificare l'ortografia del registro di sistema e il registro di sistema esistente. Verificare che l'utente disponga delle autorizzazioni appropriate e che il cloud del registro di sistema corrisponda a quello usato nell'interfaccia della riga di comando di Azure.

## <a name="connectivity_forbidden_error"></a>CONNECTIVITY_FORBIDDEN_ERROR

Questo errore indica che l'endpoint di richiesta per il registro di sistema specificato ha risposto con uno stato HTTP non consentito 403. Questo errore indica che gli utenti non hanno accesso al registro di sistema, probabilmente a causa di una configurazione di rete virtuale. Per visualizzare le regole del firewall attualmente configurate, eseguire `az acr show --query networkRuleSet --name <registry>`.

*Possibili soluzioni*: rimuovere le regole della rete virtuale o aggiungere l'indirizzo IP del client corrente all'elenco degli indirizzi consentiti.

## <a name="connectivity_challenge_error"></a>CONNECTIVITY_CHALLENGE_ERROR

Questo errore indica che l'endpoint di verifica del registro di sistema di destinazione non ha emesso una richiesta di verifica.

*Possibili soluzioni*: riprovare tra qualche minuto. Se l'errore persiste, aprire un problema in https://aka.ms/acr/issues.

## <a name="connectivity_aad_login_error"></a>CONNECTIVITY_AAD_LOGIN_ERROR

Questo errore indica che l'endpoint di verifica del registro di sistema di destinazione ha emesso una richiesta, ma il registro di sistema non supporta l'autenticazione Azure Active Directory.

*Possibili soluzioni*: provare un modo diverso per eseguire l'autenticazione, ad esempio, con le credenziali di amministratore. Se gli utenti devono eseguire l'autenticazione con Azure Active Directory, aprire un problema in https://aka.ms/acr/issues.

## <a name="connectivity_refresh_token_error"></a>CONNECTIVITY_REFRESH_TOKEN_ERROR

Questo errore indica che il server di accesso del registro di sistema non ha risposto con un token di aggiornamento, pertanto l'accesso al registro di sistema di destinazione è stato negato. Questo errore può verificarsi se l'utente non ha le autorizzazioni corrette per il registro di sistema o se le credenziali utente per l'interfaccia della riga di comando di Azure non sono aggiornate.

*Possibili soluzioni*: verificare se l'utente dispone delle autorizzazioni appropriate per il registro di sistema. eseguire `az login` per aggiornare autorizzazioni, token e credenziali.

## <a name="connectivity_access_token_error"></a>CONNECTIVITY_ACCESS_TOKEN_ERROR

Questo errore indica che il server di accesso del registro di sistema non ha risposto con un token di accesso, in modo che l'accesso al registro di destinazione sia stato negato. Questo errore può verificarsi se l'utente non ha le autorizzazioni corrette per il registro di sistema o se le credenziali utente per l'interfaccia della riga di comando di Azure non sono aggiornate.

*Possibili soluzioni*: verificare se l'utente dispone delle autorizzazioni appropriate per il registro di sistema. eseguire `az login` per aggiornare autorizzazioni, token e credenziali.

## <a name="connectivity_ssl_error"></a>CONNECTIVITY_SSL_ERROR

Questo errore indica che il client non è riuscito a stabilire una connessione sicura al registro contenitori. Questo errore si verifica in genere se si esegue o si usa un server proxy.

*Possibili soluzioni*: altre informazioni sull'uso di un proxy sono [disponibili qui](https://github.com/Azure/azure-cli/blob/master/doc/use_cli_effectively.md#working-behind-a-proxy).

## <a name="login_server_error"></a>LOGIN_SERVER_ERROR

Questo errore indica che l'interfaccia della riga di comando non è stata in grado di trovare il server di accesso del registro di sistema specificato e non è stato trovato alcun suffisso predefinito per il Cloud corrente. Questo errore può verificarsi se il registro di sistema non esiste, se l'utente non ha le autorizzazioni corrette per il registro di sistema, se il cloud del registro di sistema e il cloud dell'interfaccia della riga di comando di Azure corrente non corrispondono o se la versione dell'interfaccia della riga di comando di Azure è obsoleta.

*Possibili soluzioni*: verificare che l'ortografia sia corretta e che il registro esista. Verificare che l'utente disponga delle autorizzazioni corrette per il registro di sistema e che i cloud del registro di sistema e l'ambiente dell'interfaccia della riga di comando corrispondano; aggiornare l'interfaccia della riga di comando di Azure alla versione più recente.

## <a name="next-steps"></a>Passaggi successivi

Per le opzioni per verificare l'integrità di un registro, vedere [verificare l'integrità di un registro contenitori di Azure](container-registry-check-health.md).

Vedere le domande [frequenti](container-registry-faq.md) sulle domande frequenti e altri problemi noti relativi ad Azure container Registry.





<!-- LINKS - internal -->
[az-acr-check-health]: /cli/azure/acr#az-acr-check-health
