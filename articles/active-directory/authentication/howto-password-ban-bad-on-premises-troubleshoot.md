---
title: Risoluzione dei problemi nell'anteprima di Password di protezione di Azure AD
description: Informazioni sulla risoluzione dei problemi comuni nell'anteprima di Password di protezione di Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jsimmons
ms.openlocfilehash: 7474027368949d5ad2202881ac68096fac2b8bd2
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/04/2019
ms.locfileid: "55693905"
---
# <a name="preview-azure-ad-password-protection-troubleshooting"></a>Anteprima: Risoluzione dei problemi relativi a Password di protezione di Azure AD

|     |
| --- |
| Password di protezione è una funzionalità di Azure Active Directory disponibile in anteprima pubblica. Per altre informazioni sulle funzioni in anteprima, vedere [Condizioni per l'utilizzo supplementari per le anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).|
|     |

Dopo la distribuzione di Password di protezione di Azure AD, può essere necessario risolvere alcuni problemi. Questo articolo presenta informazioni dettagliate su alcuni passaggi comuni per la risoluzione dei problemi.

## <a name="weak-passwords-are-not-getting-rejected-as-expected"></a>Le password vulnerabili non vengono rifiutate come previsto

Le cause possono essere diverse:

1. Uno o più agenti del controller di dominio non hanno ancora scaricato i criteri. Il sintomo è dato dalla presenza di 30001 eventi nel registro eventi di amministrazione degli agenti del controller di dominio.

    Le possibili cause di questo problema includono:

    1. La foresta non è ancora registrata
    2. Il proxy non è ancora registrato
    3. I problemi di connettività di rete impediscono al servizio Proxy di comunicare con Azure (verificare i requisiti del proxy HTTP)

2. La modalità di imposizione dei criteri password è ancora impostata su Controllo. In tal caso, riconfigurarla in modo da imporre l'uso del portale di Password di protezione di Azure AD. Vedere [Abilitare la password di protezione](howto-password-ban-bad-on-premises-operations.md#enable-password-protection).

3. I criteri password sono stati disabilitati. In tal caso, riconfigurarli in modo da abilitare l'uso del portale di Password di protezione di Azure AD. Vedere [Abilitare la password di protezione](howto-password-ban-bad-on-premises-operations.md#enable-password-protection).

4. L'algoritmo di convalida delle password potrebbe non funzionare come previsto. Vedere [Come vengono valutate le password](concept-password-ban-bad.md#how-are-passwords-evaluated).

## <a name="directory-services-repair-mode"></a>Modalità di ripristino dei servizi directory

Se il controller di dominio viene avviato in modalità di ripristino dei servizi directory, il servizio agente del controller di dominio rileva questo comportamento e disabilita automaticamente tutte le attività di applicazione e convalida delle password, indipendentemente dalla configurazione dei criteri attiva.

## <a name="emergency-remediation"></a>Correzione di emergenza

Se si verifica una situazione in cui il servizio agente del controller di dominio provoca problemi, tale servizio può essere arrestato immediatamente. La DLL di filtro dell'agente del controller di dominio tenta ancora di chiamare il servizio non in esecuzione e registra eventi di avviso (10012, 10013), ma durante questo periodo di tempo tutte le password in ingresso vengono accettate. Il servizio agente del controller di dominio può quindi essere configurato anche tramite Gestione controllo servizi di Windows con tipo di avvio "Disabilitato" in base alle esigenze.

Un'altra misura correttiva può essere quella di impostare la modalità di abilitazione su No nel portale di Password di protezione di Azure AD. Dopo aver scaricato i criteri aggiornati, ogni servizio agente del controller di dominio passa a una modalità inattiva in cui tutte le password vengono accettate così come sono. Per altre informazioni, vedere [Modalità di imposizione](howto-password-ban-bad-on-premises-operations.md#enforce-mode).

## <a name="domain-controller-demotion"></a>Abbassamento di livello del controller di dominio

È possibile abbassare di livello un controller di dominio che è ancora in esecuzione nel software dell'agente del controller di dominio. Gli amministratori devono tenere tuttavia presente il fatto che durante la procedura di abbassamento di livello il software dell'agente del controller di dominio continua a imporre i criteri password correnti. La nuova password dell'account amministratore locale (specificata come parte dell'operazione di abbassamento di livello) viene convalidata come qualsiasi altra password. Microsoft consiglia di scegliere password sicure per gli account amministratore locali nell'ambito di una procedura di abbassamento di livello del controller di dominio. Tuttavia, la convalida della nuova password dell'account amministratore locale da parte del software dell'agente del controller di dominio può avere un forte impatto sulle procedure operative di abbassamento di livello preesistenti.

