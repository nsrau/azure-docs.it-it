---
title: Risolvere i problemi relativi alla distribuzione e all'individuazione dell'appliance di Azure MigrateTroubleshoot Azure Migrate appliance deployment and discovery
description: Ottenere assistenza per la distribuzione dell'appliance di Azure Migrate e l'individuazione dei computer.
author: musa-57
ms.manager: abhemraj
ms.author: hamusa
ms.topic: troubleshooting
ms.date: 01/02/2020
ms.openlocfilehash: 6cb83a87f2e96eb62696e5d92095ef2b8d7c7def
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81677331"
---
# <a name="troubleshoot-the-azure-migrate-appliance-and-discovery"></a>Risolvere i problemi relativi all'appliance di Azure Migrate e all'individuazioneTroubleshoot the Azure Migrate appliance and discovery

Questo articolo consente di risolvere i problemi relativi alla distribuzione dell'appliance [di Azure Migrate](migrate-services-overview.md) e all'uso dell'appliance per individuare i computer locali.


## <a name="whats-supported"></a>Attività supportate

[Esaminare](migrate-appliance.md) i requisiti di supporto dell'appliance.


## <a name="invalid-ovf-manifest-entry"></a>"Voce del manifesto OVF non valida"

Se viene visualizzato l'errore "Il file manifesto fornito non è valido: voce di manifesto OVF non valida", eseguire le operazioni seguenti:

1. Verificare che il file OVA dell'appliance di Azure Migrate venga scaricato correttamente controllandone il valore hash. [Altre informazioni](https://docs.microsoft.com/azure/migrate/tutorial-assessment-vmware) Se il valore hash non corrisponde, scaricare nuovamente il file OVA e ripetere la distribuzione.
2. Se la distribuzione continua a non riuscire e si utilizza il client VMware vSphere per distribuire il file OVF, provare a distribuirlo tramite il client web vSphere. Se la distribuzione continua a non riuscire, provare a utilizzare un Web browser diverso.
3. Se si utilizza il client web vSphere e si tenta di distribuirlo su vCenter Server 6.5 o 6.7, provare a distribuire l'OVA direttamente nell'host ESXi:
   - Connettersi direttamente all'host ESXi (anziché vCenter Server) con il client Web (https://<indirizzo *IP host*>/ui).
   - In **Inventario domestico** > **selezionare****Modello OVF distribuzione** **file** > . Individuare l'OVA e completare la distribuzione.
4. Se l'errore di distribuzione persiste, contattare il supporto di Azure Migrate.

## <a name="cant-connect-to-the-internet"></a>Impossibile connettersi a Internet

Ciò può accadere se il computer dell'appliance si trova dietro un proxy.

