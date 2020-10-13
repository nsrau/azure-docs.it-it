---
title: Problemi di diagnostica di Desktop virtuale Windows - Azure
description: Come utilizzare la funzionalità di diagnostica di Desktop virtuale Windows per diagnosticare i problemi.
author: Heidilohr
ms.topic: troubleshooting
ms.date: 09/21/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 70676bd1a07acdfcbba071a906b390ed66d70074
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91279859"
---
# <a name="identify-and-diagnose-windows-virtual-desktop-issues"></a>Identificare e diagnosticare i problemi relativi al desktop virtuale Windows

>[!IMPORTANT]
>Questo contenuto si applica a Desktop virtuale Windows con gli oggetti Azure Resource Manager di Desktop virtuale Windows. Se si usa Desktop virtuale Windows (versione classica) senza gli oggetti Azure Resource Manager, vedere [questo articolo](./virtual-desktop-fall-2019/diagnostics-role-service-2019.md).

Desktop virtuale Windows offre una funzionalità di diagnostica che consente all'amministratore di identificare i problemi usando un'unica interfaccia. Per ulteriori informazioni sulle funzionalità di diagnostica di desktop virtuale di Windows, vedere [utilizzare log Analytics per la funzionalità di diagnostica](diagnostics-log-analytics.md).

Le connessioni che non raggiungono Desktop virtuale Windows non vengono visualizzate nei risultati della diagnostica perché il servizio dei ruoli di diagnostica fa parte del servizio Desktop virtuale Windows. Quando l'utente finale riscontra problemi di connettività di rete, possono verificarsi problemi di connessione di Desktop virtuale Windows.

## <a name="common-error-scenarios"></a>Scenari di errore comuni

La tabella WVDErrors tiene traccia degli errori in tutti i tipi di attività. La colonna denominata "ServiceError" fornisce un flag aggiuntivo contrassegnato da "true" o "false". Questo flag indica se l'errore è correlato al servizio.

* Se il valore è "true", il team del servizio potrebbe avere già esaminato questo problema. Se questo influisca sull'esperienza utente e viene visualizzato un numero elevato di volte, è consigliabile inviare un ticket di supporto per desktop virtuale di Windows.
* Se il valore è "false", potrebbe trattarsi di una configurazione errata che può essere risolta manualmente. Il messaggio di errore può fornire un indizio sul punto in cui iniziare.

La tabella seguente elenca gli errori comuni che possono essere riscontrati dagli amministratori.

>[!NOTE]
>L'elenco include gli errori più comuni e viene aggiornato con frequenza regolare. Per assicurarsi di avere le informazioni più aggiornate, consultare questo articolo almeno una volta al mese.

## <a name="management-errors"></a>Errori di gestione

|Messaggio di errore|Soluzione suggerita|
|---|---|
|Non è stato possibile creare la chiave di registrazione |Non è stato possibile creare il token di registrazione. Provare a crearlo di nuovo con un'ora di scadenza più breve (tra 1 ora e 1 mese). |
|Non è stato possibile eliminare la chiave di registrazione|Non è stato possibile eliminare il token di registrazione. Provare a eliminarlo di nuovo. Se ancora non funziona, usare PowerShell per verificare se il token è ancora presente. Se è presente, eliminarlo con PowerShell.|
|Non è stato possibile modificare la modalità di svuotamento host sessione |Non è stato possibile modificare la modalità di svuotamento nella macchina virtuale. Verificare lo stato della macchina virtuale. Se la macchina virtuale non è disponibile, non è possibile modificare la modalità di svuotamento.|
|Non è stato possibile disconnettere le sessioni utente |Non è stato possibile disconnettere l'utente dalla macchina virtuale. Verificare lo stato della macchina virtuale. Se la macchina virtuale non è disponibile, la sessione utente non può essere disconnessa. Se la macchina virtuale è disponibile, controllare lo stato della sessione utente per verificare se è disconnessa. |
|Non è stato possibile disconnettere tutti gli utenti all'interno dell'host sessione |Non è stato possibile disconnettersi gli utenti dalla macchina virtuale. Verificare lo stato della macchina virtuale. Se non è disponibile, gli utenti non possono essere disconnessi. Controllare lo stato della sessione utente per verificare se è già stato disconnesso. È possibile forzare la disconnessione con PowerShell. |
|Non è stato possibile annullare l'assegnazione dell'utente dal gruppo di applicazioni|Non è stato possibile annullare la pubblicazione di un gruppo di app per un utente. Verificare se l'utente è disponibile in Azure AD. Verificare se l'utente fa parte di un gruppo di utenti in cui è pubblicato il gruppo di app. |
|Si è verificato un errore durante il recupero delle posizioni disponibili |Controllare la posizione della macchina virtuale usata nella creazione guidata pool host. Se l'immagine non è disponibile in tale percorso, aggiungere l'immagine in quel percorso o scegliere una posizione diversa per la macchina virtuale. |

