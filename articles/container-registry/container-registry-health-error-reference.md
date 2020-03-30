---
title: Informazioni di riferimento sugli errori per i controlli di integritàError reference for health checks
description: Codici di errore e possibili soluzioni ai problemi rilevati eseguendo il comando di diagnostica di controllo az acr nel Registro di sistema contenitore di AzureError codes and possible solutions to problems found by running the az acr check-health diagnostic command in Azure Container Registry
ms.topic: article
ms.date: 07/02/2019
ms.openlocfilehash: 971b28b2bf8d9ac22cec0efe979837886762cf17
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80289142"
---
# <a name="health-check-error-reference"></a>Informazioni di riferimento sugli errori del controllo di integrità

Di seguito sono riportati i dettagli sui codici di errore restituiti dal comando [az acr check-health.][az-acr-check-health] Per ogni errore sono elencate le possibili soluzioni.

## <a name="docker_command_error"></a>DOCKER_COMMAND_ERROR

Questo errore indica che non è stato possibile trovare il client Docker per l'interfaccia della riga di comando. Di conseguenza, non vengono eseguiti i seguenti controlli aggiuntivi: individuazione della versione Docker, valutazione dello stato del daemon Docker e esecuzione di un comando pull Docker.

*Possibili soluzioni*: Installare il client Docker; aggiungere il percorso Docker alle variabili di sistema.

## <a name="docker_daemon_error"></a>DOCKER_DAEMON_ERROR

Questo errore indica che lo stato di daemon Docker non è disponibile o che non è stato possibile raggiungerlo utilizzando l'interfaccia della riga di comando. Di conseguenza, le operazioni `docker login` Docker (ad esempio e `docker pull`) non sono disponibili tramite l'interfaccia della riga di comando.

*Potenziali soluzioni*: Riavviare Docker daemon o verificare che sia installato correttamente.

## <a name="docker_version_error"></a>DOCKER_VERSION_ERROR

Questo errore indica che l'interfaccia `docker --version`della riga di comando non è stata in grado di eseguire il comando.

*Potenziali soluzioni*: Provare a eseguire il comando manualmente, assicurarsi di disporre della versione dell'interfaccia della riga di comando più recente e analizzare il messaggio di errore.

## <a name="docker_pull_error"></a>DOCKER_PULL_ERROR

Questo errore indica che l'interfaccia della riga di comando non è stata in grado di eseguire il pull di un'immagine di esempio nell'ambiente.

*Possibili soluzioni*: Verificare che tutti i componenti necessari per estrarre un'immagine funzionino correttamente.

## <a name="helm_command_error"></a>HELM_COMMAND_ERROR

Questo errore indica che il client Helm non è stato trovato dall'interfaccia della riga di comando, il che impedisce altre operazioni Helm.

*Possibili soluzioni*: verificare che il client Helm sia installato e che il relativo percorso venga aggiunto alle variabili di ambiente del sistema.

## <a name="helm_version_error"></a>HELM_VERSION_ERROR

Questo errore indica che l'interfaccia della riga di comando non è stata in grado di determinare la versione di Helm installata. Ciò può verificarsi se la versione dell'interfaccia della riga di comando di Azure (o se la versione Helm) in uso è obsoleta.

*Potenziali soluzioni:* aggiornamento all'ultima versione dell'interfaccia della riga di comando di Azure o alla versione di Helm consigliata. eseguire il comando manualmente e analizzare il messaggio di errore.

## <a name="connectivity_dns_error"></a>CONNECTIVITY_DNS_ERROR

Questo errore indica che il DNS per il server di accesso del Registro di sistema specificato è stato eseguito il ping ma non ha risposto, il che significa che non è disponibile. Ciò può indicare alcuni problemi di connettività. In alternativa, il Registro di sistema potrebbe non esistere, l'utente potrebbe non disporre delle autorizzazioni sul Registro di sistema (per recuperare correttamente il server di accesso) o il Registro di sistema di destinazione si trova in un cloud diverso da quello usato nell'interfaccia della riga di comando di Azure.Alternatively, the registry might not exist, the user might not have the permissions on the registry (to retrieve its login server properly) or the target registry is in a different cloud than the one used in the Azure CLI.

*Possibili soluzioni*: Convalidare la connettività; verificare l'ortografia del Registro di sistema e che il Registro di sistema esiste; Verificare che l'utente disponga delle autorizzazioni appropriate e che il cloud del Registro di sistema sia lo stesso usato nell'interfaccia della riga di comando di Azure.Verify that the user have the right permissions on it and that the registry's cloud is the same that is used in the Azure CLI.

## <a name="connectivity_forbidden_error"></a>CONNECTIVITY_FORBIDDEN_ERROR

Questo errore indica che l'endpoint di verifica per il Registro di sistema specificato ha risposto con uno stato HTTP 403 non consentito. Questo errore indica che gli utenti non hanno accesso al Registro di sistema, molto probabilmente a causa di una configurazione di rete virtuale. Per visualizzare le regole del `az acr show --query networkRuleSet --name <registry>`firewall attualmente configurate, eseguire .

