---
title: Risolvere i problemi di connessione dell'agente di Azure Arc per i server
description: Questo articolo illustra come risolvere i problemi relativi all'agente del computer connesso che si verifica con Azure Arc per i server (anteprima) quando si tenta di connettersi al servizio.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-servers
author: mgoedtel
ms.author: magoedte
ms.date: 07/10/2020
ms.topic: conceptual
ms.openlocfilehash: 37f99ade366a73cb96caf55a562a92476223eb6b
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2020
ms.locfileid: "86262022"
---
# <a name="troubleshoot-the-connected-machine-agent-connection-issues"></a>Risolvere i problemi di connessione dell'agente computer connesso

Questo articolo fornisce informazioni sulla risoluzione dei problemi e sulla risoluzione dei problemi che possono verificarsi durante il tentativo di configurare l'agente di computer connesso di Azure Arc per server (anteprima) per Windows o Linux. Sono inclusi entrambi i metodi di installazione interattiva e su larga scala durante la configurazione della connessione al servizio. Per informazioni generali, vedere [Cenni preliminari su Arc for Servers](./overview.md).

## <a name="agent-verbose-log"></a>Log dettagliato dell'agente

Prima di seguire i passaggi di risoluzione dei problemi descritti più avanti in questo articolo, le informazioni minime necessarie sono il log dettagliato. Contiene l'output dei comandi dello strumento **azcmagent** , quando si usa l'argomento Verbose (-v). I file di log vengono scritti in `%ProgramData%\AzureConnectedMachineAgent\Log\azcmagent.log` per Windows e Linux in `/var/opt/azcmagent/log/azcmagent.log` .

### <a name="windows"></a>Windows

Di seguito è riportato un esempio del comando per abilitare la registrazione dettagliata con l'agente computer connesso per Windows durante l'esecuzione di un'installazione interattiva.

```
& "$env:ProgramFiles\AzureConnectedMachineAgent\azcmagent.exe" connect --resource-group "resourceGroupName" --tenant-id "tenantID" --location "regionName" --subscription-id "subscriptionID" --verbose
```

Di seguito è riportato un esempio del comando per abilitare la registrazione dettagliata con l'agente computer connesso per Windows durante l'esecuzione di un'installazione su larga scala tramite un'entità servizio.

```
& "$env:ProgramFiles\AzureConnectedMachineAgent\azcmagent.exe" connect `
  --service-principal-id "{serviceprincipalAppID}" `
  --service-principal-secret "{serviceprincipalPassword}" `
  --resource-group "{ResourceGroupName}" `
  --tenant-id "{tenantID}" `
  --location "{resourceLocation}" `
  --subscription-id "{subscriptionID}"
  --verbose
```

### <a name="linux"></a>Linux

Di seguito è riportato un esempio del comando per abilitare la registrazione dettagliata con l'agente computer connesso per Linux quando si esegue un'installazione interattiva.

```
azcmagent connect --resource-group "resourceGroupName" --tenant-id "tenantID" --location "regionName" --subscription-id "subscriptionID" --verbose
```

Di seguito è riportato un esempio del comando per abilitare la registrazione dettagliata con l'agente computer connesso per Linux quando si esegue un'installazione su larga scala usando un'entità servizio.

```
azcmagent connect \
  --service-principal-id "{serviceprincipalAppID}" \
  --service-principal-secret "{serviceprincipalPassword}" \
  --resource-group "{ResourceGroupName}" \
  --tenant-id "{tenantID}" \
  --location "{resourceLocation}" \
  --subscription-id "{subscriptionID}"
  --verbose
```

## <a name="agent-connection-issues-to-service"></a>Problemi di connessione dell'agente al servizio

Nella tabella seguente sono elencati alcuni degli errori noti e i suggerimenti su come risolverli e risolverli.

