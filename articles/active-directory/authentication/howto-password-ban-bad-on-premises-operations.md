---
title: Operazioni e reporting di Protezione password di Azure AD in anteprima
description: Operazioni e reporting post-distribuzione di Protezione password di Azure AD in anteprima
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: article
ms.date: 11/02/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jsimmons
ms.openlocfilehash: 8d7002a014fc6cfab1888a6bc97c0f864de1d99d
ms.sourcegitcommit: 58dc0d48ab4403eb64201ff231af3ddfa8412331
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/26/2019
ms.locfileid: "55080872"
---
# <a name="preview-azure-ad-password-protection-operational-procedures"></a>Anteprima: procedure operative di Password di protezione di Azure AD

|     |
| --- |
| La protezione password di Azure AD è una funzionalità in anteprima pubblica di Azure Active Directory. Per altre informazioni sulle funzioni in anteprima, vedere [Condizioni per l'utilizzo supplementari per le anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).|
|     |

Dopo aver completato l'[installazione di Protezione password di Azure AD](howto-password-ban-bad-on-premises-deploy.md) in locale, è necessario configurare alcuni componenti nel portale di Azure.

## <a name="configure-the-custom-banned-password-list"></a>Configurare l'elenco personalizzato password escluse

Seguire le indicazioni fornite nell'articolo [Configurazione dell'elenco personalizzato password escluse](howto-password-ban-bad-configure.md) per la procedura di personalizzazione dell'elenco di password escluse per la propria organizzazione.

## <a name="enable-password-protection"></a>Abilitare la protezione con password

