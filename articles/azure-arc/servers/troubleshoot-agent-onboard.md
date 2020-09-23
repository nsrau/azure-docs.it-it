---
title: Risolvere i problemi di connessione dell'agente di Azure Arc Enabled Servers
description: Questo articolo illustra come risolvere i problemi relativi all'agente del computer connesso che si verificano con i server abilitati per Azure Arc quando si tenta di connettersi al servizio.
ms.date: 09/02/2020
ms.topic: conceptual
ms.openlocfilehash: 36feb6a65ec52d99dfd664ae54cb099ea6a7e239
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90900683"
---
# <a name="troubleshoot-the-connected-machine-agent-connection-issues"></a>Risolvere i problemi di connessione dell'agente computer connesso

Questo articolo fornisce informazioni sulla risoluzione dei problemi e sulla risoluzione dei problemi che possono verificarsi durante il tentativo di configurare l'agente computer connesso di Azure Arc Enabled Server per Windows o Linux. Sono inclusi entrambi i metodi di installazione interattiva e su larga scala durante la configurazione della connessione al servizio. Per informazioni generali, vedere [Cenni preliminari sui server abilitati per Arc](./overview.md).

## <a name="agent-verbose-log"></a>Log dettagliato dell'agente

Prima di seguire i passaggi di risoluzione dei problemi descritti più avanti in questo articolo, le informazioni minime necessarie sono il log dettagliato. Contiene l'output dei comandi dello strumento **azcmagent** , quando si usa l'argomento Verbose (-v). I file di log vengono scritti in `%ProgramData%\AzureConnectedMachineAgent\Log\azcmagent.log` per Windows e Linux in `/var/opt/azcmagent/log/azcmagent.log` .

### <a name="windows"></a>Windows

Di seguito è riportato un esempio del comando per abilitare la registrazione dettagliata con l'agente computer connesso per Windows durante l'esecuzione di un'installazione interattiva.

```console
& "$env:ProgramFiles\AzureConnectedMachineAgent\azcmagent.exe" connect --resource-group "resourceGroupName" --tenant-id "tenantID" --location "regionName" --subscription-id "subscriptionID" --verbose
```

Di seguito è riportato un esempio del comando per abilitare la registrazione dettagliata con l'agente computer connesso per Windows durante l'esecuzione di un'installazione su larga scala tramite un'entità servizio.

```console
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

>[!NOTE]
>Per eseguire **azcmagent**, è necessario disporre delle autorizzazioni di accesso alla *radice* nei computer Linux.

```bash
azcmagent connect --resource-group "resourceGroupName" --tenant-id "tenantID" --location "regionName" --subscription-id "subscriptionID" --verbose
```

Di seguito è riportato un esempio del comando per abilitare la registrazione dettagliata con l'agente computer connesso per Linux quando si esegue un'installazione su larga scala usando un'entità servizio.

```bash
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