|Message |Errore |Possibile causa |Soluzione |
|--------|------|---------------|---------|
|Non è stato possibile acquisire il flusso del dispositivo token di autorizzazione |`Error occurred while sending request for Device Authorization Code: Post https://login.windows.net/fb84ce97-b875-4d12-b031-ef5e7edf9c8e/oauth2/devicecode?api-version=1.0:  dial tcp 40.126.9.7:443: connect: network is unreachable.` |Impossibile raggiungere l' `login.windows.net` endpoint | Verificare la connettività all'endpoint. |
|Non è stato possibile acquisire il flusso del dispositivo token di autorizzazione |`Error occurred while sending request for Device Authorization Code: Post https://login.windows.net/fb84ce97-b875-4d12-b031-ef5e7edf9c8e/oauth2/devicecode?api-version=1.0:  dial tcp 40.126.9.7:443: connect: network is Forbidden`. |Il proxy o il firewall blocca l'accesso all' `login.windows.net` endpoint. | Verificare la connettività all'endpoint e non è bloccata da un firewall o un server proxy. |
|Non è stato possibile acquisire il token di autorizzazione dal nome SPN |`Failed to execute the refresh request. Error = 'Post https://login.windows.net/fb84ce97-b875-4d12-b031-ef5e7edf9c8e/oauth2/token?api-version=1.0: Forbidden'` |Il proxy o il firewall blocca l'accesso all' `login.windows.net` endpoint. |Verificare la connettività all'endpoint e non è bloccata da un firewall o un server proxy. |
|Non è stato possibile acquisire il token di autorizzazione dal nome SPN |`Invalid client secret is provided` |Segreto dell'entità servizio errato o non valido. |Verificare il segreto dell'entità servizio. |
| Non è stato possibile acquisire il token di autorizzazione dal nome SPN |`Application with identifier 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx' was not found in the directory 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'. This can happen if the application has not been installed by the administrator of the tenant or consented to by any user in the tenant` |Entità servizio e/o ID tenant non corretti. |Verificare l'entità servizio e/o l'ID tenant.|
|Ottieni risposta risorse ARM |`The client 'username@domain.com' with object id 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx' does not have authorization to perform action 'Microsoft.HybridCompute/machines/read' over scope '/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myResourceGroup/providers/Microsoft.HybridCompute/machines/MSJC01' or the scope is invalid. If access was recently granted, please refresh your credentials."}}" Status Code=403` |Credenziali e/o autorizzazioni errate |Verificare che l'entità servizio sia un membro del ruolo di **onboarding del computer connesso di Azure** . |
|Non è stato possibile AzcmagentConnect la risorsa ARM |`The subscription is not registered to use namespace 'Microsoft.HybridCompute'` |I provider di risorse di Azure non sono registrati. |Registrare i [provider di risorse](./agent-overview.md#register-azure-resource-providers). |
|Non è stato possibile AzcmagentConnect la risorsa ARM |`Get https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myResourceGroup/providers/Microsoft.HybridCompute/machines/MSJC01?api-version=2019-03-18-preview:  Forbidden` |Il server proxy o il firewall blocca l'accesso all' `management.azure.com` endpoint. |Verificare la connettività all'endpoint e non è bloccata da un firewall o un server proxy. |

## <a name="next-steps"></a>Passaggi successivi

Se il problema riscontrato non è presente in questo elenco o se non si riesce a risolverlo, visitare uno dei canali seguenti per ottenere ulteriore assistenza:

* Ottieni risposte dagli esperti di Azure tramite [Microsoft Q&A](https://docs.microsoft.com/answers/topics/azure-arc.html).

* Connettersi con [@AzureSupport](https://twitter.com/azuresupport), l'account ufficiale Microsoft Azure per migliorare l'esperienza del cliente. Il supporto di Azure consente di entrare in contatto con la community di Azure e quindi di ottenere risposte, assistenza e consulenza.

* Archiviare un incidente del supporto tecnico di Azure. Accedere al [sito del supporto tecnico di Azure](https://azure.microsoft.com/support/options/) e selezionare **Supporto tecnico**.