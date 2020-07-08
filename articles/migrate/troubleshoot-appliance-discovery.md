---
title: Risolvere i problemi di distribuzione e individuazione di Azure Migrate Appliance
description: Ottenere assistenza per la distribuzione di Azure Migrate Appliance e l'individuazione di computer.
author: musa-57
ms.manager: abhemraj
ms.author: hamusa
ms.topic: troubleshooting
ms.date: 01/02/2020
ms.openlocfilehash: 92a8e129188f2790a3e46162b207373b5d6e6ce4
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85611357"
---
# <a name="troubleshoot-the-azure-migrate-appliance-and-discovery"></a>Risolvere i problemi relativi a Azure Migrate Appliance e all'individuazione

Questo articolo illustra come risolvere i problemi durante la distribuzione del dispositivo [Azure migrate](migrate-services-overview.md) e l'uso dell'appliance per individuare i computer locali.


## <a name="whats-supported"></a>Attività supportate

[Esaminare](migrate-appliance.md) i requisiti di supporto dell'appliance.


## <a name="invalid-ovf-manifest-entry"></a>"Voce del manifesto OVF non valida"

Se viene visualizzato l'errore "il file manifesto specificato non è valido: voce del manifesto OVF non valida", eseguire le operazioni seguenti:

1. Verificare che il file OVA del dispositivo Azure Migrate venga scaricato correttamente controllando il relativo valore hash. [Altre informazioni](https://docs.microsoft.com/azure/migrate/tutorial-assessment-vmware) Se il valore hash non corrisponde, scaricare di nuovo il file OVA e riprovare la distribuzione.
2. Se la distribuzione ha ancora esito negativo e si usa il client VMware vSphere per distribuire il file OVF, provare a distribuirlo tramite il client Web di vSphere. Se la distribuzione non riesce ancora, provare a usare un altro Web browser.
3. Se si usa il client Web vSphere e si prova a distribuirlo in server vCenter 6,5 o 6,7, provare a distribuire gli OVA direttamente nell'host ESXi:
   - Connettersi direttamente all'host ESXi (invece di server vCenter) con il client Web (https://<*indirizzo IP host*>/UI).
   - In **Home**  >  **Inventory**selezionare **file**  >  **deploy OVF template**. Passare a OVA e completare la distribuzione.
4. Se l'errore di distribuzione persiste, contattare il supporto di Azure Migrate.

## <a name="cant-connect-to-the-internet"></a>Non è possibile connettersi a Internet

Questo problema può verificarsi se il computer dell'appliance si trova dietro un proxy.

- Assicurarsi di specificare le credenziali di autorizzazione se sono necessarie per il proxy.
- Se si usa un proxy firewall basato su URL per controllare la connettività in uscita, aggiungere [questi URL](migrate-appliance.md#url-access) a un elenco Consenti.
- Se si usa un proxy di intercettazione per connettersi a Internet, importare il certificato proxy nella macchina virtuale dell'appliance seguendo [questa procedura](https://docs.microsoft.com/azure/migrate/concepts-collector).

## <a name="cant-sign-into-azure-from-the-appliance-web-app"></a>Non è possibile accedere ad Azure dall'app Web Appliance

Se si usa l'account Azure errato per accedere ad Azure, viene visualizzato l'errore "dispiace, ma si è verificato un problema durante l'accesso". Questo errore si verifica per due motivi:

- Se si accede all'applicazione Web Appliance per il cloud pubblico, usando le credenziali dell'account utente per il portale cloud per enti pubblici.
- Se si accede all'applicazione Web Appliance per il cloud per enti pubblici usando le credenziali dell'account utente per il portale del cloud privato.

Assicurarsi di usare le credenziali corrette.

##  <a name="datetime-synchronization-error"></a>Errore di sincronizzazione di data/ora

Un errore relativo alla sincronizzazione di data e ora (802) indica che l'orologio del server potrebbe non essere sincronizzato con l'ora corrente da più di cinque minuti. Modificare l'ora di clock della macchina virtuale dell'agente di raccolta in modo che corrisponda all'ora corrente:

1. Nella macchina virtuale aprire un prompt dei comandi come amministratore.
2. Per controllare il fuso orario, eseguire **w32tm/TZ**.
3. Per sincronizzare l'ora, eseguire **w32tm sull'/Resync**.


## <a name="unabletoconnecttoserver"></a>UnableToConnectToServer

Se viene ricevuto questo errore di connessione, potrebbe non essere possibile connettersi a server vCenter *nomeserver*. com: 9443. I dettagli dell'errore indicano che non esiste alcun endpoint in ascolto in `https://\*servername*.com:9443/sdk` grado di accettare il messaggio.

- Controllare se si sta eseguendo la versione più recente dell'appliance. In caso contrario, aggiornare l'appliance alla [versione più recente](https://docs.microsoft.com/azure/migrate/concepts-collector).
- Se il problema si verifica ancora nella versione più recente, l'appliance potrebbe non essere in grado di risolvere il nome del server vCenter specificato oppure la porta specificata potrebbe essere errata. Per impostazione predefinita, se la porta non è specificata, l'agente di raccolta tenterà di connettersi al numero di porta 443.

    1. Eseguire il ping *ServerName*. com dall'appliance.
    2. Se il passaggio 1 ha esito negativo, provare a connettersi al server vCenter usando l'indirizzo IP.
    3. Identificare il numero di porta corretto per la connessione server vCenter.
    4. Verificare che server vCenter sia attivo e in esecuzione.


## <a name="error-6005260039-appliance-might-not-be-registered"></a>Errore 60052/60039: l'appliance potrebbe non essere registrata

- Errore 60052, "l'appliance potrebbe non essere stata registrata correttamente nel progetto Azure Migrate" si verifica se l'account Azure usato per registrare l'appliance non dispone di autorizzazioni sufficienti.
    - Assicurarsi che l'account utente di Azure usato per registrare il dispositivo disponga almeno delle autorizzazioni di collaboratore per la sottoscrizione.
    - [Altre](https://docs.microsoft.com/azure/migrate/migrate-appliance#appliance---vmware) informazioni sui ruoli e sulle autorizzazioni di Azure richiesti.
- Errore 60039: "è possibile che l'appliance non sia stata registrata correttamente nel progetto Azure Migrate" se la registrazione non riesce perché il progetto Azure Migrate usato per registrare l'appliance non è stato trovato.
    - Nel portale di Azure e verificare se il progetto esiste nel gruppo di risorse.
    - Se il progetto non esiste, creare un nuovo progetto di Azure Migrate nel gruppo di risorse e registrare di nuovo l'appliance. [Informazioni su come](https://docs.microsoft.com/azure/migrate/how-to-add-tool-first-time#create-a-project-and-add-a-tool) creare un nuovo progetto.

## <a name="error-6003060031-key-vault-management-operation-failed"></a>Errore 60030/60031: operazione di gestione Key Vault non riuscita

Se viene visualizzato l'errore 60030 o 60031, "un'operazione di gestione Azure Key Vault non riuscita", eseguire le operazioni seguenti:
- Assicurarsi che l'account utente di Azure usato per registrare il dispositivo disponga almeno delle autorizzazioni di collaboratore per la sottoscrizione.
- Verificare che l'account abbia accesso all'insieme di credenziali delle chiavi specificato nel messaggio di errore, quindi ripetere l'operazione.
- Se il problema persiste, contattare il supporto tecnico Microsoft.
- [Altre](https://docs.microsoft.com/azure/migrate/migrate-appliance#appliance---vmware) informazioni sui ruoli e sulle autorizzazioni di Azure richiesti.

## <a name="error-60028-discovery-couldnt-be-initiated"></a>Errore 60028: non è stato possibile avviare l'individuazione

Errore 60028: "Impossibile avviare l'individuazione a causa di un errore. L'operazione non è riuscita per l'elenco specificato di host o cluster "indica che non è stato possibile avviare l'individuazione negli host elencati nell'errore a causa di un problema durante l'accesso o il recupero delle informazioni sulla macchina virtuale. Il resto degli host è stato aggiunto correttamente.

- Aggiungere di nuovo gli host elencati nell'errore utilizzando l'opzione **Aggiungi host** .
- Se si verifica un errore di convalida, rivedere le indicazioni per la correzione per correggere gli errori, quindi provare a eseguire di nuovo l'opzione **Salva e avvia individuazione** .

## <a name="error-60025-azure-ad-operation-failed"></a>Errore 60025: operazione Azure AD non riuscita 
Errore 60025: "un'operazione Azure AD non è riuscita. L'errore si è verificato durante la creazione o l'aggiornamento dell'applicazione Azure AD "si verifica quando l'account utente di Azure usato per avviare l'individuazione è diverso dall'account usato per registrare l'appliance. Eseguire una delle operazioni seguenti:

- Assicurarsi che l'account utente che avvia l'individuazione sia uguale a quello usato per registrare l'appliance.
- Fornire Azure Active Directory le autorizzazioni di accesso alle applicazioni per l'account utente per il quale l'operazione di individuazione ha esito negativo.
- Eliminare il gruppo di risorse creato in precedenza per il progetto Azure Migrate. Creare un altro gruppo di risorse per riavviarlo.
- [Altre](https://docs.microsoft.com/azure/migrate/migrate-appliance#appliance---vmware) informazioni sulle autorizzazioni per l'applicazione Azure Active Directory.


## <a name="error-50004-cant-connect-to-host-or-cluster"></a>Errore 50004: non è possibile connettersi all'host o al cluster

Errore 50004: "Impossibile connettersi a un host o a un cluster perché il nome del server non può essere risolto. Codice errore WinRM: 0x803381B9 "potrebbe verificarsi se il servizio DNS di Azure per l'appliance non riesce a risolvere il cluster o il nome host specificato.

- Se viene visualizzato questo errore nel cluster, FQDN del cluster.
- Questo errore può essere visualizzato anche per gli host in un cluster. Ciò indica che l'appliance è in grado di connettersi al cluster, ma il cluster restituisce nomi host che non sono FQDN. Per correggere l'errore, aggiornare il file hosts nell'appliance aggiungendo un mapping dell'indirizzo IP e dei nomi host:
    1. Aprire Blocco note come amministratore.
    2. Aprire il file C:\Windows\System32\Drivers\etc\hosts.
    3. Aggiungere l'indirizzo IP e il nome host in una riga. Ripetere l'operazione per ogni host o cluster in cui viene visualizzato questo errore.
    4. Salvare e chiudere il file degli host.
    5. Controllare se l'appliance è in grado di connettersi agli host usando l'app di gestione Appliance. Dopo 30 minuti, nel portale di Azure verranno visualizzate le informazioni più recenti per questi host.

## <a name="discovered-vms-not-in-portal"></a>Macchine virtuali individuate non nel portale

Se lo stato di individuazione è "individuazione in corso", ma non sono ancora visibili le VM nel portale, attendere alcuni minuti:
- Sono necessari circa 15 minuti per una VM VMware.
- Sono necessari circa due minuti per ogni host aggiunto per l'individuazione di macchine virtuali Hyper-V.

Se si attende che lo stato non cambia, selezionare **Aggiorna** nella scheda **Server** . Verrà visualizzato il numero dei server individuati in Azure Migrate: server assessment e Azure Migrate: migrazione server.

Se questa operazione non funziona e si stanno scoprendo i server VMware:

- Verificare che l'account vCenter specificato disponga delle autorizzazioni impostate correttamente, con accesso ad almeno una macchina virtuale.
- Azure Migrate non riesce a individuare le macchine virtuali VMware se l'account vCenter ha accesso concesso a livello di cartella della macchina virtuale vCenter. [Altre](set-discovery-scope.md) informazioni sull'individuazione dell'ambito.

## <a name="vm-data-not-in-portal"></a>Dati della macchina virtuale non nel portale

Se le macchine virtuali individuate non vengono visualizzate nel portale o se i dati della macchina virtuale sono obsoleti, attendere alcuni minuti. Sono necessari fino a 30 minuti per la visualizzazione delle modifiche nei dati di configurazione della macchina virtuale individuati nel portale. Potrebbero essere necessarie alcune ore per visualizzare le modifiche apportate ai dati dell'applicazione. Se non sono presenti dati dopo questa volta, provare ad aggiornare, come indicato di seguito.

1. In **Server**  >  **Azure migrate Assessment server**selezionare **Panoramica**.
2. In **Gestisci**selezionare **integrità agente**.
3. Selezionare **Aggiorna agente**.
4. Attendere il completamento dell'operazione di aggiornamento. Verranno ora visualizzate informazioni aggiornate.

## <a name="deleted-vms-appear-in-portal"></a>Le VM eliminate vengono visualizzate nel portale

Se si eliminano macchine virtuali che vengono comunque visualizzate nel portale, attendere 30 minuti. Se vengono ancora visualizzati, aggiornare come descritto in precedenza.

## <a name="i-do-not-see-performance-data-for-some-network-adapters-on-my-physical-servers"></a>Non vengono visualizzati i dati sulle prestazioni per alcune schede di rete nei server fisici

Questo problema può verificarsi se nel server fisico è abilitata la virtualizzazione Hyper-V. A causa del Gap del prodotto, la velocità effettiva della rete viene acquisita sulle schede di rete virtuali individuate.

## <a name="error-the-file-uploaded-is-not-in-the-expected-format"></a>Errore: il formato del file caricato non è corretto
Alcuni strumenti hanno impostazioni internazionali che creano il file CSV con il punto e virgola come delimitatore. Modificare le impostazioni in modo che il delimitatore sia una virgola.

## <a name="i-imported-a-csv-but-i-see-discovery-is-in-progress"></a>È stato importato un CSV ma viene visualizzato il messaggio "Individuazione in corso"
Questo stato viene visualizzato se il caricamento del file CSV non è riuscito a causa di un errore di convalida. Provare di nuovo a importare il CSV. Per correggere gli errori, è possibile scaricare il report dell’errore relativo al caricamento precedente e seguire le istruzioni per la correzione nel file. Il report degli errori può essere scaricato dalla sezione "Dettagli dell'importazione" nella pagina "Individua macchine virtuali".

## <a name="do-not-see-application-details-even-after-updating-guest-credentials"></a>Non visualizzare i dettagli dell'applicazione anche dopo l'aggiornamento delle credenziali Guest
L'individuazione dell'applicazione viene eseguita ogni 24 ore. Se si desidera visualizzare immediatamente i dettagli, aggiornare come indicato di seguito. L'operazione potrebbe richiedere alcuni minuti a seconda del numero di minuti. delle macchine virtuali individuate.

1. In **Server**  >  **Azure migrate Assessment server**selezionare **Panoramica**.
2. In **Gestisci**selezionare **integrità agente**.
3. Selezionare **Aggiorna agente**.
4. Attendere il completamento dell'operazione di aggiornamento. Verranno ora visualizzate informazioni aggiornate.

## <a name="unable-to-export-application-inventory"></a>Non è possibile esportare l'inventario delle applicazioni
Assicurarsi che l'utente che Scarica l'inventario dal portale disponga dei privilegi di collaboratore per la sottoscrizione.

## <a name="common-app-discovery-errors"></a>Errori comuni di individuazione delle app

Azure Migrate supporta l'individuazione di applicazioni, ruoli e funzionalità, utilizzando Azure Migrate: server assessment. L'individuazione delle app è attualmente supportata solo per VMware. [Altre](how-to-discover-applications.md) informazioni sui requisiti e i passaggi per la configurazione dell'individuazione delle app.

I tipici errori di individuazione delle app sono riepilogati nella tabella. 

**Errore** | **Causa** | **Azione**
--- | --- | ---
9000: Impossibile rilevare lo stato dello strumento VMware.     |   È possibile che gli strumenti VMWare non siano installati o siano danneggiati.    |   Verificare che gli strumenti VMware siano installati e in esecuzione nella macchina virtuale.
9001: gli strumenti VMware non sono installati.     |   È possibile che gli strumenti VMWare non siano installati o siano danneggiati.    |   Verificare che gli strumenti VMware siano installati e in esecuzione nella macchina virtuale.
9002: gli strumenti VMware non sono in esecuzione.   |   È possibile che gli strumenti VMWare non siano installati o siano danneggiati.    |   Verificare che gli strumenti VMware siano installati e in esecuzione nella macchina virtuale.
9003: il tipo di sistema operativo non è supportato per l'individuazione di macchine virtuali guest.    |   Il sistema operativo in esecuzione nel server non è né Windows né Linux.    |   I tipi di sistema operativo supportati sono solo Windows e Linux. Se il server è effettivamente Windows o Linux, controllare il tipo di sistema operativo specificato in server vCenter.
9004: la macchina virtuale non è in esecuzione.     |   La macchina virtuale è spenta.  |   Verificare che la macchina virtuale sia accesa.
9005: il tipo di sistema operativo non è supportato per l'individuazione di macchine virtuali guest.    |   Il tipo di sistema operativo non è supportato per l'individuazione delle macchine virtuali guest.     |   I tipi di sistema operativo supportati sono solo Windows e Linux.
9006: l'URL per scaricare il file di metadati dal Guest è vuoto.     |   Questo problema può verificarsi se l'agente di individuazione non funziona come previsto.    |   Il problema dovrebbe risolvere automaticamente IN24 ore. Se il problema persiste, contattare il supporto tecnico Microsoft.
9007: Impossibile trovare il processo che esegue l'attività di individuazione nella macchina virtuale guest.   |   Questo problema può verificarsi se l'agente di individuazione non funziona correttamente.   |   Il problema dovrebbe risolversi automaticamente tra 24 ore. Se il problema persiste, contattare il supporto tecnico Microsoft.
9008: Impossibile recuperare lo stato del processo della macchina virtuale guest.   |   Il problema può verificarsi a causa di un errore interno.   |   Il problema dovrebbe risolversi automaticamente tra 24 ore. Se il problema persiste, contattare il supporto tecnico Microsoft.
9009: l'account utente di Windows ha impedito l'esecuzione dell'attività di individuazione nel server.  |   Le impostazioni di controllo dell'account utente (UAC) di Windows nel server sono restrittive e impediscono l'individuazione delle applicazioni installate.  |   In impostazioni di controllo dell'account utente nel server configurare l'impostazione UAC su uno dei due livelli inferiori.
9010: la macchina virtuale è spenta.     |   La macchina virtuale è spenta.  |   Verificare che la macchina virtuale sia accesa.
9011: il file di metadati individuato non è stato trovato nella macchina virtuale guest file system.    |   Il problema può verificarsi a causa di un errore interno.   |   Il problema dovrebbe risolversi automaticamente tra 24 ore. Se il problema persiste, contattare il supporto tecnico Microsoft.
9012: il file di metadati individuati è vuoto.     |   Il problema può verificarsi a causa di un errore interno.   |   Il problema dovrebbe risolversi automaticamente tra 24 ore. Se il problema persiste, contattare il supporto tecnico Microsoft.
9013: viene creato un nuovo profilo temporaneo per ogni account di accesso.    |   Per ogni account di accesso alla macchina virtuale VMware viene creato un nuovo profilo temporaneo.    |   Contattare supporto tecnico Microsoft per una risoluzione.
9014: Impossibile recuperare i metadati dalla macchina virtuale guest file system.     |   Nessuna connettività all'host ESXi    |   Verificare che l'appliance sia in grado di connettersi alla porta 443 nell'host ESXi che esegue la macchina virtuale
9015: il ruolo operazioni Guest non è abilitato nell'account utente di vCenter   |   Il ruolo operazioni Guest non è abilitato nell'account utente di vCenter.   |   Verificare che il ruolo operazioni Guest sia abilitato nell'account utente di vCenter.
9016: Impossibile individuare perché l'agente operativo guest non è aggiornato.   |   Gli strumenti VMware non sono installati correttamente o non sono aggiornati.    |   Verificare che gli strumenti VMware siano installati correttamente e aggiornati.
9017: il file con metadati individuati non è stato trovato nella macchina virtuale.  |   Il problema può verificarsi a causa di un errore interno.   |   Contattare supporto tecnico Microsoft per una risoluzione.
9018: PowerShell non è installato nelle macchine virtuali guest.  |   PowerShell non è disponibile nella macchina virtuale guest.    |   Installare PowerShell nella macchina virtuale guest.
9019: Impossibile individuare a causa di errori dell'operazione della macchina virtuale guest.     |   L'operazione guest VMware non è riuscita nella macchina virtuale.    |   Verificare che le credenziali della macchina virtuale siano valide e che il nome utente specificato nelle credenziali della macchina virtuale Guest sia in formato UPN.
9020: autorizzazione per la creazione di file negata.    |   Il ruolo associato all'utente o i criteri di gruppo limitano l'utente alla creazione del file nella cartella    |   Controllare se l'utente Guest specificato dispone dell'autorizzazione Crea per il file nella cartella. Vedere **notifiche** in server assessment per il nome della cartella.
9021: Impossibile creare il file nel percorso temporaneo di sistema.     |   VMware Tool segnala il percorso temporaneo del sistema anziché il percorso temporaneo degli utenti.    |   Aggiornare la versione dello strumento VMware sopra 10287 (formato client NGC/VI).
9022: accesso all'oggetto WMI negato.    |   Il ruolo associato all'utente o i criteri di gruppo limitano l'accesso dell'utente all'oggetto WMI.  |   Contattare il supporto tecnico Microsoft.
9023: Impossibile eseguire PowerShell perché il valore della variabile di ambiente SystemRoot è vuoto.    |   Il valore della variabile di ambiente SystemRoot è vuoto per la macchina virtuale guest.     |   Contattare supporto tecnico Microsoft per una risoluzione.
9024: Impossibile individuare poiché il valore della variabile di ambiente TEMP è vuoto.    |   Il valore della variabile di ambiente TEMP è vuoto per la macchina virtuale guest.   |   Contattare il supporto tecnico Microsoft.
9025: PowerShell è danneggiato nelle VM guest.  |   PowerShell è danneggiato nella macchina virtuale guest.    |   Reinstallare PowerShell nella macchina virtuale guest e verificare che PowerShell possa essere eseguito nella macchina virtuale guest.
9026: non è possibile eseguire le operazioni Guest nella macchina virtuale.  |   Lo stato della macchina virtuale non consente l'esecuzione di operazioni Guest nella macchina virtuale.   |   Contattare supporto tecnico Microsoft per una risoluzione.
9027: l'agente operativo guest non è in esecuzione nella macchina virtuale.   |   Non è stato possibile contattare l'agente operativo guest in esecuzione all'interno della macchina virtuale.    |   Contattare supporto tecnico Microsoft per una risoluzione.
9028: non è possibile creare il file a causa di spazio di archiviazione su disco insufficiente nella macchina virtuale.     |   Spazio su disco insufficiente.   |   Verificare che lo spazio disponibile nell'archiviazione su disco della macchina virtuale sia sufficiente.
9029: nessun accesso a PowerShell sulle credenziali della macchina virtuale Guest fornita.   |   L'accesso a PowerShell non è disponibile per l'utente.     |   Assicurarsi che l'utente aggiunto all'appliance possa accedere a PowerShell nella macchina virtuale guest.
9030: non è possibile raccogliere i metadati individuati perché l'host ESXi è disconnesso.     |   L'host ESXi si trova in uno stato disconnesso.   |   Verificare che l'host ESXi che esegue la macchina virtuale sia connesso.
9031: non è possibile raccogliere i metadati individuati perché l'host ESXi non risponde.   |   Lo stato dell'host remoto non è valido.    |   Verificare che l'host ESXi che esegue la macchina virtuale sia in esecuzione e connesso.
9032: Impossibile individuare a causa di un errore interno.   |   Il problema può verificarsi a causa di un errore interno.   |   Contattare supporto tecnico Microsoft per una risoluzione.
9033: non è possibile rilevare perché il nome utente della macchina virtuale contiene caratteri non validi.     |   Sono stati rilevati caratteri non validi nel nome utente.   |   Fornire di nuovo le credenziali della macchina virtuale per verificare che non siano presenti caratteri non validi.
9034: il nome utente specificato non è in formato UPN.    |   Il nome utente non è in formato UPN.  |   Verificare che il nome utente sia in formato UPN (User Principal Name).
9035: Impossibile individuare perché la modalità linguaggio di PowerShell non è impostata su' lingua completa '.  |   La modalità linguaggio per PowerShell nella macchina virtuale guest non è impostata su Full Language.   |   Verificare che la modalità linguaggio di PowerShell sia impostata su "lingua completa".
10000: il tipo di sistema operativo non è supportato.   |   Il sistema operativo in esecuzione nel server non è né Windows né Linux.    |   I tipi di sistema operativo supportati sono solo Windows e Linux.
10001: lo script per l'individuazione del server non è stato trovato nel dispositivo.    |   L'individuazione non funziona come previsto.   |   Contattare supporto tecnico Microsoft per una risoluzione.
10002: l'attività di individuazione non è stata completata nel tempo.     |   L'agente di individuazione non funziona come previsto.     |   Il problema dovrebbe risolversi automaticamente tra 24 ore. Se il problema persiste, contattare il supporto tecnico Microsoft.
10003: il processo che esegue l'attività di individuazione è stato terminato con un errore.    |   Il processo che esegue l'attività di individuazione è stato terminato con un errore.  |   Il problema dovrebbe risolversi automaticamente tra 24 ore. Se il problema persiste, contattare supporto tecnico Microsoft.
10004: le credenziali non sono state specificate per il tipo di sistema operativo guest.  |   Le credenziali per accedere ai computer di questo tipo di sistema operativo non sono state fornite nell'appliance Azure Migrate.    |   Aggiungere le credenziali per i computer nell'appliance
10005: le credenziali specificate non sono valide.   |   Le credenziali fornite per l'appliance per accedere al server non sono corrette.  |   Aggiornare le credenziali fornite nell'appliance e assicurarsi che il server sia accessibile tramite le credenziali.
10006: il tipo di sistema operativo guest non è supportato dall'archivio delle credenziali.  |   Il sistema operativo in esecuzione nel server non è né Windows né Linux.    |   I tipi di sistema operativo supportati sono solo Windows e Linux.
10007: Impossibile elaborare i metadati individuati.    |   Si è verificato un errore durante il tentativo di deserializzare JSON.    |   Contattare supporto tecnico Microsoft per una risoluzione.
10008: non è possibile creare un file nel server.    |  Il problema può verificarsi a causa di un errore interno.    |   Contattare supporto tecnico Microsoft per una risoluzione.
10009: Impossibile scrivere i metadati individuati in un file nel server.  |   Il problema può verificarsi a causa di un errore interno.   |   Contattare supporto tecnico Microsoft per una risoluzione.




## <a name="next-steps"></a>Passaggi successivi
Configurare un'appliance per [VMware](how-to-set-up-appliance-vmware.md), [Hyper-V](how-to-set-up-appliance-hyper-v.md)o [server fisici](how-to-set-up-appliance-physical.md).