|Messaggio |Errore |Possibile causa |Soluzione |
|--------|------|---------------|---------|
|Non è stato possibile acquisire il flusso del dispositivo token di autorizzazione |`Error occurred while sending request for Device Authorization Code: Post https://login.windows.net/fb84ce97-b875-4d12-b031-ef5e7edf9c8e/oauth2/devicecode?api-version=1.0:  dial tcp 40.126.9.7:443: connect: network is unreachable.` |Impossibile raggiungere l' `login.windows.net` endpoint | Verificare la connettività all'endpoint. |
|Non è stato possibile acquisire il flusso del dispositivo token di autorizzazione |`Error occurred while sending request for Device Authorization Code: Post https://login.windows.net/fb84ce97-b875-4d12-b031-ef5e7edf9c8e/oauth2/devicecode?api-version=1.0:  dial tcp 40.126.9.7:443: connect: network is Forbidden`. |Il proxy o il firewall blocca l'accesso all' `login.windows.net` endpoint. | Verificare la connettività all'endpoint e non è bloccata da un firewall o un server proxy. |
|Non è stato possibile acquisire il flusso del dispositivo token di autorizzazione  |`Error occurred while sending request for Device Authorization Code: Post https://login.windows.net/fb84ce97-b875-4d12-b031-ef5e7edf9c8e/oauth2/devicecode?api-version=1.0:  dial tcp lookup login.windows.net: no such host`. | Criteri di gruppo *Configurazione computer oggetto \ modelli amministrativi \ System \ utente profili \ Elimina profili utente più vecchi di un numero specificato di giorni al riavvio del sistema* è abilitato. | Verificare che l'oggetto Criteri di gruppo sia abilitato e che abbia come destinazione il computer interessato. Per informazioni dettagliate, vedere la Nota <sup>[1](#footnote1)</sup> . |
|Non è stato possibile acquisire il token di autorizzazione dal nome SPN |`Failed to execute the refresh request. Error = 'Post https://login.windows.net/fb84ce97-b875-4d12-b031-ef5e7edf9c8e/oauth2/token?api-version=1.0: Forbidden'` |Il proxy o il firewall blocca l'accesso all' `login.windows.net` endpoint. |Verificare la connettività all'endpoint e non è bloccata da un firewall o un server proxy. |
|Non è stato possibile acquisire il token di autorizzazione dal nome SPN |`Invalid client secret is provided` |Segreto dell'entità servizio errato o non valido. |Verificare il segreto dell'entità servizio. |
| Non è stato possibile acquisire il token di autorizzazione dal nome SPN |`Application with identifier 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx' was not found in the directory 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'. This can happen if the application has not been installed by the administrator of the tenant or consented to by any user in the tenant` |Entità servizio e/o ID tenant non corretti. |Verificare l'entità servizio e/o l'ID tenant.|
|Ottieni risposta risorse ARM |`The client 'username@domain.com' with object id 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx' does not have authorization to perform action 'Microsoft.HybridCompute/machines/read' over scope '/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myResourceGroup/providers/Microsoft.HybridCompute/machines/MSJC01' or the scope is invalid. If access was recently granted, please refresh your credentials."}}" Status Code=403` |Credenziali e/o autorizzazioni errate |Verificare che l'entità servizio sia un membro del ruolo di **onboarding del computer connesso di Azure** . |
|Non è stato possibile AzcmagentConnect la risorsa ARM |`The subscription is not registered to use namespace 'Microsoft.HybridCompute'` |I provider di risorse di Azure non sono registrati. |Registrare i [provider di risorse](./agent-overview.md#register-azure-resource-providers). |
|Non è stato possibile AzcmagentConnect la risorsa ARM |`Get https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myResourceGroup/providers/Microsoft.HybridCompute/machines/MSJC01?api-version=2019-03-18-preview:  Forbidden` |Il server proxy o il firewall blocca l'accesso all' `management.azure.com` endpoint. |Verificare la connettività all'endpoint e non è bloccata da un firewall o un server proxy. |

<a name="footnote1"></a><sup>1</sup> Se questo GPO è abilitato e si applica ai computer con l'agente del computer connesso, viene eliminato il profilo utente associato all'account predefinito specificato per il servizio *himds* . Viene quindi eliminato anche il certificato di autenticazione utilizzato per comunicare con il servizio memorizzato nella cache nell'archivio certificati locale per 30 giorni. Prima del limite di 30 giorni, viene effettuato un tentativo di rinnovare il certificato. Per risolvere questo problema, seguire la procedura per annullare la [registrazione del computer](manage-agent.md#unregister-machine) e quindi registrarlo di nuovo con il servizio in esecuzione `azcmagent connect` .

## <a name="next-steps"></a>Passaggi successivi

Se il problema riscontrato non è presente in questo elenco o se non si riesce a risolverlo, visitare uno dei canali seguenti per ottenere ulteriore assistenza:

* Ottieni risposte dagli esperti di Azure tramite [Microsoft Q&A](/answers/topics/azure-arc.html).

* Connettersi con [@AzureSupport](https://twitter.com/azuresupport), l'account ufficiale Microsoft Azure per migliorare l'esperienza del cliente. Il supporto di Azure consente di entrare in contatto con la community di Azure e quindi di ottenere risposte, assistenza e consulenza.

* Archiviare un incidente del supporto tecnico di Azure. Accedere al [sito del supporto tecnico di Azure](https://azure.microsoft.com/support/options/) e selezionare **Supporto tecnico**.