*Possibili soluzioni*: rimuovere le regole di rete virtuale o aggiungere l'indirizzo IP client corrente all'elenco dei prodotti consentiti.

## <a name="connectivity_challenge_error"></a>CONNECTIVITY_CHALLENGE_ERROR

Questo errore indica che l'endpoint di verifica del Registro di sistema di destinazione non ha emesso una richiesta di verifica.

*Possibili soluzioni*: Riprovare dopo qualche tempo. Se l'errore persiste, https://aka.ms/acr/issuesaprire un problema in .

## <a name="connectivity_aad_login_error"></a>CONNECTIVITY_AAD_LOGIN_ERROR

Questo errore indica che l'endpoint di verifica del Registro di sistema di destinazione ha emesso una sfida, ma il Registro di sistema non supporta l'autenticazione di Azure Active Directory.This error means that the challenge endpoint of the target registry issued a challenge, but the registry does not support Azure Active Directory authentication.

*Possibili soluzioni*: provare un modo diverso per eseguire l'autenticazione, ad esempio con credenziali di amministratore. Se gli utenti devono eseguire l'autenticazione https://aka.ms/acr/issuestramite Azure Active Directory, aprire un problema in .

## <a name="connectivity_refresh_token_error"></a>CONNECTIVITY_REFRESH_TOKEN_ERROR

Questo errore indica che il server di accesso al Registro di sistema non ha risposto con un token di aggiornamento, pertanto l'accesso al Registro di sistema di destinazione è stato negato. Questo errore può verificarsi se l'utente non dispone delle autorizzazioni appropriate nel Registro di sistema o se le credenziali utente per l'interfaccia della riga di comando di Azure non sono aggiornate.

*Potenziali soluzioni*: Verificare se l'utente dispone delle autorizzazioni appropriate per il Registro di sistema; per `az login` aggiornare autorizzazioni, token e credenziali.

## <a name="connectivity_access_token_error"></a>CONNECTIVITY_ACCESS_TOKEN_ERROR

Questo errore indica che il server di accesso al Registro di sistema non ha risposto con un token di accesso, in modo che l'accesso al Registro di sistema di destinazione è stato negato. Questo errore può verificarsi se l'utente non dispone delle autorizzazioni appropriate nel Registro di sistema o se le credenziali utente per l'interfaccia della riga di comando di Azure non sono aggiornate.

*Potenziali soluzioni*: Verificare se l'utente dispone delle autorizzazioni appropriate per il Registro di sistema; per `az login` aggiornare autorizzazioni, token e credenziali.

## <a name="connectivity_ssl_error"></a>CONNECTIVITY_SSL_ERROR

Questo errore indica che il client non è stato in grado di stabilire una connessione sicura al Registro di sistema del contenitore. Generalmente questo errore si verifica se si esegue o si utilizza un server proxy.

*Possibili soluzioni*: Ulteriori informazioni su come lavorare dietro un proxy sono [disponibili qui](https://github.com/Azure/azure-cli/blob/master/doc/use_cli_effectively.md#working-behind-a-proxy).

## <a name="login_server_error"></a>LOGIN_SERVER_ERROR

Questo errore indica che l'interfaccia della riga di comando non è stata in grado di trovare il server di accesso del Registro di sistema specificato e non è stato trovato alcun suffisso predefinito per il cloud corrente. Questo errore può verificarsi se il Registro di sistema non esiste, se l'utente non dispone delle autorizzazioni appropriate per il Registro di sistema, se il cloud del Registro di sistema e il cloud dell'interfaccia della riga di comando di Azure corrente non corrispondono o se la versione dell'interfaccia della riga di comando di Azure è obsoleta.

*Potenziali soluzioni*: Verificare che l'ortografia sia corretta e che il Registro di sistema esista; verificare che l'utente disponga delle autorizzazioni appropriate per il Registro di sistema e che i cloud del Registro di sistema e dell'ambiente CLI corrispondano; aggiornare l'interfaccia della riga di comando di Azure alla versione più recente.

## <a name="notary_version_error"></a>NOTARY_VERSION_ERROR

Questo errore indica che l'interfaccia della riga di comando non è compatibile con la versione attualmente installata di Docker/Notary. Provare a eseguire il downgrade della versione di notary.exe a una versione precedente alla 0.6.0 sostituendo manualmente il client Notary dell'installazione di Docker per risolvere il problema.

## <a name="next-steps"></a>Passaggi successivi

Per le opzioni per controllare l'integrità di un Registro di sistema, vedere [Controllare l'integrità di un registro contenitori](container-registry-check-health.md)di Azure.For options to check the health of a registry, see Check the health of an Azure container registry .

Vedere le [domande frequenti](container-registry-faq.md) per le domande frequenti e altri problemi noti sul Registro di sistema del contenitore di Azure.See the FAQ for frequently asked questions and other known issues about Azure Container Registry.





<!-- LINKS - internal -->
[az-acr-check-health]: /cli/azure/acr#az-acr-check-health