Al termine dell'abbassamento di livello e quando il controller di dominio è stato riavviato ed è di nuovo in esecuzione come normale server membro, il software dell'agente del controller di dominio torna a operare in modalità passiva. Può quindi essere disinstallato in qualsiasi momento.

## <a name="removal"></a>Rimozione

Se si decide di disinstallare il software in versione di anteprima pubblica e di pulire tutti gli stati correlati dai domini e dalla foresta, è possibile completare i passaggi seguenti a questo scopo:

> [!IMPORTANT]
> È importante eseguire i passaggi nell'ordine specificato. Se un'istanza del servizio proxy viene lasciata in esecuzione, ne viene periodicamente ricreare il relativo oggetto serviceConnectionPoint. Se un'istanza del servizio agente del controller di dominio viene lasciata in esecuzione, ne vengono ricreati periodicamente l'oggetto serviceConnectionPoint e lo stato sysvol.

1. Disinstallare il software del proxy da tutti i computer. Questo passaggio **non** richiede un riavvio.
2. Disinstallare il software dell'agente del controller di dominio da tutti i controller di dominio. Questo passaggio **richiede** un riavvio.
3. Rimuovere manualmente tutti i punti di connessione del servizio proxy in ogni contesto dei nomi di dominio. Il percorso di questi oggetti può essere individuato con il comando di PowerShell per Active Directory seguente:

   ```PowerShell
   $scp = "serviceConnectionPoint"
   $keywords = "{ebefb703-6113-413d-9167-9f8dd4d24468}*"
   Get-ADObject -SearchScope Subtree -Filter { objectClass -eq $scp -and keywords -like $keywords }
   ```

   Non omettere l'asterisco ("*") alla fine del valore della variabile $keywords.

   L'oggetto risultante individuabile tramite il comando `Get-ADObject` può quindi essere inoltrato tramite pipe a `Remove-ADObject` oppure può essere eliminato manualmente. 

4. Rimuovere manualmente tutti i punti di connessione dell'agente del controller di dominio in ogni contesto dei nomi di dominio. Potrebbe essere presente uno di questi oggetti per ogni controller di dominio nella foresta, a seconda dell'ampiezza della distribuzione del software in versione di anteprima pubblica. Il percorso dell'oggetto può essere individuato con il comando di PowerShell per Active Directory seguente:

   ```PowerShell
   $scp = "serviceConnectionPoint"
   $keywords = "{2bac71e6-a293-4d5b-ba3b-50b995237946}*"
   Get-ADObject -SearchScope Subtree -Filter { objectClass -eq $scp -and keywords -like $keywords }
   ```

   L'oggetto risultante individuabile tramite il comando `Get-ADObject` può quindi essere inoltrato tramite pipe a `Remove-ADObject` oppure può essere eliminato manualmente.

   Non omettere l'asterisco ("*") alla fine del valore della variabile $keywords.

5. Rimuovere manualmente lo stato di configurazione a livello di foresta. Lo stato di configurazione a livello di foresta viene mantenuto in un contenitore nel contesto dei nomi della configurazione di Active Directory. Può essere individuato ed eliminato in questo modo:

   ```PowerShell
   $passwordProtectionConfigContainer = "CN=Azure AD Password Protection,CN=Services," + (Get-ADRootDSE).configurationNamingContext
   Remove-ADObject -Recursive $passwordProtectionConfigContainer
   ```

6. Rimuovere manualmente tutto lo stato correlato a sysvol eliminando manualmente la cartella seguente e tutto il suo contenuto:

   `\\<domain>\sysvol\<domain fqdn>\AzureADPasswordProtection`

   Se necessario, è possibile accedere a questo percorso anche in locale in un controller di dominio specificato. il percorso predefinito può essere simile al seguente:

   `%windir%\sysvol\domain\Policies\AzureADPasswordProtection`

   Il percorso è diverso se la condivisione sysvol è stata configurata in un percorso non predefinito.

## <a name="next-steps"></a>Passaggi successivi

[Domande frequenti per Password di protezione di Azure AD](howto-password-ban-bad-on-premises-faq.md)

Per altre informazioni sugli elenchi personalizzati di password escluse, vedere l'articolo [Eliminare le password non appropriate nell'organizzazione](concept-password-ban-bad.md)
