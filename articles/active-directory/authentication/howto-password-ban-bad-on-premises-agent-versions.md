---
title: Password protection agent release history - Azure Active Directory
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

Release date: 3/22/2019

* Fix minor typo errors in event log messages
* Update EULA agreement to final General Availability version

> [!NOTE]
> Build 1.2.125.0 is the General Availability build. Thank you again to everyone has provided feedback on the product!

## <a name="121160"></a>1.2.116.0

Release date: 3/13/2019

* The Get-AzureADPasswordProtectionProxy and Get-AzureADPasswordProtectionDCAgent cmdlets now report software version and the current Azure tenant with the following limitations:
  * Software version and Azure tenant data are only available for DC agents and proxies running version 1.2.116.0 or later.
  * Azure tenant data may not be reported until a re-registration (or renewal) of the proxy or forest has occurred.
* The Proxy service now requires that .NET 4.7 is installed.
  * .NET 4.7 should already be installed on a fully updated Windows Server. If this is not the case, download and run the installer found at [The .NET Framework 4.7 offline installer for Windows](https://support.microsoft.com/help/3186497/the-net-framework-4-7-offline-installer-for-windows).
  * On Server Core systems it may be necessary to pass the /q flag to the .NET 4.7 installer to get it to succeed.
* The Proxy service now supports automatic upgrade. Automatic upgrade uses the Microsoft Azure AD Connect Agent Updater service which is installed side-by-side with the Proxy service. Automatic upgrade is on by default.
* Automatic upgrade can be enabled or disabled using the Set-AzureADPasswordProtectionProxyConfiguration cmdlet. The current setting can be queried using the Get-AzureADPasswordProtectionProxyConfiguration cmdlet.
* The service binary for the DC agent service has been renamed to AzureADPasswordProtectionDCAgent.exe.
* The service binary for the Proxy service has been renamed to AzureADPasswordProtectionProxy.exe. Firewall rules may need to be modified accordingly if a third-party firewall is in-use.
  * NOTE: if an http proxy config file was being used in a previous Proxy install, it will need to be renamed (from *proxyservice.exe.config* to *AzureADPasswordProtectionProxy.exe.config*) after this upgrade.
* All time-limited functionality checks have been removed from the DC agent.
* Minor bugs fixes and logging improvements.

## <a name="12650"></a>1.2.65.0

Release date: 2/1/2019

Modifiche:

* L'agente del controller di dominio e il servizio proxy sono ora supportati in Server Core. Mininimum OS requirements are unchanged from before: Windows Server 2012 for DC agents, and Windows Server 2012 R2 for proxies.
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
