---
title: Cronologia delle versioni dell'agente protezione password-Azure Active Directory
description: Documentazione della cronologia delle versioni e delle modifiche di comportamento
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: article
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6458f31b21d09e54afb080ffc73598903a9831e0
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74381725"
---
# <a name="azure-ad-password-protection-agent-version-history"></a>Cronologia delle versioni dell'agente di Protezione password di Azure AD

## <a name="121250"></a>1.2.125.0

Data di rilascio: 3/22/2019

* Correzione di errori di digitazione secondari nei messaggi del registro eventi
* Aggiornare il contratto EULA alla versione finale di disponibilità generale

> [!NOTE]
> Build 1.2.125.0 è la build di disponibilità generale. Grazie ancora a tutti gli utenti hanno inviato commenti e suggerimenti sul prodotto.

## <a name="121160"></a>1.2.116.0

Data di rilascio: 3/13/2019

* I cmdlet Get-AzureADPasswordProtectionProxy e Get-AzureADPasswordProtectionDCAgent ora segnalano la versione del software e il tenant di Azure corrente con le limitazioni seguenti:
  * La versione del software e i dati del tenant di Azure sono disponibili solo per gli agenti DC e i proxy che eseguono la versione 1.2.116.0 o successiva.
  * I dati del tenant di Azure potrebbero non essere segnalati fino a quando non si è verificata una nuova registrazione (o rinnovo) del proxy o della foresta.
* Il servizio proxy richiede ora l'installazione di .NET 4,7.
  * .NET 4,7 dovrebbe essere già installato in un server Windows completamente aggiornato. In caso contrario, scaricare ed eseguire il programma di installazione disponibile nel [programma di installazione di .NET Framework 4,7 offline per Windows](https://support.microsoft.com/help/3186497/the-net-framework-4-7-offline-installer-for-windows).
  * Nei sistemi Server Core può essere necessario passare il flag/q al programma di installazione di .NET 4,7 per ottenere un esito positivo.
* Il servizio proxy supporta ora l'aggiornamento automatico. L'aggiornamento automatico usa il servizio Microsoft Azure AD Connect Agent Updater, installato side-by-side con il servizio proxy. L'aggiornamento automatico è on per impostazione predefinita.
* L'aggiornamento automatico può essere abilitato o disabilitato usando il cmdlet Set-AzureADPasswordProtectionProxyConfiguration. È possibile eseguire query sull'impostazione corrente usando il cmdlet Get-AzureADPasswordProtectionProxyConfiguration.
* Il file binario del servizio per il servizio agente di controller di dominio è stato rinominato in AzureADPasswordProtectionDCAgent. exe.
* Il file binario del servizio proxy è stato rinominato in AzureADPasswordProtectionProxy. exe. Se è in uso un firewall di terze parti, potrebbe essere necessario modificare le regole del firewall di conseguenza.
  * Nota: se è stato usato un file di configurazione proxy HTTP in un'installazione proxy precedente, sarà necessario rinominarlo (da *proxyservice. exe. config* a *AzureADPasswordProtectionProxy. exe. config*) dopo l'aggiornamento.
* Tutti i controlli di funzionalità con limiti di tempo sono stati rimossi dall'agente del controller di dominio.
* Correzioni di bug secondari e miglioramenti della registrazione.

## <a name="12650"></a>1.2.65.0

Data di rilascio: 2/1/2019

Modifiche:

* L'agente del controller di dominio e il servizio proxy sono ora supportati in Server Core. I requisiti del sistema operativo Mininimum sono rimasti invariati rispetto alle precedenti: Windows Server 2012 per gli agenti DC e Windows Server 2012 R2 per proxy.
* I cmdlet Register-AzureADPasswordProtectionProxy e Register-AzureADPasswordProtectionForest ora supportano le modalità di autenticazione di Azure basate sul codice del dispositivo.
* Il cmdlet Get-AzureADPasswordProtectionDCAgent ignorerà i punti di connessione del servizio modificati e/o non validi. Questa modifica corregge il bug a causa del quale talvolta i controller di dominio venivano visualizzati più volte nell'output.
* Il cmdlet Get-AzureADPasswordProtectionSummaryReport ignorerà i punti di connessione del servizio modificati e/o non validi. Questa modifica corregge il bug a causa del quale talvolta i controller di dominio venivano visualizzati più volte nell'output.
* Il modulo Proxy di PowerShell viene ora registrato da %ProgramFiles%\WindowsPowerShell\Modules. La variabile di ambiente PSModulePath del computer non viene più modificata.
* È stato aggiunto un nuovo cmdlet Get-AzureADPasswordProtectionProxy per facilitare l'individuazione dei proxy registrati in una foresta o un dominio.
* L'agente del controller di dominio usa una nuova cartella nella condivisione SYSVOL per replicare i criteri per le password e altri file.

   Percorso della cartella precedente:

   `\\<domain>\sysvol\<domain fqdn>\Policies\{4A9AB66B-4365-4C2A-996C-58ED9927332D}`

   Percorso della nuova cartella:

   `\\<domain>\sysvol\<domain fqdn>\AzureADPasswordProtection`

   Questa modifica è stata apportata per evitare falsi positivi negli avvisi di "oggetti Criteri di gruppo isolati".

   > [!NOTE]
   > Fra la cartella precedente e quella nuova non verrà eseguita alcuna migrazione o condivisione di dati. Le versioni precedenti dell'agente del controller di dominio continueranno a usare il vecchio percorso finché non verranno aggiornate a questa versione o a una successiva. Quando tutti gli agenti del controller di dominio eseguiranno la versione 1.2.65.0 o una versione successiva, sarà possibile eliminare manualmente la vecchia cartella SYSVOL.

* L'agente del controller di dominio e il servizio proxy sono ora in grado di rilevare ed eliminare le copie modificate dei rispettivi punti di connessione del servizio.
* Ogni agente del controller di dominio eliminerà periodicamente i punti di connessione del servizio modificati e non aggiornati nel proprio dominio, sia per l'agente stesso che per il proxy. I punti di connessione del servizio dell'agente e del proxy vengono entrambi considerati non aggiornati se il relativo timestamp di heartbeat è più vecchio di sette giorni.
* L'agente del controller di dominio rinnoverà ora il certificato della foresta quando necessario.
* Il servizio proxy rinnoverà ora il certificato del proxy quando necessario.
* Aggiornamenti all'algoritmo di convalida delle password: l'elenco globale delle password escluse e quello specifico del cliente (se configurato) vengono uniti prima di eseguire la convalida delle password. Ora una determinata password può essere rifiutata (non riuscita o riuscita solo a livello di controllo) se contiene token sia dell'elenco globale che di quello specifico del cliente. La documentazione del log eventi è stata aggiornata per includere questa modifica. Vedere [Monitoraggio e registrazione in Protezione password di Azure AD](howto-password-ban-bad-on-premises-monitor.md).
* Correzioni per prestazioni e affidabilità
* Registrazione migliorata

> [!WARNING]
> Funzionalità a durata limitata: il servizio agente del controller di dominio in questa versione (1.2.65.0) non elaborerà più le richieste di convalida password a partire dall'1 settembre 2019.  I servizi agente del controller di dominio di versioni precedenti (vedere l'elenco di seguito) interromperanno l'elaborazione a partire dall'1 luglio 2019. Il servizio agente del controller di dominio in tutte le versioni registrerà 10.021 eventi nel log eventi di amministrazione nei due mesi precedenti a queste date. Tutte le restrizioni temporali verranno rimosse nella prossima versione di disponibilità generale. Il servizio agente Proxy non presenta limitazioni temporali in alcuna versione, ma deve comunque essere aggiornato alla versione più recente per poter beneficiare di tutte le successive correzioni di bug e di altri miglioramenti.