### <a name="connection-error-codes"></a>Codici di errore di connessione

|Codice numerico|Codice di errore|Soluzione suggerita|
|---|---|---|
|-2147467259|ConnectionFailedAdTrustedRelationshipFailure|L'host di sessione non è stato aggiunto correttamente ad Active Directory.|
|-2146233088|ConnectionFailedUserHasValidSessionButRdshIsUnhealthy|Le connessioni non sono riuscite perché l'host di sessione non è disponibile. Controllare l'integrità dell'host di sessione.|
|-2146233088|ConnectionFailedClientDisconnect|Se questo errore viene visualizzato di frequente, verificare che il computer dell'utente sia connesso alla rete.|
|-2146233088|ConnectionFailedNoHealthyRdshAvailable|La sessione a cui l'utente dell'host ha tentato di connettersi non è integra. Eseguire il debug della macchina virtuale.|
|-2146233088|ConnectionFailedUserNotAuthorized|L'utente non è autorizzato ad accedere all'app pubblicata o al desktop. L'errore potrebbe essere visualizzato dopo che l'amministratore ha rimosso le risorse pubblicate. Chiedere all'utente di aggiornare il feed nell'applicazione Desktop remoto.|
|2|FileNotFound|L'applicazione a cui l'utente ha tentato di accedere non è installata correttamente o è impostata su un percorso errato.<br><br>Quando si pubblicano nuove app mentre l'utente ha una sessione attiva, l'utente non sarà in grado di accedere a questa app. La sessione deve essere arrestata e riavviata prima che l'utente possa accedere all'app. |
|3|InvalidCredentials|Il nome utente o la password che l'utente ha immesso non corrisponde ad alcun nome utente o password esistente. Verificare che le credenziali siano state digitate correttamente e riprovare.|
|8|ConnectionBroken|La connessione tra il client e il gateway o il server si è interrotta. Non è necessaria alcuna azione a meno che l'interruzione sia avvenuta in modo imprevisto.|
|14|UnexpectedNetworkDisconnect|La connessione alla rete si è interrotta. Chiedere all'utente di connettersi di nuovo.|
|24|ReverseConnectFailed|La macchina virtuale host non comunica direttamente con Gateway Desktop remoto. Verificare che l'indirizzo IP del gateway possa essere risolto.|

## <a name="error-cant-add-user-assignments-to-an-app-group"></a>Errore: non è possibile aggiungere assegnazioni utente a un gruppo di app

Dopo l'assegnazione di un utente a un gruppo di app, nella portale di Azure viene visualizzato un avviso con il messaggio "terminazione sessione" o "problemi di autenticazione-Microsoft_Azure_WVD estensione". La pagina di assegnazione non viene caricata e, successivamente, le pagine interrompono il caricamento nel portale di Azure (ad esempio, monitoraggio di Azure, Log Analytics, integrità del servizio e così via).

**Motivo:** Si è verificato un problema con i criteri di accesso condizionale. Il portale di Azure sta tentando di ottenere un token per Microsoft Graph, che dipende da SharePoint Online. Il cliente dispone di un criterio di accesso condizionale denominato "Microsoft Office 365 condizioni di archiviazione dati per l'utilizzo" che richiede agli utenti di accettare le condizioni per l'utilizzo per accedere all'archiviazione dei dati. Tuttavia, non hanno ancora eseguito l'accesso, quindi il portale di Azure non è in grado di ottenere il token.

**Correzione:** Prima di accedere al portale di Azure, l'amministratore deve prima accedere a SharePoint e accettare le condizioni per l'utilizzo. Successivamente, dovrebbero essere in grado di accedere al portale di Azure come di consueto.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sui ruoli all'interno di Desktop virtuale Windows, vedere [Ambiente di Desktop virtuale Windows](environment-setup.md).

Per visualizzare l'elenco dei cmdlet di PowerShell disponibili per Desktop virtuale Windows, vedere le [informazioni di riferimento su PowerShell](/powershell/windows-virtual-desktop/overview).