1. Accedere al [portale di Azure](https://portal.azure.com) e passare a **Azure Active Directory**, **Metodi di autenticazione** e quindi **Password protection (Preview)** (Protezione password - Anteprima).
1. Impostare **Abilita la password di protezione in Windows Server Active Directory** su **Sì**
1. Come indicato nella [Guida alla distribuzione](howto-password-ban-bad-on-premises-deploy.md#deployment-strategy), è consigliabile impostare inizialmente **Modalità** su **Controllo**
   * Dopo avere familiarizzato con la funzione, è possibile impostare **Modalità** su **Applicato**
1. Fare clic su **Save**

![Abilitazione dei componenti di Protezione password di Azure AD nel portale di Azure](./media/howto-password-ban-bad-on-premises-operations/authentication-methods-password-protection-on-prem.png)

## <a name="audit-mode"></a>Modalità di controllo

La modalità di controllo è concepita come un modo per eseguire il software in modalità "what-if". Ogni servizio agente del controller di dominio valuta una password in ingresso in base ai criteri attualmente attivi. Se i criteri correnti sono configurati per essere in modalità di controllo, le password "non corrette" generano messaggi del log eventi, ma vengono accettate. Questa è l'unica differenza tra le modalità di controllo e la modalità di imposizione; tutte le altre operazioni vengono eseguite nello stesso modo.

> [!NOTE]
> Microsoft consiglia di avviare la distribuzione iniziale e i test sempre in modalità di controllo. È quindi opportuno controllare gli eventi nel log eventi per cercare di prevedere se eventuali processi operativi esistenti potrebbero essere ostacolati dopo l'attivazione della modalità di imposizione.

## <a name="enforce-mode"></a>Modalità di imposizione

La modalità di imposizione è concepita come configurazione finale. Come nella modalità di controllo riportata sopra, ogni servizio agente del controller di dominio valuta le password in ingresso in base ai criteri attualmente attivi. Se è abilitata la modalità di imposizione, tuttavia, la password considerata non sicura in base ai criteri viene rifiutata.

Quando una password viene rifiutata in modalità di imposizione dall'agente del controller di dominio di Protezione password di Azure AD, l'impatto visibile ricevuto dall'utente finale è identico a quello che riceverebbe se la password fosse stata rifiutata dall'applicazione tradizionale che gestisce la complessità delle password in locale. Ad esempio, un utente potrebbe ricevere il seguente messaggio di errore tradizionale nella schermata di accesso/modifica password di Windows:

`Unable to update the password. The value provided for the new password does not meet the length, complexity, or history requirements of the domain.`

Questo messaggio è solo un esempio dei possibili risultati. Il messaggio di errore specifico può variare a seconda del software o dello scenario effettivo che sta tentando di impostare una password non sicura.

Gli utenti finali interessati potrebbero avere bisogno di collaborare con il proprio personale IT per comprendere i nuovi requisiti e scegliere password sicure.

## <a name="enable-mode"></a>Modalità di abilitazione

Questa impostazione deve essere lasciata in genere nello stato predefinito abilitato (Sì). Se si configura questa impostazione sullo stato disabilitato (No) tutti gli agenti del controller di dominio di protezione password di Azure AD passeranno a una modalità inattiva in cui tutte le password vengono accettate così come sono, senza alcuna attività di convalida (ad esempio, non verranno generati nemmeno gli eventi di controllo).

## <a name="usage-reporting"></a>Report sull'utilizzo

È possibile usare il cmdlet `Get-AzureADPasswordProtectionSummaryReport` per produrre una visualizzazione di riepilogo dell'attività. Di seguito viene mostrato un esempio dell'output di questo cmdlet:

```PowerShell
Get-AzureADPasswordProtectionSummaryReport -DomainController bplrootdc2
DomainController                : bplrootdc2
PasswordChangesValidated        : 6677
PasswordSetsValidated           : 9
PasswordChangesRejected         : 10868
PasswordSetsRejected            : 34
PasswordChangeAuditOnlyFailures : 213
PasswordSetAuditOnlyFailures    : 3
PasswordChangeErrors            : 0
PasswordSetErrors               : 1
```

È possibile influire sull'ambito del reporting del cmdlet usando uno dei parametri –Forest, -Domain o –DomainController. Se non si specifica un parametro, viene usato il parametro –Forest.

> [!NOTE]
> Questo cmdlet funziona aprendo una sessione di PowerShell per ogni controller di dominio. Per il corretto funzionamento, il supporto di sessioni remote di PowerShell deve essere abilitato in ogni controller di dominio e il client deve avere privilegi sufficienti. Per altre informazioni sui requisiti per le sessioni remote di PowerShell, eseguire 'Get-Help about_Remote_Troubleshooting' in una finestra di PowerShell.

> [!NOTE]
> Questo cmdlet funziona eseguendo query in modalità remota sul log eventi di amministrazione di ogni servizio agente del controller di dominio. Se i log eventi contengono un numero elevato di eventi, il completamento del cmdlet potrebbe richiedere molto tempo. Inoltre, le query di rete in blocco di grandi set di dati possono influire sulle prestazioni dei controller di dominio. Di conseguenza, questo cmdlet deve essere usato con cautela negli ambienti di produzione.

## <a name="dc-agent-discovery"></a>Individuazione dell'agente del controller di dominio

È possibile usare il cmdlet `Get-AzureADPasswordProtectionDCAgent` per visualizzare informazioni di base sui diversi agenti del controller di dominio in esecuzione in un dominio o una foresta. Queste informazioni vengono recuperate dagli oggetti serviceConnectionPoint registrati da uno o più servizi agente del controller di dominio in esecuzione. Di seguito viene mostrato un esempio dell'output di questo cmdlet:

```PowerShell
Get-AzureADPasswordProtectionDCAgent
ServerFQDN            : bplChildDC2.bplchild.bplRootDomain.com
Domain                : bplchild.bplRootDomain.com
Forest                : bplRootDomain.com
Heartbeat             : 2/16/2018 8:35:01 AM
```

Le diverse proprietà vengono aggiornate da ogni servizio agente del controller di dominio all'incirca ogni ora. I dati sono comunque soggetti alla latenza di replica di Active Directory.

L'ambito della query del cmdlet può essere ulteriormente definito usando il parametro –Forest o –Domain.

## <a name="next-steps"></a>Passaggi successivi

[Monitoraggio, creazione di report e risoluzione dei problemi nella protezione password di Azure AD](howto-password-ban-bad-on-premises-troubleshoot.md)
