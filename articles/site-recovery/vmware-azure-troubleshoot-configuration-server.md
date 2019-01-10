---
title: Risolvere i problemi relativi al server di configurazione durante il ripristino di emergenza di macchine virtuali VMware e server fisici in Azure con Azure Site Recovery | Microsoft Docs
description: Questo articolo offre informazioni sulla risoluzione dei problemi di distribuzione del server di configurazione per il ripristino di emergenza di macchine virtuali VMware e server fisici in Azure con Azure Site Recovery.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 12/17/2018
ms.author: ramamill
ms.openlocfilehash: f5c8241907459a06f0a6206ae6865cdf3fe9ab89
ms.sourcegitcommit: da69285e86d23c471838b5242d4bdca512e73853
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/03/2019
ms.locfileid: "53998966"
---
# <a name="troubleshoot-configuration-server-issues"></a>Risolvere i problemi del server di configurazione

Questo articolo offre informazioni sulla risoluzione dei problemi di distribuzione e gestione del server di configurazione di [Azure Site Recovery](site-recovery-overview.md). Il server di configurazione opera come un server di gestione e viene usato per configurare il ripristino di emergenza di macchine virtuali VMware locali e server fisici in Azure con Site Recovery. Le sezioni seguenti spiegano gli errori più frequenti che si verificano quando si aggiunge un nuovo server di configurazione e si gestisce un server di configurazione.

## <a name="registration-failures"></a>Errori di registrazione

Il computer di origine esegue la registrazione con il server di configurazione durante l'installazione dell'agente di mobilità. È possibile eseguire il debug di eventuali errori durante questo passaggio seguendo le linee guida seguenti:

1. Consultare C:\ProgramData\ASR\home\svsystems\var\configurator_register_host_static_info.log. ProgramData può essere una cartella nascosta. Se non è possibile individuarla, provare a visualizzare nuovamente la cartella. Le cause degli errori posso essere molteplici.
2. Cercare la stringa "Nessun indirizzo IP valido trovato". Se si trova la stringa,
    - verificare se l'id host richiesto è lo stesso del computer di origine.
    - Il computer di origine deve avere almeno un indirizzo IP assegnato alla scheda di interfaccia fisica per eseguire correttamente la registrazione con CS.
    - Eseguire il comando nel computer di origine `> ipconfig /all` (per sistema operativo di Windows) e `# ifconfig -a` (per sistema operativo Linux) per ottenere tutti gli indirizzi IP del computer di origine.
    - Si noti che la registrazione dell'agente richiede un indirizzo IP valido v4 assegnato alla scheda di interfaccia fisica.
