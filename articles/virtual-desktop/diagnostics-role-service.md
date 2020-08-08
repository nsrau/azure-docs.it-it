---
title: Problemi di diagnostica di Desktop virtuale Windows - Azure
description: Come utilizzare la funzionalità di diagnostica di Desktop virtuale Windows per diagnosticare i problemi.
author: Heidilohr
ms.topic: troubleshooting
ms.date: 04/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: a985ce4f93b04e4065b5189b2a406b54729720c3
ms.sourcegitcommit: 98854e3bd1ab04ce42816cae1892ed0caeedf461
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/07/2020
ms.locfileid: "88005096"
---
# <a name="identify-and-diagnose-windows-virtual-desktop-issues"></a>Identificare e diagnosticare i problemi relativi al desktop virtuale Windows

>[!IMPORTANT]
>Questo contenuto si applica a Desktop virtuale Windows con gli oggetti Azure Resource Manager di Desktop virtuale Windows. Se si usa Desktop virtuale Windows (versione classica) senza gli oggetti Azure Resource Manager, vedere [questo articolo](./virtual-desktop-fall-2019/diagnostics-role-service-2019.md).

Desktop virtuale Windows offre una funzionalità di diagnostica che consente all'amministratore di identificare i problemi usando un'unica interfaccia. Per ulteriori informazioni sulle funzionalità di diagnostica di desktop virtuale di Windows, vedere [utilizzare log Analytics per la funzionalità di diagnostica](diagnostics-log-analytics.md).

Le connessioni che non raggiungono Desktop virtuale Windows non vengono visualizzate nei risultati della diagnostica perché il servizio dei ruoli di diagnostica fa parte del servizio Desktop virtuale Windows. Quando l'utente finale riscontra problemi di connettività di rete, possono verificarsi problemi di connessione di Desktop virtuale Windows.

## <a name="common-error-scenarios"></a>Scenari di errore comuni

Gli scenari di errore sono suddivisi in problemi interni al servizio e problemi esterni a Desktop virtuale Windows.

* Problema interno: specifica scenari che non possono essere mitigati dal cliente e che devono essere risolti come un problema di supporto. Quando si forniscono commenti e suggerimenti tramite la [community Windows Virtual Desktop](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop), includere l'ID di correlazione e l'intervallo di tempo approssimativo in cui si è verificato il problema.
* Problema esterno: si riferiscono a scenari che possono essere mitigati dal cliente. e che sono esterni a Desktop virtuale Windows.

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

### <a name="external-connection-error-codes"></a>Codici di errore relativi alla connessione esterna

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

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sui ruoli all'interno di Desktop virtuale Windows, vedere [Ambiente di Desktop virtuale Windows](environment-setup.md).

Per visualizzare l'elenco dei cmdlet di PowerShell disponibili per Desktop virtuale Windows, vedere le [informazioni di riferimento su PowerShell](/powershell/windows-virtual-desktop/overview).