## <a name="12250"></a>1.2.25.0

Data di rilascio: 01/11/2018

Correzioni:

* Il servizio agente del controller di dominio e il servizio proxy non devono più fallire a causa di errori di attendibilità del certificato.
* Il servizio agente del controller di dominio e il servizio proxy dispongono di correzioni aggiuntive per i computer conformi a FIPS.
* Il servizio proxy funzionerà ora correttamente solo in un ambiente di rete TLS 1.2.
* Correzioni minori per prestazioni e affidabilità
* Registrazione migliorata

Modifiche:

* Il requisito minimo relativo al sistema operativo per il servizio proxy è ora Windows Server 2012 R2. Il requisito minimo relativo al sistema operativo per il servizio agente del controller di dominio rimane Windows Server 2012.
* Il servizio Proxy richiede ora .NET versione 4.6.2.
* L'algoritmo di convalida delle password usa una tabella di normalizzazione dei caratteri espansa. Ciò potrebbe comportare il rifiuto delle password accettate nelle versioni precedenti.

## <a name="12100"></a>1.2.10.0

Data di rilascio: 17/08/2018

Correzioni:

* Register-AzureADPasswordProtectionProxy e Register-AzureADPasswordProtectionForest ora supportano l'autenticazione a più fattori
* Register-AzureADPasswordProtectionProxy richiede un controller di dominio WS2012 o versioni successive nel dominio per evitare errori di crittografia.
* Il servizio agente del controller di dominio è più affidabile per le richieste dei nuovi criteri password da Azure all'avvio.
* Il servizio agente del controller di dominio richiederà i nuovi criteri password da Azure ogni ora, se necessario, ma adesso in base a un'ora di inizio selezionata in modo casuale.
* Il servizio agente del controller di dominio non causerà più un ritardo indefinito nell'annuncio di un nuovo controller di dominio in caso di installazione in un server prima della relativa promozione come replica.
* Il servizio agente del controller di dominio rispetterà ora l'impostazione di configurazione "Abilita la password di protezione in Windows Server Active Directory"
* I programmi di installazione dell'agente del controller di dominio e del proxy supporteranno ora entrambi l'aggiornamento sul posto per l'aggiornamento a versioni future.

> [!WARNING]
> L'aggiornamento sul porto dalla versione 1.1.10.3 non è supportato e genererà un errore di installazione. Per eseguire l'aggiornamento alla versione 1.2.10 o versioni successive, è prima di tutto necessario disinstallare completamente il software del servizio agente del controller di dominio e del servizio proxy, quindi installare la nuova versione da zero. È richiesta una nuova registrazione del servizio proxy di protezione password di Azure AD.  Non è necessario ripetere la registrazione della foresta.

> [!NOTE]
> Per gli aggiornamenti sul posto del software dell'agente del controller di dominio sarà richiesto un riavvio.

* Il servizio agente del controller di dominio e il servizio proxy supportano ora l'esecuzione in un server configurato per usare solo algoritmi conformi a FIPS.
* Correzioni minori per prestazioni e affidabilità
* Registrazione migliorata

## <a name="11103"></a>1.1.10.3

Data di rilascio: 15/06/2018

Versione di anteprima pubblica iniziale

## <a name="next-steps"></a>Passaggi successivi

[Distribuire la funzionalità Protezione password di Azure AD](howto-password-ban-bad-on-premises-deploy.md)