3. Se non si trova la stringa precedente, cercare la stringa "Motivo"=>"NULL". Se trovata,
    - l'errore si verifica quando il computer di origine usa un host vuoto e se si effettua la registrazione con il server di configurazione.
    - Dopo la risoluzione dei problemi, seguire le linee guida presenti [qui](vmware-azure-troubleshoot-configuration-server.md#register-source-machine-with-configuration-server) per riprovare manualmente a effettuare la registrazione.
4. Se non si trova la stringa precedente, passare alla macchina virtuale di origine e controllare il registro C:\ProgramData\ASRSetupLogs\UploadedLogs\* ASRUnifiedAgentInstaller.log ProgramData può essere una cartella nascosta. Se non è possibile individuarla, provare a visualizzare nuovamente la cartella. Le cause degli errori posso essere molteplici. Cercare la stringa "richiesta post: (7): Impossibile connettersi al server". Se trovata,
    - risolvere i problemi di rete tra il computer di origine e il server di configurazione. Assicurarsi che il server di configurazione sia raggiungibile dal computer di origine usando strumenti di rete come ping, traceroute, browser Web, ecc. Assicurarsi che il computer di origine possa raggiungere il server di configurazione attraverso la porta 443.
    - Verificare se sono presenti regole del firewall nel computer di origine che bloccano la connessione tra il server del computer e il server di configurazione. Rivolgersi all'amministratore di rete per sbloccare i problemi di connessione.
    - Verificare che le cartelle indicate [qui](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program) siano escluse dal software antivirus.
    - Dopo la risoluzione dei problemi di rete, riprovare a effettuare la registrazione seguendo le linee guida presenti [qui](vmware-azure-troubleshoot-configuration-server.md#register-source-machine-with-configuration-server).
5. Se non trovato, cercare nello stesso registro la stringa "richiesta: (60) - Peer certificate cannot be authenticated with given CA certificates" (Impossibile autenticare il certificato peer con i certificati CA assegnati). Se trovata, 
    - l'errore può essere causato da un certificato del server di configurazione scaduto o dal fatto che il computer di origine non supporti TLS 1.0 o le versioni successive dei protocolli SSL, oppure se un firewall blocca la comunicazione SSL tra il computer di origine e il server di configurazione.
    - Per risolvere il problema, connettersi all'indirizzo IP del server di configurazione usando un browser Web su un computer di origine con l'URI https://<CSIPADDRESS>:443/. Assicurarsi che tale macchina di origine sia in grado di raggiungere il server di configurazione tramite la porta 443.
    - Verificare se sono presenti regole del firewall nel computer di origine che bloccano la connessione tra il server del computer e il server di configurazione. Rivolgersi all'amministratore di rete per sbloccare i problemi di connessione.
    - Verificare che le cartelle indicate [qui](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program) siano escluse dal software antivirus.  
    - Dopo la risoluzione dei problemi, riprovare a effettuare registrazione seguendo le linee guida presenti [qui](vmware-azure-troubleshoot-configuration-server.md#register-source-machine-with-configuration-server).
6. In Linux, se il valore della piattaforma in <INSTALLATION_DIR>/etc/drscout.conf è danneggiato, la registrazione non avviene correttamente. Per identificare il problema, passare a /var/log/ua_install.log. Si troverà la stringa "Aborting configuration as VM_PLATFORM value is either null or it is not VmWare/Azure" (L'interruzione della configurazione come valore VM_PLATFORM è null o non è VmWare/Azure). La piattaforma deve essere impostata su "VmWare" o "Azure". Poiché il file drscout.conf è danneggiato, si consiglia di [disinstallare](vmware-physical-mobility-service-overview.md#uninstall-the-mobility-service) l'agente di mobilità e reinstallarlo. Se si verifica un errore di disinstallazione, seguire questa procedura:
    - Aprire il file Installation_Directory/uninstall.sh e commentare la chiamata alla funzione *StopServices*
    - Aprire il file Installation_Directory/Vx/bin/uninstall e commentare la chiamata alla funzione `stop_services`
    - Aprire il file Installation_Directory/Fx/uninstall e commentare la sezione completa che prova a fermare il servizio Fx.
    - A questo punto provare a [disinstallare](vmware-physical-mobility-service-overview.md#uninstall-the-mobility-service) l'agente di mobilità. Al termine della disinstallazione, riavviare il sistema e provare nuovamente a installare l'agente.

## <a name="installation-failure---failed-to-load-accounts"></a>Errore di installazione - Impossibile caricare gli account

Questo errore si verifica quando il servizio non riesce a leggere i dati dalla connessione di trasporto durante l'installazione dell'agente di mobilità e la registrazione del server di configurazione. Per risolvere il problema, assicurarsi che TLS 1.0 sia attivato sul computer di origine.

## <a name="change-ip-address-of-configuration-server"></a>Modificare l'indirizzo IP del server di configurazione

È consigliabile non modificare l'indirizzo IP di un server di configurazione. Verificare che tutti gli indirizzi IP assegnati al server di configurazione siano indirizzi IP statici e non DHPC.

## <a name="acs50008-saml-token-is-invalid"></a>ACS50008: Token SAML non valido

Per evitare che si verifichi questo errore assicurarsi che l'ora del clock di sistema non differisca di oltre 15 minuti rispetto all'ora locale. Eseguire di nuovo il programma di installazione per completare la registrazione.

## <a name="failed-to-create-certificate"></a>Creazione del certificato non riuscita

Impossibile creare un certificato richiesto per l'autenticazione di Azure Site Recovery. Eseguire di nuovo l'installazione dopo essersi assicurati di eseguire il programma di installazione come amministratore locale.

## <a name="register-source-machine-with-configuration-server"></a>Registrare il computer di origine con il server di configurazione

### <a name="if-source-machine-has-windows-os"></a>Se il computer di origine dispone di Windows OS,

eseguire il comando seguente sul computer di origine

```
  cd C:\Program Files (x86)\Microsoft Azure Site Recovery\agent
  UnifiedAgentConfigurator.exe  /CSEndPoint <CSIP> /PassphraseFilePath <PassphraseFilePath>
  ```
**Impostazione** | **Dettagli**
--- | ---
Uso | UnifiedAgentConfigurator.exe  /CSEndPoint <CSIP> /PassphraseFilePath <PassphraseFilePath>
Log di configurazione dell'agente | In %ProgramData%\ASRSetupLogs\ASRUnifiedAgentConfigurator.log.
/CSEndPoint | Parametro obbligatorio. Specifica l'indirizzo IP del server di configurazione. Qualsiasi indirizzo IP valido.
/PassphraseFilePath |  Obbligatorio. Percorso della passphrase. Usare qualsiasi percorso file locale o UNC valido.

### <a name="if-source-machine-has-linux-os"></a>Se il computer di origine dispone di Linux OS,

eseguire il comando seguente sul computer di origine

```
  /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <CSIP> -P /var/passphrase.txt
  ```
**Impostazione** | **Dettagli**
--- | ---
Uso | cd /usr/local/ASR/Vx/bin<br/><br/> UnifiedAgentConfigurator.sh -i <CSIP> -P <PassphraseFilePath>
-i | Parametro obbligatorio. Specifica l'indirizzo IP del server di configurazione. Qualsiasi indirizzo IP valido.
-P |  Obbligatorio. Percorso completo del file in cui è stata salvata la passphrase. Usare qualsiasi cartella valida