- Assicurarsi di fornire le credenziali di autorizzazione se il proxy ne ha bisogno.
- Se si utilizza un proxy firewall basato su URL per controllare la connettività in uscita, aggiungere [questi URL](migrate-appliance.md#url-access) a un elenco Consenti.
- Se si utilizza un proxy di intercettazione per connettersi a Internet, importare il certificato proxy nella macchina virtuale dell'appliance attenendosi [alla seguente procedura.](https://docs.microsoft.com/azure/migrate/concepts-collector)

## <a name="cant-sign-into-azure-from-the-appliance-web-app"></a>Impossibile accedere ad Azure dall'app Web dell'appliance

Se si usa l'account Azure non corretto, viene visualizzato l'errore "Spiacenti, ma si verificano problemi di accesso". Questo errore si verifica per un paio di motivi:

- Se si accede all'applicazione Web dell'appliance per il cloud pubblico, si usano le credenziali dell'account utente per il portale cloud di enti pubblici.
- Se si accede all'applicazione Web dell'appliance per il cloud governativo usando le credenziali dell'account utente per il portale del cloud privato.

Assicurarsi di utilizzare le credenziali corrette.

##  <a name="datetime-synchronization-error"></a>Errore di sincronizzazione data/ora

Un errore relativo alla sincronizzazione di data e ora (802) indica che l'orologio del server potrebbe non essere sincronizzazione con l'ora corrente di più di cinque minuti. Modificare l'ora dell'orologio nella macchina virtuale dell'agente di raccolta in modo che corrisponda all'ora corrente:Change the clock time on the collector VM to match the current time:

1. Nella macchina virtuale aprire un prompt dei comandi come amministratore.
2. Per controllare il fuso orario, eseguire **w32tm /tz**.
3. Per sincronizzare l'ora, eseguire **w32tm /resync**.


## <a name="unabletoconnecttoserver"></a>"ImpossibileConnettere il server"

Se viene visualizzato questo errore di connessione, potrebbe non essere possibile connettersi a vCenter *Servername*.com:9443. I dettagli dell'errore indicano che `https://\*servername*.com:9443/sdk` non è presente alcun endpoint in ascolto che può accettare il messaggio.

- Verificare se è in esecuzione la versione più recente dell'appliance. In caso contrario, aggiornare l'appliance alla [versione più recente.](https://docs.microsoft.com/azure/migrate/concepts-collector)
- Se il problema persiste nella versione più recente, l'appliance potrebbe non essere in grado di risolvere il nome del server vCenter specificato o la porta specificata potrebbe essere errata. Per impostazione predefinita, se la porta non è specificata, l'agente di raccolta tenterà di connettersi al numero di porta 443.By default, if the port is not specified, the collector will try to connect to port number 443.

    1. Ping *Servername*.com dall'appliance.
    2. Se il passaggio 1 non riesce, provare a connettersi al server vCenter utilizzando l'indirizzo IP.
    3. Identificare il numero di porta corretto per la connessione al server vCenter.
    4. Verificare che vCenter Server sia in esecuzione.


## <a name="error-6005260039-appliance-might-not-be-registered"></a>Errore 60052/60039: L'appliance potrebbe non essere registrata

- Errore 60052, "L'appliance potrebbe non essere registrata correttamente per il progetto Azure Migrate" si verifica se l'account di Azure utilizzato per registrare l'appliance non dispone di autorizzazioni sufficienti.
    - Assicurarsi che l'account utente di Azure usato per registrare l'appliance disponga almeno delle autorizzazioni di collaboratore per la sottoscrizione.
    - [Altre informazioni](https://docs.microsoft.com/azure/migrate/migrate-appliance#appliance---vmware) sui ruoli e le autorizzazioni di Azure necessari.
- Errore 60039, "l'appliance potrebbe non essere registrata correttamente per il progetto Azure Migrate" può verificarsi se la registrazione non riesce perché il progetto di Azure Migrate usato per il registro impossibile trovare l'appliance.
    - Nel portale di Azure e verificare se il progetto esiste nel gruppo di risorse.
    - Se il progetto non esiste, creare un nuovo progetto di Azure Migrate nel gruppo di risorse e registrare nuovamente l'appliance. [Scopri come](https://docs.microsoft.com/azure/migrate/how-to-add-tool-first-time#create-a-project-and-add-a-tool) creare un nuovo progetto.

## <a name="error-6003060031-key-vault-management-operation-failed"></a>Errore 60030/60031: Operazione di gestione dell'Insieme di credenziali delle chiavi non riuscita

Se viene visualizzato l'errore 60030 o 60031, "Un'operazione di gestione dell'insieme di credenziali delle chiavi di Azure non riuscita", eseguire le operazioni seguenti:If is receive the error 60030 or 60031, "An Azure Key Vault management operation failed", do the following:
- Assicurarsi che l'account utente di Azure usato per registrare l'appliance disponga almeno delle autorizzazioni collaboratore per la sottoscrizione.
- Assicurarsi che l'account abbia accesso all'insieme di credenziali delle chiavi specificato nel messaggio di errore, quindi ripetere l'operazione.
- Se il problema persiste, contattare il supporto tecnico Microsoft.
- [Altre informazioni](https://docs.microsoft.com/azure/migrate/migrate-appliance#appliance---vmware) sui ruoli e le autorizzazioni di Azure necessari.

## <a name="error-60028-discovery-couldnt-be-initiated"></a>Errore 60028: Impossibile avviare l'individuazione

Errore 60028: "Impossibile avviare l'individuazione a causa di un errore. L'operazione non riuscita per l'elenco specificato di host o cluster" indica che non è stato possibile avviare l'individuazione negli host elencati nell'errore a causa di un problema nell'accesso o nel recupero delle informazioni sulla macchina virtuale. Il resto degli host sono stati aggiunti correttamente.

- Aggiungere nuovamente gli host elencati nell'errore, utilizzando l'opzione **Aggiungi host.**
- Se si verifica un errore di convalida, esaminare le indicazioni per la correzione per correggere gli errori, quindi provare l'opzione Salva e avvia di nuovo **l'individuazione.**

## <a name="error-60025-azure-ad-operation-failed"></a>Errore 60025: Operazione di Azure AD non riuscitaError 60025: Azure AD operation failed 
Errore 60025: "Operazione di Azure AD non riuscita. L'errore si è verificato durante la creazione o l'aggiornamento dell'applicazione Azure AD" si verifica quando l'account utente di Azure usato per avviare l'individuazione è diverso dall'account usato per registrare l'appliance. Eseguire una delle operazioni seguenti:

- Assicurarsi che l'account utente che ha indetto l'individuazione sia lo stesso utilizzato per registrare l'appliance.
- Fornire le autorizzazioni di accesso all'applicazione Azure Active Directory per l'account utente per il quale l'operazione di individuazione non riesce.
- Eliminare il gruppo di risorse creato in precedenza per il progetto Azure Migrate.Delete the resource group previously created for the Azure Migrate project. Creare un altro gruppo di risorse per ricominciare.
- [Altre informazioni](https://docs.microsoft.com/azure/migrate/migrate-appliance#appliance---vmware) sulle autorizzazioni dell'applicazione Azure Active Directory.Learn more about Azure Active Directory application permissions.


## <a name="error-50004-cant-connect-to-host-or-cluster"></a>Errore 50004: Impossibile connettersi all'host o al cluster

Errore 50004: "Impossibile connettersi a un host o cluster perché non è possibile risolvere il nome del server. Codice di errore gestione remota Windows: 0x803381B9" potrebbe verificarsi se il servizio DNS di Azure per l'appliance non è in grado di risolvere il nome cluster o host fornito.

- Se viene visualizzato questo errore nel cluster, FQDN del cluster.
- Questo errore può essere visualizzato anche per gli host in un cluster. Ciò indica che l'appliance può connettersi al cluster, ma il cluster restituisce nomi host che non sono FQDN. Per risolvere questo errore, aggiornare il file hosts nell'appliance aggiungendo un mapping dell'indirizzo IP e dei nomi host:
    1. Aprire Blocco note come amministratore.
    2. Aprire il file C:
    3. Aggiungere l'indirizzo IP e il nome host in una riga. Ripetere l'operazione per ogni host o cluster in cui viene visualizzato questo errore.
    4. Salvare e chiudere il file degli host.
    5. Verificare se l'appliance può connettersi agli host utilizzando l'app di gestione dell'appliance. Dopo 30 minuti, dovresti vedere le informazioni più recenti per questi host nel portale di Azure.After 30 minutes, you should see the latest information for these hosts in the Azure portal.

## <a name="discovered-vms-not-in-portal"></a>Macchine virtuali individuate non nel portale

Se lo stato di individuazione è "Discovery in corso", ma non è ancora visualizzato il sistema di macchine virtuali nel portale, attendere alcuni minuti:If discovery state is "Discovery in progress", but don't yet see the VMs in the portal, wait few minutes:
- Ci vogliono circa 15 minuti per una VM VMware .
- Sono necessari circa due minuti per ogni host aggiunto per l'individuazione di macchine virtuali Hyper-V.It takes circa two minutes for each added host for Hyper-V VM discovery.

Se si attende e lo stato non cambia, selezionare **Aggiorna** nella scheda **Server.** Questo dovrebbe mostrare il conteggio dei server individuati in Azure Migrate: Server Assessment e Azure Migrate: Server Migration.

Se questo non funziona e si sta scoprendo i server VMware:

- Verificare che l'account vCenter specificato disponga di autorizzazioni impostate correttamente, con accesso ad almeno una macchina virtuale.
- Azure Migrate non è in grado di individuare le macchine virtuali VMware se l'account vCenter ha accesso concesso a livello di cartella VM vCenter. [Ulteriori informazioni](set-discovery-scope.md) sull'individuazione dell'ambito.

## <a name="vm-data-not-in-portal"></a>Dati delle macchine virtuali non nel portaleVM data not in portal

Se le macchine virtuali rilevate non vengono visualizzate nel portale o se i dati della macchina virtuale non sono aggiornati, attendere alcuni minuti. Richiede fino a 30 minuti per visualizzare nel portale le modifiche nei dati di configurazione della macchina virtuale individuata. La visualizzazione delle modifiche ai dati dell'applicazione potrebbe richiedere alcune ore. Se non ci sono dati dopo questo periodo di tempo, provare ad aggiornare, come segue

1. In **Server** > **di valutazione del server di migrazione di Azure**selezionare **Panoramica**.
2. In **Gestisci**selezionare **Integrità agente**.
3. Selezionare **Aggiorna agente**.
4. Attendere il completamento dell'operazione di aggiornamento. Ora dovresti vedere informazioni aggiornate.

## <a name="deleted-vms-appear-in-portal"></a>Le macchine virtuali eliminate vengono visualizzate nel portaleDeleted VMs appear in portal

Se si eliminano le macchine virtuali che vengono comunque visualizzate nel portale, attendere 30 minuti. Se vengono ancora visualizzati, aggiornare come descritto in precedenza.

## <a name="common-app-discovery-errors"></a>Errori comuni di individuazione delle app

Azure Migrate supporta l'individuazione di applicazioni, ruoli e funzionalità tramite Azure Migrate: valutazione del server. L'individuazione delle app è attualmente supportata solo per VMware. [Ulteriori informazioni](how-to-discover-applications.md) sui requisiti e sui passaggi per configurare l'individuazione delle app.

Gli errori tipici di individuazione delle app sono riepilogati nella tabella. 

**Error (Errore) (Error (Errore)e)** | **Causa** | **Azione**
--- | --- | --- | ---
10000: "Impossibile individuare le applicazioni installate sul server". | Ciò può verificarsi se il sistema operativo del computer non è Windows o Linux.This might occur if the machine operating system isn't Windows or Linux. | Usare solo l'individuazione delle app per Windows/Linux.
10001: "Impossibile recuperare le applicazioni installate nel server". | Errore interno - alcuni file mancanti nell'appliance. | Contattare il supporto Microsoft.
10002: "Impossibile recuperare le applicazioni installate nel server". | L'agente di individuazione sull'appliance potrebbe non funzionare correttamente. | Se il problema non si risolve entro 24 ore, contatta il supporto tecnico.
10003 "Impossibile recuperare le applicazioni installate nel server". | L'agente di individuazione sull'appliance potrebbe non funzionare correttamente. | Se il problema non si risolve entro 24 ore, contatta il supporto tecnico.
10004: "Impossibile individuare le applicazioni installate per <computer windows/Linux>". |  Le credenziali per accedere <computer Windows/Linux> non sono state fornite nell'appliance.| Aggiungere una credenziale all'appliance che ha accesso ai <computer Windows/Linux>.
10005: "Impossibile accedere al server locale". | Le credenziali di accesso potrebbero essere errate. | Aggiornare le credenziali dell'appliance assicurarsi di poter accedere alla macchina pertinente con esse. 
10006: "Impossibile accedere al server locale". | Ciò può verificarsi se il sistema operativo del computer non è Windows o Linux.This might occur if the machine operating system isn't Windows or Linux.|  Usare solo l'individuazione delle app per Windows/Linux.
10007: "Impossibile elaborare i metadati recuperati" | Questo errore interno si è verificato durante il tentativo di deserializzare JSON | Contattare il supporto tecnico Microsoft per una risoluzione
9000/9001/9002: "Impossibile individuare le applicazioni installate sul server". | Gli strumenti VMware potrebbero non essere installati o danneggiati. | Installare/reinstallare gli strumenti VMware nel computer in questione e verificare che sia in esecuzione.
9003: Impossibile individuare le applicazioni installate sul server". | Ciò può verificarsi se il sistema operativo del computer non è Windows o Linux.This might occur if the machine operating system isn't Windows or Linux. | Usare solo l'individuazione delle app per Windows/Linux.
9004: "Impossibile individuare le applicazioni installate sul server". | Ciò può verificarsi se la macchina virtuale è spenta. | Per l'individuazione, assicurarsi che la macchina virtuale sia attivata.
9005: "Impossibile individuare le applicazioni installate nella macchina virtuale. | Ciò può verificarsi se il sistema operativo del computer non è Windows o Linux.This might occur if the machine operating system isn't Windows or Linux. | Usare solo l'individuazione delle app per Windows/Linux.
9006/9007: "Impossibile recuperare le applicazioni installate nel server". | L'agente di individuazione sull'appliance potrebbe non funzionare correttamente. | Se il problema non si risolve entro 24 ore, contatta il supporto tecnico.
9008: "Impossibile recuperare le applicazioni installate nel server". | Potrebbe essere un errore interno.  | Tf il problema non si risolve entro 24 ore, contattare il supporto tecnico.
9009: "Impossibile recuperare le applicazioni installate nel server". | Può verificarsi se le impostazioni di Controllo dell'account utente di Windows sul server sono restrittive e impediscono l'individuazione delle applicazioni installate. | Cercare le impostazioni di "Controllo dell'account utente" nel server e configurare l'impostazione controllo dell'account utente sul server su uno dei due livelli inferiori.
9010: "Impossibile recuperare le applicazioni installate nel server". | Potrebbe essere un errore interno.  | Tf il problema non si risolve entro 24 ore, contattare il supporto tecnico.
9011: "File da scaricare dal guest non viene trovato nella macchina virtuale guest" | Il problema può verificarsi a causa di un errore interno. | Il problema dovrebbe essere risolto automaticamente in 24 ore. Se il problema persiste, contattare il supporto tecnico Microsoft.
9012: "Il contenuto del file di risultati è vuoto." | Il problema può verificarsi a causa di un errore interno. | Il problema dovrebbe essere risolto automaticamente in 24 ore. Se il problema persiste, contattare il supporto tecnico Microsoft.
9013: "Viene creato un nuovo profilo temporaneo per ogni accesso alla macchina virtuale VMware" | Viene creato un nuovo profilo temporaneo per ogni account di accesso nella macchina virtuale | Verificare che il nome utente fornito nelle credenziali della macchina virtuale guest sia in formato UPN.
9015: "Impossibile connettersi alle macchine virtuali VMware a causa di privilegi insufficienti su vCenter" | Ruolo operazioni guest non è abilitato nell'account utente vCenter | Verificare che il ruolo Operazioni guest sia abilitato nell'account utente di vCenter.
9016: "Impossibile connettersi alle macchine virtuali VMware poiché l'agente operativo guest è fuori dai dati"9016: "Unable to connect to VMware VMs as the guest operations agent is out of data" | Gli strumenti VMware non sono installati correttamente o non sono aggiornati. | Assicurarsi che gli strumenti VMware siano installati correttamente e aggiornati.
9017: "File con metadati individuati non trovato nella macchina virtuale." | Il problema può verificarsi a causa di un errore interno. | Contattare il supporto tecnico Microsoft per una risoluzione.
9018: "PowerShell non è installato nelle macchine virtuali guest." | PowerShell non è disponibile nella macchina virtuale guest. | Installare PowerShell nella macchina virtuale guest.
9019: "Impossibile individuare a causa di errori di operazione della macchina virtuale guest"9019: "Impossibile individuare a causa di errori di operazione della macchina virtuale guest"9019: "Unable to discover due to guest VM | Operazione guest VMware non riuscita nella macchina virtuale. | Verificare che le credenziali della macchina virtuale siano valide e che il nome utente fornito nelle credenziali della macchina virtuale guest sia in formato UPN.
9020: "Autorizzazione per la creazione di file negata". | Il ruolo associato all'utente o ai criteri di gruppo limita l'utente alla creazione del file nella cartella | Verificare se l'utente guest fornito dispone dell'autorizzazione di creazione per il file nella cartella. Vedere **Notifiche** nella valutazione del server per il nome della cartella.
9021: "Autorizzazione di creazione file negata nella cartella Percorso temporaneo di sistema". | La versione dello strumento VMware nella macchina virtuale non è supportataVMware tool version on the VM is unsupported | Aggiornare la versione dello strumento VMware precedente alla 10.2.0.
9022: "Ottenere l'accesso agli oggetti WMI negato." | Il ruolo associato all'utente o ai criteri di gruppo limita l'accesso all'oggetto WMI da parte dell'utente. | Contattare il supporto tecnico Microsoft.
9023: "Valore della variabile di ambiente SystemRoot è vuoto." | Non noto | Contattare il supporto tecnico Microsoft.
9024: "Il valore della variabile di ambiente TEMP è vuoto." | Non noto | Contattare il supporto tecnico Microsoft.
9025: "PowerShell è danneggiato nelle macchine virtuali guest." | Non noto | Reinstallare PowerShell nella macchina virtuale guest e verificare se PowerShell può essere eseguito nella macchina virtuale guest.
8084: "Impossibile individuare le applicazioni a <Exception from VMware>causa di errore VMware: " | L'appliance Azure Migrate usa le API VMware per individuare le applicazioni. Questo problema può verificarsi se viene generata un'eccezione da vCenter Server durante il tentativo di individuare le applicazioni. Il messaggio di errore da VMware viene visualizzato nel messaggio di errore visualizzato nel portale. | Cercare il messaggio nella documentazione di [VMware](https://pubs.vmware.com/vsphere-51/topic/com.vmware.wssdk.apiref.doc/index-faults.html)e seguire la procedura per risolvere il problema. Se non è possibile risolvere il problema, contattare il supporto tecnico Microsoft.If you can't fix, contact Microsoft support.



## <a name="next-steps"></a>Passaggi successivi
Configurare un'appliance per [VMware,](how-to-set-up-appliance-vmware.md) [Hyper-V](how-to-set-up-appliance-hyper-v.md)o [server fisici.](how-to-set-up-appliance-physical.md)
